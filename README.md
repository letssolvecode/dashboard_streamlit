# Olist Context Lab

An end-to-end learning and portfolio project combining PostgreSQL, statistics, NumPy, Pandas, Polars, Matplotlib, Seaborn, Plotly and Streamlit using the Brazilian Olist e-commerce dataset.

## Implemented features

- Sales overview with date filtering, revenue/order/item KPIs, monthly sales and distribution views
- Sales analysis by month, product category and customer state
- Customer RFM segmentation using `customer_unique_id` and a customer cohort-retention heatmap
- Product-category and seller performance, including eligible-order on-time delivery rates
- Delivery-duration, estimate-delay and review-score analysis
- Statistics lab with a t-based confidence interval for average monthly revenue, IQR outlier flags, and a Mann–Whitney comparison of review-score distributions for late versus on-time deliveries
- Data-quality and database-health dashboard page, plus SQL/Pandas/Polars result validation
- Reusable PostgreSQL query helpers, local-versus-Neon parity utility, SQL exercises, notebooks and tests
- Local `.env` support with `NEON_DATABASE_URL` priority and Streamlit Community Cloud Secrets support

The dashboard computes its displayed values from the database; it does not generate sample KPI data. Some analyses require their corresponding Olist tables. Statistics and business indicators are descriptive, and hypothesis-test results are observational rather than causal. Validate the metric definitions before using the results in business decisions.

## Expected tables

- `analytics.orders`
- `analytics.order_items`
- `analytics.customers`
- `analytics.products`
- `analytics.order_payments`
- `analytics.order_reviews`
- `analytics.sellers`
- `analytics.geolocation`
- `analytics.product_category_name_translation`
