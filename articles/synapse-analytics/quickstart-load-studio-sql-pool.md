---
title: 'Quickstart: Bulk load data with dedicated SQL pool'
description: Use the Synapse Studio to bulk load data into dedicated SQL pool in Azure Synapse Analytics.
services: synapse-analytics
author: kevinvngo
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: quickstart
ms.date: 12/11/2020
ms.author: kevin
ms.reviewer: jrasnick
---

# Quickstart: Bulk loading with Synapse SQL

Loading data is easy with the Bulk Load wizard in Synapse Studio. The Bulk Load wizard will guide you through creating a T-SQL script with the [COPY statement](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true) to bulk load data. 

## Entry points to the Bulk Load wizard

You can easily bulk load data using dedicated SQL pools with a simple right-click on the following areas within the Synapse Studio:

- A file or folder from an Azure storage account attached to your workspace
![Right-clicking on a file or folder from a storage account](./sql/media/bulk-load/bulk-load-entry-point-0.png)

## Prerequisites

- This wizard generates a COPY statement, which uses Azure AD pass-through for authentication. Your [Azure AD user must have access](
./sql-data-warehouse/quickstart-bulk-load-copy-tsql-examples.md#d-azure-active-directory-authentication) to the workspace with at least the Storage Blob Data Contributor Azure role to the ADLS Gen2 Account. 

- You must have the required [permissions to use the COPY statement](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#permissions) and Create table permissions if you're creating a new table to load to.

- The linked service associated with the ADLS Gen2 Account **must have access to the file**/**folder** to load. For example, if the linked service authentication mechanism is Managed Identity, the workspace managed identity must have at least Storage blob reader permission on the storage account.

- If VNet is enabled on your workspace, make sure the integrated runtime associated to the ADLS Gen2 Account linked services for the source data and error file location has interactive authoring enabled. Interactive authoring is required for autoschema detection, previewing the source file contents, and browsing ADLS Gen2 storage accounts within the wizard.

### Steps

1. Select the storage account and the file or folder you're loading from on the Source storage location panel. The wizard will automatically attempt to detect Parquet files as well as delimited text (CSV) files including mapping the source fields from the file to the appropriate target SQL data types. 

   ![Selecting source location](./sql/media/bulk-load/bulk-load-source-location.png)

2. Select the file format settings including your error settings for when there are rejected rows during the bulk load process. You can also select "Preview data" to see how the COPY statement will parse the file to help you configure the file format settings. Select "Preview data" every time you change a file format setting to see how the COPY statement will parse the file with the updated setting:

   ![Previewing data](./sql/media/bulk-load/bulk-load-file-format-settings-preview-data.png) 

> [!NOTE]  
>
> - Previewing the data with multi-character field terminators is not support in the bulk load wizard. The bulk load wizard will preview the data within a single column when a multi-character field terminator is specified. 
> - When selecting "Infer column names", the bulk load wizard will parse the columns names from the first row specified by the "First row" field. The bulk load wizard will automatically increment the FIRSTROW value in the COPY statement by 1 to ignore this header row. 
> - Specifying multi-character row terminators is supported in the COPY statement; however, this is not supported in the bulk load wizard where an error will be thrown.

3. Select the dedicated SQL pool you're using to load including whether the load will be for an existing table or new table:
   ![Selecting target location](./sql/media/bulk-load/bulk-load-target-location.png)
4. Select "Configure column mapping" to make sure you have the appropriate column mapping. Note columns names will automatically be detected if "Infer column names" was enabled. For new tables, configuring the column mapping is critical for updating the target column data types:

   ![Configuring column mapping](./sql/media/bulk-load/bulk-load-target-location-column-mapping.png)
5. Select "Open script" and a T-SQL script will be generated with the COPY statement to load from your data lake:
   ![Opening the SQL script](./sql/media/bulk-load/bulk-load-target-final-script.png)

## Next steps

- Check the [COPY statement](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true#syntax) article for more information on COPY capabilities
- Check the [data loading overview](./sql-data-warehouse/design-elt-data-loading.md#what-is-elt) article
