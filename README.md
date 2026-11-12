# 🏠 Home Assistant Blueprints

Blueprints für Home Assistant, vor allem iOS Live Activities (Sperrbildschirm & Dynamic Island) über die Companion App. Klicke auf einen Namen, um direkt zur Anleitung zu springen.

<a id="inhalt"></a>

## 📚 Inhaltsverzeichnis

| Blueprint | Wofür |
| :-- | :-- |
| [📱 Bambu Lab 3D-Drucker](#bambu) | Druckstatus, Fortschritt und Endzeit als Live Activity, optional mit Foto vom fertigen Druck |
| [🍽️ Geschirrspüler (Home Connect)](#geschirrspueler) | Programm, Fortschritt und Endzeit als Live Activity für Siemens, Bosch, Neff und Gaggenau |

---

<a id="bambu"></a>

## 📱 Bambu Lab iOS Live Activity für Home Assistant

Dieser Blueprint erstellt vollautomatisch eine native **iOS Live Activity** (Sperrbildschirm & Dynamic Island) für deinen Bambu Lab 3D-Drucker in Home Assistant. Er zeigt den aktuellen Status, den Druckfortschritt und die Endzeit an, übersetzt englische Statusmeldungen ins Deutsche und alarmiert dich sofort bei Fehlern.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/SteeeveAT/ha-blueprints/main/bambu-live-activity.yaml)

### ✨ Features

- **Native iOS Integration:** Fortschrittsbalken und Restzeit direkt auf dem Sperrbildschirm und in der Dynamic Island.
- **Stumme Updates:** Der Fortschritt wird im Hintergrund aktualisiert (`silent: true`), ohne dass das iPhone bei jedem Update vibriert oder klingelt.
- **Sparsame Updates:** Die Companion App erlaubt 500 Push-Nachrichten pro Tag und Gerät, und jedes Update der Live-Aktivität zählt als Push. Der Blueprint sendet den Fortschritt deshalb nur in einstellbaren Schritten. Im Leerlauf sendet er nichts, kurze Verbindungsabbrüche überbrückt er.
- **Deutsche Übersetzung:** Englische Statuswerte (z. B. `printing`, `paused_cutter_error`, `bed_level_phase_1`) werden automatisch in sauberes Deutsch übersetzt.
- **Intelligente Fehlererkennung:** Geht der Drucker in einen Fehlerzustand (Filament leer, Spaghetti-Erkennung etc.), färbt sich die Live-Aktivität rot und schickt dir _einmalig_ eine akustische Push-Warnung.
- **Deep-Linking (Klick-Aktion):** Beim Antippen der Live-Aktivität öffnet sich direkt ein definierbares Dashboard (z. B. deine 3D-Drucker Ansicht) oder der Kamera-Stream.
- **Foto bei Druckende (optional):** Ist eine Kamera gewählt, kommt bei Druckende eine zusätzliche Benachrichtigung mit einem Foto vom fertigen Druck. In der Live-Aktivität selbst lässt iOS keine Bilder zu.
- **Automatischer Cleanup:** Die Benachrichtigung verschwindet automatisch 15 Minuten nach dem fertigen Druck oder sofort, wenn der Drucker offline/idle geht.

### 📋 Voraussetzungen

1. Die offizielle **Home Assistant Companion App** für iOS (Live-Aktivitäten müssen in den iOS-Einstellungen für die App erlaubt sein).
2. Ein eingebundener Bambu Lab Drucker (z. B. über die [ha-bambulab HACS Integration](https://github.com/greghesp/ha-bambulab)).

### 🚀 Installation & Einrichtung

1. Klicke oben auf den **"Import Blueprint"** Button oder importiere die Raw-URL manuell in Home Assistant.
2. Gehe zu **Einstellungen -> Automatisierungen -> Blaupausen** und klicke bei dieser Blaupause auf _Automatisierung erstellen_.
3. Fülle die folgenden Felder aus:

#### Konfigurationsfelder

| Feld                        | Beschreibung                                             | Beispiel                                     |
| :-------------------------- | :------------------------------------------------------- | :------------------------------------------- |
| **Benachrichtigungsdienst** | Der exakte Name deines iPhone Notify-Dienstes.           | `notify.mobile_app_iphone_stephan`           |
| **Titel**                   | Überschrift der Benachrichtigung.                        | `Bambu Lab P2S`                              |
| **Status-Sensor**           | Entität für den Arbeitsschritt.                          | `sensor.p2s_..._aktueller_arbeitsschritt`    |
| **Fortschritts-Sensor**     | Entität für die %.                                       | `sensor.p2s_..._druckfortschritt`            |
| **Endzeit-Sensor**          | Entität für die geplante Endzeit.                        | `sensor.p2s_..._endzeit`                     |
| **Aktion beim Antippen**    | URL, die beim Klick auf dem iPhone geöffnet werden soll. | `/lovelace/14` oder `/?entity_id=camera.xyz` |
| **Bild bei Druckende**      | Kamera- oder Bild-Entität für das Foto vom fertigen Druck. Leer = kein Foto. | `camera.x1c_kamera` / `image.p1s_kamera` |
| **Farben**                  | Hex-Codes für Normalbetrieb und Fehler.                  | `#00AE42` (Bambu Grün) / `#F44336` (Rot)     |
| **Fortschritts-Schrittweite** | Update nur bei jedem Vielfachen dieses Werts.          | `5` % (Standard)                             |
| **Mindestabstand**          | Mindestzeit zwischen Routine-Updates. Start, Pause, Fehler und Ende kommen immer sofort. | `5` min (Standard) |
| **Endzeit-Schwelle**        | Ab dieser Abweichung löst die Endzeit ein eigenes Update aus. | `10` min (Standard)                     |

### 💡 Fehlerbehebung (Troubleshooting)

- **Es kommt keine Benachrichtigung an:** Prüfe unter _Entwicklerwerkzeuge -> Aktionen_, ob der Dienstname unter "Benachrichtigungsdienst" (z. B. `notify.mobile_app_iphone_max`) exakt stimmt und funktioniert.
- **Push-Limit erreicht:** Den aktuellen Zähler zeigt die iOS-App unter _Einstellungen -> Benachrichtigungen_. Das Limit wird um Mitternacht UTC zurückgesetzt. Mehrere Drucker auf einem iPhone teilen sich das Limit, erhöhe dann die Schrittweite oder den Mindestabstand.
- **Die Automation bricht ab:** Dieser Blueprint verlässt sich darauf, dass die Sensoren die Original-Statuswerte ausgeben (z. B. `printing` und nicht `Druckt`). Prüfe unter _Entwicklerwerkzeuge -> Zustände_, ob die rohen Werte auf Englisch ankommen.
- **Kein Foto bei Druckende:** Prüfe unter _Entwicklerwerkzeuge -> Zustände_, ob die gewählte Entität das Attribut `entity_picture` hat. Das Foto zählt als zusätzlicher Push.

### 🔄 Updates

- 2026-09-15: Initialisieren und parallele Live-Aktivitäten sind jetzt möglich
- 2026-09-16: Color-Selectoren, Übersetzung optimiert
- 2026-09-21: Status-Sensor ändern, da der vorherige bei Fehler diese nicht angezeigt hat
- 2026-11-06: Deutlich weniger Push-Nachrichten durch einstellbare Schrittweite, Mindestabstand und Endzeit-Schwelle
- 2026-11-11: Optionales Foto vom fertigen Druck als Benachrichtigung bei Druckende

[⬆️ Zurück zum Inhaltsverzeichnis](#inhalt)

---

<a id="geschirrspueler"></a>

## 🍽️ Geschirrspüler (Home Connect) iOS Live Activity

Zeigt Programm, Fortschritt und Endzeit eines Home-Connect-Geschirrspülers (Siemens, Bosch, Neff, Gaggenau) als iOS Live Activity an. Updates sind wie beim Bambu-Blueprint gedrosselt, damit das Push-Limit von 500 pro Tag nicht erreicht wird.

[![Open your Home Assistant instance and show the blueprint import dialog with a specific blueprint pre-filled.](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https://raw.githubusercontent.com/SteeeveAT/ha-blueprints/main/siemens-geschirrspueler-live-activity.yaml)

Voraussetzung ist die **Home Connect**-Integration von Home Assistant.

| Feld                        | Beschreibung                                             | Beispiel                                     |
| :-------------------------- | :------------------------------------------------------- | :------------------------------------------- |
| **Benachrichtigungsdienst** | Der exakte Name deines iPhone Notify-Dienstes.           | `notify.mobile_app_iphone_stephan`           |
| **Betriebszustand**         | Entität für den Betriebszustand.                         | `sensor.geschirrspuler_betriebszustand`      |
| **Fortschritts-Sensor**     | Entität für die %.                                       | `sensor.geschirrspuler_programm_fortschritt` |
| **Endzeit-Sensor**          | Entität für die Programmendzeit.                         | `sensor.geschirrspuler_programm_endzeit`     |
| **Aktives Programm**        | Optional: Programmname in der Live-Aktivität.            | `sensor.<geräte-id>_active_program`          |
| **Farben**                  | Läuft, pausiert, Aktion erforderlich/Fehler, fertig.     | `#26C6DA` / `#FF9800` / `#F44336` / `#4CAF50` |
| **Schrittweite, Mindestabstand, Endzeit-Schwelle** | Wie beim Bambu-Blueprint. | `5` % / `5` min / `10` min |

Start, Pause, „Aktion erforderlich“, Fehler und Ende werden immer sofort gesendet. Bei einem Fehler endet die Live-Aktivität und es kommt eine eigene Warnung. Nach „Fertig“ verschwindet die Live-Aktivität nach 15 Minuten.

[⬆️ Zurück zum Inhaltsverzeichnis](#inhalt)

---

_Erstellt für die Home Assistant Community. Happy Printing! 🚀 (Hört gerne mal in den 3D-Druck Podcast **Extrusion & Error** rein!)_
