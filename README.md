# Databricks Data Profiler

A comprehensive, enterprise-grade data profiling application for Databricks that provides deep insights into data quality, completeness, patterns, and relationships across your data assets.

## 🎯 Overview

The Databricks Data Profiler is a full-stack application built specifically for Databricks Apps, helping data science and analytics teams understand their data quality through intelligent profiling, AI-powered insights, and advanced analytics. Profile data across catalogs, schemas, and tables with interactive visualizations, detailed metrics, and exportable reports.

## ✨ Key Features

### 📊 Executive Summary Dashboard
- **8 Key Metrics**: Total Columns, Total Rows, Issues Found, Completeness %, Quality Score, High Cardinality Count, Date Columns, Empty Columns
- Real-time calculations with visual metric cards
- Type distribution chart (Integer, String, Date, Other)
- Quality overview panel (Excellent, Good, Needs Attention)

### 📈 Comprehensive Column Profiling

#### Type-Aware Analysis
- **String Fields**: Length statistics (min/max/median), pattern detection, categorical value distributions
- **Numerical Fields**: Min/Max/Mean/Median/StdDev, percentiles (25th, 75th, 95th, 99th), zeros/negatives/infinites count
- **Temporal Fields**: Day-of-week histograms, hour-of-day distributions, time series analysis
- **All Types**: Completeness, uniqueness, null analysis, quality scoring

#### Value Analysis
- Full value distributions (not just top 3)
- Frequency analysis with percentages
- Extreme values (smallest and largest)
- Sample values (first rows and random samples)

