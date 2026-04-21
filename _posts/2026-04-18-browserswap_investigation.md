---
title: "BrowserSwap - A Cryptojacking Campaign"
description: "Phishing for Crypto, but with spreadsheets"
date: 2026-04-18
categories: [Research, cybersecurity]
tags: [cybersecurity, malware, phishing, TheProtocolOne]
author: ashlynn
comments: false
published: true
---

# Summary
BrowserSwap is a moderately sophisticated, and actively-deployed, browser-side Bitcoin replacement campaign, targeting three specific vendors (Swapzone, SimpleSwap, and ChangeNOW). The threat actor is currently being tracked by their online Pseudonym: TheOperationOne.
The attack is directed by tricking the victim into running malicious JavaScript snippets in their Chromium-based browsers and replacing their wallet address with a randomly selected attacker-controlled address.
The campaign is notable for its use of Google Sheets as a C2 backend, delivering a multi-stage payload and targeting three major exchange platforms. As of writing this, on 18-04-2026, the infrastructure is confirmed to be active – takedown reports pending.

# Infrastructure and Delivery Chain
## C2 Backend: Google Sheets JSONP
The attacker uses a publicly accessible Google Sheets document served via the Google Visualization JSONP API as a live configuration and payload store. This is technique is generally designed to evade network-based detection by routing the payload retrieval through well-known and rarely blocked domains and obscuring itself within common SaaS traffic. 

The sheet is structured as a key-value rows with the following payload slots:

| Field   | Content                                                                          |
| :------- | :-------------------------------------------------------------------------------- |
| apiv4   | First half of `GsjOWJG` obfuscated payload (XOR key `0x16`) - Targets SimpleSwap |
| apiv5   | Second half of apiv4 payload continuation                                        |
| api_1   | First half of `iRQpZbb` payload (XOR key `0x2A`) - Targets Swapzone/ChangeNOW    |
| api_2   | Second half of api_1 payload continuation                                        |
| API Key | NodeAPI_[REDACTED]                                                               |
| Status  | “API ONLINE” + live datestamp - Confirms active monitoring                       |
| Nodes   | Accessible from Nodes: 1.8, 1.9, 2.0 - Seemingly arbitrary for phishing purposes |

The payloads are split across multiple rows to avoid single-string detection, the sheet was last updated on the 18th of April 2026, the date of discovery, confirming active maintenance.

## Injection Point
The delivery URL embedded in the Swapzone/ChangeNOW payload poses as a legitimate exchange node module:
`hxxps[://]swapzone[.]io/exchange/nodes/changenow/btc/node-1[.]9[.]js`
Paired with fake alert box messages withing the payload are designed to disarm developers or inexperience victims from question the legitimacy of the proposed script:
```
[SUCCESS] Initializing secure node handshake...
[SUCCESS] Loading core module from: hxxps[://]swapzone[.]io/exchange/nodes/changenow/btc/node-1[.]9[.]js
[INFO] This script is part of a developer testing suit.
[INFO] Found issues? Report to support[@]swapzone[.]io
```

## Obfuscation Technique
For each stage of the payloads, there are a few common approaches that cut down a lot of time for reversing the malware:
1. String Array Obfuscation: All string literals are stored as hex-encoded bytes in a bootstrap array (i.e. `jdeeT_yGM`/`zhMBEcMjJjMkoUrLQtFd`).
2. XOR Decode at Runtime: A decode function XORs each byte against a fixed set of keys. For the api_1/2 it was `0x2A (42)` and for apiv4/5 it was `0x16 (22)`.
3. Index Shuffling: An IIFE with a precomputed target sum repeatedly rotates the array until a math integrity check passes, preventing static index mapping.
4. Payload Splitting: Each script is split across two Google Sheet cells, as previously mentioned, and reassembled client-side.
5. Anti-Tamper Check: Unlike the Swapzone/ChangeNOW payload, the SimpleSwap payload contains a hash integrity check (`j_tkeOP`) that alerts and redirects if the script picks up on any tampering. The expected hash is stored as `_0xa1`.

# Attach Behaviour by Target
## Swapzone & ChangeNOW (`api_1` + `api_2`)
### Address Replacement
The payload behind Swapzone/ChangeNOW monitors the offer list an deposit block for any BTC-destined transactions. When a qualifying transaction is detected, the visible destination address is replaced with a randomly selected attacker-controlled address from a pool of 27 known addresses.
- Targets selector: `.styles__offer__2Cquj img[alt="ChangeNOW logo"]`
- Also targets: `.BlocksStyles_titleDepositBlock__2Bju9` (ChangeNOW deposit page)
- Address pool is randomised per-session via `UwsQWYPvtsAgyMq()`
- A single selected address is cached (via `WKoGnNYmNOJEZ()`) and reused for QR/clipboard consistency

