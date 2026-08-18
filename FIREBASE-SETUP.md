# Firebase setup for the DCS website

The website code is finished. These are the console steps only you can do —
they need a browser login to your Google account. Budget about 5 minutes.

Project: **dcs-60b92**

---

## 1. Enable Email/Password sign-in

1. Open https://console.firebase.google.com/project/dcs-60b92/authentication/providers
2. Click **Get started** if you see it.
3. In the provider list click **Email/Password**.
4. Turn on the first **Enable** toggle. Leave "Email link" off.
5. **Save**.

## 2. Enable Google sign-in

1. Same **Sign-in method** page → click **Google**.
2. Turn on **Enable**.
3. Set **Project public-facing name** to `Divine Cyber Solution`
   (this is what users see on the Google consent screen).
4. Pick your email under **Project support email**.
5. **Save**.

## 3. Authorise your live domains

Without this, Google sign-in fails on the live site with
`auth/unauthorized-domain`.

1. Go to **Authentication → Settings → Authorised domains**
   https://console.firebase.google.com/project/dcs-60b92/authentication/settings
2. Click **Add domain** and add:
   - `dcs-website.onrender.com`  ← the live Render site
   - any custom domain you add later (e.g. `divinecybersolution.com`)

`localhost` is already allowed, which is why local testing works.

The two old `*.vercel.app` entries can be removed once Vercel is deleted —
they do no harm, but they're dead weight.

## 4. Create the Firestore database

This is where enrollment submissions land. Until it exists, the form still
works but shows "we couldn't confirm your details saved".

1. Open https://console.firebase.google.com/project/dcs-60b92/firestore
2. Click **Create database**.
3. Choose **Start in production mode** (the rules in step 5 replace the defaults).
4. Location: **asia-south1 (Mumbai)** — closest to your users.
5. **Enable**.

## 5. Apply the security rules

1. In Firestore click the **Rules** tab.
2. Delete what's there and paste the entire contents of `firestore.rules`
   from this repo.
3. **Publish**.

These rules let a signed-in user create and read only their own enrollment,
and block all edits and deletes from the browser. Without them, production
mode blocks every write and the form can't save.

---

## Where enrollments show up

Firestore → **Data** tab → `enrollments` collection. Each document holds:

| Field | Example |
|---|---|
| `name` | Kunal Bankhele |
| `mobile` | +919876543210 |
| `profession` | Student |
| `courses` | ["AI with Cyber Security"] |
| `amount` | 4499 |
| `reference` | DCS-3210-4499 |
| `paymentStatus` | pending |
| `uid` / `email` | from the signed-in account |
| `createdAt` | server timestamp |

---

## Razorpay (later)

In `index.html`, find:

```js
const RAZORPAY_KEY_ID = '';   // <-- paste your Razorpay Key ID here when ready
```

Paste your **Key ID** there. Until then the Pay button explains that payment
isn't connected and tells the user to reach out on WhatsApp.

Only the Key ID belongs in this file. The **Key Secret** must never go in
front-end code — it needs a small server endpoint to sign orders. Ask for
that step when you have your Razorpay account.

---

## Testing after setup

1. Open the site, click **ENROLL NOW** on any course.
2. The login box should appear.
3. **Continue with Google** → pick your account → the box closes and the
   enrollment form opens with that course already ticked.
4. Fill it in and submit. You should land on the payment step with **no**
   yellow warning — that means Firestore saved it.
5. Check Firestore → Data → `enrollments` for the new document.
