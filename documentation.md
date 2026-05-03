# HTML Editor – Entwicklungsprotokoll

*Erstellt mit Claude (claude.ai) – April 2026*

---

## Ausgangslage

Ziel war ein einfacher WYSIWYG-Editor für HTML-Seiten, der **ohne Installation** funktioniert und auch von technisch weniger erfahrenen Nutzern bedient werden kann. Anlass war die Pflege der Website des **Südthüringer Literaturvereins**, deren HTML-Dateien bisher manuell in einem Texteditor bearbeitet wurden – mit Problemen bei Umlauten und fehlender visueller Kontrolle.

---

## Entwicklungsschritte

### Schritt 1 – Grundkonzept & erste Version (React-Artefakt)
- Erster Prototyp als **React-Komponente** direkt in Claude
- Vorschau per `<iframe>` mit Click-Tracking via `postMessage`
- Elemente per Klick auswählen (blauer Rahmen)
- Aktionen: **Kopieren, Duplizieren, Einfügen, Hoch/Runter, Löschen**
- Geteilte Ansicht: Vorschau links, HTML-Code rechts
- Tabs: Geteilt / Vorschau / Code
- Datei öffnen (UTF-8) und speichern (Download)

### Schritt 2 – Standalone HTML-Datei
- Umwandlung in eine **einzelne `.html`-Datei** ohne Abhängigkeiten
- Läuft per Doppelklick direkt im Browser (Firefox, Chrome, Edge)
- Kann per E-Mail versendet werden
- Tastaturkürzel: `Strg+S`, `Strg+C/V`, `Strg+D`, `Entf`

### Schritt 3 – Direkte Textbearbeitung (WYSIWYG)
- **Doppelklick** auf beliebigen Text → direktes Tippen im Element (grüner Rahmen)
- Button **✏️ Text bearbeiten** als Alternative
- Beenden per `Enter`, Klick außerhalb oder `Escape` (Abbrechen)
- Änderungen werden sofort in den HTML-Code übernommen
- Toolbar-Button zeigt animierten „wird bearbeitet"-Zustand

### Schritt 4 – Foto einfügen (eingebettet)
- Button **🖼 Foto einfügen** und **🖼 Foto ans Ende**
- Bilder wurden zunächst als **Base64 eingebettet** (keine externen Dateien nötig)

### Schritt 5 – Foto per Pfad verlinken
- Umstieg auf **externe Verlinkung** statt Base64-Einbettung
- Dialog mit Eingabe von Bildordner und Dateiname
- Schnellauswahl-Buttons für häufige Pfade (`bilder/`, `../bilder/` usw.)
- Automatische **URL-Kodierung** von Leerzeichen im Dateinamen (`%20`)
- Warnung bei problematischen Dateinamen

### Schritt 6 – Automatische Pfadberechnung
- Neuer Dialog mit **zwei Pfad-Feldern**: HTML-Datei und Bilddatei
- **Relativer Pfad wird automatisch berechnet** aus beiden absoluten Pfaden
- Unterstützt Windows-Pfade (`C:\Website\Verein\termine.html`)
- Beispiel: `C:\Website\Verein\termine.html` + `C:\Website\Bilder\foto.jpg` → `../Bilder/foto.jpg`
- HTML-Dateipfad wird gespeichert und beim nächsten Mal vorausgefüllt
- Datei-Auswahl-Button übernimmt Dateinamen automatisch

### Schritt 7 – Foto nachträglich bearbeiten
- Klick auf ein bestehendes Foto → Button **🖼 Foto bearbeiten** leuchtet orange auf
- Dialog zeigt aktuellen Bildpfad an
- **Foto tauschen**: neues Bild wählen, Pfad wird automatisch berechnet
- **Größe ändern**: Volle Breite / 75% / 50% / 33% / Klein / Mittel / Originalgröße
- **Ausrichtung**: Links / Zentriert / Rechts
- Aktuelle Einstellungen werden beim Öffnen des Dialogs vorausgefüllt

### Schritt 8 – Arbeitspfad & CSS-Inlining
- Neuer Button **📁 Arbeitspfad festlegen** (muss vor dem Öffnen einer Datei ausgeführt werden)
- Nutzer wählt den **Website-Ordner** über den Browser-Dateidialog (`showDirectoryPicker`)
- Alle Dateien des Ordners (inkl. Unterordner) werden in `folderFileMap` (Map) geladen
- Gefundene **CSS-Dateien** werden automatisch ausgelesen und als `<style>`-Tag inline in die Vorschau eingebettet
- Die gespeicherte Datei behält die **originalen `<link>`-Tags** – kein CSS wird in die Ausgabe geschrieben
- Ohne Arbeitspfad funktioniert der Editor wie bisher (keine CSS-Vorschau, keine Bildvorschau)

### Schritt 9 – Bilder aus Ordner automatisch anzeigen
- Alle Bild-Dateien aus `folderFileMap` (`.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`, `.svg`) werden beim Rendern erkannt
- Für jedes Bild wird eine **Blob-URL** erzeugt und im iframe als `src` eingesetzt
- Relativpfade aus dem HTML werden dabei aufgelöst (Pfad der aktuellen Datei als Basis)
- Blob-URLs sind temporär (nur für die Vorschau) – in der gespeicherten Datei stehen die **originalen relativen Pfade**
- Ermöglicht vollständige visuelle Vorschau ohne Webserver

