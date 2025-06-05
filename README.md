# go-http

Golang http common functionalities

## Features

- Custom `ResponseWriter` for capturing HTTP status codes
- Middleware for structured access logging using `log/slog`

## Installation

```
go get github.com/rsgcata/go-http
```

## Usage

### 1. Custom ResponseWriter

The package provides a `ResponseWriter` wrapper that caches the status code written by your handler. This is useful for logging and middleware.

**Example:**

```go
import (
    "github.com/rsgcata/go-http/http"
    "net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
    rw := http.NewResponseWriter(w)
    rw.WriteHeader(404)
    // ...
    status := rw.StatusCode() // status == 404
}
```

### 2. Access Logging Middleware

The middleware logs HTTP request and response details in structured JSON using `log/slog`.

**Example:**

```go
import (
    "github.com/rsgcata/go-http/http/router/middleware"
    "log/slog"
    "net/http"
    "os"
)

logger := slog.New(slog.NewJSONHandler(os.Stdout, &slog.HandlerOptions{}))

accessLogger := middleware.NewHttpAccessLogger(
    http.HandlerFunc(yourHandler),
    logger,
    middleware.AccessLogOptions{LogClientIp: true},
)

http.ListenAndServe(":8080", accessLogger)
```

**Log Output Example:**

```json
{
  "level": "INFO",
  "msg": "HTTP Request",
  "Client IP": "127.0.0.1",
  "Method": "GET",
  "Host": "localhost:8080",
  "Path": "/",
  "Query": "",
  "Protocol": "HTTP/1.1",
  "User Agent": "curl/7.68.0",
  "Response Status Code": "200",
  "Duration (s)": "0.01"
}
```

## Testing

The package uses [testify](https://github.com/stretchr/testify) for unit tests. Run tests with:

```
go test ./...
```

## License

MIT License
