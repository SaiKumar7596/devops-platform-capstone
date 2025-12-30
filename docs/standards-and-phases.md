# DevOps Platform Capstone  
## Standards, Rules & Execution Phases

---

## 🔴 GLOBAL RULES (APPLY TO ALL TASKS)

❌ No manual server configuration  
❌ No UI-based infrastructure creation  
❌ No secrets in code  
❌ No skipping failed stages  

✅ Everything must be reproducible  
✅ Every task must have proof  

---

## 🟦 PHASE 0 — PROJECT INITIALIZATION & STANDARDS

### TASK 0.1 — Repository & Standards Setup

**Objective**  
Establish a professional repository structure and clear ownership.

**Actions**
- Create GitHub repository: `devops-platform-capstone`
- Create folders:
  - `terraform/`
  - `ansible/`
  - `jenkins/`
  - `app/`
  - `docs/`

- Add `README.md` with:
  - Project purpose
  - Toolchain overview
  - High-level architecture

**Expected Output**
- Clean repository structure
- No infrastructure or application code yet

**Evaluation Signals**
- Clear separation of concerns
- README explains ownership and scope

---

## 🟦 PHASE 1 — INFRASTRUCTURE PROVISIONING (TERRAFORM)

### TASK 1.1 — Terraform Backend Setup

**Objective**  
Ensure safe, team-ready Terraform usage.

**Actions**
- Configure remote backend:
  - State storage
  - State locking
- Enable:
  - Encryption
  - Versioning
- Validate backend initialization

**Expected Output**
- `terraform init` succeeds
- State file stored remotely

**Failure Injection (Mandatory)**
- Run `terraform apply` in two terminals

**Evaluation Signals**
- One run fails with lock error
- Student explains why state locking exists

---

### TASK 1.2 — Network Module

**Objective**  
Create isolated, configurable networking.

**Actions**
- Create `modules/network`
- Define:
  - Custom VPC
  - Public subnet
  - Internet Gateway
  - Route table
- Parameterize CIDR blocks

**Expected Output**
- No default VPC usage
- Network outputs:
  - VPC ID
  - Subnet ID

**Evaluation Signals**
- No hardcoded IPs
- Variables are well-named

---

### TASK 1.3 — Security Module

**Objective**  
Enforce least-privilege access.

**Actions**
- Create `modules/security`
- Define Security Groups:
  - Jenkins
  - SonarQube
  - Nexus
  - Docker Host
- Restrict access using SG references

**Expected Output**
- Only required ports open
- No `0.0.0.0/0` unless justified

**Failure Injection**
- Remove Jenkins → SonarQube rule

**Evaluation Signals**
- Sonar stage fails
- Fix applied via Terraform only

---

### TASK 1.4 — Compute Modules

**Objective**  
Provision dedicated servers.

**Actions**
- Create separate modules:
  - `compute-jenkins`
  - `compute-sonarqube`
  - `compute-nexus`
  - `compute-docker`
- Attach:
  - Security Groups
  - IAM roles
- No software installation here

**Expected Output**
- 4 running VMs
- Terraform outputs expose public IPs

**Evaluation Signals**
- One responsibility per server
- No `user_data` installs

---

## 🟦 PHASE 2 — CONFIGURATION MANAGEMENT (ANSIBLE)

### TASK 2.1 — Dynamic Inventory Setup

**Objective**  
Eliminate static infrastructure assumptions.

**Actions**
- Generate inventory from Terraform outputs
- Group hosts by role:
  - jenkins
  - sonarqube
  - nexus
  - docker

**Expected Output**
- Inventory updates automatically after infra change

**Evaluation Signals**
- No hardcoded IPs
- Inventory regenerates cleanly

---

### TASK 2.2 — Jenkins Role

**Objective**  
Fully configure Jenkins via code.

**Actions**
- Install Java
- Install Jenkins
- Install required plugins
- Start & enable Jenkins service

**Expected Output**
- Jenkins UI accessible
- Plugins installed automatically

**Failure Injection**
- Manually uninstall a plugin

**Evaluation Signals**
- Ansible re-run fixes drift
- Student explains idempotency

