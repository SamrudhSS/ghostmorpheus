# Deployment Guide: Sentinel 4 (Vercel/Netlify + Railway)

This guide covers deploying **Sentinel 4** to **Vercel or Netlify** (frontend) and **Railway** (backend) without Docker.

---

## 🏗️ Architecture

- **Frontend**: Static HTML/JS dashboard (Vercel/Netlify)
- **Backend**: Python asyncio service (Railway)
- **Database**: PostgreSQL (Railway)
- **Simulation Server**: Node.js (Railway or local)

---

## 🚀 Quick Start

### Prerequisites
- GitHub account (for repository hosting)
- Vercel or Netlify account (for frontend)
- Railway account (for backend + database)
- Groq API key (for LLM diagnostics)
- (Optional) Twilio account (for voice alerts)

---

## Phase 1: Prepare Repository

### 1.1 Push to GitHub
Ensure your repository is pushed to GitHub with the following structure:
```
Sentinel4/
├── frontend/          # Static files (HTML, JS, CSS)
├── app/              # Python modules
├── main.py          # Entry point
├── config.py        # Configuration
├── requirements.txt # Python dependencies
├── .env.example     # Environment template
├── vercel.json      # Vercel configuration
├── railway.json     # Railway configuration
└── malendau-hackathon/  # Simulation server (optional)
```

### 1.2 Verify .env.example
Your `.env.example` should document all required variables:
```
GROQ_API_KEY=
SIM_SERVER_URL=
TWILIO_ACCOUNT_SID=
TWILIO_AUTH_TOKEN=
TWILIO_FROM_NUMBER=
TWILIO_TO_NUMBER=
EMAIL_SMTP_SERVER=
EMAIL_SMTP_PORT=
EMAIL_SENDER=
EMAIL_PASSWORD=
EMAIL_RECEIVER=
```

---

## Phase 2: Deploy Backend to Railway

Railway auto-detects Python projects and installs dependencies from `requirements.txt`.

