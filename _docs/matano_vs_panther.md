
# Matano vs. Panther: A Comparison

This document provides a comparison between Matano and Panther, two modern security platforms that treat detections as code.

## Overview

**Matano** is an open-source, serverless security data platform. It is designed to be deployed in your own AWS account, giving you full control over your data and infrastructure. Matano normalizes all log sources to the Elastic Common Schema (ECS) and stores data in an Apache Iceberg-based security data lake. Its primary goal is to provide a flexible, cost-effective, and open platform for security analytics at scale.

**Panther** is a commercial security analytics platform that also emphasizes a "detections as code" workflow. It is a fully managed SaaS solution designed for security teams to build high-fidelity alerts. Panther also uses Python for writing detection logic and focuses on structuring security data to make it easily searchable and analyzable.

## Key Differences

| Feature | Matano | Panther |
|---|---|---|
| **Core Model** | Open-source, self-hosted | Commercial SaaS with a free-tier |
| **Data Normalization** | Natively normalizes all data to Elastic Common Schema (ECS) | Uses its own data models, can be mapped to others |
| **Data Storage** | Apache Iceberg in your AWS account | Data stored and managed by Panther |
| **Deployment** | Deployed into your own AWS account | Fully managed SaaS platform |
| **Focus** | Providing an open, flexible, and cost-effective platform | Providing a polished, enterprise-ready solution |

## Detailed Comparison

### Philosophy and Architecture

Both Matano and Panther are built on the idea that writing detections as code (specifically Python) is the most effective way to build a modern security monitoring program. This allows security teams to use software engineering best practices like version control, code review, and automated testing.

*   **Matano** takes an open-source, "bring your own cloud" approach. It is deployed directly into your AWS environment, which means your data never leaves your control. By building on open standards like ECS and Apache Iceberg, Matano aims to prevent vendor lock-in and provide maximum flexibility. This makes it a powerful choice for teams that want deep control and customization.

*   **Panther** offers a more managed, out-of-the-box experience. As a commercial SaaS product, it handles all the infrastructure and data management, allowing teams to focus purely on writing detections and analyzing data. This is ideal for organizations that want a powerful solution without the overhead of managing the underlying infrastructure.

### Data Normalization and Storage

How a platform structures data is critical for effective detection and response.

*   **Matano** is built around the **Elastic Common Schema (ECS)**. It automatically parses and normalizes data from all sources into this standard format. This provides a unified way to query and write detections across disparate log types. The data is stored in your own S3-based data lake using Apache Iceberg, an open table format for huge analytic datasets.

*   **Panther** uses its own internal data models but provides tools for mapping incoming data to these models. While it also supports standardizing data, the native format is specific to Panther. This provides a highly optimized experience within the Panther ecosystem.

### Use Cases and Target Audience

*   **Choose Matano** if you are looking for an open-source, highly customizable security data platform that you can run in your own cloud environment. It is particularly well-suited for organizations with a strong engineering culture that want to avoid vendor lock-in and have fine-grained control over their security data and costs.

*   **Choose Panther** if you are looking for a commercial, fully-managed security analytics solution with a polished user experience and enterprise support. It is an excellent choice for teams that want to get up and running quickly with a powerful detection-as-code platform without managing infrastructure.

## Conclusion

Both Matano and Panther represent the future of security analytics, moving away from traditional black-box SIEMs toward a more open, code-driven approach. The choice between them largely depends on your organization's philosophy regarding open-source vs. commercial software, and whether you prefer to manage your own cloud infrastructure or use a fully managed SaaS solution.
