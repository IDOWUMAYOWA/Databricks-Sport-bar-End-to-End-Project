# End-to-End Data Engineering Project: Sports Company Data Integration

## 📋 Project Overview

This project implements a comprehensive data engineering solution to integrate a newly acquired sports company into the parent company's existing data ecosystem. The solution establishes a modern data lakehouse architecture using Databricks, enabling unified analytics and reporting across both entities.

### Business Context

- **Parent Company**: Established sports retailer with a functional OLTP (Online Transaction Processing) system
- **Acquired Company**: Newly acquired sports company requiring data integration
- **Objective**: Build a scalable data engineering pipeline to merge operational data from both companies into a unified analytical platform

### Key Achievements

✅ Implemented medallion architecture (Bronze → Silver → Gold layers)  
✅ Built dimensional data model for sales analytics  
✅ Developed both batch and incremental loading patterns  
✅ Created interactive Databricks Lakeview dashboard for business insights  
✅ Established automated data quality checks and monitoring  

---

## 🏗️ Architecture

### Data Lakehouse Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                    Source Systems (OLTP)                     │
│  Parent Company Data  │  Acquired Company Data              │
└────────────┬──────────┴─────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│                    Bronze Layer (Raw Data)                   │
│  - Landing zone for all source data                          │
│  - Minimal transformations                                   │
│  - Full historical data retention                            │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│               Silver Layer (Cleaned & Conformed)             │
│  - Data quality checks applied                               │
│  - Standardized schemas                                      │
│  - Deduplication and validation                              │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│            Gold Layer (Business-Level Aggregates)            │
│  - Star Schema (Dimensions + Facts)                          │
│  - Optimized for analytics                                   │
│  - Business KPIs and metrics                                 │
└────────────┬────────────────────────────────────────────────┘
             │
             ▼
┌─────────────────────────────────────────────────────────────┐
│              Databricks Lakeview Dashboard                   │
│  - Sales insights and analytics                              │
│  - Real-time business metrics                                │
└─────────────────────────────────────────────────────────────┘
```

### Dimensional Model (Star Schema)

**Fact Table:**
- `fact_sales` - Transactional sales data with foreign keys to dimensions

**Dimension Tables:**
- `dim_customer` - Customer master data
- `dim_product` - Product catalog and attributes
- `dim_price` - Pricing information and history
- `dim_date` - Date dimension for time-based analysis

---

## 🛠️ Technology Stack

| Component | Technology |
|-----------|-----------|
| **Cloud Platform** | Databricks |
| **Processing Engine** | Apache Spark (PySpark) |
| **Storage** | AWS S3|
| **Data Format** | Delta Lake |
| **Orchestration** | Databricks Workflows |
| **Visualization** | Databricks Lakeview Dashboard |
| **Language** | Python, SQL |

---

## 📁 Project Structure

```
project-root/
│
├── 1_Setup.ipynb                       # Environment setup and configuration
├── utilities.ipynb                     # Reusable helper functions
│
├── dimension_processing/
│   ├── customer_data_processing.ipynb  # Customer dimension ETL
│   ├── products_data_processing.ipynb  # Product dimension ETL
│   ├── price_data_processing.ipynb     # Price dimension ETL
│   └── dim_date_table_creation.ipynb   # Date dimension generation
│
├── fact_processing/
│   ├── 1_full_load_fact.ipynb          # Initial full load of fact table
│   └── 2_incremental_load_fact.ipynb   # Incremental/delta loading
│
├── views/
│   └── dashhboard      # Gold layer views for dashboard
│
├── orchestration/
│   └── workflow_job.json               # Databricks workflow configuration
│
├── dashboards/
│   └── Dashboard_Sales.pdf             # Sales analytics dashboard (Lakeview)
│
└── README.md                           # This file
```

---

## 🚀 Getting Started

### Prerequisites

- Databricks workspace with Unity Catalog enabled
- Access to source OLTP systems (parent and acquired company)
- AWS S3 buckket
- Databricks Runtime 13.3 LTS or higher

### Setup Instructions

#### 1. Clone Repository
```bash
# Clone the project repository
git clone <repository-url>
cd sports-company-data-integration
```

#### 2. Configure Environment
Run the `1_Setup.ipynb` notebook to:
- Set up database schemas
- Configure storage paths
- Initialize Delta tables
- Set up access credentials

```python
# Example configuration
catalog = "fmcg"
bronze_schema = "bronze"
silver_schema = "silver"
gold_schema = "gold"
```

#### 3. Load Utility Functions
Execute `utilities.ipynb` to load reusable helper functions for:
- Data quality checks
- Schema validation
- Error handling


#### 4. Process Dimensions
Run dimension processing notebooks in the following order:
1. `dim_date_table_creation.ipynb` - Create date dimension
2. `customer_data_processing.ipynb` - Process customer data
3. `products_data_processing.ipynb` - Process product data
4. `price_data_processing.ipynb` - Process pricing data

#### 5. Load Fact Table

**Initial Load:**
```bash
# Run full load for historical data
Run: 1_full_load_fact.ipynb
```

**Incremental Load:**
```bash
# Schedule for daily/hourly incremental updates
Run: 2_incremental_load_fact.ipynb
```

#### 6. Create Dashboard Views

Create optimized views for the Lakeview dashboard:
```sql
-- Run the view creation script
Run: views/create_dashboard_views.sql

