# SOC 2 Compliance Policy Management System

This repository contains a modular, microservice-based system to manage policy generation, approval, acknowledgement, and compliance tracking for SOC 2.

## 📘 Table of Contents

- [Overview](#-overview)
- [Features](#-features)
- [Architecture](#%EF%B8%8F-architecture)
- [Microservices](#%EF%B8%8F-microservices)
- [Data Model](#%EF%B8%8F-data-model)
- [Key Services and API Endpoints](#-key-services-and-API-Endpoints)
- [Workflow and Functions](#-workflow-and-functions)

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

![Design Sprinto drawio](https://github.com/user-attachments/assets/1a5a8299-b145-46f5-9750-288e2b331a88)


---

## ⚙️ **Microservices**

| Service                     | Responsibilities                                   |
| --------------------------- | -------------------------------------------------- |
| **Policy Service**          | CRUD for templates and custom policies, versioning |
| **Approval Service**        | Approval workflow (CTO, policy lifecycle)          |
| **Acknowledgement Service** | Manage acknowledgement states, due dates           |
| **Role Mapping Service**    | Policy-Role mapping logic                          |
| **Notification Service**    | Sends emails, escalations                          |
| **Scheduler Service**       | Triggers periodic or SLA-based checks              |
| **Audit Log Service**       | Writes structured logs                             |
| **Company Service**         | configurations, settings                           |

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

---

🧪 Sample API Endpoints

## 📦 Key Services and API Endpoints

### 📝 Policy Service

#### Create Policy
- **Endpoint**: `POST /api/policies`
- **Description**: Creates a new policy (from a template or custom).
- **Request Payload**: Policy type, policy name, content, etc.
- **Response**: Policy ID and details.

#### Get All Policies
- **Endpoint**: `GET /api/policies`
- **Description**: Retrieves a list of all policies in the system.
- **Response**: List of policy objects with their current status and version.

#### Update Policy
- **Endpoint**: `PUT /api/policies/{policy_id}`
- **Description**: Updates an existing policy.
- **Request Payload**: Updated policy content, version, etc.
- **Response**: Success or failure message.


### ✅ Policy Approval Service

#### Approve Policy
- **Endpoint**: `POST /api/policies/{policy_id}/approve`
- **Description**: Marks a policy as approved by the designated approver (e.g., CTO).
- **Request Payload**: Approver ID.
- **Response**: Approval status.



### 🙋 Employee Acknowledgement Service

#### Acknowledge Policy
- **Endpoint**: `POST /api/acknowledge/{policy_id}`
- **Description**: Allows an employee to acknowledge a policy.
- **Request Payload**: Employee ID, acknowledgement type (new join, periodic, manual).
- **Response**: Acknowledgement status.

#### Get Acknowledgement Status
- **Endpoint**: `GET /api/acknowledge/status`
- **Description**: Retrieves the acknowledgement status of policies for a specific employee.
- **Response**: List of policies with their acknowledgement status.


### 👥 Role and Policy Mapping Service

#### Assign Policies to Role
- **Endpoint**: `POST /api/roles/{role_id}/assign_policies`
- **Description**: Assigns a list of policies to a specific employee role.
- **Request Payload**: List of policy IDs.
- **Response**: Success message.

#### Get Policies for Role
- **Endpoint**: `GET /api/roles/{role_id}/policies`
- **Description**: Retrieves the list of policies assigned to a particular role.
- **Response**: List of policy IDs.


### 🧑 Employee Service

#### New Employee Onboarding
- **Endpoint**: `POST /api/employees/onboard`
- **Description**: Onboards a new employee and triggers the policy acknowledgement workflow.
- **Request Payload**: Employee details, company ID, etc.
- **Response**: Employee ID and status of the acknowledgement process.

#### Get Employee Acknowledgement Status
- **Endpoint**: `GET /api/employees/{employee_id}/acknowledgement`
- **Description**: Retrieves the acknowledgement status for all policies of a specific employee.
- **Response**: List of policies with their acknowledgement status.


### 📜 Audit Log Service

#### Log Policy Action
- **Endpoint**: `POST /api/audit/log`
- **Description**: Logs actions like policy creation, modification, approval, etc.
- **Request Payload**: Action details (type, actor, target, timestamp).
- **Response**: Success message.

### 🔗 Notification Service

### Send Escalation To CXO
- **Endpoint**: `POST /api/notifications/escalate`
- **Description**: Sends an escalation email to CXO (or escalation contact) if a policy acknowledgement is overdue.
- **Request Payload**: Company ID, employee ID, policy ID, policy version ID, due date, escalation level (e.g., CXO).
- **Response**: Status message confirming escalation email was sent.
  
---

## 🔄 Workflow and Functions

### 🛠️ Policy Creation and Approval Flow

- A company administrator can create a new policy using either a predefined template or custom content.
- If a template is used, the system creates a `policy` record based on the selected `policy_template`.
- The newly created policy is marked as **"draft"** and awaits approval by a designated approver (e.g., CTO).
- Upon approval, the policy status transitions to **"approved"** and becomes the company's active version.


### 🙋 Employee Acknowledgement Flow

- When a **new employee** is onboarded, the system automatically triggers **acknowledgement requests** for all active policies with a **30-day** deadline.
- For **periodic acknowledgements** (e.g., annual), the system tracks due dates and sends timely reminders.
- If an employee **misses the acknowledgement deadline**, the system generates an **alert**, escalating it to a CXO or designated authority.
- In case of **modified policies**, only the updated policies are sent for re-acknowledgement.


### 📋 Audit and Monitoring

- Every significant action (e.g., policy creation, updates, approvals, acknowledgements) is **logged** in the audit system.
- The system maintains a **complete acknowledgement history** per employee to support compliance audits and traceability.

---


