---
title: Netty Multipart File Upload
description: Netty HttpServer 환경에서 multipart/form-data 파일 업로드를 처리하는 Java 예제 코드 정리
date: 2025-12-28 18:00:00 +09:00
categories: [ SWH, Netty ]
tags: [
    java,
    java17,
    netty,
    multipart,
    file upload,
]
---

## Netty Multipart File Upload 구현 (Java)

본 글에서는 **Netty 기반 HTTP 서버**에서 `multipart/form-data` 형식의 파일 업로드를 처리하는 방법을 정리한다.

Spring MVC와 달리 Netty 환경에서는  
Multipart 요청 파싱과 파일 저장을 직접 구현해야 하기 때문에,  
전체 요청 흐름과 주요 핸들러 역할을 중심으로 설명한다.

---

## 프로젝트 구성 개요

- Java 17
- Maven
- Netty HTTP Server
- Multipart File Upload

서버는 **8080 포트**에서 실행되며,  
요청 경로에 따라 다음과 같이 처리된다.

| 경로 | 메서드 | 설명 |
|----|----|----|
| `/` | GET | index.html 정적 페이지 반환 |
| `/upload` | POST | Multipart 파일 업로드 처리 |

---

## 전체 처리 흐름

1. `NettyServer` 실행 → 8080 포트 바인딩
2. 새 연결 생성 시 `HttpServerInitializer`에서 Pipeline 구성
3. `/` 요청 → `StaticFileHandler`
4. `/upload` POST 요청 → `UploadServerHandler`
5. Multipart 파싱 후 파일을 로컬 디렉토리에 저장

---

## NettyServer

애플리케이션의 진입점으로,  
Netty 서버 부트스트랩과 EventLoop 그룹을 설정한다.

### 역할

- boss / worker EventLoopGroup 생성
- ServerBootstrap 설정
- 포트(8080) 바인딩
- HTTP Channel 파이프라인 초기화

### 핵심 포인트

- **boss 그룹**: 새로운 연결(accept) 처리
- **worker 그룹**: 실제 I/O(read/write) 처리
- 역할 분리를 통해 동시성 및 성능 확보


### 코드
```java
import io.netty.bootstrap.ServerBootstrap;
import io.netty.channel.Channel;
import io.netty.channel.ChannelOption;
import io.netty.channel.EventLoopGroup;
import io.netty.channel.nio.NioEventLoopGroup;
import io.netty.channel.socket.nio.NioServerSocketChannel;

public class NettyServer {

    public static void main(String[] args) throws Exception {
        int port = 8080;

        EventLoopGroup boss = new NioEventLoopGroup(1); // 보통 1개 스레드면 충분
        EventLoopGroup worker = new NioEventLoopGroup(); // 기본값 = CPU 코어 수 * 2 등

        try {
            ServerBootstrap b = new ServerBootstrap();

            b.group(boss, worker)
                    .channel(NioServerSocketChannel.class) // NIO 기반 서버 소켓 채널 사용
                    .childHandler(new HttpServerInitializer()) // 새 채널이 만들어질 때 파이프라인을 초기화
                    .option(ChannelOption.SO_BACKLOG, 128) // accept 대기 큐 크기
                    .childOption(ChannelOption.SO_KEEPALIVE, true); // 연결 유지를 기본으로

            // bind 하고 블로킹으로 완료 대기
            Channel ch = b.bind(port).sync().channel();

            System.out.println("✅ Netty Server started: http://localhost:" + port);

            // 서버가 종료될 때까지 대기
            ch.closeFuture().sync();
        } finally {
            // 종료: EventLoop 그룹을 정리
            boss.shutdownGracefully();
            worker.shutdownGracefully();
        }
    }
}
```

---

## HttpServerInitializer

새로운 `SocketChannel`이 생성될 때마다  
해당 채널의 **ChannelPipeline**을 구성하는 클래스이다.

### Pipeline 구성 순서

1. `HttpServerCodec`  
   - ByteBuf ↔ HTTP 메시지 변환

2. `HttpObjectAggregator`  
   - 여러 HTTP 조각을 하나의 `FullHttpRequest`로 합침
   - Multipart 요청을 단순하게 처리하기 위해 사용

3. `ChunkedWriteHandler`  
   - 대용량 데이터(파일 등) 스트리밍 전송 지원

