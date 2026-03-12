# Lab: Fabric Data Ingestion from ADLS Gen2 with Auto-Trigger

This lab guide explains how to set up an automated data ingestion pipeline in Microsoft Fabric that triggers whenever new data is uploaded to your Azure Data Lake Storage (ADLS) Gen2 account.

## 1. Architecture Diagram

```text
+-------------------------+       +----------------------------+       +-------------------------+
|  Azure Data Lake Gen2   | ----> |  Fabric Storage Event      | ----> |  Fabric Data Factory    |
|  (Account: testingminjdemo) |   |  Trigger (Auto-Trigger)    |       |  Pipeline (Ingestion)   |
+-----------+-------------+       +-------------+--------------+       +------------+------------+
            |                                   |                                   |
            | (New File Uploaded)               | (Starts Pipeline)                 | (Loads Data)
            v                                   v                                   v
    [ raw_data/2023.csv ]               [ Trigger Event ]                   [ Fabric Lakehouse ]
```

## 2. Prerequisites & Sample Data

- **Sample Data Download**: [CMS Medicare Part D Prescribers (2023)](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider-and-drug)
- **File Name**: Download the CSV file and rename it to `2023.csv`.
- **Storage Container Name**: `testingminjdemo`
- **Target Location**: `abfss://testingminjdemo@testingminjdemo.dfs.core.windows.net/raw_data/2023.csv`

## 3. Steps to Create Azure Blob Storage Gen2 (Azure UI)

1.  **Search for Storage Accounts**: Log in to the Azure Portal and search for **Storage Accounts**.
2.  **Create New Account**:
    - **Basics**: Select your Subscription, Resource Group, and Name (e.g., `testingminjdemo`).
    - **Advanced**: Ensure **Enable hierarchical namespace** is **Checked** (this makes it Gen2).
    - **Review + Create**: Click create and wait for deployment.
3.  **Create Container**:
    - Go to your storage account, click **Data storage** > **Containers**.
    - Click **+ Container** and name it `testingminjdemo`.
4.  **Upload Sample Data**:
    - Inside the container, click **+ Folder** and name it `raw_data`.
    - Open `raw_data`, click **Upload**, and select your `2023.csv` file.
5.  **Get Access Key**:
    - Go to **Security + networking** > **Access keys**.
    - Copy **Key 1** (this is already configured in your ingestion notebook).

## 4. Quick Install Demo Setup (Fabric)

This solution uses the following files from your GitHub repository `minj1992/medicalfabric`:

- **install_cms_demo.ipynb**: The main automation notebook that creates your workspace artifacts.
- **create_ingestion_pipeline.ipynb**: Automates the creation of the Data Factory pipeline.
- **01-IngestFromBlob.ipynb**: The core ingestion logic using your `testingminjdemo` account.

**Steps to Run**:

1.  **Import & Open**: Import `install_cms_demo.ipynb` into your Fabric Workspace.
2.  **Configuration**: Update the `storage_account_key` in the configuration cell if you regenerated it.
3.  **Run All**: Click **Run All**. It will:
    - Create a Lakehouse named `cms_lakehouse`.
    - Import all 3 notebooks from your GitHub.
    - Create a Data Pipeline `cms_ingestion_pipeline`.
    - Trigger the pipeline to start data loading.

## 5. Auto-Trigger Configuration

To make the pipeline trigger automatically when you update data in Blob Storage:

1.  **Open Pipeline**: Open `cms_ingestion_pipeline` in Fabric.
2.  **Add Trigger**: Click **Trigger** > **New/Edit** > **New**.
3.  **Event-Based Trigger**:
    - **Type**: Select **Storage events**.
    - **Storage Account**: Select your Azure subscription and `testingminjdemo` storage account.
    - **Container**: `testingminjdemo`.
    - **Blob path begins with**: `raw_data/`.
    - **Blob path ends with**: `.csv`.
    - **Event**: Select **Blob created**.
4.  **Publish**: Click **OK** and then **Publish** the pipeline. Now, any file upload to `raw_data/` will start the ingestion.
