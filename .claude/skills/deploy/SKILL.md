---
name: deploy
description: Baut die gView-GIS-Doku (de + en) mit Sphinx und kopiert das Ergebnis nach app/de bzw. app/en, damit es beim nächsten Push live geht. Verwenden, wenn der Nutzer sagt "deploy", "build", "veröffentlichen", "html bauen" o. Ä.
---

# Doku bauen & veröffentlichen

Siehe [CLAUDE.md](../../../CLAUDE.md) für den Gesamtkontext (RST/Sphinx, de/en-Struktur,
warum `app/` = live). Dieser Skill deckt nur den mechanischen Build- und Kopier-Schritt ab.

## Ablauf

1. **Beide Sprachen bauen** (jeweils im Sprachverzeichnis ausführen):

   ```
   cd src/de && make.bat html
   cd src/en && make.bat html
   ```

   Der Build läuft lokal gegen `sphinx-build` — falls der Befehl fehlschlägt, weil
   `sphinx-build` nicht gefunden wird, dem Nutzer mitteilen, dass Sphinx installiert werden
   muss (`pip install sphinx sphinxcontrib-mermaid piccolo-theme`), nicht selbstständig
   global installieren ohne Rückfrage.

2. **Build-Output nach `app/` kopieren**, für jede Sprache separat, Inhalt von
   `src/{lang}/build/html` nach `app/{lang}` (Zielordner-Inhalt wird dabei ersetzt, nicht
   nur ergänzt — gelöschte/umbenannte Seiten aus dem alten Build sollen nicht als Leichen in
   `app/` zurückbleiben):

   ```
   # PowerShell (nicht Git Bash! dort mangelt /MIR zu einem Pfad wie ".../Git/MIR")
   robocopy src\de\build\html app\de /MIR
   robocopy src\en\build\html app\en /MIR
   ```

   `/MIR` spiegelt den Ordner (löscht in `app/{lang}` auch Dateien, die im neuen Build nicht
   mehr existieren). Ohne `robocopy` verfügbar: `Copy-Item -Recurse -Force`, dann aber manuell
   prüfen, ob verwaiste Dateien in `app/{lang}` übrig geblieben sind.

   **robocopy-Exit-Codes 0–7 sind Erfolg** (Bitmask: 1 = Dateien kopiert, 2 = zusätzliche
   Dateien im Ziel entfernt, ...). Erst ab 8 liegt ein echter Fehler vor — nicht jeden
   Nicht-Null-Exit-Code als Fehlschlag werten.

   Falls in `src/{de,en}/build/html` eine Datei/Ordner `.doctrees` auftaucht: Das ist eine
   Doctree-Cache-Verunreinigung aus einem `sphinx-build -b html source build/html`-Aufruf ohne
   separates Doctree-Verzeichnis (im Gegensatz zu `-M html`/`make html`, die die Doctrees
   sauber nach `build/doctrees` auslagern). Vor dem Kopieren löschen, sonst landet der Cache
   in `app/{lang}` und wird live mit ausgeliefert.

3. **Diff kurz sichten** (`git status` / `git diff --stat` auf `app/`), bevor committet wird —
   ein `/MIR`-Kopiervorgang kann größere Mengen an geänderten/gelöschten Dateien erzeugen
   (`.buildinfo`, `_images`, `searchindex.js`, `objects.inv` ändern sich praktisch bei jedem
   Build).

4. **Committen und pushen ist ein separater, expliziter Schritt** — nicht automatisch nach dem
   Kopieren ausführen. Erst nach Zustimmung des Nutzers `git add app/`, commit und push, da der
   Push auf den konfigurierten Branch sofort die Azure-Static-Web-Apps-Deployment-Action
   auslöst und die Seite live aktualisiert.

## Nicht anfassen

* `src/{de,en}/build/` selbst nie committen (gitignored, reines Zwischenergebnis).
* Quell-`.rst`-Dateien werden von diesem Skill nicht verändert — nur gebaut und kopiert.
