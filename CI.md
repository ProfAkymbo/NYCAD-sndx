# Continuous Integration (CI) Pipeline Documentation

This project uses **GitHub Actions** to automate builds and tests for both the backend and frontend applications.  
The CI workflow ensures code quality and consistency before changes are merged or deployed.

---

## 📂 Workflow File
- Location: `.github/workflows/ci.yml`
- Trigger: Runs on every `push` event.
- Jobs: 
  - **Backend** (build + test)
  - **Frontend** (build + test)
- Node.js versions: **18, 20, 22**

---

## ⚙️ Workflow Steps

### 1. Checkout
- Action: [`actions/checkout@v4`](https://github.com/actions/checkout)
- Purpose: Clones the repository into the runner environment.

### 2. Set up Node.js
- Action: [`actions/setup-node@v4`](https://github.com/actions/setup-node)
- Purpose: Installs the specified Node.js version and caches `npm` dependencies for faster builds.

### 3. Install Dependencies
- Command:  
  - `npm ci` (when `package-lock.json` exists)  
  - `npm install` (when lockfile is missing)
- Purpose: Ensures a clean, reproducible dependency installation.

### 4. Build
- Command: `npm run build --if-present`
- Purpose: Compiles and prepares the application for production (only runs if a `build` script exists).

### 5. Test
- Command: `npm test`
- Purpose: Runs the test suite.
- Notes:  
  - If no `test` script exists in `package.json`, the step is skipped gracefully.  
  - If a `test` script exists but fails, the job will fail.

---

## ✅ Current Status
- **Backend:** Full build + test coverage (CI runs successfully).  
- **Frontend:** Build works, but tests are skipped because no `test` script exists.  
- **Documentation:** This file (`CI.md`) documents the pipeline setup.  

---

## 🚀 Next Steps
1. **Frontend testing:** Add a `test` script in `frontend/package.json`, even a placeholder:
   ```json
   "scripts": {
     "test": "echo \"No tests yet\" && exit 0"
   }
