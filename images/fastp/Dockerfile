# ==============================================================================
# Stage 1: Downloader
# Fetches a specific version of the pre-compiled fastp binary.
# ==============================================================================
FROM ubuntu:24.04 AS downloader

# Define build arguments for versioning
ARG FASTP_VERSION=0.23.4

# Switch APT sources to a regional mirror for faster package installation
RUN sed -i \
    -e 's|http://archive.ubuntu.com/ubuntu/|http://mirrors.aliyun.com/ubuntu/|g' \
    -e 's|http://security.ubuntu.com/ubuntu/|http://mirrors.aliyun.com/ubuntu/|g' \
    /etc/apt/sources.list.d/ubuntu.sources

# Install wget, download the binary, and clean up in a single layer
RUN apt-get update && \
    apt-get install -y wget && \
    rm -rf /var/lib/apt/lists/* && \
    wget http://opengene.org/fastp/fastp.${FASTP_VERSION} -O /fastp && \
    chmod a+x /fastp


# ==============================================================================
# Stage 2: Final Runtime Image
# Creates a minimal final image containing only the fastp binary and bash.
# ==============================================================================
FROM alpine:latest

# Re-declare ARG for use in this stage
ARG FASTP_VERSION=0.23.4

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>" \
      version="${FASTP_VERSION}" \
      description="Fast all-in-one FASTQ preprocessor"

# Switch APK sources to a regional mirror and install bash
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && \
    apk --no-cache add bash

# Copy the fastp binary from the downloader stage
COPY --from=downloader /fastp /usr/local/bin/fastp

# Set working directory
WORKDIR /data