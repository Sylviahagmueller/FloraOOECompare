# 🌿 FloraOOE Compare

**FloraOOE Compare** ist ein Cloud-basiertes Vergleichstool für Artenlisten (CSV-Dateien) aus dem ZOBODAT-System. Es identifiziert Arten, die im aktuellen Kartierungs-Quadranten **fehlen**, aber in benachbarten Vergleichsquadranten **vorkommen**.

---

## Funktionen

### `compare_csvs` (CSV-Download)
- Vergleicht eine Original-Artenliste mit mehreren Vergleichslisten
- Liefert das Ergebnis als `text/csv` zum direkten Download
- Optionaler `strict`-Modus:
  - **false (Standard):** Alle Arten, die in einer beliebigen Vergleichsdatei vorkommen, aber nicht im Original
  - **true:** Nur Arten, die **in allen Vergleichsdateien vorhanden** sind, aber nicht im Original

### `preview_csv` (HTML-Vorschau)
- Zeigt die fehlenden Arten als formatiertes HTML im Browser
- Nutzbar im neuen Tab über ein Webformular
- Enthält einen eingebauten Button zum CSV-Download

### `get_quadrant` (Koordinaten → PQ & Quadrant)
- Wandelt eine geografische Position (`lat`, `lon`) in:
  - **PQ-Wert** (Kartierungseinheit 100er-Code)
  - **Quadrant** (1–4) auf Basis von Nord/Süd und West/Ost
- Unterstützt CORS (`OPTIONS`-Handling) für Web-Frontend

---

## Vergleichslogik

Die Vergleiche basieren auf einer zusammengesetzten Spalte `Gattung_Art`:

``Gattung_Art = Gattung.strip() + "_" + Art.strip()``

**Ergebnis:**  
Eine sortierte Liste mit folgenden Spalten:
- `Anr`
- `Familie`
- `Gattung`
- `Art`
- `Uart`

Sortiert nach: `Familie`, `Gattung`, `Art`

---

## Eingabeparameter

Die Funktionen erwarten einen `POST`-Request mit `multipart/form-data`.

### Pflichtfelder:
- `original_csv` → CSV-Datei für den Ziel-Quadranten
- `compare_csvs` → Eine oder mehrere Vergleichsdateien (CSV)
- `pq` → Kennung des PQ-Werts (z. B. `7951`)

### Optional:
- `strict` → `"true"` oder `"false"` (Standard: `"false"`)

---

## API-Endpoints 

| Funktion         | Methode | Endpoint                           | Beschreibung                                     |
|------------------|---------|------------------------------------|--------------------------------------------------|
| Vorschau         | POST    | `/preview_csv`                    | Gibt eine HTML-Vorschau der fehlenden Arten      |
| Download         | POST    | `/compare_csvs`                   | Gibt das Ergebnis als CSV-Datei zurück           |
| Quadrant-Suche   | GET     | [`https://flora-calc-quadrant-p-285194166533.europe-west3.run.app?lat=...&lon=...`](https://flora-calc-quadrant-p-285194166533.europe-west3.run.app)   | Wandelt Koordinaten in PQ + Quadrant (JSON)      |


---

## Web-Frontend: FloraOOE Compare UI

Ein responsives HTML-Frontend zur Interaktion mit den Cloud Functions. Entwickelt ohne Frameworks – leicht zu hosten und zu pflegen.

### Features

1. **Karte (Leaflet.js)**  
   - Klick auf Karte → Koordinaten → Berechnung von PQ & Quadrant

2. **Manuelle Koordinateneingabe**  
   - Felder für `lat` und `lon`, automatische API-Anbindung

3. **"Quadrant berechnen"-Button**  
   - Ruft `get_quadrant`-API auf

4. **Dateiuploads**  
   - `original_csv`: Ziel-Quadrant
   - `compare_csvs`: Vergleichs-Quadranten (mehrere möglich)
   - Hellgrüner Upload-Button für bessere UX

5. **Strict-Modus (optional)**  
   - Nur Arten, die in **allen** Vergleichsdateien vorkommen

6. **Auswertung**
   - Button 1: CSV direkt herunterladen (`compare_csvs`)
   - Button 2: Vorschau im neuen Tab (`preview_csv`)

7. **ZOBODAT-Link-Generator**  
   - Öffnet passendes Suchergebnis auf [zobodat.at](https://www.zobodat.at)

###  Mobile optimiert
- Zoomfähig
- Responsives Layout
- Touch-optimierte Felder und Buttons

---

##  Deployment

### Google Cloud Functions (v2 empfohlen)
- Laufzeit: **Python 3.10+**
- Einstiegspunkt pro Funktion:
  - `compare_csvs`
  - `preview_csv`
  - `get_quadrant`

### Anforderungen (`requirements.txt`)
```txt
pandas
Flask
functions-framework

