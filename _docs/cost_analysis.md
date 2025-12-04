
# Cost Analysis: Matano's Log Ingestion vs. Modern Alternatives

## Introduction: The Matano Ingestion Model

Matano employs a serverless, event-driven architecture for log ingestion and processing within a customer's AWS account. The pipeline is triggered when logs are delivered to an Amazon S3 bucket. This event places a message onto an SQS queue, which in turn invokes an AWS Lambda function (`Transformer`) written in Rust. This function parses, normalizes (to ECS), and enriches the log data before passing it to another Lambda function (`Lake Writer`) that writes the final output to a cost-effective S3 data lake using the Apache Iceberg table format.

This design's cost structure is fundamentally based on a **pay-per-use** model. Costs are incurred only when data is actively being ingested and processed, differing significantly from traditional and other modern architectures that rely on always-on infrastructure.

## Qualitative Cost Comparison

### 1. Matano (Serverless Data Lake)

-   **Compute Costs:** Pay-per-use (AWS Lambda). No cost when idle. Performance-optimized code (Rust) minimizes execution time and cost.
-   **Storage Costs:** Utilizes Amazon S3, the most economical cloud storage. Data can be moved to cheaper tiers automatically.
-   **Licensing Costs:** None (Open Source).
-   **Operational Costs:** Minimal. The serverless model eliminates server management, and infrastructure is managed via code.

### 2. Commercial SaaS SIEMs (e.g., Splunk Cloud, Datadog)

-   **Compute & Storage Costs:** Bundled into a high-margin service fee.
-   **Licensing Costs:** The primary cost driver, typically based on daily ingestion volume (GB/day). This model scales poorly and becomes very expensive.
-   **Operational Costs:** Minimal, which is their main value proposition, but it comes at a very high price.

### 3. Self-Hosted on IaaS (e.g., ELK Stack on EC2)

-   **Compute Costs:** High. Requires a cluster of virtual machines running 24/7, which must be over-provisioned to handle peak loads.
-   **Storage Costs:** High. Requires expensive block storage (EBS) for indexed data.
-   **Licensing Costs:** The core stack is open-source, but essential security and management features require paid licenses.
-   **Operational Costs:** Extremely high. Requires significant, ongoing engineering effort to manage, scale, and maintain the cluster.

## Scenario-Based TCO Analysis

To provide a clearer financial picture, let's analyze the Total Cost of Ownership (TCO) for two common business scenarios. For this comparison, we will use a **Self-Hosted ELK Stack** as the typical alternative architecture.

**Assumptions:**
*   Costs are based on AWS `us-east-1` pricing.
*   Engineer's fully burdened salary is **$150,000/year** ($12,500/month).
*   Data is compressed 5x in the Matano data lake (S3) vs. 2x in Elasticsearch (EBS).

---

### **Scenario 1: Small-to-Medium Enterprise (SME)**
*   **Data Ingestion:** 100 GB / day
*   **Hot Storage:** 90 days

| Cost Component | Matano (Serverless) | Self-Hosted ELK (IaaS) |
| :--- | :--- | :--- |
| **Monthly Compute** | ~$25 | ~$445 (3x m5.xlarge + 1x t3.medium) |
| **Monthly Storage** | ~$45 (1.8TB on S3) | ~$400 (4.5TB on EBS gp3) |
| **Monthly Operations** | **$625** (5% of 1 Engineer) | **$3,125** (25% of 1 Engineer) |
| **Licensing** | $0 | $0 (assuming only open source features) |
| **TOTAL (Monthly)** | **~$695** | **~$3,970** |
| **TOTAL (Annual)** | **~$8,340** | **~$47,640** |

**Analysis:** For an SME, the Matano architecture is approximately **5.7 times cheaper** than running a self-managed ELK stack. The primary driver of this difference is the massive reduction in operational overhead and the shift from 24/7 compute to pay-per-use.

---

### **Scenario 2: Enterprise**
*   **Data Ingestion:** 1 TB / day
*   **Hot Storage:** 90 days

| Cost Component | Matano (Serverless) | Self-Hosted ELK (IaaS) |
| :--- | :--- | :--- |
| **Monthly Compute** | ~$300 | ~$4,000 (Reserved Instances for a large cluster) |
| **Monthly Storage** | ~$415 (18TB on S3) | (Included in compute via storage-optimized instances) |
| **Monthly Operations** | **$1,250** (10% of 1 Engineer) | **$12,500** (1 Full-Time Engineer) |
| **Licensing** | $0 | ~$3,333 (Required for enterprise features) |
| **TOTAL (Monthly)** | **~$1,965** | **~$19,833** |
| **TOTAL (Annual)** | **~$23,580** | **~$238,000** |

