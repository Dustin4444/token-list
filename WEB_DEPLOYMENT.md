# Web Deployment Guide

This project now includes a web interface that can be deployed to Vercel with integrated Web Analytics.

## Features

- **Static Landing Page**: A modern, responsive landing page showcasing the Monad token lists
- **Vercel Web Analytics**: Integrated analytics to track visitor engagement
- **JSON API**: Direct access to token list JSON files via web endpoints
- **CORS Support**: Properly configured headers for cross-origin access to token lists

## Project Structure

```
├── public/
│   ├── index.html              # Landing page with Vercel Analytics
│   ├── tokenlist-mainnet.json  # Mainnet token list (copied from root)
│   └── tokenlist-testnet.json  # Testnet token list (copied from root)
├── vercel.json                 # Vercel deployment configuration
└── package.json                # Node.js dependencies (@vercel/analytics)
```

## Vercel Web Analytics Setup

The project uses Vercel Web Analytics to track page views and visitor engagement. The analytics script is integrated using the static HTML method as documented in the [Vercel Analytics Quickstart Guide](https://vercel.com/docs/analytics/quickstart).

### Implementation Details

In `public/index.html`, the analytics is initialized with:

```html
<!-- Vercel Web Analytics -->
<script>
    window.va = window.va || function () { (window.vaq = window.vaq || []).push(arguments); };
</script>
<script defer src="/_vercel/insights/script.js"></script>
```

This implementation:
- Uses the official Vercel analytics script injection method for static HTML
- Loads asynchronously to not block page rendering
- Automatically tracks page views when deployed on Vercel

## Deployment to Vercel

### Prerequisites
1. A Vercel account
2. Vercel CLI installed (`npm i -g vercel`)

### Deployment Steps

1. **Install Dependencies**:
   ```bash
   npm install
   ```

2. **Update Token Lists** (if modified):
   ```bash
   cp tokenlist-mainnet.json public/
   cp tokenlist-testnet.json public/
   ```

3. **Deploy to Vercel**:
   ```bash
   vercel deploy
   ```

4. **Enable Web Analytics in Vercel Dashboard**:
   - Go to your project in the Vercel dashboard
   - Navigate to the "Analytics" tab
   - Enable Web Analytics
   - Analytics data will start appearing after the next deployment

### Automatic Deployment

You can configure GitHub Actions to automatically deploy to Vercel on push:

```yaml
name: Deploy to Vercel
on:
  push:
    branches: [main]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: '18'
      - run: npm install
      - run: cp tokenlist-*.json public/
      - uses: amondnet/vercel-action@v20
        with:
          vercel-token: ${{ secrets.VERCEL_TOKEN }}
          vercel-org-id: ${{ secrets.VERCEL_ORG_ID }}
          vercel-project-id: ${{ secrets.VERCEL_PROJECT_ID }}
```

## Configuration

### vercel.json

The `vercel.json` file configures:

- **Security Headers**: X-Content-Type-Options, X-Frame-Options, X-XSS-Protection
- **CORS Headers**: Allow cross-origin access to JSON files
- **Content-Type**: Proper JSON content type for token list files
- **Rewrites**: Direct access to token list files

### Updating Token Lists

When token lists are updated via the Python scripts:

1. Regenerate the token lists using existing Python scripts
2. Copy updated JSON files to the public directory:
   ```bash
   cp tokenlist-mainnet.json public/
   cp tokenlist-testnet.json public/
   ```
3. Redeploy to Vercel

## Local Development

To test the web interface locally:

```bash
# Install dependencies
npm install

# Copy token lists
cp tokenlist-*.json public/

# Serve the static files (requires npx)
npm run dev
```

Then visit `http://localhost:3000` in your browser.

## Analytics Data

Once deployed and enabled in the Vercel dashboard, you can view:

- Page views and unique visitors
- Traffic sources and referrers
- Geographic distribution of visitors
- Device and browser information

Access analytics at: `https://vercel.com/<your-account>/<project-name>/analytics`

## API Endpoints

The deployed site provides these endpoints:

- `/` - Landing page
- `/tokenlist-mainnet.json` - Mainnet token list
- `/tokenlist-testnet.json` - Testnet token list

## Notes

- The `@vercel/analytics` package is included in dependencies but the actual analytics script is loaded from Vercel's CDN
- Analytics only work when deployed on Vercel (not in local development)
- Token lists must be copied to the `public/` directory before deployment
- The landing page is purely informational and requires no build process
