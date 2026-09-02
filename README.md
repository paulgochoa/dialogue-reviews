# Dialogue Review & Reward System — How It Works

This document explains, in plain language, how the "leave a review, get ₱100 off" system works, where every piece of it lives, and what to do if something breaks. No coding background needed to read this.

If you ever hire a developer to work on this, send them this file first — it'll save hours of them figuring out how everything connects.

---

## The One-Sentence Version

A customer fills out a review form on your website → if they upload a photo, the system automatically creates a real ₱100-off discount code in your Shopify store and shows it to them on the spot.

## Step by Step, What Actually Happens

1. A customer visits **review.dialoguecards.co** and fills out the review form (name, email, star rating, review text, a photo, favorite edition).
2. When they submit it, their answers and photo are saved into your database (a place called **Supabase**, think of it like a private spreadsheet in the cloud).
3. If they included a photo, the system automatically asks Shopify to generate a one-time discount code (like `DIA100-B3BZNN`) worth ₱100 off, valid for 3 months.
4. That code is shown right there on the screen, with a "copy" button, so they can save it immediately.
5. The code is now a real, active discount in your Shopify store — ready to use at checkout.

Nothing here needs you to do anything manually. It all happens automatically, instantly, for every review with a photo.

---

## Where Everything Lives

| Piece | What it's for | Where to find it |
|---|---|---|
| **The review form itself** | The page customers actually see and fill out | Live at [review.dialoguecards.co](https://review.dialoguecards.co) |
| **The form's source code** | The actual file that builds the page above | [GitHub: paulgochoa/dialogue-reviews](https://github.com/paulgochoa/dialogue-reviews) |
| **Hosting for the form** | The service that keeps the page online 24/7, for free | [Vercel](https://vercel.com/dialogue-team/dialogue-reviews) (project name: `dialogue-reviews`) |
| **The database** | Stores every review, name, email, and photo submitted | [Supabase](https://supabase.com/dashboard/project/ubykaaouxbvyjwjkjjfv) |
| **The discount-code generator** | The automation that talks to Shopify and creates the actual discount code | Also inside Supabase, called `generate-review-discount` |
| **The Shopify app** | The permission-holder that lets the generator create discounts in your store | [Shopify Dev Dashboard → "Perkly"](https://dev.shopify.com/dashboard/173533356/apps/417440825345) |
| **Your domain's address book** | Controls where `review.dialoguecards.co` points to | [Namecheap DNS settings](https://ap.www.namecheap.com/domains/domaincontrolpanel/dialoguecards.co/advancedns) |

Bookmark this table — it's the map to everything.

---

## About the Secret Keys (Important — Please Read)

A few of these services talk to each other using private "keys," basically long passwords that prove they're allowed to talk to each other. You don't need to know what they say, but you should know:

- **Never share these keys in chat, email, or anywhere public.** If a key ever leaks, generate a new one immediately from wherever it lives (Supabase or Shopify).
- The two important ones are the **Shopify Client ID and Client Secret** for the "Perkly" app (stored inside Supabase, under Edge Function secrets — not visible in this document on purpose) and Supabase's own project keys.
- This GitHub repository (where the code lives) is currently **public**, meaning anyone can view the code. That's fine because none of the secret keys are stored in the code itself — but it's worth knowing. If you'd rather it be private, that's a one-click setting change on GitHub (Settings → General → Danger Zone → Change visibility), and I can walk you through it anytime.
- Consider saving copies of your key service logins (Shopify, Supabase, Vercel, GitHub, Namecheap) in a password manager like 1Password or Bitwarden, so you're never locked out.

---

## Your Website Address

`review.dialoguecards.co` is a custom address you own, pointed at the form's hosting (Vercel) through one DNS setting at Namecheap. The technical version, in case you ever need it again:

- **Where:** Namecheap → Domain List → dialoguecards.co → Advanced DNS
- **The record:** a "CNAME" entry — Host: `review`, Value: `cname.vercel-dns.com.`

There's also a backup address that always works no matter what: **dialogue-reviews.vercel.app**

---

## Things Worth Remembering

- **If you ever add new permissions to the Shopify "Perkly" app** (beyond discounts), you'll likely need to uninstall and reinstall it on your store for the new permission to actually take effect — just releasing a new version isn't enough. This tripped us up once already.
- There's an unused "App Automation Token" sitting in the Shopify Dev Dashboard for the Perkly app. It was created as part of troubleshooting but never actually used (a Shopify tooling bug got in the way). It's harmless sitting there, but safe to delete if you want to tidy up.
- Your Shopify subscription had a couple of failed billing attempts around late August — worth double-checking your payment method on file so nothing gets interrupted.

---

## Not Built Yet (Ideas for Later)

These were discussed but intentionally not built yet:

- Automatic follow-up emails asking customers to leave a review (e.g., 7, 10, and 13 days after purchase)
- Verifying your own sending domain with Resend (the email-sending service), which the above would need

---

## If Something Breaks

| Symptom | Where to look first |
|---|---|
| Review form won't load | [Vercel dashboard](https://vercel.com/dialogue-team/dialogue-reviews) — check if the latest deployment succeeded |
| Form loads but discount code never appears | [Supabase → Edge Functions → generate-review-discount → Logs](https://supabase.com/dashboard/project/ubykaaouxbvyjwjkjjfv) — this shows the exact error |
| Discount code generated but says "access denied" | Check the Perkly app's permissions in the [Shopify Dev Dashboard](https://dev.shopify.com/dashboard/173533356/apps/417440825345) — may need reinstalling, see note above |
| `review.dialoguecards.co` doesn't load at all | Check the DNS record is still there in [Namecheap](https://ap.www.namecheap.com/domains/domaincontrolpanel/dialoguecards.co/advancedns) |

---

*Last updated after building and launching the review + discount reward system, including the custom domain and favicon.*