---

### TASK 2.3 — SonarQube Role

**Objective**  
Provide static analysis service.

**Actions**
- Install Docker
- Deploy SonarQube container
- Persist volumes
- Expose port 9000

**Expected Output**
- SonarQube dashboard accessible

**Failure Injection**
- Stop container

**Evaluation Signals**
- Pipeline fails at quality stage
- Recovery via Ansible

---

### TASK 2.4 — Nexus Role

**Objective**  
Centralize artifact storage.

**Actions**
- Deploy Nexus via Docker
- Create hosted repository
- Configure credentials

**Expected Output**
- Artifact repository accessible

**Failure Injection**
- Remove credentials

**Evaluation Signals**
- Upload fails
- Credentials restored securely

---

### TASK 2.5 — Docker Host Role

**Objective**  
Isolate build runtime.

**Actions**
- Install Docker engine
- Configure daemon
- Validate non-root access

**Expected Output**
- Docker commands work remotely

**Evaluation Signals**
- Jenkins does not build locally
- Docker builds run on host

---

## 🟦 PHASE 3 — CI/CD PIPELINE (JENKINS)

### TASK 3.1 — Jenkins Global Configuration

**Objective**  
Prepare Jenkins securely.

**Actions**
- Configure credentials:
  - GitHub
  - SonarQube
  - Nexus
  - Docker Hub
- Configure global tools

**Expected Output**
- No secrets in Jenkinsfile

**Evaluation Signals**
- Credentials referenced by ID only

---

### TASK 3.2 — Jenkinsfile Creation

**Objective**  
Define full pipeline.

**Stages**
- Checkout
- SonarQube Analysis
- Build & Test
- Artifact Upload
- Docker Build
- Docker Push
- Notifications

**Expected Output**
- Declarative pipeline
- Commit-based tagging

---

### TASK 3.3 — SonarQube Quality Gate Failure

**Objective**  
Enforce quality ownership.

**Actions**
- Introduce bad code
- Run pipeline

**Expected Output**
- Pipeline stops at quality gate

**Evaluation Signals**
- No downstream stages executed

---

### TASK 3.4 — Build & Test Failure

**Objective**  
Enforce test discipline.

**Actions**
- Break a unit test

**Expected Output**
- Build fails
- No artifact produced

---

### TASK 3.5 — Nexus Upload Validation

**Objective**  
Ensure artifact traceability.

**Actions**
- Upload artifact
- Verify metadata:
  - Version
  - Commit hash

**Expected Output**
- Artifact retrievable

---

### TASK 3.6 — Docker Build & Push

**Objective**  
Produce deployable image.

**Actions**
- Multi-stage Dockerfile
- Tag using commit SHA
- Push to Docker Hub

**Expected Output**
- Image visible in registry

**Failure Injection**
- Break Dockerfile

**Evaluation Signals**
- Build fails
- Fix documented

---

## 🟦 PHASE 4 — TRACEABILITY & OBSERVABILITY

### TASK 4.1 — End-to-End Traceability Proof

**Objective**  
Prove production readiness.

**Actions**
- Pick a Docker image
- Trace back to:
  - Jenkins build
  - Git commit
  - Artifact in Nexus

**Expected Output**
- Full trace documented

---

### TASK 4.2 — Notifications Validation

**Objective**  
Ensure feedback loop.

**Actions**
- Trigger success & failure builds

**Expected Output**
- Accurate notifications sent

---

## 🟦 PHASE 5 — DOCUMENTATION & DEFENSE

### TASK 5.1 — Documentation Creation

**Mandatory Sections**
- Architecture
- Terraform
- Ansible
- Jenkins
- Failures
- Learnings

**Expected Output**
- Professional documentation
- Screenshots everywhere

---

### TASK 5.2 — Final Defense (Mandatory)

Student must explain:
- Why Terraform ≠ Ansible
- Why quality gates block pipelines
- How failures propagate
- How recovery works using code

---

## ✅ FINAL PASS CONDITION

A candidate passes only if:

> **“They can build it, break it, fix it, and explain it — without touching the UI.”**

