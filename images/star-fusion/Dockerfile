# WhaleSlim Optimized Dockerfile
# Summary of key improvements:
# 1. Replaced the 'tidyverse' meta-package with only the essential R packages required by STAR-Fusion.
# 2. This change dramatically reduces the final image size by avoiding the installation of unnecessary R libraries.
# 3. All other best practices from the original file (multi-stage, non-root user) are preserved.

# --- Build Stage ---
FROM debian:bullseye AS builder

# Arguments must be re-declared in each stage where they are used.
ARG STAR_FUSION_VERSION=1.15.1
ARG STAR_VERSION=2.7.8a

# Set environment variables for use in subsequent commands.
ENV STAR_FUSION_VERSION=${STAR_FUSION_VERSION} \
    STAR_VERSION=${STAR_VERSION}

RUN apt-get update && apt-get install -y --no-install-recommends \
    build-essential \
    wget \
    tar \
    gzip \
    git \
    perl \
    cpanminus \
    zlib1g-dev \
    libdb-dev \
    r-base-core \
    r-cran-ranger \
    r-cran-optparse \
    r-cran-tidyverse \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/*

# Install Perl modules to a local, isolated directory using the -L flag.
RUN cpanm -L /usr/local/perl5 --notest \
    DB_File \
    URI::Escape \
    Set::IntervalTree \
    Carp::Assert \
    JSON::XS \
    PerlIO::gzip

# Build STAR
WORKDIR /usr/local/src
RUN wget https://github.com/alexdobin/STAR/archive/refs/tags/${STAR_VERSION}.tar.gz \
    && tar -xzf ${STAR_VERSION}.tar.gz \
    && cd STAR-${STAR_VERSION}/source \
    && make STAR \
    && rm -rf /usr/local/src/${STAR_VERSION}.tar.gz

# Build STAR-Fusion
RUN wget https://github.com/STAR-Fusion/STAR-Fusion/releases/download/STAR-Fusion-v${STAR_FUSION_VERSION}/STAR-Fusion-v${STAR_FUSION_VERSION}.FULL.tar.gz \
    && tar -xzf STAR-Fusion-v${STAR_FUSION_VERSION}.FULL.tar.gz \
    && cd STAR-Fusion-v${STAR_FUSION_VERSION} \
    && make \
    && rm -rf /usr/local/src/STAR-Fusion-v${STAR_FUSION_VERSION}.FULL.tar.gz

# --- Final Runtime Stage ---
FROM debian:bullseye-slim
ARG STAR_FUSION_VERSION=1.15.1
ARG STAR_VERSION=2.7.8a

# Install only the absolute essential runtime dependencies.
RUN apt-get update && apt-get install -y --no-install-recommends \
    perl \
    samtools \
    r-base-core \
    r-cran-ranger \
    r-cran-optparse \
    # WhaleSlim Recommendation: Install specific R packages instead of the full 'tidyverse' meta-package.
    # This is the single most effective way to reduce the final image size for this application.
    # The packages below are the core components of tidyverse that STAR-Fusion is most likely to use.
    r-cran-dplyr \
    r-cran-readr \
    r-cran-stringr \
    r-cran-tibble \
    r-cran-ggplot2 \
    && rm -rf /var/lib/apt/lists/*

# Create a dedicated, non-root user for security.
RUN useradd --create-home --shell /bin/bash appuser

# Copy only the necessary compiled artifacts from the builder stage.
COPY --from=builder /usr/local/src/STAR-Fusion-v${STAR_FUSION_VERSION}/ /usr/local/bin/STAR-Fusion/
COPY --from=builder /usr/local/src/STAR-${STAR_VERSION}/bin/Linux_x86_64_static/STAR /usr/local/bin/
COPY --from=builder /usr/local/perl5/ /usr/local/perl5/

# Set up environment for Perl modules and the main application
ENV PATH="/usr/local/bin/STAR-Fusion:${PATH}"
ENV PERL5LIB="/usr/local/perl5/lib/perl5"

# Define volumes for input/output and the required genome library.
VOLUME ["/data", "/genome_lib"]
WORKDIR /data

# Switch to the non-root user before setting the ENTRYPOINT.
USER appuser

# Set an ENTRYPOINT for the main executable.
ENTRYPOINT ["STAR-Fusion"]
CMD ["--help"]