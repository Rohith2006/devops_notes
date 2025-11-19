# 📘 Notes: Introduction to Docker

## 🐳 What Is Docker?

Docker is an **open-source platform** used to **build, package, ship, and run applications in containers**.

A **container** includes everything needed to run an application:

* Code
* Runtime
* Libraries
* System tools
* Configuration

📌 **Key Idea:**
Containers ensure your app runs **the same everywhere** — laptop, server, or cloud.

---

## ❓ Why Do We Need Docker?

### 🛑 Before Docker (Traditional Deployment)

* Apps installed directly on the OS
* Conflicts due to different library versions
* Manual setup on every system
* No isolation between apps

### ✅ With Docker

* Each app runs in its **own isolated container**
* No dependency conflicts
* Same environment on all machines
* Easy to start/stop/copy/remove containers

| Traditional Deployment         | Docker Deployment                    |
| ------------------------------ | ------------------------------------ |
| Manual environment setup       | Environment bundled inside container |
| Conflicts between apps         | Full isolation                       |
| Hard to reproduce environments | Same everywhere                      |
| Slow deployments               | Instant launches                     |

---

# 🧩 Key Docker Components

| Concept            | Description                                          |
| ------------------ | ---------------------------------------------------- |
| **Image**          | Read-only blueprint used to create containers        |
| **Container**      | Running instance of an image                         |
| **Dockerfile**     | Instructions to build an image                       |
| **Docker Engine**  | Runtime that builds & runs containers                |
| **Docker Hub**     | Public registry for images                           |
| **Docker Compose** | Define multi-container apps via `docker-compose.yml` |

---

# 🏗️ Docker Architecture

```
+-----------------------------+
|      Docker CLI (Client)    |
+-----------------------------+
              ↓
+-----------------------------+
|   Docker Daemon (Server)    |
|  Builds & manages containers |
+-----------------------------+
              ↓
+-----------------------------+
|  Images + Containers Layer   |
+-----------------------------+
              ↓
+-----------------------------+
|     Host Operating System    |
+-----------------------------+
```

📌 **Client-Server Model:**
`docker` CLI → talks to Docker Daemon → daemon does heavy work.

---

# 🧰 Installing Docker

### 🚀 Convenience Script (Recommended)

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
```

### 🐧 Ubuntu (Standard Method)

```bash
sudo apt update
sudo apt install docker.io -y
sudo systemctl enable docker
sudo systemctl start docker
docker --version
```

Test:

```bash
docker run hello-world
```

---

# 🔥 Basic Docker Commands

| Task                    | Command                      | Description              |
| ----------------------- | ---------------------------- | ------------------------ |
| Check version           | `docker --version`           | Verify Docker            |
| Run a container         | `docker run hello-world`     | Test setup               |
| List running containers | `docker ps`                  | Active containers        |
| List all containers     | `docker ps -a`               | Includes stopped         |
| List images             | `docker images`              | Installed images         |
| Pull image              | `docker pull nginx`          | Download from Docker Hub |
| Shell inside container  | `docker run -it ubuntu bash` | Interactive mode         |
| Stop container          | `docker stop <id>`           | Graceful stop            |
| Remove container        | `docker rm <id>`             | Delete                   |
| Remove image            | `docker rmi <id>`            | Delete image             |

---

# 📦 Docker Images — Detailed View

## 🧠 What is a Docker Image?

A **read-only, layered template** used to create Docker containers.

Contains:

* OS base layer
* App code
* Dependencies
* Config

---

# 🏗️ Layered File System (UnionFS)

Every `RUN`, `COPY`, `ADD` instruction in a Dockerfile creates a **new layer**.

Example Dockerfile:

```
FROM ubuntu:22.04
RUN apt-get install nginx -y
COPY . /var/www/html
CMD ["nginx", "-g", "daemon off;"]
```

### 🧱 Image Layers (Read-Only)

```
+------------------------------------+
| CMD / ENTRYPOINT  (metadata)        |
+------------------------------------+
| COPY app files / configs            |
+------------------------------------+
| RUN apt install nginx               |
+------------------------------------+
| FROM ubuntu:22.04  (Base Layer)     |
+------------------------------------+
```

### 🖊️ Container View (Writable Layer)

```
+----------------------------------------+
| Writable Container Layer (diffs)       |
+----------------------------------------+
| Read-only Image Layers                 |
+----------------------------------------+
```

📌 **Modifications inside containers happen ONLY in the writable layer** (copy-on-write).

---

# 🧬 How Union File System Works

* Layers merge into a **single unified filesystem**
* If a file appears in multiple layers → Docker uses the **topmost** version
* When modifying a file:

  * It is first copied from read-only layer → writable layer
  * Modifications happen only in writable layer

---

# 📁 Where Docker Stores Images (Linux)

Default storage driver: **overlay2**

Path:

```
/var/lib/docker/overlay2/
```

Structure:

```
/var/lib/docker/
├── overlay2/
│   ├── <layer_id>/
│   │   ├── diff/      # filesystem changes
│   │   ├── lower/     # references to parent layers
│   │   └── work/
├── image/
│   └── overlay2/
│       ├── imagedb/content/sha256/
│       └── layerdb/
```

---

# 🔍 Inspecting Layers

### List all layers:

```bash
docker history <image>
```

### Inspect with details:

```bash
docker image inspect <image> --format='{{json .RootFS.Layers}}' | jq
```

---

# ⚡ Layer Caching & Reuse

Docker uses **content-addressable storage** (SHA256 digests).

✔️ If two images share a base layer → Docker **reuses** it
✔️ Builds become **faster** and **storage-efficient**

---

# 🏁 Conclusion

* Docker packages apps into **portable, consistent containers**
* Images use **layered architecture** for efficiency
* Docker daemon manages builds, pulls, and containers
* UnionFS provides copy-on-write and fast image reuse
* Docker simplifies deployment, scaling, and DevOps workflows