#### Pattern Detection
- Automatic pattern recognition (emails, phone numbers, IDs, codes)
- Pattern explanations (# = digit, A = letter, etc.)
- Mixed pattern detection with detailed breakdowns

### 🔍 Advanced Column Detail Modal (7 Tabs)

1. **Overview**: Type comparison, basic metrics, value ranges
2. **Statistics**: Cardinality, completeness, full value distributions with smart visualizations
3. **Extreme Values**: Smallest and largest values for quick anomaly detection
4. **Samples**: First rows and random samples for data verification
5. **Patterns**: Detected patterns with explanations and frequency counts
6. **Quality**: Detailed quality scoring with actionable recommendations
7. **Visualizations**: Advanced charts (box plots, word clouds, temporal analysis)
8. **AI Insights**: LLM-powered analysis and recommendations

### 🤖 AI-Powered Features

#### AI Insights (Databricks Foundation Models)
- Natural language analysis of column profiles
- Actionable recommendations for data quality improvement
- Uses `databricks-gemma-3-12b` model
- Intelligent fallback to rule-based insights

#### Cross-Column Analysis
- **Correlation Matrix**: Heatmap showing relationships between numerical columns
- **Composite Key Detection**: Automatic identification of potential primary keys
- **Conditional Profiling**: Segment analysis (e.g., statistics by category)

#### Profile Snapshots & Comparison
- Save profile snapshots for historical tracking
- Side-by-side snapshot comparison with delta analysis
- Persistent storage across app restarts
- Multi-worker safe with filesystem-based storage

### 📊 Advanced Visualizations

- **Box Plots**: Statistical distribution visualization for numerical data
- **Word Clouds**: Visual representation of text field values
- **Time Series**: Temporal pattern analysis with interactive charts
- **Correlation Heatmaps**: Multi-column relationship visualization
- **Bar Charts**: Intelligent orientation (vertical for numerical, horizontal for categorical)
- **Histograms**: Day-of-week and hour-of-day distributions

### 🎨 Interactive Features

- **Smart Search**: Filter columns by name
- **Quick Filters**: All, Issues, Dates, Strings, Numbers
- **Resizable Columns**: Click and drag to adjust column widths
- **Show/Hide Columns**: Customize table view
- **Real-time Progress**: Visual feedback during profiling and analysis
- **Multi-table Selection**: Profile fields across different tables
- **Hierarchical Browser**: Navigate catalogs → schemas → tables → columns

### 📤 Export Capabilities

#### Multi-Sheet Excel Export
- **Summary Sheet**: High-level metrics and statistics
- **Detailed Sheet**: Complete column-by-column analysis
- **Patterns Sheet**: Data pattern detection results
- **Quality Sheet**: Quality scores and recommendations

Fallback to multi-CSV export if Excel generation fails.

## 🏗️ Architecture

### Full-Stack Databricks App

```
data_profiler_app/
├── Frontend (React + TypeScript)
│   ├── app.tsx                          # Main application
│   ├── index.tsx                        # Entry point
│   ├── data_profiling_ui.tsx           # Dashboard UI
│   ├── components/
│   │   ├── DataSelector.tsx            # Catalog browser
│   │   ├── AdvancedVisualizations.tsx  # Charts & graphs
│   │   ├── AIInsightsAndComparison.tsx # AI & snapshots
│   │   └── CrossColumnAnalysis.tsx     # Correlation & keys
│   ├── services/
│   │   └── profilingService.ts         # API integration
│   └── utils/
│       └── sqlProfiler.ts              # SQL generation
│
├── Backend (FastAPI + Python)
│   ├── databricks_app.py               # Main API server
│   ├── app.yaml                        # Databricks Apps config
│   └── requirements.txt                # Python dependencies
│
└── Deployment
    └── client/build/                   # Compiled React app
```

### Technology Stack

#### Frontend
- **React 18**: Modern React with hooks and concurrent features
- **TypeScript**: Type-safe development
- **Tailwind CSS**: Utility-first styling
- **Lucide React**: Beautiful, consistent icons
- **Recharts**: Responsive charts and graphs
- **React-Wordcloud**: Word cloud visualizations
- **Vite**: Lightning-fast build tool

#### Backend
- **FastAPI**: High-performance Python web framework
- **Databricks SDK**: Native Databricks integration
- **Uvicorn**: ASGI server for production
- **Gunicorn**: Process manager with multiple workers
- **OpenPyxl**: Excel file generation

#### Data Layer
- **Databricks SQL Warehouses**: Query execution
- **Unity Catalog**: Metadata access via `system.information_schema`
- **SQL Statements API**: Asynchronous query execution
- **Filesystem Storage**: Multi-worker snapshot persistence

## 🚀 Getting Started

### Prerequisites

**For Deployment Only:**
- Databricks workspace
- SQL Warehouse configured
- Databricks CLI configured

**For Development (if modifying code):**
- Node.js >= 18.0.0
- Python >= 3.11
- npm >= 9.0.0

### Local Development

```bash
# Install frontend dependencies
npm install

# Install backend dependencies
pip install -r requirements.txt

# Start development server
npm run dev

# Build for production
npm run build
```

### Databricks Deployment

> **Quick Deploy**: Pre-built frontend files are included in `client/build/`. You can deploy directly without Node.js!

#### Deploy to Databricks

```bash
# Create deployment package
mkdir -p deploy_tmp/client
cp databricks_app.py app.yaml requirements.txt deploy_tmp/
cp -r client/build deploy_tmp/client/

# Upload to Databricks workspace
databricks workspace import-dir deploy_tmp /Workspace/Users/<your.email@company.com>/data-profiling-app --overwrite

# Clean up
rm -rf deploy_tmp
```

#### 3. Deploy as Databricks App

In Databricks workspace:
1. Go to **Apps** → **Create App**
2. Select source: `/Workspace/Users/your.email@company.com/data-profiling-app`
3. Choose SQL Warehouse
4. Click **Create**

The app will be available at `https://your-app.cloud.databricks.com`

#### Rebuilding Frontend (Optional)

**Only required if you modify React components or styles:**

```bash
cd <your-local-path>/data_profiler_app
npm install  # First time only
npm run build
```

This regenerates the `client/build/` directory with your changes. Then redeploy using the steps above.

## 📝 Configuration

### app.yaml

```yaml
command: ["uvicorn", "databricks_app:api_app", "--host", "0.0.0.0", "--port", "8000"]

resources:
  - name: main_sql_warehouse
    description: SQL warehouse for profiling queries
    sql_warehouse:
      permission: CAN_USE

env:
  - name: SQL_WAREHOUSE_ID
    value: "{{resources.main_sql_warehouse.id}}"
```

### Environment Variables

The app automatically uses:
- `SQL_WAREHOUSE_ID`: From app.yaml resources
- OAuth tokens: Managed by Databricks Apps runtime

## 🔌 API Endpoints

### Metadata Endpoints
- `GET /api/catalogs` - List available catalogs
- `GET /api/schemas` - List schemas in a catalog
- `GET /api/tables` - List tables in a schema
- `GET /api/columns` - List columns in a table

### Profiling Endpoints
- `POST /api/databricks/execute` - Execute profiling queries
- `POST /api/databricks/execute-stream` - Execute with real-time progress (SSE)
- `POST /api/export/excel` - Generate multi-sheet Excel export

### Cross-Column Analysis
- `POST /api/cross-column/correlations` - Calculate correlation matrix
- `POST /api/cross-column/composite-keys` - Detect composite key candidates
- `POST /api/cross-column/conditional-profiling` - Segment analysis

### AI & Insights
- `POST /api/ai/generate-insights` - Generate AI-powered insights
- `POST /api/temporal/analyze` - Analyze temporal patterns

### Snapshot Management
- `POST /api/snapshots/save` - Save profile snapshot
- `GET /api/snapshots/list` - List all snapshots
- `GET /api/snapshots/{id}` - Get specific snapshot
- `DELETE /api/snapshots/{id}` - Delete snapshot
- `POST /api/snapshots/compare` - Compare two snapshots

## 📊 SQL Query Generation

### Intelligent Type-Aware Profiling

The profiler generates optimized SQL queries tailored to each data type:

#### For String Fields
```sql
WITH base_stats AS (
  SELECT
    COUNT(*) as total_rows,
    COUNT(column_name) as non_null_count,
    COUNT(DISTINCT column_name) as unique_count,
    AVG(LENGTH(CAST(column_name AS STRING))) as avg_length,
    MIN(LENGTH(CAST(column_name AS STRING))) as min_length,
    MAX(LENGTH(CAST(column_name AS STRING))) as max_length,
    PERCENTILE(LENGTH(CAST(column_name AS STRING)), 0.50) as median_length
  FROM catalog.schema.table
),
value_analysis AS (
  SELECT 
    COLLECT_LIST(column_name) as all_values,
    -- Pattern detection, frequency analysis
  FROM catalog.schema.table
)
SELECT * FROM base_stats CROSS JOIN value_analysis;
```

#### For Numerical Fields
```sql
WITH base_stats AS (
  SELECT
    COUNT(*) as total_rows,
    COUNT(column_name) as non_null_count,
    MIN(column_name) as min_value,
    MAX(column_name) as max_value,
    AVG(column_name) as mean_value,
    STDDEV(column_name) as stddev_value,
    PERCENTILE(column_name, 0.25) as p25_value,
    PERCENTILE(column_name, 0.50) as median_value,
    PERCENTILE(column_name, 0.75) as p75_value,
    COUNT(CASE WHEN column_name = 0 THEN 1 END) as zeros_count,
    COUNT(CASE WHEN column_name < 0 THEN 1 END) as negatives_count
  FROM catalog.schema.table
)
SELECT * FROM base_stats;
```

### Performance Features
- **Common Table Expressions (CTEs)**: Efficient query organization
- **Window Functions**: Leverage Spark SQL capabilities
- **Sampling Support**: Handle large datasets gracefully
- **Incremental Loading**: Fetch metadata on-demand
- **Streaming Progress**: Real-time feedback with Server-Sent Events

## 🎯 Use Cases

### 1. Data Quality Assessment
Profile tables before using them in analytics pipelines to understand completeness, accuracy, and consistency.

### 2. Schema Validation
Verify that actual data matches documented schema types, catching type mismatches early.

### 3. Migration Validation
Profile data before and after migrations to ensure data integrity and catch regressions.

### 4. Compliance & Auditing
Document data completeness and quality for compliance requirements (SOX, GDPR, HIPAA).

### 5. Pattern Detection & Standardization
Identify data formats and patterns across systems for standardization efforts.

### 6. Anomaly Detection
Find columns with unexpected null rates, cardinality, or value distributions.

### 7. Cross-Column Relationships
Discover hidden correlations and composite key candidates for data modeling.

### 8. Historical Tracking
Save snapshots over time to track data quality trends and measure improvements.

## 🔧 Advanced Features

### Multi-Worker Architecture

The app runs with multiple gunicorn workers for performance. Snapshots use filesystem-based storage (`/tmp/databricks_profiler_snapshots/`) to ensure consistency across workers.

### Real-Time Progress Tracking

Server-Sent Events (SSE) provide real-time updates during long-running profiling operations:

```typescript
// Frontend automatically consumes SSE
{
  "current": 3,
  "total": 10,
  "percentage": 30,
  "fieldKey": "catalog.schema.table.column",
  "description": "Profiling numerical column..."
}
```

### Intelligent Chart Selection

The UI automatically selects appropriate visualizations based on data types:
- **Numerical**: Vertical bar charts, box plots, histograms
- **Categorical**: Horizontal bar charts, word clouds
- **Temporal**: Time series, day-of-week, hour-of-day charts

### Auto-Sizing Columns

Table columns auto-size to content with minimum widths, preventing text overlap while remaining manually resizable.

## 📊 Quality Scoring Algorithm

Quality scores (0-100) are calculated based on multiple factors:

### Scoring Components

1. **Completeness** (up to -30 points)
   - 0% nulls = 100 points
   - Each % of nulls reduces score proportionally

2. **Uniqueness** (-5 points)
   - Applies to high-cardinality fields
   - Penalty for unexpectedly low uniqueness

3. **Type Consistency** (-10 points)
   - Inferred type vs documented type mismatch

### Quality Ranges
- **95-100** (Excellent): Production-ready, minimal issues
- **80-94** (Good): Acceptable with minor improvements needed
- **< 80** (Needs Attention): Significant data quality issues require investigation

## 🐛 Troubleshooting

### Common Issues

**Issue**: Queries timing out on large tables
**Solution**: The app auto-limits value collection. For very large tables (>100M rows), consider sampling.

**Issue**: Snapshots not persisting
**Solution**: Snapshots are stored in `/tmp/` and survive app deploys but not server reboots. For production, consider database storage.

**Issue**: "No column metadata in manifest" warnings
**Solution**: These are debug messages. The app handles both dictionary and array result formats automatically.

**Issue**: Cross-column analysis shows 404
**Solution**: Ensure snapshots are loaded from disk. The app auto-reloads before comparison.

**Issue**: AI insights timeout
**Solution**: AI insights use Foundation Models API. Ensure your workspace has access to `databricks-gemma-3-12b`.

### Debug Mode

Check app logs in Databricks for detailed debugging:
- Snapshot operations: `✅ Saved snapshot...`, `📋 Listing snapshots...`
- API calls: `🔍 Comparing snapshots...`, `💾 Persisted snapshot...`
- Progress: Real-time SSE events logged

## 📝 Best Practices

### For Best Performance
1. **Profile in Batches**: Profile 10-20 columns at a time
2. **Use Filters**: Filter to specific data types before profiling
3. **Monitor Progress**: Real-time progress shows execution status
4. **Export Results**: Save Excel reports for offline analysis

### For Data Quality
1. **Regular Profiling**: Schedule weekly or monthly profiling runs
2. **Track Snapshots**: Save snapshots to monitor quality trends
3. **Use AI Insights**: Review AI recommendations for each column
4. **Cross-Column Analysis**: Check correlations before modeling
5. **Quality Thresholds**: Set organizational standards (e.g., min 95% completeness)

### For Multi-User Environments
1. **Shared Snapshots**: Use descriptive snapshot names with dates
2. **Export Before Analysis**: Download Excel reports for team sharing
3. **Document Findings**: Use AI insights as starting point for documentation

## 🤝 Contributing

We welcome contributions! Areas for enhancement:
- Additional chart types and visualizations
- More pattern detection rules
- Custom quality scoring algorithms
- Database-backed snapshot storage
- Scheduled profiling jobs
- Integration with data catalogs

## 🗺️ Roadmap

- [x] Type-aware profiling (Phase 1)
- [x] Enhanced statistics (Phase 2)
- [x] Advanced modal organization (Phase 3)
- [x] AI-powered insights
- [x] Cross-column analysis
- [x] Profile snapshots and comparison
- [x] Advanced visualizations
- [x] Real-time progress tracking
- [x] Multi-sheet Excel export
- [x] Resizable/hideable columns
- [ ] Database-backed snapshot storage
- [ ] Real-time profiling with streaming data
- [ ] Historical trend dashboards
- [ ] Scheduled profiling jobs
- [ ] API for programmatic access
- [ ] Multi-workspace support
- [ ] Custom rule engine

## 📚 Additional Resources

- [Databricks Apps Documentation](https://docs.databricks.com/dev-tools/databricks-apps/index.html)
- [Databricks SQL Documentation](https://docs.databricks.com/sql/index.html)
- [Unity Catalog Documentation](https://docs.databricks.com/data-governance/unity-catalog/index.html)
- [Foundation Models API](https://docs.databricks.com/machine-learning/foundation-models/index.html)
- [React Documentation](https://react.dev)
- [FastAPI Documentation](https://fastapi.tiangolo.com)
- [Tailwind CSS Documentation](https://tailwindcss.com)

## 📄 License

MIT License - see LICENSE file for details

## 🙋 Support

For questions or issues:
- Check app logs in Databricks Apps console
- Review console logs in browser DevTools (F12)
- Verify SQL Warehouse is configured and accessible
- Ensure Unity Catalog access permissions

## 🏆 Features That Set This Apart

- **Native Databricks Integration**: Built specifically for Databricks Apps
- **Multi-Worker Safe**: Filesystem-based snapshot storage
- **AI-Powered**: Uses Foundation Models for intelligent insights
- **Type-Aware**: Different profiling strategies for different data types
- **Real-Time Feedback**: SSE-based progress tracking
- **Production-Ready**: Handles large-scale data with optimized SQL
- **Enterprise UX**: Resizable columns, export options, snapshot comparison

---

**Built with ❤️ for Data Teams Using Databricks**

*Comprehensive data profiling made simple, scalable, and intelligent.*
