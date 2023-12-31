#### Bulding executable
FROM golang:1.18.6 as build-stage

# Build Delve
RUN go install github.com/go-delve/delve/cmd/dlv@latest

WORKDIR $GOPATH/src/packages/web-server
COPY ../.. .

# Install dependencies
RUN go mod download

# Build the binary
RUN GOARCH=amd64 GOOS=linux go build -gcflags="all=-N -l" -o /build-output/web-server main.go

#### Building small image
FROM ubuntu:kinetic as image
ENV GOENV="development"

WORKDIR /app

# Copy executable
COPY --from=build-stage /go/bin/dlv ./
COPY --from=build-stage /build-output/web-server ./
COPY ../../config/compose.toml ./config/server.toml

EXPOSE 8080 40000

ENTRYPOINT ["/app/dlv", "--listen=:40000", "--headless=true", "--api-version=2", "--accept-multiclient", "exec", "/app/web-server"]
