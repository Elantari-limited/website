# elantari.org

Static, single-page site for Elantari Limited. Vanilla HTML / CSS /
~20 lines of JS. No build step.

## File map

```
elantari-website/
├── index.html                       single page; section anchors in the nav
├── style.css                        all styling
├── script.js                        mobile nav + current-year footer
├── CNAME                            elantari.org   (used by GitHub Pages)
├── README.md                        this file
├── .gitignore
└── assets/
    ├── climaguard-demo.mp4          compressed UI demo (~2 MB, 1280×720)
    ├── climaguard-demo-poster.jpg   first frame, used as <video poster>
    ├── climaguard.png               ARCHITECTURE DIAGRAM — DROP IN
    └── logo.png                     Elantari wordmark — DROP IN
```

The two `DROP IN` images aren't included in this initial commit
(they were attached in the chat as inline images, not as files I could
read). Save the originals as exactly:

- `assets/logo.png`        — the Elantari wordmark logo
- `assets/climaguard.png`  — the "AI CLIMATE FORECAST & RISK ASSESSMENT"
                             architecture diagram

The site renders cleanly without them (the IMG tags use `onerror` to hide
the broken-image icon and the brand still shows the text wordmark), but
visually it's much stronger with both in place.

## Local preview

```
cd elantari-website
python3 -m http.server 8000
open http://localhost:8000
```

## Deployment — GitHub Pages with the elantari.org custom domain

1. **Push to a public repo** (e.g. `elantari-org/website`):

   ```
   cd elantari-website
   git init
   git add .
   git commit -m "Initial site"
   git branch -M main
   git remote add origin git@github.com:elantari-org/website.git
   git push -u origin main
   ```

2. **Enable Pages**: GitHub repo → *Settings* → *Pages* → Source = branch
   `main`, folder `/`. Pages will detect the `CNAME` file and propose
   `elantari.org` automatically — click *Save*.

3. **Add four DNS records on Squarespace**
   (Settings → Domains → elantari.org → DNS Settings):

   | Type  | Host | Value                  | TTL  |
   |-------|------|------------------------|------|
   | A     | @    | 185.199.108.153        | auto |
   | A     | @    | 185.199.109.153        | auto |
   | A     | @    | 185.199.110.153        | auto |
   | A     | @    | 185.199.111.153        | auto |
   | CNAME | www  | elantari-org.github.io.| auto |

   Wait 5–30 min for propagation. Then back on GitHub Pages, tick
   "Enforce HTTPS" — it goes live once Let's Encrypt issues the cert
   (usually within an hour).

4. **Done.** `https://elantari.org` resolves to the site; `www`
   redirects to the apex.

## Updating content

- Edit `index.html` directly. Each section is bracketed with a
  comment so the structure is easy to follow.
- New images go in `assets/`. Keep total page weight modest;
  the video is already the heaviest asset at ~2 MB.
- Push to `main`; Pages redeploys in ~30 s.

## Replacing the demo video

The current MP4 was compressed from a 107 MB source to ~2 MB
(1280×720, H.264 CRF 26, no audio). To regenerate from a new
source:

```
ffmpeg -y -i ClimaGuard_Website.mp4 \
    -vf "scale=1280:-2,fps=30" \
    -c:v libx264 -crf 26 -preset slow -movflags +faststart \
    -an \
    assets/climaguard-demo.mp4

ffmpeg -y -ss 1 -i assets/climaguard-demo.mp4 \
    -frames:v 1 -q:v 3 \
    assets/climaguard-demo-poster.jpg
```

Keep the file under ~25 MB (GitHub's soft warning) and well below
100 MB (hard limit per file in a non-LFS repo).

## Notes

- No analytics, no cookies, no third-party trackers — just two
  Google Fonts requests. Drop those too if a fully self-hosted font
  stack is preferred (the system-sans fallback is already in place).
- Colour palette: charcoal `#0F172A`, soft gray `#475569`, accent
  teal `#0E7C66`, white background. Defined as CSS custom
  properties at the top of `style.css`.
