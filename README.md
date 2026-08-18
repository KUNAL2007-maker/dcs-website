# Divine Cyber Solution (DCS)

Landing site for Divine Cyber Solution — cybersecurity training courses.

## Structure

Single-page static site. No build step.

- `index.html` — the entire site (HTML, Tailwind via CDN, inline JS)
- `dcs-logo.jpeg` — brand logo
- `Jayraj.jpeg` — instructor photo
- `firestore.rules` — Firestore security rules, including the admin allowlist
- `FIREBASE-SETUP.md` — the Firebase console steps and admin setup
- `render.yaml` — Render blueprint

## Admin console

Registrations are viewable in-page: an allowlisted account sees an **ADMIN**
button in the header, opening a searchable table with Excel and PDF export.
The address must be listed in both `ADMIN_EMAILS` (index.html) and `isAdmin()`
(firestore.rules) — see `FIREBASE-SETUP.md` section 6. Only the rules are
security; the button is just convenience.

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
