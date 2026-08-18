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

## 6. Turn on the ADMIN tab

The admin view is built into the same page — there is no separate admin site.
When an authorised account logs in, an **ADMIN** button appears in the header
next to LOG IN. It opens a table of every registration, with search and
**EXCEL** / **PDF** download buttons.

The admin address is already set to **checkship66@gmail.com** in the code. It
lives in **two** places, which do different jobs:

### a) `index.html` — makes the button appear

Near the top of the `<script type="module">` block:

```js
const ADMIN_EMAILS = [
    'checkship66@gmail.com',
];
```

To add a second admin later, put one quoted address per line, comma
separated — and add it to (b) as well.

### b) `firestore.rules` — actually releases the data

The `isAdmin()` function holds the same address:

```
&& request.auth.token.get('email', '').lower() in [
     'checkship66@gmail.com'
   ];
```

**You still have to publish this.** Paste the whole `firestore.rules` file into
Firebase → Firestore → **Rules** → **Publish**:
https://console.firebase.google.com/project/dcs-60b92/firestore/rules

**Only step (b) is security.** Step (a) just shows a button, and anyone can
unhide a button with browser devtools. The rules are what make Firestore
refuse. Until you publish them the admin sees a clear red error saying so.

### The admin must sign in with Google

Firebase does not check that you own an address when you register it with a
password, so the rules require a **verified** email. Google sign-in verifies
automatically; email/password does not.

So: log in with **Continue with Google** as `checkship66@gmail.com`. If you use
email/password instead, the console opens but explains that the address is
unverified and shows no data.

### What the admin can and cannot do

- Read every registration, search it, export it — yes.
- Edit or delete a registration from the browser — no. `update` and `delete`
  are blocked for everyone, admin included. Do those in the Firebase console.

### The exports

| | |
|---|---|
| **EXCEL** | A real `.xlsx` with a filter row and sized columns. If an ad blocker blocks the spreadsheet library, it falls back to `.csv`, which opens in Excel just the same, and says so. |
| **PDF** | Landscape A4 table with a heading, total value and page numbers. If the library is blocked it tells you to use Ctrl+P → Save as PDF. |

Both files are named `DCS-Registrations-YYYY-MM-DD`, and both contain **only
the rows currently matching the search box** — so you can send a client just
one course's registrations by searching for it first.

Amounts are plain numbers under an "Amount (INR)" heading rather than a ₹
column, because the PDF font has no rupee glyph and would print a blank box.

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
6. Log in as `checkship66@gmail.com` (via **Continue with Google**) and confirm
   the **ADMIN** button appears, the registration from step 4 is listed, and
   both EXCEL and PDF download.
