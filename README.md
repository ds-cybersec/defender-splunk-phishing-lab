# Multi-Layer Email Security & SIEM Ingestion Lab: Defender for Office 365 & Splunk

## Executive Summary
This project demonstrates the architecture and implementation of an enterprise-grade email security monitoring baseline. It bridges cloud-native email telemetry from Exchange Online Protection (EOP) / Microsoft Defender for Office 365 with an on-premises Splunk SIEM instance.

## Key Components & Implementation
1. **Email Authentication Baseline:** Configured SPF, DKIM, and DMARC parameters to establish sender legitimacy and mitigate spoofing risks.
2. **Telemetry Ingestion:** Mapped and ingested Exchange Online / M365 Management Activity API logs into Splunk for centralized indexing.
3. **Data Normalization:** Applied field extractions to correlate sender IP addresses, recipient domains, and delivery action statuses.

## Sample SPL Queries
```spl
index="o365_security" sourcetype="o365:message:trace" 
| eval sender_domain=mvindex(split(SenderAddress, "@"), 1)
| where sender_domain != "yourdomain.com"
| stats count as total_emails, values(Subject) as subjects by SenderAddress, RecipientAddress
| sort - total_emails
