# Multi-Layer Email Security & SIEM Ingestion Lab: Defender for Office 365 & Splunk

## Executive Summary
This project demonstrates the architecture and implementation of an enterprise-grade email security monitoring baseline, bridging cloud-native email telemetry from Exchange Online Protection (EOP) / Microsoft Defender for Office 365 with a Splunk SIEM instance.

---

## Lab Architecture & Workflow
* **Email Authentication:** Configured SPF, DKIM, and DMARC parameters to establish sender legitimacy and mitigate spoofing risks.
* **Cloud Telemetry Simulation:** Generated structured JSON event logs mirroring the Microsoft Management Activity API schema (such as `UserLoggedIn` audit trails) to simulate live enterprise telemetry without requiring expensive production licensing.
* **SIEM Ingestion Pipeline:** Uploaded and indexed mock logs into Splunk, mapping sourcetype properties (`_json`) to extract key fields including `ClientIP`, `operation`, and `UserId`.

---

## Sample Ingested Log Structure (`o365_mock_events.json`)
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
