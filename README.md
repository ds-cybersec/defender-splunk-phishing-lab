```markdown
# Multi-Layer Email Security & SIEM Ingestion Lab: Defender for Office 365 & Splunk

## Executive Summary
This project demonstrates the architecture and implementation of an enterprise-grade email security monitoring baseline, bridging cloud-native email telemetry from Exchange Online Protection (EOP) / Microsoft Defender for Office 365 with a Splunk SIEM instance.

---

## Lab Architecture & Workflow
* **Email Authentication:** Configured SPF, DKIM, and DMARC parameters to establish sender legitimacy and mitigate spoofing risks.
* **Cloud Telemetry Simulation:** Generated structured JSON event logs mirroring the Microsoft Management Activity API schema to simulate live enterprise telemetry without requiring expensive production licensing.
* **SIEM Ingestion Pipeline:** Uploaded and indexed mock logs into Splunk, mapping sourcetype properties (`_json`) to extract key fields including `ClientIP`, `operation`, and `UserId`.

---

## Lab Scenarios & Ingested Log Structures

### Scenario 1: Baseline Successful Authentication (Clean Traffic)
* **Description:** Represents normal, routine user authentication activity within the Azure Active Directory workload to establish a clean monitoring baseline.
```json
{
  "creationTime": "2026-09-11T01:00:00",
  "id": "guid-sample-12345",
  "operation": "UserLoggedIn",
  "organizationId": "yourdomain.onmicrosoft.com",
  "recordType": 15,
  "resultStatus": "Succeeded",
  "userKey": "user@yourdomain.onmicrosoft.com",
  "userType": 0,
  "version": 1,
  "workload": "AzureActiveDirectory",
  "ClientIP": "192.168.1.50",
  "UserId": "user@yourdomain.onmicrosoft.com"
}

```

### Scenario 2: Failed Login / Credential Harvesting Phishing Simulation

* **Description:** Captures an event where an email containing a phishing link bypassed initial filters, resulting in a failed authentication attempt from an external IP address.

```json
{
  "creationTime": "2026-09-11T02:15:00",
  "id": "guid-phish-99887",
  "operation": "UserLoginFailed",
  "organizationId": "yourdomain.onmicrosoft.com",
  "resultStatus": "Failed",
  "SenderAddress": "spoofed-support@external-phishing-domain.com",
  "RecipientAddress": "user@yourdomain.onmicrosoft.com",
  "Subject": "Urgent: Verify Your Corporate Password Immediately",
  "AuthenticationResults": "spf=fail dkim=fail dmarc=fail",
  "ClientIP": "185.220.101.5",
  "Workload": "AzureActiveDirectory"
}

```

### Scenario 3: Email Spoofing & DMARC Failure (Blocked by EOP)

* **Description:** Demonstrates Exchange Online Protection (EOP) successfully intercepting and quarantining a spoofed email due to failed SPF and DMARC checks.

```json
{
  "creationTime": "2026-09-11T03:30:00",
  "id": "guid-spoof-44556",
  "operation": "MessageBlocked",
  "organizationId": "yourdomain.onmicrosoft.com",
  "SenderAddress": "admin@hr-corporate-secured.com",
  "RecipientAddress": "employee@yourdomain.onmicrosoft.com",
  "Subject": "Mandatory Payroll Update",
  "AuthenticationResults": "spf=fail dkim=pass dmarc=fail",
  "ClientIP": "203.0.113.88",
  "DeliveryAction": "Quarantined",
  "ThreatDetection": "Spoof"
}

```

### Scenario 4: Data Loss Prevention (DLP) Policy Violation

* **Description:** Tracks an internal user attempting to transmit sensitive data externally, triggering a DLP rule block and notification event.

```json
{
  "creationTime": "2026-09-11T04:45:00",
  "id": "guid-dlp-11223",
  "operation": "DlpRuleMatch",
  "organizationId": "yourdomain.onmicrosoft.com",
  "SenderAddress": "user@yourdomain.onmicrosoft.com",
  "RecipientAddress": "personal-email@external.com",
  "Subject": "Q3 Financial Projections",
  "DlpPolicyName": "Financial Data Protection Policy",
  "ActionTaken": "BlockedAndNotified",
  "Workload": "Exchange"
}

```

---

## SPL Verification & Threat Hunting Queries

To query and analyze these distinct scenarios within Splunk, use the following syntax:

**Baseline Verification:**

```spl
index=main sourcetype=_json operation="UserLoggedIn"

```

**Threat Hunting for Email Authentication & Policy Failures:**

```spl
index=main sourcetype=_json (AuthenticationResults="*fail*" OR DeliveryAction="Quarantined" OR ActionTaken="BlockedAndNotified")
| stats count by operation, SenderAddress, Subject, DeliveryAction
| sort - count

```

---

## Skills Demonstrated

* **SIEM Administration:** Configuring local file inputs, managing data ingestion paths, and reviewing source types in Splunk.
* **Log Normalization:** Parsing unstructured JSON payloads into clean, searchable key-value fields for threat hunting.
* **Cloud Security Telemetry:** Analyzing cross-workload logs from Microsoft email gateways, authentication platforms, and DLP rulesets.

```

```
