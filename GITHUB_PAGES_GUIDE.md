# dourDe - GitHub Pages Deployment Guide

## Complete Step-by-Step Formula for Free Hosting (No Domain Purchase Required)

This guide walks you through deploying **dourDe** to GitHub Pages for completely free hosting without purchasing any domain. Your site will be live at `https://yourusername.github.io/dourDe/`

---

## Phase 1: Prepare Your Local Repository

### Step 1: Initialize Git Repository

```bash
cd /home/ubuntu/dourDe

# Initialize git if not already done
git init

# Add all files
git add .

# Create initial commit
git commit -m "Initial commit: dourDe multi-agentic LLM OS with Banglish support"
```

### Step 2: Create `.gitignore`

```bash
cat > .gitignore << 'EOF'
# Dependencies
node_modules/
pnpm-lock.yaml
yarn.lock
package-lock.json

# Build outputs
dist/
build/
.next/

# Environment
.env
.env.local
.env.*.local

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Logs
*.log
npm-debug.log*
yarn-debug.log*
EOF

git add .gitignore
git commit -m "Add .gitignore"
```

### Step 3: Update `package.json` for GitHub Pages

Edit `package.json` and add the `homepage` field:

```json
{
  "name": "dourDe",
  "version": "1.0.0",
  "homepage": "https://yourusername.github.io/dourDe/",
  "type": "module",
  "scripts": {
    "dev": "vite --host",
    "build": "vite build",
    "preview": "vite preview --host",
    "deploy": "npm run build && gh-pages -d dist"
  },
  ...
}
```

Replace `yourusername` with your actual GitHub username.

### Step 4: Install GitHub Pages Deployment Tool

```bash
cd /home/ubuntu/dourDe
npm install --save-dev gh-pages
# or
pnpm add -D gh-pages
```

---

## Phase 2: Create GitHub Repository

### Step 1: Create New Repository on GitHub

```bash
# Create a new private repository (recommended)
gh repo create dourDe --private --source=. --remote=origin --push
```

**Alternative: Create via GitHub Web Interface**

1. Go to https://github.com/new
2. Repository name: `dourDe`
3. Description: "Multi-agentic LLM OS with Banglish support and Windows 98 retro UI"
4. Choose "Private" (recommended for security)
5. Click "Create repository"

### Step 2: Add Remote and Push

```bash
cd /home/ubuntu/dourDe

# If you created via web interface, add the remote
git remote add origin https://github.com/yourusername/dourDe.git
git branch -M main
git push -u origin main
```

---

## Phase 3: Configure GitHub Pages

### Step 1: Enable GitHub Pages

1. Go to your repository: `https://github.com/yourusername/dourDe`
2. Click **Settings** (top right)
3. Left sidebar → **Pages**
4. Under "Source", select **Deploy from a branch**
5. Branch: Select `gh-pages`
6. Folder: Select `/ (root)`
7. Click **Save**

### Step 2: Create GitHub Actions Workflow (Automated Deployment)

Create `.github/workflows/deploy.yml`:

```bash
mkdir -p .github/workflows

cat > .github/workflows/deploy.yml << 'EOF'
name: Deploy to GitHub Pages

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v3
      
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: '18'
          cache: 'pnpm'
      
      - name: Install pnpm
        run: npm install -g pnpm
      
      - name: Install dependencies
        run: pnpm install
      
      - name: Build
        run: pnpm build
      
      - name: Deploy to GitHub Pages
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
EOF

git add .github/workflows/deploy.yml
git commit -m "Add GitHub Pages deployment workflow"
git push origin main
```

---

## Phase 4: Build and Deploy

### Step 1: Build the Project Locally

```bash
cd /home/ubuntu/dourDe
pnpm install
pnpm build
```

This creates a `dist/` folder with all static files.

### Step 2: Deploy to GitHub Pages

**Option A: Automatic (via GitHub Actions)**

Simply push to `main` branch:

```bash
git push origin main
```

GitHub Actions will automatically:
1. Install dependencies
2. Build the project
3. Deploy to `gh-pages` branch
4. Publish to GitHub Pages

**Option B: Manual Deployment**

```bash
cd /home/ubuntu/dourDe
pnpm run build
pnpm run deploy
```

### Step 3: Verify Deployment

1. Go to your repository Settings → Pages
2. You should see: "Your site is live at https://yourusername.github.io/dourDe/"
3. Wait 1-2 minutes for the site to be available
4. Visit the URL in your browser

---

## Phase 5: Custom Domain (Optional, No Purchase Needed)

If you want to use a custom domain you already own:

### Step 1: Configure DNS

In your domain registrar's DNS settings, add:

```
CNAME yourusername.github.io
```

### Step 2: Add Domain to GitHub Pages

1. Repository Settings → Pages
2. Under "Custom domain", enter your domain name
3. Click **Save**
4. GitHub will create a `CNAME` file automatically

---

## Phase 6: Continuous Deployment Setup

