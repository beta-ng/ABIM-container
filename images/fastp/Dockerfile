# WhaleSlim Optimized Dockerfile
# Summary of key improvements:
# 1. Uses a multi-stage build to create a minimal final image.
# 2. The final image is built from 'scratch' for ultimate size reduction and security.
# 3. Pins the application version for reproducible builds.
# 4. Sets a clear ENTRYPOINT to make the container behave like the executable.

# --- Build Stage ---
# This stage uses a full OS (alpine) to download the tool.
# It will be discarded after the build is complete.
FROM alpine:3.20 AS builder

# WhaleSlim Recommendation: Use a pinned base image version (e.g., alpine:3.20)
# instead of 'latest' to ensure build reproducibility.
LABEL stage=builder

# WhaleSlim Recommendation: Consolidate RUN commands to reduce layers.
# Here we download, rename, and set permissions in a single step.
RUN wget http://opengene.org/fastp/fastp.0.23.4 \
    && mv fastp.0.23.4 /tmp/fastp \
    && chmod a+x /tmp/fastp

# --- Final Stage ---
# This is the final, production-ready image.
# It starts from a completely empty base image ('scratch').
FROM scratch

# WhaleSlim Recommendation: Copy only the required artifact (the binary) from
# the builder stage. This is the core of the multi-stage build pattern.
COPY --from=builder /tmp/fastp /fastp

# WhaleSlim Recommendation: Use ENTRYPOINT for tool-based containers. This makes
# the container executable, behaving just like the tool itself.
# For example: docker run <image_name> --version
ENTRYPOINT ["/fastp"]