# GitHub Pages Deployment Instructions

Your Vue recipe app has been pushed to GitHub and is configured for automatic deployment to GitHub Pages!

## Repository
- **GitHub URL**: https://github.com/splendiferousnoctifer/myrecipes

## Automatic Deployment

The app is configured to automatically deploy to GitHub Pages whenever you push to the `main` branch.

### Setup GitHub Pages (One-time setup)

1. Go to your repository on GitHub: https://github.com/splendiferousnoctifer/myrecipes
2. Click on **Settings**
3. Scroll down to **Pages** (in the left sidebar under "Code and automation")
4. Under **Source**, select:
   - Source: **GitHub Actions**
5. Save the settings

That's it! The GitHub Actions workflow will automatically:
- Build your Vue app
- Deploy it to GitHub Pages
- Make it available at: **https://splendiferousnoctifer.github.io/myrecipes/**

## View Your Site

Once the deployment is complete (usually 1-2 minutes after pushing), your site will be available at:

🌐 **https://splendiferousnoctifer.github.io/myrecipes/**

## Check Deployment Status

1. Go to the **Actions** tab in your GitHub repository
2. You'll see the "Deploy to GitHub Pages" workflow running
3. Once it shows a green checkmark ✓, your site is live!

## Future Updates

Every time you push changes to the `main` branch, the site will automatically rebuild and redeploy.

```bash
# Make your changes
git add .
git commit -m "Your commit message"
git push

# Wait 1-2 minutes for automatic deployment
```

## Local Development

```bash
npm run dev      # Start dev server at http://localhost:3000
npm run build    # Build for production
npm run preview  # Preview production build
```

## Adding New Recipes

1. Create a new JSON file in `data/` folder
2. Add the filename to `data/recipes-manifest.json`
3. Commit and push
4. The site will automatically update!

---

✨ Your recipe app is now live on GitHub Pages! ✨

