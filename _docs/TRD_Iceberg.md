
# TRD: Open Standards (Apache Iceberg) in Matano

## 1. Overview and Objective

This document provides the technical requirements and strategic rationale for Matano's use of open standards, specifically **Apache Iceberg**, as the foundation for its security data lake. The primary objective is to provide users with **true data ownership and control**, fundamentally differentiating Matano from traditional SIEMs that lock data into proprietary formats.

By building on open standards, Matano ensures that a user's security data is stored in a transparent, portable, and future-proof format within their own cloud environment. This aligns directly with the core user job of avoiding vendor lock-in and creating a flexible analytics platform.

## 2. Why Apache Iceberg?

While simply storing data in an open format like Parquet is a good first step, it is insufficient for a robust, enterprise-grade data lake. Matano uses Apache Iceberg, an open table format for huge analytic datasets, for several critical reasons:

1.  **ACID Transactions:** Iceberg brings transactional integrity to the data lake. This ensures that data operations (like the `LakeWriter` function writing new batches) are atomic, consistent, and isolated. It prevents data corruption and guarantees that queries always see a consistent state of the data.
2.  **Schema Evolution:** Security log sources change over time. Iceberg provides robust support for schema evolution, allowing new fields to be added or altered without requiring a complete rewrite of the entire dataset. This is crucial for the long-term maintainability of the data lake.
3.  **Time Travel and Auditing:** Iceberg maintains a versioned manifest of all data files. This enables powerful "time travel" queries, allowing users to query the state of their data at a specific point in time. This is invaluable for incident investigation and compliance auditing.
4.  **Performance Optimization:** Iceberg tracks table metadata and statistics about the data files. This allows query engines like Amazon Athena to perform partition pruning and skip reading irrelevant files, leading to significantly faster queries and lower costs.

## 3. Key Use Cases Enabled by Iceberg

The choice of Iceberg is not merely technical; it directly enables critical use cases for the Security Engineer:

*   **Incident Investigation with Historical Context:** A security team can use Iceberg's time travel capabilities to query the data lake *as it was* at the time of a suspected breach, even if the data has since been updated or corrected. This ensures that investigations are based on accurate historical fact.
*   **Compliance and Reproducibility:** For compliance audits, an organization can prove the exact state of its data at any point in time. Detection logic can be re-run against historical snapshots to validate findings or test new rules on old data.
*   **"Bring Your Own Analytics":** Because the data is in an open format within the user's account, they are not limited to Matano's tools. They can point other analytics engines, machine learning frameworks (e.g., Spark), or business intelligence tools directly at their security data without a complex export/ETL process.

## 4. Pros and Cons

| Pros | Cons |
| :--- | :--- |
| **Complete Data Ownership:** User has direct access to their data in their own S3 bucket. | **Increased Complexity:** Managing table metadata introduces more complexity than simply writing Parquet files to a directory. |
| **No Vendor Lock-In:** Data is in an open, portable format that can be read by a wide ecosystem of tools. | **Dependency on Open Source:** The project's health is tied to the continued development and support of the Apache Iceberg project. |
| **Future-Proofs Data:** The data lake can outlive any specific tool or vendor, ensuring long-term value. | **Potential for Misconfiguration:** An incorrectly managed Iceberg table can lead to performance issues (e.g., too many small files). |
| **Enables Advanced Analytics:** Features like time travel and schema evolution are critical for mature security analytics. | |

## 5. Conclusion

The decision to build Matano on Apache Iceberg is a core strategic choice. While it introduces a layer of complexity compared to simpler storage methods, the benefits are overwhelming and directly address the primary pain points of traditional SIEM users.

As stated in the architecture overview, Matano's key innovation is the **"fusion of open-source data standards with a serverless operational model."** Apache Iceberg provides the robust, open standard, while the serverless `LakeWriter` function abstracts away the complexity of managing it. This combination delivers the power and ownership of a modern data platform without the operational overhead, giving users the best of both worlds.
