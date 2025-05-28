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

The `notarize.js` script is as follow
```js
const { notarize } = require('@electron/notarize');
const { build } = require('../../package.json');

exports.default = async function notarizeMacos(context) {
  const { electronPlatformName, appOutDir } = context;
  console.log('[notarize] Platform:', electronPlatformName);
  console.log('[notarize] App output directory:', appOutDir);

  if (electronPlatformName !== 'darwin') {
    console.log('[notarize] Skipping notarization. Not running on macOS.');
    return;
  }

  console.log('[notarize] Running in CI:', process.env.CI);
  if (process.env.CI !== 'true') {
    console.warn('[notarize] Skipping notarizing step. Not running in CI.');
    return;
  }

  const { APPLE_ID, APPLE_ID_PASS, APPLE_TEAM_ID } = process.env;
  if (!APPLE_ID || !APPLE_ID_PASS) {
    console.warn('[notarize] Skipping notarizing step. APPLE_ID and APPLE_ID_PASS must be set.');
    return;
  }

  console.log('[notarize] APPLE_ID:', APPLE_ID);
  console.log('[notarize] APPLE_TEAM_ID:', APPLE_TEAM_ID || '(not set)');
  console.log('[notarize] Using appId from package.json:', build.appId);

  const appName = context.packager.appInfo.productFilename;
  const appPath = `${appOutDir}/${appName}.app`;
  console.log('[notarize] App name:', appName);
  console.log('[notarize] App path:', appPath);

  const notarizeConfig = {
    tool: 'notarytool',
    appBundleId: build.appId,
    appPath,
    appleId: APPLE_ID,
    appleIdPassword: APPLE_ID_PASS,
  };

  if (APPLE_TEAM_ID) {
    notarizeConfig.teamId = APPLE_TEAM_ID;
    console.log('[notarize] teamId added to config');
  }

  try {
    console.log('[notarize] Starting notarization with config:', notarizeConfig);
    await notarize(notarizeConfig);
    console.log('[notarize] Notarization complete!');
  } catch (error) {
    console.error('[notarize] Notarization failed:', error);
    throw error; // re-throw to fail the build
  }
};
```

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
