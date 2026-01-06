# Databricks Data Profiler

A comprehensive, enterprise-grade data profiling application for Databricks that provides deep insights into data quality, completeness, and patterns.

## 🎯 Overview

The Databricks Data Profiler helps data science and analytics teams understand their data quality by analyzing fields across catalogs, schemas, and tables. It provides interactive visualizations, detailed metrics, and exportable reports.

## ✨ Features

### 📊 Executive Summary Dashboard
- **8 Key Metrics**: Total Columns, Total Rows, Issues Found, Completeness %, Quality Score, High Cardinality Count, Date Columns, Empty Columns
- Real-time calculations based on profiling data
- Visual metric cards with icons

### 📈 Rich Visualizations
- **Type Distribution Chart**: Visual breakdown of Integer, String, Date, and Other types with percentage bars
- **Quality Overview Panel**: Categorized into Excellent (95-100%), Good (80-94%), and Needs Attention (<80%)
- Color-coded progress bars and quality indicators

### 📋 Comprehensive Column Analysis
Each column shows:
- Column name with visual badges (High Nulls, Has Nulls, High Cardinality)
- Inferred type with confidence % + documented schema type
- Unique values (count + percentage)
- Uniqueness progress bar
- Null count + percentage
- Completeness progress bar (color-coded: green/yellow/red)
- Quality score out of 100
- Details button for deep dive

### 🔍 Advanced Column Detail Modal (4 Tabs)

#### 1. Overview Tab
- Inferred vs Documented type comparison
- Unique values, Null values, Average length metrics
- Min/Max value range display

#### 2. Statistics Tab
- Cardinality analysis (total rows, unique values, duplicates, uniqueness %)
- Completeness metrics with visual bars
- Top 3 most frequent values with counts and percentages

