# MYOB OAuth Redirect Page

A minimal Azure Static Web App that serves as the redirect URI for the MYOB OAuth 2.0 authorization flow used by the ConnectWise–MYOB Live Integration app.

## Purpose

When a user completes the MYOB login in the embedded WebView2 browser, MYOB redirects to this page with the authorization code and company file identifiers in the query string. The integration app intercepts the navigation before the page loads and extracts the parameters — this page only ever displays if that interception fails, providing a clean fallback message instead of a browser error.

## Contents

```
StaticWebApp/
├── staticwebapp.config.json   # Azure Static Web Apps routing config
├── README.md
└── myob-callback/
    └── index.html             # Redirect landing page
```

## Redirect URI

```
https://<your-static-web-app-domain>/myob-callback
```

Register this URL in the MYOB developer portal at `my.myob.com` under your app's registered redirect URIs.

## Deployment

### Option A — GitHub (recommended)

1. Push this folder to a GitHub repository.
2. In the Azure portal, create a **Static Web App** and connect it to the repository.
3. Set **App location** to `/` and leave API location and output location blank.
4. Azure automatically deploys via GitHub Actions on every push.

### Option B — SWA CLI (manual, no GitHub required)

1. In the Azure portal, create a **Static Web App** and choose **Other** as the deployment source. Copy the deployment token.
2. Install the SWA CLI:
   ```bash
   npm install -g @azure/static-web-apps-cli
   ```
3. Deploy:
   ```bash
   swa deploy ./StaticWebApp --deployment-token <your-token> --env production
   ```

## Post-deployment steps

1. Copy the deployed URL (e.g. `https://purple-rock-abc123.azurestaticapps.net/myob-callback`).
2. Register it as the redirect URI in the MYOB developer portal.
3. Update `AccountRightLiveRedirectUrl` in `MYOBLive\Entities\Constants.vb`.
4. Update the `Host` check in `MYOBLive\Helpers\OAuthLogin.vb` (`WebBDocumentTitleChanged`) to match the new domain.
