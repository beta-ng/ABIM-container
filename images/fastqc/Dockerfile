ARG FASTQC_VERSION="0.12.1"

# ==============================================================================
# Stage 1: Build Stage
# ==============================================================================
FROM alpine:3.19 AS builder

ARG FASTQC_VERSION

RUN apk add --no-cache curl unzip

WORKDIR /app

RUN curl -fSL "https://www.bioinformatics.babraham.ac.uk/projects/fastqc/fastqc_v${FASTQC_VERSION}.zip" -o "fastqc.zip" && \
    unzip "fastqc.zip" -d /opt/ && \
    rm "fastqc.zip"

# ==============================================================================
# Stage 2: Runtime Stage
# ==============================================================================
FROM alpine:3.19

ARG FASTQC_VERSION

LABEL maintainer="BomanNg boman.ngs@gmail.com" \
      version="0.12.1" \
      description="A secure, minimal, and production-ready Docker image for FastQC."

# --- STEP 1: Perform all setup as ROOT ---
RUN apk add --no-cache openjdk11-jre-headless tini bash perl

ARG USER_ID=1001
ARG GROUP_ID=1001
RUN addgroup -S -g ${GROUP_ID} fastqc && \
    adduser -S -u ${USER_ID} -G fastqc -D fastqc

COPY --from=builder /opt/FastQC /opt/FastQC

RUN chmod 755 /opt/FastQC/fastqc && \
    ln -s /opt/FastQC/fastqc /usr/local/bin/fastqc && \
    chown -R fastqc:fastqc /opt/FastQC

RUN mkdir -p /data && \
    chown -R fastqc:fastqc /data

USER fastqc

WORKDIR /data

ENTRYPOINT ["/sbin/tini", "--", "fastqc"]

CMD ["--help"]