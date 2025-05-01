# Shinra Proxy

A powerful and efficient CORS proxy server built with Express.js and TypeScript, specifically designed to handle streaming media formats like M3U8 playlists, MPEG-TS segments, and various other media formats.

## Features

- **Full CORS Support**: Handles preflight requests and sets appropriate headers for cross-origin requests
- **Multiple URL Formats**: Support for query parameters, path parameters, and base64-encoded URLs
- **Advanced Media Handling**:
  - Intelligent processing for M3U8 playlists with automatic URL rewriting
  - Special handling for TS segments, subtitles, and other streaming formats
  - Automatic detection of content types based on binary signatures
- **Comprehensive Compression Support**:
  - Built-in support for GZIP, Brotli, Zstandard (ZSTD), and Deflate compression
  - Automatic compression format detection based on magic bytes
- **Anti-Hotlinking Protection Bypass**:
  - Domain-specific header templates for common streaming services
  - Intelligent referrer and origin handling for media CDNs
- **Performance Optimizations**:
  - Worker thread pool for resource-intensive tasks like decompression
  - Efficient memory usage for streaming large files
  - Intelligent caching system with size and TTL controls
- **Security Features**:
  - URL validation with customizable rules
  - Optional domain whitelisting
  - Request size limits and timeouts
- **Detailed Monitoring**:
  - Comprehensive logging with customizable levels
  - Performance metrics and statistics endpoints
  - Detailed error handling with clear messages

## Installation

```bash
# Clone the repository
git clone https://github.com/yourusername/shinra-proxy.git
cd shinra-proxy

# Install dependencies
npm install

# Create .env file from example
cp .env.example .env

# Build the project
npm run build

# Start the server
npm start
```

## Usage

The proxy supports multiple ways to specify target URLs:

### 1. Query Parameter

```
http://localhost:3000/proxy?url=https://example.com/video.m3u8
```

### 2. Path Parameter

```
http://localhost:3000/proxy/https://example.com/video.m3u8
```

### 3. Base64-Encoded Parameter

Useful for complex URLs with special characters:

```
http://localhost:3000/proxy/base64/aHR0cHM6Ly9leGFtcGxlLmNvbS92aWRlby5tM3U4
```

### Streaming Media Support

Shinra Proxy has specialized handling for various streaming media formats:

- **HLS Playlists**: Automatically processes M3U8 playlists to rewrite all segment URLs
- **MPEG-TS Segments**: Handles TS segments with proper content type detection
- **WebVTT Subtitles**: Processes VTT files to rewrite image URLs
- **Image Segments**: Some services use JPG extensions for TS segments - Shinra handles this correctly

Example for proxying an HLS stream:

```
http://localhost:3000/proxy?url=https://streaming-site.com/master.m3u8
```

## Configuration

The proxy is configured using environment variables:

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | Server port | `3000` |
| `NODE_ENV` | Environment mode | `development` |
| `LOG_LEVEL` | Logging level | `info` |
| `PROXY_BASE` | Base path for proxy routes | `/proxy` |
| `MAX_REQUEST_SIZE` | Max request size in bytes | `8192` (8KB) |
| `REQUEST_TIMEOUT` | Request timeout in milliseconds | `30000` (30s) |
| `ENABLE_DOMAIN_WHITELIST` | Enable domain whitelist | `false` |
| `ALLOWED_DOMAINS` | Comma-separated list of allowed domains | `*` (all) |
| `ALLOWED_ORIGINS` | CORS allowed origins | `*` (all) |
| `ENABLE_CACHE` | Enable response caching | `true` |
| `CACHE_TTL` | Cache TTL in seconds | `300` (5 min) |
| `CACHE_CHECK_PERIOD` | Cache cleanup interval in seconds | `600` (10 min) |
| `CACHE_MAX_ITEMS` | Maximum number of cached items | `1000` |
| `CACHE_MAX_SIZE` | Maximum cache size in bytes | `104857600` (100MB) |
| `USE_WORKER_THREADS` | Enable worker threads for decompression | `true` |
| `WORKER_THREADS` | Number of worker threads (0 = auto) | `0` |
| `ENABLE_STREAMING` | Enable optimized streaming for large files | `true` |
| `STREAM_SIZE_THRESHOLD` | Size threshold for streaming mode in bytes | `1048576` (1MB) |