4. `StaticFileHandler`  
   - 루트(`/`) 요청 처리

5. `UploadServerHandler`  
   - `/upload` POST 요청 처리

> Aggregator 크기는 실제 서비스 환경에 맞게 신중히 설정해야 한다.  
> (너무 크면 메모리 부담, 너무 작으면 업로드 실패 가능)

### 코드
```java
import io.netty.channel.ChannelInitializer;
import io.netty.channel.socket.SocketChannel;
import io.netty.handler.codec.http.HttpObjectAggregator;
import io.netty.handler.codec.http.HttpServerCodec;
import io.netty.handler.stream.ChunkedWriteHandler;

public class HttpServerInitializer extends ChannelInitializer<SocketChannel> {

    @Override
    protected void initChannel(SocketChannel ch) {
        // HTTP encoder/decoder: 바이트 스트림과 HTTP 객체를 상호 변환
        ch.pipeline().addLast(new HttpServerCodec());

        // 여러 조각으로 들어오는 HTTP 메시지(예: chunked 전송)를 하나로 합쳐 FullHttpRequest로 제공
        // 100B 까지 합치도록 설정(실제 운영에서는 더 신중히 결정)
        ch.pipeline().addLast(new HttpObjectAggregator(100 * 1024 * 1024));

        // 큰 파일을 전송할 때 backpressure/streaming에 도움을 주는 핸들러
        ch.pipeline().addLast(new ChunkedWriteHandler());

        // 루트 경로 접근 시 index.html을 반환하는 핸들러
        ch.pipeline().addLast(new StaticFileHandler());

        // 실제 업로드 로직을 처리하는 핸들러
        ch.pipeline().addLast(new UploadServerHandler());
    }
}
```

---

## StaticFileHandler

브라우저가 서버에 단순 접속했을 때  
기본으로 보여줄 **정적 HTML 페이지**를 제공하는 핸들러이다.

### 동작 방식

- 요청 URI가 `/` 인 경우만 직접 처리
- `resources/web/index.html` 파일을 classpath에서 로드
- HTML 내용을 HTTP 응답으로 반환
- 다른 경로 요청은 다음 핸들러로 전달

### 목적

- 업로드 테스트용 UI 제공
- 정적 파일 제공 로직을 업로드 로직과 분리

### 코드
```java
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.handler.codec.http.*;

import java.io.InputStream;
import java.nio.charset.StandardCharsets;

public class StaticFileHandler extends SimpleChannelInboundHandler<FullHttpRequest> {

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, FullHttpRequest req) throws Exception {

        // 이 핸들러는 루트 경로만 직접 처리 — 다른 경로는 다음 핸들러에게 위임
        if (!"/".equals(req.uri())) {
            // 요청 객체를 그대로 다음 핸들러로 전달하려면 retain 필요 (참조 카운트)
            ctx.fireChannelRead(req.retain());
            return;
        }

        // classpath에서 HTML 파일 로드
        try (InputStream is = getClass().getClassLoader().getResourceAsStream("web/index.html")) {
            if (is == null) {
                // 리소스가 없으면 404 응답
                FullHttpResponse notFound = new DefaultFullHttpResponse(
                        HttpVersion.HTTP_1_1, HttpResponseStatus.NOT_FOUND,
                        Unpooled.copiedBuffer("404 - index.html not found", StandardCharsets.UTF_8)
                );
                notFound.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/plain; charset=UTF-8");
                ctx.writeAndFlush(notFound);
                return;
            }

            byte[] bytes = is.readAllBytes();

            // HTML 응답 생성
            FullHttpResponse response = new DefaultFullHttpResponse(
                    HttpVersion.HTTP_1_1,
                    HttpResponseStatus.OK,
                    Unpooled.wrappedBuffer(bytes)
            );

            response.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/html; charset=UTF-8");
            response.headers().setInt(HttpHeaderNames.CONTENT_LENGTH, bytes.length);

            // 응답 전송
            ctx.writeAndFlush(response);
        }
    }
}
```

## UploadServerHandler

`POST /upload` 요청을 처리하는 **핵심 핸들러**이다.

`multipart/form-data` 형식의 요청을 직접 파싱하여  
업로드된 파일을 로컬 디렉토리에 저장한다.

