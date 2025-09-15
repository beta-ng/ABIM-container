# ==============================================================================
# Stage 1: Builder
# - Installs dependencies into a virtual environment using 'uv'.
# - This stage will be discarded, and only the created .venv is copied over.
# ==============================================================================
FROM python:3.12-slim-bookworm AS builder

LABEL author="Boman Ng <boman.ngs@gmail.com>"
LABEL stage="builder"

WORKDIR /app

# Install 'uv', a high-performance Python package manager
RUN pip install --no-cache-dir uv

# Copy only dependency definition files first to leverage Docker's layer caching.
# The next step will only re-run if these files change.
COPY pyproject.toml uv.lock ./

# Create a virtual environment and install dependencies into it.
# Note: 'uv' automatically detects and uses the .venv in the current directory,
# so the '. .venv/bin/activate' step is technically redundant but harmless.
RUN uv venv && \
    . .venv/bin/activate && \
    uv sync --no-cache


# ==============================================================================
# Stage 2: Final Image
# - Creates the final, lightweight production image.
# - Copies the pre-built virtual environment from the builder stage.
# - Copies the application source code.
# ==============================================================================
FROM python:3.12-slim-bookworm

LABEL author="Boman Ng <boman.ngs@gmail.com>"

WORKDIR /app

# Copy the virtual environment with all dependencies from the builder stage
COPY --from=builder /app/.venv ./.venv

# Set environment variables to use the Python interpreter and packages from the venv.
# This makes scripts and packages available directly without needing to 'source' the venv.
ENV PATH="/app/.venv/bin:$PATH"
ENV PYTHONPATH="/app/.venv/lib/python3.12/site-packages"

# Copy the application source code into the final image
COPY . .