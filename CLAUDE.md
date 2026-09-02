# Project notes for Claude

## Goal

Reach **ysl.com directly** from the session and work from the live product page.
Retailer mirrors (Gaudenzi, The Fashion Square) were an acceptable stopgap for
product data, but they are not the goal — keep trying ysl.com first.

## Browser access from the cloud sandbox (hard-won lessons)

### Fix: Chromium fails ALL HTTPS with ERR_CONNECTION_RESET through the agent proxy

The sandbox's egress proxy MITMs TLS and cannot handle Chrome's TLS 1.3
ClientHello (large hello with post-quantum key share). Every site resets —
this is NOT the target site blocking you. The fix is one flag:

```
--ssl-version-max=tls1.2
```

Working Playwright launch recipe (run under `xvfb-run -a node script.mjs`):

```js
import { chromium } from '/opt/node22/lib/node_modules/playwright/index.mjs';

const browser = await chromium.launch({
  headless: false, // headed under Xvfb; headless shell also works once TLS is fixed
  executablePath: '/opt/pw-browsers/chromium-1194/chrome-linux/chrome',
  proxy: { server: process.env.HTTPS_PROXY },
  args: ['--no-sandbox', '--disable-blink-features=AutomationControlled', '--ssl-version-max=tls1.2'],
});
const ctx = await browser.newContext({ ignoreHTTPSErrors: true, locale: 'en-GB', timezoneId: 'Europe/London' });
```

Notes:
- Global playwright is at `/opt/node22/lib/node_modules/playwright` (ESM import
  by absolute path; `NODE_PATH` does not work for ESM).
- Diagnose proxy-side failures with `curl -sS "$HTTPS_PROXY/__agentproxy/status"` —
  `ws_closed_mid_exchange` with ~1.7–1.8KB sent / ~39B received = the TLS 1.3 issue above.

### Still blocked after the TLS fix: Akamai IP-reputation sites

ysl.com, harrods.com, saksfifthavenue.com, leam.com serve 403 ("Access Denied",
AkamaiGHost) or reset the connection because the sandbox's datacenter egress IP
is blocklisted. No browser flag fixes this — it is an IP-level block. Also
blocked from this egress: web.archive.org, html.duckduckgo.com, r.jina.ai's
crawler (blocked by YSL on its own IPs).

What DID work for product data (exact SKU `800332Y16PG1290`):
- Gaudenzi Boutique product page (browser, 200 OK) — photos at
  `img.gaudenziboutique.com/product/152727/...`, price $845
- The Fashion Square UK (WebFetch) — GBP price £620, fit/composition copy
- WebFetch/WebSearch use a different egress than the sandbox proxy; when the
  browser is blocked, try WebFetch and vice versa.

## Repo layout

- `index.html` — self-contained static recreation of the YSL product page for
  the Slim Jeans in Carbon Black Denim (`800332Y16PG1290`), no dependencies.
- Verify changes by loading `file:///home/user/claude/index.html` with the
  Playwright recipe above and screenshotting.
