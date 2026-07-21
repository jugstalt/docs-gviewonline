# gView GIS Dokumentation

Dieses Repo enthält die Online-Doku für [gView GIS](https://github.com/jugstalt/gview-gis).
Quellformat ist **reStructuredText (RST)**, gebaut wird mit **Sphinx** (Theme `piccolo_theme`,
Extension `sphinxcontrib.mermaid`). Ausgeliefert wird die Doku unter:

* Englisch: https://docs.webgiscloud.com/gview/en/index.html
* Deutsch: https://docs.webgiscloud.com/gview/de/index.html

## Verzeichnisstruktur

```
src/de/source/...   deutsche RST-Quellen
src/en/source/...   englische RST-Quellen
src/de/build/html   lokaler Sphinx-Build (gitignored, nicht committen)
src/en/build/html   lokaler Sphinx-Build (gitignored, nicht committen)
app/de              gebautes HTML, wird deployed (getrackt in git)
app/en              gebautes HTML, wird deployed (getrackt in git)
```

`src/de/source` und `src/en/source` sind **strukturell identisch** — gleiche Ordner, gleiche
Dateinamen, gleiche `img/`-Unterordner neben den jeweiligen `.rst`-Dateien. Nur der Inhalt ist
übersetzt.

Wichtige Ordner unter `source/`: `setup/`, `webapps/`, `server/` (mit `inst/` und `manual/`),
`commandline/`, `examples/`, `appendix/`, `spec/`. Jede Sektion hat eine `index.rst` mit
`.. toctree::`.

## Zweisprachigkeit — die wichtigste Regel

**Jede inhaltliche Änderung (neue Seite, geänderter Absatz, neues Bild) muss in beiden
Sprachversionen erfolgen.** Beim Bearbeiten von `src/de/...` immer prüfen, ob die
korrespondierende Datei unter `src/en/...` (gleicher relativer Pfad) ebenfalls angepasst werden
muss — und umgekehrt. Wenn nur eine Sprache explizit gewünscht ist oder eine Übersetzung noch
aussteht, das dem Nutzer explizit mitteilen statt es stillschweigend zu einer Sprache inkonsistent
zu lassen.

Bilder liegen im `img/`-Unterordner neben der jeweiligen `.rst`-Datei und werden i.d.R. für
beide Sprachen identisch verwendet (gleicher Dateiname in `src/de/.../img/` und
`src/en/.../img/`), außer der Screenshot zeigt sprachabhängigen UI-Text.

## RST-Stilkonventionen (aus bestehenden Seiten abgeleitet)

* **Überschriften:** Level 1 = Unterstrich mit `=`, Level 2 = Unterstrich mit `-` (Länge der
  Unterstreichung ≥ Länge des Titeltexts). Es gibt bisher keine tiefere Verschachtelung (Level 3)
  in der Doku — falls nötig, `~~~~` als nächste Ebene verwenden.
* **Hervorhebungen:**
  * `*kursiv*` für Produkt-/Komponentennamen (*gView Carto*, *gView Server*, *gView MapServer*)
  * `**fett**` für UI-Begriffe/Labels (**Client**, **Secret**, **Process Model**)
  * `` ``code`` `` für Literale, Dateinamen, Befehle, feste Werte (``LocalSystem``,
    ``dotnet-hosting-8.0.x-win.exe``)
* **Bilder:** `.. image:: img/dateiname.png` (relativer Pfad zum lokalen `img/`-Ordner).
* **Hinweisboxen:** `.. note::` für Tipps/Warnungen, eingerückter Body.
* **Querverweise:** Anchor am Seitenanfang oder vor einer Section mit `.. _label-name:`,
  referenziert über `` :ref:`label-name` ``. Label-Namen sind lowercase-mit-unterstrichen/bindestrichen
  (z. B. `server_postinstallation`, `config-server`).
* **Externe Links:** `` `Linktext`_ `` mit späterer Definition ``.. _`Linktext`: https://...``.
* **Listen:** einfache `-`-Listen für Aufzählungen.
* **toctree:** jede `index.rst` einer Sektion bindet ihre Unterseiten per
  `.. toctree::` mit `:maxdepth:` und `:caption:` ein — neue Seiten dort eintragen, sonst
  tauchen sie nicht in der Navigation auf.

## Build & Veröffentlichung

Pro Sprache einzeln bauen (im jeweiligen `src/de` bzw. `src/en` Verzeichnis):

```
make html          # Linux/macOS
make.bat html       # Windows
```

Output landet in `src/{de,en}/build/html` (gitignored, lokales Build-Artefakt).

**`app/de` und `app/en` sind exakt das, was live veröffentlicht wird.** Es gibt keinen
Build-Schritt in der CI — die GitHub-Action (`.github/workflows/azure-static-web-apps-*.yml`)
lädt bei jedem Push auf den konfigurierten Branch (aktuell `gview6`) den kompletten Inhalt von
`app/` 1:1 als Azure Static Web App hoch. D. h. nach jeder inhaltlichen Änderung müssen:

1. beide Sprachen neu gebaut werden (`make.bat html` in `src/de` **und** `src/en`),
2. der Inhalt von `src/{de,en}/build/html` nach `app/{de,en}` kopiert werden,
3. die Änderungen unter `app/` committet und gepusht werden — erst dann ist die Änderung online.

Für den kompletten Build-und-Kopier-Ablauf siehe den `deploy`-Skill.

**Nie direkt in `app/de` oder `app/en` editieren** — das sind Build-Artefakte und werden beim
nächsten Sphinx-Build überschrieben.

## Sonstiges

* `conf.py` je Sprache setzt u. a. `language = 'de'` bzw. `'en'`, `project = 'gView GIS'`,
  `html_theme = 'piccolo_theme'`.
* Rechtschreibprüfung: `.vscode/settings.json` setzt `cSpell.language` auf `,de` (Deutsch +
  Englisch aktiv).