-- Or execute in Databricks SQL Editor
CREATE OR REPLACE VIEW gold.vw_sales_summary AS ...
CREATE OR REPLACE VIEW gold.vw_customer_revenue AS ...
CREATE OR REPLACE VIEW gold.vw_product_performance AS ...
```

#### 7. Configure Automated Workflow

Set up the Databricks workflow for automated orchestration:

1. Navigate to **Workflows** in Databricks UI
2. Click **Create Job**
3. Import the workflow configuration from `orchestration/workflow_job.json` OR
4. Manually configure tasks in this order:
   - Task 1: `customer_data_processing` 
   - Task 2: `products_data_processing` (depends on Task 1)
   - Task 3: `price_data_processing` (depends on Task 2)
   - Task 4: `2_incremental_load_fact` (depends on Task 3)
   - Task 5: Create/Refresh dashboard views (depends on Task 4)
5. Set schedule: Daily at **02:00 AM UTC**
6. Configure email notifications
7. **Save and Enable** the workflow

#### 8. Connect Dashboard to Views

Configure the Databricks Lakeview dashboard:
1. Create a new Lakeview dashboard
2. Add visualizations using the gold layer views:
   - `gold.vw_sales_summary`
   - `gold.vw_customer_revenue`
   - `gold.vw_product_performance`
   - `gold.vw_monthly_trend`
   - `gold.vw_channel_performance`
3. Configure filters and parameters
4. Publish dashboard for business users

---

## 📊 Data Pipeline Details

### 1. Dimension Processing

#### Customer Dimension (`dim_customer`)
- **Source**: Parent and acquired company customer databases
- **Transformations**: 
  - Deduplication using business keys
  - Data standardization (addresses, phone numbers)
  - Customer segmentation
- **SCD Type**: Type 2 (Slowly Changing Dimension with history tracking)

#### Product Dimension (`dim_product`)
- **Source**: Product master data from both companies
- **Transformations**: 
  - Product hierarchy mapping
  - Category standardization
  - SKU harmonization
- **SCD Type**: Type 2

#### Price Dimension (`dim_price`)
- **Source**: Pricing systems
- **Transformations**: 
  - Currency conversion
  - Price history tracking
  - Discount calculations
- **SCD Type**: Type 2

#### Date Dimension (`dim_date`)
- **Generation**: Programmatically generated for 10-year range
- **Attributes**: Year, Quarter, Month, Week, Day, Fiscal periods, Holidays

### 2. Fact Table Loading

#### Full Load (`1_full_load_fact.ipynb`)
- **Purpose**: Initial historical data load
- **Process**: 
  1. Extract all historical transactions
  2. Join with dimension tables to get surrogate keys
  3. Load into `fact_sales` table
  4. Data validation and reconciliation

#### Incremental Load (`2_incremental_load_fact.ipynb`)
- **Purpose**: Daily/hourly delta updates
- **Process**: 
  1. Identify new/updated records using watermark (timestamp)
  2. Perform incremental merge using Delta Lake MERGE operation
  3. Handle late-arriving facts
  4. Update aggregates and summaries

**Change Data Capture (CDC) Strategy:**
```python
# Pseudo-code for incremental load
last_update_timestamp = get_watermark()
new_records = source.filter(col("updated_at") > last_update_timestamp)

