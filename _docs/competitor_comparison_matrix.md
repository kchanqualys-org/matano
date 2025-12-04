
# Matano Competitor Comparison Matrix

This document provides a high-level comparison matrix between Matano and other major players in the SIEM and security analytics market.

| Feature | Matano | Panther | Elastic SIEM | Splunk | Google Chronicle SIEM |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Model** | **Open-source** | Commercial (SaaS) | Open-source core, Commercial (SaaS/Self-managed) | Commercial (SaaS/Self-managed) | Commercial (SaaS) |
| **Hosting** | **Self-hosted (in user's AWS account)** | Fully Managed SaaS | SaaS or Self-hosted | SaaS or Self-hosted | Fully Managed SaaS (GCP) |
| **Detections as Code**| **Core Philosophy (Python)** | Core Philosophy (Python) | Yes (via API/Kibana) | Partially (via configs) | Yes (YARA-L) |
| **Data Normalization**| **Elastic Common Schema (ECS)** | Proprietary models | Elastic Common Schema (ECS) | Common Information Model (CIM) | Unified Data Model (UDM) |
| **Data Lake Tech** | **Apache Iceberg, Parquet** | Proprietary | Proprietary (Lucene) | Proprietary (Indexers) | Proprietary |
| **Data Ownership** | **Full ownership in user's account** | Vendor-controlled | Full ownership (if self-hosted) | Vendor-controlled (SaaS) or Self-managed | Vendor-controlled |
| **Primary Audience**| Cloud-native teams, DevOps-focused security engineers | Security teams wanting a managed detection platform | Teams invested in the Elastic ecosystem | Enterprise SOCs, IT operations | Enterprises on GCP, threat hunters |

## Summary

- **Matano** stands out by being a fully open-source solution that runs in your own AWS account. This provides maximum data ownership and prevents vendor lock-in. Its use of open standards like ECS and Apache Iceberg is a key differentiator from more proprietary systems.

- **Panther** is the most similar in philosophy, with a strong focus on "detections as code." However, it is a commercial, fully-managed SaaS platform, which abstracts away the infrastructure but cedes some data control.

- **Elastic SIEM** is a strong competitor, especially for organizations already using the Elastic Stack. It shares a commitment to ECS but uses its own proprietary data store (Lucene). It offers both self-hosted and SaaS options.

- **Splunk** is the traditional market leader. While incredibly powerful, it is a more monolithic and proprietary system. Its "detections as code" capabilities are less integrated than in modern platforms.

- **Google Chronicle SIEM** is a cloud-native SaaS offering from a major cloud provider. It is built for massive scale but operates as a "black box," with less transparency and control over the underlying data structures compared to Matano.
