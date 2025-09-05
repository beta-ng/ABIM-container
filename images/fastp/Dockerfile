# WhaleSlim Optimized Dockerfile
# Summary of key improvements:
# 1. Uses a multi-stage build to download the pre-built binary, avoiding compilation entirely.
# 2. Final image is now based on 'busybox' to provide a minimal shell environment for debugging and interaction.
# 3. The final image remains extremely small and secure.
# 4. Uses an ARG to easily specify the desired fastp version.
# 5. Correctly updates APT sources for Ubuntu 24.04 (Noble) to use Alibaba Cloud mirrors.

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
# WhaleSlim Recommendation: Use 'busybox' for a minimal image that includes a shell and basic utilities.
FROM busybox:stable-glibc

# WhaleSlim Recommendation: Copy only the downloaded binary from the downloader stage.
COPY --from=downloader /fastp /usr/local/bin/fastp

# WhaleSlim Recommendation: Create a non-root user for security (Least Privilege Principle).
# BusyBox has a /etc/passwd file, so we can add a user.
RUN adduser -D -u 1001 nonroot
USER nonroot

# WhaleSlim Recommendation: Set the entrypoint for the container.
ENTRYPOINT ["/usr/local/bin/fastp"]
CMD ["--help"]