# DouyinLiveRecorder - Visual Architecture Diagrams

This document contains detailed visual diagrams explaining the architecture and workflow of the DouyinLiveRecorder project.

## 🏗️ System Architecture Overview

```mermaid
graph TB
    subgraph "User Layer"
        UI[Configuration Files]
        CMD[Command Line Interface]
        WEB[Web Interface - index.html]
    end
    
    subgraph "Application Layer"
        MAIN[main.py - Controller]
        CONFIG[Config Manager]
        MONITOR[Stream Monitor]
        SCHEDULER[Task Scheduler]
    end
    
    subgraph "Business Logic Layer"
        SPIDER[Spider Engine]
        STREAM[Stream Processor]
        ROOM[Room Handler]
        PROXY[Proxy Manager]
    end
    
    subgraph "Integration Layer"
        JS[JavaScript Engine]
        HTTP[HTTP Clients]
        FFMPEG[FFmpeg Wrapper]
        PUSH[Push Notifications]
    end
    
    subgraph "Data Layer"
        FILES[Video Files]
        LOGS[Log Files]
        CACHE[Temporary Cache]
        BACKUP[Config Backup]
    end
    
    UI --> MAIN
    CMD --> MAIN
    WEB --> MAIN
    
    MAIN --> CONFIG
    MAIN --> MONITOR
    MAIN --> SCHEDULER
    
    MONITOR --> SPIDER
    MONITOR --> STREAM
    MONITOR --> ROOM
    
    SPIDER --> JS
    SPIDER --> HTTP
    SPIDER --> PROXY
    
    STREAM --> FFMPEG
    MONITOR --> PUSH
    
    FFMPEG --> FILES
    MAIN --> LOGS
    HTTP --> CACHE
    CONFIG --> BACKUP
    
    style MAIN fill:#e1f5fe
    style SPIDER fill:#fff3e0
    style FFMPEG fill:#c8e6c9
    style JS fill:#fce4ec
    style PUSH fill:#f3e5f5
```

## 🔄 Recording Workflow Process

```mermaid
sequenceDiagram
    participant USER as User
    participant MAIN as Main Process
    participant CONF as Config Loader
    participant MON as Monitor
    participant SPIDER as Spider
    participant STREAM as Stream Handler
    participant FFMPEG as FFmpeg
    participant NOTIFY as Notifier
    
    USER->>MAIN: Start Application
    MAIN->>CONF: Load Configuration
    CONF-->>MAIN: Config Data
    
    MAIN->>MON: Initialize Monitor
    MON->>MON: Start Monitoring Loop
    
    loop Every Check Interval
        MON->>SPIDER: Check Stream Status
        SPIDER->>SPIDER: Extract Stream Data
        
        alt Stream is Live
            SPIDER-->>MON: Live Stream Data
            MON->>STREAM: Process Stream URL
            STREAM-->>MON: Playable URL
            
            MON->>FFMPEG: Start Recording
            FFMPEG-->>MON: Recording Started
            
            MON->>NOTIFY: Send Start Notification
            NOTIFY-->>MON: Notification Sent
            
            loop While Recording
                MON->>SPIDER: Check Stream Status
                alt Stream Still Live
                    SPIDER-->>MON: Stream Active
                else Stream Ended
                    SPIDER-->>MON: Stream Offline
                    MON->>FFMPEG: Stop Recording
                    FFMPEG-->>MON: Recording Stopped
                    MON->>NOTIFY: Send End Notification
                end
            end
            
        else Stream Offline
            SPIDER-->>MON: Stream Offline
            MON->>MON: Wait for Next Check
        end
    end
```

## 🕷️ Spider Module Architecture

