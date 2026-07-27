# FROM python:3.6-slim

# WORKDIR /app

# COPY requirements.txt .
# RUN pip install --no-cache-dir -r requirements.txt

# COPY app.py .

# EXPOSE 8080

# CMD ["python", "app.py"]


# ---- Build stage: unchanged from original, pins app deps exactly as before ----
FROM python:3.6-slim AS builder

WORKDIR /app
COPY requirements.txt .
# --target isolates installed packages so we can copy just the site-packages
# tree into the distroless runtime image below.
RUN pip install --no-cache-dir --target=/deps -r requirements.txt

# ---- Runtime stage: distroless, non-root, no shell, no package manager ----
FROM gcr.io/distroless/python3-debian10:nonroot

WORKDIR /app

# Bring in only the installed dependencies and app source — nothing else.
COPY --from=builder /deps /deps
COPY app.py .

ENV PYTHONPATH=/deps
EXPOSE 8080

# distroless python3 image's ENTRYPOINT is already ["python3"];
# CMD supplies the script, matching the original "python app.py" behavior.
CMD ["app.py"]