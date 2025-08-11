# Mine-GeoJSON
A vendor-neutral GeoJSON standard for minefield reconnaissance data

## Mine-GeoJSON v0.3

---

### 1. Einleitung

**Hinweis:** Mine-GeoJSON ist ein verbindlicher Industriestandard. Um die Einheitlichkeit zu gewährleisten und die Interoperabilität zu sichern, ist es nicht gestattet, zusätzliche oder eigene Attribute außerhalb der definierten Struktur zu ergänzen. Erweiterungen erfolgen ausschließlich durch die verantwortliche Projektleitung und werden im Rahmen offizieller Versionierungen dokumentiert.

Dieses Handbuch beschreibt den Aufbau und die Anwendung des Datenstandards **Mine-GeoJSON**, eines interoperablen, offenen Formats zur standardisierten Bereitstellung von Detektionsdaten aus der abstandsfähigen Kampfmittelerkundung.

Der Standard wurde im Rahmen eines Innovationsvorhabens des Cyber Innovation Hub der Bundeswehr in Zusammenarbeit mit den Luftlandepionieren 270 entwickelt, um die Vielzahl an proprietären Sensorformaten zu vereinheitlichen und die Integration in zentrale Führungssysteme zu ermöglichen. Ziel ist es, mit Mine-GeoJSON ein gemeinsames Lagebild aus heterogenen Detektionsquellen zu erzeugen, Daten vergleichbar zu machen und eine vernetzte Gefahreneinschätzung in SitaWare Frontline zu ermöglichen.

---

### 2. Überblick über Mine-GeoJSON

Mine-GeoJSON basiert auf dem GeoJSON-Standard (RFC 7946) und erweitert ihn um kampfmittelspezifische Eigenschaften (Properties).

Der Standard ist:

- **offen**: frei zugänglich und dokumentiert
- **herstellerunabhängig**: für alle Sensorsysteme nutzbar
- **interoperabel**: durch Standardisierung auf JSON-Basis
- **zukunftssicher**: modular erweiterbar (z. B. neue Sensorarten, KI-basierte Attribute)

**Verwendungsszenarien**:

- UAV-basierte Minendetektion
- UGV-basierte Minendetektion
- kombinierte Sensorplattformen
- Import in GIS-Systeme und militärische Lageinformationssysteme

**Zielgruppe:** Softwareanbieter, Systemintegratoren, Sensorhersteller, öffentliche Stellen

---

### 3. Technische Grundlagen

- **Formatbasis:** GeoJSON (RFC 7946-konform)
- **Datenstruktur:** FeatureCollection → Feature → geometry + properties
- **Geometrietyp:** Punkt ("Point") für Einzelfunde, optional Fläche ("Polygon")
- **Koordinatensystem:** **MGRS / UTMREF (WGS84) -** [abweichend von GeoJSON-Vorgabe WGS84]
    - Hinweis: Obwohl GeoJSON offiziell WGS84 (Längengrad/Breitengrad) vorschreibt, verwendet Mine-GeoJSON aus praktischen und taktischen Gründen den NATO Standard MGRS. Systeme, die Mine-GeoJSON verarbeiten, müssen dies berücksichtigen.
- **Zeitformat:** ISO 8601 (z. B. 2025-03-20T10:45:00Z)
    - Alternativ kann zusätzlich die militärische Datum-Zeit-Gruppe (DTG) verwendet werden (z. B. 260900AMAR25 für 26. März 2025, 09:00 Uhr UTC+1).

---

### 4. Struktur des Datenformats

Mine-GeoJSON ist als standardisierte GeoJSON-Datei aufgebaut. Der Kern der Datei ist eine `FeatureCollection`, die einzelne Objekte (Funde) als `Feature` enthält. 

Jedes Feature besteht aus:

- einem `geometry`Block mit den Koordinaten (MGRS)
- einem `properties`Block mit den zugehörigen Attributen

Beispielstruktur (vereinfacht):

```json
{
"type": "FeatureCollection",
"features": [
{
"type": "Feature",
"geometry": {
"type": "Point",
"coordinates": [32UNB, 392000.212, 5821000.213]
},
"properties": {
"id": "UXO-20250320-001",
"type": "UXO",
"hazard_status": "suspected",
"last_update": "2025-03-20T10:45:00Z"
}
}
]
}
```

Weitere Details zu den Feldern finden sich im nächsten Kapitel.

---

### 5. Feldreferenz (Properties)

Die folgende Tabelle listet alle aktuell definierten Felder (Properties) auf, die im properties-Block jedes GeoJSON-Features verwendet werden können. 
Diese Definitionen sind verbindlich für alle Hersteller und Systeme, die Mine-GeoJSON implementieren.

