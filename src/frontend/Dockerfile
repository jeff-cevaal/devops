FROM python:3.13-alpine AS builder

# Install uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/

# Change working directory
WORKDIR /app

# Install dependancies in a cache layer to speed up build time.
RUN --mount=type=cache,target=/root/.cache/uv \
  --mount=type=bind,source=uv.lock,target=uv.lock \
  --mount=type=bind,source=pyproject.toml,target=pyproject.toml \
  uv sync --locked --no-install-project --no-editable

# Copy project
COPY . /app

# Sync the project and install it
RUN --mount=type=cache,target=/root/.cache/uv \
  uv sync --locked --no-editable --no-dev

FROM python:3.13-alpine

# Create a non-root user and group
RUN addgroup -S -g 1000 app && adduser -S -u 1000 -G app app

# Copy the environment but not the source code
COPY --from=builder --chown=app:app /app/.venv /app/.venv

# Switch to non-root user
USER app

# Expose the correct port
EXPOSE 22111

# Run application
CMD [ "/app/.venv/bin/study-tracker-frontend" ]