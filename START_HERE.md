# 🎉 Welcome to Databricks Data Profiler!

## 🎯 What You Have

A **complete, enterprise-grade data profiling application** built specifically for Databricks Apps with:

✅ **AI-Powered Insights** - LLM-based analysis using Foundation Models  
✅ **Cross-Column Analysis** - Correlation matrices, composite keys, conditional profiling  
✅ **Advanced Visualizations** - Box plots, word clouds, temporal charts, heatmaps  
✅ **Profile Snapshots** - Save, compare, and track data quality over time  
✅ **Type-Aware Profiling** - Different strategies for strings, numbers, dates  
✅ **Real-Time Progress** - Visual feedback during profiling operations  
✅ **Multi-Sheet Excel Export** - Comprehensive reports with multiple tabs  
✅ **Interactive UI** - Resizable columns, filters, search, show/hide  
✅ **Full-Stack App** - React frontend + FastAPI backend  
✅ **Production-Ready** - Multi-worker safe, optimized SQL, enterprise UX  

---

## 🚀 Quick Start (3 Steps)

### Step 1: Build Frontend
```bash
cd <your-local-path>/data_profiler_app
npm install  # First time only
npm run build
```

### Step 2: Deploy to Databricks
```bash
mkdir -p deploy_tmp/client && \
cp databricks_app.py app.yaml requirements.txt deploy_tmp/ && \
cp -r client/build deploy_tmp/client/ && \
databricks workspace import-dir deploy_tmp /Workspace/Users/<your.email@company.com>/data-profiling-app --overwrite && \
rm -rf deploy_tmp && \
echo "✅ Deployed!"
```

### Step 3: Create App (First Time Only)
1. Go to **Databricks workspace** → **Apps**
2. Click **Create App**
3. Source: `/Workspace/Users/<your.email@company.com>/data-profiling-app`
4. Select SQL Warehouse
5. Click **Create**

**That's it!** Your app is live at `https://your-app.cloud.databricks.com` 🎊

For subsequent updates, just run Steps 1-2 and refresh your browser!

---

## 🎯 What It Does

### 📊 Executive Summary
- **8 Key Metrics**: Total columns, rows, issues, completeness, quality score, high cardinality, date columns, empty columns
- **Type Distribution**: Visual breakdown of data types across selected columns
- **Quality Overview**: Categorized into Excellent (95-100%), Good (80-94%), Needs Attention (<80%)

### 🔍 Column-Level Profiling

#### For All Data Types
- Completeness (null analysis)
- Uniqueness (distinct values)
- Value distributions with frequencies
- Pattern detection
- Quality scoring (0-100)
- Extreme values (min/max)
- Sample values

#### Type-Specific Analysis
- **Strings**: Length stats (min/max/median), categorical distributions
- **Numbers**: Statistical measures (mean, median, stddev, percentiles), zeros/negatives
- **Dates/Timestamps**: Temporal patterns (day-of-week, hour-of-day, time series)

### 🤖 AI-Powered Features

#### AI Insights
- Natural language analysis of each column
- Actionable recommendations for data quality
- Uses Databricks Foundation Models (`databricks-gemma-3-12b`)

#### Cross-Column Analysis
- **Correlation Matrix**: Discover relationships between numerical columns
- **Composite Keys**: Identify potential primary key candidates
- **Conditional Profiling**: Segment analysis (e.g., stats by category)

#### Profile Snapshots
- Save profile results with custom names
- Compare two snapshots side-by-side
- Track data quality trends over time
- Multi-worker safe with filesystem storage

### 📊 Advanced Visualizations
- **Box Plots**: Statistical distributions for numerical data
- **Word Clouds**: Visual text analysis for categorical fields
- **Time Series**: Temporal pattern analysis with interactive charts
- **Correlation Heatmaps**: Multi-column relationship visualization
- **Smart Bar Charts**: Vertical for numbers, horizontal for categories
- **Temporal Histograms**: Day-of-week and hour-of-day distributions

### 📤 Export Options
- **Multi-Sheet Excel**: Summary, Detailed, Patterns, Quality sheets
- **Fallback CSV**: Multiple CSV files if Excel generation fails
- **Customizable**: Choose which data to include

---

## 📁 Project Structure