#### 3. Patterns Tab
- Detected data patterns (e.g., "########" for 8-digit numbers, "AAA" for 3-letter codes)
- Pattern legend explaining symbols (# = digit, A = letter, etc.)

#### 4. Quality Tab
- Overall quality score (large, color-coded)
- Detailed quality factors: Completeness, Type Consistency, Schema Alignment
- Contextual assessment with recommendations

### 🎨 Interactive Features
- Smart search by column name
- Quick filters: All, Issues, Dates, Strings, Numbers
- Hover effects and smooth transitions
- Re-Profile and Export Report buttons
- Multi-table field selection
- Hierarchical catalog/schema/table browser

## 🏗️ Architecture

### Frontend
```
data_profiler_app/
├── app.tsx                      # Main application component
├── index.tsx                    # Entry point
├── data_profiling_ui.tsx        # Dashboard UI component
├── components/
│   └── DataSelector.tsx         # Catalog/Schema/Table/Field selector
├── services/
│   └── profilingService.ts      # Profiling execution and export
├── utils/
│   └── sqlProfiler.ts           # SQL query generation
└── styles.css                   # Tailwind styles
```

### Technology Stack
- **React 18**: Modern React with hooks
- **TypeScript**: Type-safe development
- **Tailwind CSS**: Utility-first styling
- **Lucide React**: Beautiful, consistent icons
- **Vite**: Fast build tool and dev server

## 🚀 Getting Started

### Prerequisites
- Node.js >= 16.0.0
- npm >= 8.0.0
- Databricks workspace with SQL access

### Installation

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

The app will be available at `http://localhost:3000`

## 🔌 Databricks Integration

### SQL Query Generation

The profiler generates optimized SQL queries for Databricks:

```typescript
import { generateProfilingQueries } from './utils/sqlProfiler';

const queries = generateProfilingQueries([
  {
    catalog: 'main',
    schema: 'healthcare',
    table: 'h_mbr_rel',
    field: 'mbr_rel_sk',
    dataType: 'bigint'
  }
]);
```

### Query Features
- **CTEs for Performance**: Uses Common Table Expressions for efficient execution
- **Window Functions**: Leverages Databricks SQL window functions
- **Pattern Detection**: Identifies data patterns using REGEXP_LIKE
- **Type Inference**: Automatically detects actual data types
- **Frequency Analysis**: Calculates top values and distributions
- **Null Analysis**: Comprehensive null value detection

### Example Generated Query

```sql
WITH base_stats AS (
  SELECT
    COUNT(*) as total_rows,
    COUNT(mbr_rel_sk) as non_null_count,
    COUNT(*) - COUNT(mbr_rel_sk) as null_count,
    COUNT(DISTINCT mbr_rel_sk) as unique_count
  FROM main.healthcare.h_mbr_rel
),
value_frequencies AS (
  SELECT
    mbr_rel_sk as field_value,
    COUNT(*) as frequency,
    ROW_NUMBER() OVER (ORDER BY COUNT(*) DESC) as rank
  FROM main.healthcare.h_mbr_rel
  WHERE mbr_rel_sk IS NOT NULL
  GROUP BY mbr_rel_sk
  LIMIT 10
),
type_inference AS (
  SELECT
    CASE
      WHEN mbr_rel_sk IS NULL THEN 'NULL'
      ELSE 'BIGINT'
    END as inferred_type,
    COUNT(*) as type_count
  FROM main.healthcare.h_mbr_rel
  GROUP BY 1
  ORDER BY type_count DESC
  LIMIT 1
)
SELECT * FROM base_stats CROSS JOIN type_inference;
```

### Backend API Integration

To connect to Databricks, implement the backend API endpoint:

```typescript
// Backend API endpoint (Python/Node.js)
// POST /api/databricks/execute

import { DatabricksSQL } from '@databricks/sql';

async function executeProfilingQueries(queries) {
  const client = new DatabricksSQL({
    host: process.env.DATABRICKS_HOST,
    path: process.env.DATABRICKS_HTTP_PATH,
    token: process.env.DATABRICKS_TOKEN
  });

  const results = [];
  for (const query of queries) {
    const result = await client.execute(query.query);
    results.push(result);
  }

  return results;
}
```

### Environment Variables

Create a `.env` file:

```env
DATABRICKS_HOST=your-workspace.cloud.databricks.com
DATABRICKS_HTTP_PATH=/sql/1.0/warehouses/your-warehouse-id
DATABRICKS_TOKEN=your-access-token
```

## 📤 Export Capabilities

### CSV Export Formats

The profiler supports multiple export formats:

#### 1. Single Detailed CSV
All profiling data in one comprehensive file.

#### 2. Multi-Sheet Export
Four separate CSV files mimicking Excel sheets:
- **Summary**: High-level metrics and statistics
- **Detailed**: Complete column-by-column analysis
- **Patterns**: Data pattern detection results
- **Quality**: Quality scores and issues

### Export Usage

```typescript
import { downloadMultiSheetExport } from './services/profilingService';

// Export all sheets
downloadMultiSheetExport(profileResult);

// Files generated:
// - Profile_H_MBR_REL_Summary_2024-01-05.csv
// - Profile_H_MBR_REL_Detailed_2024-01-05.csv
// - Profile_H_MBR_REL_Patterns_2024-01-05.csv
// - Profile_H_MBR_REL_Quality_2024-01-05.csv
```

## 📊 Quality Scoring

The profiler calculates a comprehensive quality score (0-100) based on:

### Quality Factors
1. **Completeness** (up to -40 points penalty)
   - Based on null percentage
   - 0% nulls = no penalty
   - High null rates significantly reduce score

2. **Uniqueness** (up to -15 points penalty)
   - For fields expected to have high cardinality
   - Low uniqueness in large cardinality fields = penalty

3. **Type Consistency** (up to -25 points penalty)
   - How well values match inferred type
   - 100% consistency = no penalty

4. **Schema Alignment** (-20 points penalty)
   - Whether inferred type matches documented type
   - Mismatch indicates potential data quality issues

### Quality Score Ranges
- **95-100**: Excellent - Minimal issues, meets all quality standards
- **80-94**: Good - Some areas for improvement
- **< 80**: Needs Attention - Significant data quality issues

## 🎯 Use Cases

### Data Quality Assessment
Profile tables before using them in analytics to understand completeness and quality.

### Schema Validation
Verify that actual data matches documented schema types.

### Migration Validation
Profile data before and after migrations to ensure data integrity.

### Compliance Auditing
Document data completeness for compliance requirements.

### Pattern Detection
Identify data formats and patterns for data standardization efforts.

### Anomaly Detection
Find columns with unexpected null rates or cardinality.

## 🔧 Customization

### Adding Custom Metrics

Edit `utils/sqlProfiler.ts` to add custom profiling logic:

```typescript
export function generateCustomMetricQuery(
  catalog: string,
  schema: string,
  table: string,
  field: string
): string {
  return `
    SELECT
      '${field}' as column_name,
      -- Add your custom metrics here
      COUNT(DISTINCT CASE WHEN ${field} LIKE '%test%' THEN ${field} END) as test_value_count
    FROM ${catalog}.${schema}.${table}
  `;
}
```

### Styling Customization

Modify `tailwind.config.js` for custom themes:

```javascript
theme: {
  extend: {
    colors: {
      primary: {
        // Your custom color palette
      },
    },
  },
}
```

## 🐛 Troubleshooting

### Common Issues

**Issue**: Queries timing out
**Solution**: Implement sampling for large tables, modify `samplingPolicy` in `sqlProfiler.ts`

**Issue**: Type inference incorrect
**Solution**: Adjust regex patterns in `generateSingleFieldProfilingQuery`

**Issue**: Memory issues with large datasets
**Solution**: Process queries in batches, implement pagination

## 📝 Best Practices

1. **Start with Sampling**: For tables > 100M rows, use sampling
2. **Batch Processing**: Profile 10-20 fields at a time for best performance
3. **Cache Results**: Store profiling results to avoid repeated scans
4. **Schedule Regular Profiling**: Set up automated profiling jobs
5. **Export and Archive**: Keep historical profiling data for trend analysis

## 🤝 Contributing

We welcome contributions! Please follow these guidelines:

1. Fork the repository
2. Create a feature branch
3. Make your changes with tests
4. Submit a pull request

## 📄 License

MIT License - see LICENSE file for details

## 🙋 Support

For questions or issues:
- Open an issue on GitHub
- Contact the data engineering team
- Check Databricks documentation

## 🗺️ Roadmap

- [ ] Real-time profiling with streaming data
- [ ] ML-powered anomaly detection
- [ ] Historical trend analysis
- [ ] Integration with data catalogs
- [ ] Custom rule engine for quality checks
- [ ] Scheduled profiling jobs
- [ ] API for programmatic access
- [ ] Multi-language support

## 📚 Additional Resources

- [Databricks SQL Documentation](https://docs.databricks.com/sql/index.html)
- [React Documentation](https://react.dev)
- [Tailwind CSS Documentation](https://tailwindcss.com)
- [Data Profiling Best Practices](https://example.com/best-practices)

---

**Built with ❤️ for Data Teams**

