# One Rep Max Calculator — FitBudd

A single-file HTML tool that ranks for **"One Rep Max Calculator"**, captures coach leads via HubSpot, and soft-sells FitBudd. Built for Vercel static hosting and iframe embedding on Webflow blog pages.

---

## What's in the box

| File | Purpose |
|---|---|
| `index.html` | Entire tool — HTML, CSS, and JS inline, no dependencies |
| `vercel.json` | Clean URL config (drops the `.html` extension) |
| `README.md` | This file |

---

## 1. Configure HubSpot

Open `index.html` and find the two constants near the top of the `<script>` block:

```js
const HUBSPOT_PORTAL_ID = 'YOUR_PORTAL_ID';
const HUBSPOT_FORM_GUID = 'YOUR_FORM_GUID';
```

**Portal ID** — HubSpot → Settings → Account Setup → Account → Hub ID
**Form GUID** — HubSpot → Marketing → Forms → open the form → copy the GUID from the page URL

The form submission sends these fields to HubSpot:

| Field name | Value |
|---|---|
| `email` | User's email address |
| `calculator_exercise` | Selected exercise (e.g. "Bench Press") |
| `calculator_1rm_result` | Epley 1RM in the selected unit |
| `calculator_unit` | `lbs` or `kg` |
| `lead_source` | Always `1rm_calculator` |
| `utm_source` _(if present)_ | From URL query string |
| `utm_medium` _(if present)_ | From URL query string |
| `utm_campaign` _(if present)_ | From URL query string |
| `utm_content` _(if present)_ | From URL query string |

Create matching custom contact properties in HubSpot if you want to use these fields in workflows or segmentation.

---

## 2. Set the canonical URL

In `<head>`, replace the placeholder:

```html
<link rel="canonical" href="TODO: Set your canonical URL here">
```

Use the final production URL, e.g. `https://www.fitbudd.com/tools/1rm-calculator`.

---

## 3. Deploy to Vercel

### Option A — Connect GitHub repo (recommended)

1. Push this repo to GitHub
2. Go to [vercel.com](https://vercel.com) → New Project → Import your repo
3. Framework preset: **Other** (it's a static file — no build step)
4. Click Deploy

### Option B — Vercel CLI

```bash
npm i -g vercel   # install once
vercel --prod     # deploy from this directory
```

Vercel will serve `index.html` at the root URL. `vercel.json` removes the `.html` extension automatically.

---

## 4. Embed on Webflow

Paste this snippet into a Webflow **Embed** component on the blog post:

```html
<div style="max-width:720px;margin:0 auto;">
  <iframe
    src="https://YOUR-PROJECT.vercel.app"
    width="100%"
    height="1400"
    frameborder="0"
    style="border:none;"
    title="One Rep Max Calculator"
    loading="lazy"
  ></iframe>
</div>
```

Replace `YOUR-PROJECT.vercel.app` with your actual Vercel URL.

**Note on iframe height:** The `height="1400"` is a safe fixed value for desktop. To avoid a scroll bar inside the iframe, implement the `postMessage` auto-height script described in the TODO comment at the bottom of `index.html`. This will resize the iframe dynamically as the results section appears.

---

## 5. UTM parameter structure

All FitBudd CTAs inside the tool already carry UTMs. When embedding, you can append UTMs to the iframe `src` URL to track which blog post drove a conversion:

```
https://YOUR-PROJECT.vercel.app?utm_source=webflow&utm_medium=embed&utm_campaign=1rm_calculator&utm_content=blog_post_slug
```

UTMs appended to the tool URL are automatically forwarded to HubSpot on email submit.

---

## 6. Formula verification — test cases

Before shipping, verify these against the Epley 1RM shown in the primary result:

| Weight | Reps | Unit | Expected Epley 1RM |
|---|---|---|---|
| 225 | 5 | lbs | **263** |
| 315 | 3 | lbs | **347** |
| 100 | 10 | kg | **133** |
| 185 | 8 | lbs | **234** |
| 405 | 1 | lbs | **405** (already 1RM — no formula section shown) |
| 135 | 12 | lbs | **189** |

Manual check for 225 lbs × 5 reps:
`225 × (1 + 5/30) = 225 × 1.1667 = 262.5 → 263 ✓`

---

## 7. Quality checklist

- [ ] HubSpot Portal ID and Form GUID replaced
- [ ] Canonical URL set
- [ ] Vercel URL updated in Webflow embed snippet
- [ ] All 6 test cases pass (see table above)
- [ ] kg/lbs toggle updates the training chart correctly
- [ ] Formula comparison section expands and collapses smoothly
- [ ] Rep pills navigate with arrow keys and Enter/Space
- [ ] Email submit shows success message on 200, error + retry on failure
- [ ] FitBudd trial CTA link is live and UTMs are correct
- [ ] Zero console errors in Chrome DevTools
- [ ] No horizontal scroll at 375px, 390px, 768px, 1440px
