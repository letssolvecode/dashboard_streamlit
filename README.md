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

Expected column names follow the standard Olist CSV files. Adapt SQL if your imported schema uses different names.

## Local setup (Windows PowerShell)

```powershell
cd olist-context-lab
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
pip install -r requirements.txt
Copy-Item .env.example .env
```

Edit `.env` and set `DATABASE_URL` to your local PostgreSQL connection, or set `NEON_DATABASE_URL` to your Neon connection string. When both are present, `NEON_DATABASE_URL` takes priority. Example for local PostgreSQL:

```text
DATABASE_URL=postgresql+psycopg://postgres:YOUR_PASSWORD@localhost:5432/olist

# Or use Neon (paste the connection string from the Neon console):
# NEON_DATABASE_URL=postgresql://USER:PASSWORD@HOST/DBNAME?sslmode=require
```

The app loads `.env` from the project root. `NEON_DATABASE_URL` takes priority over `DATABASE_URL`, and standard Neon `postgresql://` URLs are adapted to the installed Psycopg 3 driver. Do not commit `.env`. The `analytics` schema and imported tables must exist first.

Run the app:

```powershell
streamlit run dashboard/app.py
```

Run tests:

```powershell
pytest -q
```

## Deploy on Streamlit Community Cloud

1. Create a GitHub repository and push this project, excluding `.env`, credentials and the raw CSVs.
2. Ensure the deployment database is Neon PostgreSQL and has the expected tables/schema.
3. In Streamlit Community Cloud, create an app from the repository and set the entry point to `dashboard/app.py`.
4. In the app's **Settings > Secrets**, add:

```toml
DATABASE_URL = "postgresql+psycopg://USER:PASSWORD@HOST/DBNAME?sslmode=require"
```

Use the connection string copied from Neon. Keep it private. If Neon provides a pooled URL, it is suitable for many app workloads; use the correct hostname and SSL settings from the provider.
5. Deploy, then check the app logs if connection or SQL errors occur.

Streamlit Cloud is the application host; Neon is the database host. The app does not automatically import CSVs or sync local PostgreSQL with Neon.

## Data import

Place the original Olist CSV files in `data/raw/` locally. This directory is ignored by Git. Import into PostgreSQL using pgAdmin 4 or a controlled ETL script. Inspect CSV headers and types first; do not blindly assume every import has the same schema.

The standard source includes nine files: customers, geolocation, order items, payments, reviews, orders, products, sellers and category translations.

## Metric definitions

- **Item revenue** = `SUM(order_items.price)`. This is not profit and excludes freight.
- **Freight** = `SUM(order_items.freight_value)`, reported separately.
- **Order count** = `COUNT(DISTINCT orders.order_id)`.
- **Revenue per order** = item revenue divided by distinct orders in the selected population.
- The default analysis includes all order statuses with a non-null purchase timestamp. Add a documented status rule if you need delivered-only or net-sales measures.
- The monthly chart is a time series. Months are not independent observations; use caution with statistical inference.

## Learning loop

For each business question:
1. Write a SQL query and state the metric definition.
2. Reproduce the aggregate in Pandas.
3. Independently reproduce key statistics in Polars/NumPy.
4. Visualize with Matplotlib/Seaborn and Plotly.
5. Add the validated view to Streamlit.
6. Record discrepancies and run tests.

## Important interpretation and deployment notes

- **Revenue** means `SUM(order_items.price)`. It is not profit and excludes freight, refunds and payment/accounting adjustments.
- RFM recency is relative to the latest purchase in the selected analysis window; frequency and monetary value also use the selected window.
- The simple monthly-revenue confidence interval assumes independent observations; monthly revenue may be seasonal and autocorrelated.
- The delivery/review hypothesis test is observational and is not adjusted for multiple comparisons. A small p-value does not show that delivery delay caused a review score.
- The Streamlit dashboard does not automatically transfer data from local PostgreSQL to Neon. Both databases must contain the expected `analytics` tables if you want to compare them.
- Before calling this production-grade, add a controlled schema migration process, broader integration tests against a disposable database, centralized logging/alerts, access controls, and explicit data-refresh orchestration.
"# dashboard_streamlit" 
