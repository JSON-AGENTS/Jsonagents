# Cloudflare Pages Deployment Guide

This guide explains how to deploy the JSON Agents website to Cloudflare Pages with Turborepo.

## Prerequisites

- Cloudflare account
- GitHub repository connected
- Cloudflare API Token (for GitHub Actions)

## Deployment Methods

### Method 1: Cloudflare Pages Dashboard (Easiest)

1. **Go to Cloudflare Dashboard:**
   - Visit https://dash.cloudflare.com
   - Navigate to **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**

2. **Connect Repository:**
   - Select `Traves-Theberge/Jsonagents`
   - Click **Begin setup**

3. **Configure Build Settings:**
   ```
   Project name: jsonagents (or your preferred name)
   Production branch: main
   Build command: npm run build -- --filter=@json-agents/website
   Build output directory: apps/website/out
   Root directory: (leave empty)
   ```

4. **Environment Variables:**
   ```
   NODE_VERSION = 18
   ```

5. **Save and Deploy** - Cloudflare will automatically deploy on every push to main

### Method 2: GitHub Actions (Automated CI/CD)

The repository includes a GitHub Actions workflow (`.github/workflows/deploy-cloudflare.yml`) for automated deployments.

**Setup Steps:**

1. **Get Cloudflare Credentials:**
   - Go to Cloudflare Dashboard → My Profile → API Tokens
   - Create a token with **Cloudflare Pages** permissions
   - Note your Account ID from the URL: `dash.cloudflare.com/<ACCOUNT_ID>`

2. **Add GitHub Secrets:**
   - Go to your GitHub repo → Settings → Secrets and variables → Actions
   - Add the following secrets:
     ```
     CLOUDFLARE_API_TOKEN = your_api_token
     CLOUDFLARE_ACCOUNT_ID = your_account_id
     ```

3. **Deploy:**
   - Push to `main` branch or manually trigger the workflow
   - GitHub Actions will automatically build and deploy

### Method 3: CLI Deployment (Manual)

For manual deployments using the CLI:

1. **Install Wrangler:**
   ```bash
   npm install -g wrangler
   ```

2. **Login to Cloudflare:**
   ```bash
   wrangler login
   ```

3. **Deploy from root:**
   ```bash
   # Build the website
   npm run build -- --filter=@json-agents/website
   
   # Deploy to Cloudflare Pages
   wrangler pages deploy apps/website/out --project-name=jsonagents
   ```

## Turborepo Configuration

The deployment leverages Turborepo's caching and filtering:

- **Build command:** `npm run build -- --filter=@json-agents/website`
  - Only builds the website app and its dependencies
  - Uses Turbo's cache for faster builds
  - Automatically handles workspace dependencies

- **Output directory:** `apps/website/out`
  - Next.js static export
  - Optimized for Cloudflare Pages

## Next.js Configuration

The website is configured for static export (`output: 'export'` in `next.config.ts`):
- Pre-rendered at build time
- No server-side runtime required
- Perfect for Cloudflare Pages edge deployment

## Custom Domains

After deployment, you can add a custom domain:
1. Go to your Pages project → Custom domains
2. Add your domain (e.g., `jsonagents.org`)
3. Update DNS records as instructed

## Troubleshooting

### Build Fails
- Ensure Node version is 18+
- Check that all dependencies are in package.json
- Verify the build works locally: `npm run build -- --filter=@json-agents/website`

### Wrong Directory
- Ensure build output is in `apps/website/out`
- Check `next.config.ts` has `output: 'export'`

### Monorepo Issues
- Use `--filter=@json-agents/website` to target only the website
- Ensure root package.json has the workspace configured
- Run `npm install` from the root directory

## Resources

- [Cloudflare Pages Documentation](https://developers.cloudflare.com/pages/)
- [Next.js Static Export](https://nextjs.org/docs/app/building-your-application/deploying/static-exports)
- [Turborepo Filtering](https://turbo.build/repo/docs/core-concepts/monorepos/filtering)
