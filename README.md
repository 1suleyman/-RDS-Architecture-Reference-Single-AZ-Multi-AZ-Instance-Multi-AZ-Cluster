# 🧩 **RDS Architecture Reference — Single-AZ, Multi-AZ Instance & Multi-AZ Cluster**

A practical guide I can revisit whenever I need to design the right Amazon RDS architecture for a project — from simple dev setups to highly available production databases.

This document explains **what each architecture looks like**, **when to use it**, **its trade-offs**, and **how to make the right decision** as a DevOps engineer.

---

## 📌 **1. Overview of RDS Deployment Models**

AWS RDS offers three major architecture patterns:

1. **Single-AZ** — cheapest, simplest, no failover
2. **Multi-AZ Instance** — primary + synchronous standby, improved availability
3. **Multi-AZ Cluster** — primary + standby + read replicas, best for production

Use this guide to pick the right one *based on cost, traffic, and reliability requirements*.

---

# 🔑 **2. Single-AZ RDS Setup**

<img width="485" height="211" alt="Screenshot 2025-11-24 at 10 59 28" src="https://github.com/user-attachments/assets/725621db-aa26-4eb8-992d-27af660cef8e" />

### **Architecture**

* App in AZ A
* Database in AZ B
* **One DB instance only** (handles both reads + writes)

### **Best for**

* Development
* Staging
* POCs and prototypes
* Low-traffic internal apps

### **Strengths**

* 💲 Lowest cost
* ⚡ Fastest setup
* 🔧 Minimal infrastructure

### **Limitations**

* ❌ No automatic failover
* ❌ Single point of failure
* ❌ Downtime if AZ B goes down
* ❌ Cannot scale reads

> 💡 If the DB AZ fails, the whole application goes offline.

---

# 🔑 **3. Multi-AZ Instance Setup (Primary + Synchronous Standby)**

<img width="681" height="333" alt="Screenshot 2025-11-24 at 10 59 48" src="https://github.com/user-attachments/assets/1f6c39c7-51d2-4842-a140-3969d40b9aed" />

### **Architecture**

* App in AZ A
* Primary in AZ B
* Standby copy in AZ A (for automatic failover)
* Optional **read replicas** (in AZ A or B)

### **Why this matters**

* **Primary DB handles writes**
* **Read replica handles reads** → faster performance
* **Automatic failover** to standby

### **Use cases**

* Apps with high read traffic
* Apps where availability matters more than cost
* Mid-scale production systems

### **Drawbacks**

* 💲 Higher cost than Single-AZ
* Read replica is still a separate instance
* Standby **cannot** be used for reads

> ➡️ Great for *read scaling*, but not the best for heavy production workloads.

---

# 🔑 **4. Multi-AZ Cluster (Primary + Standby + Read Replicas)**

<img width="725" height="313" alt="Screenshot 2025-11-24 at 11 00 18" src="https://github.com/user-attachments/assets/4c30156b-1c85-49a0-9f27-ecf154dc0acd" />

### **This is AWS’s modern production-grade architecture.**

### **Architecture Components**

| Component                            | Purpose                                             |
| ------------------------------------ | --------------------------------------------------- |
| **Primary DB**                       | Performs reads + writes                             |
| **Standby DB**                       | Failover only — promotes instantly if primary fails |
| **Read Replicas**                    | Scale read load                                     |
| **Cross-region replicas (optional)** | Improve global latency + DR                         |

### **Benefits**

* ✔ Automatic failover (fast + reliable)
* ✔ Horizontal read scaling
* ✔ Cross-region read replicas
* ✔ Ideal for high-traffic production systems
* ✔ Strong resilience + low RPO/RTO

### **Perfect for**

* Mission-critical apps
* High-traffic production workloads
* Global user bases
* Apps requiring strong HA + scalability

---

# 🔑 **5. Cost Comparison**

| Architecture          | Cost           |
| --------------------- | -------------- |
| **Single-AZ**         | 💲 Lowest      |
| **Multi-AZ Instance** | 💲💲 Medium    |
| **Multi-AZ Cluster**  | 💲💲💲 Highest |

> ✔ Cost must match app criticality
> ✔ Don’t over-architect dev/test systems
> ✔ Don’t under-architect production systems

---

# 🔑 **6. How Replication Works (High-Level)**

AWS automatically manages:

* Sync replication → standby
* Async replication → read replicas
* Secure region-to-region replication
* Failover + promotion logic

> 💡 DevOps engineers never manually manage logs or replication — AWS RDS does it for you.

---

# 🔑 **7. When To Choose Which Architecture**

| Scenario                               | Recommended Architecture                     |
| -------------------------------------- | -------------------------------------------- |
| Dev, test, POC                         | **Single-AZ**                                |
| Medium read traffic                    | **Multi-AZ Instance**                        |
| High availability **and** read scaling | **Multi-AZ Cluster**                         |
| Mission-critical prod apps             | **Multi-AZ Cluster**                         |
| Global users                           | **Multi-AZ Cluster + cross-region replicas** |

> 💡 Evaluate: **cost → traffic → app criticality → HA requirements**

---

# ❓ **Q&A (Active Recall)**

| Question                             | Answer                            |
| ------------------------------------ | --------------------------------- |
| Q1: What is Single-AZ best for?      | Dev, staging, POCs                |
| Q2: Does Single-AZ support failover? | No                                |
| Q3: What defines Multi-AZ Instance?  | Primary + read replica            |
| Q4: What defines Multi-AZ Cluster?   | Primary + standby + read replicas |
| Q5: Can standby handle reads?        | No                                |
| Q6: Best production-grade option?    | Multi-AZ Cluster                  |
| Q7: Who manages replication?         | AWS automatically                 |
| Q8: Which setup is most expensive?   | Multi-AZ Cluster                  |

---

# ⚡ **Big-Picture Takeaway**

AWS provides a tiered path:

* **Single-AZ**
  Simple + cheapest → great for dev/test

* **Multi-AZ Instance**
  Read scalability → moderate HA

* **Multi-AZ Cluster**
  High availability + read scaling + global reach → best for production

Choosing the right one is a core DevOps responsibility.

---

# 🧠 Analogy (To Remember Forever)

**Think of RDS architectures like houses:**

* **Single-AZ** → One door
  *If it breaks, no one gets in.*

* **Multi-AZ Instance** → Two doors
  *One for family (writes), one for guests (reads).*

* **Multi-AZ Cluster** → Two doors + backup emergency exit + guest entrance
  *High availability, multiple ways in, always online.*
