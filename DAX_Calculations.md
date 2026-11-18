# 📐 DAX Calculations – ShopNest Store Business Analytics

### Power BI | DAX Measures | Business Intelligence

![Power BI](https://img.shields.io/badge/Power%20BI-DAX-F2C811?style=for-the-badge&logo=powerbi&logoColor=black)
![Analytics](https://img.shields.io/badge/Business-Analytics-4472C4?style=for-the-badge)
![Data Modeling](https://img.shields.io/badge/Data-Modeling-217346?style=for-the-badge)

---

## 📌 Overview

This document contains the DAX calculations used or required to reproduce the key business metrics in the ShopNest Store Business Analytics Dashboard.

The calculations support analysis of:

- Product category sales.
- Delayed and on-time deliveries.
- Payment method distribution.
- Product ratings.
- State-wise sales.
- Quarterly sales trends.
- Yearly revenue trends.

**Important:** The accessible PBIX report metadata confirms the measure names `Total sales`, `Delayed orders`, and `On Time Orders`, but does not expose every original formula. The expressions below are reproducible definitions based on the source columns and dashboard requirements.

The table names follow the available project documentation. If your Power BI model uses different table names, update the references accordingly.

---

## 1️⃣ Total Sales

**Business Objective:** Calculate total product sales across categories, products, states, and time periods.

```dax
Total sales =
SUM ( 'Nexusgoods_order_items_dataset'[price] )
```

**Explanation:**
- Uses the `price` column from the Order Items dataset.
- Calculates the sum of product item prices.
- Supports category-wise, state-wise, quarterly, and yearly sales analysis.
- Excludes freight charges.
- Represents sales value, not profit.

---

## 2️⃣ Delayed Orders

**Business Objective:** Identify orders delivered after their estimated delivery dates.

```dax
Delayed orders =
CALCULATE (
    DISTINCTCOUNT (
        'Nexusgoods_orders_dataset (1)'[order_id]
    ),
    FILTER (
        'Nexusgoods_orders_dataset (1)',
        NOT ISBLANK (
            'Nexusgoods_orders_dataset (1)'[order_delivered_customer_date]
        )
            &&
        'Nexusgoods_orders_dataset (1)'[order_delivered_customer_date]
            >
        'Nexusgoods_orders_dataset (1)'[order_estimated_delivery_date]
    )
)
```

**Explanation:**
- Uses actual and estimated delivery dates.
- Identifies orders delivered later than the estimated date.
- Uses `DISTINCTCOUNT` to avoid counting an order multiple times.
- Supports delayed orders by product category and monthly delivery analysis.

---

## 3️⃣ On-Time Orders

**Business Objective:** Calculate the number of orders delivered on or before the estimated delivery date.

```dax
On Time Orders =
CALCULATE (
    DISTINCTCOUNT (
        'Nexusgoods_orders_dataset (1)'[order_id]
    ),
    FILTER (
        'Nexusgoods_orders_dataset (1)',
        NOT ISBLANK (
            'Nexusgoods_orders_dataset (1)'[order_delivered_customer_date]
        )
            &&
        'Nexusgoods_orders_dataset (1)'[order_delivered_customer_date]
            <=
        'Nexusgoods_orders_dataset (1)'[order_estimated_delivery_date]
    )
)
```

**Explanation:**
- Compares actual delivery dates with estimated delivery dates.
- Counts orders delivered on time or earlier.
- Excludes orders without an actual delivery date.
- Supports monthly delivery performance comparisons.

---

## 4️⃣ Payment Records

**Business Objective:** Analyze the frequency of payment records by payment method.

```dax
Payment Records =
COUNT (
    'Nexusgoods_order_payments_dataset'[order_id]
)
```

**Explanation:**
- Counts payment records using `order_id`.
- Helps analyze payment method distribution.
- Supports the payment method visualization.

**Note:** An order can have multiple payment records. Therefore, this measure represents payment records, not necessarily distinct orders.

---

## 5️⃣ Average Product Rating

**Business Objective:** Evaluate customer ratings to identify the highest-rated and lowest-rated products.

```dax
Average Product Rating =
AVERAGE (
    'Nexusgoods_order_reviews_dataset'[review_score]
)
```

**Explanation:**
- Calculates the average customer review score.
- Supports product rating comparisons.
- Helps identify products with higher or lower customer satisfaction.

**Important:** Product-level ratings require the appropriate relationship path between Reviews, Orders, Order Items, and Products.

Products with very few reviews should be interpreted carefully.

---

## 6️⃣ Total Freight

**Business Objective:** Calculate the total freight value associated with order items.

```dax
Total Freight =
SUM (
    'Nexusgoods_order_items_dataset'[freight_value]
)
```

**Explanation:**
- Aggregates freight charges.
- Can support additional delivery-cost analysis.
- Helps distinguish product sales from freight value.

---

## 7️⃣ Total Payment Value

**Business Objective:** Calculate the total recorded payment value.

```dax
Total Payment Value =
SUM (
    'Nexusgoods_order_payments_dataset'[payment_value]
)
```

**Explanation:**
- Aggregates payment values.
- Supports financial and payment analysis.
- Represents recorded payment value and should not automatically be treated as product sales.

---

## 8️⃣ Distinct Orders

**Business Objective:** Calculate the unique number of orders.

```dax
Distinct Orders =
DISTINCTCOUNT (
    'Nexusgoods_orders_dataset (1)'[order_id]
)
```

**Explanation:**
- Counts unique order IDs.
- Avoids duplicate order counting.
- Can support additional order-performance KPIs.

---

## 9️⃣ Average Review Score

**Business Objective:** Calculate the overall average customer review score.

```dax
Average Review Score =
AVERAGE (
    'Nexusgoods_order_reviews_dataset'[review_score]
)
```

**Explanation:**
- Calculates the average review score.
- Helps monitor overall customer satisfaction.
- Can be used alongside product-level ratings.

---

## 🔗 Data Model Relationships

The following relationship paths are required for the measures and dashboard visuals to filter correctly:

**1. Orders and Order Items**
- Orders: `order_id`
- Order Items: `order_id`

**2. Orders and Order Payments**
- Orders: `order_id`
- Order Payments: `order_id`

**3. Orders and Order Reviews**
- Orders: `order_id`
- Order Reviews: `order_id`

**4. Order Items and Products**
- Order Items: `product_id`
- Products: `product_id`

**5. Orders and Customers**
- Orders: `customer_id`
- Customers: `customer_id`

Use appropriate relationship cardinality and filter directions based on the actual data model. Avoid unnecessary many-to-many relationships.

---

## 📅 Date Fields Used in the Dashboard

The dashboard uses the following date-related fields:

- `Year`
- `Quarter`
- `Month Year`

These fields support:

- Yearly revenue trends.
- Quarterly sales comparisons.
- Monthly delayed and on-time order analysis.

The available report metadata confirms their use in visuals but does not establish whether they were created in Power Query, as calculated columns, or in a separate date table.

---

## 📊 DAX Measures and Dashboard Visuals

The documented calculations support these dashboard components:

- **Total sales:** Top 10 product categories and state-wise sales.
- **Delayed orders:** Delayed orders by category.
- **On Time Orders:** Monthly delivery comparison.
- **Payment Records:** Payment method analysis.
- **Average Product Rating:** Top 10 and bottom 10 product ratings.
- **Total sales with date context:** Quarterly and yearly sales trends.

---

## 💡 Key Learning Outcomes

Through this project, the following Power BI and DAX concepts were applied:

- Aggregation functions: `SUM()`, `COUNT()`, `AVERAGE()`.
- Unique counting: `DISTINCTCOUNT()`.
- Filter context: `CALCULATE()`.
- Conditional filtering: `FILTER()`.
- Blank-value handling: `ISBLANK()`.
- Date-based business analysis.
- Relationship-based filtering across multiple datasets.

---

## 👩‍💻 Author

**Devika Sutar**

Data Analytics | Power BI | SQL | Python | Excel

---

⭐ These DAX calculations document the business logic behind the ShopNest Store Power BI dashboard.
