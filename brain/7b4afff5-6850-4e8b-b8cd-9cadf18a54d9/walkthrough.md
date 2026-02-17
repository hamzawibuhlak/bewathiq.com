# WathiqApp — App Store Preparation Walkthrough

## Changes Made

### 1. Client Form Redesign (Mobile)
Completely rewrote `CreateClientScreen.tsx` and `ClientDetailsScreen.tsx` with Company/Individual differentiation:

| Feature | Individual | Company |
|---|---|---|
| Name field | ✅ Full name | ✅ Company name |
| Identity doc | ✅ National ID + upload | ❌ |
| Commercial Reg | ❌ | ✅ Number + upload |
| National Address | ❌ | ✅ Required upload |
| Representative | ❌ | ✅ Full section |
| Doc type chips | ❌ | ✅ 4 types |
| Document upload | ✅ react-native-document-picker | ✅ |

### 2. App Renamed
Display name changed from **WathiqMobile** → **WathiqApp** in:
- `Info.plist` (iOS)
- `strings.xml` (Android)
- `app.json`

### 3. Types Updated
Added 15+ new fields to `Client` interface in `models.types.ts`.

---

## App Store Screenshots

````carousel
![Login Screen](file:///Users/hamzabuhlakq/.gemini/antigravity/brain/7b4afff5-6850-4e8b-b8cd-9cadf18a54d9/screenshot_login_1771361634972.png)
<!-- slide -->
![Dashboard](file:///Users/hamzabuhlakq/.gemini/antigravity/brain/7b4afff5-6850-4e8b-b8cd-9cadf18a54d9/screenshot_dashboard_1771361657350.png)
<!-- slide -->
![Cases List](file:///Users/hamzabuhlakq/.gemini/antigravity/brain/7b4afff5-6850-4e8b-b8cd-9cadf18a54d9/screenshot_cases_1771361690967.png)
<!-- slide -->
![Clients List](file:///Users/hamzabuhlakq/.gemini/antigravity/brain/7b4afff5-6850-4e8b-b8cd-9cadf18a54d9/screenshot_clients_1771361711857.png)
````

---

## iOS Build Configuration

| Setting | Value |
|---|---|
| Bundle ID | `com.bewathiq.mobile` |
| Version | `1.0` |
| Build | `1` |
| Display Name | `WathiqApp` |
| Privacy Manifest | ✅ Configured |
| ATS | ✅ Secure (no arbitrary loads) |

## Next Steps for App Store Submission

1. **Add App Icon** — Place your 1024×1024 icon in `ios/WathiqMobile/Images.xcassets/AppIcon.appiconset/`
2. **Build in Xcode** — Open `.xcworkspace`, select signing team, archive
3. **Upload to App Store Connect** — Use Xcode Organizer or Transporter
4. **Fill App Store Listing** — Description, keywords, screenshots, privacy policy URL
