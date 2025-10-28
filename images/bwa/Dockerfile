# Stage 1: Build BWA v0.7.19 from source using Debian
FROM debian:bullseye AS builder

# Define BWA version and download URL
ARG BWA_VERSION=0.7.19
ARG BWA_URL="https://github.com/lh3/bwa/archive/refs/tags/v${BWA_VERSION}.tar.gz"

# Install build dependencies, download tools, and CA certificates
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        zlib1g-dev \
        wget \
        tar \
        ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Set working directory
WORKDIR /app

# Download and extract BWA source code
RUN wget --no-check-certificate -O bwa.tar.gz "${BWA_URL}" && \
    tar -xzf bwa.tar.gz && \
    rm bwa.tar.gz

# Compile BWA
WORKDIR /app/bwa-${BWA_VERSION}
RUN make

# Stage 2: Create the final lean image using Debian Slim (glibc compatible)
FROM debian:bullseye-slim

# Re-declare ARG for use in this stage
ARG BWA_VERSION=0.7.19

# Install runtime dependencies (zlib) and bash
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        zlib1g \
        bash \
    && rm -rf /var/lib/apt/lists/*

# Set working directory to /data as requested by user
WORKDIR /data

# Copy the compiled BWA executable from the builder stage
COPY --from=builder /app/bwa-${BWA_VERSION}/bwa /usr/local/bin/bwa
