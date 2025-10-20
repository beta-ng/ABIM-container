# Use the slim image, as it's the smallest official image with /bin/bash
FROM python:3.12-slim

# Per your requirement, we will run as the default 'root' user.

# Set the 'uv' mirror URL to Tsinghua (as requested).
ENV UV_INDEX_URL="https://pypi.tuna.tsinghua.edu.cn/simple"

# Install 'uv' and immediately clean the pip cache in the same layer.
# This is the primary optimization for size reduction.
RUN pip install uv \
    && rm -rf /root/.cache/pip

# Set the working directory
WORKDIR /app