fact_sales.alias("target").merge(
    new_records.alias("source"),
    "target.transaction_id = source.transaction_id"
).whenMatchedUpdateAll().whenNotMatchedInsertAll().execute()
```

---

## 📈 Dashboard & Analytics

### Databricks Lakeview Dashboard

The `Dashboard_Sales.pdf` showcases key business metrics:

#### KPIs Tracked:
- **Total Revenue**: 119.93B INR
- **Total Quantity Sold**: 39.05M units
- **Unique Customers**: 53
- **Total Quantity Ordered**: 4052.46

#### Visualizations:

1. **Best Selling Products by Revenue** (Bar Chart)
   - Top 10 products ranked by sales revenue

2. **Total Revenue by Channel** (Donut Chart)
   - Channel distribution: General, Distributor, Wholesale, Retailer

3. **Monthly Revenue Trend** (Area Chart)
   - Time series analysis showing revenue trends from Jan 2024 to Nov 2025

4. **All Customers by Revenue and Quantity** (Table)
   - Customer-level analysis with sortable columns

5. **Top Revenue by Category** (Bar Chart)
   - Category-level performance metrics

#### Interactive Features:
- **Filters**: Quarter, Year, Month, Channel, Platform, Category
- **Drill-down**: Click on visualizations to filter related charts
- **Export**: Download data or visualizations for presentations

---

## 🔄 Data Quality & Monitoring

### Data Quality Checks

Implemented across all pipeline stages:

1. **Schema Validation**: Ensure correct data types and required fields
2. **Null Checks**: Identify and handle missing values
3. **Duplicate Detection**: Remove duplicate records based on business keys
4. **Referential Integrity**: Validate foreign key relationships
5. **Business Rule Validation**: 
   - Prices are positive
   - Quantities are within acceptable ranges
   - Dates are valid

### Monitoring & Alerting

- **Data Freshness**: Monitor last update timestamps
- **Record Counts**: Track daily record volumes
- **Pipeline Failures**: Alert on ETL job failures
- **Data Drift**: Detect schema changes in source systems

---

## 🎯 Key Features

### 1. Unified Data Model
- Single source of truth for sales data across both companies
- Consistent business definitions and metrics
- Historical data preservation

### 2. Scalable Architecture
- Handles millions of transactions daily
- Auto-scaling Databricks clusters
- Optimized Delta Lake storage

### 3. Batch & Incremental Processing
- **Batch**: Full historical loads for initial setup
- **Incremental**: Efficient daily updates using CDC
- Reduced processing time and costs

### 4. Automated Orchestration
- **End-to-End Workflow**: Automated pipeline from source to dashboard
- **Task Dependencies**: Sequential execution ensuring data integrity
- **Scheduled Runs**: Daily automated execution at 02:00 AM
- **Failure Handling**: Retry logic and email alerts
- **No Manual Intervention**: Fully automated data refresh

### 5. Optimized Dashboard Views
- **Pre-aggregated Views**: Fast dashboard performance
- **Gold Layer Abstraction**: Business-friendly data models
- **Auto-refresh**: Views updated after each pipeline run
- **Query Optimization**: Indexed and partitioned for speed

### 6. Self-Service Analytics
- Business users can access dashboards directly
- No SQL knowledge required for basic reporting
- Drill-down capabilities for detailed analysis

### 7. Data Governance
- Unity Catalog for centralized governance
- Row-level and column-level security
- Audit logging for compliance

---


---

## 📝 Usage Examples

### Query Sales by Customer
```sql
SELECT 
    c.customer_name,
    SUM(f.total_amount_inr) as total_revenue,
    SUM(f.sold_quantity) as total_quantity
FROM gold.fact_sales f
JOIN gold.dim_customer c ON f.customer_key = c.customer_key
GROUP BY c.customer_name
ORDER BY total_revenue DESC
LIMIT 10;
```

### Monthly Revenue Trend
```sql
SELECT 
    d.year,
    d.month_name,
    SUM(f.total_amount_inr) as monthly_revenue
FROM gold.fact_sales f
JOIN gold.dim_date d ON f.date_key = d.date_key
GROUP BY d.year, d.month_name, d.month_number
ORDER BY d.year, d.month_number;
```

### Product Performance Analysis
```sql
SELECT 
    p.product_name,
    p.category,
    SUM(f.sold_quantity) as total_quantity,
    SUM(f.total_amount_inr) as total_revenue,
    AVG(pr.price) as avg_price
