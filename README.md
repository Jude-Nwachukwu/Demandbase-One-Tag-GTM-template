# Demandbase Tag

A Google Tag Manager custom tag template for deploying the Demandbase visitor identification tag on standard websites and single-page applications — without touching a line of code.

---

## Overview

Demandbase identifies the companies visiting your website by analysing visitor data and matching it against their database. For that identification to work, a Demandbase tracking script needs to be present on every page of your site.

This template handles that deployment entirely within GTM. It covers both the initial script load on standard multi-page websites and the additional firing requirement for single-page applications (SPAs), where a standard page load trigger alone won't capture URL changes between views.

Rather than maintaining two separate Custom HTML tags, this template consolidates both use cases into a single, purpose-built configuration — keeping your GTM workspace cleaner and the setup less error-prone.

---

## When to Use This Template

- You are implementing Demandbase on a standard website and want to manage the tag through GTM rather than hardcoding it in your site's HTML
- Your website is a single-page application (SPA) built on a framework like React, Angular, or Vue, and you need Demandbase to fire on each virtual page view — not just the first load
- You are managing Demandbase across multiple domains or page types and want consistent, controlled deployment through GTM

---

## Features

- Supports standard multi-page websites and single-page applications from a single template
- Simple two-mode configuration — no additional tags or workarounds required for SPA support
- Tracking ID field only appears when relevant, keeping the tag configuration focused
- Safe to use alongside tag sequencing and GTM Preview Mode
- Duplicate script loading is prevented automatically, even if the tag fires multiple times

---

## Setup Instructions

### Step 1 — Import the Template

1. In your GTM workspace, go to **Templates** in the left navigation
2. Under **Tag Templates**, click **New**
3. Click the More Actions menu (⋮) in the top right and select **Import**
4. Select the `.tpl` file from this repository and click **Save**

### Step 2 — Create the Initial Page Load Tag

This tag loads the core Demandbase script on your website. It should fire once per page on standard websites.

1. Go to **Tags** and click **New**
2. Click the Tag Configuration area and select **Demandbase Tag** from the Custom section
3. Set **Tag Type** to **Initial Page Load (Standard Tag)**
4. Enter your **Tracking ID** (see Field Explanations below)
5. Under Triggering, select or create a **DOM Ready** trigger set to fire on all pages
6. Name the tag — for example, `Demandbase - Initial Load` — and save

### Step 3 — Create the SPA Tag (if applicable)

Skip this step if your website is not a single-page application.

1. Go to **Tags** and click **New**
2. Select **Demandbase Tag** from the Custom section
3. Set **Tag Type** to **SPA URL Change (Single-Page Application)**
4. No Tracking ID is needed for this tag — it reuses the script loaded by the first tag
5. Under Triggering, select or create a **History Change** trigger set to fire on all history changes
6. Name the tag — for example, `Demandbase - SPA Fire` — and save

### Step 4 — Publish

Preview and test your setup before publishing. Once confirmed, submit and publish your container.

---

## Field Explanations

### Tag Type

This is the first setting you configure, and it controls what the tag actually does when it fires.

**Initial Page Load (Standard Tag)** — Choose this for your primary Demandbase tag. This loads the Demandbase identification script on the page, which is required before any Demandbase functionality can run. Use this on every page of your website, paired with a DOM Ready trigger.

**SPA URL Change (Single-Page Application)** — Choose this for the secondary tag that handles navigation within a single-page application. When a visitor moves between views in an SPA, the page doesn't fully reload — so the standard trigger won't fire again. This mode tells Demandbase to re-run its identification process on each URL change. It depends on the Initial Page Load tag having already fired first.

---

### Tracking ID

This field is only shown when **Tag Type** is set to **Initial Page Load**.

Your Tracking ID is the unique identifier that connects the tag to your Demandbase account. You can find it inside the Demandbase platform under **Settings → Demandbase-Wide Settings → Tag Configurations → Demandbase Tag**.

The ID is the alphanumeric string in your tag snippet URL — for example, `f2836c1a7e9d4b52`. Enter only the ID itself, not the full URL.

This field is required. The tag will not fire without a valid Tracking ID.

---

## Triggering Recommendations