```
data_profiler_app/
│
├── 🎨 Frontend (React + TypeScript)
│   ├── app.tsx                          # Main application
│   ├── index.tsx                        # Entry point
│   ├── data_profiling_ui.tsx           # Dashboard UI (2,347 lines)
│   │
│   ├── components/
│   │   ├── DataSelector.tsx            # Catalog/schema/table browser
│   │   ├── AdvancedVisualizations.tsx  # Charts (box plots, word clouds, etc.)
│   │   ├── AIInsightsAndComparison.tsx # AI insights & snapshot comparison
│   │   └── CrossColumnAnalysis.tsx     # Correlation & composite keys
│   │
│   ├── services/
│   │   └── profilingService.ts         # API integration & export
│   │
│   └── utils/
│       └── sqlProfiler.ts              # Type-aware SQL generation
│
├── 🔧 Backend (FastAPI + Python)
│   ├── databricks_app.py               # Main API server (1,696 lines)
│   ├── app.yaml                        # Databricks Apps config
│   └── requirements.txt                # Python dependencies
│
├── 📚 Documentation (8 files)
│   ├── README.md                       # Complete feature docs (590 lines)
│   ├── QUICK_DEPLOY.md                 # 3-step deployment guide
│   ├── START_HERE.md                   # This file!
│   ├── ARCHITECTURE.md                 # System design
│   ├── DEPLOYMENT.md                   # Deployment strategies
│   ├── APP_FLOW.md                     # Visual workflow
│   ├── FILE_INDEX.md                   # File reference
│   └── PROJECT_SUMMARY.md              # Overview
│
├── ⚙️ Configuration
│   ├── package.json                    # NPM dependencies
│   ├── tsconfig.json                   # TypeScript config
│   ├── vite.config.ts                  # Vite build config
│   ├── tailwind.config.js              # Tailwind CSS
│   └── postcss.config.js               # PostCSS
│
└── 🏗️ Build Output
    └── client/build/                   # Compiled React app (deployed to Databricks)
```

---

## 🎓 How to Use

### 1. Select Data
1. Choose **Catalog** from dropdown
2. Expand to select **Schema**
3. Expand to select **Table(s)**
4. Check fields you want to profile (can span multiple tables)

### 2. Run Profile
1. Click **"Run Profile"** button
2. Watch real-time progress bar
3. Wait for profiling to complete (can take 1-2 minutes for large datasets)

### 3. Explore Results

#### Main Dashboard
- View executive summary with 8 key metrics
- See type distribution and quality overview
- Browse comprehensive column table with inline stats
- Use filters: **All** | **Issues** | **Dates** | **Strings** | **Numbers**
- Search by column name
- Resize columns by dragging borders
- Show/hide columns using **Columns** button

#### Column Details
Click **"Details"** on any column to see:
- **Overview**: Type info, uniqueness, nulls, value ranges
- **Statistics**: Cardinality, completeness, full value distributions
- **Extreme Values**: Smallest and largest values
- **Samples**: First rows and random samples
- **Patterns**: Detected patterns with explanations
- **Quality**: Detailed quality scoring and recommendations
- **Visualizations**: Charts (box plots, word clouds, temporal)
- **AI Insights**: LLM-powered analysis and recommendations

### 4. Advanced Features

#### Cross-Column Analysis
1. Profile 2+ columns from the same table
2. Click **"Cross-Column Analysis"** button
3. View correlation matrix (heatmap)
4. Check composite key suggestions
5. Run conditional profiling (segment by category)

#### Profile Snapshots
1. After profiling, enter snapshot name
2. Click **"Save Snapshot"**
3. To compare: select two snapshots, click **"Compare"**
4. View side-by-side comparison with deltas
5. Delete snapshots using trash icon

### 5. Export
Click **"Export Excel"** to download multi-sheet report with:
- **Summary** sheet: High-level metrics
- **Detailed** sheet: All column analysis
- **Patterns** sheet: Pattern detection results
- **Quality** sheet: Quality scores and recommendations

---

## 🔌 How It Works

### Architecture Flow
```
┌──────────────────────────────────────────────────────────────┐
│                        User Browser                          │
│                    (React + TypeScript)                      │
└─────────────────────────┬────────────────────────────────────┘
                          │ HTTPS
                          ▼
┌──────────────────────────────────────────────────────────────┐
│                   Databricks Apps Runtime                    │
│                    (Handles OAuth, CORS)                     │
└─────────────────────────┬────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│                    FastAPI Backend                           │
│              (databricks_app.py - Gunicorn)                  │
│                                                              │
│  • Metadata APIs (catalogs, schemas, tables)                │
│  • Profiling execution (SQL generation & execution)         │
│  • AI insights (Foundation Models API)                      │
│  • Cross-column analysis (correlations, keys)               │
│  • Snapshot management (filesystem storage)                 │
│  • Export generation (multi-sheet Excel)                    │
└─────────────────────────┬────────────────────────────────────┘
                          │
                          ▼
┌──────────────────────────────────────────────────────────────┐
│                   Databricks SQL Warehouse                   │
│                                                              │
│  • Query execution (profiling SQL)                          │
│  • Metadata queries (system.information_schema)             │
│  • Unity Catalog access                                     │
└──────────────────────────────────────────────────────────────┘
```