FROM gold.fact_sales f
JOIN gold.dim_product p ON f.product_key = p.product_key
JOIN gold.dim_price pr ON f.price_key = pr.price_key
GROUP BY p.product_name, p.category
ORDER BY total_revenue DESC;
```

---

## 🔧 Troubleshooting

### Common Issues

**Issue**: Incremental load picking up duplicate records
- **Solution**: Check watermark timestamp logic; ensure proper deduplication

**Issue**: Dashboard showing outdated data
- **Solution**: Verify scheduled jobs are running; check Delta table refresh

**Issue**: Performance degradation in queries
- **Solution**: Run OPTIMIZE and VACUUM commands on Delta tables; check cluster sizing

**Issue**: Schema mismatch errors
- **Solution**: Enable schema evolution in Delta Lake; validate source data

---

## 🚦 Pipeline Orchestration

### Databricks Workflows

The project implements an automated workflow that orchestrates the entire ETL pipeline, ensuring data flows sequentially through all processing stages.

#### Workflow Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    Databricks Workflow Job                       │
│                  (Scheduled: Daily at 02:00 AM)                  │
└─────────────────────────────────────────────────────────────────┘
                              │
                              ▼
        ┌─────────────────────────────────────────────┐
        │  Task 1: Customer Data Processing            │
        │  Notebook: customer_data_processing.ipynb    │
        │  Output: dim_customer (Silver → Gold)        │
        └──────────────────┬──────────────────────────┘
                           │ (On Success)
                           ▼
        ┌─────────────────────────────────────────────┐
        │  Task 2: Product Data Processing             │
        │  Notebook: products_data_processing.ipynb    │
        │  Output: dim_product (Silver → Gold)         │
        └──────────────────┬──────────────────────────┘
                           │ (On Success)
                           ▼
        ┌─────────────────────────────────────────────┐
        │  Task 3: Price Data Processing               │
        │  Notebook: price_data_processing.ipynb       │
        │  Output: dim_price (Silver → Gold)           │
        └──────────────────┬──────────────────────────┘
                           │ (On Success)
                           ▼
        ┌─────────────────────────────────────────────┐
        │  Task 4: Fact Table Load (Incremental)       │
        │  Notebook: 2_incremental_load_fact.ipynb     │
        │  Output: fact_sales (Gold Layer)             │
        └──────────────────┬──────────────────────────┘
                           │ (On Success)
                           ▼
        ┌─────────────────────────────────────────────┐
        │  Task 5: Create/Refresh Dashboard Views      │
        │  Creates optimized views for Lakeview        │
        │  Output: Gold layer views for visualization  │
        └─────────────────────────────────────────────┘
```

#### Task Dependencies

Each task depends on the successful completion of the previous task:

1. **Customer Processing** → Runs first, no dependencies
2. **Product Processing** → Depends on Customer Processing
3. **Price Processing** → Depends on Product Processing  
4. **Fact Loading** → Depends on all three dimensions being ready
5. **View Creation** → Depends on Fact table being loaded

#### Workflow Configuration

```python
# Workflow job configuration (JSON format in Databricks)
{
  "name": "Sports_Company_ETL_Pipeline",
  "tasks": [
    {
      "task_key": "customer_processing",
      "notebook_task": {
        "notebook_path": "/dimension_processing/customer_data_processing",
        "base_parameters": {}
      },
      "cluster_spec": {...},
      "timeout_seconds": 3600
    },
    {
      "task_key": "product_processing",
      "depends_on": [{"task_key": "customer_processing"}],
      "notebook_task": {
        "notebook_path": "/dimension_processing/products_data_processing"
      }
    },
    {
      "task_key": "price_processing",
      "depends_on": [{"task_key": "product_processing"}],
      "notebook_task": {
        "notebook_path": "/dimension_processing/price_data_processing"
      }
    },
    {
      "task_key": "fact_loading",
      "depends_on": [{"task_key": "price_processing"}],
      "notebook_task": {
        "notebook_path": "/fact_processing/2_incremental_load_fact"
      }
    },
    {
      "task_key": "create_dashboard_views",
      "depends_on": [{"task_key": "fact_loading"}],
      "sql_task": {
        "query": {
          "query_id": "view_creation_query_id"
        }
      }
    }
  ],
  "schedule": {
    "quartz_cron_expression": "0 0 2 * * ?",
    "timezone_id": "UTC"
  }
}
```

