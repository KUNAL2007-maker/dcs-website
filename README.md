# Divine Cyber Solution (DCS)

Landing site for Divine Cyber Solution — cybersecurity training courses.

## Structure

Single-page static site. No build step.

- `index.html` — the entire site (HTML, Tailwind via CDN, inline JS)
- `dcs-logo.jpeg` — brand logo
- `Jayraj.jpeg` — instructor photo

## Local preview

```bash
python -m http.server 8000
```

Then open http://localhost:8000

## Deployment

Deployed on [Render](https://render.com) as a static site, configured by
`render.yaml`. Pushes to `main` deploy automatically.

Live: https://dcs-website.onrender.com

Adding a new domain also needs it added to Firebase → Authentication →
Settings → Authorised domains, or Google sign-in fails there. See
`FIREBASE-SETUP.md`.
