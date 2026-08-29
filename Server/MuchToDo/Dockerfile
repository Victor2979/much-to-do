FROM golang:1.25-alpine AS builder
ENV GOFLAGS=-mod=mod
RUN apk add --no-cache git ca-certificates
WORKDIR /build
COPY . .
RUN go mod download
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-w -s" -o /build/app ./cmd/api

FROM alpine:3.20
RUN apk add --no-cache ca-certificates wget
RUN adduser -D -u 1001 appuser
WORKDIR /app
COPY --from=builder /build/app /app/app
COPY entrypoint.sh /app/entrypoint.sh
RUN chmod +x /app/entrypoint.sh && chown -R appuser:appuser /app
USER appuser
EXPOSE 8080
HEALTHCHECK --interval=30s --timeout=5s --start-period=15s --retries=3 CMD wget -qO- http://localhost:8080/health || exit 1
ENTRYPOINT ["/app/entrypoint.sh"]
