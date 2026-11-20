# SiteLock Browser

A minimal Android WebView browser with a **site lock switch** for watching videos without
pop-ups, pop-up tabs, or redirect-away ads interrupting playback.

## How it works

The app is its own browser — it is **not** a plugin for Chrome. Android does not allow any
app to control tabs or navigation inside Chrome, so this app opens video sites in its own
WebView where navigation can be controlled.

1. Type the URL of the site where you want to watch a video and tap **Go**.
2. Start the video.
3. Flip **Site lock** ON. From that moment:
   - the current host is remembered;
   - any main-frame navigation to another host (redirect / "leave site") is cancelled;
   - every `window.open` / new window / pop-up is swallowed;
   - requests to known ad/tracker domains are returned empty;
   - JavaScript alert/confirm/prompt dialogs are suppressed;
   - the address bar and reload button are disabled so the page cannot be changed.

The page is never reloaded or paused when the switch changes, so the video keeps playing.
Flip the switch OFF to browse normally again.

## Build with GitHub Actions (no local SDK needed)

A ready-made workflow lives at `.github/workflows/build.yml`.

1. Create a GitHub account if you don't have one.
2. Create a new **public or private** repository (leave it empty — no README/license).
3. On Windows, open this `SiteLockBrowser` folder in File Explorer and press **Ctrl+H**
   once so hidden items are visible. You should now see the `.github` folder and
   `.gitignore` file — both are needed.
4. Select **all the contents** of this folder (**Ctrl+A**) — not the folder itself — and
   drag them onto the upload area on GitHub, in your repo: **Add file → Upload files**.
   The top level of the repo must contain `app`, `build.gradle`, `settings.gradle`, etc.
   directly, not a nested `SiteLockBrowser` folder.
5. Scroll to the bottom, type a commit message such as `SiteLock Browser`, and click
   **Commit changes**.
6. Check that `.github/workflows/build.yml` now exists in the repo. If it is missing
   (some browsers skip hidden folders when dragging), add it by hand: **Add file →
   Create new file**, enter `.github/workflows/build.yml` as the filename (GitHub creates
   the folders), paste the contents of the local `.github\workflows\build.yml` file,
   and commit.
7. Open the repo → **Actions** tab. The **Build Android APK** workflow starts
   automatically (or click **Run workflow** to trigger it manually).
8. When the job finishes (green check), click the run → scroll to **Artifacts** →
   download **SiteLockBrowser-debug-apk**. Unzip it to get `app-debug.apk`.
9. Copy the APK to your phone, enable *Install unknown apps* for your file manager
   or browser, and open the APK to install.

You can also add this badge to your repo README:

```markdown
[![Build Android APK](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/build.yml/badge.svg)](https://github.com/YOUR_USERNAME/YOUR_REPO/actions/workflows/build.yml)
```

## Build locally

This folder is a standard Android Studio project (no third-party libraries).

1. Install Android Studio (Hedgehog or newer) with Android SDK 35 and JDK 17.
2. **File → Open…** and select this `SiteLockBrowser` folder.
3. Let Gradle sync. If asked about the missing Gradle wrapper, accept Android Studio's
   bundled Gradle or choose *Use Gradle from Gradle wrapper* and let it regenerate.
4. Run on a device or emulator: **Run → Run 'app'** (`Shift+F10`).

To build an APK from the command line (with Gradle installed):

```bat
gradle assembleDebug
```

The APK will be at `app\build\outputs\apk\debug\app-debug.apk`.

## Important limitations (please read)

- This cannot modify Google Chrome itself. You browse inside this app instead.
- "Lock to the same site" also blocks **legitimate** off-site links while the switch is on.
  That is intentional — the switch is meant only for "I am watching this video now" moments.
- The host, its parent domain, and its subdomains are allowed. So `www.example.com` allows
  `example.com` and `cdn.example.com`, but blocks `othersite.com`.
- In-page advertising (for example a pre-roll ad embedded by the site itself, or an ad
  served from the same domain as the video) cannot be reliably identified and may still
  appear. The lock stops redirects, pop-ups and known ad-network resources — it is not a
  full ad blocker.
- The ad-domain list lives in `AD_HOSTS` in `MainActivity.java`; add or remove suffixes there.
- Blocked navigation count and the last blocked host are shown at the bottom of the screen.
