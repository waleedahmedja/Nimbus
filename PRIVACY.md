# Privacy Policy

**Effective date:** April 2026
**Last updated:** April 2026

Nimbus is a weather app designed for personal use. This policy explains how information is — and is not — handled.

---

## Short Version

Nimbus collects your approximate location to show your local weather. That's it. Nothing is sold. Nothing is shared with advertisers. There are no Nimbus servers. Everything that isn't sent to Open-Meteo or Nominatim stays on your device.

---

## 1. What Nimbus collects

**Location**

Nimbus requests your device location to fetch a weather forecast for your position. Location is used only to generate a latitude and longitude for the API call. It is not stored beyond your current session in any identifiable form. It is never transmitted to Nimbus servers — because there are none.

**Nothing else**

Nimbus does not collect:
- Your name or any personal identifier
- Contact information
- Usage analytics or crash reports
- Advertising identifiers
- Biometric data
- Keystrokes or screen content
- Browsing history

---

## 2. How your data is used

Your location serves one purpose: fetching a weather forecast.

The request goes to Open-Meteo — a free, open-source weather API. Only your latitude and longitude are sent. No personal data is attached. Open-Meteo's privacy policy governs their handling of that data.

City name resolution uses Nominatim, OpenStreetMap's geocoding service. Again, only coordinates are sent. The resolved city name is stored locally on your device. It is never re-fetched once cached — Nominatim receives at most one request per saved location, ever.

**Nimbus has no backend.** There is no Nimbus server receiving, storing, or processing your data. The only outbound network requests are the two described above.

---

## 3. Daily sentences

The daily sentence feature uses a hand-written local library of 440 sentences stored inside the app. No AI API is called. No data leaves your device to generate a sentence. It is selected entirely offline.

---

## 4. Local storage

All app data is stored locally on your device using Android DataStore:
- Units preference (°C/°F, km/h/mph)
- Saved location names (up to 3)
- Cached city names

Uninstalling the app removes all of this permanently. There is no cloud backup.

---

## 5. Third-party services

Nimbus uses two external services:

| Service | Purpose | Data sent |
|---|---|---|
| Open-Meteo | Weather forecast | Latitude and longitude |
| Nominatim (OpenStreetMap) | City name lookup | Latitude and longitude |

No analytics SDKs. No advertising SDKs. No tracking frameworks. No cloud database.

---

## 6. Children's privacy

Nimbus does not knowingly collect data from anyone. Since no personal data is collected from any user, no special processing applies to users of any age.

---

## 7. Changes

This policy may be updated to reflect new features or legal requirements. The date at the top of this document will reflect any changes. Significant changes will be noted in the app's release notes.

---

## 8. Contact

Questions about this policy can be raised as a GitHub Issue:
[github.com/waleedahmedja/nimbus](https://github.com/waleedahmedja/nimbus)

---

*Nimbus is built on craft, clarity, and user trust. Your data is yours.*

— **waleedahmedja**
