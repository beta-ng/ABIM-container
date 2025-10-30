# ==============================================================================
# Stage 1: Builder
# Build GATK environment using Miniconda
# ==============================================================================
FROM docker.io/continuumio/miniconda3:24.11.1-0 AS builder

# Define build arguments for versioning
ARG GATK_VERSION="4.6.2.0"
ARG CONDA_ENV_PATH="/opt/gatk-env"

# Create a dedicated environment for GATK and clean caches in the same layer
RUN echo "Creating GATK4 ${GATK_VERSION} environment at ${CONDA_ENV_PATH}..." && \
    conda create -p ${CONDA_ENV_PATH} -c bioconda -c conda-forge "gatk4=${GATK_VERSION}" -y && \
    conda clean -afy

# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM docker.io/library/debian:bullseye-slim

# Re-declare ARGs for use in this stage
ARG CONDA_ENV_PATH="/opt/gatk-env"

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Set environment variables
ENV PATH="${CONDA_ENV_PATH}/bin:${PATH}"

# Copy the built conda environment from the builder stage
COPY --from=builder ${CONDA_ENV_PATH} ${CONDA_ENV_PATH}

# Set working directory
WORKDIR /data