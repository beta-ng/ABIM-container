# ==============================================================================
# Stage 1: Builder
# Use a Debian-based builder to match the final image, ensuring library compatibility.
# ==============================================================================
FROM debian:bullseye AS builder

# Define build arguments for versioning
ARG STAR_VERSION=2.7.8a
ARG ARRIBA_VERSION=2.5.1

# Set a working directory
WORKDIR /usr/local/src

# Install build-time dependencies for both STAR and Arriba
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        build-essential \
        ca-certificates \
        wget \
        zlib1g-dev \
        libcurl4-openssl-dev \
        libdb-dev \
        libssl-dev \
        libxml2-dev && \
    # Build STAR
    wget https://github.com/alexdobin/STAR/archive/refs/tags/${STAR_VERSION}.tar.gz && \
    tar -xzf ${STAR_VERSION}.tar.gz && \
    cd STAR-${STAR_VERSION}/source && \
    make STAR && \
    cd ../.. && \
    # Build Arriba
    wget https://github.com/suhrig/arriba/archive/refs/tags/v${ARRIBA_VERSION}.tar.gz && \
    tar -xzf v${ARRIBA_VERSION}.tar.gz && \
    cd arriba-${ARRIBA_VERSION} && \
    make && \
    # Clean up build environment
    apt-get purge -y --auto-remove build-essential wget && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /usr/local/src/*.tar.gz

# ==============================================================================
# Stage 2: Final Runtime Image
# Use the corresponding slim version for a minimal final image.
# ==============================================================================
FROM debian:bullseye-slim

# Re-declare ARGs needed in this stage
ARG STAR_VERSION=2.7.8a
ARG ARRIBA_VERSION=2.5.1

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>" \
      version="${ARRIBA_VERSION}" \
      description="Arriba fusion detection tool"

# Install only the necessary runtime dependencies
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        samtools \
        libcurl4 \
        libbz2-1.0 \
        liblzma5 \
        libgomp1 \
        libstdc++6 && \
    # Clean up
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Copy the compiled binaries and scripts from the builder stage
COPY --from=builder /usr/local/src/STAR-${STAR_VERSION}/source/STAR /usr/local/bin/
COPY --from=builder /usr/local/src/arriba-${ARRIBA_VERSION} /arriba_v${ARRIBA_VERSION}

# Set a default working directory for user data
WORKDIR /data