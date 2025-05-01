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
git clone https://github.com/xciphertv/shinra-proxy.git
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
- **GET /proxy/cache/stats**: Get cache statistics
- **POST /proxy/cache/clear**: Clear the cache
- **GET /proxy/workers/stats**: Get worker thread statistics
- **GET /proxy/metrics**: Get performance metrics
- **POST /proxy/metrics/reset**: Reset performance metrics

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