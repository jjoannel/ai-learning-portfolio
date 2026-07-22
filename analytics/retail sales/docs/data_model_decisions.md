# Data Model and Architectural Decisions
This document captures the key architectural and modeling decisions made during the design of the **Retail Sales Forecast** project. It explains *why* certain approaches were chosen and documents the trade-offs considered throughout development.

---

# Decision 1: Use a Star Schema
The project uses a **star schema** data model.

## Reasoning

The primary objective of this project is analytics and machine learning rather than transaction processing.

A star schema provides:

* Simpler SQL queries
* Fewer table joins
* Faster analytical reporting
* Easier feature engineering

Dimension tables connect directly to fact tables.
  
## Alternative Considered

A snowflake schema was considered but rejected because the additional normalization would introduce complexity for this project's size.

---

# Decision 2: Store Historical Prices in the Fact Table
`unit_price_at_sale` is stored in `fact_store_sales` and `fact_service_sales`.

## Reasoning

Product and service prices change over time.

Using the current list price from the dimension table would produce incorrect historical revenue calculations.

By storing the actual selling price at the time of the transaction, historical reporting remains accurate even if prices change in the future.

This follows common dimensional modeling practices for transactional fact tables.

---

# Decision 3: Store Product Categories in dim_product
Product category and subcategory are stored directly in `dim_product`.

## Reasoning

The product hierarchy for this project is relatively small and stable.

For example:

Face

→ Foundation

→ Liquid Foundation

Maintaining a separate category dimension would increase the number of joins without adding meaningful analytical value.

Keeping the hierarchy within `dim_product` simplifies reporting while remaining easy to maintain.

---

# Decision 4: Use Bridge Tables for Many-to-Many Relationships
Bridge tables are used to model many-to-many relationships.

Examples include:

* `bridge_product_promotion`
* `bridge_store_service`
* `bridge_product_component`

## Reasoning

Products may participate in multiple promotions over time.

Promotions may apply to multiple products.

Stores may offer multiple services.

A bridge table accurately represents these relationships while avoiding duplicated data.

---

# Decision 5: Separate Product Sales and Service Sales
Product sales and service sales are stored in separate fact tables.

## Reasoning

Products and services represent different business processes.

Product sales measure merchandise demand.

Service sales measure completed customer services.

Maintaining separate fact tables keeps each table at a consistent grain and allows each business process to evolve independently.

---

# Decision 6: Define Fact Table Grain Before Loading Data
Every fact table has a clearly defined grain.

### fact_store_sales

One row represents:

* One product
* Sold at one store
* On one calendar day

### fact_service_sales

One row represents:

* One service
* Performed at one store
* On one calendar day

## Reasoning

Clearly defining grain ensures all measures and dimensions are interpreted consistently and prevents mixing different levels of detail within the same table.

---

# Decision 7: Calendar Dimension
A dedicated calendar dimension is used instead of calculating date attributes during analysis.

## Reasoning

The calendar dimension provides reusable attributes such as:

* Calendar Year
* Calendar Month
* Fiscal Year
* Fiscal Quarter
* Fiscal Week
* ISO Week
* Weekend Flag

This simplifies reporting and supports future feature engineering for forecasting models.

---

# Decision 8: Historical Promotions
Promotions are modeled using both a promotion dimension and a product-promotion bridge table.

## Reasoning

This design supports:

* Products participating in multiple promotions
* Promotions covering multiple products
* Historical promotion analysis
* Future promotion effectiveness studies

The sales fact records the promotion associated with each transaction while promotion metadata remains centralized in the dimension table.

# Future Design Considerations
Potential enhancements include:

* Inventory fact table
* Customer dimension
* Supplier dimension
* Employee dimension
* Slowly Changing Dimensions (Type 2)
* Demand planning metrics
* Price elasticity modeling
* Inventory optimization
* Customer lifetime value analysis
* Deep learning forecasting models

These enhancements were intentionally excluded from the initial version to maintain a focused, analytics-oriented data model.
