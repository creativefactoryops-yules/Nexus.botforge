# ⚡ NEXUS AI — Autonomous Intelligence Platform

> Cyberpunk AI SaaS platform with FocusFlow task manager, command center, and Stripe monetization.

---

## 📁 File Structure

```
nexus-ai/
├── public/
│   ├── index.html          ← Main NEXUS AI landing + command center (deploy this)
│   └── focusflow.html      ← FocusFlow task manager (standalone tool)
│
├── api/
│   └── stripe/
│       ├── checkout.js     ← Stripe checkout session creator
│       └── webhook.js      ← Stripe post-payment event handler
│
├── schema.ts               ← Drizzle ORM schema (DB tables for future upgrade)
├── install-cli.sh          ← Nexus CLI installer script
├── vercel.json             ← Vercel routing + security headers config
├── package.json            ← Dependencies
├── .env.example            ← Environment variable template (copy → .env.local)
├── .gitignore              ← Keeps secrets + junk out of Git
└── README.md               ← This file
```

---

## 🚀 Deploy to Vercel via GitHub (Step by Step)

### Step 1 — GitHub Setup

```bash
# 1. Create a new repo on github.com (click + → New repository → name it "nexus-ai")
# 2. Then in your terminal:

git init
git add .
git commit -m "🚀 Initial NEXUS AI deployment"
git branch -M main
git remote add origin https://github.com/YOUR_USERNAME/nexus-ai.git
git push -u origin main
```

### Step 2 — Connect to Vercel

1. Go to [vercel.com](https://vercel.com) → **Add New Project**
2. Click **Import Git Repository** → select `nexus-ai`
3. Leave all build settings as default (Vercel auto-detects the `public/` folder)
4. Click **Deploy**

Your site will be live at `https://nexus-ai.vercel.app` in ~30 seconds.

---

## 🔑 Environment Variables (Stripe)

### Add to Vercel Dashboard

After deploying, go to:
**Vercel Dashboard → Your Project → Settings → Environment Variables**

Add these one by one:

| Variable | Value |
|----------|-------|
| `STRIPE_SECRET_KEY` | `sk_live_...` (from Stripe Dashboard → API Keys) |
| `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY` | `pk_live_...` |
| `STRIPE_PRICE_INITIATE` | `price_1TMSjz2N6gfdKnyg1WSedvqp` ← your $29 price |
| `STRIPE_PRICE_EXECUTE` | `price_1TMSmn2N6gfdKnygnF0IFy5z` ← your $99 price |
| `STRIPE_WEBHOOK_SECRET` | `whsec_...` (see Webhook Setup below) |
| `NEXT_PUBLIC_APP_URL` | `https://nexus-ai.vercel.app` (your actual Vercel URL) |

**After adding variables → Redeploy:**
Vercel Dashboard → Deployments → ⋯ → Redeploy

### For local development, create `.env.local`:
```bash
cp .env.example .env.local
# Then edit .env.local and fill in your real keys
```

---

## 💳 Stripe Webhook Setup

Stripe needs to notify your app after a payment. Do this once you have your live Vercel URL:

1. Go to [Stripe Dashboard → Developers → Webhooks](https://dashboard.stripe.com/webhooks)
2. Click **Add endpoint**
3. **Endpoint URL:** `https://your-domain.vercel.app/api/stripe/webhook`
4. **Events to listen for:**
   - `checkout.session.completed`
   - `invoice.payment_failed`
   - `customer.subscription.deleted`
   - `customer.subscription.updated`
5. Click **Add endpoint**
6. Copy the **Signing secret** (`whsec_...`) → paste as `STRIPE_WEBHOOK_SECRET` in Vercel

### Test webhooks locally:
```bash
npm install                              # install dependencies
stripe listen --forward-to localhost:3000/api/stripe/webhook
# In another terminal:
npm run dev
```

---

## 🌐 Pages & Routes

| URL | File | Description |
|-----|------|-------------|
| `/` | `public/index.html` | NEXUS AI main landing + command center |
| `/focusflow` | `public/focusflow.html` | FocusFlow task manager |
| `/api/stripe/checkout` | `api/stripe/checkout.js` | POST: creates Stripe checkout session |
| `/api/stripe/webhook` | `api/stripe/webhook.js` | POST: handles Stripe payment events |

---

## 💰 Pricing Tiers (Already Configured in Stripe)

| Tier | Price | Stripe Price ID |
|------|-------|-----------------|
| **INITIATE** | $29/month | `price_1TMSjz2N6gfdKnyg1WSedvqp` |
| **EXECUTE** | $99/month | `price_1TMSmn2N6gfdKnygnF0IFy5z` |

---

## 🛠 Local Development

```bash
# Install dependencies
npm install

# Install Vercel CLI (if not already)
npm i -g vercel

# Run locally (serves public/ + api/ functions)
vercel dev
# → Open http://localhost:3000
```

---

## 🗃 Database Schema (Future Upgrade)

`schema.ts` contains Drizzle ORM table definitions ready for when you add a database:

| Table | Purpose |
|-------|---------|
| `users` | Auth — Manus OAuth users, roles |
| `tasks` | FocusFlow tasks (My Tasks + Business Goals) |
| `memory_bank` | Key-value memory store per user |
| `command_history` | AI command log with status tracking |

**To activate the database:**
1. Create a MySQL database (recommended: [PlanetScale](https://planetscale.com) free tier or [Railway](https://railway.app))
2. Add `DATABASE_URL` to your `.env.local` and Vercel environment variables
3. Install Drizzle: `npm install drizzle-orm mysql2`
4. Run migrations: `npx drizzle-kit push`

---

## 📋 What's Left (from todo.md)

### Immediate (live now without these):
- ✅ Landing page with hero, features, pricing
- ✅ Command center UI
- ✅ FocusFlow task manager
- ✅ Stripe checkout API
- ✅ Vercel deployment config

### Phase 2 (add when ready):
- [ ] Connect real database (PlanetScale/Neon recommended)
- [ ] Manus OAuth login flow
- [ ] Real AI command execution via API
- [ ] Stripe webhook → DB user tier update
- [ ] Memory bank backend

### Phase 3 (growth):
- [ ] Email sequences (SendGrid/Resend)
- [ ] Analytics (Vercel Analytics — free, one click)
- [ ] Custom domain
- [ ] A/B test pricing page

---

## 🔄 Updating After Deployment

Every push to `main` auto-deploys to Vercel:
```bash
# Make changes, then:
git add .
git commit -m "Your change description"
git push
# Vercel auto-deploys in ~20 seconds
```

---

## 🆘 Troubleshooting

| Problem | Fix |
|---------|-----|
| Site shows 404 | Check `vercel.json` exists and `outputDirectory` is `public` |
| Stripe checkout fails | Verify `STRIPE_SECRET_KEY` is set in Vercel env vars and you redeployed |
| Webhook not firing | Make sure endpoint URL is exactly `/api/stripe/webhook` and `STRIPE_WEBHOOK_SECRET` matches |
| Env vars not working | After adding in Vercel dashboard, you must Redeploy |
| Local dev broken | Run `npm install` first, then `vercel dev` (not `npm run dev`) |

---

## 📞 Key Links

- **Stripe Dashboard:** https://dashboard.stripe.com
- **Vercel Dashboard:** https://vercel.com/dashboard
- **Vercel Env Vars:** https://vercel.com/docs/environment-variables
- **Stripe Webhooks:** https://dashboard.stripe.com/webhooks
- **Drizzle ORM Docs:** https://orm.drizzle.team/docs/get-started-mysql

---

*Built with ❤️ — NEXUS AI v2.0.4*
