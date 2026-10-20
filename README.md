# munchiebot.app website

Static site for www.munchiebot.app: a landing page plus the Privacy Policy and Support pages that App Store review requires. Plain HTML and CSS, with no scripts, trackers or external fonts.

## URLs for App Store Connect

| Field | URL |
| --- | --- |
| Privacy Policy URL (App Information) | `https://www.munchiebot.app/privacy/` |
| Support URL (version page) | `https://www.munchiebot.app/support/` |
| Marketing URL (optional) | `https://www.munchiebot.app/` |

Open both URLs in a browser before you submit. Review checks that they load.

## Files

```
index.html            Landing page
privacy/index.html    Privacy Policy
support/index.html    Support: scanning, scores, backup/restore, contact
404.html              Not-found page (GitHub Pages and Cloudflare Pages pick it up automatically)
assets/style.css      All styles (light and dark mode)
assets/fonts/         Space Grotesk Bold, subset to Latin as WOFF2 (16 KB), and its OFL license
assets/img/           App icon sizes and three screenshots copied from build/previews/
CNAME                 Custom domain for GitHub Pages (Cloudflare ignores it)
```

This README is published along with the site on both hosts. It holds nothing secret, but you can leave it out of the upload.

## Preview locally

```bash
python3 -m http.server 8765 --directory "/Users/hooptiembair/Munchie Bot/Website"
```

Then open http://localhost:8765. Folder links like `privacy/` need a server; opening the files directly with `file://` won't follow them.

## Hosting

`.app` domains only work over HTTPS (the whole TLD is on browsers' HSTS preload list). Both options below issue the certificate for you. Expect a few minutes to an hour after DNS is set before the site loads.

### Option A: Cloudflare Pages (no Git needed)

1. In the Cloudflare dashboard, go to **Workers & Pages → Create → Pages → Upload assets**.
2. Name the project (for example `munchiebot`) and upload this `Website` folder. It's live at `munchiebot.pages.dev`.
3. Under the project's **Custom domains**, add `www.munchiebot.app`. If the domain's DNS is on Cloudflare, the record is created for you. Otherwise, add a `CNAME` record at your registrar from `www` to `munchiebot.pages.dev`.
4. Optional: send the bare domain to www. With DNS on Cloudflare, add `munchiebot.app` as a second custom domain, then a Redirect Rule from `munchiebot.app/*` to `https://www.munchiebot.app/${1}` (301).

To update later, upload the folder again from the project's **Deployments** tab, or run `npx wrangler pages deploy . --project-name munchiebot` from this folder.

### Option B: GitHub Pages

1. Create a repository (it must be public on a free GitHub plan) and push the contents of this folder to the root of `main`.
2. In **Settings → Pages**, choose **Deploy from a branch**, `main`, `/ (root)`. The custom domain is read from the `CNAME` file.
3. At your registrar, add a `CNAME` record from `www` to `<your-github-username>.github.io`. For the bare domain, add `A` records for `185.199.108.153`, `185.199.109.153`, `185.199.110.153` and `185.199.111.153`. GitHub then redirects it to www.
4. Once the certificate is issued, turn on **Enforce HTTPS**. Also verify the domain under your GitHub account's **Settings → Pages** so no one else can claim it.

## Before you submit

- **App Store badge:** `index.html` has a "Coming soon" placeholder. When the listing is live, replace it with Apple's official badge from the [App Store marketing guidelines](https://developer.apple.com/app-store/marketing/guidelines/), linked to `https://apps.apple.com/app/id<APP_ID>`.
- **Contact email:** every page uses `support@munchiebot.app`. Make sure that mailbox or forward exists. To change it everywhere: `grep -rl support@munchiebot.app . | xargs sed -i '' 's/support@munchiebot.app/NEW@ADDRESS/g'`.
- **App Privacy answers:** this policy matches answering **Data Not Collected** in App Store Connect.

## Keep the policy in sync with the app

The privacy policy describes version 1.0: no accounts, analytics, ads or Munchie Bot server, and data stays on the phone. Update `privacy/index.html` and its effective date **before** shipping any version that:

- turns on `ScoringModelStore.remoteURL` (the app would then contact a Munchie Bot server),
- adds a backend, sync, shared ratings or any opt-in data sharing,
- adds an analytics, crash-reporting or ads SDK,
- adds a Pro subscription (Apple processes the payment, but describe what the app stores about it),
- requests a new permission (Photos, Contacts, background location and so on).

## Assets

- Screenshots come from `build/previews/` (`nested-home.png`, `score-rate-now.png`, `strain-save-button.png`). Re-copy them when the UI changes.
- The font subset was made from `MunchieBot/Resources/Fonts/SpaceGrotesk-Bold.ttf` with fontTools:
  `pyftsubset SpaceGrotesk-Bold.ttf --unicodes="U+0020-007E,U+00A0-00FF,U+2010-2027,U+2030-203A,U+2190-2193,U+2212,U+2122" --layout-features='kern,liga,calt,tnum' --flavor=woff2 --output-file=assets/fonts/SpaceGrotesk-Bold.woff2`
