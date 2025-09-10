# ---- Stage 1: The Builder ----
FROM python:3.12-slim-bookworm AS builder

WORKDIR /app

RUN pip install --no-cache-dir uv

COPY pyproject.toml uv.lock ./

RUN uv venv && \
    . .venv/bin/activate && \
    uv sync --no-cache


# ---- Stage 2: The Final Image ----
FROM python:3.12-slim-bookworm

WORKDIR /app

COPY --from=builder /app/.venv ./.venv

ENV PATH="/app/.venv/bin:$PATH"
ENV PYTHONPATH="/app/.venv/lib/python3.12/site-packages"

COPY . .