| Tag Instance | Recommended Trigger Type | Fires On |
|---|---|---|
| Initial Page Load | DOM Ready | All pages |
| SPA URL Change | History Change | All History Changes |

**Why DOM Ready for the initial tag?** Demandbase recommends loading their script after the DOM is available. DOM Ready gives the page structure time to load before the Demandbase script is fetched, which results in more reliable identification on each page visit.

**Why History Change for the SPA tag?** Single-page applications update the browser's URL without triggering a full page reload. The History Change trigger detects these URL transitions and fires the tag at the right moment — when a visitor has navigated to a new view.

If your SPA uses a custom event to signal route changes (rather than browser history updates), you may need to use a Custom Event trigger instead. Check with your development team if you're unsure which applies to your site.

---

## Testing and Validation

Before publishing, use GTM Preview Mode to confirm both tags are working as expected.

**For the Initial Page Load tag:**
1. Open your site in Preview Mode
2. The tag should appear in the **Tags Fired** list on the first page load
3. In your browser's Network tab, look for a request to `tag.demandbase.com` — you should see your Tracking ID in the URL
4. If the Demandbase script loads successfully, the tag fired correctly

**For the SPA tag:**
1. While still in Preview Mode, navigate between pages or views on your site
2. Each URL change should trigger a new event in the GTM Preview panel
3. Confirm the SPA tag appears in the **Tags Fired** list for each of those events
4. The Initial Page Load tag should only appear once — on the first load

---

## Common Use Cases

**Standard corporate website with a blog and landing pages**
Deploy the Initial Page Load tag with a DOM Ready trigger across all pages. No SPA tag is needed unless your site uses client-side routing.

**React or Angular marketing site**
Deploy both tags. The Initial Page Load tag fires on first render; the SPA tag fires each time the visitor navigates to a new route.

**Multi-domain implementation**
Create separate instances of the Initial Page Load tag for each domain, each with its own Tracking ID if required by your Demandbase licence. Confirm your licence covers all domains before deploying.

---

## Troubleshooting

**The Initial Page Load tag fired, but I don't see a network request to Demandbase**

Check that the Tracking ID was entered correctly — it should be only the alphanumeric string, with no slashes, dots, or extra characters. Also confirm the tag is not being blocked by a Content Security Policy (CSP). If your site has a CSP, you may need to allowlist `tag.demandbase.com`.

**The SPA tag isn't firing on page changes**

Confirm your site uses standard browser history for navigation. Some SPAs use hash-based routing (`#/page`) rather than the History API. If that's the case, a History Change trigger may not work — speak with your development team about how routing is handled and whether a Custom Event trigger would be more appropriate.

**Both tags fired on the first page load**

The SPA tag should only be triggered by a History Change trigger, not a page view or DOM Ready trigger. Check that the trigger assigned to the SPA tag is set to **History Change** and not something broader.

**Demandbase isn't identifying visitors after deployment**

Tag deployment and visitor identification are separate processes. Once the tag is confirmed as firing correctly, allow some time for identification data to appear in Demandbase. If identification is still not working after 24 hours, contact your Demandbase account team to verify your account configuration.

---

## Notes and Limitations

- This template is designed for **Web GTM containers only**. It is not compatible with server-side GTM containers.
- The SPA tag depends entirely on the Initial Page Load tag having run first. If the Initial Page Load tag is missing, blocked, or misconfigured, the SPA tag will have nothing to work with.
- The Demandbase script is loaded from Demandbase's own servers. If your organisation has a strict Content Security Policy, you will need to allowlist `tag.demandbase.com` separately — this template does not bypass CSP restrictions.
- Demandbase's licence terms restrict tag deployment to domains covered by your agreement. Confirm with your Demandbase CSM before deploying to any new domain.
- This template does not handle consent mode or conditional firing based on user consent. If your implementation requires consent-gated firing, manage that through your trigger conditions or a consent listener configured separately in GTM.

---

## Support and Maintenance

This template is maintained independently and is not an official Demandbase product. For questions about the Demandbase platform, identification results, or your account configuration, contact Demandbase support directly.

For issues with the template itself — unexpected behaviour, field questions, or trigger guidance — open an issue in this repository.
