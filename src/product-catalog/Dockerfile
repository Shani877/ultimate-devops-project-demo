# Build stage
FROM golang:1.22-alpine AS builder

WORKDIR /usr/src/app/

# Use Go build cache for dependencies
RUN --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    mkdir -p /root/.cache/go-build

# Copy Go modules files first for efficient caching
COPY go.mod go.sum ./

# Download dependencies
RUN go mod download

# Copy the rest of the source code
COPY . .

# Build the Go binary
RUN CGO_ENABLED=0 go build -o product-catalog .

####################################

# Release stage
FROM alpine AS release

WORKDIR /usr/src/app/

# Copy necessary files
COPY ./products/ ./products/
COPY --from=builder /usr/src/app/product-catalog ./

# Set environment variable
ENV PRODUCT_CATALOG_PORT=8088

# Run the app
ENTRYPOINT [ "./product-catalog" ]
