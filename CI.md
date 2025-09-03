# Continuous Integration (CI) Pipeline Documentation

This project uses **GitHub Actions** to automate builds and tests for both the backend and frontend applications.  
The CI workflow ensures code quality and consistency before changes are merged or deployed.

---

## 📂 Workflow File
- Create: `.github/workflows/ci.yml` in the Repo root
- Push code to trigger CI: Runs on every `push` event.
- GitHub Actions: Installs dependencies, Builds backend & frontend in parallel, Runs tests if available, Workflow passes only if all jobs succeed.

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

# CI Workflow — Step-by-Step Explanation

This guide explains what each part of the GitHub Actions CI workflow does for both **backend** and **frontend** apps.

---

## 1) Trigger
- **`on: push`**  
  Runs the workflow every time commits are pushed to the repository (any branch).  
  Useful for catching issues early.

---

## 2) Jobs (run in parallel)
- **`backend` job** — builds and tests the `/backend` project.  
- **`frontend` job** — builds and tests the `/frontend` project.  
Running them as separate jobs makes the pipeline faster and isolates failures.

---

## 3) Node.js version matrix
- **`strategy.matrix.node-version: [18.x, 20.x, 22.x]`**  
  Each job runs three times, once per Node.js version, ensuring compatibility across LTS/current releases.

---

## 4) Checkout source
- **`actions/checkout@v4`**  
  Pulls the repository contents into the runner so subsequent steps can access code in `/backend` and `/frontend`.

---

## 5) Set up Node.js + npm cache
- **`actions/setup-node@v4`** with `node-version` from the matrix and `cache: 'npm'`.  
  Installs the selected Node.js version and enables npm dependency caching for faster installs on repeat runs.

---

## 6) Install dependencies
### Backend
- **`working-directory: ./backend`**
- **`npm ci`**  
  Performs a clean, reproducible install using `package-lock.json`. Fails if the lockfile is missing or out of sync.

### Frontend
- **`working-directory: ./frontend`**
- **Conditional install:**  
  - If `package-lock.json` exists → **`npm ci`** (clean/reproducible).  
  - Else → **`npm install`** (fallback when no lockfile is committed).  
  This prevents the job from failing when the frontend has no lockfile yet.

---

## 7) Build applications
- **`npm run build --if-present`** in each project directory.  
  Runs the build script only if it exists in `package.json`.  
  Useful when one side may not need a build step yet.

---

## 8) Run tests (auto-skip if none)
- In **each** project directory:
  - Detects whether a `test` script exists (`npm run | grep -q "test"`).  
  - If present → **`npm test`** (job fails on test failures).  
  - If absent → prints a message and **skips** the step gracefully.  
This ensures the pipeline doesn’t fail just because tests haven’t been added yet.

---

## 9) Failure behavior
- If any command in a step exits with a non-zero status, that **job fails**.  
- Because backend and frontend are separate jobs, one can fail while the other passes, making it clear where to investigate.

---

## 10) What you need in each project
- **`package.json`** with relevant scripts:
  - `build` (optional, if you build)
  - `test` (recommended; otherwise the test step will skip)
- **`package-lock.json`** (recommended for reproducible installs; required if you want `npm ci`)
