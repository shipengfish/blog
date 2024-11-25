---
layout: post
title: 直播的原理
date: 2024-11-25 16:50:00
summary: 公司有业务要使用大疆无人机，其中需要直播推流，顺便了解下直播的原理
categories: Study
---

> 公司有业务要使用大疆无人机，其中需要直播推流，顺便了解下直播的原理...

**基本流程**

```mermaid
graph LR

    A[采集] --> B[编码]

    B --> C[推流]

    C --> D[流媒体服务器]

    D --> E[分发]

    E --> F[解码]

    F --> G[播放]
```

**详细步骤**

**1. 采集**

• 视频采集：摄像头捕获图像

• 音频采集：麦克风采集声音

视频: YUV/RGB 格式的原始数据

音频: PCM 格式的原始数据

**2. 编码**

• 视频编码：H.264/H.265/AV1

• 音频编码：AAC/MP3

```mermaid
graph LR

    A[原始视频] --> B[H.264 编码]

    C[原始音频] --> D[AAC 编码]

    B --> E[封装格式]

    D --> E

    E --> F[推流]
```

**3. 推流协议**

• RTMP (Real-Time Messaging Protocol)

• RTSP (Real Time Streaming Protocol)

• GB28181 (国标协议)

```mermaid
graph TD

    A[编码数据] --> B{推流协议}

    B --> C[RTMP]

    B --> D[RTSP]

    B --> E[GB28181]
```

**4. 流媒体服务器处理流程**

```mermaid
sequenceDiagram

    participant C as 采集端

    participant S as 流媒体服务器

    participant P as 播放端

    C->>S: 推流 (RTMP/RTSP)

    S->>S: 转码/转封装

    S->>P: RTMP 直播流

    S->>P: HLS 直播流

    S->>P: WebRTC 直播流
```

**5. 播放协议延迟对比**

| 协议 | 延迟 | 优点 | 缺点 | 适用场景 |
|------|------|------|------|----------|
| WebRTC | <1s | 超低延迟，支持 P2P | 架构复杂，成本高 | 视频会议，实时互动 |
| RTMP | 2-3s | 延迟较低，技术成熟 | 需要 Flash 支持 | 直播推流，游戏直播 |
| HLS | >10s | 兼容性好，稳定性高 | 延迟大 | 点播，大规模直播 |

**关键技术点**

**1. 编码原理**

```mermaid
graph LR

    A[视频帧] --> B[I 帧]

    A --> C[P 帧]

    A --> D[B 帧]

    B --> E[完整画面]

    C --> F[参考前帧]

    D --> G[参考前后帧]
```

**2. 直播架构**

```mermaid
graph TD

    A[推流端] --> B[边缘节点]

    B --> C[中心节点]

    C --> D[CDN]

    D --> E[播放端 1]

    D --> F[播放端 2]

    D --> G[播放端 3]
```

**实际应用配置**

**Nginx-RTMP 配置示例**

```nginx
rtmp {
    server {
        listen 1935;
        application live {
            live on;
            hls on;
            hls_path /tmp/hls;
        }
    }
}
```

**带宽计算**

比特率 = 分辨率 × 帧率 × 压缩比

1080p@30fps ≈ 2-4 Mbps
