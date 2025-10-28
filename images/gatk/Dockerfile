# GATK 4.6.2.0 Dockerfile - 多阶段构建优化版
# 构建阶段
FROM ubuntu:22.04 AS builder

# 设置环境变量
ENV GATK_VERSION=4.6.2.0
ENV DEBIAN_FRONTEND=noninteractive

# 安装构建依赖（合并为单层）
RUN apt-get update && apt-get install -y --no-install-recommends \
    wget \
    ca-certificates \
    openjdk-17-jdk \
    git \
    unzip \
    && rm -rf /var/lib/apt/lists/*

# 设置工作目录
WORKDIR /build

# 下载GATK源码
RUN wget -q https://github.com/broadinstitute/gatk/archive/refs/tags/${GATK_VERSION}.tar.gz \
    && tar -xzf ${GATK_VERSION}.tar.gz \
    && rm ${GATK_VERSION}.tar.gz

# 构建GATK
WORKDIR /build/gatk-${GATK_VERSION}
RUN ./gradlew bundle \
    && unzip build/gatk-${GATK_VERSION}.zip -d /opt/

# 运行阶段 - 最小化镜像
FROM ubuntu:22.04 AS runtime

# 设置环境变量
ENV GATK_VERSION=4.6.2.0
ENV GATK_HOME=/opt/gatk-${GATK_VERSION}
ENV PATH="${GATK_HOME}:${PATH}"
ENV DEBIAN_FRONTEND=noninteractive

# 仅安装运行时依赖
RUN apt-get update && apt-get install -y --no-install-recommends \
    openjdk-17-jre-headless \
    python3 \
    python3-pip \
    r-base-core \
    ca-certificates \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

# 从构建阶段复制GATK
COPY --from=builder /opt/gatk-${GATK_VERSION} ${GATK_HOME}

# 创建数据目录
WORKDIR /data

# 验证安装
RUN gatk --list

# 添加标签
LABEL maintainer="GATK Docker" \
      version="${GATK_VERSION}" \
      description="GATK (Genome Analysis Toolkit) - Optimized Multi-stage Build"

# 默认命令
ENTRYPOINT ["gatk"]
CMD ["--help"]