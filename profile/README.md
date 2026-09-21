<p align="center">
  <img src="https://raw.githubusercontent.com/greenurl/GreenShort/main/gs-files/static/github/GreenShort.png" alt="GreenShort" style="width: 80%; max-width: 280px; height: auto;">
</p>

<h1 align="center">GreenShort</h1>

<p align="center">
  <b>Self-hosted URL shortener that runs entirely on Cloudflare.</b><br>
  No servers. No external databases. No dependencies.
</p>

<p align="center">
  <a href="https://a1.bot.cd/gsw">
    <img src="https://img.shields.io/badge/🌐_Website-a1.bot.cd/gsw-10b981?style=for-the-badge" alt="Website">
  </a>
  <a href="https://github.com/quasvx/GreenShort">
    <img src="https://img.shields.io/badge/📦_Source-GitHub-0c110e?style=for-the-badge&logo=github&logoColor=white" alt="Source">
  </a>
  <a href="https://github.com/quasvx/GreenShort/issues">
    <img src="https://img.shields.io/badge/🐛_Issues-Report-10b981?style=for-the-badge" alt="Issues">
  </a>
</p>

---

## 🌐 Official page

**Website:** [`https://a1.bot.cd/gsw`](https://a1.bot.cd/gsw)

The official presentation page with an overview of GreenShort,
screenshots, feature highlights, and deployment instructions.

<p align="center">
  <img src="https://github.com/greenurl/GreenShort/blob/main/gs-files/static/github/LinksDashboardPreview.png?raw=true" alt="Links Dashboard" width="850" style="border-radius: 12px;">
</p>

---

## 🧩 What is GreenShort?

GreenShort is a URL shortener with an admin dashboard, designed to
be deployed in minutes and operated with zero maintenance. It
generates short links, builds link-in-bio style hubs, tracks clicks
with analytics, and offers password and captcha protection. The
frontend, backend and database all run inside Cloudflare.

It is built for people who want their own shortener without relying
on third-party services, without paying subscriptions, and without
worrying about servers. You only need a Cloudflare account, a domain
(or the free Pages subdomain), and to follow the setup steps.

---

## ✨ Features

**Short links**
Create links with a custom or random slug. Supports nested routes
like `my_brand/promo/summer`. Each link can point to its own
destination URL.

**Root redirect (empty slug)**
Leave the slug empty to redirect the root of a shortener domain.
With splat enabled, every unmatched subpath is forwarded to the
same subpath on the target — turning a short domain into a
transparent proxy. `/anything/here` becomes `target.com/anything/here`.

**Link hubs (link-in-bio)**
Design link-in-bio style pages with multiple buttons, title, bio,
custom profile picture, and color palette. Perfect for Instagram,
TikTok, or any profile where you want to group several destinations.

**Folders**
Organize your links and hubs with folders and subfolders, up to
two levels deep. Move items in bulk, filter by folder, and jump
directly to a folder with a deep link like `?group-promotions`.

**File slugs**
Slugs can include dots inside a segment, so you can create clean
short links that end in file extensions like `/promo/wa.pdf` or
`/assets/imagen.png`.

**Subroutes with splat**
A link can capture everything after the slug and pass it to the
destination. Child links under a splat parent are fully supported,
so `/promo/wa` can be a standalone link while `/promo/anything`
falls back to the parent splat.

**AI-generated slugs**
Enter a URL and GreenShort analyzes the page content (title and
description) to suggest a short, descriptive slug. Uses Workers AI.

**Built-in captcha**
Any link or hub can require a dynamically generated captcha. No
Google reCAPTCHA, no external service. Custom visual challenge,
signed with HMAC and validated with a secure cookie.

**Combined password + captcha**
When a link requires both, the dashboard serves a single combined
page so the visitor only fills one form.

**Password protection**
Protect any link or hub with a password.

**Link expiration**
Configure how long a link stays active: minutes, hours, days, or
"never". Once expired, the link stops working automatically.

**Click analytics**
Every visit is recorded with country, user agent, referrer, IP and
timestamp. Clicks from different versions of the same slug are kept
separate, so recreating a link doesn't mix old data with new clicks.

**QR generation**
Generate QR codes for any link or hub. Customize color, background,
size, margin, error correction level, and center icon. Download as
PNG or copy to clipboard.

**Live hub preview**
While editing a hub, a live preview panel shows exactly how it will
look. Updates as you type, without saving.

**Storage usage indicator**
The header shows how much of your D1 database is used, in real time,
with a selectable unit (Auto, B, KB, MB, GB, TB, PB, EB, ZB, YB).

**Deep links**
Open `?links`, `?hubs`, `?folders` or `?analytics` to jump directly
to a tab. `?action-logout` closes your session. `?group-<folder>`
opens the links view filtered by that folder.

**Multi-language dashboard**
Spanish, English, Russian, Simplified Chinese, and Traditional
Chinese. Language is stored locally per user.

**Dark theme**
Minimal dark design with green accents, optimized for desktop and
mobile.

<p align="center">
  <img src="https://raw.githubusercontent.com/greenurl/GreenShort/refs/heads/main/gs-files/static/github/OurWebsitePreview.png" alt="GreenShort Preview" width="850" style="border-radius: 12px; box-shadow: 0 8px 24px rgba(0,0,0,0.15);">
</p>

---

## ⚙️ How it works

GreenShort runs as a single Cloudflare Pages project with a
catch-all Worker that handles every request.

When someone visits a URL, the Worker checks if it matches a stored
slug. If it does, it applies the corresponding rules (password,
captcha, expiration, splat) and redirects to the destination. If it
doesn't match, it falls through to the static files served by Pages.

Slug resolution is priority-based: the longest matching slug wins.
This makes it possible to have a root fallback (empty slug) that
catches every unmatched path while still keeping specific slugs,
nested routes, and child links working as expected.

All data is stored in a Cloudflare D1 database: one table for links,
one for hub configurations, and one for folders. Clicks are recorded
in Analytics Engine, which is queried via the Cloudflare API to
display statistics in the dashboard.

The admin dashboard authenticates using a single token
(`SITE_TOKEN`), stored in an environment variable.

---

## 🚀 Deploy your own

GreenShort is open source and self-hostable. You can run your own
instance in minutes:

1. Fork or clone the repository.
2. Create a Cloudflare Pages project linked to the repository.
3. Create a D1 database and bind it to the Pages project as `DB`.
4. Create an Analytics Engine dataset named `greenshort` and bind
   it as `ANALYTICS`.
5. Add a Workers AI binding as `AI`.
6. Set the environment variables:
   - `SITE_TOKEN` — the admin password.
   - `CF_ACCOUNT_ID` — your Cloudflare account ID.
   - `CF_D1_ID` — the D1 database ID.
   - `CF_API_TOKEN` — an API token with D1 and Analytics Engine read access.
   - `MAX_SLUG_LENGTH` — optional, defaults to `20`.
   - `MAX_EXPIRATION_DAYS` — optional, defaults to `365`.
   - `AI_MODEL` — optional, the Workers AI model for slug generation.
7. Deploy. The first request will run the database migrations
   automatically.
8. Visit your admin domain and log in with your `SITE_TOKEN`.

That's it. No build step, no external services, no maintenance.

<p align="center">
  <img src="https://raw.githubusercontent.com/greenurl/GreenShort/refs/heads/main/gs-files/static/github/BindingsPreview.png" alt="Bindings Preview" width="850">
</p>

---

## 🤖 Recommended AI model

For the best balance between speed, quality, and cost:

    @cf/moonshot-ai/kimi-k2.5

This model produces clean, short, and contextually accurate slugs.
It is fast, reliable, and works well within the free Workers AI
quota for typical usage.

To configure it:

    AI_MODEL = @cf/moonshot-ai/kimi-k2.5

If you don't set this variable, GreenShort falls back to a default
model automatically.

---

## 🔒 Security

- The admin dashboard uses a single token, stored only in
  localStorage after login.
- All API endpoints require the token as a Bearer header.
- Captchas are signed with HMAC using a per-link secret.
- Password-protected links compare passwords server-side.
- Captcha cookies are HttpOnly, Secure, and SameSite=Lax.
- Reserved routes are validated both on the frontend and the
  backend, so a user cannot create a link that would shadow
  internal routes.
- Admin domains are strictly separated from shortener domains
  through the `DOMAINS` variable.
- Analytics are filtered by a per-link identifier and creation
  timestamp, so recreating a link never mixes old data with new
  clicks.

The only thing you should never do is expose your `SITE_TOKEN`
publicly. Treat it like a password.

---

## 📁 Project structure

    functions/
      [[path]].js       Worker that handles every request
      lib.js            Shared helpers (migrations, auth, captcha)

    index.html          Admin dashboard

    gs-files/
      html/set/
        hub.html               Hub template
        password.html          Password page
        captcha.html           Captcha page
        password-captcha.html  Combined password + captcha page
      js/
        qrcode.min.js          QR library (served locally)
      locales/                 Translation files
      static/                  Images and other assets

---

## 🔗 Links

- 🌐 **Website:** [a1.bot.cd/gsw](https://a1.bot.cd/gsw)
- 📦 **Source:** [github.com/greenurl/GreenShort](https://github.com/quasvx/GreenShort)
- 🐛 **Issues:** [github.com/greenurl/GreenShort/issues](https://github.com/quasvx/GreenShort/issues)

---

## 📜 License

GreenShort is open source. Check the repository for the exact
license terms.

<p align="center">
  <i>Small tools, zero maintenance.</i>
</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/quasvx/GreenShort/refs/heads/main/gs-files/static/github/CloudflarePagesPreview.png" alt="Pages Preview" width="350">
</p>
