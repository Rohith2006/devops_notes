# 📘 Notes: Creating Docker Images

## 🧠 What Is a Docker Image?

A **Docker image** is a *read-only, layered filesystem* that acts as a **blueprint** for creating containers.
It contains:

* Base OS layer
* Runtime
* Code
* Libraries
* Configuration

📌 When you run an image using `docker run`, Docker creates a **container** with a **writable layer** on top.

---

# 🧾 Dockerfile — The Recipe for Building Images

A **Dockerfile** contains instructions that define how your image is built.

### ✔️ Minimal Example

```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

---

# 🧩 Key Dockerfile Instructions

## 1. 🧱 FROM — Base Image

Defines the starting point for the image.

**Syntax:**

```
FROM <image>:<tag>
```

Examples:

```
FROM ubuntu:22.04
FROM node:20-alpine
FROM python:3.12-slim
```

**Best Practices**

* Use lightweight images (`-slim`, `-alpine`)
* Always pin versions
* Use `scratch` for minimal Go/Rust images

---

## 2. 🛠️ RUN — Execute Commands During Build

Used to install packages, modify filesystem, or configure environment.

Examples:

```dockerfile
RUN apt-get update && apt-get install -y curl
RUN pip install flask
```

**Best Practices**

* Combine commands to reduce layers
* Clean caches to reduce image size
* Prefer multi-stage builds for production images

---

## 3. ▶️ CMD — Default Command for Container

Defines the **default command** when container starts.

Example:

```
CMD ["python", "app.py"]
```

**Notes**

* Only one CMD works (last one wins)
* Use JSON exec form for accuracy

---

## 4. 🎯 ENTRYPOINT — Fixed Main Command

Defines the main application that always runs.

Example:

```
ENTRYPOINT ["python", "app.py"]
```

You can still pass extra arguments:

```
docker run myapp arg1  → python app.py arg1
```

**Best Practice**

* Use **ENTRYPOINT** for main app
* Use **CMD** for default arguments

---

## 5. 🔗 ENTRYPOINT + CMD Together

Example:

```dockerfile
ENTRYPOINT ["echo"]
CMD ["Hello, World!"]
```

Output:

```
docker run myimage  → Hello, World!
docker run myimage Bye → Bye
```

---

## 6. 📥 COPY vs ADD

### COPY (Recommended)

Copies files from host → image.

```
COPY . /app
COPY requirements.txt /app/
```

💡 Always prefer COPY unless ADD features are needed.

### ADD

Supports:

* Extracting `.tar.gz` files
* Downloading from remote URLs

```
ADD https://example.com/file.tar.gz /tmp/
```

---

## 7. 🌍 ENV — Environment Variables

```
ENV APP_ENV=production
ENV PORT=8080
```

Use ENV for stable config.
Use `docker run -e KEY=value` for dynamic settings.

---

# 🏗️ Creating a Real-World Docker Image

### Project Structure

```
flask-app/
├── app.py
├── requirements.txt
└── Dockerfile
```

### Dockerfile (Production Ready)

```dockerfile
FROM python:3.12-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

ENV PORT=8080
EXPOSE 8080

ENTRYPOINT ["python"]
CMD ["app.py"]
```

### Build & Run

```bash
docker build -t flask-app .
docker run -p 8080:8080 flask-app
```

App runs at → [http://localhost:8080](http://localhost:8080)

---

# 🧠 Docker Image Best Practices

| Category           | Best Practice                | Why                     |
| ------------------ | ---------------------------- | ----------------------- |
| Base Image         | Use slim/alpine              | Reduce size             |
| Layers             | Combine RUN commands         | Fewer layers            |
| Caching            | Copy requirements first      | Faster rebuilds         |
| Cleanup            | Remove cache files           | Prevents bloat          |
| Security           | Use non-root user            | Reduces attack surface  |
| Vulnerability Scan | Use `docker scan` or Trivy   | Detect CVEs             |
| Secrets            | Never hardcode secrets       | Use env/secret manager  |
| Versioning         | Pin versions                 | Reproducible builds     |
| Multi-stage builds | Use separate build & runtime | Smaller images          |
| Health             | Add `HEALTHCHECK`            | Self-healing containers |

---

# 🛡️ Example — Secure & Optimized Image

```dockerfile
FROM python:3.12-slim
WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt && \
    useradd -m appuser

COPY . .

USER appuser
EXPOSE 8080

ENTRYPOINT ["python"]
CMD ["app.py"]
```

✔ Runs as non-root
✔ Lightweight
✔ Proper caching

---

# 🧪 Inspecting Your Docker Image

### View Layers

```bash
docker history flask-app
```

### Inspect Metadata

```bash
docker inspect flask-app
```

### Vulnerability Scan

```bash
docker scan flask-app
# OR
trivy image flask-app
```

---

# 🏁 Final Summary

* Docker images use a **layered, read-only filesystem**
* Dockerfile defines how images are built
* Key instructions: FROM, RUN, COPY, CMD, ENTRYPOINT, ENV
* Use lightweight, secure, reproducible build practices
* Tools like `docker history`, `inspect`, and Trivy help analyze images