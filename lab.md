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

- **Storage Container Name**: `testingminjdemo`
- **Sample Data**: Rename your CMS Medicare Part D CSV file to `2023.csv`.
- **Target Location**: `abfss://testingminjdemo@testingminjdemo.dfs.core.windows.net/raw_data/2023.csv`

## 3. Setup Azure ADLS Gen2 (Azure Portal)

1.  **Storage Account**: Ensure "Hierarchical namespace" is **Enabled** in the Advanced tab.
2.  **Container**: Create a container named `testingminjdemo`.
3.  **Upload**: 
    - Create a folder named `raw_data/`.
    - Upload your renamed `2023.csv` file here.
4.  **Permissions**: Assign **Storage Blob Data Reader** to your Fabric Workspace identity in IAM settings.

## 4. How to Setup Quick Install (Fabric)

To deploy the entire solution automatically, follow these steps in your Fabric Workspace:

1.  **Import Notebook**:
    - Download `install_cms_demo.ipynb` from your GitHub.
    - In your Fabric Workspace, click **Import** > **Notebook** and upload the file.

2.  **Verify Ingestion Notebook**:
    - Ensure `01-IngestFromBlob.ipynb` is also imported.
    - This notebook is already pre-configured with your storage account name and key to ensure it works immediately.

3.  **Run Quick Install**:
    - Open the `install_cms_demo.ipynb` notebook.
    - Click **Run All** in the top toolbar.
    - **What happens next?**
        - Fabric will create the Lakehouse environment.
        - It will deploy the Silver and Gold layer transformation notebooks.
        - It will create and trigger the Data Factory pipeline.
        - The pipeline will pull `2023.csv` from your blob storage and build the Star Schema.

## 5. Auto-Trigger Mechanism

- **Event Grid**: Azure Blob Storage notifies Fabric of the "Blob Created" event.
- **Immediate Action**: The pipeline starts as soon as a new file lands in `raw_data/`.
- **Status Monitoring**: You can monitor the progress in the **Monitoring Hub** of your Fabric workspace.
