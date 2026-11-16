# 🚀 Google Play Store Deployment & Release Runbook

This guide covers building, signing, and submitting **RelayMate** to the Google Play Store, including Data Safety answers and Regional Pricing (PPP).

---

## 📦 1. Production Artifacts & Keystore Credentials

* **Release Bundle Location**: `app/build/outputs/bundle/release/app-release.aab` (**9.3 MB**)
* **Keystore File**: `keystore/release-key.jks`
* **Keystore Properties**: `keystore.properties` (secured in `.gitignore`)

### Keystore Metadata:
```text
Alias:       forwarderhub
Key Alg:     RSA 2048-bit
Validity:    10,000 days
Certificate: SHA384withRSA
DName:       CN=RelayMate, OU=Mobile App, O=RelayMate, L=San Francisco, ST=California, C=US
```

### How to Rebuild the Release Bundle:
```bash
cd /Users/sagarpabba/Source_Code/WIP/MessageForwarder
export JAVA_HOME="/Applications/Android Studio.app/Contents/jbr/Contents/Home"
./gradlew bundleRelease --no-daemon
```

---

## 🌍 2. Google Play In-App Billing: Regional Pricing (PPP)

RelayMate integrates `com.android.billingclient:billing-ktx:7.0.0` with support for Purchasing Power Parity.

In Google Play Console $\rightarrow$ **Monetize** $\rightarrow$ **Products** $\rightarrow$ **In-app products**, create:

### Product 1: `pro_lifetime` (One-Time In-App Purchase)
* **Product ID**: `pro_lifetime`
* **Name**: `Pro Lifetime Unlock`
* **Description**: `Unlimited message forwarding, OTP filter rules, and zero ads forever.`
* **Pricing Strategy**:
  * 🇺🇸 **United States / EU / UK / CA**: `$19.99` / `€19.99` / `£17.99`
  * 🇮🇳 **India**: `₹199` (~$2.40)
  * 🇧🇷 **Brazil**: `R$ 14.99` (~$2.60)
  * 🇳🇬 **Nigeria / Africa**: `₦ 3,500` (~$2.30)
  * 🇮🇩 **Indonesia / SEA**: `Rp 39,000` (~$2.50)

### Product 2: `pro_monthly` (Recurring Subscription)
* **Product ID**: `pro_monthly`
* **Base Plan**: Monthly auto-renewing
* **Pricing Strategy**:
  * 🇺🇸 **US / Tier 1**: `$1.99 / month`
  * 🇮🇳 **India**: `₹49 / month` (~$0.60)
  * 🌍 **Emerging Markets**: `~$0.75 / month`

> [!TIP]
> The app dynamically queries Google Play via `BillingManager.kt`. The exact localized price and currency symbol are auto-detected for each user without code modifications.

---

## 🛡️ 3. Google Play Data Safety Questionnaire Answers

Because RelayMate employs a strict **Zero-Server Architecture**, your Data Safety submission is simple and fast-tracked:

| Question | Answer | Details / Justification |
| :--- | :---: | :--- |
| **Does your app collect or share user data?** | **Yes** | Messages are transmitted between devices; AdMob collects standard Advertising ID. |
| **Does your app contain ads?** | **Yes** | Rewarded videos and adaptive banners for free-tier users; 100% ad-free for Pro users. |
| **Is all user data encrypted in transit?** | **Yes** | All data in transit uses TLS 1.3 and client-side AES-256-GCM. |
| **Do you provide a way for users to request data deletion?** | **Yes** | Tapping "Clear Log" purges all local records. Uninstalling wipes all data permanently. |
| **Data Type: Messages (SMS, text messages)** | **Collected (Ephemeral)** | Transmitted directly device-to-device via FCM or webhooks. **NOT stored on any developer server.** |
| **Data Type: Device or other identifiers (Advertising ID)** | **Collected (AdMob)** | Used solely by Google Mobile Ads SDK for ad serving and fraud prevention on the free tier. |
| **Is this data collected, shared, or both?** | **Collected** | Core app functionality (messages) & Advertising (free tier ads). |
| **Is this data processed ephemerally?** | **Yes** | Message data is held only in volatile memory during transmission; no persistent developer server storage. |

---

## 📝 4. Store Listing Metadata & ASO Copy

### App Title:
`RelayMate: Forward SMS & RCS` (28 characters)

### Short Description:
`Privately auto-forward SMS & RCS, bank alerts, and OTPs to your other device.` (77 characters)

### Full Description (Excerpts for Store Listing):
```text
RelayMate is the warm, private, zero-server message companion designed for family caregivers, dual-phone users, and professionals.

Safely auto-forward SMS & RCS messages, bank alerts, and missed calls from a source phone (e.g. parent's phone or secondary device) directly to your primary phone, Telegram, Discord, or desktop browser.

⭐ KEY FEATURES:
• 100% Zero-Server Architecture: No developer database. Your messages are never collected, stored, or sold.
• Zero Sensitive Permissions: Fully compliant with Google Play policies without requiring Default SMS Handler roles.
• Smart OTP Detection & Auto-Copy: Surfaces a 1-tap [📋 Copy OTP] notification action or auto-copies directly to your clipboard.
• Wear OS & Smartwatch Ready: View large, legible verification codes with a 1-tap confirmation action on your wrist.
• Client-Side E2EE: Payloads are encrypted with AES-256-GCM using keys exchanged offline via QR code.
• Multi-Receiver Hub: Concurrently forward one sender device to up to 3 companion phones.
• Dynamic App Launcher Shortcuts: Long-press the app icon to view latest OTP, toggle relay, or run diagnostics.
• Smart Channel Routing Matrix: Route sensitive OTPs strictly to encrypted FCM while sending general notifications to Webhooks.
• Live Ringing Call Alerts: Real-time incoming call notice while the phone is ringing before it becomes a missed call.
• Ephemeral OTP Auto-Dismiss: Expired OTP notifications automatically disappear from your shade after 5 minutes.
• Scheduled Quiet Hours (DND): Mute off-hours forwarding while keeping critical bank OTPs exempted.
• Senior-Proof Screen Lock: Protects parents from confusing technical screens with a warm, single-card display.
• Remote Phone Finder: Trigger an audible chime at max volume to locate a misplaced phone even if on Silent.
• Dual-SIM Identification: Automatically badges messages arriving on SIM 1 vs SIM 2.
• Desktop Web Companion: View incoming OTPs live in Chrome/Safari on your PC or Mac over home Wi-Fi.
• Multi-Channel Support: Connect directly to Telegram, Discord, Slack, Microsoft Teams, or custom webhooks.
• Diagnostics & Health Check: 1-tap audit to verify network, battery optimization, and relay status.
• Remote Battery Guardian: Receive automated warnings when the sender phone drops to 15% battery.

PRIVACY GUARANTEE:
All notification processing runs 100% locally on your device. We do not operate external databases or central servers.
```

