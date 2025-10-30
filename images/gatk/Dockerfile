# ----- Stage 1: The Builder -----
# Using an explicit docker.io image (Docker Hub) as requested.
FROM docker.io/continuumio/miniconda3:24.11.1-0 AS builder

# 1. Define Arguments
# Updated GATK_VERSION to 4.6.2.0 as requested.
ARG GATK_VERSION="4.6.2.0"
ARG CONDA_ENV_PATH="/opt/gatk-env"

# 2. Build the Environment
# Create a dedicated environment for GATK and clean caches in the same layer.
RUN echo "Creating GATK4 ${GATK_VERSION} environment at ${CONDA_ENV_PATH}..." && \
    conda create -p ${CONDA_ENV_PATH} -c bioconda -c conda-forge "gatk4=${GATK_VERSION}" -y && \
    conda clean -afy

# ----- Stage 2: The Final Production Image -----
# Using an explicit docker.io image (Docker Hub) as requested.
FROM docker.io/library/debian:bullseye-slim

# 1. Define Environment Variables
ARG CONDA_ENV_PATH="/opt/gatk-env"
ENV PATH="${CONDA_ENV_PATH}/bin:${PATH}"

# 2. Copy Only the Essential Artifacts
# Copy the built conda environment from the builder stage.
COPY --from=builder ${CONDA_ENV_PATH} ${CONDA_ENV_PATH}

# 3. Set Workdir
# Per user request, the container will run as root.
# Setting WORKDIR to /root (root's home directory).
WORKDIR /data