### Daily Schedule

```
Daily Automated Run:
├── 02:00 AM - Start Workflow
│   ├── 02:00 - Customer Dimension Processing (5-20 min)
│   ├── 02:20 - Product Dimension Processing (5-20 min)
│   ├── 02:40 - Price Dimension Processing (5-15 min)
│   ├── 02:55 - Fact Table Incremental Load (10-30 min)
│   └── 03:25 - Create/Refresh Dashboard Views (4 min)
└── 03:30 AM - Workflow Complete, Dashboard Ready
```

### Workflow Features

- **Sequential Execution**: Tasks run in order, ensuring data dependencies are met
- **Failure Handling**: If any task fails, subsequent tasks are skipped
- **Retry Logic**: Automatic retry on transient failures (configurable)
- **Email Alerts**: Notifications on success/failure sent to data engineering team
- **Monitoring**: Real-time workflow status in Databricks UI
- **Manual Trigger**: Can be triggered manually for ad-hoc runs

---

## 📊 Dashboard Views Layer

### View Creation from Gold Layer

To optimize dashboard performance and provide clean interfaces for business users, we create materialized views on top of the gold layer tables.

#### View Architecture

```sql
-- Main views created for the dashboard:

-- 1. Sales Summary View
CREATE OR REPLACE VIEW gold.vw_sales_summary AS
SELECT 
    f.transaction_id,
    d.date,
    d.year,
    d.quarter,
    d.month_name as month,
    c.customer_name as customer,
    p.product_name as product,
    p.category,
    f.channel,
    f.platform,
    pr.price,
    f.sold_quantity,
    f.total_amount_inr as total_revenue
FROM gold.fact_sales f
INNER JOIN gold.dim_date d ON f.date_key = d.date_key
INNER JOIN gold.dim_customer c ON f.customer_key = c.customer_key
INNER JOIN gold.dim_product p ON f.product_key = p.product_key
INNER JOIN gold.dim_price pr ON f.price_key = pr.price_key
WHERE f.is_current = TRUE;

-- 2. Customer Revenue View
CREATE OR REPLACE VIEW gold.vw_customer_revenue AS
SELECT 
    c.customer_name as customer,
    SUM(f.total_amount_inr) as total_revenue,
    SUM(f.sold_quantity) as total_quantity,
    COUNT(DISTINCT f.transaction_id) as transaction_count
FROM gold.fact_sales f
INNER JOIN gold.dim_customer c ON f.customer_key = c.customer_key
GROUP BY c.customer_name;

-- 3. Product Performance View
CREATE OR REPLACE VIEW gold.vw_product_performance AS
SELECT 
    p.product_name as product,
    p.category,
    SUM(f.total_amount_inr) as revenue,
    SUM(f.sold_quantity) as quantity_sold,
    COUNT(DISTINCT f.customer_key) as unique_customers
FROM gold.fact_sales f
INNER JOIN gold.dim_product p ON f.product_key = p.product_key
GROUP BY p.product_name, p.category;

-- 4. Monthly Trend View
CREATE OR REPLACE VIEW gold.vw_monthly_trend AS
SELECT 
    d.year,
    d.month_number,
    d.month_name as month,
    SUM(f.total_amount_inr) as revenue,
    SUM(f.sold_quantity) as quantity
FROM gold.fact_sales f
INNER JOIN gold.dim_date d ON f.date_key = d.date_key
GROUP BY d.year, d.month_number, d.month_name
ORDER BY d.year, d.month_number;

-- 5. Channel Performance View
CREATE OR REPLACE VIEW gold.vw_channel_performance AS
SELECT 
    f.channel,
    SUM(f.total_amount_inr) as total_revenue,
    SUM(f.sold_quantity) as total_quantity,
    COUNT(DISTINCT f.transaction_id) as transaction_count
FROM gold.fact_sales f
GROUP BY f.channel;
```

#### Dashboard View Benefits

| Benefit | Description |
|---------|-------------|
| **Performance** | Pre-aggregated data reduces query time in dashboard |
| **Simplicity** | Business users don't need to understand complex joins |
| **Consistency** | Standardized metrics across all reports |
| **Security** | Can apply row-level security at view level |
| **Maintainability** | Logic changes made once in view, not in each dashboard |

#### View Refresh Strategy

Views are automatically refreshed when the orchestration workflow completes:

