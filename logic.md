# Logic & Workflow — Phone OSINT Terminal — Radar Tracker

This document explains the core logic, workflow, and sequence of operations for the **Phone OSINT Terminal — Radar Tracker** web app.

---

## 1. User Input & Trigger

- **Input:**  
  The user enters a phone number (preferably in international format) in the input box.

- **Action:**  
  User clicks the `SCAN` button.

---

## 2. UI Feedback — Radar Animation

- **Full Map Sweep:**  
  - Radar overlay (animated beam) is shown over the entire map to simulate an initial “sweep.”
  - The results panel logs the start of the scan.

---

## 3. API Request — Phone Lookup

- **API Used:**  
  [NumVerify](https://numverify.com/) (APILayer) via:
  ```
  http://apilayer.net/api/validate?access_key=API_KEY&number=PHONE&format=1
  ```
- **Purpose:**  
  - Validates the phone number.
  - Returns carrier, country, line type, and (sometimes) location/state.

- **Error Handling:**  
  - If the API request fails or the number is invalid, show an error in the log and stop the radar animation.

---

## 4. Logging Results

- **Logs written:**  
  - Carrier name
  - Country (and country code)
  - Location (state/city if available)
  - Line type
  - Any errors or missing data

- **Display:**  
  Each log entry appears with a typing animation in the results panel.

---

## 5. Geocoding — Location to Coordinates

- **Purpose:**  
  Convert the country or specific location (state/city) to latitude & longitude for map display.

- **API Used:**  
  [OpenStreetMap Nominatim](https://nominatim.openstreetmap.org/search?format=json&q=QUERY)

- **Fallbacks:**  
  If geocoding fails for a location, fallback to known country center coordinates (predefined for some countries).

---

## 6. Map & Radar Centering

- **Country Acquired:**  
  - Radar overlay animates to the center of the country.
  - Map zooms in to country level.

- **Location/State Acquired:**  
  - If a specific region or city is found, radar overlay animates to that spot.
  - Map zooms further in (city/state level).

- **Ping Effect:**  
  - A pulsing marker ("ping-dot") is placed at the best-known coordinates (location or country center).
  - Marker uses custom animated CSS for visual feedback.

---

## 7. Finalization

- **Scan Complete:**  
  - Radar overlay fades out, leaving the ping marker visible.
  - Log notes that the scan is complete.

---

## 8. Download Log

- **Function:**  
  - User can click the `DOWNLOAD` button to save the entire log as a plain text file.

---

## 9. Help & How-To

- **Feature:**  
  - `[+] How to Use` toggles a help section with step-by-step usage instructions.

---

## 10. Responsive & Usability Notes

- **Styling:**  
  - All interface elements are styled with a hacker/terminal vibe (green-on-black, monospace, animated borders).
  - Responsive layout adapts for mobile and desktop.

---

## Error Handling & Edge Cases

- Handles:
  - Invalid or missing phone number input.
  - API failures (network or quota limits).
  - No location returned by API.
  - Geocoding failures (uses country fallback).
  - Multiple lookups in a session (clears previous markers/logs).

---

## Summary Flow (Pseudocode)

```
User enters phone number
      ↓
[SCAN] clicked
      ↓
Show radar sweep animation
      ↓
Call NumVerify API
      ↓
  ├── If error: Log error, stop radar
  └── If valid:
        ↓
      Log carrier/country/location
        ↓
      Geocode country or location
        ↓
      ├── If found: Move radar to coordinates, zoom, ping marker
      └── If not: Fallback to country center, ping marker
        ↓
      Fade out radar, log scan complete
```

---

**For more details, see the code and inline comments in `index.html`.**
