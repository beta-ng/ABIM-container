# ==============================================================================
# Stage 1: Builder
# Download and prepare Nextflow
# ==============================================================================
FROM eclipse-temurin:17-jdk-focal AS builder

# Define build arguments for versioning
ARG JDK_VERSION=17
ARG NEXTFLOW_VERSION=25.04.6

# Set environment variables
ENV NXF_VER=${NEXTFLOW_VERSION}
ENV CAPSULE_LOG=none

# Set working directory
WORKDIR /app

# Download Nextflow
RUN curl -s https://get.nextflow.io | bash && \
    chmod +x nextflow

# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM eclipse-temurin:17-jre-alpine

# Re-declare ARG for use in this stage
ARG JDK_VERSION=17
ARG NEXTFLOW_VERSION=25.04.6

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Install runtime dependencies
RUN apk add --no-cache bash && \
    rm -rf /var/cache/apk/*

# Copy Nextflow from builder stage
COPY --from=builder --chmod=755 /app/nextflow /usr/local/bin/nextflow

# Set environment variables
ENV NXF_HOME=/root/.nextflow

# Set working directory
WORKDIR /data