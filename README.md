# Senior IPTV - Custom Cast Web Receiver

This directory contains the Custom Web Receiver for Google Cast / Chromecast.

By default, Google's **Default Media Receiver** (`CC1AD845`) ignores in-band audio track switching on live MPEG-TS / HLS streams and forces playback of the first audio channel (often Danish on Nordic IPTV sports channels).

With this **Custom Web Receiver**, Chromecast uses Shaka Player's `AudioTracksManager` to:
1. **Automatically select Swedish (`sv` / `swe`) audio** as soon as the stream connects.
2. **Report all available audio tracks** back to the Senior IPTV phone app.
3. **Allow live audio track switching** directly from the phone remote's `[Ljud]` button while casting.
4. **Display a senior-friendly on-screen TV banner** showing channel name, logo, and active audio language on stream start and channel switch.

---

## Quick Setup Guide (15-30 Minutes)

### Step 1: Host `index.html` on HTTPS (Free)

Google Cast requires your receiver HTML file to be hosted on an HTTPS URL. You can host this file for free using any of the following methods:

#### Option A: GitHub Pages (Recommended, 2 Minutes)
1. Push this repository to GitHub (or create a small repo named `senior-iptv-receiver`).
2. Go to **Settings** -> **Pages**.
3. Under **Branch**, select `main` (or root / `receiver` folder) and click **Save**.
4. Your HTTPS receiver URL will be:
   `https://<your-username>.github.io/<repo-name>/receiver/index.html` (or `/index.html`)

#### Option B: Cloudflare Pages / Netlify / Vercel (1 Minute drag-and-drop)
1. Go to [Netlify Drop](https://app.netlify.com/drop) or [Cloudflare Pages](https://pages.cloudflare.com/).
2. Drag and drop the `receiver` folder.
3. You will instantly get a free HTTPS URL like:
   `https://senior-iptv-receiver.pages.dev/index.html`

---

### Step 2: Register in Google Cast Developer Console

1. Log in to the [Google Cast Developer Console](https://cast.google.com/publish/).
2. Pay the one-time registration fee ($5 USD) if not already registered.
3. Click **Add New Application**.
4. Select **Custom Receiver**.
5. Fill in the details:
   - **Name**: `Senior IPTV`
   - **Receiver Application URL**: Paste your HTTPS URL from Step 1 (e.g. `https://<your-domain>/index.html`).
   - Check **Supports Cast Streaming**.
6. Click **Save**.
7. Google will assign an **Application ID** (an 8-character hex code like `A1B2C3D4`).
8. *(Important)* Add the serial number of your Chromecast device under **Cast Developer Console -> Devices** so you can test immediately without waiting for global publishing.

---

### Step 3: Enter your Application ID in Senior IPTV

You have two easy ways to set your App ID:

#### In the App Settings:
1. Open Senior IPTV on your phone.
2. Tap the **Gear icon (⚙)** to open Settings.
3. Scroll down to **APP & PLAYBACK** (APP & UPPSPELNING).
4. Enter your 8-character ID in **Custom Cast App ID** (Eget Cast App ID) and save.

#### In the Source Code:
Open [`app/src/main/res/values/strings.xml`](file:///Users/edvin/Documents/Code/seniorIPTV/app/src/main/res/values/strings.xml) and update:
```xml
<string name="cast_receiver_app_id">YOUR_APP_ID_HERE</string>
```

---

## How It Works Technically

```
┌──────────────────────────────┐
│  Senior IPTV Android App     │
└──────────────┬───────────────┘
               │ 1. Casts HLS/TS stream URL + preferredAudio="sv"
               ▼
┌──────────────────────────────┐
│  Chromecast Custom Receiver  │
│  (Shaka Player / CAF v3)     │
└──────────────┬───────────────┘
               │ 2. AudioTracksManager demuxes TS PIDs
               │ 3. Automatically selects Swedish ('sv' / 'swe')
               │ 4. Broadcasts track list back via 'urn:x-cast:com.senioriptv'
               ▼
┌──────────────────────────────┐
│  TV Screen                   │
│  - Plays Swedish Audio!      │
│  - Displays On-Screen Banner │
└──────────────────────────────┘
```
