# ==============================================================================
# Stage 1: Builder
# Download and prepare FastQC
# ==============================================================================
FROM alpine:3.19 AS builder

# Define build arguments for versioning
ARG FASTQC_VERSION="0.12.1"

# Install build dependencies
RUN apk add --no-cache curl unzip

# Set working directory
WORKDIR /app

# Download and extract FastQC
RUN curl -fSL "https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v${FASTQC_VERSION}.zip" -o "fastqc.zip" && \
    unzip "fastqc.zip" -d /opt/ && \
    rm "fastqc.zip"

# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM alpine:3.19

# Re-declare ARG for use in this stage
ARG FASTQC_VERSION="0.12.1"

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Install runtime dependencies
RUN apk add --no-cache openjdk11-jre-headless tini bash perl

# Copy FastQC from builder stage
COPY --from=builder /opt/FastQC /opt/FastQC

# Make FastQC executable and create symlink
RUN chmod 755 /opt/FastQC/fastqc && \
    ln -s /opt/FastQC/fastqc /usr/local/bin/fastqc

# Set working directory
WORKDIR /data