```mermaid
classDiagram
    class BaseSpider {
        <<abstract>>
        +url: str
        +proxy_addr: str
        +cookies: str
        +get_stream_data()
        +validate_response()
        +handle_errors()
    }
    
    class DouyinSpider {
        +get_douyin_app_stream_data()
        +get_xbogus_signature()
        +extract_room_data()
        +parse_stream_quality()
    }
    
    class TikTokSpider {
        +get_tiktok_stream_data()
        +handle_geo_restrictions()
        +extract_live_data()
        +bypass_rate_limits()
    }
    
    class BilibiliSpider {
        +get_bilibili_stream_data()
        +extract_room_info()
        +get_quality_options()
        +handle_authentication()
    }
    
    class YouTubeSpider {
        +get_youtube_stream_url()
        +extract_live_streams()
        +handle_age_restrictions()
        +parse_manifest()
    }
    
    class PlatformFactory {
        +create_spider(platform)
        +get_supported_platforms()
        +validate_url(url)
    }
    
    BaseSpider <|-- DouyinSpider
    BaseSpider <|-- TikTokSpider
    BaseSpider <|-- BilibiliSpider
    BaseSpider <|-- YouTubeSpider
    PlatformFactory --> BaseSpider
    
    class JavaScriptEngine {
        +execute_script(script)
        +generate_signature(params)
        +decrypt_data(encrypted)
    }
    
    class HTTPClient {
        +async_request(url)
        +handle_cookies()
        +manage_proxy()
        +retry_mechanism()
    }
    
    DouyinSpider --> JavaScriptEngine
    TikTokSpider --> JavaScriptEngine
    BaseSpider --> HTTPClient
```

## 📊 Data Flow Architecture

```mermaid
flowchart LR
    subgraph "Input Sources"
        URL_CONFIG[URL Config File]
        MANUAL_INPUT[Manual URL Input]
        API_INPUT[API Input]
    end
    
    subgraph "URL Processing"
        URL_PARSER[URL Parser]
        PLATFORM_DETECTOR[Platform Detector]
        URL_VALIDATOR[URL Validator]
    end
    
    subgraph "Data Extraction"
        SPIDER_ENGINE[Spider Engine]
        JS_ENGINE[JavaScript Engine]
        HTTP_CLIENT[HTTP Client]
    end
    
    subgraph "Stream Processing"
        STREAM_ANALYZER[Stream Analyzer]
        QUALITY_SELECTOR[Quality Selector]
        URL_GENERATOR[Stream URL Generator]
    end
    
    subgraph "Recording Engine"
        FFMPEG_CONTROLLER[FFmpeg Controller]
        PROCESS_MANAGER[Process Manager]
        FILE_HANDLER[File Handler]
    end
    
    subgraph "Output Processing"
        VIDEO_CONVERTER[Video Converter]
        FILE_ORGANIZER[File Organizer]
        METADATA_EXTRACTOR[Metadata Extractor]
    end
    
    subgraph "Notification System"
        MESSAGE_FORMATTER[Message Formatter]
        MULTI_CHANNEL_SENDER[Multi-Channel Sender]
        DELIVERY_TRACKER[Delivery Tracker]
    end
    
    URL_CONFIG --> URL_PARSER
    MANUAL_INPUT --> URL_PARSER
    API_INPUT --> URL_PARSER
    
    URL_PARSER --> PLATFORM_DETECTOR
    PLATFORM_DETECTOR --> URL_VALIDATOR
    URL_VALIDATOR --> SPIDER_ENGINE
    
    SPIDER_ENGINE --> JS_ENGINE
    SPIDER_ENGINE --> HTTP_CLIENT
    SPIDER_ENGINE --> STREAM_ANALYZER
    
    STREAM_ANALYZER --> QUALITY_SELECTOR
    QUALITY_SELECTOR --> URL_GENERATOR
    URL_GENERATOR --> FFMPEG_CONTROLLER
    
    FFMPEG_CONTROLLER --> PROCESS_MANAGER
    PROCESS_MANAGER --> FILE_HANDLER
    FILE_HANDLER --> VIDEO_CONVERTER
    
    VIDEO_CONVERTER --> FILE_ORGANIZER
    FILE_ORGANIZER --> METADATA_EXTRACTOR
    
    SPIDER_ENGINE --> MESSAGE_FORMATTER
    MESSAGE_FORMATTER --> MULTI_CHANNEL_SENDER
    MULTI_CHANNEL_SENDER --> DELIVERY_TRACKER
    
    style URL_CONFIG fill:#e3f2fd
    style SPIDER_ENGINE fill:#fff3e0
    style FFMPEG_CONTROLLER fill:#e8f5e8
    style MESSAGE_FORMATTER fill:#fce4ec
```

