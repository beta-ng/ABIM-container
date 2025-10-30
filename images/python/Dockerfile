# ==============================================================================
# Python Container with uv
# ==============================================================================
FROM python:3.12-slim

# Define build arguments for versioning
ARG PYTHON_VERSION=3.12

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>"

# Set the uv mirror URL to Tsinghua
ENV UV_INDEX_URL="https://pypi.tuna.tsinghua.edu.cn/simple"

# Install uv and clean the pip cache in the same layer
RUN pip install uv \
    && rm -rf /root/.cache/pip

# Set working directory
WORKDIR /data