### Clipboard Hijacking
Unlike most malware that will hijack the clipboard in an attempt to steal critical information like passwords, OTPs, and emails, BrowserSwap instead intercepts all copy-button click events to replace victim wallet addresses with the attacker-controlled address. A fallback `textarea execoseElement` path handles older clipboard APIs in the event of a failure.

### QR Code Replacement
The legitimate QR code is replaced by one generated from the attacker-controlled address via: `hxxps[://]api[.]qrserver[.]com/v1/create-qr-code/?size=150x150&data=bitcoin:<ATTACKER_ADDR>?amount=<AMOUNT>`

### `fetch()` API Interception
The payloads wraps `window.fetch()` to intercept responses from the exchange’s own backend. Any JSON response containing a wallet address field (identified by path `data.result.address` or `data.result.toAddress`) is silently rewritten in-memory before the UI renders it: 
```js
QcPCQkO_Y[.245][.1f8][.1fa] = WKoGnNYmNOJEZ()  // attacker addr injected into parsed JSON 
```
Essentially meaning that even server-authenticated addresses are replaced before they’re displayed to the victim.

### Amount Inflation Display
The payloads read the USD equivalent of any transaction and forcibly inflate the displayed amount (by approximately +0.26% to +0.30%, via `azpuNVQGcjxShs`) to further enforce a psychological assurance that victim is going to received the advertised increase and to proceed with the transaction as-per-normal.

### Fake Loading and Minimum Threshold
A full-screen `div` with the `z-index` set to `999999` and CSS animation is injected during the swap window to mask DOM manipulation:
```css
position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: ...;
```
This is followed by a small test evasion by arbitrarily setting a minimal send threshold (defined by: `l$xoVazBy`) to 0.0019177 BTC, so it can avoid dynamic analysis or cautious victims. This technique enforces the idea that if a small value gets sent to their wallet, then the same should happen with larger sums of cryptocurrencies.

### MutationObserver Persistence
The payload deploys two MutationObservers to watch the `document.body` for any DOM changes (via: `NkOEgyLKqDrTpn_CbmOwC`, `p_Why$zfLFs`) in the event that there’s any navigation or React re-rendering that could reconstruct the webpage and remove the attacker-controlled address from it’s place. If such events are detected, it will automatically perform an injection again with one of the attacker’s addresses to maintain persistence.

## SimpleSwap (`apiv4` + `apiv5`)
### Target Selectors
- `[data-testid="recipient-address-container"]` - Destination address field
- `[[data-testid="you-get-amount"]` - Receive amount display 
- `[data-testid="copy-icon"]` - Clipboard hijack trigger 
- `[data-testid="exchange-details"]` - Transaction summary panel 
- `[data-testid="exchange-button"]` - Submit intercept 
- `.m_8bffd616.mantine-Flex-root` - Mantine UI component targeting

### API Interception
The payload intercepts the calls to the SimpleSwap exchange API: `/api/v4/exchanges-new`
and parses teh responses and address fields to be rewritten in the same `fetch()` pattern as is used in the prior payload.

### Fake Bonus Banner
A fake “bonus” UI element is injected into the exchange flow to distracted the victim and normalise the unexpected UI changes with: `<span class="bonus-breakdown">(+ bonus)</span>`
The bonus amount shown is computed as the amount * 0.0025 and is designed to appear as a cashback incentive, reducing the suspicion when the victim sees the unexpected UI element.

### Countdown Timer Manipulation
The payload creates an exchange countdown timer to induce urgency into the process. The countdown is controlled by the script (via: `CKtR_UZWsh$YXHULQKJs()`, `MFoQC$KWSXYupc()`), allowing the attacker to extend, reset, or shorten the visible timer independently of the actual exchange window. 

### Blockchain Explorer Link Replacement
The payload modifies the explorer icon link (`[data-testid="blockchain-explorer-icon"]`) to redirected to: `hxxps[://]www[.]blockchain[.]com/explorer/addresses/btc/<ATTACKER_ADDR>`
Allowing the attacker to maintain persistence over the address even if the victim attempts to navigate away, by sending them back to an attacker-controlled address.

# Indicators of Compromise (IOCs)
## Attacker-Controlled BTC Addresses (27 Unique)
All following address should be blacklisted at the exchange level and flagged with blockchain analytics providers (i.e. Chainalysis, Ellipitc, TRM Labs). As of writing this report, no reports have been made to these providers.

