# ==============================================================================
# Stage 1: Builder
# Build STAR from source
# ==============================================================================
FROM debian:bullseye AS builder

# Define build arguments for versioning
ARG STAR_VERSION=2.7.11b

# Switch to Aliyun mirror for faster package installation
RUN sed -i 's|deb.debian.org|mirrors.aliyun.com|g' /etc/apt/sources.list && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        wget \
        ca-certificates \
        xxd \
        zlib1g-dev && \
    # Download and compile STAR
    wget https://github.com/alexdobin/STAR/archive/refs/tags/${STAR_VERSION}.tar.gz -O star.tar.gz && \
    tar -xzf star.tar.gz && \
    cd STAR-${STAR_VERSION}/source && \
    make STAR && \
    # Clean up
    rm -rf /var/lib/apt/lists/* /star.tar.gz

# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM debian:bullseye-slim

# Re-declare ARG for use in this stage
ARG STAR_VERSION=2.7.11b

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Install runtime dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends libgomp1 && \
    rm -rf /var/lib/apt/lists/*

# Copy the compiled STAR binary from the builder stage
COPY --from=builder /STAR-${STAR_VERSION}/source/STAR /usr/local/bin/STAR

# Set working directory
WORKDIR /data