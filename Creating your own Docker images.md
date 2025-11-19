# 📘 Notes: Docker Containers — Deep Dive

## 🐳 What Exactly Is a Container?

A **container is NOT**:

* a virtual machine
* a mini operating system
* a separate kernel

A **container IS**:

* a **normal Linux process**
* running with **isolation** using

  * Linux namespaces
  * cgroups
  * filesystem overlays

📌 **Core Definition:**
A container = **Linux process + isolated filesystem + resource limits**.

---

# ⚡ 1. Containers Are Ephemeral

A container consists of:

* Image layers (read-only)
* Writable layer (temporary)
* One main process (PID 1 inside container)

📌 If the **main process exits → container stops → writable layer deleted → container disappears**

Containers are **stateless** and **ephemeral** unless you use:

* volumes
* bind mounts
* external DB/storage

---

# ⚙️ 2. A Container = Just a Process on the Host

Running:

```bash
docker run nginx
```

does *not* create a VM.

It creates a **Linux process**, isolated using namespaces.

### Process Tree

```
Host OS
│
├── systemd
├── sshd
├── containerd
│    └── containerd-shim
│         └── nginx  <-- container main process
```

📌 The "container" is literally just the **nginx process** on the host.

---

# 🧱 3. Container Isolation = Pseudo-Isolation

Namespaces → hide resources
Cgroups → limit CPU/memory/I/O

BUT:

* Containers **share the same host kernel**
* Processes are visible in `ps -ef`
* Root in container ≠ real root on host (user namespace)

📌 **Containers ≠ VMs**
Containers = **process-level isolation**, not full OS isolation.

---

# 🏗️ 4. Key Components: containerd, shim, runc

Modern container lifecycle:

```
docker CLI → containerd → containerd-shim → runc → your process
```

| Component           | Role                                                                          |
| ------------------- | ----------------------------------------------------------------------------- |
| **containerd**      | Manages images, snapshots, networking                                         |
| **containerd-shim** | Parent process for each container; keeps it alive even if containerd restarts |
| **runc**            | Creates namespaces + cgroups; launches the process                            |

Diagram:

```
Docker CLI
    ↓
containerd
    ↓
containerd-shim (one per container)
    ↓
runc
    ↓
Your Process (e.g., nginx)
```

---

# 🌳 5. PID & Process Tree Behavior

Inside container:

* the main process becomes **PID 1**

On host:

* it's just a normal PID like 9823

Example:

```
containerd(233)
  └── containerd-shim(9421)
        └── nginx(9422)
             ├── worker(9423)
             └── worker(9424)
```

📌 PID 1 in a container handles:

* zombie reaping
* container lifecycle

---

# 💀 6. Why Killing PID 1 Stops the Container

Container lifecycle = **lifecycle of the main process**

When PID 1 exits:

* shim detects exit
* containerd marks container stopped
* writable layer removed
* networking cleaned
* container disappears

Example:

```bash
docker kill <id>
```

Stops instantly because it kills PID 1.

---

# 📦 7. Why Containers Lose Data (Ephemeral)

Container filesystem:

```
Image Layers (read-only)
------------------------
Writable Layer (deleted on stop)
------------------------
Running Process
```

Anything stored in:

```
/tmp
/var/log
/app/data
```

disappears after `docker stop`.

Persist data with:

* **volumes**
* **bind mounts**
* **external storage**

---

# 🕶️ 8. Why Containers Feel Like Separate Machines

Linux **namespaces** create the illusion:

| Namespace   | What it isolates                 |
| ----------- | -------------------------------- |
| **PID**     | Process tree                     |
| **Network** | IP, routes, firewall, interfaces |
| **Mount**   | Filesystem root, overlays        |
| **UTS**     | Hostname                         |
| **IPC**     | Shared memory, semaphores        |
| **User**    | UID/GID mapping                  |

Containers *feel* like mini-OSes, but they are just isolated processes.

---

# ⚡ 9. Why Containers Start So Fast

Containers start in **milliseconds** because:

* No kernel boot
* No BIOS
* No virtual hardware
* No init system (unless added)

Start sequence:

```
runc → clone() syscall → process starts
```

Typical startup: ~50ms
VMs: 10–60 seconds

---

# 🪶 10. Why Containers Are Lightweight

They share:

* host kernel
* CPU scheduler
* memory management
* many libraries

Only namespaces, cgroups, and filesystem layers differ.

---

# ❗ 11. Why Containers Fail When Main Process Exits

Containers run **one main process**.

Examples:

| Command               | Behavior        |
| --------------------- | --------------- |
| `nginx` dies          | container dies  |
| `python app.py` exits | container exits |
| `sleep 5`             | stops after 5s  |

If you need multi-process apps:

* use `supervisord`
* or run services separately

📌 Containers are **process-centric**, not machine-centric.

---

# 📝 12. Summary in 10 Bullet Points

1. Containers = **process + isolation**
2. They share the host kernel
3. Isolation via **namespaces**
4. Limits via **cgroups**
5. Started by **runc**, managed by **containerd-shim**
6. PID 1 inside container is special
7. If PID 1 dies → container stops
8. Writable layer is temporary → **ephemeral**
9. Isolation ≠ VM-level → “pseudo-isolation”
10. All container processes visible via `ps -ef` on host

---

# 🧭 Bonus: How Docker Creates a Container (Step-by-Step)

1. Pull image
2. Unpack layers via OverlayFS
3. Create cgroups for limits
4. Create all namespaces
5. `chroot` into new root
6. Start process inside new isolated world

So:

```bash
docker run nginx
```

internally does:

* create net namespace
* create mount namespace
* create PID namespace
* apply cgroups
* chroot into overlayfs
* exec `/usr/sbin/nginx`

---

# 🖥️ VM vs Container — Deep Difference

### 🖥️ Virtual Machine

```
Hardware → Hypervisor → Guest OS → App
```

Runs full OS per VM.

### 🐳 Container

```
Hardware → Host Kernel → Namespaces → App
```

Shares host kernel → extremely fast & lightweight.
