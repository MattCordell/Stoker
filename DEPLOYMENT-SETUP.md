# Deployment Setup Guide

This repository uses a dual deployment strategy:
- **Production**: Main branch → GitHub Pages
- **Preview/Development**: All other branches → Netlify

## Setup Instructions

### 1. GitHub Pages Setup (Production)

GitHub Pages is already configured to deploy from the `main` branch using GitHub Actions.

**To enable it:**
1. Go to your repository on GitHub
2. Navigate to **Settings** → **Pages**
3. Under "Build and deployment":
   - **Source**: Select "GitHub Actions"
4. Push to `main` branch and the workflow will automatically deploy

Your production site will be available at: `https://mattcordell.github.io/Stoker/`

### 2. Netlify Setup (Preview Deployments)

**Step 1: Create a Netlify Account**
1. Go to [netlify.com](https://www.netlify.com/)
2. Sign up for a free account (you can use your GitHub account)

**Step 2: Create a New Site**
1. In Netlify dashboard, click "Add new site" → "Import an existing project"
2. Choose GitHub and authorize Netlify
3. Select your `Stoker` repository
4. Click "Deploy site" (Netlify will do an initial deployment - this is fine)
5. Once deployed, go to "Site configuration" → "Site details"
6. Note your **Project ID** (also known as Site ID) - looks like: `abc123-def456-ghi789`

**Step 3: Get Your Netlify API Token**
1. Click your profile icon → "User settings"
2. Go to "Applications" → "Personal access tokens"
3. Click "New access token"
4. Give it a name (e.g., "GitHub Actions")
5. Copy the token (you won't see it again!)

**Step 4: Add Secrets to GitHub**
1. Go to your GitHub repository
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click "New repository secret" and add:
   - **Name**: `NETLIFY_AUTH_TOKEN`
   - **Value**: Paste your Netlify personal access token
4. Click "New repository secret" again and add:
   - **Name**: `NETLIFY_SITE_ID`
   - **Value**: Paste your Netlify site ID

### 3. Test the Deployment

**Test Production Deployment:**
```bash
# Make a small change and push to main
git checkout main
git add .
git commit -m "Test production deployment"
git push origin main
```

Check the "Actions" tab on GitHub to see the deployment progress.

**Test Development Deployment (Netlify Production):**
```bash
# Push to develop branch
git checkout develop
git add .
git commit -m "Test development deployment"
git push origin develop
```

This will deploy to the clean URL: `https://stoker-dev.netlify.app/`

**Test Feature Branch Deployment (Netlify Preview):**
```bash
# Push to any other branch
git checkout -b test-feature
git add .
git commit -m "Test preview deployment"
git push origin test-feature
```

This will deploy to: `https://test-feature--stoker-dev.netlify.app/`

The Netlify deployment will:
- Post a comment on commits with the deployment link
- Show deployment status in GitHub Actions

## How It Works

### Production (Main Branch) - Stable Release
- Trigger: Push to `main` branch
- Workflow: [.github/workflows/deploy-production.yml](.github/workflows/deploy-production.yml)
- Destination: GitHub Pages
- URL: `https://mattcordell.github.io/Stoker/`

### Development (Develop Branch) - Latest Features
- Trigger: Push to `develop` branch
- Workflow: [.github/workflows/deploy-preview.yml](.github/workflows/deploy-preview.yml)
- Destination: Netlify (production deployment)
- URL: `https://stoker-dev.netlify.app/` (clean URL!)

### Preview (Feature Branches)
- Trigger: Push to any other branch
- Workflow: [.github/workflows/deploy-preview.yml](.github/workflows/deploy-preview.yml)
- Destination: Netlify (preview deployment)
- URL: `https://[branch-name]--stoker-dev.netlify.app/`

## Netlify Deployment URLs

- **Develop branch**: `https://stoker-dev.netlify.app/` (production URL)
- **Feature branches**: `https://[branch-name]--stoker-dev.netlify.app/` (preview URLs)
  - Example: `high-priority-ux` → `https://high-priority-ux--stoker-dev.netlify.app/`
  - Example: `feature-xyz` → `https://feature-xyz--stoker-dev.netlify.app/`

The workflow will automatically comment on commits with the deployment link.

## Troubleshooting

**GitHub Pages not deploying:**
- Ensure GitHub Actions is selected as the source in Settings → Pages
- Check the "Actions" tab for error messages
- Verify the workflow file has correct permissions

**Netlify not deploying:**
- Verify `NETLIFY_AUTH_TOKEN` and `NETLIFY_SITE_ID` secrets are set correctly
- Check the "Actions" tab for error messages
- Ensure your Netlify account is active and the site exists

**Preview URLs not working:**
- Wait a few minutes after the first deployment
- Check Netlify dashboard for deployment status
- Verify the site is not paused in Netlify settings

## Cost

- **GitHub Pages**: Free for public repositories
- **Netlify**: Free tier includes:
  - 100 GB bandwidth/month
  - 300 build minutes/month
  - Unlimited sites and deploys

This is more than sufficient for a static documentation site.

## Alternative: Use Cloudflare Pages Instead

If you prefer Cloudflare Pages over Netlify, you can replace the preview workflow with:

```yaml
- uses: cloudflare/pages-action@v1
  with:
    apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
    accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
    projectName: stoker
    directory: .
```

The setup process is similar - you'll need to create a Cloudflare account and get API credentials.
