ARG JDK_VERSION=17
ARG NEXTFLOW_VERSION=25.04.6

# ==============================================================================
# Stage 1: Build Stage
# ==============================================================================
FROM eclipse-temurin:${JDK_VERSION}-jdk-focal AS builder

SHELL ["/bin/bash", "-c"]

WORKDIR /app

RUN export NXF_VER=${NEXTFLOW_VERSION} && \
    export CAPSULE_LOG=none && \
    curl -s https://get.nextflow.io | bash && \
    chmod +x nextflow

RUN useradd -m -s /bin/bash builduser && \
    chown builduser:builduser /app/nextflow
USER builduser
WORKDIR /home/builduser

RUN /app/nextflow plugin install nf-amazon && \
    /app/nextflow plugin install nf-k8s && \
    /app/nextflow info

# ==============================================================================
# Stage 2: Runtime Stage (using distroless-like approach)
# ==============================================================================
FROM eclipse-temurin:${JDK_VERSION}-jre-alpine

LABEL maintainer="BomanNg boman.ngs@gmail.com" \
      version="25.04.6" \
      description="A secure, minimal, and production-ready Docker image for Nextflow with nf-k8s and nf-amazon plugins."

RUN apk add --no-cache bash && \
    rm -rf /var/cache/apk/*

ARG USER_ID=1000
ARG GROUP_ID=1000
RUN addgroup -g ${GROUP_ID} nextflow && \
    adduser -u ${USER_ID} -G nextflow -s /bin/bash -D nextflow

COPY --from=builder /app/nextflow /usr/local/bin/nextflow
RUN chmod 755 /usr/local/bin/nextflow

RUN mkdir -p /home/nextflow/.nextflow/plugins && \
    chown -R nextflow:nextflow /home/nextflow

COPY --from=builder --chown=nextflow:nextflow /home/builduser/.nextflow/plugins /home/nextflow/.nextflow/plugins
COPY --from=builder --chown=nextflow:nextflow /home/builduser/.nextflow/framework /home/nextflow/.nextflow/framework

USER nextflow

VOLUME ["/home/nextflow/.nextflow", "/tmp"]

WORKDIR /workspace

ENV NXF_HOME=/home/nextflow/.nextflow \
    NXF_TEMP=/tmp \
    NXF_WORK=/tmp/work \
    CAPSULE_CACHE_DIR=/tmp/capsule

ENTRYPOINT ["nextflow"]
CMD ["info"]
