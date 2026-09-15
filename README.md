# 📱 Bambu Lab iOS Live Activity für Home Assistant

Dieser Blueprint erstellt vollautomatisch eine native **iOS Live Activity** (Sperrbildschirm & Dynamic Island) für deinen Bambu Lab 3D-Drucker in Home Assistant. Er zeigt den aktuellen Status, den Druckfortschritt und die Endzeit an, übersetzt englische Statusmeldungen ins Deutsche und alarmiert dich sofort bei Fehlern.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/SteeeveAT/ha-blueprints/main/bambu-live-activity.yaml)

## ✨ Features

- **Native iOS Integration:** Fortschrittsbalken und Restzeit direkt auf dem Sperrbildschirm und in der Dynamic Island.
- **Stumme Updates:** Der Fortschritt wird im Hintergrund aktualisiert (`silent: true`), ohne dass das iPhone bei jedem Prozentpunkt vibriert oder klingelt.
- **Deutsche Übersetzung:** Englische Statuswerte (z. B. `printing`, `paused_cutter_error`, `bed_level_phase_1`) werden automatisch in sauberes Deutsch übersetzt.
- **Intelligente Fehlererkennung:** Geht der Drucker in einen Fehlerzustand (Filament leer, Spaghetti-Erkennung etc.), färbt sich die Live-Aktivität rot und schickt dir _einmalig_ eine akustische Push-Warnung.
- **Deep-Linking (Klick-Aktion):** Beim Antippen der Live-Aktivität öffnet sich direkt ein definierbares Dashboard (z. B. deine 3D-Drucker Ansicht) oder der Kamera-Stream.
- **Automatischer Cleanup:** Die Benachrichtigung verschwindet automatisch 15 Minuten nach dem fertigen Druck oder sofort, wenn der Drucker offline/idle geht.

## 📋 Voraussetzungen

1. Die offizielle **Home Assistant Companion App** für iOS (Live-Aktivitäten müssen in den iOS-Einstellungen für die App erlaubt sein).
2. Ein eingebundener Bambu Lab Drucker (z. B. über die [ha-bambulab HACS Integration](https://github.com/greghesp/ha-bambulab)).

## 🚀 Installation & Einrichtung

1. Klicke oben auf den **"Import Blueprint"** Button oder importiere die Raw-URL manuell in Home Assistant.
2. Gehe zu **Einstellungen -> Automatisierungen -> Blaupausen** und klicke bei dieser Blaupause auf _Automatisierung erstellen_.
3. Fülle die folgenden Felder aus:

### Konfigurationsfelder

| Feld                        | Beschreibung                                             | Beispiel                                     |
| :-------------------------- | :------------------------------------------------------- | :------------------------------------------- |
| **Benachrichtigungsdienst** | Der exakte Name deines iPhone Notify-Dienstes.           | `notify.mobile_app_iphone_stephan`           |
| **Titel**                   | Überschrift der Benachrichtigung.                        | `Bambu Lab P2S`                              |
| **Status-Sensor**           | Entität für den Arbeitsschritt.                          | `sensor.p2s_..._aktueller_arbeitsschritt`    |
| **Fortschritts-Sensor**     | Entität für die %.                                       | `sensor.p2s_..._druckfortschritt`            |
| **Endzeit-Sensor**          | Entität für die geplante Endzeit.                        | `sensor.p2s_..._endzeit`                     |
| **Aktion beim Antippen**    | URL, die beim Klick auf dem iPhone geöffnet werden soll. | `/lovelace/14` oder `/?entity_id=camera.xyz` |
| **Farben**                  | Hex-Codes für Normalbetrieb und Fehler.                  | `#00AE42` (Bambu Grün) / `#F44336` (Rot)     |

## 💡 Fehlerbehebung (Troubleshooting)

- **Es kommt keine Benachrichtigung an:** Prüfe unter _Entwicklerwerkzeuge -> Aktionen_, ob der Dienstname unter "Benachrichtigungsdienst" (z. B. `notify.mobile_app_iphone_max`) exakt stimmt und funktioniert.
- **Die Automation bricht ab:** Dieser Blueprint verlässt sich darauf, dass die Sensoren die Original-Statuswerte ausgeben (z. B. `printing` und nicht `Druckt`). Prüfe unter _Entwicklerwerkzeuge -> Zustände_, ob die rohen Werte auf Englisch ankommen.

---

_Erstellt für die Home Assistant Community. Happy Printing! 🚀 (Hört gerne mal in den 3D-Druck Podcast **Extrusion & Error** rein!)_
