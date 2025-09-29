# ==============================================================================
# Stage 1: Builder
# - Compiles STAR and STAR-Fusion from source.
# - Installs all build-time dependencies, including build-essential, Perl modules,
#   and R packages. The artifacts from this stage will be copied to the final image.
# ==============================================================================
FROM debian:bullseye AS builder

LABEL author="Boman Ng <boman.ngs@gmail.com>"
LABEL stage="builder"

# Declare build arguments for versioning
ARG STAR_FUSION_VERSION=1.15.1
ARG STAR_VERSION=2.7.8a

# Set environment variables from ARGs for use in RUN commands
ENV STAR_FUSION_VERSION=${STAR_FUSION_VERSION}
ENV STAR_VERSION=${STAR_VERSION}

# Install all necessary build-time dependencies
RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    ca-certificates \
    cpanminus \
    git \
    gzip \
    libcurl4-openssl-dev \
    libdb-dev \
    libssl-dev \
    libxml2-dev \
    perl \
    r-base-core \
    r-cran-optparse \
    r-cran-ranger \
    r-cran-tidyverse \
    tar \
    wget \
    zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Install required Perl modules into a local directory to be copied later
RUN cpanm -L /usr/local/perl5 --notest \
    DB_File \
    URI::Escape \
    Set::IntervalTree \
    Carp::Assert \
    JSON::XS \
    PerlIO::gzip

# Download and build STAR aligner
WORKDIR /usr/local/src
RUN wget https://github.com/alexdobin/STAR/archive/refs/tags/${STAR_VERSION}.tar.gz && \
    tar -xzf ${STAR_VERSION}.tar.gz && \
    cd STAR-${STAR_VERSION}/source && \
    make STAR && \
    rm -rf /usr/local/src/${STAR_VERSION}.tar.gz

# Download and build STAR-Fusion
RUN wget https://github.com/STAR-Fusion/STAR-Fusion/releases/download/STAR-Fusion-v${STAR_FUSION_VERSION}/STAR-Fusion-v${STAR_FUSION_VERSION}.FULL.tar.gz && \
    tar -xzf STAR-Fusion-v${STAR_FUSION_VERSION}.FULL.tar.gz && \
    cd STAR-Fusion-v${STAR_FUSION_VERSION} && \
    make && \
    rm -rf /usr/local/src/STAR-Fusion-v${STAR_FUSION_VERSION}.FULL.tar.gz


# ==============================================================================
# Stage 2: Final Runtime Image
# - Creates a slim final image containing only the compiled binaries and the
#   essential runtime dependencies.
# ==============================================================================
FROM debian:bullseye-slim

LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Re-declare ARGs for use in COPY commands
ARG STAR_FUSION_VERSION=1.15.1
ARG STAR_VERSION=2.7.8a

# Install only essential runtime dependencies.
# Note: Replaces the large 'tidyverse' meta-package with its essential components
# to significantly reduce the final image size.
RUN apt-get update && apt-get install -y --no-install-recommends \
    perl \
    python3 \
    samtools \
    && rm -rf /var/lib/apt/lists/*

# Copy compiled artifacts and dependencies from the builder stage
COPY --from=builder /usr/local/src/STAR-Fusion-v${STAR_FUSION_VERSION}/ /usr/local/bin/STAR-Fusion/
COPY --from=builder /usr/local/src/STAR-${STAR_VERSION}/bin/Linux_x86_64_static/STAR /usr/local/bin/
COPY --from=builder /usr/local/perl5/ /usr/local/perl5/

# Configure environment for the application and its Perl dependencies
ENV PATH="/usr/local/bin/STAR-Fusion:${PATH}"
ENV PERL5LIB="/usr/local/perl5/lib/perl5"

# Set a default working directory for user data
WORKDIR /data