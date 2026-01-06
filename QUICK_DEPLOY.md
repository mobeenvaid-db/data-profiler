# Quick Deployment Guide

## 🚀 Deploy in 3 Steps

### Step 1: Build Frontend
```bash
cd <your-local-path>/data_profiler_app
npm install  # Only needed first time
npm run build
```

**Expected Output**:
```
✓ 1363 modules transformed.
client/build/index.html                         0.63 kB
client/build/assets/index-*.css                 51.78 kB
client/build/assets/icons-*.js                  10.35 kB
client/build/assets/index-*.js                  59.22 kB
client/build/assets/react-vendor-*.js          140.92 kB
✓ built in 10.06s
```

### Step 2: Deploy to Databricks Workspace
```bash
# Create temp deployment directory
mkdir -p deploy_tmp/client

# Copy required files
cp databricks_app.py app.yaml requirements.txt deploy_tmp/
cp -r client/build deploy_tmp/client/

# Upload to workspace
databricks workspace import-dir deploy_tmp /Workspace/Users/<your.email@company.com>/data-profiling-app --overwrite

# Clean up
rm -rf deploy_tmp

echo "✅ Deployed successfully!"
```

### Step 3: Create/Update Databricks App

**First Time Setup:**
1. Go to Databricks workspace UI
2. Click **Apps** in the left sidebar
3. Click **Create App**
4. **Source path**: `/Workspace/Users/<your.email@company.com>/data-profiling-app`
5. **SQL Warehouse**: Select your SQL warehouse
6. Click **Create**

**Subsequent Updates:**
The app will automatically redeploy when you run Step 2. Just refresh your browser.

---

## 📝 Required Files

These 3 files + the `client/build` directory are all you need:

```
deploy_tmp/
├── databricks_app.py    # FastAPI backend
├── app.yaml             # Databricks Apps config
├── requirements.txt     # Python dependencies
└── client/
    └── build/           # Compiled React app
        ├── index.html
        └── assets/
```

---

## ⚡ One-Line Deploy Command

```bash
cd <your-local-path>/data_profiler_app && \
mkdir -p deploy_tmp/client && \
cp databricks_app.py app.yaml requirements.txt deploy_tmp/ && \
cp -r client/build deploy_tmp/client/ && \
databricks workspace import-dir deploy_tmp /Workspace/Users/<your.email@company.com>/data-profiling-app --overwrite && \
rm -rf deploy_tmp && \
echo "✅ Deployed to Databricks!"
```

---

## 🔧 Configuration

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

### No Manual Environment Variables Needed!
The app automatically:
- Gets SQL Warehouse ID from `app.yaml` resources
- Uses Databricks Apps OAuth tokens
- Configures CORS for the app domain

---

## ✅ Verification Checklist

After deployment, verify:

- [ ] App shows "Running" status in Databricks Apps UI
- [ ] App URL loads without 500 errors
- [ ] Catalogs load in the dropdown (not "Loading...")
- [ ] Can expand schemas and tables
- [ ] Can select fields and click "Run Profile"
- [ ] Profiling completes and shows results
- [ ] Can click "Details" on any column
- [ ] Export Excel button works

---

## 🐛 Troubleshooting

### Build Fails
```bash
# Clear cache and rebuild
rm -rf node_modules client/build
npm install
npm run build
```

### Deploy Fails
```bash
# Check Databricks CLI is configured
databricks auth login --host https://your-workspace.cloud.databricks.com

# Verify path is correct
databricks workspace list /Workspace/Users/<your.email@company.com>/
```

### App Crashes on Startup
Check app logs in Databricks Apps console for:
- `ImportError`: Missing Python dependencies (check requirements.txt)
- `ModuleNotFoundError`: File paths incorrect
- `Permission denied`: SQL Warehouse access issues

### App Loads But Shows Errors
- **Catalogs won't load**: Check SQL Warehouse is running and accessible
- **500 errors in console**: Check app logs for Python tracebacks
- **Blank screen**: Check browser console for JavaScript errors

---

## 📊 App Logs Location

In Databricks workspace:
1. Go to **Apps**
2. Click your app name
3. Click **Logs** tab
4. Filter by time range

Look for:
- `[ERROR]` entries for crashes
- `✅` entries for successful operations
- `🔍` entries for debugging info

---

## 🔄 Deployment Workflow

```
┌─────────────────┐
│  Edit Code      │
│  (locally)      │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  npm run build  │  ← Compiles React to client/build/
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Deploy Command │  ← Uploads files to workspace
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  App Auto-      │  ← Databricks detects changes
│  Redeploys      │     and restarts the app
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  Refresh Browser│  ← See your changes live
└─────────────────┘
```

---

## 💡 Pro Tips

1. **No Need to Rebuild Frontend** if only changing Python code
2. **Snapshots Persist** across deploys (stored in `/tmp/`)
3. **Multiple Workers** are handled automatically
4. **Real-Time Logs** available in Databricks Apps console
5. **Browser Cache**: Hard refresh (Cmd+Shift+R) if UI doesn't update

---

**Need help?** Check the main README.md for detailed documentation.

