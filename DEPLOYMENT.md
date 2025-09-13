# Deployment Guide

This guide covers different ways to deploy your Bengali Image Analyzer application.

## Prerequisites

1. **Install Git** (if not already installed):
   - Download from https://git-scm.com/download/win
   - Or use GitHub Desktop: https://desktop.github.com/

2. **GitHub Account**: Sign up at https://github.com

## Step 1: Push to GitHub

### Initialize Git Repository (if not already done)
```bash
git init
git add .
git commit -m "Initial commit"
```

### Create GitHub Repository
1. Go to https://github.com/new
2. Repository name: `bengali-image-analyzer` (or your preferred name)
3. Description: "Bengali Image Analyzer - AI-powered image analysis for Gemini prompt generation"
4. Make it **Public** (required for free GitHub Pages)
5. Click "Create repository"

### Connect and Push
```bash
# Replace YOUR_USERNAME with your GitHub username
git remote add origin https://github.com/YOUR_USERNAME/bengali-image-analyzer.git
git branch -M main
git push -u origin main
```

## Deployment Options

### Option 1: Full-Stack Deployment (Recommended)

**For Frontend + Backend together:**

#### A) Vercel (Easy, Free Tier Available)
1. Go to https://vercel.com
2. Sign in with GitHub
3. Import your repository
4. Vercel will auto-detect it as a Node.js project
5. Add environment variables in Vercel dashboard:
   - `GOOGLE_STUDIO_KEY`: Your Google Studio API key
   - `NODE_ENV`: `production`
6. Deploy!

#### B) Railway (Good for Full-Stack)
1. Go to https://railway.app
2. Sign in with GitHub
3. Click "New Project" → "Deploy from GitHub repo"
4. Select your repository
5. Add environment variables:
   - `GOOGLE_STUDIO_KEY`: Your Google Studio API key
   - `PORT`: `3000`
   - `NODE_ENV`: `production`
6. Railway will auto-deploy

#### C) Render (Free Tier Available)
1. Go to https://render.com
2. Sign in with GitHub
3. Click "New" → "Web Service"
4. Connect your GitHub repository
5. Configuration:
   - **Build Command**: `npm run install:all && npm run build`
   - **Start Command**: `npm start`
   - **Environment**: Add `GOOGLE_STUDIO_KEY`
6. Deploy!

### Option 2: Frontend Only (GitHub Pages)

**Note**: This approach requires a separate backend deployment.

#### Enable GitHub Pages
1. In your GitHub repository, go to **Settings**
2. Scroll to **Pages** section
3. Source: **GitHub Actions**
4. The workflow in `.github/workflows/deploy.yml` will handle the rest

Your frontend will be available at: `https://YOUR_USERNAME.github.io/bengali-image-analyzer`

#### Deploy Backend Separately
For the backend, use any of these services:
- **Railway**: Easy Node.js deployment
- **Render**: Free tier available
- **Heroku**: Classic choice (paid)
- **DigitalOcean App Platform**: Good performance

### Option 3: Container Deployment (Advanced)

Create `Dockerfile` in project root:
```dockerfile
FROM node:18-alpine

WORKDIR /app

# Copy package files
COPY package*.json ./
COPY frontend/package*.json ./frontend/
COPY backend/package*.json ./backend/

# Install dependencies
RUN npm run install:all

# Copy source code
COPY . .

# Build frontend
RUN npm run build

# Expose port
EXPOSE 3000

# Start server
CMD ["npm", "start"]
```

Deploy to:
- **Docker Hub + DigitalOcean**: Professional setup
- **Google Cloud Run**: Serverless containers
- **AWS ECS**: Enterprise-grade

## Environment Variables Setup

For any deployment platform, you'll need:

| Variable | Value | Required |
|----------|-------|----------|
| `GOOGLE_STUDIO_KEY` | Your Google Studio API key | ✅ Yes |
| `NODE_ENV` | `production` | ✅ Yes |
| `PORT` | `3000` (or platform default) | ❌ No |
| `RATE_LIMIT_MAX` | `60` | ❌ No |
| `RATE_LIMIT_WINDOW_MS` | `60000` | ❌ No |
| `LOG_LEVEL` | `info` | ❌ No |

## Post-Deployment Steps

1. **Test the Application**:
   - Upload an image
   - Verify analysis works
   - Check API responses

2. **Configure Custom Domain** (Optional):
   - Most platforms offer custom domain support
   - Update CORS settings if needed

3. **Monitor Performance**:
   - Check logs for errors
   - Monitor API usage
   - Set up alerts if available

## Troubleshooting

### Common Issues

**Build Failures**:
- Ensure Node.js version is 18+
- Check all dependencies are listed in package.json
- Verify environment variables are set

**CORS Errors**:
- Update `FRONTEND_URL` environment variable
- Check allowed origins in backend CORS configuration

**API Key Issues**:
- Verify Google Studio API key is correct
- Check API key has proper permissions
- Ensure key is not expired

**Image Analysis Failures**:
- Check Google AI API quotas
- Verify billing is set up for Google Studio
- Test with smaller images first

## Security Best Practices

1. **Never commit** `.env` files or API keys to GitHub
2. Use environment variables for all sensitive data
3. Enable **branch protection** rules on GitHub
4. Set up **dependabot** for security updates
5. Regular **security audits**: `npm audit`

## Continuous Deployment

The included GitHub Actions workflow will:
- ✅ Auto-build on every push to main
- ✅ Run tests (when added)
- ✅ Deploy to GitHub Pages automatically
- ✅ Handle build errors gracefully

## Support

If you encounter issues:
1. Check the deployment platform's logs
2. Verify environment variables
3. Test locally first: `npm run dev`
4. Check GitHub Actions logs for build errors

---

**Choose your deployment method and follow the corresponding steps above!**