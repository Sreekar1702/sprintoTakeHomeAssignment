# SOC 2 Compliance Policy Management System

This repository contains a modular, microservice-based system to manage policy generation, approval, acknowledgement, and compliance tracking for SOC 2 and similar frameworks.

## 📘 Table of Contents

- [Overview](#overview)
- [Features](#features)
- [Architecture](#architecture)
- [Microservices](#microservices)
- [Data Model](#data-model)
- [APIs](#apis)
- [Monitoring & Observability](#monitoring--observability)

---

## 🧩 Overview

This system enables:
- Management of default and custom policies
- Approval workflows
- Role-based acknowledgement workflows
- Policy version control
- Audit-friendly history of all actions
- Periodic & event-based triggers

---

## ✅ Features

- Default & custom policies per company
- Versioned policies with config support
- Role-based policy mapping
- CTO-level approval workflows
- Multi-mode policy acknowledgement:
  - New hire
  - Periodic (e.g., annual)
  - Manual trigger
- Alerting for overdue acknowledgements
- Escalation workflows
- Audit trails for all activity

---

## 🏗️ Architecture

Frontend (React) → API Gateway → Microservices (via REST/gRPC)
                                 ↓
                   PostgreSQL / Redis / Kafka / S3 / ELK / Prometheus

---

## ⚙️ **Microservices**

| Service                     | Responsibilities                                   |
| --------------------------- | -------------------------------------------------- |
| **Policy Service**          | CRUD for templates and custom policies, versioning |
| **Approval Service**        | Approval workflow (CTO, policy lifecycle)          |
| **Acknowledgement Service** | Manage acknowledgement states, due dates           |
| **Role Mapping Service**    | Policy-Role mapping logic                          |
| **Notification Service**    | Sends emails, Slack alerts, escalations            |
| **Scheduler Service**       | Triggers periodic or SLA-based checks              |
| **Audit Log Service**       | Writes structured logs to ELK                      |
| **User Service**            | Employee profile, role info                        |
| **Company Service**         | Tenant configurations, settings                    |

---

## 🗃️ Data Model

### Database Schema

| Table Name                | Description                                                                |
|---------------------------|----------------------------------------------------------------------------|
| `policy_template`         | Stores default policy templates provided by Sprinto.                       |
| `policy_template_version` | Tracks version history of each policy template.                            |
| `company`                 | Represents customer companies using the system.                            |
| `policy`                  | Stores company-specific policies (custom or based on a template).          |
| `policy_version`          | Tracks changes to a policy, including content and configuration updates.   |
| `employee`                | Represents an individual employee within a company.                        |
| `role`                    | Defines organizational roles (e.g., Engineering, HR).                      |
| `role_policy`             | Maps roles to the policies that employees in those roles must acknowledge. |
| `acknowledgement_event`   | Records initiation of acknowledgment cycles (e.g., new join, annual).      |
| `acknowledgement_request` | Links employees to an acknowledgment event and a specific policy version.  |
| `policy_upgrade_prompt`   | Tracks whether a company has been notified of a new template version.      |

![diagram-export-11-05-2025-19_22_06](https://github.com/user-attachments/assets/c56926d4-6e8d-4b40-9673-3833ec80f9f8)

