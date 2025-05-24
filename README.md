# Vacation Tracking System

A comprehensive system to manage employee vacation requests, approvals, and tracking with full visibility and policy compliance.

## Table of Contents
1. [Requirements](#requirements)
   - [Vision](#vision)
   - [Functional Requirements](#functional-requirements)
   - [Non-Functional Requirements](#non-functional-requirements)
   - [Constraints](#constraints)
2. [Domain Definition](#domain-definition)
3. [Actors](#actors)
4. [Use Cases](#use-cases)
   - [Manage Time Off](#manage-time-off)
     - [Entities (Data Model)](#entities-data-model)
 

## Requirements

### Vision
To create a centralized, automated system that streamlines the vacation request process, ensuring fair allocation of time off while maintaining optimal team coverage. The system will replace manual processes with a transparent, policy-compliant solution that provides real-time visibility into team availability.

### Functional Requirements
1. **Employee Features:**
   - Submit vacation requests with start/end dates and reason
   - View remaining vacation days balance
   - Check request status (Pending/Approved/Denied)
   - Cancel pending requests
   - View team vacation calendar

2. **Manager Features:**
   - Approve/deny vacation requests with comments
   - View subordinate vacation calendars
   - Receive conflict alerts
   - Override policies (with audit trail)

3. **Administrative Features:**
   - Configure vacation policies (accrual rules, blackout dates)
   - Generate utilization reports
   - Manage carry-over days
   - Handle rollovers at fiscal year-end

4. **System Features:**
   - Automatic accrual calculations
   - Policy enforcement engine
   - Email/SMS notifications
   - Audit logging

### Non-Functional Requirements
- **Performance:**
  - Support 1000+ concurrent users
  - <2s response time for 95% of requests
  - Batch processing of accruals completes within 4 hours

- **Reliability:**
  - 99.95% uptime during business hours (8AM-8PM local time)
  - Automatic failover for critical components
  - Data backup every 15 minutes

- **Security:**
  - Role-based access control
  - TLS 1.2+ for all communications
  - AES-256 encryption for sensitive data
  - SOC 2 Type II compliance

- **Usability:**
  - Mobile-responsive interface
  - WCAG 2.1 AA accessibility compliance
  - Available in English, Spanish, and French

### Constraints
1. **Technical:**
   - Must integrate with existing Active Directory
   - Support IE11 for first 6 months (legacy requirement)
   - Oracle database backend (company standard)

2. **Business:**
   - Comply with EU working time directive
   - Support union contracts with different accrual rules
   - Maintain 7-year audit history

3. **Operational:**
   - Deployable on-premises or in AWS
   - Zero-downtime patches
   - <30 minute recovery time objective

## Domain Definition

The Vacation Tracking System operates in the human capital management domain, specifically addressing workforce time-off management. Key domain concepts include:

- **Vacation Accrual:** Days earned based on tenure and work schedule
- **Blackout Periods:** Dates when vacation is restricted
- **Carryover Policy:** Unused days that can roll to next year
- **Request Lifecycle:** Submission → Manager Review → Approval/Denial → Tracking
- **Conflict Detection:** Prevent multiple key team members being absent simultaneously

The system solves problems of:
- Manual paper-based request processes
- Lack of visibility into team availability
- Inconsistent policy application
- Difficulty tracking accruals and usage

## Actors

1. **Employee**
   - Submits and manages vacation requests
   - Views personal balance and history
   - Checks team availability

2. **Manager**
   - Approves/denies subordinate requests
   - Views team calendars
   - Receives absence alerts
   - Generates team reports

3. **HR Administrator**
   - Configures company policies
   - Manages fiscal year transitions
   - Handles exceptions and overrides
   - Runs compliance audits

4. **System (Automated Processes)**
   - Calculates daily accruals
   - Enforces policy rules
   - Sends notifications
   - Generates scheduled reports

## Use Cases

### Manage Time Off

#### Entities (Data Model)
```mermaid
classDiagram
    class Employee {
        +String employeeId
        +String firstName
        +String lastName
        +Date hireDate
        +String workSchedule
        +Decimal vacationBalance
        +List~VacationRequest~ requests
        +Decimal getAccrualRate()
    }
    
    class VacationRequest {
        +String requestId
        +Date submissionDate
        +Date startDate
        +Date endDate
        +String status
        +String reason
        +String approverComments
        +Employee requester
        +Manager approver
        +Boolean overlaps(Request other)
    }
    
    class Policy {
        +String policyId
        +String description
        +Decimal baseAccrualRate
        +Map~Integer,Decimal~ tenureRates
        +Integer maxCarryover
        +List~DateRange~ blackoutPeriods
        +Boolean isAllowed(Request request)
    }
    
    Employee "1" --> "0..*" VacationRequest
    Manager --|> Employee
    VacationRequest "1" --> "1" Policy
