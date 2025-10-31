# ==============================================================================
# Stage 1: Builder
# Build BWA from source using Debian
# ==============================================================================
FROM debian:bullseye AS builder

# Define build arguments for versioning
ARG BWA_VERSION=0.7.19
ARG BWA_URL="https://github.com/lh3/bwa/archive/refs/tags/v${BWA_VERSION}.tar.gz"

# Install build dependencies
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

# ==============================================================================
# Stage 2: Final Runtime Image
# Create the final lean image using Debian Slim (glibc compatible)
# ==============================================================================
FROM debian:bullseye-slim

# Re-declare ARG for use in this stage
ARG BWA_VERSION=0.7.19

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>" \
      version="${BWA_VERSION}" \
      description="Burrows-Wheeler Aligner"

# Install runtime dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        zlib1g \
        bash \
    && rm -rf /var/lib/apt/lists/*

# Copy the compiled BWA executable from the builder stage
COPY --from=builder /app/bwa-${BWA_VERSION}/bwa /usr/local/bin/bwa

# Set working directory
WORKDIR /data