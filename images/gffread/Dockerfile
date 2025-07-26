ARG GFFREAD_VERSION=0.12.7

FROM ubuntu:22.04 AS builder

ARG GFFREAD_VERSION
ENV DEBIAN_FRONTEND=noninteractive

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

FROM alpine:3.19

ARG GFFREAD_VERSION
LABEL maintainer="Your Name <your.email@example.com>"
LABEL description="GFFRead - GFF/GTF parsing and manipulation tool"
LABEL version="${GFFREAD_VERSION}"

# Install runtime dependencies
RUN apk add --no-cache \
        bash \
        libstdc++ \
        libgcc \
        gcompat \
        libc6-compat && \
    rm -rf /var/cache/apk/*

# Create non-root user
ARG USER_ID=1000
ARG GROUP_ID=1000
RUN addgroup -g ${GROUP_ID} gffread && \
    adduser -u ${USER_ID} -G gffread -s /bin/bash -D gffread

# Copy binary from builder stage
COPY --from=builder --chmod=755 /build/gffread/gffread /usr/local/bin/gffread

# Switch to non-root user
USER gffread

# Set working directory
WORKDIR /data

# Default entrypoint and command
ENTRYPOINT ["gffread"]
CMD ["--help"]