## API Endpoints

### Status Endpoints

- **GET /proxy/status**: Get server status and resource usage
  ```json
  {
    "status": "ok",
    "version": "0.2.0",
    "uptime": 3654.8,
    "timestamp": "2025-05-01T15:30:45.123Z",
    "environment": "production",
    "memory": {
      "rss": 56.23,
      "heapTotal": 32.75,
      "heapUsed": 27.12,
      "external": 2.34
    }
  }
  ```

- **GET /proxy/cache/stats**: Get cache statistics
  ```json
  {
    "status": "ok",
    "data": {
      "enabled": true,
      "size": "45.2 MB",
      "maxSize": "100 MB",
      "items": 328,
      "maxItems": 1000,
      "hitRatio": "0.78",
      "hits": 1249,
      "misses": 352
    },
    "timestamp": "2025-05-01T15:30:45.123Z"
  }
  ```

- **POST /proxy/cache/clear**: Clear the cache
  ```json
  {
    "status": "ok",
    "message": "Cache cleared successfully",
    "timestamp": "2025-05-01T15:30:45.123Z"
  }
  ```

- **GET /proxy/workers/stats**: Get worker thread statistics
  ```json
  {
    "status": "ok",
    "data": {
      "enabled": true,
      "threadsAvailable": 8,
      "threadsRunning": 3,
      "maxThreads": 8,
      "queueSize": 0
    },
    "timestamp": "2025-05-01T15:30:45.123Z"
  }
  ```

- **GET /proxy/metrics**: Get performance metrics
  ```json
  {
    "status": "ok",
    "data": {
      "server": {
        "uptime": "2 hr 45 min",
        "system": {
          "uptime": "5 days 7 hr",
          "cpus": 8,
          "loadAvg": [1.25, 0.86, 0.52],
          "memory": {
            "total": "16 GB",
            "used": "8.7 GB",
            "free": "7.3 GB",
            "usedPercent": "54.38%"
          },
          "platform": "linux",
          "arch": "x64"
        },
        "process": {
          "uptime": "2 hr 45 min",
          "pid": 12345,
          "memory": {
            "rss": "56.23 MB",
            "heapTotal": "32.75 MB",
            "heapUsed": "27.12 MB",
            "external": "2.34 MB"
          },
          "versions": {
            "node": "v18.16.0"
          }
        }
      },
      "requests": {
        "total": 1601,
        "success": 1578,
        "error": 23,
        "successRate": "98.56%"
      },
      "performance": {
        "avgResponseTime": "187 ms",
        "maxResponseTime": "3.24 sec",
        "throughput": {
          "bytesIn": "24.7 MB",
          "bytesOut": "458.2 MB",
          "totalTransferred": "482.9 MB"
        }
      },
      "features": {
        "streaming": {
          "requests": 421,
          "totalSize": "387.5 MB",
          "avgSize": "920.4 KB"
        },
        "cache": {
          "hits": 1249,
          "misses": 352,
          "hitRatio": "78.01%"
        },
        "workers": {
          "tasks": 1892,
          "errors": 7,
          "successRate": "99.63%"
        }
      }
    },
    "timestamp": "2025-05-01T15:30:45.123Z"
  }
  ```

- **POST /proxy/metrics/reset**: Reset performance metrics
  ```json
  {
    "status": "ok",
    "message": "Performance metrics reset successfully",
    "timestamp": "2025-05-01T15:30:45.123Z"
  }
  ```

## Development

```bash
# Start development server with hot reloading
npm run dev

# Run tests
npm run test

# Run linter
npm run lint
```

## Advanced Features

### Domain Templates

Shinra Proxy includes a domain template system to bypass anti-hotlinking protection. The templates in `src/config/domain-templates.ts` define specific headers to use for different domains.

### Custom Content Type Detection

The proxy can detect content types based on binary signatures, overriding incorrect content types returned by servers.

### Adaptive Decompression

Shinra automatically detects and handles various compression formats, even when content-encoding headers are incorrect or missing.

## License

MIT
