ARG JDK_VERSION=17
ARG NEXTFLOW_VERSION=24.10.8

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

# ==============================================================================
# Stage 2: Final Runtime Stage
# ==============================================================================
FROM eclipse-temurin:${JDK_VERSION}-jre-alpine

LABEL maintainer="BomanNg boman.ngs@gmail.com" \
      version="24.10.8" \
      description="A secure, minimal, and production-ready Docker image for Nextflow."

# --- STEP 1: Perform all setup as ROOT ---
RUN apk add --no-cache bash

ARG USER_ID=1000
ARG GROUP_ID=1000
RUN addgroup -g ${GROUP_ID} nextflow && \
    adduser -u ${USER_ID} -G nextflow -s /bin/bash -D nextflow

COPY --from=builder /app/nextflow /usr/local/bin/nextflow
RUN chmod 755 /usr/local/bin/nextflow

RUN mkdir -p /home/nextflow/.nextflow && \
    chown -R nextflow:nextflow /home/nextflow

# --- STEP 2: Drop privileges. This is now the final privileged operation. ---
USER nextflow

# --- STEP 3: Define runtime environment as the non-root user ---
VOLUME /home/nextflow/.nextflow

WORKDIR /workspace

ENTRYPOINT ["nextflow"]
CMD ["info"]