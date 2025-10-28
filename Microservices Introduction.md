# 📘 Notes: Introduction to Microservices and DevOps

## 🧩 Microservices Overview

### What Are Microservices?

- Architectural style dividing an application into **independent, loosely coupled services**.
- Each service is **autonomous**, **fine-grained**, and **independently deployable**.
- Contrasts with **monolithic architecture**, where all functionality exists in a single unit.

### ✅ Benefits

- **Scalability:** Scale components independently.
- **Flexibility:** Use different languages or frameworks per service.
- **Independent Deployment:** Enables faster and isolated releases.

### 🔑 Key Concepts

- **REST API Communication:**  
  - Services interact via HTTP/HTTPS-based REST APIs.  
  - Ensures cross-language interoperability.

- **Statelessness:**  
  - Services don’t store internal state.  
  - State managed externally (e.g., databases).  
  - Facilitates replication and scalability.

- **Externalized Logging:**  
  - Logs centralized for monitoring and debugging.  
  - Common tools: **Fluentd**, **ELK Stack**, **Splunk**.

- **Load Balancing & DNS:**  
  - **Load Balancer:** Distributes network traffic for stability and availability.  
  - **DNS:** Translates domain names into IP addresses for accessibility.

---

## ⚙️ DevOps Introduction

### Why DevOps?

- Integrates **Development (Dev)** and **Operations (Ops)**.  
- Goal: **Shorter development cycles**, **continuous delivery**, and **high-quality software**.

### CI/CD Pipeline

- **Continuous Integration (CI):**  
  - Frequent merging and automated testing of code changes.
- **Continuous Deployment (CD):**  
  - Automated release of validated code to production.
- Tools form the backbone of an automated **CI/CD pipeline**.

---

### 📝 Summary

Microservices enable **modular, scalable applications**, while DevOps ensures **efficient, automated delivery** of those services. Together, they form the foundation of **modern, cloud-native software development**.
