---
description: Repository Information Overview
alwaysApply: true
---

# Repository Information Overview

## Repository Summary
This repository contains end-to-end Microsoft Fabric healthcare samples demonstrating data engineering, integration, and BI capabilities using public healthcare datasets. It features the Medallion architecture (Bronze, Silver, Gold layers) and integrates generative AI features like Fabric Data Agents and Power BI Copilot.

## Repository Structure
The repository is organized into two primary end-to-end tutorial samples:

- **analytics-bi-directlake-starschema**: Medicare Part D Prescribers dataset sample demonstrating Power BI Direct Lake mode with 275M+ records.
- **analytics-bi-largecomplex-jsondataset**: OpenFDA Drug Adverse Events dataset sample focusing on large-scale JSON ingestion (400+ GB) and flattening.

### Main Repository Components
- **Notebooks**: PySpark notebooks for data ingestion and transformation.
- **Pipelines**: Data Factory pipeline definitions for orchestration.
- **SQL Scripts**: Stored procedures and table definitions for Fabric Warehouse/SQL endpoints.
- **Power BI Assets**: Semantic models and `.pbix` report templates.
- **Documentation**: Detailed guides for quick and manual setup.

## Projects

### Analytics - BI with Direct Lake Star Schema
**Configuration**: Microsoft Fabric Workspace artifacts (Notebooks, Pipelines, Semantic Model)

#### Language & Runtime
**Language**: Python (PySpark), SQL, DAX  
**Runtime**: Microsoft Fabric Spark Runtime  
**Tools**: Microsoft Fabric, Power BI Desktop, Semantic Link (`sempy`)

#### Key Resources
- **install_cms_demo.ipynb**: Automation notebook for quick environment deployment.
- **01-DownloadCMSDataCsvFiles.ipynb**: Notebook to ingest raw Medicare Part D CSV data.
- **02-CreateCMSDataTable.ipynb**: Notebook for Silver layer data processing.
- **03-CreateCMSStarSchemaTables.ipynb**: Notebook for Gold layer star schema creation.
- **CMS Medicare Part D Star Schema Template.pbix**: Power BI report template.

#### Usage & Operations
**Quick Setup**:
1. Import `install_cms_demo.ipynb` into a Fabric Workspace.
2. Run all cells to deploy Lakehouse, Notebooks, Pipelines, and Semantic Model.
3. The notebook triggers a Data Factory pipeline asynchronously (takes 20-45 minutes).

**Manual Setup**:
Follow instructions in `manual-setup.md` for step-by-step creation of Lakehouse/Warehouse and Semantic Model.

---

### Analytics - BI over Large Complex JSON Dataset
**Configuration**: Microsoft Fabric Workspace artifacts

#### Language & Runtime
**Language**: Python (PySpark), SQL, Power Query (M)  
**Runtime**: Microsoft Fabric Spark Runtime  
**Tools**: Microsoft Fabric, Data Factory, Power BI

#### Key Resources
- **01-FDA-Download-DrugEvent-Dataset.ipynb**: Notebook for large-scale JSON ingestion from OpenFDA.
- **02-FDA-DrugEvent-CreateSilverTables.ipynb**: Notebook for flattening complex JSON into relational Delta tables.
- **03-SQL-CreateWarehouseTable.sql**: SQL script for defining Gold layer tables in Fabric Warehouse.
- **DateTable.m**: Power Query script for date dimension generation.

#### Usage & Operations
**Setup Process**:
1. Create a Fabric Lakehouse and import Spark Notebooks.
2. Create and run a Data Factory Pipeline to ingest 1400+ JSON files (400+ GB).
3. Create a Fabric Warehouse for the Gold layer (Data Mart).
4. Build a Fabric Direct Lake Semantic Model for Power BI reporting.

#### Validation
**Verification Approach**:
- Monitor Data Factory pipeline execution via "View Run History" or Fabric Monitor Hub.
- Validate data ingestion by querying Silver/Gold layer tables via Lakehouse SQL Endpoint.
- Verify Power BI report connectivity using Direct Lake mode.