### Schritt 10 – Foto-Dialog Neugestaltung (Dropdown aus Arbeitspfad)
- Bisheriger Dialog (manuelle Pfadeingabe) wurde ersetzt durch ein **Dropdown-Menü**
- Alle Bilder aus `folderFileMap` werden als Auswahl angeboten
- Unterordner werden als Gruppenüberschriften angezeigt
- **Bildvorschau** erscheint nach Auswahl direkt im Dialog
- Breite wählen: Volle Breite, Halbe Breite, Klein (150px), Mittel (300px), Größe übernehmen
- Option **„Größe aus gewähltem Element übernehmen"** erscheint nur, wenn das aktuell ausgewählte Element ein `<img>` enthält
- **Alt-Text** Eingabefeld
- Relativer Pfad wird automatisch aus `folderFileMap`-Schlüssel und aktuellem Dateipfad berechnet (via `relativePathTo`)

### Schritt 11 – Foto ersetzen (replace-Modus)
- Neuer Button **🖼 Foto ersetzen** – nur aktiv, wenn das ausgewählte Element ein `<img>` enthält
- Öffnet denselben Foto-Dialog im „replace"-Modus
- Statt ein neues Element einzufügen: bestehendes `<img>` wird **direkt im DOM ersetzt** (`replaceChild`)
- Kein doppeltes Bild, keine Verschiebung umliegender Elemente
- Alt-Text und Größe des alten Bildes werden als Startwerte vorausgefüllt

### Schritt 12 – Hyperlink-Dialog (intern/extern, relativePathTo)
- Neuer Button **🔗 Hyperlink** öffnet einen Linkdialog
- **Zwei Modi** per Radio-Button:
  - „Seite im Arbeitspfad" → Dropdown aller `.html`-Dateien aus `folderFileMap`; relativer Pfad wird automatisch berechnet
  - „Eigene URL" → Freitexteingabe; HTTPS wird automatisch ergänzt wenn fehlt
- Checkbox **„In neuem Tab öffnen"** (`target="_blank"`)
- Wenn das ausgewählte Element bereits ein `<a>` ist: bestehender `href` und `target` werden vorausgefüllt
- Wenn das Element kein `<a>` ist: das Element wird in ein `<a>`-Tag **eingebettet** (wrapped)
- Relativer Pfad-Algorithmus (`relativePathTo`) unterstützt Unterordner beliebiger Tiefe

### Schritt 13 – Info-Leiste (Arbeitspfad + Dateiname)
- Eine schmale **Info-Leiste** wurde unterhalb der Toolbar eingefügt
- Zeigt den aktuell gesetzten **Arbeitspfad** (Name des gewählten Ordners)
- Zeigt den Namen der aktuell **geöffneten Datei**
- Aktualisiert sich automatisch bei „Arbeitspfad festlegen" und „Öffnen"
- Grauer, dezenter Stil – stört den Workflow nicht

### Schritt 14 – Cortex AI Solutions Branding & Hilfe-Button
- Titelzeile enthält das **Cortex AI Solutions Logo** (SVG, inline) und den Schriftzug
- Kleiner **„?" Button** rechts in der Toolbar
- Klick öffnet die Datei `Bedienungsanleitung-HTML-Editor.html` in einem neuen Browser-Tab
- Branding ist dezent gehalten und stört die Bedienung nicht

---

## Begleitend entstandene Dokumente

| Datei | Inhalt |
|-------|--------|
| `HTML-Editor.html` | Der fertige Editor (Standalone, ~1.500 Zeilen) |
| `Bedienungsanleitung-HTML-Editor.html` | Schritt-für-Schritt-Anleitung für Einsteiger |
| `documentation.md` | Dieses Entwicklungsprotokoll |

---

## Technische Besonderheiten

- **Keine Installation, keine Abhängigkeiten** – läuft in jedem modernen Browser
- **iframe-Isolation** mit `postMessage` für sichere Kommunikation zwischen Editor und Vorschau
- **contentEditable** für direkte Textbearbeitung im iframe
- **DOMParser** für saubere HTML-Manipulation ohne Regex
- **File System Access API** (`showDirectoryPicker`) zum Laden ganzer Ordner inkl. Unterordner
- **folderFileMap** (Map): speichert alle Dateien des Website-Ordners als `File`-Objekte, Schlüssel = relativer Pfad
- **CSS-Inlining** für Vorschau: `<link rel="stylesheet">`-Tags werden erkannt, CSS inline eingebettet; gespeicherte Datei behält originale `<link>`-Tags
- **Blob-URLs** für Bildvorschau: relative `src`-Attribute werden im iframe durch temporäre `blob:`-URLs ersetzt
- **relativePathTo(from, to)**: berechnet relativen Pfad zwischen zwei absoluten Dateipfaden (unterstützt beliebige Ordnertiefen und Unterordner)
- **localStorage** zum Speichern des HTML-Dateipfads zwischen Sitzungen
- **UTF-8** beim Speichern (korrekte Darstellung von Umlauten und ß)
- Tastaturkürzel: `Strg+S` (Speichern), `Strg+C/V` (Kopieren/Einfügen), `Strg+D` (Duplizieren), `Entf` (Löschen)

---

*Entwickelt im Gespräch mit Claude Sonnet 4.6 auf claude.ai – April 2026*