|  | Feldname | Herkunft | Beschreibung | Datentyp |
| --- | --- | --- | --- | --- |
| 1 | id | Eigene Definition | Eindeutige Fund-ID
[z.B. “1,2,3,4,5,6”] | Number |
| 2 | sensor | Eigene Definition | Messgerät 
[z.B. “Magnetometer”] | String |
| 3 | unit | Eigene Definition | Einheit der Messung
[z.B. “nT”, “m”, “EA”] | String |
| 4 | detection_confidence | Eigene Definition | Wahrscheinlichkeit für Echtheit in %
[z.B. 0.87, 99, 1] | Number |
| 5 | status | Eigene Definition | Status
[”Bestätigt” / “Unbestätigt”] | Boolean |
| 6 | source | Eigene Definition | Trägerplattform 
[z.B. “UAV”, “UGV”] | String |
| 7 | depth_estimate | Eigene Definition | Geschätzte Tiefe
[z.B. -2.1, -0.3] | Number |
| 8 | last_udpate | Eigene Definition | Letzte Aktualisierung
[z. B. “260900Amar25”]   | String |
| 9 | image | Eigene Definition | Optionales Bildmaterial |  |
| 10 | geotiff | Eigene Definition | Georeferenziertes Bildmaterial (GeoTIFF) |  |
| 11 | hazard_id | IMSMA | Minenfeld- oder Bereichs ID | String |
| 12 | hazard_status | IMSMA | Status des Gebietes
[”suspected”, “confirmed”] | String |
| 13 | land_use | IMSMA | Aktuelle Landnutzung
[z.B. Feld, Straße] | String |
| 14 | accessibility | IMSMA | Zugangsbeschränkungen des sondierten Gebietes
[Freitext] | String |
| 15 | survey_type | IMSMA | Erkundungsmethode 
[”technical”, “non-technical”] | String |
| 16 | data_source | IMSMA | Quelle der Information 
[”KI”, “NGO”, “Soldat”} | String |
| 17 | verfication_type | IMSMA | Art der Überprüfung nach Sondierung
[Freitext: “visuell”, “manuell”, “etc.”) | String |
| 18 | dtg | NATO-STANAG 2221 | Datum-Zeit-Gruppe des Fundes / der Messung
[z. B. “260900Amar25”] | String |
| 19 | reporting_unit | NATO-STANAG 2221 | Meldende Einheit 
[z.B. “LLPiKp 270”] | String |
| 20 | linkup_location | NATO-STANAG 2221 | Ort für Verbindungsaufnahme mit meldender Einheit (UTM-Format)
[z.B. “347669, 5576127”] | String |
| 21 | linkup_method | NATO-STANAG 2221 | Kommunikationsart für Verbindungsaufnahme mit meldender Einheit
[z.B. “Funkfrequenz” , “Sichtzeichen”] | String |
| 22 | type | NATO-STANAG 2221 | Art des Kampfmittels
[z.B. Geschoss, 155mm / Mine, Panzerabwehr/Schützenabwehr / Granate, Hand] | String |
| 23 | subtype | NATO-STANAG 2221 | Munitionssorte des Kampfmittels [z.B. HE / HE-FRAG / AP / HEAT / APHE / …] | String |
| 24 | amount | NATO-STANAG 2221 | Anzahl der Kampfmittel 
[z.B. 2 EA ("each"] | String |
| 25 | description | NATO-STANAG 2221 | Freitextbeschreibung des Kampfmittels 
[z.B. zylindrisches Geschoss, Kaliber ca. 155mm, mit kegelförmiger Spitze] | String |
| 26 | location | NATO-STANAG 2221 | Fundort des Kampfmittels - MGRS
[z.B.”32UNB, 347669, 5576127”] | String |
| 27 | tactical_situation | NATO-STANAG 2221 | Einfluss des gefundenen Kampfmittels auf die eigene Auftragserfüllung 
[”None” , “Low”, “Medium”, “High”] | String |
| 28 | collateral_dmg | NATO-STANAG 2221 | Bereits eingetretene oder mögliche Begleitschäden 
[z.B. “Funkturm als kritische Infrastruktur direkt nebenan”] | String |
| 29 | protective_actions | NATO-STANAG 2221 | eingeleitete Schutzmaßnahmen [z.B. “umliegende Häuser evakuiert”] | String |
| 30 | recommended_priority | NATO-STANAG 2221 | empfohlene Priorität gem. Bewertung der meldenden Einheit [”immediate” , “urgent”,  “routine”  , “no threat”]  | String |
| 31 | rank_name | Eigene Definition | Messung durchgeführt durch
[z.B. “HF Müller”] | String |

---

### 6. Erweiterbarkeit & Zukunftssicherheit

Mine-GeoJSON wurde so konzipiert, dass der Standard mit zukünftigen Anforderungen wachsen kann. Die Struktur des Formats erlaubt es, zusätzliche Felder in den `properties`-Block zu integrieren – jedoch **ausschließlich** durch die verantwortlichen Herausgeber des Standards.

### Prinzipien der Erweiterung:

- **Zentral gepflegt**: Erweiterungen erfolgen ausschließlich durch die zentrale Projektleitung und werden über Versionierungen bekannt gemacht.
- **Einheitlichkeit gewährleisten**: Um die Interoperabilität und Vergleichbarkeit der Daten sicherzustellen, ist die Erweiterung durch Dritte nicht vorgesehen.
- **Dokumentationspflicht**: Alle Änderungen werden in der offiziellen Dokumentation veröffentlicht.

### Warum keine offenen Erweiterungen?

Ein offenes Hinzufügen von Feldern durch die Industrie würde den einheitlichen Standard aufweichen und könnte die automatische Verarbeitung sowie die Integration in Führungssysteme behindern. Mine-GeoJSON dient als **Single Point of Truth** – ein zentral gepflegter, verlässlicher Standard.

### Versionierung:

Jede offizielle Änderung oder Erweiterung des Standards wird mit einer neuen Versionsnummer gekennzeichnet (z. B. v0.1 → v0.2). Die aktuelle Version wird in der technischen Dokumentation und im JSON-Schema eindeutig referenziert.

---

### 7. Validierung

Um eine einheitliche Datenqualität und Kompatibilität sicherzustellen, wird ein validierbares JSON-Schema für Mine-GeoJSON bereitgestellt. Dieses Schema definiert alle Pflichtfelder, Datentypen, erlaubten Wertebereiche sowie optionale Felder.

### Ziel der Validierung

- Sicherstellung der Datenqualität und Formatkonformität
- Minimierung manueller Prüfprozesse
- Automatisierte Integration in Lageinformationssysteme

### Validierungsmöglichkeiten

- Online-Validator (z. B. https://jsonschemavalidator.net)
- Lokales Prüfskript (Python, Node.js etc.) mit bereitgestelltem Schema

### Gültigkeitskriterien

- Die GeoJSON-Struktur muss formal gültig sein

Das Schema wird gemeinsam mit dem Handbuch und der Beispieldatei bereitgestellt.

---

### 8. Bild- & Geodatenintegration

Mine-GeoJSON unterstützt die Einbindung visueller Informationen zur Ergänzung der strukturierten Detektionsdaten. Zwei spezielle Felder stehen dafür zur Verfügung:

### `image` – Verlinktes Objektbild

- **Typ:** URL oder relativer Pfad
- **Zweck:** Darstellung eines Fotos oder Screenshots des detektierten Objekts
- **Formatempfehlung:** JPG, PNG (komprimiert, max. 2 MB)
- **Beispiel:** `"image": "images/UXO-20250320-001.jpg"`

### `geotiff` – Georeferenziertes Flächenbild

- **Typ:** URL oder Dateipfad
- **Zweck:** Bereitstellung großflächiger Daten z. B. aus Heatmaps, Rasterscans, Kartenauswertungen
- **Format:** GeoTIFF (mit UTM-Koordinatenbezug)
- **Beispiel:** `"geotiff": "data/sector3_scan.tif"`

### Anforderungen & Empfehlungen

- GeoTIFFs müssen georeferenziert vorliegen (UTM)
- Transparente Layer (Alpha-Kanal) empfohlen für bessere Überlagerung
- Dateigröße: <50 MB zur Sicherstellung der Performance in Viewern
- Einheitliche Dateibenennung analog zu `hazard_id` oder `id`

### Integration in Viewer

- **Overlay** auf Karte mit Zoomfähigkeit
- Verknüpfung mit Funden über ID oder Lagebezug
- Möglichkeit zur Aktivierung/Deaktivierung einzelner Layer

### Nutzen

- Visuelle Nachvollziehbarkeit der Funde
- Verbesserung der operativen Entscheidungsgrundlagen
- Unterstützung bei Übergabe, Dokumentation und Analyse

---

### 9. Anwendungsbeispiele

Im Folgenden werden typische Anwendungsfälle dargestellt, in denen Mine-GeoJSON genutzt wird. Die Beispiele zeigen sowohl einfache Punktdaten als auch erweiterte Szenarien mit Zusatzinformationen.

### Beispiel 1: Einzelfund (UXO)

```json
{
"type": "Feature",
"geometry": {
"type": "Point",
"coordinates": [32UNB, 392000, 5821000]
},
"properties": {
"id": "UXO-20250320-001",
"type": "UXO",
"hazard_status": "suspected",
"recorded_at": "2025-03-20T10:45:00Z",
"sensor": "MAG-01",
"detection_confidence": 0.89,
"reporting_unit": "LLPiKp 270"
}
}
```

### **Beispiel 2: Flächenerkundung mit GeoTIFF**

```json
{
"type": "Feature",
"geometry": {
"type": "Polygon",
"coordinates": [[[32UNB, 392000, 5821000], [32UNB, 392050, 5821000], [32UNB, 392050, 5821050], [32UNB, 392000, 5821050], [32UNB, 392000, 5821000]]]
},
"properties": {
"hazard_id": "AREA-03",
"hazard_status": "confirmed",
"geotiff": "data/area03_heatmap.tif"
}
}
```

### **Beispiel 3: Mehrfachfund – Typ "TM-62M" (Reihenfund mit 2 m Abstand)**

```json
{
"type": "FeatureCollection",
"features": [
{
"type": "Feature",
"geometry": { "type": "Point", "coordinates": [32UNB, 392000, 5821000] },
"properties": {
"amount": "1 EA",
"image": "images/TM62M-001.jpg",
}
},
{
"type": "Feature",
"geometry": { "type": "Point", "coordinates": [32UNB, 392002, 5821000] },
"properties": {
"amount": "1 EA",
"image": "images/TM62M-002.jpg",
}
},
{
"type": "Feature",
"geometry": { "type": "Point", "coordinates": [32UNB, 392004, 5821000] },
"properties": {
"amount": "1 EA",
"image": "images/TM62M-003.jpg",
}
},
{
"type": "Feature",
"geometry": { "type": "Point", "coordinates": [32UNB, 392006, 5821000] },
"properties": {
"amount": "1 EA",
"image": "images/TM62M-004.jpg",
}
},
{
"type": "Feature",
"geometry": { "type": "Point", "coordinates": [32UNB, 392008, 5821000] },
"properties": {

"amount": "1 EA",
"image": "images/TM62M-005.jpg",
}
}
]
}
```

---

### 10. Integration in bestehende Systeme

Mine-GeoJSON ist so gestaltet, dass es sich nahtlos in bestehende Informationssysteme integrieren lässt. Dies umfasst sowohl militärische Führungssysteme als auch zivile GIS-Anwendungen.

### Kompatible Plattformen

- QGIS, ArcGIS, Kepler.gl, u.v.m.
- Lageinformationssysteme mit JSON/GeoJSON-Schnittstelle

### Empfehlungen zur Umsetzung

- Parser/Importmodul zur automatischen Mine-GeoJSON-Validierung
- Integration über REST-API, Datenbank-Import oder Dateiupload
- Verwendung standardisierter Layer- und Objektbeschreibungen

---

### 11. Konventionen & Best Practices

Zur Sicherstellung der Datenqualität empfiehlt Mine-GeoJSON folgende Konventionen:

### Dateibenennung

- `minegeojson_<gebiet>_<datum>.geojson`
- GeoTIFF: `minegeo_<hazard_id>.tif`

### Zeitangaben

- ISO 8601, UTC-Zeit
    - Beispiel: `2025-03-20T10:45:00Z`
- Datum-Zeit-Gruppe (DTG)
    - Beispiel: `260900AMAR25`

### Strukturhinweise

- Felder mit `null`Werten können entfallen

---

### 12. Lizenzierung & Nutzungsbedingungen

Mine-GeoJSON ist ein offener, nicht-proprietärer Datenstandard. Er darf frei genutzt, erweitert und in Softwaresysteme integriert werden, sofern folgende Bedingungen eingehalten werden:

- Quellenvermerk auf Mine-GeoJSON erforderlich
- Keine kommerzielle Weiterverwertung ohne Zustimmung
- Änderungen am Standard dürfen nicht als offizielle Version weitergegeben werden

---

## 13. Kontakt & Weiterentwicklung

Der Datenstandard wurde initiiert durch den Cyber Innovation Hub der Bundeswehr und maßgeblich entwickelt unter der Projektleitung von Alexander Petri, Ulf Steden, Erik Krämer und der Luftlandepionierkompanie 270 ****** im Rahmen des Innovationsvorhaben #172 - Minesweeper.

### Kontaktstelle

---

░█████╗░██╗██╗░░██╗██████╗░░██╗░░░░░░░██╗
██╔══██╗██║██║░░██║██╔══██╗░██║░░██╗░░██║
██║░░╚═╝██║███████║██████╦╝░╚██╗████╗██╔╝
██║░░██╗██║██╔══██║██╔══██╗░░████╔═████║░
╚█████╔╝██║██║░░██║██████╦╝░░╚██╔╝░╚██╔╝░
░╚════╝░╚═╝╚═╝░░╚═╝╚═════╝░░░░╚═╝░░░╚═╝░░

**Cyber Innovation Hub der Bundeswehr**

Franklinstr. 10

10587 Berlin

https://www.cyberinnovationhub.de/

---

### Versionsführung

- Aktuelle Version: **v0.3** (Stand: 01.08.2025)
