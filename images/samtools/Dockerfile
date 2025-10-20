# ==== Build Stage ====
# Use a pinned alpine version for a reproducible build environment.
FROM alpine:3.20 AS builder

# 1. Set ARG for version control.
ARG SAMTOOLS_VERSION="1.22.1"

# 2. Install build dependencies
RUN apk add --no-cache \
    build-base \
    curl \
    ncurses-dev \
    bzip2-dev \
    xz-dev \
    zlib-dev \
    curl-dev

# 3. Download, extract, and clean up in a single layer
WORKDIR /build
RUN curl -L -o samtools.tar.bz2 \
    "https://github.com/samtools/samtools/releases/download/${SAMTOOLS_VERSION}/samtools-${SAMTOOLS_VERSION}.tar.bz2" \
    && tar -jxvf samtools.tar.bz2 \
    && rm samtools.tar.bz2

# 4. Build and install samtools
RUN cd "samtools-${SAMTOOLS_VERSION}" \
    && ./configure --prefix=/usr/local \
    && make \
    && make install


# ==== Final Stage ====
# Start from a clean, minimal alpine image.
FROM alpine:3.20

# 1. Install *only* runtime dependencies + bash
RUN apk add --no-cache \
    bash \
    ncurses-libs \
    bzip2 \
    xz-libs \
    zlib \
    libcurl

# 2. Copy the compiled binary from the builder stage
COPY --from=builder /usr/local/bin/samtools /usr/local/bin/samtools

# 3. Define a working directory
WORKDIR /data