## 🔧 Component Interaction Diagram

```mermaid
graph TD
    subgraph "Core System"
        MAIN_CONTROLLER[Main Controller]
        CONFIG_MANAGER[Config Manager]
        MONITOR_SERVICE[Monitor Service]
        ERROR_HANDLER[Error Handler]
    end
    
    subgraph "Stream Processing"
        SPIDER_DISPATCHER[Spider Dispatcher]
        PLATFORM_ADAPTERS[Platform Adapters]
        STREAM_EXTRACTOR[Stream Extractor]
        QUALITY_MANAGER[Quality Manager]
    end
    
    subgraph "Recording System"
        RECORDING_SCHEDULER[Recording Scheduler]
        FFMPEG_MANAGER[FFmpeg Manager]
        FILE_MANAGER[File Manager]
        PROCESS_MONITOR[Process Monitor]
    end
    
    subgraph "External Services"
        NOTIFICATION_HUB[Notification Hub]
        PROXY_SERVICE[Proxy Service]
        LOGGING_SERVICE[Logging Service]
        METRICS_COLLECTOR[Metrics Collector]
    end
    
    MAIN_CONTROLLER <--> CONFIG_MANAGER
    MAIN_CONTROLLER <--> MONITOR_SERVICE
    MAIN_CONTROLLER <--> ERROR_HANDLER
    
    MONITOR_SERVICE <--> SPIDER_DISPATCHER
    SPIDER_DISPATCHER <--> PLATFORM_ADAPTERS
    PLATFORM_ADAPTERS <--> STREAM_EXTRACTOR
    STREAM_EXTRACTOR <--> QUALITY_MANAGER
    
    MONITOR_SERVICE <--> RECORDING_SCHEDULER
    RECORDING_SCHEDULER <--> FFMPEG_MANAGER
    FFMPEG_MANAGER <--> FILE_MANAGER
    FFMPEG_MANAGER <--> PROCESS_MONITOR
    
    MONITOR_SERVICE <--> NOTIFICATION_HUB
    PLATFORM_ADAPTERS <--> PROXY_SERVICE
    ERROR_HANDLER <--> LOGGING_SERVICE
    MONITOR_SERVICE <--> METRICS_COLLECTOR
    
    style MAIN_CONTROLLER fill:#e1f5fe
    style SPIDER_DISPATCHER fill:#fff3e0
    style FFMPEG_MANAGER fill:#e8f5e8
    style NOTIFICATION_HUB fill:#fce4ec
```

## 🌐 Platform Support Matrix

```mermaid
mindmap
  root((Platform Support))
    Chinese Platforms
      Douyin
        Live Streaming
        Short Videos
        Mobile First
      Bilibili
        Video Sharing
        Live Streaming
        Anime Focus
      Kuaishou
        Short Videos
        Live Streaming
        Rural Focus
      Huya
        Gaming Streams
        Esports
        Live Chat
      Others
        YY
        Douyu
        Xiaohongshu
    International Platforms
      TikTok
        Global Douyin
        Geo Restrictions
        Mobile Centric
      YouTube
        Google Platform
        Multiple Qualities
        Live Streaming
      Twitch
        Gaming Focus
        Amazon Owned
        Interactive Features
      Others
        ShowRoom
        LiveMe
        Shopee
    Regional Platforms
      Korean
        SOOP
        WinkTV
        FlexTV
      Japanese
        TwitCasting
        ShowRoom
      Others
        Various Regions
        Specialized Content
```

## 🔄 Error Handling Flow

