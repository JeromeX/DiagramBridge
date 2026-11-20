<div align="center">

# DiagramBridge 0.3.20

**Draw.io → Native Microsoft Visio (VSDX) · PDF Export**

A local Windows desktop converter for turning `.drawio` and draw.io XML diagrams into native Visio documents and PDF files.

[Deutsch](#deutsch) · [English](#english) · [Screenshots](#screenshots)

![Version](https://img.shields.io/badge/version-0.3.20-2f81f7)
![Platform](https://img.shields.io/badge/platform-Windows%20x64-0078D4)
![Languages](https://img.shields.io/badge/interface-DE%20%7C%20EN-286c9c)

</div>

---

## Deutsch

### Überblick

**DiagramBridge** konvertiert draw.io-/diagrams.net-Diagramme lokal auf einem Windows-Rechner in Microsoft-Visio-Dateien (`.vsdx`) und/oder PDF-Dateien. Der Visio-Export verwendet native Visio-Objekte. Es wird keine gerasterte Abbildung des Diagramms als Ersatzebene in die VSDX-Datei eingefügt.

Die Anwendung ist als Windows-x64-ZIP-Release vorgesehen. Das Programm wird ohne separaten Setup- oder MSI-Installer bereitgestellt. Die Konvertierungs-Engine wird beim ersten Start lokal eingerichtet und anschließend aus dem lokalen Anwendungscache verwendet.

**Download:** Das Programmarchiv wird als ZIP-Datei im Bereich [GitHub Releases](https://github.com/JeromeX/DiagramBridge/releases) bereitgestellt. Dieses Repository dokumentiert die veröffentlichte Anwendung; eine Entwickler- oder Build-Anleitung ist nicht Bestandteil dieser Dokumentation.

### Funktionen

| Bereich | Funktion |
|---|---|
| Quelldateien | Unterstützt `.drawio` und draw.io-XML (`.xml`). |
| Mehrfachverarbeitung | Mehrere Dateien in einer Konvertierungswarteschlange; Dateien können über den Dateiauswahldialog oder per Drag-and-drop hinzugefügt werden. |
| Duplikate | Bereits eingetragene Quelldateien werden nicht erneut in die Warteschlange aufgenommen. |
| Ausgabenamen | Der vorgeschlagene Basisname kann pro Warteschlangeneintrag bearbeitet werden. Die ausgewählte Dateiendung wird automatisch ergänzt. |
| Zielordner | Gemeinsamen Ausgabeordner auswählen oder das Zielordnerfeld leeren, um neben der jeweiligen Quelldatei zu speichern. |
| Visio | Erstellt `.vsdx` mit nativen Visio-Objekten und übernimmt die Namen der Quelldiagrammseiten. |
| PDF | Erstellt ein PDF mit einer Ausgabeseite je Diagrammseite. Der PDF-Export kann unabhängig vom VSDX-Export ausgeführt werden. |
| Kombinierte Ausgabe | Erstellt VSDX und PDF in einem Durchlauf. Ein PDF-Fehler verhindert nicht automatisch die Fertigstellung des VSDX-Exports. |
| Überschreibschutz | Vorhandene Ausgabedateien werden standardmäßig nicht stillschweigend ersetzt. Das Überschreiben kann ausdrücklich aktiviert werden. |
| Status und Protokoll | Warteschlangenstatus, Detailmeldungen und ein kopierbares technisches Protokoll; Protokolleinträge können gelöscht werden. |
| Visuelles Handbuch | Integrierte Schritt-für-Schritt-Ansicht in Deutsch und Englisch. |

### Ausgabeformate

| Auswahl | Ergebnis | Verhalten |
|---|---|---|
| **Nur Visio** | `.vsdx` | Erstellt ausschließlich eine native Visio-Datei. |
| **Nur PDF** | `.pdf` | Erstellt ausschließlich eine PDF-Datei, unabhängig vom VSDX-Export. |
| **Visio + PDF** | `.vsdx` und `.pdf` | Versucht beide Ausgaben. Ein PDF-Fehler wird als Warnung protokolliert; eine gültige VSDX-Ausgabe bleibt erhalten. |

Der PDF-Export erhält die Abmessungen der Quellseiten, solange der Diagramminhalt innerhalb der jeweiligen Seitengrenzen liegt. Reicht der Inhalt über eine Seitengrenze hinaus, kann die betroffene PDF-Seite erweitert werden, damit der Inhalt nicht abgeschnitten wird. Diese PDF-Anpassung verändert weder die Quelldatei noch die nativen VSDX-Seitenabmessungen.

### Sprache und Benutzeroberfläche

DiagramBridge enthält eine vollständige deutsche und englische Oberflächenterminologie einschließlich Statusmeldungen und visuellem Handbuch.

- Beim ersten Start wird die Sprache anhand der Windows-/Prozess-Anzeigesprache initial gewählt: Deutsch bei Sprachkennung `de`, andernfalls Englisch.
- Der **DE/EN**-Schalter ändert die Sprache unmittelbar.
- Die manuell gewählte Sprache wird gespeichert und bei späteren Starts wiederhergestellt.
- Auch der zuletzt eingestellte Ausgabeordner wird in den Anwendungseinstellungen gespeichert.

Die Einstellungen liegen unter `%LOCALAPPDATA%\DiagramBridge\settings.json`.

### Screenshots

Die folgenden Ansichten zeigen die tatsächliche Programmoberfläche aus Version 0.3.20.

| Deutsches Hauptfenster | Englisches Hauptfenster |
|---|---|
| ![DiagramBridge – Hauptfenster Deutsch](https://github.com/JeromeX/DiagramBridge/raw/main/docs/screenshots/diagrambridge-main-de.png) | ![DiagramBridge – Main window English](https://github.com/JeromeX/DiagramBridge/raw/main/docs/screenshots/diagrambridge-main-en.png) |

| Visuelles Handbuch – Deutsch | Visual guide – English |
|---|---|
| ![Visuelles Handbuch in Deutsch](https://github.com/JeromeX/DiagramBridge/raw/main/docs/screenshots/diagrambridge-guide-de.png) | ![Visual guide in English](https://github.com/JeromeX/DiagramBridge/raw/main/docs/screenshots/diagrambridge-guide-en.png) |

### Architektur

DiagramBridge trennt die Windows-Oberfläche, die Konvertierungsorchestrierung und die lokal ausgeführte Export-Engine.

```mermaid
flowchart TD
    UI["WPF desktop UI<br/>MainWindow / ManualWindow"] --> JOBS["ConversionJob<br/>Queue and status"]
    JOBS --> CONV["ConverterService<br/>Konvertierungsablauf"]
    CONV --> BOOT["EngineBootstrapper"]
    BOOT --> CACHE["Lokaler Anwendungscache<br/>%LOCALAPPDATA%\\DiagramBridge"]
    CACHE --> NODE["Node.js Runtime"]
    CACHE --> DRAWIO["diagrams.net Web-App"]
    CACHE --> PW["Playwright"]
    NODE --> BROWSER["Installiertes Edge / Chrome<br/>Headless Chromium"]
    PW --> BROWSER
    DRAWIO --> SESSION["Local editor session<br/>127.0.0.1"]
    BROWSER --> SESSION
    SESSION --> VSDX["Nativer VSDX-Export"]
    SESSION --> PDF["SVG-Seiten → PDF"]
    VSDX --> NATIVE["VsdxNativeProcessor<br/>Restore source page names"]
    NATIVE --> VALID["VsdxValidator<br/>VSDX package validation"]
    VALID --> OUT1["Output: .vsdx"]
    PDF --> OUT2["Ausgabe: .pdf"]
```

#### Komponenten

| Komponente | Verantwortung |
|---|---|
| `MainWindow` | WPF-Hauptoberfläche, Dateiauswahl, Drag-and-drop, Warteschlange, Ausgabeoptionen, Status und Protokoll. |
| `ManualWindow` | Integriertes visuelles Handbuch mit lokalisierten Texten. |
| `Localization` | Übersetzung der UI- und Statusmeldungen zwischen Deutsch und Englisch. |
| `ConversionJob` | Datenmodell eines Warteschlangeneintrags einschließlich Status, Details und Ausgabename. |
| `ConverterService` | Validiert Eingaben, erstellt eine temporäre Arbeitskopie, startet die Engine, koordiniert die Ausgabe und räumt temporäre Arbeitsdaten auf. |
| `EngineBootstrapper` | Prüft die lokale Engine und richtet fehlende, festgelegte Laufzeitkomponenten ein. |
| `export_vsdx.js` | Öffnet die lokale diagrams.net-Anwendung über Playwright und steuert den nativen VSDX- beziehungsweise PDF-Export. |
| `VsdxNativeProcessor` | Stellt die ursprünglichen Seitenbezeichnungen wieder her und prüft die Zuordnung der exportierten Seiten. |
| `VsdxValidator` | Prüft die Struktur des erzeugten VSDX-Open-XML-Pakets sowie wichtige Beziehungen, Seiten und Visio-Objekte. |

### Technischer Ablauf

1. Die Oberfläche nimmt `.drawio`- oder `.xml`-Dateien in die Warteschlange auf.
2. Der `ConverterService` erstellt für den jeweiligen Auftrag eine temporäre Kopie der Quelldatei. Die Originaldatei wird nicht direkt bearbeitet.
3. Der `EngineBootstrapper` prüft die lokale Installation. Fehlende Komponenten werden bei Bedarf heruntergeladen beziehungsweise installiert.
4. Node.js startet die Exportlogik. Playwright öffnet Microsoft Edge oder Google Chrome im Headless-Modus und lädt die lokal gespeicherte diagrams.net-Webanwendung über einen Loopback-Endpunkt (`127.0.0.1`).
5. Für VSDX wird der native diagrams.net-Visio-Exporter verwendet. Für PDF werden die Diagrammseiten als SVG aufbereitet und über den Browser als PDF ausgegeben.
6. Die VSDX-Nachbearbeitung stellt die Quellseitennamen wieder her. Danach wird das VSDX-Paket strukturell validiert.
7. Erfolgreiche Ausgaben werden in den gewählten Zielordner verschoben. Temporäre Arbeitsdateien werden anschließend entfernt.

### Laufzeitkomponenten

Die folgenden Versionen sind im Programmstand 0.3.20 festgelegt:

| Komponente | Version / Quelle | Verwendung |
|---|---|---|
| .NET | `net10.0-windows` | Windows-WPF-Anwendung. |
| Node.js | `22.23.5` · Windows x64 | Lokale Ausführung der JavaScript-Exportlogik. |
| diagrams.net | `26.0.16` · Commit `987146fb55a547a975961f57dddc781abf7648ba` | Lokal gespeicherte Diagramm-Editor- und Exportfunktionen. |
| Playwright | `1.62.0` | Browsersteuerung für den lokalen Export. |
| Browser | Microsoft Edge oder Google Chrome | Headless-Ausführung der lokalen diagrams.net-Sitzung. |

Die Engine-Komponenten werden nicht vollständig in das Programmarchiv eingebettet. Sie werden beim ersten Einrichten in `%LOCALAPPDATA%\DiagramBridge` abgelegt. Dafür ist eine Internetverbindung erforderlich. Nach erfolgreicher Einrichtung können die lokal vorhandenen Komponenten für weitere Konvertierungen verwendet werden.

### Datenschutz und lokale Verarbeitung

- Die Diagrammkonvertierung läuft auf dem lokalen Windows-Rechner.
- Die Quelldatei wird für die Verarbeitung in einen temporären Arbeitsordner kopiert; das Original wird nicht verändert.
- Die lokale Editor-Sitzung wird über `127.0.0.1` bereitgestellt.
- Die Browser-Sitzung ist auf lokale Ressourcen begrenzt; externe Browser-Anfragen werden während der Konvertierung blockiert.
- Eine Internetverbindung wird beim erstmaligen Einrichten der Engine zum Abruf der festgelegten Komponenten benötigt.
- DiagramBridge ist kein Online-Konvertierungsdienst. Die Diagramminhalte werden nicht an einen externen Konvertierungsserver übertragen.

### Systemvoraussetzungen

- Windows x64.
- Microsoft Edge oder Google Chrome muss lokal installiert sein.
- Internetverbindung für die erstmalige Einrichtung der lokalen Engine.
- Ausreichend freier Speicherplatz für Engine-Cache, temporäre Arbeitsdateien und Ausgabe.

### Grenzen und Hinweise zur Ausgabequalität

- Die native VSDX-Exportstrecke basiert auf einem adaptierten Upstream-Exportansatz, der dort als Beta beschrieben wird. Eine strukturell gültige VSDX-Datei ist kein Beweis für eine pixelgenaue Darstellung in jeder Visio-Version.
- Die VSDX-Prüfung kontrolliert die Paketstruktur und ausgewählte Inhalte. Sie ersetzt keinen vollständigen visuellen Vergleich in Microsoft Visio.
- Die VSDX-Verarbeitung akzeptiert derzeit Quelldateien mit 1 bis 200 Diagrammseiten.
- Extern verknüpfte Bilder sollten vor dem Export direkt in die draw.io-Datei eingebettet werden. Nicht lokal verfügbare oder von außen referenzierte Bildressourcen können insbesondere den PDF-Export verhindern.
- Die Darstellung einzelner Formen, Schriftarten und Verbindungen kann je nach Diagramminhalt und Visio-Version abweichen. Für wichtige Dokumente sollte das erzeugte Ergebnis in der vorgesehenen Zielanwendung geprüft werden.

### Release und Version

- **Aktuelle dokumentierte Programmversion:** `0.3.20`
- **Verteilungsform:** Windows-x64-ZIP mit dem Programm.
- **Kein separater Installer:** Die Veröffentlichung ist auf das ZIP-Programmarchiv ausgerichtet.
- **Version 0.3.20:** Startfenstergröße auf 1300 × 840 angepasst; die leere Warteschlangenfläche verwendet dieselbe dunkle Oberfläche wie das Warteschlangenpanel.

### Drittanbieter und Lizenzen

DiagramBridge verwendet beziehungsweise adaptiert Komponenten aus folgenden Projekten:

- [Larry-Labs/drawio-to-visio](https://github.com/Larry-Labs/drawio-to-visio) – Upstream-Ansatz für den lokalen, Playwright-gesteuerten VSDX-Export; Apache License 2.0.
- [Node.js](https://nodejs.org/) – Laufzeitumgebung; eigene Node.js-Lizenzbedingungen.
- [diagrams.net / draw.io](https://github.com/jgraph/drawio) – lokal heruntergeladene Diagramm- und Exportanwendung; eigene Lizenzbedingungen.
- [Playwright](https://github.com/microsoft/playwright) – Browserautomatisierung; eigene Lizenzbedingungen.

Die `THIRD-PARTY-NOTICES.md` und `LICENSE` im Repository enthalten die zugehörigen Hinweise. Die Drittanbieterhinweise beschreiben den Apache-2.0-Lizenzumfang ausdrücklich für den adaptierten Exporter-Anteil; daraus sollte keine pauschale Lizenzzusage für sämtliche getrennten Anwendungskomponenten abgeleitet werden.

---

## English

### Overview

**DiagramBridge** converts draw.io / diagrams.net diagrams locally on a Windows computer into Microsoft Visio files (`.vsdx`) and/or PDF files. The Visio export uses native Visio objects. It does not insert a rasterized picture of the diagram as a fallback layer in the VSDX document.

The application is intended to be distributed as a Windows x64 ZIP release. No separate setup wizard or MSI installer is part of the intended release format. The conversion engine is initialized locally on first launch and then reused from the local application cache.

**Download:** The application archive is published as a ZIP file in [GitHub Releases](https://github.com/JeromeX/DiagramBridge/releases). This repository documents the released application; developer and build instructions are intentionally not part of this documentation.

### Features

| Area | Feature |
|---|---|
| Input files | Supports `.drawio` and draw.io XML (`.xml`). |
| Batch processing | Multiple files in one conversion queue; files can be added through the file picker or drag-and-drop. |
| Duplicate handling | A source file already in the queue is not added a second time. |
| Output names | The proposed base name can be edited for each queue entry. The selected output extension is appended automatically. |
| Output folder | Select one shared output folder or clear the folder field to save beside each source file. |
| Visio | Creates `.vsdx` with native Visio objects and restores the source diagram page names. |
| PDF | Creates a PDF with one output page per diagram page. PDF export can run independently of VSDX export. |
| Combined output | Creates VSDX and PDF in one run. A PDF error does not automatically prevent the VSDX export from completing. |
| Overwrite protection | Existing output files are not silently replaced by default. Overwriting can be explicitly enabled. |
| Status and log | Queue status, per-item details, and a copyable technical log; log entries can be cleared. |
| Visual guide | Built-in step-by-step guide in German and English. |

### Output formats

| Selection | Result | Behaviour |
|---|---|---|
| **Visio only** | `.vsdx` | Creates a native Visio file only. |
| **PDF only** | `.pdf` | Creates a PDF independently of the VSDX export. |
| **Visio + PDF** | `.vsdx` and `.pdf` | Attempts both outputs. A PDF error is logged as a warning; a valid VSDX output is retained. |

PDF export preserves source page dimensions while the drawing content remains within the declared page boundaries. If drawing content extends beyond a page boundary, the affected PDF page can be expanded so the content is not clipped. This PDF adjustment does not modify the source file or the native VSDX page dimensions.

### Language and user interface

DiagramBridge provides German and English interface text, including status messages and the visual guide.

- On first launch, the initial language follows the Windows/process UI language: German for language identifier `de`, English otherwise.
- The **DE/EN** switch changes the interface language immediately.
- The manually selected language is saved and restored on subsequent launches.
- The last selected output folder is also stored in the application settings.

Settings are stored at `%LOCALAPPDATA%\DiagramBridge\settings.json`.


### Architecture

DiagramBridge separates the Windows user interface, conversion orchestration, and the locally executed export engine.

```mermaid
flowchart TD
    UI["WPF desktop UI<br/>MainWindow / ManualWindow"] --> JOBS["ConversionJob<br/>Queue and status"]
    JOBS --> CONV["ConverterService<br/>Conversion orchestration"]
    CONV --> BOOT["EngineBootstrapper"]
    BOOT --> CACHE["Local application cache<br/>%LOCALAPPDATA%\\DiagramBridge"]
    CACHE --> NODE["Node.js runtime"]
    CACHE --> DRAWIO["diagrams.net web app"]
    CACHE --> PW["Playwright"]
    NODE --> BROWSER["Installed Edge / Chrome<br/>Headless Chromium"]
    PW --> BROWSER
    DRAWIO --> SESSION["Local editor session<br/>127.0.0.1"]
    BROWSER --> SESSION
    SESSION --> VSDX["Native VSDX export"]
    SESSION --> PDF["SVG pages → PDF"]
    VSDX --> NATIVE["VsdxNativeProcessor<br/>Restore source page names"]
    NATIVE --> VALID["VsdxValidator<br/>VSDX package validation"]
    VALID --> OUT1["Output: .vsdx"]
    PDF --> OUT2["Output: .pdf"]
```

#### Components

| Component | Responsibility |
|---|---|
| `MainWindow` | WPF main interface, file selection, drag-and-drop, queue, output options, status, and log. |
| `ManualWindow` | Built-in visual guide with localized text. |
| `Localization` | German and English translations for UI and status messages. |
| `ConversionJob` | Queue item model, including state, details, and output name. |
| `ConverterService` | Validates input, creates a temporary working copy, starts the engine, coordinates outputs, and cleans up temporary work files. |
| `EngineBootstrapper` | Checks the local engine and initializes missing, pinned runtime components. |
| `export_vsdx.js` | Opens the local diagrams.net application through Playwright and controls native VSDX and PDF export. |
| `VsdxNativeProcessor` | Restores original diagram page names and checks the mapping of exported pages. |
| `VsdxValidator` | Validates the generated VSDX Open XML package structure, relationships, pages, and Visio objects. |

### Processing flow

1. The UI adds `.drawio` or `.xml` files to the conversion queue.
2. `ConverterService` creates a temporary copy for each job. The original source file is not edited directly.
3. `EngineBootstrapper` checks the local installation. Missing components are downloaded or installed when required.
4. Node.js starts the export logic. Playwright launches Microsoft Edge or Google Chrome in headless mode and loads the locally stored diagrams.net web app through a loopback endpoint (`127.0.0.1`).
5. VSDX output uses the native diagrams.net Visio exporter. For PDF output, diagram pages are prepared as SVG and printed to PDF by the browser.
6. VSDX post-processing restores source page names. The VSDX package is then structurally validated.
7. Successful outputs are moved to the selected destination. Temporary working files are removed afterwards.

### Runtime components

The following versions are pinned in application version 0.3.20:

| Component | Version / source | Purpose |
|---|---|---|
| .NET | `net10.0-windows` | Windows WPF application. |
| Node.js | `22.23.5` · Windows x64 | Local execution of the JavaScript export logic. |
| diagrams.net | `26.0.16` · commit `987146fb55a547a975961f57dddc781abf7648ba` | Locally stored diagram editor and export functions. |
| Playwright | `1.62.0` | Browser automation for local export. |
| Browser | Microsoft Edge or Google Chrome | Headless execution of the local diagrams.net session. |

The engine components are not all embedded in the application archive. They are stored under `%LOCALAPPDATA%\DiagramBridge` during first-time setup. An internet connection is required for this initial setup. After setup, subsequent conversions can use the locally cached components.

### Privacy and local processing

- Diagram conversion runs on the local Windows computer.
- The source file is copied to a temporary working folder; the original is not modified.
- The local editor session is hosted on `127.0.0.1`.
- The browser session is restricted to local resources; external browser requests are blocked during conversion.
- An internet connection is needed on first engine setup to retrieve the pinned components.
- DiagramBridge is not an online conversion service. Diagram content is not transferred to an external conversion server.

### System requirements

- Windows x64.
- Microsoft Edge or Google Chrome installed locally.
- Internet access for the initial local engine setup.
- Sufficient free storage for the engine cache, temporary working files, and output files.

### Limitations and output-quality notes

- The native VSDX export path is based on an adapted upstream export approach that is described as beta by that upstream project. A structurally valid VSDX file does not guarantee pixel-perfect rendering in every Visio version.
- VSDX validation checks package structure and selected content. It is not a full visual comparison in Microsoft Visio.
- The current VSDX processing path accepts source files containing 1 to 200 diagram pages.
- Externally linked images should be embedded directly in the draw.io file before export. Image resources that are not locally available or that are referenced externally may prevent PDF export in particular.
- The rendering of individual shapes, fonts, and connectors can vary with diagram content and Visio version. Important documents should be reviewed in the intended target application.

### Release and version

- **Current documented application version:** `0.3.20`
- **Distribution format:** Windows x64 ZIP containing the application.
- **No separate installer:** The release process is intended to publish the program ZIP archive.
- **Version 0.3.20:** Initial window size adjusted to 1300 × 840; the empty queue viewport now uses the same dark surface as the queue panel.

### Third-party components and licences

DiagramBridge uses or adapts components from the following projects:

- [Larry-Labs/drawio-to-visio](https://github.com/Larry-Labs/drawio-to-visio) – upstream approach for local Playwright-driven VSDX export; Apache License 2.0.
- [Node.js](https://nodejs.org/) – runtime environment; separate Node.js licence terms.
- [diagrams.net / draw.io](https://github.com/jgraph/drawio) – locally downloaded diagram and export application; separate licence terms.
- [Playwright](https://github.com/microsoft/playwright) – browser automation; separate licence terms.

The repository's `THIRD-PARTY-NOTICES.md` and `LICENSE` files contain the related notices. The third-party notice explicitly describes the Apache 2.0 scope for the adapted exporter portion; it should not be interpreted as a blanket licensing statement for every separate application component.

---

<div align="center">

**DiagramBridge 0.3.20** · © 2026 Malte Speck · [GitHub](https://github.com/JeromeX)

</div>