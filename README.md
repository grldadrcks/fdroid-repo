# Jote F-Droid Repo

Self-hosted F-Droid repository for Jote. GitHub Actions generates the index and deploys it to GitHub Pages automatically on every push.

---

## One-time setup

### 1. Generate the signing keystore

Run this once — store the keystore file somewhere safe (NOT in this repo):

```powershell
keytool -genkey -v -keystore jote-fdroid-key.jks -alias fdroid -keyalg RSA -keysize 4096 -validity 10000
```

Keep note of the passwords you set — you'll need them in step 3.

### 2. Create and configure the GitHub repo

1. Create a new GitHub repo named `fdroid-repo` (can be public or private)
2. Push this folder to it:
   ```powershell
   cd fdroid-repo
   git init
   git add .
   git commit -m "Initial F-Droid repo setup"
   git remote add origin https://github.com/YOUR_USERNAME/fdroid-repo.git
   git push -u origin main
   ```
3. Edit `config.yml` and replace `YOUR_GITHUB_USERNAME` with your actual GitHub username
4. In the repo settings → Pages → set Source to **GitHub Actions**

### 3. Add GitHub Secrets

Go to repo Settings → Secrets and variables → Actions → New repository secret. Add these four:

| Secret name | Value |
|---|---|
| `FDROID_KEYSTORE` | Base64-encoded keystore file (see below) |
| `FDROID_KEYSTORE_PASS` | Keystore password |
| `FDROID_KEY_ALIAS` | `fdroid` (or whatever alias you used) |
| `FDROID_KEY_PASS` | Key password |

To get the base64 value of the keystore:
```powershell
[Convert]::ToBase64String([IO.File]::ReadAllBytes("C:\path\to\jote-fdroid-key.jks")) | Set-Clipboard
```

---

## Publishing a new Jote release

1. Build the signed APK:
   ```powershell
   # In jote/ directory
   npm run build
   npx cap sync android
   cd android
   .\gradlew assembleRelease
   # APK is at: android/app/build/outputs/apk/release/app-release.apk
   ```

2. Copy the APK into this repo's `repo/` folder — rename it to include the version:
   ```
   repo/com.jote.app_1.apk
   ```

3. Commit and push:
   ```powershell
   git add repo/com.jote.app_1.apk
   git commit -m "Add Jote v1.0"
   git push
   ```

GitHub Actions runs automatically and deploys the updated index to GitHub Pages.

---

## F-Droid URL for users

Once deployed, users open F-Droid → Settings → Repositories → + and enter:

```
https://YOUR_GITHUB_USERNAME.github.io/fdroid-repo
```
