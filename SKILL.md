---
name: notarycentral-embed-setup
description: Set up or troubleshoot the NotaryCentral RON customer signing experience on WordPress or a developer-managed website, including iframe routing, account settings, and invitation testing. Use for embedding the signer journey; booking-only widgets use a separate integration.
---

# NotaryCentral embed setup

Help the user host the NotaryCentral signer journey inside a page on their website. Match the instructions to their website platform and verify observable results before reporting success.

## Sources and scope

Read the relevant current source before generating platform-specific code:

- [WordPress setup](https://www.notarycentral.org/embed-notarycentral-wordpress): published page, Custom HTML, and iframe snippet.
- [General setup](https://www.notarycentral.org/embed-customer-experience): routing, account configuration, and signer flow.
- [React/Vite sample](https://github.com/NotaryCentral/embedded-experience-sample-code): developer-managed implementation.
- [Account settings](https://app.notarycentral.org/search/settings/embed-customer-experience): authenticated settings page, under **RON customer signing experience**.

The live settings UI and current implementation resolve conflicting documentation. Some guide versions mention **Check my page**, **Send myself a test invitation**, or **Signing page URL (your website)**. Do not instruct users to use a control unless it exists in their UI. The verified form uses **RON embedder page URL**, **RON allowed origin**, **Enable embed**, and **Save**. Use a real signer invitation for testing when no dedicated test control is available.

If a source cannot be accessed, state that limitation and use the verified routing contract below. Ask for a redacted field label or error only when needed. Do not invent newer controls or claim to have inspected an authenticated page you could not access.

## Choose the shortest setup path

Determine the platform, public signing-page URL, and whether the user can publish both an iframe and JavaScript. Reuse information already supplied. For WordPress, start with its page editor and Custom HTML approach; introduce React/Vite only when the user is using that stack or needs a separate hosted app.

Before code, explain these three values with the user's domain when known:

| Value | Example | Where it belongs |
| --- | --- | --- |
| Public parent page | `https://example.com/notary-signing/` | **RON embedder page URL** |
| Parent origin | `https://example.com` | **RON allowed origin**, if configured |
| NotaryCentral app origin | `https://app.notarycentral.org` | Code that builds the iframe destination; `VITE_IFRAME_BASE_URL` in the sample |

The allowed origin has no path, query, or fragment; scheme, hostname, and port must match the final public page. `www.example.com` and `example.com` differ. The parent page setting has no query, fragment, credentials, or signer-specific route. Use HTTPS in production. `VITE_IFRAME_BASE_URL` is a sample-app environment variable, not an account-settings field. Replace the app origin only when the user's official NotaryCentral deployment requires it.

## Build the parent page

### WordPress

Create a public page such as `/notary-signing/`, preferably with space for the full signing interface. Use the current guide's dynamic HTML/JavaScript snippet in a **Custom HTML** block, or adapt it into the site's existing plugin/template when page content cannot retain scripts. An ordinary WordPress Embed block or a fixed signer URL is insufficient.

Verify the published page, not only its editor preview: confirm that the iframe and script survived publishing. If WordPress strips them, inspect the editor's code permissions, hosting/plan capabilities, and security plugins. Check current WordPress requirements before recommending a plan change; do not assume every WordPress installation permits arbitrary code.

### Developer-managed site

Adapt the official sample to the existing stack. Set its app base URL explicitly for production; the sample's localhost default is for development. Keep the parent origin and app origin distinct. Handle the current location on page load and navigation changes that affect `nc_path`, including payment returns when applicable.

### Routing contract for either path

- NotaryCentral redirects an invitation to the configured parent page with `nc_embed=1` and an encoded `nc_path`.
- Read `nc_path` with `URLSearchParams`; it returns the decoded value. Do not decode it a second time or discard its internal query parameters.
- Resolve that route against the configured, trusted NotaryCentral app origin. Require the resulting URL to have that exact origin and a `/customer/` pathname before assigning it to the iframe. Do not load arbitrary URLs supplied in the query string.
- Without `nc_path`, show a message asking the signer to use their invitation link. This placeholder is expected when opening the parent page directly.
- Preserve the invitation query through redirects, caching, and page navigation. Never hard-code one signer's customer URL in the iframe.
- Allow camera and microphone in the iframe. Preserve the current official snippet's other required permissions and check the host's Permissions Policy and Content Security Policy when blocked. Avoid adding a restrictive sandbox without testing the signer flow.
- Do not log full invitation URLs or send their token-bearing query strings to analytics. Use synthetic routes for examples; redact real signer tokens in diagnostics.

## Configure and verify

1. Keep embedding disabled while preparing the public page. Save its URL and, when used, the matching parent origin in the RON section of the settings page.
2. Open the public parent page directly. Confirm its invitation-link placeholder and inspect the published iframe/script. A placeholder alone proves only that the page is present.
3. When ready to test routing, enable embedding and save. Explain that this account setting affects signer invitations; coordinate an appropriate test window if the account is already serving signers.
4. Use an authorized test RON signing with an email address the tester controls. Send a fresh signer invitation through the normal workflow and open it in a private browser window. Do not promise this test avoids creating a signing session, identity checks, or applicable charges.
5. Confirm the address bar shows the parent page with `nc_embed=1` and `nc_path`, while the iframe destination is on the trusted NotaryCentral app origin at `/customer/...`.
6. Run the system check, test camera/microphone, continue through applicable identity or prerequisite steps, and confirm entry to the signing room. Test the browsers/devices relevant to the user's signers.

Do not request account passwords, API secrets, or full invitation links. The user can open their invitation themselves or provide redacted diagnostics. Create sessions, send invitations, publish pages, or change account settings only within the user's authorized scope; otherwise provide the prepared code and exact next action.

Report which checks were actually observed. Distinguish a published page, working invitation routing, and completed signer-flow verification. If a check fails, stop live rollout, identify the failing boundary, and offer restoration of the prior embed settings within the user's authorization.

## Diagnose the failing boundary

| Symptom | Next useful check |
| --- | --- |
| Direct page shows the invitation message | Expected without `nc_path`; test the original invitation next. |
| Invitation still shows that message | Check that embedding was enabled/saved and the final parent URL retained `nc_path`; inspect redirects or caching. |
| Iframe/script missing after publishing | Inspect WordPress permissions, hosting capabilities, and code-stripping plugins. |
| Blank or blocked iframe | Compare final origins, HTTPS, browser console, host `frame-src`/`script-src`, and app framing restrictions. |
| Wrong customer step | Inspect preserved route/query locally; use a fresh invitation and remove any hard-coded session destination. |
| Camera/microphone blocked | Check iframe `allow`, browser permission, HTTPS, host Permissions Policy, and email-app embedded browsers. |
| Payment return leaves an old screen | Confirm the parent reads the returned `nc_path` on load/navigation and updates the iframe. |

Ask for the smallest redacted evidence that distinguishes these cases. A screenshot of the parent domain alone does not establish that the iframe or signer flow works.
