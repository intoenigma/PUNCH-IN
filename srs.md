# 📘 Software Requirement Specification (SRS)

## Product Name: **PunchDesk**

**Version:** Phase 1
**Document Type:** Software Requirement Specification
**Audience:** Backend, Web, Mobile Development Teams

---

## 1. Introduction

### 1.1 Purpose

The purpose of this document is to define the **functional, technical, and behavioral requirements** of the PunchDesk system.
This document acts as a **single source of truth** for all development teams and ensures:

* Clear understanding of system behavior
* Consistent implementation across platforms
* Reduced ambiguity during development
* Easy scalability and future enhancements

---

### 1.2 Product Overview

PunchDesk is a **cloud-based, multi-tenant workforce management system** designed to manage:

* Attendance (biometric + mobile)
* Leave management
* Payroll processing
* Compliance calculations
* Analytics and insights

The system dynamically adapts its **dashboard and KPIs based on organization type**, such as:

* Office
* Corporate
* School
* Hospital
* Factory
* Laboratory

---

### 1.3 Technology Stack

#### Backend

* Python (Flask)
* RESTful APIs (JSON)
* JWT-based authentication
* Webhook support for biometric devices
* Async processing (future-ready)

#### Web Frontend

* React (Headless architecture)
* API-driven state management
* Role-based UI rendering

#### Mobile Applications

* Core React Native (without Expo)
* Native builds for Android and iOS
* Push notification support

---

## 2. System Overview

### 2.1 High-Level Architecture

```
Biometric Devices / Mobile Apps
            ↓
        Flask API
            ↓
        Database
            ↓
  React Web / React Native Apps
```

* Backend acts as the **central authority**
* Web and Mobile clients are **API consumers only**
* No business logic exists on the client side

---

### 2.2 User Roles

| Role               | Description                             |
| ------------------ | --------------------------------------- |
| Super Admin        | Internal PunchDesk system administrator |
| Organization Owner | Owner of an organization                |
| HR / Admin         | Manages staff, attendance, payroll      |
| Manager            | Supervises team members                 |
| Staff              | Regular employee                        |

---

## 3. Functional Requirements

---

## 3.1 Authentication & Authorization

### 3.1.1 Authentication

* Email and password login
* JWT token generation on successful login
* Token-based session management

### 3.1.2 Authorization

* Role-based access control (RBAC)
* Permissions enforced at API level
* UI dynamically adapts to user role

---

## 3.2 Organization Management

### 3.2.1 Organization Setup

Each organization must configure:

* Organization name
* Organization type
* Time zone
* Attendance rules

### 3.2.2 Organization Type Behavior

Organization type influences:

* Dashboard layout
* Default KPIs
* Visibility of certain modules

> Core business logic remains unchanged; only presentation differs.

---

## 3.3 User Management

### 3.3.1 User Creation

Admins can:

* Create and update users
* Assign department and shift
* Assign roles
* Assign salary structures

### 3.3.2 User Status

Supported statuses:

* Active
* Inactive
* Terminated (historical data retained)

---

## 3.4 Attendance Management

### 3.4.1 Attendance Sources

Attendance events may originate from:

* Biometric machines
* Mobile applications
* Manual admin entry

### 3.4.2 Attendance Rules

* Punch-IN must occur before Punch-OUT
* Duplicate punches are ignored
* Shift rules applied automatically
* Late arrival and early exit detection

### 3.4.3 Real-Time Processing

* Attendance saved instantly
* Dashboards update in real time
* Alerts triggered when rules are violated

---

## 3.5 Live Dashboard

### 3.5.1 Dashboard Metrics

* Present / Absent count
* Late arrivals
* Employees on leave
* Working hours
* Overtime

### 3.5.2 Behavior

* Auto-refresh enabled
* Role-based visibility
* Organization-specific widgets

---

## 3.6 Leave Management

### 3.6.1 Leave Configuration

* Multiple leave types
* Paid and unpaid leaves
* Accrual and carry-forward rules

### 3.6.2 Leave Workflow

1. Employee applies for leave
2. Manager reviews request
3. Approval or rejection
4. Attendance auto-adjustment
5. Payroll impact applied

---

## 3.7 Employee Self-Service Portal

### 3.7.1 Employee Capabilities

* Punch in/out
* View attendance history
* Check leave balance
* Apply for leave
* Download payslips
* View notifications

### 3.7.2 Restrictions

* Employees cannot edit attendance
* Payroll data is read-only
* No administrative access

---

## 3.8 Shift Management

### 3.8.1 Shift Definition

* Start and end time
* Grace period
* Overnight shift support

### 3.8.2 Assignment Rules

* One default shift per user
* Shift change history maintained

---

## 3.9 Payroll Processing

### 3.9.1 Payroll Types

* Monthly salaried
* Daily wage
* Hourly wage

### 3.9.2 Payroll Workflow

1. Select payroll period
2. Lock attendance data
3. Automatic salary calculation
4. Admin review
5. Payroll finalization

### 3.9.3 Compliance

* Provident Fund (PF)
* Employee State Insurance (ESI)
* Professional Tax
* Basic TDS

---

## 3.10 Salary Structure Management

### 3.10.1 Salary Components

* Earnings
* Deductions
* Formula-based calculations

### 3.10.2 Assignment

* User-specific salary structures
* Effective date tracking

---

## 3.11 Cashbook & Advance Management

### 3.11.1 Entry Types

* Salary advance
* Loan
* Expense
* Adjustment

### 3.11.2 Payroll Integration

* Automatic deduction rules
* Configurable recovery logic

---

## 3.12 Alerts & Notifications

### 3.12.1 Trigger Events

* Late arrival
* Missed punch
* Leave approval
* Payroll completion

### 3.12.2 Delivery Channels

* Web notifications
* Mobile push notifications

---

## 3.13 Analytics & Insights

### 3.13.1 Reports

* Attendance trends
* Late arrival patterns
* Overtime costs
* Department-level insights

### 3.13.2 AI (Phase 1 – Basic)

* Pattern detection
* Anomaly alerts

---

## 3.14 API & Integrations

### 3.14.1 APIs

* Attendance APIs
* User APIs
* Payroll APIs

### 3.14.2 Webhooks

* Biometric machine data push
* Attendance event notifications
* Payroll finalization events

---

## 4. Non-Functional Requirements

| Category     | Requirement          |
| ------------ | -------------------- |
| Performance  | API response < 300ms |
| Availability | 99.9% uptime         |
| Security     | JWT, RBAC            |
| Scalability  | Multi-tenant ready   |
| Auditability | Full action logs     |

---

## 5. Error Handling

* Standardized API error codes
* User-friendly error messages
* Retry mechanisms for device data
* No silent failures

---

## 6. Logging & Audit Trail

* Attendance edits logged
* Payroll actions logged
* User activity tracked

---

## 7. Assumptions

* Stable internet connectivity
* Biometric devices support push APIs
* Admin properly configures organization rules

---

## 8. Out of Scope (Phase 1)

* Face recognition
* GPS geo-fencing
* Student attendance
* WhatsApp automation
* Direct bank payouts

---

## 9. Future Considerations

System architecture supports:

* Advanced AI analytics
* Financial integrations
* Compliance automation
* Third-party marketplace integrations

---

## 10. Conclusion

PunchDesk Phase 1 is designed as a **scalable, secure, and industry-adaptive workforce management platform**.