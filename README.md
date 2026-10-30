# Strongroom

A private, end-to-end encrypted vault for documents, photos and files. Folders, prompt-style search, and places (files grouped by location). Hosted on Vercel, synced through Supabase.

## How the security works

- **Account password** signs you in to Supabase.
- **Vault passphrase** encrypts every file, thumbnail, name, tag and note in your browser (AES-256-GCM, key derived with PBKDF2, 310,000 rounds). It is never sent anywhere and cannot be reset.
- Supabase only stores ciphertext. Row-level security limits each account to its own rows and its own folder in the private `vault` storage bucket.
- An encrypted copy is cached in the browser, so the vault opens offline. Changes sync when you're back online.

## Supabase (already set up)

Project `strongroom` (`qeoemudkmwmniwlxkfhe`, us-east-1) has:

- `vault_meta`: one row per user with the salt and passphrase check value
- `vault_records`: one encrypted row per file, folder and place
- `vault` storage bucket: private, 50 MB per-file limit

The URL and publishable key are in `config.js`. Both are meant to be public.

## Deploy to Vercel

**Option A: Vercel CLI (no Git needed)**

```bash
cd strongroom-site
npx vercel        # log in, accept the defaults: no framework, no build command
npx vercel --prod
```

**Option B: GitHub**

1. Push this folder to a new private GitHub repository.
2. In Vercel, choose **Add New → Project** and import the repository.
3. Set Framework Preset to **Other** and leave Build Command empty.
4. Deploy.

## After the first deploy (2 minutes in Supabase)

In the Supabase dashboard, open project **strongroom → Authentication → URL Configuration**:

1. Set **Site URL** to your Vercel address, for example `https://strongroom-yourname.vercel.app`.
2. Add the same address under **Redirect URLs**.

Without this, the confirmation email after "Create account" links to localhost.

Optional, under **Authentication → Sign In / Providers → Email**:

- Turn off **Allow new users to sign up** once you've made your account, so nobody else can create one.

## Limits on the Supabase free plan

- 1 GB of file storage, 50 MB per file. Larger files stay on the device they were added from.
- Free projects pause after about a week with no activity. Open the dashboard and press Restore if that happens. Your data is kept.

## Files

| File | Purpose |
| --- | --- |
| `index.html` | The whole app |
| `config.js` | Supabase URL and publishable key |
| `vendor/` | Self-hosted Supabase client (v2.45.4) |
| `vercel.json` | Security headers (strict CSP, no framing, no indexing) |
