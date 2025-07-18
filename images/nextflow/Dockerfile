ARG JDK_VERSION=17

FROM eclipse-temurin:${JDK_VERSION}-jdk-focal AS builder

ARG NEXTFLOW_VERSION=25.04.6
ENV NXF_VER=${NEXTFLOW_VERSION}
ENV CAPSULE_LOG=none

WORKDIR /app
RUN curl -s https://get.nextflow.io | bash && \
    chmod +x nextflow

FROM eclipse-temurin:${JDK_VERSION}-jre-alpine
LABEL maintainer="Your Name <your.email@example.com>"

RUN apk add --no-cache bash && \
    rm -rf /var/cache/apk/*

ARG USER_ID=1000
ARG GROUP_ID=1000
RUN addgroup -g ${GROUP_ID} nextflow && \
    adduser -u ${USER_ID} -G nextflow -s /bin/bash -D nextflow

COPY --from=builder /app/nextflow /usr/local/bin/nextflow

USER nextflow

ENV NXF_HOME=/home/nextflow/.nextflow

WORKDIR /workspace

ENTRYPOINT ["nextflow"]

CMD ["info"]