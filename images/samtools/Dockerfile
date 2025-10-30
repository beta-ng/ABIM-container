# ==============================================================================
# Stage 1: Builder
# Build Samtools from source
# ==============================================================================
FROM alpine:3.20 AS builder

# Define build arguments for versioning
ARG SAMTOOLS_VERSION="1.22.1"

# Install build dependencies
RUN apk add --no-cache \
    build-base \
    curl \
    ncurses-dev \
    bzip2-dev \
    xz-dev \
    zlib-dev \
    curl-dev

# Set working directory
WORKDIR /build

# Download and extract Samtools source code
RUN curl -L -o samtools.tar.bz2 \
    "https://github.com/samtools/samtools/releases/download/${SAMTOOLS_VERSION}/samtools-${SAMTOOLS_VERSION}.tar.bz2" \
    && tar -jxvf samtools.tar.bz2 \
    && rm samtools.tar.bz2

# Build and install Samtools
RUN cd "samtools-${SAMTOOLS_VERSION}" \
    && ./configure --prefix=/usr/local \
    && make \
    && make install


# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM alpine:3.20

# Re-declare ARG for use in this stage
ARG SAMTOOLS_VERSION="1.22.1"

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Install only the required runtime libraries
RUN apk add --no-cache \
    bash \
    zlib \
    libbz2 \
    xz-libs \
    ncurses-libs \
    libcurl

# Copy the compiled binary from the builder stage
COPY --from=builder /usr/local/bin/samtools /usr/local/bin/samtools

# Set working directory
WORKDIR /data