# Apex International — Certificate Verification System

A free, no-backend certificate verification website. Anyone can enter a
certificate number and instantly see whether it was genuinely issued by
Apex International, with the full certificate displayed on success.

- **Hosting:** GitHub Pages (free)
- **Database:** a Google Sheet you control (no paid backend)
- **Tech:** plain HTML, CSS, JavaScript

---

## 1. Project folder structure

Your repository should look exactly like this:

```
certificate-verification-system/
├── index.html                 → Homepage
├── verify.html                 → Verify Certificate page
├── certificate.html            → Standalone certificate view (used by QR links)
├── qr-generator.html           → QR Code Generator page
├── README.md                   → This file
├── css/
│   └── style.css               → All styling
├── js/
│   ├── config.js               → YOUR settings (Sheet link + site URL)
│   ├── data.js                 → Loads & parses the Google Sheet
│   ├── script.js                → Shared small behaviours (menu, footer year)
│   ├── verify.js                → Verify page logic
│   ├── certificate.js           → Builds the certificate design
│   └── qr.js                    → QR generator logic
└── assets/
    ├── iosh-crest.png           → IOSH crest (printed on the certificate itself)
    ├── sig-vanessa.png          → Signature image (from your template)
    ├── sig-niaz.png             → Signature image (from your template)
    ├── iosh-icon.png            → Small IOSH circle icon (on the certificate)
    └── iosh-approved-badge.png  → "iosh Training" badge (site header/footer)
```

**Nothing needs to move.** Everything is already in the right place in the
files provided — you just need to upload this whole folder to GitHub.

---

## 2. What each file does (plain English)

| File | Job |
|---|---|
| `index.html` | The homepage people land on first. |
| `verify.html` | Where someone types a certificate number and clicks "Verify now". |
| `certificate.html` | A clean, printable, single-certificate page — this is what a scanned QR code opens directly. |
| `qr-generator.html` | Where **you** type a certificate number to generate a QR code for it (e.g. to print on the physical certificate or share). |
| `css/style.css` | All the colours, fonts, spacing — the visual design. |
| `js/config.js` | The only file you *must* edit — your Google Sheet link and your live site address. |
| `js/data.js` | Fetches your Google Sheet and turns it into usable data. |
| `js/verify.js` | Runs the verify form and shows the result. |
| `js/certificate.js` | Draws the certificate (name, course, dates, signatures, crest) using your Sheet data. |
| `js/qr.js` | Generates and downloads QR codes. |

---

## 3. Your data source — already connected

You're using **SheetDB**, which turns your Google Sheet into a live JSON API
automatically — so there's no "Publish to web" step needed. Both links are
already wired into the code:

- **SheetDB API:** `https://sheetdb.io/api/v1/g38wugkic5dix`
- **Google Sheet (for editing records):** `https://docs.google.com/spreadsheets/d/1iRtuW18wV24zND9n6pIwQoxOJaCUoX4xAUwzp1iMbZ8/edit?usp=sharing`

Your sheet's first row should have exactly these column headers:

```
Name | CertificateNumber | IssueDate
```

Add one row per student/certificate, for example:

| Name | CertificateNumber | IssueDate |
|---|---|---|
| Majeed Anwar | 53358796-01-99PB | 02 December 2025 |

That's it — whenever you add or edit a row in the Google Sheet, SheetDB
serves the updated data automatically, and the website picks it up the
next time someone verifies a certificate (no republishing needed).

> **Course name:** since your sheet doesn't have a Course column, the
> certificate always prints **"IOSH Managing Safely"** (set in
> `js/config.js` as `DEFAULT_COURSE_NAME`). If you ever run other courses,
> either change that one line, or add a `CourseName` column to your sheet
> and the site will automatically use it per-row instead.
>
> **Status/Result:** not tracked in your sheet either, so every found
> certificate shows as "Verified". If you later add a `Status` column
> (`Pass` / `Fail` / `Pending` / `Revoked`), the site will automatically
> respect it — no code changes needed.

> **Privacy note:** a SheetDB API link is publicly readable by anyone who
> has it — don't share that raw link, only the verification page itself.

---

## 4. Step-by-step: upload to GitHub and publish

You said you've already created a GitHub repository named
`certificate-verification-system` — good, here's what to do with it.

### Option A — using GitHub's website (easiest for beginners)

