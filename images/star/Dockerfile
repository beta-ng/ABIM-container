# --- Build Stage ---
# FIX: Changed builder to debian:bullseye to match the final stage's OS.
FROM debian:bullseye AS builder

ARG STAR_VERSION=2.7.11b

RUN \
    # Debian does not use sources.list.d/ubuntu.sources, directly edit sources.list
    sed -i 's|deb.debian.org|mirrors.aliyun.com|g' /etc/apt/sources.list && \
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

# --- Final Stage ---
# Now this stage is compatible with the builder
FROM debian:bullseye-slim

ARG STAR_VERSION=2.7.11b

RUN apt-get update && \
    apt-get install -y --no-install-recommends libgomp1 && \
    rm -rf /var/lib/apt/lists/*

COPY --from=builder /STAR-${STAR_VERSION}/source/STAR /usr/local/bin/STAR

ENTRYPOINT ["/usr/local/bin/STAR"]
CMD ["--help"]