# 📘 Business Requirement Document (BRD)

## Product Name: **PunchDesk**

**Version:** Phase 1
**Product Type:** Cloud-based Attendance, Payroll & Workforce Management Platform

---

## 1. Product Overview

**PunchDesk** is a **cloud-based, headless workforce management system** designed for organizations of different types (School, Office, Corporate, Hospital, Lab, Factory, etc.).

The system integrates with **biometric machines** and **mobile-based attendance**, processes attendance data in **real-time**, and provides **custom dashboards based on organization type**.

PunchDesk focuses on:

* Real-time attendance tracking
* Payroll automation
* Compliance
* Analytics & insights
* Industry-specific experiences

---

## 2. Objectives & Goals

### Primary Objectives

* Capture **accurate punch-in/punch-out data** in real time
* Reduce payroll errors & manual work
* Provide **industry-adapted dashboards**
* Enable employees and managers via mobile apps
* Offer **open APIs** for integrations

### Business Goals

* Compete with eTimeOffice, PagarBook, Petpooja Payroll
* Become a **platform**, not just attendance software
* Enable future expansion (GPS, face recognition, payroll lending, etc.)

---

## 3. Target Organization Types (Phase 1)

| Organization Type  | Special Focus                      |
| ------------------ | ---------------------------------- |
| Office / Corporate | Attendance, payroll, leaves        |
| School             | Staff attendance, leave visibility |
| Hospital / Lab     | Shift-based workforce              |
| Factory            | Multi-shift, overtime              |

> Organization type is selected during onboarding and affects **dashboard layout & KPIs**.

---

## 4. User Roles & Access Control

### 4.1 User Roles

| Role        | Description                       |
| ----------- | --------------------------------- |
| Super Admin | PunchDesk internal admin          |
| Org Owner   | Organization-level owner          |
| HR/Admin    | Attendance, payroll control       |
| Manager     | Team-level visibility & approvals |
| Staff       | Attendance, self-service          |

### 4.2 Role-Based Access Control (RBAC)

* Fine-grained permissions
* API-level enforcement
* UI adapts based on role

---

## 5. System Architecture (High Level)

### Backend

* **Flask (Python)**
* REST APIs (JSON)
* JWT-based authentication
* Webhooks for machine data
* Background jobs (Celery / RQ later)

### Frontend (Web)

* **React (Headless)**
* Consumes APIs only
* Dashboard rendered dynamically by org type

### Mobile Apps

* **Core React Native (No Expo)**
* Separate apps or role-based UI

  * Staff App
  * Manager App

### Database

* MySQL
* Multi-tenant ready (org_id based)

---

## 6. Core Features (Phase 1)

### 6.1 Unified Attendance Tracking ✅

**Sources:**

* Biometric machines (Fingerprint / Face)
* Mobile app punch-in
* Manual admin correction (with audit)

**Captured Data:**

* user_id
* date
* punch_type (IN / OUT)
* time
* device_id
* source (machine / mobile)
* extra_notes
* location (optional)

**Key Rules:**

* One IN → One OUT per shift
* Late & early rules configurable
* Offline data sync supported

---

### 6.2 Live Dashboard & Real-Time Alerts ✅

**Live Metrics:**

* Present / Absent / Late
* On Leave
* Working hours
* Overtime today

**Alerts:**

* Missed punch
* Late arrival
* Early leave
* Shift understaffed

**Delivery:**

* Web dashboard
* Mobile push (Manager)

---

### 6.3 Comprehensive Leave Management ✅

**Features:**

* Multiple leave types
* Paid / unpaid
* Accrual rules
* Carry forward
* Half-day support

**Workflow:**

1. Staff applies leave
2. Manager approves/rejects
3. Leave auto-adjusts attendance
4. Payroll reflects leave impact

---

### 6.4 Employee Self-Service Portal (Web & Mobile) ✅

**Staff Can:**

* Punch in/out
* View attendance history
* View leave balance
* Apply for leave
* Download payslips
* View announcements

**Goal:** Reduce HR dependency

---

### 6.5 Payroll Automation & Compliance ✅

**Payroll Capabilities:**

* Monthly / Daily / Hourly staff
* Overtime calculation
* Late deductions
* Bonuses

**Compliance:**

* PF
* ESI
* Professional Tax
* TDS (basic)

**Outputs:**

* Payslip (PDF)
* Payroll summary
* Compliance reports

---

### 6.6 Customizable Salary Structures ✅

* Salary components:

  * Basic
  * HRA
  * Allowances
  * Deductions
* Formula-based calculation
* Organization-level templates
* Role-wise salary structures

---

### 6.7 Bulk Payments & Bank Integration ✅

**Phase 1 Scope:**

* Export bank-ready salary sheet
* Mark payment status (Paid / Pending)

**Future Ready:**

* Direct bank API
* UPI payouts
* Earned wage access

---

### 6.8 Cashbook & Expense Management ✅

**Features:**

* Salary advances
* Staff loans
* Manual adjustments
* Expense remarks

**Purpose:**

* Track money outside payroll
* Reduce accounting mismatch

---

### 6.9 Analytics & AI Insights ✅

**Analytics:**

* Attendance trends
* Late patterns
* Overtime cost
* Department-wise insights

**AI (Basic Phase 1):**

* Anomaly detection
* Repeated late behavior
* Irregular punch alerts

---

### 6.10 Industry-Specific Dashboard Modes ✅

**Onboarding Step:**
User selects org type → dashboard adapts.

**Examples:**

* School → Staff attendance focus
* Hospital → Shift-wise view
* Factory → Overtime & productivity
* Office → Payroll & leave KPIs

**USP Feature – NOT AVAILABLE in competitors**

---

### 6.11 Third-Party Integrations & API ✅

**APIs:**

* Attendance data
* Employee data
* Payroll summary

**Webhooks:**

* Biometric push
* Attendance events
* Payroll finalized

**Use Cases:**

* ERP
* Accounting
* Custom apps

---

### 6.12 Robust Data Security & Cloud Performance ✅

**Security:**

* JWT auth
* Role-based access
* Encrypted sensitive fields
* Audit logs

**Performance:**

* Async processing
* Scalable APIs
* Real-time safe

---

### 6.13 Mobile Apps for Managers ✅

**Manager Capabilities:**

* Team attendance snapshot
* Leave approvals
* Alerts & notifications
* Daily summary

**Tech:**

* Core React Native
* Native build (Android/iOS)

---

## 7. Non-Functional Requirements

| Category     | Requirement          |
| ------------ | -------------------- |
| Scalability  | 1000+ orgs           |
| Availability | 99.9%                |
| Latency      | < 300ms APIs         |
| Localization | Multi-language ready |
| Compliance   | Indian laws          |

---

## 8. Phase 1 Exclusions (Future Phases)

* Face recognition via phone
* GPS geo-fencing
* Student attendance
* Payroll lending
* Insurance integrations
* WhatsApp automation

---

## 9. Success Metrics (KPIs)

* Attendance accuracy > 99%
* Payroll error rate < 1%
* Daily active users
* Org retention rate
* API usage count

---

## 10. Final Summary

**PunchDesk Phase 1** will deliver:

* All **market-standard features**
* Multiple **market-gap features**
* Strong **technical foundation**
* Clear **expansion roadmap**

This positions PunchDesk as:

> **“Not just attendance software, but a workforce operating system.”**