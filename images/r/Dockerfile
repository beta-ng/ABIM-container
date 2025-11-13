# ==============================================================================
# R Base Environment
# Provides R kernel and common Bioconductor/CRAN libraries
# ==============================================================================
FROM debian:bullseye-slim

# Define build arguments for versioning
# Using a generic version tag as we are not pinning a specific R version
ARG R_ENV_VERSION="1.0"

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>" \
      version="${R_ENV_VERSION}" \
      description="R environment with r-base, circlize, GenomicAlignments, and GenomicRanges"

# Install R and required libraries
# Using noninteractive frontend and cleaning up in one layer
RUN export DEBIAN_FRONTEND=noninteractive && \
    apt-get update && \
    apt-get install -y --no-install-recommends \
        bash \
        r-base \
        r-cran-circlize \
        r-bioc-genomicalignments \
        r-bioc-genomicranges \
        libxml2 \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# Set working directory as per project conventions
WORKDIR /data