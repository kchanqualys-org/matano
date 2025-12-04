
# Product Requirements Document: Matano

## 1. Document Overview
This document provides a detailed overview of the Matano product, a security analytics platform. The information herein is derived from an analysis of the Matano source code. The purpose of this PRD is to outline the product's objectives, features, and technical specifications to guide development and strategic planning.

## 2. Objective
The primary objective of Matano is to provide a powerful, scalable, and open-source security analytics platform. It enables security teams to ingest logs from various sources, normalize and enrich the data, run detections as code, and analyze security events in a cloud-native environment, built on a foundation of a data lake.

## 3. Scope
### In Scope
*   **Log Ingestion & Processing:** Ingesting logs from a variety of sources (e.g., AWS services, SaaS applications). The system transforms raw logs into a structured format based on the Elastic Common Schema (ECS).
*   **Data Lake:** Storing normalized log data in an Apache Iceberg-based data lake on Amazon S3.
*   **Detections as Code:** Writing security detections in Python. The system runs these detections against the ingested log data.
*   **Enrichment:** Enriching log data with contextual information to aid in analysis.
*   **Alerting:** Generating alerts from detection matches and forwarding them to external systems (e.g., Slack).
*   **Infrastructure as Code:** Managing the entire platform's infrastructure on AWS using the AWS CDK.
*   **Command Line Interface (CLI):** Providing a comprehensive CLI for developers and security engineers to create, manage, and deploy their Matano projects.

### Out of Scope (Assumed)
*   **Graphical User Interface (GUI):** This PRD assumes there is no dedicated web-based GUI for analysis or platform management. Interaction is primarily through the CLI and direct queries on the data lake (e.g., using Amazon Athena).
*   **Real-time Threat Intelligence Integration:** While there is an enrichment capability, a fully-featured, real-time threat intelligence feed integration is assumed to be out of scope unless manually configured.
*   **Managed Deployment:** The user is responsible for deploying and managing the Matano instance within their own AWS account.

## 4. User Personas and Use Cases
### Personas
*   **Security Engineer:** Responsible for protecting the organization's cloud environment. They use Matano to write and tune detection rules, investigate security incidents, and manage log sources.
*   **DevOps/Platform Engineer:** Responsible for deploying and maintaining the infrastructure. They use Matano's IaC capabilities to manage the platform as part of their existing cloud environment.

### Use Cases
*   **Use Case 1: Initializing a new Matano Project**
    1.  A Security Engineer runs `matano init` via the CLI.
    2.  The system scaffolds a new directory with a default configuration (`matano.config.yml`) and example folders for `detections` and `log_sources`.
*   **Use Case 2: Adding a new Detection Rule**
    1.  The Security Engineer creates a new sub-directory under the `detections` folder.
    2.  Inside, they create a `detection.yml` file to define metadata for the detection (e.g., severity, description).
    3.  They write the detection logic in a `detect.py` file.
    4.  They run `matano deploy` to deploy the new detection to their Matano instance.
*   **Use Case 3: Onboarding a new Log Source**
    1.  The Security Engineer creates a new `log_source.yml` file, defining the schema and parsing rules for the new source.
    2.  They deploy the changes using `matano deploy`.
    3.  Matano begins ingesting and processing data from the new log source.

## 5. Functional Requirements
*   **FR-1: CLI-based Project Management**
    -   **Description:** The system must provide a CLI to initialize, configure, and deploy Matano projects.
    -   **Input:** Commands like `init`, `deploy`, `diff`, `destroy`.
    -   **Output:** A scaffolded project directory or a deployed/updated Matano instance in AWS.
*   **FR-2: Python-based Detections**
    -   **Description:** Users must be able to write detection logic in Python.
    -   **Input:** A Python script (`detect.py`) that processes log data.
    -   **Output:** Detections that trigger alerts.