| #   | Bitcoin Address                            | Payload            |
| :--- | :------------------------------------------ | :------------------ |
| 1   | bc1qgahr6ghrghsdakn6x8g3sskv2ts9yplgsvcvzj | Both               |
| 2   | bc1qe5lwff6fjlhy5eqjyt6dcrfrzv9hv4rnuur84f | Both               |
| 3   | bc1qagd7vsu78amqehvtljzn5qy4gkx2fvrvtl7qke | Both               |
| 4   | bc1qakvna8jzn379edlx45m2g4chtasxuk42swdw7n | Both               |
| 5   | bc1q5663ft74jver7t2d92qrxhkalhfzvckxagyatf | SimpleSwap         |
| 6   | bc1qh6r2vjn7mhczxarmpg03sjccqev0xp293zk2gq | SimpleSwap         |
| 7   | bc1q0p4rndplcwakcku2zqn23zz87ca03hxs7xpdkr | Both               |
| 8   | bc1qrazutj4e84rcd7epy754azqk0dxsk2h4249z3c | Both               |
| 9   | bc1qu9qswhc7c8n03dz628dtrktvdnhtefmgwh9ejw | Both               |
| 10  | bc1qhtu6lhky6zlfp4x96td39m0x7vrxa955q565rw | Both               |
| 11  | bc1qjjp64vnrpxzwk2em4k0eausl9kwlgurfatgkwf | SimpleSwap         |
| 12  | bc1qwek5zz4877eq7lvslkkz4nncf24zp6ufl3cere | SimpleSwap         |
| 13  | bc1qtc3gw8hqpsy32gefmfjhh2qegp5wf2fke6wuv7 | SimpleSwap         |
| 14  | bc1ql77saeza8haujtx363yrtxvg6z0lex0ahzshcp | Both               |
| 15  | bc1qdjxmcrkdhxzyxpcpk28xumh9md54vfgqck7szx | Both               |
| 16  | bc1qsc9m0cudnsx3sp5u0cs8fc6g0g4wsscwe2qu6g | Both               |
| 17  | bc1qfv75ffrkcarlxplxkjcx4z3tve035t2lustrtu | Both               |
| 18  | bc1qjks48ujaj3e3rv4uvlxjn6t72elazcz9tjtmpw | Both               |
| 19  | bc1qggj2wpx7pl46tvvve3q0u7ev9qlyp3fkh3dgfd | Both               |
| 20  | bc1qm7znny29cld26tlvega8rtlwas75txz8mv9lxz | Swapzone/ChangeNOW |
| 21  | bc1qxey0wp6qhdsaj6uztmycsfp0cylv4xkpzpyz5h | Swapzone/ChangeNOW |
| 22  | bc1qnhxmlmkzrurgq6e6um5nt93fycu88heck6rc9z | Swapzone/ChangeNOW |
| 23  | bc1qrrn9h9ax4sksmar8ye5r7we4gauldxqjxeu497 | Swapzone/ChangeNOW |
| 24  | bc1qazxr0dvgw5j9alu88cpcu6ur9jtr90gva5ttyx | Swapzone/ChangeNOW |
| 25  | bc1qlhpqyg3qcvnx98vs48mhshqjlez2psswafm9l5 | Swapzone/ChangeNOW |
| 26  | bc1q44wyq7urn4f2gyk90fw5qt8ddt8jt9g7dhkhdd | Swapzone/ChangeNOW |
| 27  | bc1q109rr7vc3vtyqlk9ps5rjhqw7le7czjf7yjd6n | SimpleSwap         |


## Network IOCs

| Type              | Indicator                                                | Purpose                  |
| :------------------| :---------------------------------------------------------| :-------------------------|
| C2 (JSONP)        | sheets.googleapis.com                                    | Payload retrieval        |
| C2 (JSONP)        | docs.google.com/spreadsheets/                            | Payload retrieval        |
| Delivery URL      | swapzone.io/exchange/nodes/changenow/btc/node-1.9.js     | Script injection point   |
| API intercepted   | /api/v4/exchanges-new/                                   | SimpleSwap backend       |
| QR abuse          | api.qrserver.com/v1/create-qr-code/                      | Attacker QR generation   |
| Explorer redirect | blockchain.com/explorer/addresses/btc/                   | False verification       |


## Script-Level Signatures
Due to the nature of obfuscated/minified JavaScript, the following table may become obsolete as XOR keys change and payloads are replaced. Use with Caution.

