# 📋 Databricks Apps Deployment Checklist

Quick reference for deploying your data profiler to Databricks Apps.

---

## ✅ Pre-Deployment Checklist

### 1. **Build React Frontend**
```bash
cd <your-local-path>/data_profiler_app
npm install
npm run build
```

**Verify:**
```bash
ls -la client/build/
# Should see: index.html, assets/, etc.
```

### 2. **Configure app.yaml**

Edit `app.yaml` and update these values:

```yaml
env:
  - name: 'DATABRICKS_HTTP_PATH'
    value: '/sql/1.0/warehouses/YOUR_WAREHOUSE_ID'  # ← CHANGE THIS
  - name: 'DATABRICKS_SERVER_HOSTNAME'
    value: 'YOUR_WORKSPACE.cloud.databricks.com'   # ← CHANGE THIS
```

**How to find your values:**

**SQL Warehouse ID:**
1. Go to Databricks UI
2. Click "SQL Warehouses" in sidebar
3. Click on your warehouse name
4. Click "Connection details" tab
5. Copy the "HTTP Path" value
   - Example: `/sql/1.0/warehouses/fc63b669d98bde08`

**Workspace Hostname:**
- Your Databricks URL without `https://`
- Example: If URL is `https://my-workspace.cloud.databricks.com`, use `my-workspace.cloud.databricks.com`

### 3. **Verify Files Exist**

```bash
# Check required files
ls -1 databricks_app.py app.yaml requirements.txt client/build/index.html

# Output should show all 4 files exist
```

---

## 🚀 Deployment Steps

### Step 1: Install Databricks CLI (if not installed)

```bash
pip install databricks-cli
```

### Step 2: Configure Authentication

```bash
databricks configure --token
```

**You'll be prompted for:**
- **Host**: Your workspace URL (e.g., `https://my-workspace.cloud.databricks.com`)
- **Token**: Your personal access token
  - Generate: User Settings → Developer → Access Tokens → Generate New Token

### Step 3: Create the App

```bash
databricks apps create data-profiler
```

### Step 4: Deploy

```bash
databricks apps deploy data-profiler \
  --source-code-path . \
  --requirements requirements.txt \
  --config app.yaml
```

**Expected output:**
```
Uploading files...
Building app...
Deploying app...
✓ App deployed successfully
URL: https://your-workspace.cloud.databricks.com/apps/data-profiler
```

### Step 5: Access Your App

```bash
# Open in browser
open https://your-workspace.cloud.databricks.com/apps/data-profiler
```

---

## 🔍 Verification

### 1. Check App Status

```bash
databricks apps get data-profiler
```

**Expected output:**
```json
{
  "name": "data-profiler",
  "status": "RUNNING",
  "url": "https://your-workspace.cloud.databricks.com/apps/data-profiler"
}
```

### 2. View Logs

```bash
# Real-time logs
databricks apps logs data-profiler --follow

# Last 100 lines
databricks apps logs data-profiler --tail 100
```

### 3. Test Health Endpoint

```bash
curl https://your-workspace.cloud.databricks.com/apps/data-profiler/api/health
```

**Expected response:**
```json
{
  "status": "healthy",
  "databricks": "connected",
  "app": "data-profiler",
  "version": "1.0.0"
}
```

---

## 🐛 Troubleshooting

### Issue: "Failed to deploy"

**Check:**
1. `client/build/` directory exists and contains files
2. `app.yaml` has correct SQL Warehouse ID
3. You have permission to create apps in workspace

**Fix:**
```bash
# Rebuild frontend
npm run build

# Verify build
ls client/build/index.html

# Try deploying again
databricks apps deploy data-profiler --source-code-path . --requirements requirements.txt --config app.yaml
```

### Issue: "404 Not Found" for React routes

**Cause:** `client/build/` not included in deployment

**Fix:**
```bash
# Ensure build exists before deploying
npm run build
ls -la client/build/

# Redeploy
databricks apps deploy data-profiler --source-code-path . --requirements requirements.txt --config app.yaml
```

### Issue: "Databricks connection failed"

**Cause:** Incorrect SQL Warehouse ID in `app.yaml`

**Fix:**
1. Verify SQL Warehouse is running
2. Copy correct HTTP Path from SQL Warehouse connection details
3. Update `app.yaml`
4. Redeploy

### Issue: "Permission denied" errors

**Cause:** Insufficient Unity Catalog permissions

**Fix:**
```sql
-- Grant necessary permissions
GRANT USE CATALOG main TO `your_user@company.com`;
GRANT SELECT ON CATALOG main TO `your_user@company.com`;
GRANT CAN_USE ON SQL WAREHOUSE your_warehouse_id TO `your_user@company.com`;
```

---

## 🔄 Updating the App

### Update Code

```bash
# Make changes to databricks_app.py or other files

# Redeploy
databricks apps deploy data-profiler \
  --source-code-path . \
  --requirements requirements.txt \
  --config app.yaml
```

### Update Frontend

```bash
# Make changes to React code

# Rebuild
npm run build

# Redeploy
databricks apps deploy data-profiler \
  --source-code-path . \
  --requirements requirements.txt \
  --config app.yaml
```

### Update Dependencies

```bash
# Edit requirements.txt

# Redeploy with new dependencies
databricks apps deploy data-profiler \
  --source-code-path . \
  --requirements requirements.txt \
  --config app.yaml
```

---

## 📊 Monitoring

### View App Metrics

In Databricks UI:
1. Go to "Apps" in sidebar
2. Click on "data-profiler"
3. View metrics:
   - Request count
   - Response times
   - Error rates
   - Active users

### View SQL Warehouse Usage

1. Go to "SQL Warehouses"
2. Click on your warehouse
3. View "Query History" tab
4. See profiling queries executed by your app

---

## 🎯 Quick Reference

### Deployment Command (One-liner)

```bash
npm run build && databricks apps deploy data-profiler --source-code-path . --requirements requirements.txt --config app.yaml
```

### Files Required for Deployment

```
✓ databricks_app.py          (FastAPI application)
✓ app.yaml                    (Databricks configuration)
✓ requirements.txt            (Python dependencies)
✓ client/build/index.html     (React production build)
✓ client/build/assets/        (React assets)
```

### Environment Variables in app.yaml

```yaml
env:
  - name: 'DATABRICKS_HTTP_PATH'
    value: '/sql/1.0/warehouses/YOUR_WAREHOUSE_ID'
  - name: 'DATABRICKS_SERVER_HOSTNAME'
    value: 'YOUR_WORKSPACE.cloud.databricks.com'
```

---

## ✅ Success Indicators

- ✅ App status shows "RUNNING"
- ✅ Health check returns 200 OK
- ✅ Can access UI at `/apps/data-profiler`
- ✅ Can select catalogs/schemas/tables
- ✅ Profiling queries execute successfully
- ✅ Can export CSV reports

---

## 📚 Additional Resources

- **Full Guide:** `DATABRICKS_DEPLOYMENT.md`
- **Architecture:** `DATABRICKS_APPS_REVIEW.md`
- **Troubleshooting:** `README.md` → Troubleshooting section

---

**Ready to deploy?** Follow the checklist and you'll be live in 10 minutes! 🚀

