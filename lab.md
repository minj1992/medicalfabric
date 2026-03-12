# Lab: Fabric Data Ingestion from ADLS Gen2 with Auto-Trigger

This lab guide explains how to set up an automated data ingestion pipeline in Microsoft Fabric that triggers whenever new data is uploaded to an Azure Data Lake Storage (ADLS) Gen2 account.

## Architecture Diagram

```text
+-------------------------+       +----------------------------+       +-------------------------+
|  Azure Data Lake Gen2   | ----> |  Fabric Storage Event      | ----> |  Fabric Data Factory    |
|  (Blob Storage Source)  |       |  Trigger (Auto-Trigger)    |       |  Pipeline (Ingestion)   |
+-----------+-------------+       +-------------+--------------+       +------------+------------+
            |                                   |                                   |
            | (New File Uploaded)               | (Starts Pipeline)                 | (Loads Data)
            v                                   v                                   v
    [ raw_data/*.csv ]                  [ Trigger Event ]                   [ Fabric Lakehouse ]
```

## 1. Prerequisites & Sample Data

The project uses the **CMS Medicare Part D Prescribers** dataset.

- **Storage Container Name**: `testingminjdemo`
- **Data Source Link**: [CMS Medicare Part D Dataset](https://data.cms.gov/provider-summary-by-type-of-service/medicare-part-d-prescribers/medicare-part-d-prescribers-by-provider-and-drug)
- **Direct Metadata API**: `https://data.cms.gov/data.json`

## 2. Setup Azure ADLS Gen2 in Azure Portal

1.  **Create Storage Account**:
    - Sign in to [Azure Portal](https://portal.azure.com).
    - Search for **Storage accounts** and click **Create**.
    - Select your Subscription and Resource Group.
    - Provide a unique **Storage account name**.
    - **Crucial**: Go to the **Advanced** tab and check **Enable hierarchical namespace** (this turns Blob Storage into ADLS Gen2).
    - Click **Review + create** then **Create**.

2.  **Create Container**:
    - Go to your new Storage Account resource.
    - Select **Containers** under Data storage.
    - Click **+ Container**, name it `testingminjdemo`, and set Access level to `Private`.

3.  **Upload Data**:
    - Inside the container, create a folder named `raw_data/`.
    - Rename your downloaded CSV to `2023.csv`.
    - Upload `2023.csv` to the `raw_data/` folder.

## 3. Fully Automated Quick Install

To use the updated **Quick Install** that pulls from your Blob Storage:

1.  **Configure Permissions**:
    - Assign the **Storage Blob Data Reader** role to your **Fabric Workspace Identity** or your own account in the Azure Storage IAM settings.

2.  **Update Ingestion Notebook**:
    - Open `01-IngestFromBlob.ipynb` in your Fabric Workspace.
    - Set the `storage_account_name` variable in the first code cell to your Azure Storage Account name.

3.  **Run Quick Install**:
    - Run the `install_cms_demo.ipynb` notebook. It will now:
        - Create the Lakehouse.
        - Import the notebooks (including the new Blob Ingestion one).
        - Create a Pipeline that triggers the Blob Ingestion, followed by the Silver and Gold layer processing.

## 4. How Auto-Trigger Works

- **Event Grid Integration**: Azure Blob Storage (Gen2) uses Azure Event Grid to notify Fabric when a "Blob Created" event occurs.
- **Immediate Execution**: As soon as a file is uploaded to the specified container/path, Fabric receives the event and starts the pipeline.
- **Incremental Loading**: You can configure the pipeline to only pick up the file that triggered the event by using pipeline parameters (e.g., `@trigger().outputs.body.fileName`).