```mermaid
stateDiagram-v2
    [*] --> Monitoring
    
    Monitoring --> StreamCheck
    StreamCheck --> Live : Stream Online
    StreamCheck --> Offline : Stream Offline
    StreamCheck --> Error : Check Failed
    
    Live --> Recording
    Recording --> RecordingActive : Success
    Recording --> RecordingError : Failure
    
    RecordingActive --> StreamCheck : Continue Monitoring
    RecordingActive --> RecordingComplete : Stream Ended
    
    RecordingError --> RetryRecording : Retry Available
    RecordingError --> ErrorLogged : Max Retries Exceeded
    
    RetryRecording --> Recording : Attempt Restart
    
    RecordingComplete --> PostProcess
    PostProcess --> FileReady : Success
    PostProcess --> PostProcessError : Failure
    
    FileReady --> Notification
    PostProcessError --> ErrorLogged
    
    Notification --> Monitoring : Continue
    
    Offline --> WaitRetry
    WaitRetry --> StreamCheck : After Delay
    
    Error --> ErrorAnalysis
    ErrorAnalysis --> Retry : Recoverable
    ErrorAnalysis --> ErrorLogged : Fatal
    
    Retry --> StreamCheck
    ErrorLogged --> Monitoring : Continue with Other Streams
    
    state RecordingActive {
        [*] --> Starting
        Starting --> Active
        Active --> Monitoring_Quality
        Monitoring_Quality --> Active
        Active --> Stopping
        Stopping --> [*]
    }
```

## 📈 Performance Monitoring Dashboard

```mermaid
graph TB
    subgraph "System Metrics"
        CPU[CPU Usage: 35%]
        MEMORY[Memory: 2.1GB/8GB]
        DISK[Disk: 850GB/1TB]
        NETWORK[Network: 45MB/s]
    end
    
    subgraph "Application Metrics"
        ACTIVE_RECORDINGS[Active Recordings: 12]
        QUEUE_SIZE[Queue Size: 5]
        SUCCESS_RATE[Success Rate: 98.5%]
        ERROR_RATE[Error Rate: 1.5%]
    end
    
    subgraph "Stream Status"
        ONLINE_STREAMS[Online: 23]
        OFFLINE_STREAMS[Offline: 67]
        FAILED_STREAMS[Failed: 3]
        TOTAL_STREAMS[Total Monitored: 93]
    end
    
    subgraph "Performance Indicators"
        AVG_RESPONSE_TIME[Avg Response: 245ms]
        CONCURRENT_REQUESTS[Concurrent: 8/10]
        THROUGHPUT[Throughput: 125 req/min]
        UPTIME[Uptime: 99.8%]
    end
    
    style ACTIVE_RECORDINGS fill:#c8e6c9
    style SUCCESS_RATE fill:#c8e6c9
    style ONLINE_STREAMS fill:#c8e6c9
    style UPTIME fill:#c8e6c9
    
    style ERROR_RATE fill:#ffcdd2
    style FAILED_STREAMS fill:#ffcdd2
    
    style CPU fill:#fff3e0
    style MEMORY fill:#fff3e0
    style QUEUE_SIZE fill:#fff3e0
    style AVG_RESPONSE_TIME fill:#fff3e0
```

## 🛠️ Development Architecture

```mermaid
graph LR
    subgraph "Development Environment"
        IDE[IDE/Editor]
        DEBUGGER[Debugger]
        PROFILER[Profiler]
        TESTER[Test Runner]
    end
    
    subgraph "Source Control"
        GIT[Git Repository]
        BRANCHES[Feature Branches]
        RELEASES[Release Tags]
        ISSUES[Issue Tracker]
    end
    
    subgraph "CI/CD Pipeline"
        BUILD[Build System]
        TESTS[Automated Tests]
        DEPLOY[Deployment]
        DOCKER[Docker Build]
    end
    
    subgraph "Monitoring & Maintenance"
        LOGS[Log Analysis]
        METRICS[Performance Metrics]
        ALERTS[Alert System]
        BACKUP[Backup System]
    end
    
    IDE --> GIT
    DEBUGGER --> GIT
    PROFILER --> METRICS
    TESTER --> TESTS
    
    GIT --> BUILD
    BRANCHES --> BUILD
    BUILD --> TESTS
    TESTS --> DEPLOY
    DEPLOY --> DOCKER
    
    DEPLOY --> LOGS
    METRICS --> ALERTS
    LOGS --> BACKUP
    ALERTS --> ISSUES
    
    style IDE fill:#e3f2fd
    style GIT fill:#e8f5e8
    style BUILD fill:#fff3e0
    style METRICS fill:#fce4ec
```

---

*These diagrams provide a comprehensive visual understanding of the DouyinLiveRecorder architecture, from high-level system overview to detailed component interactions.*