이 핸들러에서는 Netty에서 제공하는  
`HttpPostRequestDecoder`를 사용해 Multipart 요청을 처리한다.

### 코드
```java
import io.netty.buffer.Unpooled;
import io.netty.channel.ChannelHandlerContext;
import io.netty.channel.SimpleChannelInboundHandler;
import io.netty.handler.codec.http.*;
import io.netty.handler.codec.http.multipart.*;

import java.io.File;
import java.nio.charset.StandardCharsets;
import java.util.List;

import static io.netty.handler.codec.http.HttpResponseStatus.*;
import static io.netty.handler.codec.http.HttpVersion.HTTP_1_1;

public class UploadServerHandler extends SimpleChannelInboundHandler<FullHttpRequest> {

    private static final HttpDataFactory factory = new DefaultHttpDataFactory(true);

    @Override
    protected void channelRead0(ChannelHandlerContext ctx, FullHttpRequest request) throws Exception {

        // 이 핸들러는 POST /upload 요청만 처리
        if (!"/upload".equals(request.uri()) || request.method() != HttpMethod.POST) {
            // 해당하지 않으면 다음 핸들러에게 전달
            ctx.fireChannelRead(request.retain());
            return;
        }

        // 요청 Content-Type이 multipart/form-data인지 검사(없으면 에러)
        String contentType = request.headers().get(HttpHeaderNames.CONTENT_TYPE);
        if (contentType == null || !contentType.startsWith("multipart/form-data")) {
            sendError(ctx, BAD_REQUEST, "Content-Type must be multipart/form-data");
            return;
        }

        HttpPostRequestDecoder decoder = null;
        String targetDir = "upload"; // 기본 저장 폴더
        int savedCount = 0;

        try {
            // decoder 생성: 요청과 factory 기반으로 multipart 파싱 준비
            decoder = new HttpPostRequestDecoder(factory, request);
            decoder.setDiscardThreshold(0); // 디스크 임시 파일을 바로 사용하도록 함(임계값 0)

            // Netty 권장 방식: getBodyHttpDatas()로 전체 파트를 한 번에 가져와 처리
            List<InterfaceHttpData> datas = decoder.getBodyHttpDatas();

            // debug 로그: 파트 개수(운영에서는 로그 레벨 주의)
            System.out.println("Multipart parts count: " + datas.size());

            // 각 파트 검사
            for (InterfaceHttpData data : datas) {

                // 일반 폼 필드 (예: targetDir)
                if (data.getHttpDataType() == InterfaceHttpData.HttpDataType.Attribute) {
                    Attribute attr = (Attribute) data;
                    System.out.println("Form field: " + attr.getName() + " = " + attr.getValue());
                    if ("targetDir".equals(attr.getName())) {
                        // 클라이언트가 지정한 targetDir을 사용 (실무에선 경로 검증 필수)
                        targetDir = sanitizeTargetDir(attr.getValue());
                    }
                }

                // 파일 파트 처리
                if (data.getHttpDataType() == InterfaceHttpData.HttpDataType.FileUpload) {
                    FileUpload fileUpload = (FileUpload) data;

                    // 업로드가 완료된 상태인지 확인 (부분 업로드 등 고려)
                    if (fileUpload.isCompleted()) {
                        // 디렉토리 생성(없으면)
                        File dir = new File(targetDir);
                        if (!dir.exists()) {
                            boolean ok = dir.mkdirs();
                            if (!ok) {
                                System.err.println("Failed to create directory: " + dir.getAbsolutePath());
                            }
                        }

                        // 저장 파일 경로. FileUpload.renameTo는 임시파일을 이동/이름변경
                        File dest = new File(dir, fileUpload.getFilename());

                        // renameTo 사용 (메모리/디스크 기반에 따라 동작)
                        fileUpload.renameTo(dest);
                        savedCount++;

                        System.out.println("Saved file: " + dest.getAbsolutePath() + " (size=" + dest.length() + ")");
                    } else {
                        System.err.println("File upload not completed: " + fileUpload.getFilename());
                    }
                }
            }

            // 성공 응답 생성
            String result = "✅ Saved files: " + savedCount;
            FullHttpResponse response = new DefaultFullHttpResponse(
                    HTTP_1_1,
                    OK,
                    Unpooled.copiedBuffer(result, StandardCharsets.UTF_8)
            );
            response.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/plain; charset=UTF-8");
            ctx.writeAndFlush(response);

        } catch (HttpPostRequestDecoder.ErrorDataDecoderException ex) {
            // 파싱 실패: 클라이언트가 보낸 멀티파트가 손상되었거나 잘못된 경우
            ex.printStackTrace();
            sendError(ctx, BAD_REQUEST, "Multipart parsing error: " + ex.getMessage());
        } catch (Exception ex) {
            // 그 외 예외
            ex.printStackTrace();
            sendError(ctx, INTERNAL_SERVER_ERROR, "Server error: " + ex.getMessage());
        } finally {
            // 반드시 decoder 자원 정리(임시 파일 삭제 등)
            if (decoder != null) {
                decoder.destroy();
            }
            // 주의: FullHttpRequest는 SimpleChannelInboundHandler가 자동으로 release 해줌
        }
    }

    /**
     * 간단한 targetDir 검증(실무에서는 더 엄격해야 함)
     * - 상대경로('..') 제거
     * - 절대경로 허용/금지 정책은 운영 정책에 따라 결정
     */
    private String sanitizeTargetDir(String input) {
        if (input == null || input.isBlank()) return "upload";
        // 간단히 .. 제거 (완전한 보호 아님)
        return input.replace("..", "").trim();
    }

    /**
     * 에러 응답을 간단히 보내는 헬퍼
     */
    private void sendError(ChannelHandlerContext ctx, HttpResponseStatus status, String message) {
        FullHttpResponse resp = new DefaultFullHttpResponse(
                HTTP_1_1, status,
                Unpooled.copiedBuffer(message, StandardCharsets.UTF_8)
        );
        resp.headers().set(HttpHeaderNames.CONTENT_TYPE, "text/plain; charset=UTF-8");
        ctx.writeAndFlush(resp);
    }
}
```

