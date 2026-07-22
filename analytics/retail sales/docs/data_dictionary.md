# Data Dictionary

## Purpose

This document defines every table, column, and business metric used
throughout the Retail Sales project.

---

# Dimension Tables

## dim_calendar
Contains calendar and fiscal date attributes used for reporting and
forecast feature engineering

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| date | DATE | PK | Calendar date |
| day_name | STRING | | Day name |
| day_of_wk | INT | | Day of week |
| is_weekend | BOOLEAN | | Weekend indicator |
| cal_yr | INT | | Calendar year |
| cal_m | INT | | Calendar month |
| cal_m_name | STRING | | Month name |
| cal_day | INT | | Day of month |
| fiscal_yr | INT | | Fiscal year |
| fiscal_q | STRING | | Fiscal quarter |
| fiscal_m | INT | | Fiscal month |
| fiscal_wk | INT | | Fiscal week |
| ISO_wk | INT | | ISO week, have exactly 7 days and start on a Monday|
| ISO_yr | INT | | ISO year, always start on the first Monday closest to January 1 (starts anywhere between December 29 and January 4) |

## dim_product
Contains descriptive information for retail products

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| product_id | INT | PK | Unique product identifier |
| product_category | STRING | | Product category |
| sub_category | STRING | | Product subcategory |
| product_name | STRING | | Display product name |
| product_size | STRING | | Product size |
| product_shade | STRING | | Cosmetic shade |
| product_status | STRING | | Product status active or inactive|
| list_price | DECIMAL | | Standard retail price |

## dim_store
Contains store attributes

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| store_id | INT | PK | Store identifier |
| retailer | STRING | | Retailer name |
| store_name | STRING | | Store name |
| store_type | STRING | | Store format |
| door_tier | STRING | | Sales tier |
| region | STRING | | Sales region |
| state | STRING | | State |
| city | STRING | | City |
| sq_ft | INT | | Store square footage |
| open_date | DATE | | Opening date |

## dim_promotion
Contains promotional campaigns

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| promo_id | INT | PK | Promotion identifier |
| promo_name | STRING | | Promotion name |
| promo_type | STRING | | Promotion type |
| discount_pct | DECIMAL | | Discount percentage |
| discount_ship | BOOLEAN | | Shipping discount |
| start_date | DATE | | Promotion start |
| end_date | DATE | | Promotion end |
| discount_code | STRING | | Coupon code |

## dim_service
Containes beauty services available at stores

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| service_id | INT | PK | Service identifier |
| service | STRING | | Service name |

---

# Bridge Tables

## bridge_product_promotion
Maps products to promotional campaigns

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| product_id | FK | | Product |
| promo_id | FK | | Promotion |
| value_of_offering | STRING | | Promotional offer description |

## bridge_product_component
Maps products to ingredients/components

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| product_id | FK | | Product |
| component_id | FK | | Component |
| component_desc | STRING | | Component descriptions in product set |

## bridge_store_service
Maps stores to available services

| Column | Type | Key | Description |
|----------|------|-----|-------------|
| store_id | FK | | Store |
| service_id | FK | | Service |
| price | DECIMAL | | Service price |
| effective_date | DATE | | Effective date |
| end_date | DATE | | End date |

---

# Fact Tables

## fact_store_sales
Containes daily retail product sales
One row represents one product sold at one store on one calendar day

| Column | Type | Key | Key Description | Description |
|----------|------|-----|-------------|-------------|
| date | DATE | FK | date → dim_calendar | Sales date |
| store_id | INT | FK | store_id → dim_store | Selling store |
| product_id | INT | FK | product_id → dim_product | Product sold |
| promo_id | INT | FK | promo_id → dim_promotion | Promotion applied |
| units_sold | INT | | | Quantity sold |
| unit_price_at_sale | DECIMAL | | | Selling price |
| gross_sales | DECIMAL | | | Before discounts |
| discount_amount | DECIMAL | | | Dollar discount |
| net_revenue | DECIMAL | |  | Revenue after discounts |

## fact_service_sales
Containes service transactions sales
One row represents one service performed at one store on one calendar day

### Foreign Keys

| Column | Type | Key | Key Description | Description |
|----------|------|-----|-------------|-------------|
| date | DATE | FK | date → dim_calendar | Service date |
| store_id | INT | FK | store_id → dim_store | Store |
| service_id | INT | FK | service_id → dim_service | Service |
| service_count | INT | | Number of services completed |
| unit_price_at_sale | DECIMAL | | Selling price |
| gross_sales | DECIMAL | | Revenue before discounts |
| discount_amount | DECIMAL | | Discount applied |
| net_revenue | DECIMAL | | Revenue collected |

---

# Business Definitions
* Gross Sales: Units Sold × List Price
  * Gross sales only includes revenue from direct sale of goods or services before any deductions
* Discount Amount: Gross Sales x Discount percentage
  * Sale price can be calculated Gross Sales - (Gross Sales - Discount percentage)
* Net Sales: Gross Sales - Discounts - Sales Return - Allowances
  * Currently this dataset does not have sales return or allowances
* Door Tier: is an independent attribute of the store size (Flagship: 900-1400, A: 500-900, B: 250-500, C: 100-250  sqft), it drives sales performance in the simulation (via a demand multiplier), it isn't derived from sales results, foot traffic, or any other prior information
  * More details care in the Assumptions.md

# Future Business Definitions
* Sell Through: Units Sold ÷ Beginning Inventory
  * Beginning Inventory is a snapshot period in time, it can be taken at the beginining of the fiscal day, month, quarter, 
