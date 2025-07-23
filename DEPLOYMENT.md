# Deployment Guide

This guide will help you deploy the Miruro anime streaming application to various platforms.

## Prerequisites

1. **Environment Variables**: You need to set up the following environment variables for the application to work properly:

   ```env
   VITE_BACKEND_URL=https://public-miruro-consumet-api.vercel.app/
   VITE_CLIENT_ID=your_anilist_client_id
   VITE_CLIENT_SECRET=your_anilist_client_secret
   VITE_REDIRECT_URI=https://yourdomain.com/callback
   VITE_DEPLOY_PLATFORM=VERCEL  # or CLOUDFLARE
   VITE_SKIP_TIMES=https://api.aniskip.com/
   VITE_PROXY_URL=https://corsproxy.io/
   VITE_API_KEY=your_optional_api_key
   VITE_GA_MEASUREMENT_ID=your_google_analytics_id
   ```

2. **AniList OAuth Application**: Create an OAuth application at [AniList Developer Settings](https://anilist.co/settings/developer) to get your client ID and secret.

## Deployment Options

### 1. Vercel Deployment

**Automatic Deployment:**
1. Fork this repository
2. Connect your GitHub account to Vercel
3. Import the project and set the environment variables
4. Set `VITE_DEPLOY_PLATFORM=VERCEL`
5. Deploy!

**Manual Deployment:**
```bash
npm install -g vercel
npm run build
vercel --prod
```

**Important Configuration:**
- The `vercel.json` file is already configured
- Make sure to set all environment variables in the Vercel dashboard
- The API function will be available at `/api/exchange-token`

### 2. Cloudflare Pages Deployment

**Automatic Deployment:**
1. Fork this repository
2. Connect your GitHub account to Cloudflare Pages
3. Set the build settings:
   - Build command: `npm run build`
   - Build output directory: `dist`
4. Set `VITE_DEPLOY_PLATFORM=CLOUDFLARE`
5. Configure environment variables in Cloudflare dashboard
6. Deploy!

**Manual Deployment:**
```bash
npm install -g wrangler
npm run build
wrangler pages publish dist
```

**Important Configuration:**
- The `wrangler.toml` file is already configured
- Environment variables should be set in Cloudflare dashboard
- The API function will be available at `/exchange-token`

### 3. Self-Hosted Deployment

**Using Node.js/Express:**
```bash
npm install
npm run build
npm start
```

**Using Docker:**
```dockerfile
FROM node:20-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build
EXPOSE 5173
CMD ["npm", "start"]
```

**Using a Reverse Proxy (Nginx):**
```nginx
server {
    listen 80;
    server_name yourdomain.com;

    location / {
        proxy_pass http://localhost:5173;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

## Environment Variable Details

| Variable | Required | Description |
|----------|----------|-------------|
| `VITE_BACKEND_URL` | Yes | API backend URL for anime data |
| `VITE_CLIENT_ID` | Yes* | AniList OAuth client ID |
| `VITE_CLIENT_SECRET` | Yes* | AniList OAuth client secret |
| `VITE_REDIRECT_URI` | Yes* | OAuth redirect URI |
| `VITE_DEPLOY_PLATFORM` | Yes | Platform type: VERCEL or CLOUDFLARE |
| `VITE_SKIP_TIMES` | No | Skip times API URL |
| `VITE_PROXY_URL` | No | CORS proxy URL |
| `VITE_API_KEY` | No | Optional API key |
| `VITE_GA_MEASUREMENT_ID` | No | Google Analytics ID |

*Required only if you want AniList login functionality

## Common Deployment Issues and Solutions

### 1. API Functions Not Working
- **Vercel**: Make sure the `vercel.json` file is properly configured
- **Cloudflare**: Ensure the `functions/` directory is present and `wrangler.toml` is configured
- **Self-hosted**: Check that the Express server is running and API endpoint is accessible

### 2. Environment Variables Not Loading
- Make sure all `VITE_*` prefixed variables are set in your deployment platform
- For local development, use `.env` or `.env.local` files
- Variables should be available at build time for Vite

### 3. CORS Issues
- All API functions now include proper CORS headers
- If you encounter CORS issues, check your proxy configuration
- Make sure the `VITE_PROXY_URL` is set if needed

### 4. OAuth Redirect Issues
- Ensure `VITE_REDIRECT_URI` matches your domain
- Update AniList OAuth application settings with the correct redirect URI
- Set `VITE_DEPLOY_PLATFORM` to match your deployment target

### 5. Build Failures
- Make sure all dependencies are installed: `npm install`
- Check Node.js version compatibility (requires Node.js 18+)
- Verify TypeScript compilation: `npm run build`

## Testing Your Deployment

1. **Basic Functionality**: Visit your deployed URL and check if the home page loads
2. **API Functions**: Test the OAuth flow by trying to log in with AniList
3. **Anime Search**: Search for anime to test API connectivity
4. **Video Playback**: Try watching an episode to test streaming functionality

## Performance Optimization

1. **CDN Configuration**: Enable CDN for static assets
2. **Caching**: Configure proper cache headers for assets
3. **Compression**: Enable gzip/brotli compression
4. **Image Optimization**: Consider using WebP images where supported

## Security Considerations

1. Never expose your `VITE_CLIENT_SECRET` in client-side code
2. Use environment variables for all sensitive configuration
3. Enable HTTPS for production deployments
4. Consider implementing rate limiting for API endpoints
5. Keep dependencies updated regularly

## Support

If you encounter deployment issues:
1. Check the browser console for errors
2. Verify all environment variables are set correctly
3. Test the build process locally first
4. Review the platform-specific documentation
5. Open an issue on GitHub with detailed error logs