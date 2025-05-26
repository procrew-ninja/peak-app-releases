# 🖥️ Desktop Build Instructions

## 🍏 macOS

### ✅ Preparation

Before building, make sure you have the following:

- **Apple ID**
  Your Apple account email.

- **Team ID**
  The alphanumeric string in parentheses next to your team name on the Apple Developer portal.

- **Developer Certificate**
  1. Visit [Apple Developer Certificates](https://developer.apple.com/account/resources/certificates/list).
  2. Generate a certificate of type: `Developer ID Application: <Team Name> (TEAM ID)`.
  3. Download the certificate.
  4. Double-click the file to install it.
  5. Export it as a `.p12` file with a secure password.
  6. Save both the exported file and the password securely.

- **App-Specific Password**
  1. Go to [appleid.apple.com](https://appleid.apple.com).
  2. Scroll down to **App-Specific Passwords**.
  3. Generate a new password and save it for later use.

### 🧱 Build Steps

1. Create a script file named `build.sh` and place all your build commands inside it.
2. Ensure you have a `notarize.js` file similar to the one in your repository.
3. In your `package.json`, make sure to reference `notarize.js` in the `afterSign` section.

To build the app, run:

```bash
./build.sh
```

After a short wait, a .dmg file will be generated. That’s your signed macOS build.

## 🪟 Windows

### 🛠️ Build Steps

1. Add your `GH_TOKEN` to the `publish.sh` script file.
2. Ensure `nsis` is included as a target in your `package.json`.

Then run:

```bash
./publish.sh
```

This will generate and publish your Windows build.

Let me know if you'd like to include signing instructions or CI/CD automation for Windows as well.