### Automatic Updates

Every time you push to `main`:

```bash
cd /home/ubuntu/dourDe

# Make changes
# Edit files...

# Commit and push
git add .
git commit -m "Update: Add new feature"
git push origin main
```

GitHub Actions will automatically rebuild and deploy within 2-3 minutes.

---

## Troubleshooting

### Issue: Site Not Showing

**Solution:**
1. Check GitHub Actions: Repository → Actions → Check for failed workflows
2. Verify `homepage` in `package.json` matches your URL
3. Wait 5 minutes for GitHub Pages to update
4. Hard refresh browser (Ctrl+Shift+R or Cmd+Shift+R)

### Issue: 404 Errors on Subpages

**Solution:**

Create `public/404.html`:

```html
<!DOCTYPE html>
<html>
  <head>
    <script>
      sessionStorage.redirect = location.href;
    </script>
    <meta http-equiv="refresh" content="0;URL='/dourDe/'"></meta>
  </head>
  <body>
  </body>
</html>
```

And update `vite.config.ts`:

```typescript
export default {
  base: '/dourDe/',
  // ... rest of config
}
```

### Issue: Assets Not Loading

**Solution:**

Ensure all image/asset URLs use the correct base path:

```jsx
// ❌ Wrong
<img src="/image.png" />

// ✅ Correct
<img src="/dourDe/image.png" />
```

Or use relative paths:

```jsx
<img src="./image.png" />
```

---

## File Structure for GitHub Pages

```
dourDe/
├── .github/
│   └── workflows/
│       └── deploy.yml          # Automated deployment
├── client/
│   ├── src/
│   │   ├── pages/
│   │   │   ├── Home.tsx
│   │   │   ├── Marketplace.tsx
│   │   │   └── Cosmic.tsx
│   │   ├── lib/
│   │   │   ├── banglish.ts
│   │   │   └── agents.ts
│   │   └── index.css
│   ├── index.html
│   └── public/
├── dist/                        # Generated on build (deployed to GitHub Pages)
├── package.json
├── vite.config.ts
├── CNAME                        # Created by GitHub Pages (if custom domain)
└── README.md
```

---

## Revenue Mechanisms (Local Money-Making)

### 1. Cryptocurrency Wallet Integration

Users can connect their crypto wallet to:
- Buy premium agents from marketplace
- Earn from shared prompts
- Receive affiliate commissions

### 2. Agent Monetization

Create agents and list on marketplace:
- Set price in BTC/ETH
- Earn 70% commission (30% platform fee)
- Track earnings in dashboard

### 3. Affiliate Links

Embed contextual affiliate links:
- AI tools and services
- Crypto exchanges
- Learning platforms
- Hosting services

### 4. Premium Features (Future)

- Advanced astrology readings
- Custom agent creation tools
- API access for developers
- White-label solutions

---

## Automated Local LLM Setup

### Running Ollama Locally

```bash
# Install Ollama (macOS/Linux/Windows)
# From: https://ollama.ai

# Download a model
ollama pull llama2
ollama pull mistral

# Start Ollama server
ollama serve

# In dourDe, connect to local LLM
# API endpoint: http://localhost:11434
```

### Supported Models

- **llama2**: 7B, 13B, 70B variants
- **mistral**: Fast and efficient
- **neural-chat**: Optimized for conversations
- **dolphin-mixtral**: Advanced reasoning

---

## Summary: From Zero to Live in 10 Minutes

```bash
# 1. Initialize repository
cd /home/ubuntu/dourDe
git init
git add .
git commit -m "Initial commit"

# 2. Create GitHub repo
gh repo create dourDe --private --source=. --remote=origin --push

# 3. Enable GitHub Pages
# Go to Settings → Pages → Deploy from branch (gh-pages)

# 4. Add workflow
mkdir -p .github/workflows
# Create deploy.yml (see Phase 3, Step 2)

# 5. Push to trigger deployment
git push origin main

# 6. Visit your site
# https://yourusername.github.io/dourDe/
```

---

## Next Steps

1. **Customize Branding**: Update colors, fonts, and agent names
2. **Add More Agents**: Create specialized agents for your use case
3. **Integrate Local LLM**: Connect Ollama for local model execution
4. **Set Up Marketplace**: Configure cryptocurrency payments
5. **Deploy Custom Domain**: Point your domain to GitHub Pages
6. **Monitor Analytics**: Track user engagement and revenue

---

## Resources

- **GitHub Pages Docs**: https://pages.github.com/
- **Vite Documentation**: https://vitejs.dev/
- **React Documentation**: https://react.dev/
- **Ollama**: https://ollama.ai/
- **Crypto Wallets**: https://metamask.io/, https://www.coinbase.com/wallet

---

**Status**: Deployment guide complete ✓  
**Your Site URL**: `https://yourusername.github.io/dourDe/`  
**No domain purchase required** ✓  
**Free hosting forever** ✓