| Pattern                                  | Significance                               |
| :---------------------------------------- | :------------------------------------------ |
| NodeAPI_7fK2mQ9xL4pV8nR1cT6yH3zW5bJ0dN2u | Hardcoded API key present in both payloads |
| `Fc$oittgrRB$JzscNWfquyxE`               | Swapzone/ChangeNOW bootstrap function name |
| `jdeeT_yGM`                              | Swapzone/ChangeNOW string array variable   |
| `GsjOWJG` / `XzddHjXjDwtqkIIaQ`          | SimpleSwap bootstrap function names        |
| `zhMBEcMjJjMkoUrLQtFd`                   | SimpleSwap string array variable           |
| `WKoGnNYmNOJEZ()`                        | Address cache getter (both payloads)       |
| `ODnpBBxbETueJiP$DrM`                    | Address replacement trigger function       |
| `UwsQWYPvtsAgyMq()`                      | Random address pool selector               |
| `.{1,2}/g` + XOR decode pattern          | Hex-encoded string decode loop             |
| transaction-loading-overlay              | Injected overlay element ID                |
| btc-bonus-modal                          | Injected fake bonus modal ID               |
| `azpuNVQGcjxShs = 0.26`                  | Amount inflation constant                  |
| `l$xoVazBy = 0.0019177`                  | Minimum BTC threshold constant             |
| `sYYeVRYVGYWfpmTn = 0.44`                | Amount scaling constant                    |


# Detection Guidance
## EDR/AV YARA Rule
The following YARA rule targets the obfuscation skeleton common to both payloads. As with the prior IOCs, some of these values are subject to change as the payloads evolve or get redistributed.

```
rule CryptoAddrHijack_TheProtocolOne { 
  meta: 
    description = "Browser-side BTC address replacement - Swapzone/ChangeNOW/SimpleSwap" 
    date = "2026-04-18" 
    tlp = "CLEAR" 
  strings: 
    $api_key   = "NodeAPI_7fK2mQ9xL4pV8nR1cT6yH3zW5bJ0dN2u" 
    $fn1       = "Fc$oittgrRB$JzscNWfquyxE" 
    $fn2       = "XzddHjXjDwtqkIIaQ" 
    $arr1      = "jdeeT_yGM" 
    $arr2      = "zhMBEcMjJjMkoUrLQtFd" 
    $overlay   = "transaction-loading-overlay" 
    $modal     = "btc-bonus-modal" 
    $threshold = "0.0019177" 
    $xor_loop  = { 2E 7B 31 2C 32 7D 2F 67 } // ".{1,2}/g" 
    $addr_fn   = "ODnpBBxbETueJiP$DrM" 
  condition: 
    any of ($api_key, $fn1, $fn2, $arr1, $arr2) or 
    2 of ($overlay, $modal, $threshold, $xor_loop, $addr_fn) 
} 
```
{: file="YARA" :}

## Network Detection
- Alert on outbound requests to sheets[.]googleapis[.]com or docs[.]google[.]com[/]spreadsheets originating from a browser JavaScript context on exchange-adjacent domains 
- Alert on `fetch()` calls to api.qrserver.com containing bitcoin: in the query string from crypto exchange pages 
- Alert on any of the 27 BTC addresses above appearing in outbound HTTP(S) request parameters 
- Alert on `navigator.clipboard.writeText()` being called from a non-user-gesture context (`fetch().then()` chain) 

## Exchange-Side Detection
- Monitor for address display mismatches between the confirmed server response and the value rendered in the DOM 
- Implement Subresource Integrity (SRI) on all first-party JS bundles 
- Add a Content Security Policy that blocks `eval()`, inline script execution, and restricts `fetch()` origins 
- Implement a server-side address echo visible to the user that cannot be overwritten by client JS (e.g., in page title or HTTP header displayed by browser) 
- Rate-limit and monitor requests to `/api/v4/exchanges-new/` for anomalous patterns

# Appendix - Decode Methodology:
Both payloads were decoded using the following approach:
1. Extract the hex-string array from the bootstrap function.
2. Evaluate the XOR key constants, resolving to `0x2A` for api_1 and `0x16` for apiv4
3. Apply XOR decode for each hex pair (`parseInt(hex, 16) ^ key)`, then `String.fromCharCode()`)
4. Map decoded strings back to indexed references to reconstruct function logic

```js
// Partial deobfuscation logic (3-4).
function decodeChunk(hex, key = 42 /* or 0x16 for apiv4 */) {
    const bytes = hex.match(/.{1,2}/g).map(b => parseInt(b, 16));
    return String.fromCharCode(...bytes.map(b => b ^ key));
}

const stage1 = jdeeT_yGM.map(s => decodeChunk(s)).join('');
const stage2 = ...; // Replace with the needed map for apiv4
console.log(stage1);
console.log(stage2);
```
