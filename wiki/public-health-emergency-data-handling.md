---
confidence: medium
created: '2026-04-16'
orphan: false
sources: []
status: active
tags:
- guidance
- protocol
- security
- privacy
title: Guidance 003 Pandemic Response Data
---

# Guidance 003 Pandemic Response Data

Guidance Document: GUIDANCE-003 
Title: Data Handling for Public Health Emergency Response 
Version: 1.0 
Date: March 5, 2026 
 
1.0 Introduction 
This document provides guidance on the secure and timely handling of data during a declared 
public health emergency, such as a novel influenza pandemic. It complements existing data 
governance SOPs with specific protocols for rapid response scenarios. 
 
2.0 Data Classification 
During a pandemic response, all incoming patient and case data is to be immediately classified 
as "High Sensitivity" and is subject to all applicable privacy and security controls, including 
HIPAA. Data must be encrypted both at rest and in transit. 
 
3.0 Data Ingestion and Processing 
Data ingestion pipelines must prioritize speed and reliability. The preferred platform for initial 
data staging is the secure Azure Data Lake Storage (ADLS) environment. Processing should be 
conducted using approved tools like Azure Databricks or Azure Synapse. Direct analysis on raw 
data stores is discouraged; use curated and de-identified datasets where possible. 
 
4.0 Data Sharing with Partners 
Sharing data with external public health partners (e.g., state health departments, WHO) must 
be done through the approved Palantir data-sharing platform. Direct data transfers via email or 
unapproved file-sharing services are prohibited. All data sharing agreements must be logged 
with the Office of the General Counsel. 
 
5.0 System Decommissioning 
Post-emergency, all temporary systems and data stores created for the pandemic response must 
be formally decommissioned according to SOP-018 (System Decommissioning). An after-action 
report detailing data lineage and final disposition is required within 90 days of the emergency 
declaration being lifted. 