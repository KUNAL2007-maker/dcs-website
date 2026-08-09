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

Deployed on Vercel as a static site. Pushes to `main` deploy automatically.
