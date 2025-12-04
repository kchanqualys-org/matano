
# TRD: AWS Lambda in Matano

## 1. Overview and Objective

This document provides the technical requirements and design for the AWS Lambda functions that constitute the core compute layer of the Matano platform. The primary objective of using AWS Lambda is to create a **serverless, event-driven, and highly scalable** architecture. This approach minimizes operational overhead and adheres to a pay-per-use cost model, which is fundamental to Matano's value proposition.

...

---

## Appendix: Alternative - Apache OpenWhisk

...

### Conclusion

Apache OpenWhisk is a powerful platform, but the TCO and migration cost analyses confirm that self-hosting it is contrary to the goals of Matano. The financial and operational burden of managing the underlying infrastructure is significant.

For the Matano project, where the primary goal is to provide a scalable, cost-effective security analytics platform *without* burdening the user with infrastructure management, AWS Lambda is the unequivocally superior choice. Its deep integration with the AWS ecosystem and its true pay-per-use, zero-ops model allow Matano to focus entirely on its core mission: processing data and finding threats.

---

## Appendix: Alternative - Google Cloud Platform (GCP)

An alternative to self-hosting an open-source solution is to port the architecture to another major cloud provider, such as Google Cloud Platform (GCP). This would involve replacing the AWS-native services with their GCP equivalents.

### Component Mapping: AWS to GCP

| AWS Service | GCP Equivalent | Analysis |
| :--- | :--- | :--- |
| AWS Lambda | Google Cloud Functions | Both are mature, managed FaaS platforms with similar pay-per-use pricing. |
| Amazon S3 | Google Cloud Storage (GCS) | Both are scalable, cost-effective object storage services. |
| Amazon SQS | Google Cloud Pub/Sub | Both are managed messaging services suitable for event-driven architectures. |
| Amazon Athena | Google BigQuery | Both are powerful serverless query engines. BigQuery is a strong competitor to Athena. |
| AWS CDK | Terraform / Deployment Manager| The entire infrastructure-as-code base would need to be rewritten from scratch. |
| AWS IAM | Google Cloud IAM | Both provide granular identity and access management. |

### TCO and Porting Cost Analysis

Unlike the comparison with a self-hosted platform, the ongoing operational costs between an AWS-native and a GCP-native serverless architecture are roughly comparable. Both providers have similar pricing models for their respective services. The real cost is the **one-time migration cost**.

**Key Migration Tasks:**

1.  **Complete IaC Rewrite:** The entire AWS CDK project is useless in a GCP environment. The entire infrastructure, from object storage buckets to function definitions and IAM policies, would need to be re-written in a cloud-agnostic tool like Terraform. This is the most significant part of the effort.
2.  **Full SDK and API Replacement:** Every line of code that interacts with an AWS service via the AWS SDK must be rewritten to use the corresponding GCP SDK. This affects data ingestion, processing, and alerting.
3.  **Service-Specific Logic:** The logic for parsing triggers from SQS would need to be adapted for Pub/Sub. The SQL dialects for Athena and BigQuery have differences that would require rewriting all detection queries.
4.  **Testing and Validation:** A full suite of integration and end-to-end tests would be necessary to validate that the ported architecture is functioning identically to the original.

**Estimated Cost:**

This is a complete platform re-build on a new cloud. Assuming a team of two senior engineers:

*   **Total Estimated Effort:** 16-24 weeks
*   **One-Time Porting Cost:** (2 Engineers * 20 weeks * $3,125/week) = **~$125,000**

### Conclusion

A GCP-native version of the Matano architecture is technically feasible and would likely have a similar operational cost profile to the current AWS-based platform. However, the business case for such a migration is extremely weak.

The project would incur a **prohibitive one-time cost of over $125,000** and halt all new feature development for at least a full quarter, with no tangible benefit to the end-user or improvement in the TCO. This analysis confirms that the initial choice of a cloud provider for a deeply integrated, serverless application creates a significant (and appropriate) incentive to remain within that ecosystem. The cost of switching is not in the services themselves, but in the engineering effort required to re-build the connective tissue between them.