### 2.1 Create Railway Project
1. Go to [railway.app](https://railway.app)
2. Sign up with GitHub
3. Click **Create New Project** → **Deploy from GitHub repo**
4. Select your `Sentinel4` repository
5. Railway will detect the Python project automatically

### 2.2 Add PostgreSQL Plugin
1. In your Railway project, click **+ Add**
2. Select **PostgreSQL**
3. Railway auto-generates a `DATABASE_URL` environment variable

### 2.3 Configure Environment Variables
In Railway's **Variables** tab, add all required variables:

```
# Groq LLM
GROQ_API_KEY=your_groq_key_here

# Simulation Server (if running on Railway)
SIM_SERVER_URL=https://your-sim-server.railway.app

# Voice Alerts (optional)
TWILIO_ACCOUNT_SID=your_sid
TWILIO_AUTH_TOKEN=your_token
TWILIO_FROM_NUMBER=+1234567890
TWILIO_TO_NUMBER=+1234567890

# Email Notifications (optional)
EMAIL_SMTP_SERVER=smtp.gmail.com
EMAIL_SMTP_PORT=587
EMAIL_SENDER=your_email@gmail.com
EMAIL_PASSWORD=your_app_password
EMAIL_RECEIVER=supervisor@example.com
```

**Note**: `DATABASE_URL` is auto-populated by the PostgreSQL plugin.

### 2.4 Deploy
Railway auto-deploys on GitHub push. To deploy manually:
```bash
npm install -g railway
railway up
```

Your backend will be available at: `https://your-backend.railway.app`

---

## Phase 3: Deploy Frontend to Vercel

### 3.1 Import Repository
1. Go to [vercel.com](https://vercel.com)
2. Sign up with GitHub
3. Click **Add New → Project**
4. Select your `Sentinel4` repository

### 3.2 Configure Project
- **Framework**: Other (it's a static site)
- **Root Directory**: `frontend`
- **Build Command**: (leave empty)
- **Output Directory**: (leave empty)

### 3.3 Set Environment Variables
In **Settings → Environment Variables**, add:

```
REACT_APP_HISTORY_API_URL=https://your-backend.railway.app/api/history
REACT_APP_SIM_SERVER_URL=https://your-sim-server.railway.app
```

Or if simulation server is local:
```
REACT_APP_SIM_SERVER_URL=http://localhost:3000
```

### 3.4 Deploy
Vercel automatically deploys on Git push. Your frontend will be live at:
```
https://your-project.vercel.app
```

---

## Phase 3 (Alternative): Deploy Frontend to Netlify

### 3.1 Import Repository
1. Go to [netlify.com](https://netlify.com)
2. Sign up with GitHub
3. Click **Add new site → Import an existing project**
4. Select your `Sentinel4` repository

### 3.2 Configure Site
- **Base directory**: `frontend`
- **Build command**: (leave empty)
- **Publish directory**: `./` (or leave empty)

### 3.3 Set Environment Variables
In **Site settings → Build & deploy → Environment**, add:

```
REACT_APP_HISTORY_API_URL=https://your-backend.railway.app/api/history
REACT_APP_SIM_SERVER_URL=https://your-sim-server.railway.app
```

### 3.4 Deploy
```bash
netlify deploy --prod --dir=frontend
```

---

## Phase 4: Deploy Simulation Server (Optional)

If you want to run the Node.js simulation server on Railway:

### 4.1 Create New Railway Service
1. In your Railway project, click **+ Add**
2. Select **Deploy from GitHub**
3. Deploy from the same repo, but use root directory: `malendau-hackathon`

### 4.2 Configure for Node.js
Railway auto-detects `package.json` in the subdirectory.

### 4.3 Set Start Command
Add a variable: `START_CMD=node server.js`

Your simulation server will be available at: `https://your-sim-server.railway.app`

---

## 🔧 Environment Variables Summary

### Railway (Backend)
| Variable | Purpose | Required |
|----------|---------|----------|
| `GROQ_API_KEY` | LLM diagnostics | Yes |
| `SIM_SERVER_URL` | Simulation server URL | Yes |
| `DATABASE_URL` | PostgreSQL (auto-set) | Auto |
| `HISTORY_API_HOST` | Bind address (0.0.0.0) | No |
| `HISTORY_API_PORT` | Port (8010) | No |
| `TWILIO_ACCOUNT_SID` | Twilio SID | Optional |
| `TWILIO_AUTH_TOKEN` | Twilio token | Optional |
| `TWILIO_FROM_NUMBER` | Caller ID | Optional |
| `TWILIO_TO_NUMBER` | Recipient phone | Optional |
| `EMAIL_SMTP_SERVER` | SMTP server | Optional |
| `EMAIL_SENDER` | Email address | Optional |
| `EMAIL_PASSWORD` | Email app password | Optional |
| `EMAIL_RECEIVER` | Alert recipient | Optional |

### Vercel/Netlify (Frontend)
| Variable | Purpose | Required |
|----------|---------|----------|
| `REACT_APP_HISTORY_API_URL` | Backend API base URL | Yes |
| `REACT_APP_SIM_SERVER_URL` | Simulation server URL | Yes |

---

## ✅ Verification Checklist

After deployment:

- [ ] Frontend loads without errors
- [ ] Browser console shows no API connection errors
- [ ] Real-time data updates in dashboard
- [ ] EventSource connections show `200` responses
- [ ] Backend logs show incoming connections
- [ ] (Optional) Voice alerts work if Twilio configured
- [ ] (Optional) Email notifications work if SMTP configured

### Test API Connectivity
```bash
# Test backend is running
curl https://your-backend.railway.app/api/history

# Test simulation server
curl https://your-sim-server.railway.app/alerts
```

---

## 🐛 Troubleshooting

### Frontend shows "Cannot connect"

**Check**:
1. Verify environment variables are set in Vercel/Netlify
2. Verify Railway backend is running: Check build logs
3. Test API directly: `curl https://your-backend.railway.app/api/history`

### Backend won't start

**Check**:
1. Review Railway build logs for Python errors
2. Verify all environment variables are set
3. Check `requirements.txt` includes all dependencies
4. Test locally: `pip install -r requirements.txt && python main.py`

### EventSource connection fails

**Check**:
1. Verify `REACT_APP_SIM_SERVER_URL` is correct
2. Ensure simulation server is running
3. Check for CORS issues in browser console
4. If using Railway for sim server, verify it's deployed

### Database connection errors

**Check**:
1. PostgreSQL plugin is added to Railway project
2. `DATABASE_URL` environment variable is set
3. Database is accessible from backend service

---

## 📚 Additional Resources

- **Railway Docs**: https://docs.railway.app
- **Vercel Docs**: https://vercel.com/docs
- **Netlify Docs**: https://docs.netlify.com
- **Python on Railway**: https://docs.railway.app/reference/start-guide
- **Node.js on Railway**: https://docs.railway.app/reference/nodejs

---

## 🎯 Next Steps

1. **Monitor performance**:
   - Railway: Check logs and metrics in project dashboard
   - Vercel/Netlify: Use analytics to monitor frontend performance

2. **Set up alerts**:
   - Railway: Configure health checks
   - Vercel: Enable email alerts for deployments

3. **Custom domain** (optional):
   - Vercel/Netlify: Add custom domain in settings
   - Railway: Use Railway domain or add custom domain

---

**Status**: Ready for production. Updated April 2026.
