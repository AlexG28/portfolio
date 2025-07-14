# 🚀 GitHub Pages Deployment Guide

Follow these steps to deploy your Hugo website to GitHub Pages:

## Step 1: Create GitHub Repository

1. Go to [GitHub](https://github.com) and sign in
2. Click the "+" icon in the top right and select "New repository"
3. Name it `portfolio` (or your preferred name)
4. Make it **Public** (required for free GitHub Pages)
5. **Don't** initialize with README, .gitignore, or license
6. Click "Create repository"

## Step 2: Push Your Code

Run these commands in your terminal:

```bash
# Add all files to git
git add .

# Commit the changes
git commit -m "Initial commit: Personal portfolio website"

# Add your GitHub repository as remote
git remote add origin https://github.com/YOUR_USERNAME/portfolio.git

# Push to GitHub
git push -u origin main
```

**Replace `YOUR_USERNAME` with your actual GitHub username.**

## Step 3: Enable GitHub Pages

1. Go to your repository on GitHub
2. Click on **Settings** tab
3. Scroll down to **Pages** section (in the left sidebar)
4. Under **Source**, select **GitHub Actions**
5. The workflow will automatically be created from the `.github/workflows/hugo.yml` file

## Step 4: Configure GitHub Pages

1. In the **Pages** settings, make sure:
   - **Source**: GitHub Actions
   - **Branch**: main (should be automatic)
2. Your site will be available at: `https://YOUR_USERNAME.github.io/portfolio/`

## Step 5: Update Base URL (Important!)

After creating the repository, update the base URL in `hugo.toml`:

```toml
baseurl = "https://YOUR_USERNAME.github.io/portfolio/"
```

Replace `YOUR_USERNAME` with your actual GitHub username.

## Step 6: Deploy

1. Commit and push the updated configuration:
   ```bash
   git add hugo.toml
   git commit -m "Update base URL for GitHub Pages"
   git push
   ```

2. Go to the **Actions** tab in your repository
3. You should see the "Deploy Hugo site to Pages" workflow running
4. Wait for it to complete (usually 2-3 minutes)

## Step 7: Verify Deployment

1. Once the workflow completes successfully, your site will be live
2. Visit `https://YOUR_USERNAME.github.io/portfolio/`
3. You should see your website!

## 🔄 Updating Your Site

To update your site:

1. Make changes to your content
2. Commit and push:
   ```bash
   git add .
   git commit -m "Update website content"
   git push
   ```
3. The GitHub Action will automatically rebuild and deploy your site

## 🛠️ Troubleshooting

### Common Issues:

1. **Site not loading**: Check that the base URL in `hugo.toml` matches your repository name
2. **Build failures**: Check the Actions tab for error messages
3. **Images not showing**: Make sure images are in `static/images/` and referenced correctly

### Check Build Status:

1. Go to your repository on GitHub
2. Click **Actions** tab
3. Click on the latest workflow run
4. Check the build logs for any errors

## 📝 Custom Domain (Optional)

To use a custom domain:

1. Buy a domain (e.g., from Namecheap, GoDaddy)
2. In GitHub Pages settings, add your custom domain
3. Update `hugo.toml`:
   ```toml
   baseurl = "https://yourdomain.com/"
   ```
4. Configure DNS settings with your domain provider

## 🎉 Success!

Your Hugo website is now live on GitHub Pages! The site will automatically update whenever you push changes to the main branch.

**Your site URL**: `https://YOUR_USERNAME.github.io/portfolio/` 