```python
# Pseudo-code for view refresh in orchestration
def refresh_dashboard_views():
    """
    Refresh all dashboard views after fact table load
    """
    views = [
        "gold.vw_sales_summary",
        "gold.vw_customer_revenue",
        "gold.vw_product_performance",
        "gold.vw_monthly_trend",
        "gold.vw_channel_performance"
    ]
    
    for view in views:
        spark.sql(f"REFRESH TABLE {view}")
        log.info(f"Refreshed view: {view}")
```

#### Dashboard Connection

The Databricks Lakeview dashboard queries these views directly:

- **Total Revenue KPI** → `SELECT SUM(total_revenue) FROM gold.vw_sales_summary`
- **Customer Table** → `SELECT * FROM gold.vw_customer_revenue`
- **Product Chart** → `SELECT * FROM gold.vw_product_performance`
- **Monthly Trend** → `SELECT * FROM gold.vw_monthly_trend`
- **Channel Donut Chart** → `SELECT * FROM gold.vw_channel_performance`

---

## 🔄 Monitoring & Alerting

### Workflow Monitoring

Track workflow execution through:

1. **Databricks Workflow UI**: Real-time status of each task
2. **Run History**: Historical execution logs and duration trends
3. **Task Logs**: Detailed logs for debugging failures
4. **Metrics Dashboard**: Custom dashboard tracking pipeline health

### Email Notifications

Configured email alerts for:
- ✅ **Success**: Daily summary of successful runs
- ❌ **Failure**: Immediate alert with error details
- ⚠️ **SLA Breach**: Alert if workflow exceeds expected duration
- 📊 **Data Quality Issues**: Alert on validation failures

### Sample Alert Configuration

```json
{
  "email_notifications": {
    "on_success": ["data-team@sportscompany.com"],
    "on_failure": ["data-team@sportscompany.com", "on-call@sportscompany.com"],
    "no_alert_for_skipped_runs": false
  },
  "webhook_notifications": {
    "on_failure": [{
      "id": "slack-webhook-id"
    }]
  }
}
```

---

## 📚 Best Practices Implemented

1. **Medallion Architecture**: Structured data flow through Bronze → Silver → Gold
2. **Delta Lake**: ACID transactions, time travel, schema evolution
3. **Partition Strategy**: Date-based partitioning for optimal query performance
4. **Z-Ordering**: Data skipping optimization on frequently filtered columns
5. **Modular Code**: Reusable functions in utilities notebook
6. **Error Handling**: Comprehensive try-catch blocks with logging
7. **Documentation**: Inline comments and markdown cells in notebooks
8. **Version Control**: Git integration for code versioning

---

## 🎓 Learning Outcomes

This project demonstrates:
- Building end-to-end data pipelines in Databricks
- Implementing dimensional modeling for analytics
- Handling both batch and streaming data patterns
- Creating interactive dashboards for business users
- Managing data quality and governance
- Optimizing Spark workloads for performance

---

## 🤝 Contributing

For questions, issues, or contributions:
1. Create an issue in the repository
2. Submit a pull request with detailed description
3. Follow coding standards and add appropriate tests

---

## 📄 License

[Specify your license here - e.g., MIT, Apache 2.0]

---

## 👥 Team

- **Data Engineering Team**: Pipeline development and maintenance
- **Business Analysts**: Requirements gathering and dashboard design
- **Data Governance**: Security and compliance oversight

---

## 📞 Support

For technical support or questions:
- **Documentation**: [Link to internal docs]
- **Email**: data-engineering@sportscompany.com
- **Slack**: #data-engineering-support

---

## 🗓️ Project Timeline

- **Phase 1** (Weeks 1-2): Requirements gathering and architecture design
- **Phase 2** (Weeks 3-4): Bronze and Silver layer implementation
- **Phase 3** (Weeks 5-6): Gold layer and dimensional modeling
- **Phase 4** (Week 7): Dashboard development
- **Phase 5** (Week 8): Testing, optimization, and go-live

---

## 🔮 Future Enhancements

- [ ] Real-time streaming pipeline using Spark Structured Streaming
- [ ] Machine learning models for sales forecasting
- [ ] Advanced anomaly detection in sales patterns
- [ ] Integration with external marketing platforms
- [ ] Mobile dashboard application
- [ ] Natural language query interface (SQL through ChatGPT)

---

**Last Updated**: January 2026  
**Version**: 1.0  
**Status**: Production
