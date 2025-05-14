# 📦 Order Tracking Event Driven Data Ingestion 

This project implements a robust, file-triggered data pipeline in **Databricks** using **Delta Lake**. It efficiently stages and loads order tracking data from raw CSV files into Delta tables with upsert logic for incremental processing.

---

## 📊 Architecture Overview

The pipeline uses two sequential notebooks in a **Databricks Workflow**:

```
order_stage_load ➔ orders_target_load
```

Each notebook is responsible for a part of the end-to-end processing:

* `order_stage_load`: Reads incoming CSV files and stages them
* `orders_target_load`: Performs a Delta Lake merge into the target table

---

## 📁 Input & Output

| Component         | Path / Table Location                                    |
| ----------------- | -------------------------------------------------------- |
| 🔹 Source Folder  | `/Volumes/incremental_load/default/orders_data/source/`  |
| 📤 Archive Folder | `/Volumes/incremental_load/default/orders_data/archive/` |
| 🔍 Stage Table    | `incremental_load.default.orders_stage`                  |
| 📈 Target Table   | `incremental_load.default.orders_target`                 |

---

## 📒 Notebook 1: `order_stage_load`

### 📙 Description:

* Reads CSV files from the source folder
* Applies schema to ensure correctness
* Writes data to the staging Delta table
* Moves processed files to the archive directory

### 🔧 Features:

* Enforced schema using `StructType`
* Timestamp parsing support
* File archival using `dbutils.fs.mv`
* Overwrites the staging table for freshness
* Files are **automatically moved** to the archive directory after being processed

---

## 📒 Notebook 2: `orders_target_load`

### 📙 Description:

* Reads data from the stage table
* Performs upsert using `MERGE` on `tracking_num`
* Creates target table if it doesn’t exist

### 🔧 Merge Logic:

* Deletes matched rows (`whenMatchedDelete()`)
* Appends remaining data to the target table

---

## ⏰ Scheduling

Triggered **every minute** using file arrival on:

```
/Volumes/incremental_load/default/orders_data/source/
```

---

## ⚙️ Technologies Used

* Apache Spark (PySpark)
* Delta Lake
* Databricks Workflows
* GitHub Integration

---

## 🚀 Example Use Case

Tracking shipments and updates from vendors:

* Source CSVs represent shipment updates
* Processed in near real-time
* Maintains an up-to-date master table

---

## 🔄 Future Enhancements

* Schema evolution support
* Append-only mode with CDC
* Add email/SMS alerts on job failure
* Add row-level audit columns for history tracking

---
