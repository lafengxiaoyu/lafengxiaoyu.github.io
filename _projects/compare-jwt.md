---
title: "Compare JWT - JWT对比工具"
collection: projects
permalink: /projects/compare-jwt
date: 2026-03-01
---

A powerful web application for comparing JWT files or tracking JWT changes across Git commits, with support for multiple report formats.

**Repository:** https://github.com/lafengxiaoyu/compare-jwt

## Description

Compare JWT is a security-focused tool designed for developers and security teams to analyze and compare JWT tokens across different environments or Git commits. It's particularly useful for banking and financial institutions with strict encryption environments where data cannot be uploaded to external servers.

## Key Features

- 🔐 **JWT File Comparison** - Input two JWT tokens, automatically decode and visualize differences
- 🌍 **Multi-Environment Comparison** - Track JWT changes across multiple environments (prd/acc/tst) with global detection
- 📄 **Report Generation** - Export reports in Markdown, HTML, and JSON formats
- 🎨 **GitHub-Style Diff Interface** - Clear visualization of additions, deletions, and modifications
- 📋 **Claims Table View** - Tabular display of JWT claims with synchronized scrolling
- 🚀 **Real-time Preview** - Instant comparison results
- 📚 **Built-in User Manual** - Comprehensive usage guide
- 🔒 **Local-Only Processing** - Perfect for secure environments with no external data transmission

## Technology Stack

- **Frontend:** React, TypeScript, Vite
- **Backend:** Node.js, Express
- **Git Operations:** simple-git
- **JWT Parsing:** jwt-decode
- **Diff Comparison:** diff
- **Testing:** Vitest

## Screenshots

<div class="row">
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/projects/compare-jwt/截屏2026-03-08 11.08.41.png" title="JWT Diff Comparison" class="img-fluid rounded z-depth-1" %}
  </div>
  <div class="col-sm mt-3 mt-md-0">
    {% include figure.liquid path="assets/img/projects/compare-jwt/截屏2026-03-08 11.09.04.png" title="Multi-Environment Comparison" class="img-fluid rounded z-depth-1" %}
  </div>
</div>

## Usage

### 1. JWT File Comparison

1. Click "JWT文件对比" (JWT File Comparison) tab
2. Paste the first JWT token in the left input box
3. Paste the second JWT token in the right input box
4. Click "对比 JWT/JWE" (Compare JWT/JWE) button
5. View the decoded JSON difference comparison

**View Options:**
- **Diff View**: GitHub-style side-by-side comparison
- **Claims Table**: Table view with row-level highlighting and synchronized scrolling

### 2. Multi-Environment Comparison

This feature is ideal for tracking JWT configuration changes across multiple environments (prd/acc/tst), and also supports global detection of all changed files.

**Use Cases:**
- Updated JWT configurations in prd, acc, tst environments
- Need to view JWT changes before and after a specific commit
- Need to compare new endpoints, modified manifestVersion, etc.
- Files not in standard environment directories, need global detection

**Steps:**

1. Click "多环境对比" (Multi-Environment Comparison) tab
2. Configure parameters:
   - **API Address**: Backend service address (default: http://localhost:3001)
   - **Repository Path**: Absolute path to your Git repository
     - Example: `/Users/yourname/projects/my-repo`
   - **Commit 1 (Start)**: Commit before changes
     - Example: `HEAD~10` or specific commit hash `abc123`
   - **Commit 2 (End)**: Commit after changes
     - Example: `HEAD` or specific commit hash `def456`
   - **Environment Directories (Optional)**: Comma-separated environment paths
     - Example: `config/prd, config/acc, config/tst`
     - If left blank, auto-detects common environment directories
     - If no standard directories found, processes all changed files globally
3. Click "多环境对比" (Multi-Environment Comparison) button
4. View results:
   - **Summary Cards**: Number of modified JWT files per environment
   - **Details**: Change statistics for each JWT file
     - Green: Added lines
     - Red: Deleted lines
     - Gray: Unchanged lines
   - **Diff View**: Specific change content
5. **Generate Reports**:
   - Click download buttons after comparison
   - 📄 Download Markdown Report
   - 🌐 Download HTML Report
   - 📦 Download JSON Report

### 3. Report Generation

**Supported Formats:**

1. **Markdown Format**
   - Easy to read and edit
   - Supports GitHub-style diff syntax
   - Suitable for documentation sharing and Git commits
   - Filename example: `jwt-compare-report-2026-03-06.markdown`

2. **HTML Format**
   - Beautiful rendering effects
   - Syntax highlighting
   - Suitable for browser viewing
   - Can be directly shared with team
   - Filename example: `jwt-compare-report-2026-03-06.html`

3. **JSON Format**
   - Machine-readable structured data
   - Suitable for program processing and automation integration
   - Contains complete change information
   - Filename example: `jwt-compare-report-2026-03-06.json`

**Generate Reports via Web:**

1. Complete comparison in "Multi-Environment Comparison" page
2. Click corresponding download buttons

**Generate Reports via Command Line:**

```bash
# Generate Markdown report (all files)
./scripts/generate-report.sh /path/to/repo commit1 commit2

# Generate HTML report (specific file)
./scripts/generate-report.sh /path/to/repo commit1 commit2 test-data/test.jose html

# Generate JSON report
./scripts/generate-report.sh /path/to/repo commit1 commit2 '' json
```

**Generate Reports via CURL:**

```bash
# Generate Markdown report
curl -X POST http://localhost:3001/api/git/generate-report \
  -H "Content-Type: application/json" \
  -d '{
    "repoPath": "/path/to/repo",
    "commit1": "commit1-hash",
    "commit2": "commit2-hash",
    "format": "markdown"
  }'

# Generate HTML report (specific file)
curl -X POST http://localhost:3001/api/git/generate-report \
  -H "Content-Type: application/json" \
  -d '{
    "repoPath": "/path/to/repo",
    "commit1": "commit1-hash",
    "commit2": "commit2-hash",
    "file": "test-data/test.jose",
    "format": "html"
  }'
```

## Environment Directory Auto-Detection

If "Environment Directories" is left blank, the system automatically detects directories containing the following keywords:

- **Production**: `prd`, `prod`, `production`
- **Acceptance**: `acc`, `acceptance`
- **Test**: `tst`, `test`
- **Staging**: `stg`, `staging`
- **Development**: `dev`

If no standard environment directories are detected, the system automatically processes all changed files (global detection).

## Installation

```bash
npm install
```

## Running

### Development Mode

Requires running both frontend and backend simultaneously:

```bash
# Terminal 1 - Start frontend
npm run dev

# Terminal 2 - Start backend
npm run server
```

Then visit http://localhost:3000

### Production Mode

```bash
npm run build
npm run preview
```

## Security

This tool runs completely locally, making it perfect for:
- Financial institutions with strict encryption environments
- Enterprise environments that prohibit data upload or crawling
- Internal code repositories like Azure DevOps

**All operations are performed locally without uploading any data to external servers.**

## CLI Tools

### compare-commits.sh - Quick comparison between two commits

```bash
./scripts/compare-commits.sh /path/to/repo HEAD~10 HEAD
```

### generate-report.sh - Generate comparison report

```bash
# Generate Markdown report
./scripts/generate-report.sh /path/to/repo commit1 commit2

# Generate HTML report (specific file)
./scripts/generate-report.sh /path/to/repo commit1 commit2 test-data/test.jose html

# Generate JSON report
./scripts/generate-report.sh /path/to/repo commit1 commit2 '' json
```

## Testing

Run unit tests:

```bash
npm test
```

## License

MIT License
