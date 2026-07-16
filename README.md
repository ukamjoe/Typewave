# TypeWave — Combined App (Auth + Paywall + Video Annotator)

Everything from both feature builds merged into one structure: Google auth,
Stripe/Paystack upgrade flow, and the AI video action annotator gated behind Pro.

## Structure
```
sql/01_auth_and_billing.sql          → profiles table, run first
sql/02_video_annotations.sql         → video_annotations table, run second

react/src/lib/supabase.js            → Supabase client
react/src/lib/extractFrames.js       → client-side frame grabber
react/src/context/AuthContext.jsx    → auth state, Google sign-in, isPro flag
react/src/app/auth/callback/route.js → OAuth callback

react/src/components/LoginButton.jsx
react/src/components/ProtectedFeature.jsx   → generic Pro-gate wrapper
react/src/components/UpgradeModal.jsx       → Stripe/Paystack picker
react/src/components/VideoAnnotator.jsx     → annotator UI, wrapped in ProtectedFeature
react/src/components/Dashboard.jsx          → the page that ties it all together

react/api/checkout/stripe/route.js
react/api/checkout/paystack/route.js
react/api/webhooks/stripe/route.js
react/api/webhooks/paystack/route.js
react/api/analyze-video/route.js            → Claude vision call, the annotator's brain

static/typewave-app.html             → same thing, single-file, no build step
```

## What's gated behind Pro vs free
- **Free**: sign in, upload, transcribe, capped minutes, copy-text-only export
- **Pro**: full export formats, unlimited minutes, priority processing, **and the AI
  action annotator** — gating this one matters most since every "Suggest" click is a
  real Claude API cost, not just a UI convenience feature.

## Setup order
1. Run both SQL files in Supabase, in order (`01` then `02` — `02` doesn't depend on
   `01` but keeping the order avoids confusion later).
2. Enable Google in Supabase Authentication > Providers (needs Google Cloud OAuth
   credentials — see the earlier auth README for the exact steps if you still have it).
3. Env vars (Vercel project settings):
   ```
   NEXT_PUBLIC_SUPABASE_URL
   NEXT_PUBLIC_SUPABASE_ANON_KEY
   SUPABASE_SERVICE_ROLE_KEY
   STRIPE_SECRET_KEY
   STRIPE_PRICE_ID
   STRIPE_WEBHOOK_SECRET
   PAYSTACK_SECRET_KEY
   PAYSTACK_PLAN_CODE        (optional)
   ANTHROPIC_API_KEY
   NEXT_PUBLIC_SITE_URL
   ```
4. Add `@anthropic-ai/sdk` to `package.json` dependencies (edit via GitHub web editor).
5. Drop the `react/` files into matching paths in your Typewave repo. `Dashboard.jsx`
   is meant to be your `app/dashboard/page.js` — adjust the import paths if you place
   components elsewhere.
6. Set up the Stripe and Paystack webhook endpoints pointing at
   `/api/webhooks/stripe` and `/api/webhooks/paystack` (see dashboards for each).
7. Redeploy.

## If you're keeping the static HTML version alongside the React app
`static/typewave-app.html` is the same flow condensed into one file — paste it into
your existing static page before `</body>`. It still calls the same `/api/*` routes,
so both versions can share one Vercel deployment and one set of secrets.

## What changed from the two separate builds
- `VideoAnnotator.jsx` now wraps its own UI in `ProtectedFeature` internally, so you
  can drop `<VideoAnnotator />` anywhere and it handles its own gating — no need to
  wrap it yourself at the call site.
- `Dashboard.jsx` is new: a single page showing nav + auth, upload, transcript export
  (Pro-gated), and the annotator (Pro-gated) all together, matching the layout you
  previewed earlier.
- Everything shares one `AuthProvider`, one `profiles.is_pro` flag, and one upgrade
  modal — a single upgrade unlocks both the export formats and the annotator.
