# ==============================================================================
# Stage 1: Builder
# Clone and build gffread from source
# ==============================================================================
FROM ubuntu:22.04 AS builder

# Define build arguments for versioning
ARG GFFREAD_VERSION=0.12.7

# Set environment
ENV DEBIAN_FRONTEND=noninteractive

# Set working directory
WORKDIR /build

# Install build dependencies
RUN apt-get update && \
    apt-get install -y \
        build-essential \
        git \
        wget \
        ca-certificates && \
    rm -rf /var/lib/apt/lists/*

# Clone and build gffread from source
RUN git clone --branch v${GFFREAD_VERSION} https://github.com/gpertea/gffread.git && \
    cd gffread && \
    make release && \
    chmod +x gffread && \
    ls -la gffread

# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM alpine:3.19

# Re-declare ARG for use in this stage
ARG GFFREAD_VERSION=0.12.7

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Install runtime dependencies
RUN apk add --no-cache \
        bash \
        libstdc++ \
        libgcc \
        gcompat \
        libc6-compat && \
    rm -rf /var/cache/apk/*

# Copy binary from builder stage
COPY --from=builder --chmod=755 /build/gffread/gffread /usr/local/bin/gffread

# Set working directory
WORKDIR /data