---

## index

### 코드
```html
<!DOCTYPE html>
<html lang="ko">
<head>
    <meta charset="UTF-8">
    <title>Multipart File Upload</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            padding: 40px;
            background: #f4f6f8;
        }
        .container {
            background: #fff;
            padding: 30px;
            width: 450px;
            border-radius: 10px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        input, button {
            width: 100%;
            margin-top: 10px;
            padding: 12px;
            font-size: 16px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            cursor: pointer;
        }
        button:hover {
            background-color: #45a049;
        }
        .log {
            margin-top: 20px;
            background: #000;
            color: #00ffcc;
            padding: 15px;
            height: 180px;
            overflow-y: auto;
            font-family: Consolas, monospace;
            font-size: 13px;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>📁 파일 업로드 (Multipart)</h2>

    <label>저장 디렉토리 (서버 경로)</label>
    <input id="targetDir" placeholder="예: C:/target" type="text" value="C:/target"/>

    <label>파일 선택 (다중 가능)</label>
    <input id="files" multiple type="file"/>

    <button onclick="uploadFiles()">전송</button>

    <div class="log" id="logBox"></div>
</div>

<script>
    async function uploadFiles() {
        const log = document.getElementById("logBox");
        log.textContent = "⏳ 업로드 시작...\n";

        const targetDir = document.getElementById("targetDir").value;
        const fileInput = document.getElementById("files");
        const files = fileInput.files;

        if (!targetDir) {
            log.textContent += "❌ targetDir 입력 필요\n";
            return;
        }

        if (!files || files.length === 0) {
            log.textContent += "❌ 파일을 선택하세요\n";
            return;
        }

        const formData = new FormData();
        formData.append("targetDir", targetDir);

        for (let i = 0; i < files.length; i++) {
            formData.append("files", files[i]);
            log.textContent += `📄 파일 추가: ${files[i].name}\n`;
        }

        try {
            const response = await fetch("/upload", {
                method: "POST",
                body: formData
            });

            const text = await response.text();
            log.textContent += "✅ 서버 응답:\n" + text + "\n";

        } catch (e) {
            log.textContent += "🔥 오류 발생:\n" + e.message + "\n";
        }
    }
</script>

</body>
</html>
```
{: file='resources/web/index.html'}

---

## 실행 방법

1. NettyServer 클래스 실행
2. 브라우저에서 접속 `http://localhost:8080`
3. 파일 선택 후 전송 버튼 클릭
4. 지정한 로컬 디렉토리에 파일 저장 확인