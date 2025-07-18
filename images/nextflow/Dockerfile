ARG JDK_VERSION=17
ARG NEXTFLOW_VERSION=25.04.6

# ==============================================================================
# Stage 1: Build Stage
# ==============================================================================
FROM eclipse-temurin:${JDK_VERSION}-jdk-focal AS builder

SHELL ["/bin/bash", "-c"]

ENV NXF_VER=${NEXTFLOW_VERSION}
ENV CAPSULE_LOG=none

WORKDIR /app
RUN curl -s https://get.nextflow.io | bash && \
    chmod +x nextflow

RUN useradd -m -s /bin/bash builduser
USER builduser
WORKDIR /home/builduser

RUN /app/nextflow plugin install nf-amazon && \
    /app/nextflow plugin install nf-k8s

# ==============================================================================
# Stage 2: Runtime Stage
# ==============================================================================
FROM eclipse-temurin:${JDK_VERSION}-jre-alpine

LABEL maintainer="BomanNg boman.ngs@gmail.com" \
      version=${NEXTFLOW_VERSION} \

RUN apk add --no-cache bash && \
    rm -rf /var/cache/apk/*

ARG USER_ID=1000
ARG GROUP_ID=1000
RUN addgroup -g ${GROUP_ID} nextflow && \
    adduser -u ${USER_ID} -G nextflow -s /bin/bash -D nextflow

COPY --from=builder /app/nextflow /usr/local/bin/nextflow

RUN mkdir -p /home/nextflow/.nextflow && \
    chown -R nextflow:nextflow /home/nextflow

COPY --from=builder --chown=nextflow:nextflow /home/builduser/.nextflow /home/nextflow/.nextflow

USER nextflow

VOLUME ["/home/nextflow/.nextflow", "/tmp"]

WORKDIR /workspace

ENV NXF_HOME=/home/nextflow/.nextflow \
    NXF_TEMP=/tmp

ENTRYPOINT ["nextflow"]
CMD ["info"]