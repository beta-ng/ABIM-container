# WhaleSlim Optimized Dockerfile
# Summary of key improvements:
# 1. Uses a multi-stage build to download the pre-built binary.
# 2. Final image is based on 'alpine' to provide a minimal environment with a package manager.
# 3. Explicitly installs 'bash' in the final stage to meet user requirements.
# 4. The final image remains very small, secure, and now includes bash.
# 5. Uses an ARG to easily specify the desired fastp version.

# --- Downloader Stage ---
# WhaleSlim Recommendation: Use a minimal base image with necessary tools (like wget) to download the binary.
FROM ubuntu:24.04 AS downloader

# WhaleSlim Recommendation: Use ARG to make the fastp version easily configurable at build time.
ARG FASTP_VERSION=0.23.4

# WhaleSlim Recommendation: Switch APT sources to an Alibaba Cloud mirror, compatible with the new DEB822 format in Ubuntu 24.04+.
RUN sed -i \
    -e 's|http://archive.ubuntu.com/ubuntu/|http://mirrors.aliyun.com/ubuntu/|g' \
    -e 's|http://security.ubuntu.com/ubuntu/|http://mirrors.aliyun.com/ubuntu/|g' \
    /etc/apt/sources.list.d/ubuntu.sources

# WhaleSlim Recommendation: Install only wget, which is the sole dependency for this stage.
# Clean up apt cache in the same layer.
RUN apt-get update && apt-get install -y wget && rm -rf /var/lib/apt/lists/*

# WhaleSlim Recommendation: Download the specified version of the pre-built binary and make it executable.
# The URL format is based on the user's provided link.
RUN wget http://opengene.org/fastp/fastp.${FASTP_VERSION} -O /fastp && \
    chmod a+x /fastp

# --- Final Stage ---
# WhaleSlim Recommendation: Use 'alpine' for a minimal image that includes a robust package manager.
# We use an Alibaba mirror for apk for faster package installation.
FROM alpine:latest

# WhaleSlim Recommendation: Install bash. --no-cache cleans up the cache in the same layer.
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories && \
    apk --no-cache add bash

# WhaleSlim Recommendation: Copy only the downloaded binary from the downloader stage.
COPY --from=downloader /fastp /usr/local/bin/fastp

# WhaleSlim Recommendation: Create a non-root user for security (Least Privilege Principle).
RUN adduser -D -u 1001 nonroot
USER nonroot

# WhaleSlim Recommendation: Set the entrypoint for the container.
ENTRYPOINT ["/usr/local/bin/fastp"]
CMD ["--help"]