# ==============================================================================
# Stage 1: Builder
# Install Python packages using uv
# ==============================================================================
FROM python:3.12-slim AS builder

# Define build arguments for versioning
ARG PYTHON_VERSION=3.12

# Set the uv mirror URL to Tsinghua
ENV UV_INDEX_URL="https://pypi.tuna.tsinghua.edu.cn/simple"

# Install uv and build dependencies
RUN pip install --no-cache-dir uv && \
    rm -rf /root/.cache/pip

# Set working directory
WORKDIR /app

# Create a virtual environment and install packages using uv
# Use --no-cache to avoid storing downloaded packages
RUN uv venv /app/venv && \
    uv pip install \
    --no-cache \
    --python=/app/venv/bin/python \
    pandas \
    openpyxl \
    matplotlib && \
    find /app/venv -type d -name "__pycache__" -exec rm -rf {} + 2>/dev/null || true && \
    find /app/venv -type f -name "*.pyc" -delete && \
    find /app/venv -type f -name "*.pyo" -delete && \
    find /app/venv -type d -name "tests" -exec rm -rf {} + 2>/dev/null || true && \
    find /app/venv -type d -name "test" -exec rm -rf {} + 2>/dev/null || true


# ==============================================================================
# Stage 2: Final Runtime Image
# ==============================================================================
FROM python:3.12-slim

# Re-declare ARG for use in this stage
ARG PYTHON_VERSION=3.12

# Add metadata
LABEL author="Boman Ng <boman.ngs@gmail.com>" \
      version="${PYTHON_VERSION}" \
      description="Python environment for data processing and analysis with pandas, openpyxl, and matplotlib"

# Install bash and clean up in one layer
RUN apt-get update && \
    apt-get install -y --no-install-recommends bash && \
    rm -rf /var/lib/apt/lists/*

# Copy the virtual environment from builder stage
COPY --from=builder /app/venv /app/venv

# Set environment PATH to use the virtual environment
ENV PATH="/app/venv/bin:$PATH" \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Set working directory
WORKDIR /data
