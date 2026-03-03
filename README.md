# 🔐 Huawei Config Viewer

<div align="center">

![Huawei Config Viewer](https://img.shields.io/badge/Huawei-Config%20Viewer-00d4ff?style=for-the-badge&logo=huawei&logoColor=white)
![Version](https://img.shields.io/badge/version-1.0.0-brightgreen?style=for-the-badge)
![License](https://img.shields.io/badge/license-MIT-blue?style=for-the-badge)
![No Backend](https://img.shields.io/badge/100%25-Client--Side-orange?style=for-the-badge)
![JavaScript](https://img.shields.io/badge/JavaScript-ES2020-yellow?style=for-the-badge&logo=javascript)

**A professional, browser-based tool to parse, decrypt, and visualize Huawei router backup files (`hw_ctree_*.html`).**

[**🚀 Live Demo**](https://yourusername.github.io/huawei-config-viewer) · [**📖 Blog Post**](https://blog.fayaru.me/posts/huawei_router_config/) · [**🐛 Report Bug**](../../issues) · [**✨ Request Feature**](../../issues)

</div>

---

## 📸 Preview

> Load your `hw_ctree_*.html` backup → instantly get a full decrypted view of your router configuration.

```
📦 Drag & drop backup file
        ↓
🔓 AES-CBC decryption runs in-browser
        ↓
📊 Organized dashboard with all sections
```

---

## ✨ Features

| Feature | Details |
|---|---|
| 🔓 **Full AES-CBC Decryption** | Decrypts all `$2...$` encrypted strings using Huawei's internal key and encoding scheme |
| 📶 **WiFi Networks** | SSID, passwords (decrypted), channel, security mode, TX power |
| 🌐 **PPPoE / ISP Credentials** | Username and decrypted password for your ISP connection |
| 🔑 **System Users** | Web admin and CLI users with their password hash modes |
| 🔌 **LAN / DHCP** | IP ranges, gateway, DNS, MTU, ACL access settings |
| 📱 **Connected Devices** | Full history of all MAC addresses that ever connected |
| 🖥️ **TR-069 / ACS** | Remote management server credentials (decrypted) |
| ⚙️ **System Info** | Firmware version, uptime stats, ISP/operator info |
| 📄 **Raw XML** | View and download the original configuration file |
| 💻 **100% Client-Side** | No server, no uploads, no tracking — everything runs in your browser |

---

## 🔐 Encryption Details

Huawei routers (EchoLife series, HG8xxx, etc.) encrypt sensitive fields in their backup files using **AES-128-CBC** with a custom encoding scheme. This tool fully implements the decryption:

```
Encrypted string format:  $2<encoded_data>$

Decryption steps:
1. Strip prefix "$2" and suffix "$"
2. Decode via HW_AES_AscUnvisible (pairs of chars offset from 0x23)
3. Extract last 20 bytes → reduce to 16-byte IV via HW_AES_PlainToBin (XOR fold)
4. Key: "Df7!ui%s9(lmV1L8" (AES-128-CBC)
5. Decrypt remaining data with PKCS7 padding
```

> **Reference:** [Huawei router config encryption writeup](https://blog.fayaru.me/posts/huawei_router_config/) by [@andreluis034](https://github.com/andreluis034)  
> **Original utility:** [huawei-utility-page](https://github.com/andreluis034/huawei-utility-page)

### Password Hash Modes

| Mode | Algorithm | Reversible |
|---|---|---|
| `0` | Plain text | ✅ Yes |
| `1` | MD5(password) | ❌ No |
| `2` | SHA256(MD5(password)) | ❌ No |
| `3` | PBKDF2-SHA256 (5000 iterations) | ❌ No |
| `$2...$` | AES-128-CBC encrypted | ✅ **Yes** |

---

## 🚀 Usage

### Option 1 — GitHub Pages (Recommended)
Visit the live demo at: `https://yourusername.github.io/huawei-config-viewer`

### Option 2 — Local
```bash
git clone https://github.com/yourusername/huawei-config-viewer.git
cd huawei-config-viewer
# Open index.html in any modern browser
open index.html         # macOS
xdg-open index.html     # Linux
start index.html        # Windows
```

### Option 3 — Direct Download
Download [`index.html`](index.html) and open it directly — no installation needed.

---

## 📁 Project Structure

```
huawei-config-viewer/
├── index.html              # Main application (self-contained)
├── README.md               # This file
├── SECURITY.md             # Security policy and responsible disclosure
├── CHANGELOG.md            # Version history
├── LICENSE                 # MIT License
├── .github/
│   ├── ISSUE_TEMPLATE/
│   │   ├── bug_report.md
│   │   └── feature_request.md
│   └── workflows/
│       └── deploy.yml      # GitHub Pages auto-deploy
└── docs/
    ├── encryption.md       # Deep dive into Huawei's encryption
    └── compatibility.md    # Tested router models
```

---

## 🛡️ Compatible Devices

Tested with backup files from the following Huawei ONT/Router models:

| Model | Firmware | Status |
|---|---|---|
| EchoLife HG8247H | V500R019 | ✅ Tested |
| EchoLife HG8247Q | V500R019 | ✅ Tested |
| EchoLife HS8247W | V300R016 | ✅ Tested |
| EchoLife HG8245H | V300R018 | ✅ Tested |
| EchoLife HG8145V | V300R016 | ⚠️ Partial |

> Have a model not listed? Open an issue with your results!

---

## 🏗️ Technical Stack

- **Vanilla JavaScript** (ES2020) — zero framework dependencies
- **CryptoJS 4.1.1** — AES decryption (loaded from CDN)
- **Google Fonts** — Syne + JetBrains Mono
- **DOMParser API** — XML parsing
- **Clipboard API** — one-click copy

---

## ⚠️ Legal & Ethical Notice

This tool is intended for:
- Network administrators managing their own equipment
- Security researchers analyzing router firmware
- ISP technicians troubleshooting customer devices
- Educational purposes

**Do not use this tool to access devices you do not own or have explicit permission to analyze.**

---

## 🤝 Contributing

Contributions are welcome! Please read the guidelines:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Commit your changes: `git commit -m 'feat: add amazing feature'`
4. Push to the branch: `git push origin feature/amazing-feature`
5. Open a Pull Request

---

## 📄 License

Distributed under the MIT License. See [`LICENSE`](LICENSE) for more information.

---

## 🙏 Credits

- Encryption research by [André Brandão (@andreluis034)](https://github.com/andreluis034) — [writeup](https://blog.fayaru.me/posts/huawei_router_config/)
- AES implementation via [CryptoJS](https://github.com/brix/crypto-js)

---

<div align="center">
Made with ☕ for network engineers and security researchers
</div>