1. Open your repository on github.com.
2. Click **Add file → Upload files**.
3. Drag the **entire contents** of the `certificate-verification-system`
   folder (all the files and the `css`, `js`, `assets` folders) into the
   upload box. Make sure `index.html` ends up at the **root** of the repo,
   not inside an extra subfolder.
4. Scroll down, click **Commit changes**.
5. Go to **Settings → Pages** (left sidebar).
6. Under "Build and deployment" → **Source**, choose **Deploy from a branch**.
7. Under "Branch", choose `main` and folder `/ (root)`, then **Save**.
8. Wait 1–2 minutes. GitHub will show you your live URL, something like:
   `https://yourusername.github.io/certificate-verification-system/`

### Option B — using Git on your computer (if you have it installed)

```bash
cd certificate-verification-system
git init
git add .
git commit -m "Initial certificate verification site"
git branch -M main
git remote add origin https://github.com/YOUR-USERNAME/certificate-verification-system.git
git push -u origin main
```
Then follow steps 5–8 above to turn on GitHub Pages.

---

## 5. Step-by-step: connect your live site address

The SheetDB link is already in `js/config.js` — the **only** thing you
still need to add is your live GitHub Pages URL, so QR codes and the
certificate's own QR point to the right place. Open `js/config.js` and edit:

```javascript
SITE_BASE_URL: "https://yourusername.github.io/certificate-verification-system",
```

Paste your real GitHub Pages URL from step 4 (**no trailing slash**), save,
then re-upload/commit this one file. That's the only setup step left —
everything else already works.

---

## 6. How to update student records later

Just edit your Google Sheet — add a new row for each new certificate, or
edit `Status` to `Revoked` if you need to invalidate one. Changes appear on
the live site within a few seconds to a couple of minutes (no re-upload,
no redeploy needed), because the site reads the sheet live every time
someone verifies a certificate.

---

## 7. How to replace the certificate template later

The certificate design lives in `js/certificate.js` (the layout/text) and
`css/style.css` (the styling), plus the images in `assets/`:

- To change the **crest, signatures, or small IOSH icon**, replace the
  corresponding PNG in `assets/` with your new image, keeping the same
  filename (or update the filename referenced in `certificate.js`).
- To change **wording** (e.g. "in association with Apex International"),
  edit the text directly inside the `render()` function in `js/certificate.js`.

## 8. How to replace your logos later

- **Header/footer "Approved IOSH Provider" badge:** replace
  `assets/iosh-approved-badge.png`.
- **Your own institute wordmark:** currently the header shows "Apex
  International" as text. If you'd like an actual logo image there instead,
  add your logo file to `assets/` and swap the `<span class="brand-name">`
  text in `index.html`, `verify.html`, `certificate.html`, and
  `qr-generator.html` for an `<img>` tag pointing to it.

---

## 9. How the QR codes work

1. Go to `qr-generator.html` on your live site.
2. Type a certificate number, click **Generate QR code**.
3. Download the PNG or copy the link.
4. Anyone who scans it is taken straight to `verify.html?cert=THAT-NUMBER`,
   which automatically runs the verification for them — no typing needed.

Each certificate rendered on-screen also carries its own small QR code
(bottom-left) that points to the same verification link, generated the same way.

---

## 10. Troubleshooting

| Problem | Likely cause |
|---|---|
| "SheetDB API link is not configured yet" | Shouldn't happen — it's pre-filled — but check `js/config.js` still has the `SHEETDB_API_URL` line intact. |
| "Could not reach SheetDB" | Your SheetDB plan/link may have hit its request limit, or the API link was changed/regenerated on sheetdb.io. |
| A real certificate shows "not found" | Check for extra spaces in the `CertificateNumber` column, or typos in the number you entered. |
| QR codes open the wrong page | `SITE_BASE_URL` in `js/config.js` doesn't match your actual GitHub Pages URL yet. |
| Site looks unstyled on GitHub Pages | Make sure `css/` and `js/` folders were uploaded alongside the HTML files, at the repo root. |

---

## 11. A note on data privacy

Because this uses a SheetDB API link as the data source, anyone with that
link could read all rows (not just the one they search for). Avoid storing
anything sensitive beyond what's needed to verify a certificate, and don't
share the raw SheetDB link publicly — only the verification page itself.