**Analysis:** At the enterprise scale, the savings become even more dramatic. The Matano architecture is over **10 times cheaper**. The cost of the ELK stack explodes due to the need for a dedicated full-time engineer and expensive enterprise licensing, while Matano's costs scale linearly and predictably.

## Conclusion

| **Factor** | **Matano (Serverless)** | **Commercial SaaS SIEM** | **Self-Hosted (IaaS)** |
| :--- | :--- | :--- | :--- |
| **Compute Model** | Pay-per-use (Lambda) | Bundled (High Margin) | 24/7 (EC2) |
| **Primary Storage** | Object (S3) | Bundled (Expensive) | Block (EBS) |
| **Licensing Cost** | **None** | **Very High** | High for Enterprise |
| **Operational Cost** | **Very Low** | Minimal | **Very High**|
| **Cost Scalability** | **Excellent & Linear** | Poor (Exponential) | Poor (Step-Function) |

Matano's serverless architecture provides a modern, highly cost-effective approach to log ingestion at scale. As demonstrated in the TCO analysis, it is **5-10x cheaper** than a comparable self-hosted ELK stack, with savings amplifying as data volume grows. By eliminating licensing fees, minimizing operational overhead, and leveraging the most economical cloud services, Matano allows organizations to build an enterprise-grade security data lake without the prohibitive costs of traditional architectures. This fundamentally democratizes security analytics.

---
## TCO Analysis for Alternative Platforms

The analysis above compares Matano primarily with a self-hosted ELK stack. However, it is also useful to extend this analysis to other modern alternatives discussed in the architecture TRDs, namely a port to Google Cloud Platform (GCP) or a self-hosted platform built on Apache OpenWhisk.

### Enterprise Scenario (1 TB/day) - Extended Comparison

Here we expand the Enterprise TCO analysis to include these additional platforms.

| Cost Component | Matano (AWS) | Self-Hosted ELK | **Self-Hosted OpenWhisk** | **GCP (Ported)** |
| :--- | :--- | :--- | :--- | :--- |
| **Monthly Compute** | ~$300 | ~$4,000 | ~$4,000 | ~$300 |
| **Monthly Storage** | ~$415 (18TB on S3) | (Included) | ~$1,475 (18TB MinIO on EBS) | ~$370 (18TB on GCS) |
| **Monthly Operations** | **$1,250** (10% of 1 Engineer) | **$12,500** (1 FTE) | **$18,750** (1.5 FTEs) | **$1,250** (10% of 1 Engineer) |
| **Licensing** | $0 | ~$3,333 | $0 | $0 |
| **TOTAL (Monthly)** | **~$1,965** | **~$19,833** | **~$24,225** | **~$1,920** |
| **TOTAL (Annual)** | **~$23,580** | **~$238,000** | **~$290,700** | **~$23,040** |

**Analysis:**
- **GCP:** On a recurring basis, a GCP-native architecture offers a nearly identical TCO to the AWS-native Matano architecture. Both benefit from the efficiencies of managed serverless offerings.
- **Apache OpenWhisk:** A self-hosted FaaS platform is even more expensive than a traditional ELK stack. The operational complexity of managing a full cloud-native stack (Kubernetes, Kafka, MinIO, OpenWhisk) requires significantly more human resources, making it the most expensive option.

### Porting and Migration Costs

A TCO analysis must also consider the significant one-time cost required to migrate from the existing AWS-native architecture.

*   **Porting to GCP:** This requires a complete platform re-build. The entire AWS CDK IaC must be rewritten for GCP, and all AWS SDK calls must be replaced with GCP SDKs.
    *   **Estimated One-Time Cost: ~$125,000**
*   **Porting to Self-Hosted OpenWhisk:** This involves a substantial re-architecture to replace AWS services with open-source alternatives, containerize functions, and rewrite all IaC.
    *   **Estimated One-Time Cost: ~$62,500**

**Conclusion:**
While a GCP-native architecture has a comparable long-term TCO, the prohibitive one-time migration cost makes it financially unviable. Self-hosting with OpenWhisk is both prohibitively expensive to migrate to *and* has the highest long-term TCO. This analysis reinforces that the initial choice of a deeply integrated, serverless cloud stack provides the best long-term value and creates a strong, justifiable barrier to switching platforms.
