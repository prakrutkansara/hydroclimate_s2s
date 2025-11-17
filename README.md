# Hugo Personal Website - Build & Deployment Guide

Complete guide for building, developing, and deploying the NASA GEOSS2S Hydroclimate S2S personal website with integrated Panel dashboard.

---

## 📋 Table of Contents

1. [Prerequisites](#prerequisites)
2. [Local Setup](#local-setup)
3. [Development Workflow](#development-workflow)
4. [Building the Site](#building-the-site)
5. [Deployment](#deployment)
6. [Dashboard Development](#dashboard-development)
7. [Troubleshooting](#troubleshooting)

---

## Prerequisites

### Required Software

- **Hugo Extended** (v0.152.2 or later)
  ```bash
  # macOS
  brew install hugo
  
  # Linux
  wget https://github.com/gohugoio/hugo/releases/download/v0.152.2/hugo_extended_0.152.2_Linux-64bit.tar.gz
  tar -xzf hugo_extended_0.152.2_Linux-64bit.tar.gz
  sudo mv hugo /usr/local/bin/
  
  # Verify installation
  hugo version
  ```

- **Git** (for version control and deployment)
  ```bash
  # macOS
  brew install git
  
  # Linux
  sudo apt-get install git
  
  # Configure git
  git config --global user.name "Your Name"
  git config --global user.email "your.email@example.com"
  ```

- **Python 3.10+** (for dashboard development)
  ```bash
  # macOS
  brew install python@3.12
  
  # Linux
  sudo apt-get install python3.12 python3.12-venv
  
  # Verify installation
  python3 --version
  ```

### Optional Tools

- **Docker** (for containerized dashboard deployment)
- **VS Code** with Hugo extensions (recommended IDE)

---

## Local Setup

### 1. Clone or Create the Repository

If starting from existing repository:
```bash
cd ~/sites  # or your preferred directory
git clone https://github.com/YOUR_USERNAME/hydroclimate_s2s.git personalwebsite
cd personalwebsite
```

If creating from scratch:
```bash
cd ~/sites
hugo new site personalwebsite
cd personalwebsite
```

### 2. Initialize PaperMod Theme

The site uses the PaperMod theme as a git submodule:

```bash
# Initialize and update theme submodule
git submodule init
git submodule update --remote

# Or if starting fresh:
git submodule add --depth=1 https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update --init --recursive
```

### 3. Verify Directory Structure

Your project should have this structure:
```
personalwebsite/
├── .github/
│   └── workflows/
│       └── hugo.yml          # GitHub Actions deployment
├── archetypes/
├── assets/
├── content/
│   ├── about/
│   ├── dashboard/
│   ├── maps/
│   ├── posts/
│   └── research/
├── dashboard/
│   ├── data/
│   │   └── *.nc             # NetCDF forecast data
│   ├── app.py               # Local development dashboard
│   ├── app_hf.py            # Hugging Face deployment version
│   ├── Dockerfile           # Container configuration
│   └── requirements.txt     # Python dependencies
├── layouts/
├── static/
│   └── images/
│       └── profile.jpg
├── themes/
│   └── PaperMod/            # Git submodule
├── config.toml              # Hugo configuration
├── .gitignore
└── README.md
```

---

## Development Workflow

### 1. Start Hugo Development Server

For live preview with automatic reload:

```bash
cd personalwebsite
hugo server -D
```

Options:
- `-D` or `--buildDrafts`: Include draft posts
- `-F` or `--buildFuture`: Include future-dated posts
- `--disableFastRender`: Full rebuild on changes
- `--port 1313`: Custom port (default: 1313)

Access the site at: `http://localhost:1313`

### 2. Create New Content

```bash
# New blog post
hugo new posts/my-post.md

# New research page
hugo new research/project-name.md
```

Edit the created markdown file and set `draft: false` when ready to publish.

### 3. Customize Configuration

Edit `config.toml` to update:
- Site title and description
- Base URL (for deployment)
- Social media links
- Menu items
- Theme parameters

**Important:** For GitHub Pages deployment, set:
```toml
baseURL = "https://YOUR_USERNAME.github.io/REPO_NAME/"
```

### 4. Add Images and Assets

- **Profile photo:** `static/images/profile.jpg`
- **Other images:** `static/images/` or page bundles
- **Custom CSS:** `assets/css/extended/custom.css`

---

## Building the Site

### Local Build

Generate static files for production:

```bash
# Clean build
hugo --minify

# Build with specific environment
HUGO_ENVIRONMENT=production hugo --minify

# Build with custom base URL
hugo --minify --baseURL "https://example.com/"
```

Output directory: `public/`

### Verify Build

Check for errors:
```bash
hugo --minify --verbose
```

Test the built site locally:
```bash
# Serve from public/ directory
cd public
python3 -m http.server 8000
# Visit http://localhost:8000
```

---

## Deployment

### GitHub Pages Deployment

#### Step 1: Repository Setup

1. Create GitHub repository:
   - Go to https://github.com/new
   - Name: `hydroclimate_s2s` (or your preferred name)
   - Make it public

2. Update `config.toml`:
   ```toml
   baseURL = "https://YOUR_USERNAME.github.io/hydroclimate_s2s/"
   ```

3. Initialize git and push:
   ```bash
   git init
   git add .
   git commit -m "Initial commit"
   git branch -M main
   git remote add origin https://github.com/YOUR_USERNAME/hydroclimate_s2s.git
   git push -u origin main
   ```

#### Step 2: Enable GitHub Actions

1. Go to repository Settings → Pages
2. Under "Build and deployment" → Source:
   - Select **"GitHub Actions"** (NOT "Deploy from a branch")
3. Save settings

#### Step 3: Verify Deployment

1. Check Actions tab: https://github.com/YOUR_USERNAME/hydroclimate_s2s/actions
2. Wait for workflow to complete (green checkmark ✓)
3. Visit your site: https://YOUR_USERNAME.github.io/hydroclimate_s2s/

#### Common Issues

**Site shows README instead of Hugo content:**
- Ensure GitHub Pages source is set to "GitHub Actions"
- Verify workflow completed successfully
- Hard refresh browser (Cmd+Shift+R / Ctrl+Shift+F5)

**Workflow fails with submodule error:**
```bash
git submodule update --init --recursive
git add .
git commit -m "Update theme submodule"
git push
```

**404 errors on subpages:**
- Check `baseURL` ends with `/`
- Verify relative links in content

---

## Dashboard Development

### Local Dashboard Setup

1. **Create Python virtual environment:**
   ```bash
   cd dashboard
   python3 -m venv venv
   source venv/bin/activate  # macOS/Linux
   # or: venv\Scripts\activate  # Windows
   ```

2. **Install dependencies:**
   ```bash
   pip install --upgrade pip
   pip install -r requirements.txt
   ```

3. **Add NetCDF data:**
   ```bash
   # Place your .nc files in dashboard/data/
   cp /path/to/forecast.nc data/
   ```

4. **Run dashboard locally:**
   ```bash
   python app.py
   # Visit http://localhost:8050
   ```

### Dashboard Configuration

**For local development** (`app.py`):
- Port: 8050
- Address: 0.0.0.0
- WebSocket origins: localhost:8050

**For Hugging Face deployment** (`app_hf.py`):
- Port: 7860 (HF Spaces requirement)
- WebSocket origins: wildcard (*)

### Hugging Face Spaces Deployment

1. **Create Space:**
   - Go to https://huggingface.co/new-space
   - Name: `hydroclimate-s2s`
   - SDK: Docker
   - Hardware: CPU Basic (free)

2. **Upload files:**
   ```bash
   cd dashboard
   # Upload via web interface or git:
   git clone https://huggingface.co/spaces/YOUR_USERNAME/hydroclimate-s2s
   cp app.py requirements.txt Dockerfile hydroclimate-s2s/
   cp -r data/ hydroclimate-s2s/
   cd hydroclimate-s2s
   git add .
   git commit -m "Initial dashboard deployment"
   git push
   ```

3. **Update Hugo site:**
   Edit `content/dashboard/index.md`:
   ```html
   <iframe src="https://huggingface.co/spaces/YOUR_USERNAME/hydroclimate-s2s"
           width="100%" height="1200px"></iframe>
   ```

4. **Push changes:**
   ```bash
   cd /path/to/personalwebsite
   git add content/dashboard/index.md
   git commit -m "Update dashboard URL"
   git push
   ```

---

## Troubleshooting

### Hugo Build Errors

**Error: "failed to render pages"**
- Check for syntax errors in markdown files
- Verify template syntax in layouts
- Escape HTML entities in JavaScript: `<` → `&#60;`, `>` → `&#62;`

**Error: "theme not found"**
```bash
git submodule update --init --recursive
```

### Git Issues

**Error: "fatal: remote origin already exists"**
```bash
git remote remove origin
git remote add origin https://github.com/YOUR_USERNAME/REPO.git
```

**Error: "Updates were rejected"**
```bash
git pull --rebase origin main
git push
```

### Dashboard Issues

**ModuleNotFoundError:**
```bash
pip install -r requirements.txt
# or specific package:
pip install geoviews cartopy
```

**Port already in use:**
```bash
# Find and kill process on port 8050
lsof -ti:8050 | xargs kill -9
```

**NetCDF file not found:**
- Verify file is in `dashboard/data/` directory
- Check file permissions: `chmod 644 data/*.nc`

### GitHub Actions

**Workflow not triggering:**
- Verify `.github/workflows/hugo.yml` exists
- Check workflow syntax: https://www.yamllint.com/
- Ensure GitHub Actions is enabled in Settings

**Deprecated action warnings:**
- Update actions in workflow file:
  - `actions/checkout@v4`
  - `actions/upload-pages-artifact@v3`
  - `actions/deploy-pages@v4`

---

## Quick Reference Commands

```bash
# Development
hugo server -D                    # Start dev server
hugo new posts/title.md          # Create new post

# Building
hugo --minify                    # Production build
hugo --minify --verbose          # Build with logs

# Git workflow
git add .
git commit -m "Description"
git push

# Dashboard
python dashboard/app.py          # Run local dashboard
pip install -r requirements.txt  # Install dependencies

# Cleanup
rm -rf public/ resources/        # Remove build artifacts
find . -name ".DS_Store" -delete # Remove system files
```

---

## Additional Resources

- **Hugo Documentation:** https://gohugo.io/documentation/
- **PaperMod Theme:** https://github.com/adityatelange/hugo-PaperMod
- **Panel Documentation:** https://panel.holoviz.org/
- **GitHub Pages:** https://docs.github.com/pages
- **Hugging Face Spaces:** https://huggingface.co/docs/hub/spaces

---

**Need Help?**

- Check existing documentation: `DEPLOYMENT.md`, `PANEL_DASHBOARD_GUIDE.md`
- Review GitHub Actions logs for deployment errors
- Verify all paths and URLs are correct
- Ensure all dependencies are installed

---

*Last updated: November 2025*
