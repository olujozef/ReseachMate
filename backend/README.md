# ResearchMate Backend

Backend API proxy for ResearchMate. Securely proxies Claude API calls so users don't need to provide their own API keys.

## Local Development

### Prerequisites
- Node.js 18+
- Anthropic API key from [console.anthropic.com](https://console.anthropic.com)

### Setup

1. Copy `.env.example` to `.env`:
```bash
cp .env.example .env
```

2. Add your Anthropic API key to `.env`:
```
ANTHROPIC_API_KEY=sk-ant-your-key-here
```

3. Install dependencies:
```bash
npm install
```

4. Start the server:
```bash
npm start
```

Server runs at `http://localhost:3000`

## API Endpoints

### Health Check
```
GET /health
```

Returns status of the server.

### Claude API Proxy
```
POST /api/claude
```

Accepts requests in this format:
```json
{
  "system": "You are a helpful assistant.",
  "messages": [
    { "role": "user", "content": "Hello" }
  ],
  "max_tokens": 1000
}
```

Returns Claude's response with content, usage, and stop_reason.

## Deployment to Render

### Step 1: Push to GitHub
Make sure your backend code is committed to the `backend` branch.

### Step 2: Create Render Service
1. Go to [render.com](https://render.com)
2. Sign up or log in
3. Click **New +** → **Web Service**
4. Select your `ResearchMate` repository
5. Configure:
   - **Name:** `researchmate-backend` (or any name)
   - **Root Directory:** `backend`
   - **Build Command:** `npm install`
   - **Start Command:** `npm start`
   - **Plan:** Free (or upgrade as needed)

### Step 3: Add Environment Variable
1. In the Render dashboard, go to your service's **Environment** tab
2. Click **Add Environment Variable**
3. Set:
   - **Key:** `ANTHROPIC_API_KEY`
   - **Value:** Your Anthropic API key (from [console.anthropic.com](https://console.anthropic.com))
4. Click **Save Changes**

### Step 4: Deploy
Click **Deploy**. Render will build and start your service.

Once deployed, you'll get a URL like:
```
https://researchmate-backend.onrender.com
```

## Update Frontend

After deploying the backend, update `index.html` in the main branch:

Find this line (around line 94):
```javascript
const BACKEND_URL = null;
```

Replace with:
```javascript
const BACKEND_URL = "https://researchmate-backend.onrender.com/api/claude";
```

Also remove or comment out the API key input from the UI since users won't need it anymore.

## Cost

- **Render Free Tier:** Includes 750 free hours per month (enough for ~2.5 hours/day continuous use)
- **Anthropic API:** You pay based on usage at [claude pricing](https://www.anthropic.com/pricing)

## Troubleshooting

### "ANTHROPIC_API_KEY environment variable is not set"
- Make sure you added the environment variable in Render's dashboard
- Redeploy after adding the variable

### "Rate limited"
- Claude API has rate limits. Wait a moment and retry
- Consider upgrading your Anthropic plan for higher limits

### "Unauthorized: Invalid or expired API key"
- Check your API key is correct in Render's environment variables
- Generate a new key from [console.anthropic.com](https://console.anthropic.com)

## Security

- ✅ API key stored only on Render server (never exposed to frontend)
- ✅ CORS enabled for your domain only
- ✅ `.env` file never committed to Git
- ✅ `node_modules/` ignored
