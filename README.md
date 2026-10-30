# Retail Order Processing – Multi-Source Incremental Data Pipeline

**End-to-End Azure Data Engineering Project**

Azure Blob Storage | Azure Data Factory | Mapping Data Flows | Azure SQL Database | SQL | CSV | JSON | Parquet

---

## 📌 Project Overview

This project demonstrates an end-to-end Azure Data Engineering pipeline designed to consolidate retail order data from two different source systems: Store A (offline orders) and an Online Website (e-commerce orders).

The source systems provide data in different formats and structures. Store A provides order header and order item data in CSV format, while the Website provides nested JSON order data containing customer, payment, delivery, item, and promotion details.

Azure Data Factory is used to orchestrate the ingestion and processing workflow. Mapping Data Flows perform data transformation, validation, business calculations, and integration of both source systems into a common output structure.

The consolidated data is stored in Parquet format in Azure Blob Storage and loaded into Azure SQL Database using upsert operations.

The project also implements watermark-based incremental processing, SQL watermark management, and post-processing file cleanup.

## 🎯 Project Objectives

- Integrate order data from multiple source systems.
- Process structured CSV and semi-structured JSON data.
- Flatten nested JSON arrays and objects.
- Join Store A order headers with corresponding order items.
- Apply data validation and business calculations.
- Separate invalid records through error-handling branches.
- Consolidate valid records into a common output schema.
- Store transformed data in Parquet format.
- Load consolidated records into Azure SQL Database.
- Implement watermark-based incremental data processing.
- Automate post-processing file cleanup.

## 🏗️ Project Architecture

**Store A CSV Files + Website JSON Files → Azure Blob Storage → Azure Data Factory → Mapping Data Flows → Parquet → Azure SQL Database**

The pipeline processes data through the following stages:

1. **Source Data Ingestion:** Store A provides order and order-item CSV files, while the Website provides nested JSON order files.
2. **Raw Storage:** Incoming source files are stored in Azure Blob Storage.
3. **File Discovery:** Azure Data Factory Get Metadata and Filter activities identify eligible source files.
4. **Data Transformation:** Mapping Data Flows process both source systems independently.
5. **Data Quality Validation:** Apply required-field, status, data-type, quantity, and amount validations.
6. **Data Integration:** Combine valid Store A and Website records using a Union transformation.
7. **Processed Storage:** Write consolidated records in Parquet format.
8. **Azure SQL Loading:** Load processed records into Azure SQL Database using upsert behavior.
9. **Incremental Processing:** Maintain separate watermarks for Store A and Website.
10. **File Cleanup:** Delete processed source and output files after the configured SQL loading and watermark update activities succeed.

## 🛠️ Technologies Used

- Microsoft Azure
- Azure Data Factory (ADF)
- Azure Blob Storage
- Azure Mapping Data Flows
- Azure SQL Database
- SQL
- CSV
- JSON
- Parquet
- ETL/ELT
- Incremental Data Loading
- Watermark Management

## 📂 Source Data

The project integrates three source datasets:

| Source | Format | Description |
|---|---|---|
| Store A Orders | CSV | Order header, customer, billing, and payment details |
| Store A Order Items | CSV | Product, quantity, rate, and discount details |
| Website Orders | JSON | Online orders with nested customer, payment, delivery, items, and promotions |

## ⚙️ Key Project Components

### 1. Azure Data Factory Pipeline – storeaweb

The pipeline orchestrates the complete data processing workflow.

Key activities include:

- Get Metadata for source file discovery.
- Filter activities for incremental file selection.
- Matching Store A order files with corresponding item files.
- Watermark lookup and batch-end timestamp management.
- Mapping Data Flow execution.
- Azure SQL data loading.
- Stored procedure execution for watermark updates.
- Post-processing file cleanup.

### 2. Mapping Data Flow – StoreAProcessing

The Mapping Data Flow integrates three source datasets:

- SRCStoreorder
- SRCStoreorderitem
- SRCWEBDATA

**Store A Processing:**
- Reads order header and order-item CSV files.
- Joins order and item records using invoice number and file timestamp.
- Performs data type conversions and validation.
- Calculates gross amount and net amount.
- Maps valid records into the common output schema.

**Website Processing:**
- Reads nested JSON order files.
- Flattens customer, payment, delivery, item, and promotion structures.
- Aggregates promotion amounts.
- Calculates gross amount and net amount.
- Assigns delivery fees at order-line level.
- Validates required identifiers, order status, quantity, and net amount.

**Data Integration:**

The valid Store A and Website records are combined using a Union transformation and written to the processed Parquet output.

### 3. Data Quality and Error Handling

The pipeline applies validation rules to maintain data quality.

- Required invoice, order, and customer identifiers.
- Data type conversion and cast error handling.
- Store A paid-status validation.
- Website completed-status validation.
- Positive quantity and net amount validation for Website records.
- Separate error-handling branches for invalid records.

### 4. Azure Blob Storage

Azure Blob Storage is used to maintain source, processed, and rejected data.

| Folder | Purpose |
|---|---|
| storea/ | Store A order and order-item CSV files |
| website/ | Website order JSON files |
| processed/ | Consolidated Parquet output |
| Rejected_data/ | Configured rejected/error record outputs |

### 5. Azure SQL Database

The consolidated order data is loaded into Azure SQL Database.

**Final Data Table:** `dbo.storeaweborders`

**Watermark Table:** `dbo.watermark`

The Copy activity uses upsert behavior with the following key columns:

- invoice_number
- line_number
- source_channel

This allows records from Store A and Website to remain distinguishable.

### 6. Watermark and Incremental Load Implementation

The pipeline maintains separate watermark values for:

- STORE_A
- WEBSITE

The file timestamps are extracted from source filenames and compared against the stored watermark and current batch-end timestamp.

**Incremental Processing Logic:**

`last_processed_time < source_file_timestamp <= batch_end_time`

After successful SQL loading, stored procedure activities update the corresponding watermark records.

### 7. Cleanup Implementation

The pipeline includes automated cleanup activities.

- Deletes processed Store A order CSV files.
- Deletes corresponding Store A order-item CSV files.
- Deletes processed Website JSON files.
- Deletes files from the processed output folder after the configured dependencies succeed.

This ensures cleanup occurs after the SQL loading and watermark update stages.

## 📊 Project Outcomes

- Designed a multi-source Azure Data Engineering pipeline.
- Integrated structured CSV and nested JSON data.
- Developed transformations using Azure Mapping Data Flows.
- Implemented data validation and business calculations.
- Standardized different source structures into a common output schema.
- Configured Parquet-based intermediate storage.
- Implemented Azure SQL upsert operations.
- Designed watermark-based incremental processing.
- Automated post-processing cleanup using Azure Data Factory.

## 📸 Screenshots and Evidence

The repository includes implementation evidence covering:

- Overall project architecture.
- Azure Blob Storage source folder structure.
- Azure Data Factory pipeline configuration.
- Mapping Data Flow transformations.
- Store A CSV and Website JSON source data.
- Processed Parquet output.
- Rejected/error data configuration.
- Azure SQL final table.
- Watermark table and incremental processing.
- Pipeline execution and cleanup activities.

## 👨‍💻 Author

**Dhilip Kumar**

Azure Data Engineering | Azure Data Factory | SQL | Python | PySpark

[LinkedIn](https://www.linkedin.com/in/dhilipkumar20) | [GitHub](https://github.com/imdhilipkumar)

---

*This project was developed for hands-on learning and portfolio demonstration of Azure Data Engineering concepts.*
