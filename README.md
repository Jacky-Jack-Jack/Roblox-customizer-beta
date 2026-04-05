# Roblox Customizer
<img width="432" height="373" alt="image" src="https://github.com/user-attachments/assets/dd8fced3-c807-4fed-a66a-45f3c44546c0" />

A Chrome/Edge/Brave extension that lets you visually edit your Roblox profile appearance directly in the browser. All changes are **client-side only** — nothing is sent to Roblox's servers, nothing actually changes on your account. It's purely cosmetic, for your eyes only.

> **Note:** This extension is intended for personal visual customization. It does not modify any real account data, bypass any systems, or interact with Roblox's API in any unauthorized way.


---

## Features

### Identity
- Change your displayed name in the profile header
- Change your `@username` handle
- Edit your bio / about text
- Set a custom join date

### Stats
- Override your Robux balance (shown in the nav bar and on the Transactions page)
- Set custom Friends, Followers, and Following counts
- Stat pills are automatically un-greyed and made clickable when a value is set

### Appearance
- Replace your profile headshot avatar (shown in the nav bar and on your profile)
- Replace the full-body / 720x720 avatar render on your profile
- Hide the 3D model toggle button
- Hide the "Currently Wearing" items carousel

### Profile-specific targeting
- Enter a **Player ID** to restrict profile changes to only your own profile page (`/users/[ID]/profile`)
- Leave it empty to apply changes on any profile you visit

### Edit Profile page
- Overrides also apply on `/users/profile/edit` — display name, @username, and bio rows all update visually

### Persistence
- Settings are saved as a cookie on `.roblox.com` with a 1-year expiry
- They auto-load every time you open a Roblox page — no need to re-apply manually

---

## Pages Supported

| Page | What changes |
|------|-------------|
| All pages | Nav headshot, nav Robux amount, age-bracket username label |
| `/users/[ID]/profile` | Display name, @handle, stat pills, large avatar, full-body image, bio, join date, 3D button, currently wearing |
| `/users/profile/edit` | Display name row, @username row, bio row, avatar circle |
| `/transactions` | Robux balance label |

---

## Installation

This extension is not on the Chrome Web Store. Install it manually in developer mode.

**Requirements:** Chrome, Edge, or any Chromium-based browser.

1. Download the latest release ZIP from the [Releases](https://github.com/Jacky-Jack-Jack/Roblox-customizer-beta/releases) page
2. Unzip it to a folder on your computer
3. Open your browser and go to `chrome://extensions`
4. Enable **Developer Mode** using the toggle in the top-right corner
5. Click **Load unpacked**
6. Select the unzipped `roblox-customizer` folder
7. The extension icon will appear in your toolbar

---

## Usage

1. Click the extension icon in your browser toolbar
2. Fill in any fields you want to change — you do not need to fill all of them
3. Click **Apply** to push changes live to the current tab immediately
4. Click **Save** to persist the settings as a cookie (also applies immediately)
5. Click the reset button (↺) to clear all settings and remove the cookie

The popup has four collapsible sections: **Profile Target**, **Identity**, **Stats**, and **Appearance**.

---

## File Structure

```
roblox-customizer/
├── manifest.json       # Extension manifest (Manifest V3)
├── background.js       # Service worker — session tokens, cookie storage, message relay
├── content.js          # Injected on roblox.com — applies all visual overrides
├── content.css         # Minimal transition styles injected alongside content.js
├── popup.html          # Extension popup UI
├── popup.js            # Popup logic — save, load, apply, reset
└── icons/
    ├── icon16.png
    ├── icon48.png
    └── icon128.png
```

---

### Cookie storage
Config is saved as a cookie named `rbx_customizer_cfg` on `.roblox.com` with:
- 1-year expiry
- `secure: true`
- `sameSite: no_restriction` (so it's available on all roblox.com subdomains)
- There is also addicional cookie "was_Activated", but for now it's not used for anything.

Because cookies are accessible to the page's own JavaScript, the actual read/write is handled exclusively by the background worker (which holds the `cookies` permission), not by the content script directly.

### Content script (`content.js`)
Runs on every `*.roblox.com` page. Uses exact CSS selectors derived from Roblox's current DOM structure. Key behaviors:
- Applies immediately on page load, then re-applies at 1.5s and 4s to catch React's late hydration
- Watches for SPA navigation via `MutationObserver` on `pushState` changes and re-boots on route change
- All profile-specific selectors are gated behind a player ID check against the current URL

---

## Permissions Used

| Permission | Why |
|-----------|-----|
| `cookies` | Read and write the config cookie on `.roblox.com` |
| `tabs` | Check whether the active tab is on roblox.com before relaying messages |
| `storage` | Used by the keepalive heartbeat |
| `alarms` | Keep the service worker alive via periodic alarm |
| `host_permissions: *://*.roblox.com/*` | Inject content scripts and access cookies on Roblox |

---

## Disclaimer

This extension makes no changes to your actual Roblox account. It only modifies what you see in your browser using CSS/JS DOM manipulation. Refreshing the page without the extension active will show your real account data. Use it responsibly.

## Changelog
- V1.1.1 - Major bug fixes
- V1.0.0 - first time seeing the sunlight