*   **FR-3: Configurable Log Sources**
    -   **Description:** The system must allow users to define new log sources using YAML configuration files.
    -   **Input:** A `log_source.yml` file specifying parsing and schema information.
    -   **Output:** Processed and normalized log data stored in the data lake.
*   **FR-4: Data Normalization to ECS**
    -   **Description:** All ingested data must be normalized to the Elastic Common Schema (ECS).
    -   **Constraint:** Based on the ECS Iceberg schema found in the codebase.
*   **FR-5: Alert Forwarding**
    -   **Description:** The system must be able to forward alerts to external systems.
    -   **Inference:** Inferred from the presence of `alert_forwarder` code and assets like `matano_slack_alert.png`.

## 6. Non-Functional Requirements
*   **NFR-1: Scalability:**
    -   The system is designed to be highly scalable, leveraging serverless AWS services and a data lake architecture that can handle large volumes of log data. (Inferred from architecture).
*   **NFR-2: Maintainability:**
    -   The entire platform is defined as code (CDK), promoting maintainability and version control. The modular, multi-language structure in the `lib/` directory allows for independent maintenance of components.
*   **NFR-3: Security:**
    -   As a security product, the system itself must be secure. The presence of a `SECURITY.md` file and use of modern technologies suggest security is a key consideration.
*   **NFR-4: Performance:**
    -   Performance-critical components, such as log processing, appear to be written in Rust to ensure high throughput and low latency. (Inferred from `lib/rust` contents).

## 7. Technical Specifications
*   **Technology Stack:**
    -   **CLI:** NodeJS, TypeScript (packaged with `vercel/pkg`).
    -   **Infrastructure:** AWS CDK (TypeScript).
    -   **Application Logic:** Rust, Python, Java.
    -   **Data Storage:** Apache Iceberg, Apache Parquet, Apache Avro, Amazon S3.
    -   **Data Processing/Querying:** Amazon Athena, VRL (Vector Remap Language).
*   **Architecture (Inferred):**
    -   Matano employs a serverless, microservices-based architecture running on AWS.
    -   **Data Ingestion:** Log sources deliver data into the system, likely via S3 or SQS.
    -   **Transformer:** A component (likely a Rust function) processes and normalizes the raw logs.
    -   **Lake Writer:** A component writes the normalized data into the Iceberg data lake on S3.
    -   **Detections:** Python functions run on a schedule or trigger to analyze the data and generate findings.
    -   **Alerting:** An `alert_forwarder` component sends alerts to configured destinations.
*   **Key Components:**
    -   `cli/`: The command-line interface.
    -   `infra/`: The AWS CDK project defining all infrastructure.
    -   `lib/`: Core application logic, with subdirectories for Rust, Python, and Java components.
    -   `data/`: Contains schema definitions and configurations for managed sources.

## 8. Risks and Assumptions
*   **Risks:**
    -   **Complexity:** The multi-language, distributed nature of the project can be complex to manage and contribute to.
    -   **AWS Lock-in:** The architecture is tightly coupled with AWS services, which may be a risk for organizations with multi-cloud strategies.
*   **Assumptions:**
    -   Users have sufficient AWS knowledge to deploy and manage the platform.
    -   Analysis and querying are primarily performed using Amazon Athena, not a built-in UI.
    -   The primary users are technical (Security/DevOps Engineers) comfortable with CLI and "as-code" workflows.

## 9. Dependencies
*   **Cloud Provider:** Amazon Web Services (AWS).
*   **Key AWS Services (Inferred):** S3, SQS, Lambda, Athena, IAM, Step Functions, DynamoDB.
*   **Open Source Libraries/Frameworks:** Apache Iceberg, Apache Arrow, AWS CDK, NodeJS, Python, Rust, Java, Vercel Pkg.

## 10. Timeline and Milestones
This information is not available from the source code.

## 11. Appendix
For further details, refer to the source code and the `DEVELOPMENT.md` file within the repository.
