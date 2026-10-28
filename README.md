# QR Shield 🛡️
> Pre-Scan Threat & Phishing Inspector for Students & Everyday UPI Users.

**QR Shield** is an open-source, client-side cybersecurity tool built with vanilla HTML5, CSS3, and JavaScript. It helps users inspect QR codes, URLs, and UPI payment payloads **before** trusting them, neutralizing phishing traps, credential theft, and UPI payment frauds.

---

## ⚡ Key Features

- **Triple Input Mode**:
  - **Upload Image**: Drag and drop or browse QR code photos/screenshots.
  - **Live Camera Scanner**: Real-time scanning using web camera video feed (`getUserMedia`).
  - **Clipboard Paste**: Press `Ctrl + V` anywhere on the page to paste a screenshot or snippet.
- **Zero-Network Architecture**:
  - All decoding happens entirely in-browser using local `jsQR.js`.
  - **The inspected URL is NEVER visited, fetched, or pinged.**
  - Decoded links are displayed in a secure preview box with safe copy functionality.
- **Dedicated UPI Payment Handling (`upi://pay`)**:
  - Shows a neutral blue **Payment QR** verdict (never Danger/red just for being UPI).
  - Clear guidance: *"This is a UPI payment QR. Scanning it is used to SEND money, never to receive money."*
  - Prominent reminder: *"Only pay if you trust this person or business. Check that the payee name matches who you expect before entering your UPI PIN."*
  - Parsed card showing: Payee Name (`pn`), Payee UPI ID (`pa`), Amount (`am`, formatted with ₹), and Note (`tn`). Missing fields show *"Not provided"*.
  - Pre-filled amount alert: *"This QR asks you to pay ₹X. Make sure that is the amount you expect."*
  - Yellow "Be careful" note for scam lure keywords (`reward`, `claim`, `lottery`, `refund`, `kyc`) without turning the main verdict red.
- **WiFi Network Configuration Handling (`WIFI:`)**:
  - Shows a neutral blue **WiFi QR** verdict (never runs web/URL checks like HTTP, IP address, or domain resolution).
  - Clear guidance: *"Scanning this QR will connect your phone to this WiFi network. Only join networks you trust, such as your home, college or a place you know."*
  - Parsed card showing: Network Name (`S`), Security Type (`T`), Hidden status (`H`), and Password (`P`).
  - Passwords masked by default (`••••••••`) with a "Show password" toggle to avoid accidental screen exposure.
  - Plain-language security warnings for open networks (`nopass`) and weak legacy protocols (`WEP`).
- **Explainable Rule-Based Web Heuristics**:
  - Transparent rule checks for web links with single-sentence plain language explanations:
    - Insecure HTTP protocol
    - Numerical IP address as host
    - URL shorteners concealing destinations
    - `@` credential obfuscation
    - Excessive subdomain levels
    - Punycode & homoglyph spoofing (`xn--`)
    - Phishing lure keywords

---

## 🚀 How to Run

1. Open File Explorer and navigate to:
   ```
   c:\Users\gadiy\Downloads\hackathon
   ```
2. Open `index.html` in **Google Chrome** (or any modern browser):
   - Double-click `index.html`, OR
   - Run in PowerShell:
     ```powershell
     Start-Process "chrome.exe" "index.html"
     ```
   - Alternatively, start a local test server:
     ```bash
     python -m http.server 8080
     ```
     and open `http://localhost:8080` in Chrome.

---

## 📋 Test Checklist: Fictional UPI Payloads

Test these by pasting with `Ctrl + V` or generating/uploading test QR images:

### 1. Standard Merchant UPI with Amount
- **Payload**: `upi://pay?pa=demo.shop@fakebank&pn=Demo%20Shop&am=250&cu=INR`
- **Expected Verdict**: Neutral Blue **Payment QR**
- **Payee Name (pn)**: `Demo Shop`
- **Payee UPI ID (pa)**: `demo.shop@fakebank`
- **Amount (am)**: `₹250`
- **Note (tn)**: `Not provided`
- **Pre-filled Amount Info**: *"This QR asks you to pay ₹250. Make sure that is the amount you expect."*
- **Scam Note**: None

### 2. Peer-to-Peer UPI without Amount
- **Payload**: `upi://pay?pa=demo.person@fakebank&pn=Demo%20Payee`
- **Expected Verdict**: Neutral Blue **Payment QR**
- **Payee Name (pn)**: `Demo Payee`
- **Payee UPI ID (pa)**: `demo.person@fakebank`
- **Amount (am)**: `Not provided`
- **Note (tn)**: `Not provided`
- **Pre-filled Amount Info**: None (customer enters amount in their UPI app)
- **Scam Note**: None

### 3. Pre-filled Amount with Suspicious Lure Note
- **Payload**: `upi://pay?pa=demo.person@fakebank&pn=Demo%20Payee&am=5000&cu=INR&tn=Claim%20reward`
- **Expected Verdict**: Neutral Blue **Payment QR** (Verdict remains "Payment QR", NOT Danger!)
- **Payee Name (pn)**: `Demo Payee`
- **Payee UPI ID (pa)**: `demo.person@fakebank`
- **Amount (am)**: `₹5000`
- **Note (tn)**: `Claim reward`
- **Pre-filled Amount Info**: *"This QR asks you to pay ₹5000. Make sure that is the amount you expect."*
- **Separate "Be Careful" Note (Yellow)**:  
  *"⚠️ Be careful: The note or payee name contains words commonly associated with scams (reward, claim). Scammers often promise rewards, refunds, or KYC verification to trick people into sending money."*

---

## 📶 Test Checklist: Fictional WiFi Payloads

### 1. Modern WPA Network with Password
- **Payload**: `WIFI:S:Demo-Cafe;T:WPA;P:fakepass123;H:false;;`
- **Expected Verdict**: Neutral Blue **WiFi QR**
- **Network Name (SSID)**: `Demo-Cafe`
- **Security Type**: `WPA`
- **Hidden Network**: `No (Visible network)`
- **Password**: Masked as `••••••••••••` by default with toggle button *"👁️ Show password"* (reveals `fakepass123`)
- **Guidance**: *"Scanning this QR will connect your phone to this WiFi network. Only join networks you trust, such as your home, college or a place you know."*

### 2. Open Network (No Password)
- **Payload**: `WIFI:S:Free-Open-WiFi;T:nopass;;`
- **Expected Verdict**: Neutral Blue **WiFi QR**
- **Network Name (SSID)**: `Free-Open-WiFi`
- **Security Type**: `nopass`
- **Hidden Network**: `No (Visible network)`
- **Password**: `No password (Open network)`
- **Warning Callout**: *"⚠️ This network has no password, so other people on it may be able to see your traffic."*

### 3. Legacy Weak Protocol (WEP) & Hidden Network
- **Payload**: `WIFI:S:OldRouter;T:WEP;P:fakepass123;H:true;;`
- **Expected Verdict**: Neutral Blue **WiFi QR**
- **Network Name (SSID)**: `OldRouter`
- **Security Type**: `WEP`
- **Hidden Network**: `Yes (Hidden network)`
- **Password**: Masked as `••••••••••••` with toggle button
- **Warning Callout**: *"⚠️ WEP is an outdated, weak security type."*

---

## 🔒 Heuristic Disclaimer
*QR Shield uses client-side heuristic pattern checks. While it helps users spot common fraudulent patterns, heuristics cannot guarantee 100% safety. Users should always independently confirm payee identities before entering their UPI PIN.*
