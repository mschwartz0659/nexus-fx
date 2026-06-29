FROM python:3.13-slim AS builder

WORKDIR /build

COPY requirements.txt .
RUN python -m venv /build/.venv && \
    /build/.venv/bin/pip install --no-cache-dir -r requirements.txt

COPY app/ app/

FROM python:3.13-slim AS runtime

RUN apt-get update && apt-get install -y --no-install-recommends curl && \
    rm -rf /var/lib/apt/lists/* && \
    groupadd --system appuser && useradd --system --gid appuser appuser

WORKDIR /app

COPY --from=builder /build/.venv /app/.venv
COPY --from=builder /build/app ./app

ENV PATH="/app/.venv/bin:$PATH"

USER appuser

EXPOSE 8001

HEALTHCHECK --interval=30s --timeout=5s --retries=3 \
    CMD curl -f http://localhost:8001/health || exit 1

CMD ["python", "-m", "uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "8001"]