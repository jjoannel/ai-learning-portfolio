# 🫧 Retail analytics

## 1. Overview
...

## 2. Key Challenges Solved
* **Date Normalization:** Consolidated multiple date formats (e.g., `YYYY-MM-DD` and `MM/DD/YYYY`) into a single standardized datetime type.
* **Text Formatting:** Stripped accidental whitespaces and corrected inconsistent casing across name fields.
* **Data Integrity:** Handled missing primary keys (`Customer_ID`) and purged duplicate transaction logs.
* **Type Casting:** Cleaned mixed string characters (like `$`) out of financial metrics to cast them as clean floats.

* **Problem:** Raw customer sign-up logs arrive with corrupted date strings, duplicate entries, mixed string casing, and hidden null values.
* **Solution:** A robust Pandas pipeline that programmatically sanitizes, normalizes, and validates incoming data.
* **Python Packages:** Pandas, NumPy
  
## 3. Repository Contents
* `customer_data_cleaner.py`: The core Python script containing the cleaning pipeline logic.


