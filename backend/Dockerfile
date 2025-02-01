FROM tiangolo/uvicorn-gunicorn-fastapi:python3.10-slim-2022-11-25

ENV PYTHONUNBUFFERED=1
WORKDIR /code

# Install dependencies (including curl)
RUN apt clean && apt update && apt install -y curl

# Install Poetry correctly
RUN curl -sSL https://install.python-poetry.org | python3 -
ENV PATH="/root/.local/bin:$PATH"

# Copy pyproject.toml and poetry.lock
COPY app/pyproject.toml app/poetry.lock* /code/

# Install dependencies (ensure Alembic is installed)
RUN poetry install --no-root --no-dev

# Set Python Path
ENV PYTHONPATH=/code

# Expose port
EXPOSE 9090