### Key Technologies
- **Frontend**: React 18, TypeScript, Tailwind CSS, Recharts, React-Wordcloud
- **Backend**: FastAPI, Databricks SDK, Uvicorn, Gunicorn, OpenPyxl
- **Data Layer**: Databricks SQL Warehouses, Unity Catalog, SQL Statements API
- **Storage**: Filesystem-based snapshot persistence (`/tmp/`)

---

## 📚 Documentation Guide

### For Getting Started
1. **START_HERE.md** (this file) - Quick overview and setup
2. **QUICK_DEPLOY.md** - Step-by-step deployment guide
3. **README.md** - Complete feature documentation

### For Understanding the System
4. **APP_FLOW.md** - Visual workflow and user journey
5. **ARCHITECTURE.md** - System design and technical details
6. **PROJECT_SUMMARY.md** - What was built and why

### For Developers
7. **FILE_INDEX.md** - Complete file reference with descriptions
8. **DEPLOYMENT.md** - Production deployment strategies

---

## 🎯 Common Tasks

### Update the App
```bash
# Make code changes, then:
npm run build  # If you changed frontend
# Deploy (same command as initial deployment)
[deployment command from step 2 above]
# Refresh browser to see changes
```

### View Logs
1. Go to Databricks workspace → **Apps**
2. Click your app name
3. Click **Logs** tab
4. Look for:
   - `✅` = Success messages
   - `🔍` = Debug info
   - `[ERROR]` = Problems

### Debug Issues
1. **Browser Console** (F12): Check for JavaScript errors
2. **App Logs**: Check for Python errors
3. **SQL Warehouse Logs**: Check for SQL execution errors
4. **Network Tab**: Check API responses (should be 200, not 500/404)

---

## 🏆 Key Features That Stand Out

1. **AI-Powered**: Uses Foundation Models for intelligent insights
2. **Type-Aware**: Different profiling strategies per data type
3. **Multi-Worker Safe**: Filesystem storage for snapshot consistency
4. **Real-Time Feedback**: SSE-based progress tracking
5. **Enterprise UX**: Resizable columns, filters, export options
6. **Cross-Column Intelligence**: Discovers relationships and patterns
7. **Historical Tracking**: Snapshot comparison over time
8. **Production-Ready**: Optimized SQL, handles large-scale data

---

## 💡 Pro Tips

1. **Profile Strategically**: Start with 5-10 columns, not entire tables
2. **Use Filters**: Filter to specific data types before profiling
3. **Save Snapshots**: Track data quality trends over time
4. **Review AI Insights**: Get actionable recommendations
5. **Cross-Column Analysis**: Discover hidden relationships
6. **Export Reports**: Share Excel files with stakeholders
7. **Watch Progress**: Real-time progress shows exactly what's happening
8. **Resize Columns**: Drag column borders for better visibility

---

## 🐛 Troubleshooting

### App Won't Deploy
- Check `databricks` CLI is configured: `databricks auth login`
- Verify workspace path exists
- Ensure files were copied to `deploy_tmp/` correctly

### App Loads But Catalogs Don't Appear
- Check SQL Warehouse is **running** (not stopped)
- Verify Unity Catalog access permissions
- Check app logs for SQL errors

### Profiling Fails
- Check SQL Warehouse logs for query errors
- Verify you have SELECT permissions on tables
- Try with fewer columns first

### Snapshots Overwriting Each Other
- **Fixed!** Now uses filesystem storage with UUIDs
- Snapshots persist across app restarts
- Multi-worker safe

### AI Insights Not Loading
- Verify workspace has access to Foundation Models API
- Check model name is `databricks-gemma-3-12b`
- Review app logs for API errors

---

## 🎊 You're Ready!

**Everything you need is here:**
- ✅ Production-ready code
- ✅ Comprehensive documentation
- ✅ Simple deployment process
- ✅ Advanced features built-in

**Next Steps:**
1. Deploy the app (3 steps above)
2. Profile your first table
3. Explore AI insights
4. Save a snapshot
5. Share Excel reports with your team

**Questions?** Check the main **README.md** for detailed documentation.

---

**Built with ❤️ for Data Teams**

*Making data profiling intelligent, scalable, and delightful.*
