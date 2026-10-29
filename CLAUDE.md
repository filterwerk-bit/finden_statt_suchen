# CLAUDE.md – Filterwerk Website («Finden statt suchen»)

Übergabe, Stand 29.10.2026. Sprache im Projekt: **Deutsch** (Schweizer Schreibweise, «ss»). Antworten an André immer auf Deutsch.

## 1. Ziel und Kontext

- Auftraggeber: André, Leiter Digital Learning Hub Sek II (DLH, dlh.zh.ch, Kanton Zürich).
- Leitfrage von Lehrpersonen: «Was brauche ich für meinen Unterricht?» Der DLH hat Hunderte Angebote in vielen Gefässen (Praxisbeispiele, Workshops, Essenzen, Innovationsfonds …), aber keine gemeinsame Suche.
- Das Projekt baut **Prototypen zur Diskussion** (nicht die offizielle Website): eine Seite, die den ganzen DLH-Bestand über Fach, Anliegen, Art des Angebots, Nutzungsart und Schultyp filterbar, durchsuchbar und per Bot befragbar macht.
- Titel der Seiten: «Inspiration vom DLH für Deinen Unterricht». Durchgehend **Du-Form**.
- Bestand aktuell: **467 Einträge, 461 aktiv, 6 Archiv** (Stand 29.10.2026).
- Beteiligte: André (Entscheide, Upload auf GitHub); Hans (betreibt Bot-Backend und -Frontend, liefert Anleitungen für Bot-Anpassungen).

## 2. Ordnerstruktur (Projektstamm)

| Datei / Ordner | Rolle |
|---|---|
| `Suchen-Filter-Ergebnisse.html` | **Hauptfassung** (SFE): Empfang mit 3 Kacheln, dann Filterspalte links + Ergebniskarten + Bot |
| `Suchen-Filter-Ergebnisse-Dropdown.html` | Variante: 5 Dropdowns im violetten Band oben, Ergebnisse volle Breite |
| `Suchen-Filter-Ergebnisse-Dropdown_links.html` | Variante: 5 Dropdowns als sticky Spalte links (von Dropdown abgeleitet) |
| `Definitionen und Bestandeskarte.htm` | Nachschlageseite: Angebotsarten, Matrizen Thema/Anliegen × Angebotsart, Bestandskarte – **eigene Datenkopie** |
| `DLH_Matrix_Anliegen_Format_A3.pdf`, `DLH_Matrix_Thema_Format_A3.pdf` | A3-Druckblätter (Stand 3.10.2026, veraltet) |
| `Filterseite_Konzept.html`, `Filterseite_Fassung_ohne_Bot.html`, `Filter-Ergebnisse-Suchen.html` | Ältere Fassungen, eingefroren (363/369 Angebote) – nur noch zum Vergleich |
| `Downloadpruefung_2026-09-30.html` | Prüfbericht aller Downloads (historisch) |
| `ANLEITUNG_ANDRE_*.md` | Anleitungen von Hans (Bot) |
| `Claude outputs/README.md` | Kopie der Repo-README (Stand 24.10.) |
| `_Archiv/` | Sicherungen vor jeder Änderung (`<Datei>_vor_<Thema>_<Datum>.html`), alte Build-Skripte (`build_sheets_2026-10-03.py`) |

Jede Seite ist **eine einzelne HTML-Datei ohne Build-Schritt**: CSS, JS und Daten inline. Schrift und Logo werden von dlh.zh.ch nachgeladen.

Nur in diesem Ordner arbeiten. Der frühere Ordner «OneDrive – KME Zürich/Claude_DLH/Suchen-Filter-Ergebnisse Website» ist stillgelegt.

## 3. Datenmodell

In jeder SFE-Seite steht eine Zeile `const DB = {...}` (JSON) mit `stand_der_uebersicht`, `anliegen` (20), `faecher` (33), `angebote`.
Felder pro Angebot: `id, titel, lead, url, format, faecher, fach_gesetzt, anliegen, stufen, aufwand, datum, quelle, tags, personen, archiv`.

- Angebotsarten (`format`): Innovationsfonds 128 · Essenz 105 · Impuls-Workshop 88 · Tipp des Monats 52 · Kuratiertes 22 · Digi Talk 21 · eAssessment-Methoden 20 · Praxisbeispiele KI 14 · Podcast 10 · Weiterbildung 7.
- Interne Code-Namen bleiben alt (`a.format`, `FORMATE`, `a.aufwand`, `data-kategorie="aufwand"`), sichtbar heisst es «Art des Angebots» bzw. «Einsatz/Nutzungsart».
- `stufen` wird beim Laden per `schultypen(a)` neu gesetzt (nur Gymnasium/Berufsbildung, «Regel C»).
- Good Practice ist **keine Art**, sondern eine Auswahl per Regel `istGoodPractice` (Regex GP_BERICHT u. a.); aktuell 63.
- `personen`: nur Input/Projektleitung/Autorschaft, **keine Moderation**.

**Der DB-Block muss in allen drei SFE-Seiten byte-identisch sein** (Prüfung: `grep '^const DB' <Datei> | md5sum`).

## 4. Wichtigste Entscheide (mit Begründung)

- **Drei parallele SFE-Fassungen** zum Vergleichen; André hat sich noch nicht für eine entschieden. Jede Änderung an Daten/Logik in **alle drei** nachziehen (`_links` von Hand, sie ist kein Build-Produkt).
- **Begriffe:** «Gefäss» → «Format» → «Art des Angebots» (Lehrpersonen verstanden «Gefäss» nicht; eAssessments sind kein «Format»). In der Bestandeskarte bleibt bewusst «Angebotsart» – nicht angleichen. Angebote heissen sichtbar «Inspirationen».
- **«Wo fange ich an?»** als dritte Kachel (für Late Majority): 7 Situationen mit kuratierten ids statt Filter; eigene LateMajority-Seite aufgegeben.
- **20 Anliegen**: Wortlaute am 16.10. redaktionell überarbeitet, **keine Umteilung** von Angeboten. Anliegen 5 (Fachschaft-KI-Assistenten) war bewusst Platzhalter und hat seit 24.10. erst wenige Einträge – nicht als Fehler melden, nicht ungefragt befüllen.
- **«Überfachlich»** nicht mehr in der Fachliste (Verwechslung mit «alle Fächer»); Fachfilter ist streng.
- **Downloads ohne Referrer**: jDownloads auf dlh.zh.ch liefert `/downloads/send/` nur ohne fremden Referrer, sonst Ordnerübersicht. Lösung: MutationObserver setzt `referrerPolicy=no-referrer` nur auf diese Links (globales Meta-Tag verworfen, weil dlh.zh.ch sonst die Statistik-Herkunft verliert). Handy öffnet PDFs im neuen Tab.
- **eAssessment-Accordions** als bereinigte Kopie (`ACC_INHALT`) im Ansichtsfenster, mit Link zum Original. Text-Fragment-Links (`#:~:text=`) abgelehnt.
- **Bot-Sicherheit** (Hinweise Hans, 26.10.): `/health` erst beim Öffnen des Bots, `sanitizeBotHtml()` vor jedem `innerHTML`; Datenhinweis «keine Personendaten eingeben». `window.sendSuggestedQuestion = q => frageStellen(q);` für Vorschlagsknöpfe.
- **Aufnahmeregeln Bestand**: DLH-eigene Angebote ja (inkl. EB Digital, Digi Talk, alle Tipp-des-Monats-Videos, angekündigte Workshops); rein externe WB-Kompass-Inhalte nein; vergangene Terminkurse nein; **Schulalltag gar nicht** (nicht erneut vorschlagen).
- **YOOtheme-Import** (Layout-JSON für Joomla): von André zurückgestellt – nicht anbieten, solange er nicht fragt.

## 5. Aktueller Stand und offene Punkte

Stand 29.10.2026: Bestandsprüfung durchgeführt (+7 Einträge, ids 462–468), alle drei SFE-Seiten und die Bestandeskarte nachgeführt.

Offen:
- Upload der Änderungen seit 24.10. auf GitHub sowie README/index.html nachführen (Zählwörter, Stand, «Nutzungsart/Schultyp» statt «Einsatz, Stufe»).
- A3-Blätter (Stand 3.10., alte Anliegen-Wortlaute, 371) neu bauen – immer **beide** zusammen.
- Bestandeskarte: `personen` fehlt in ihrer Datenkopie; «Denken mit KI 14» ungeklärt (nicht anfassen ohne Rückfrage).
- Personen: Podcast-Gäste Ep. 2–8 fehlen; Workshop 150 und 11 frühe Essenzen ohne Personen.
- Anliegen 8 enthält Ausreisser (nicht bereinigt); Einsatz folgt fast 1:1 der Angebotsart – Begriffsdiskussion offen.
- «Wo fange ich an?»: Situationen/Zitate/Kontakt vom DLH redaktionell zu bereinigen.
- GitHub-Connector auf Andrés Rechner ist als `aapdinternet` angemeldet und hat **keine Schreibrechte** im neuen Repo.
- Echte Bot-Antworten wurden lokal nie getestet (CORS bei `file://`, Bot-Server aus der Cloud gesperrt) – Test auf GitHub Pages.

## 6. Arbeitskonventionen

- **Vor jeder Änderung Sicherung** nach `_Archiv/<Datei>_vor_<Thema>_<JJJJ-MM-TT>.html` (bei mehreren Dateien Unterordner `_Archiv/vor_<Thema>_<Datum>/`). Nichts löschen; `_Archiv` nie inhaltlich ändern.
- Dateien **direkt im Ordner auf Andrés Rechner bearbeiten** (Python read-modify-write oder `sed -i`), nicht Inhalte abtippen. Die Dateien sind gross (bis ~490 KB, ~2000 Zeilen, DB in einer Zeile).
- Nach jeder JS-Änderung: Skript extrahieren und `node --check`; DB-md5 über alle drei Seiten vergleichen; wenn möglich mit Playwright im Browser prüfen (Trefferzahl «<b>N</b> von N Inspirationen»).
- Bei Datenübernahme in die Bestandeskarte: Matrizen alt/neu vergleichen (Details öffnen, sonst `innerText` leer). Falle: BB_F braucht «ABU».
- Eigene Leads ohne GP_BERICHT-Wörter («erfahrungen aus» u. ä.) formulieren, sonst wird ein Workshop fälschlich Good Practice.
- Stand-Datum bei Bestandsänderungen überall nachführen: `DB.stand_der_uebersicht`, Ergebniskopf, Fuss der `_links`-Spalte, Bestandeskarte.
- Inhaltliche Entscheide (Aufnahme, Umteilung, Begriffe) **nicht selbst treffen** – André fragen, Varianten vorschlagen.
- Bestandsprüfung gegen dlh.zh.ch: dlh.zh.ch ist aus der Cloud gesperrt → im Browser-Pane per `fetch` im Seitenkontext (Artikel-ID-Probe `index.php?option=com_content&view=article&id=N`, jDownloads-Kategorie 3, Tipp des Monats h3, Digi Talk, IF-Übersicht, eAssessments 13+7).
- Keine Passwörter, Tokens oder Personendaten in Dateien, Commits oder Memory.

## 7. Deployment

- Öffentlich über GitHub Pages: Repo **filterwerk-bit/finden_statt_suchen**, Branch `main`, Pages «Deploy from a branch», `/ (root)`.
  URL: https://filterwerk-bit.github.io/finden_statt_suchen/ (altes Repo aapdinternet/GoodPractice nicht mehr pflegen).
- Im Repo: die sechs Seiten, `Definitionen%20und%20Bestandeskarte.htm`, beide A3-PDFs, `index.html`, `README.md`, `.nojekyll`. **README und index.html existieren nur im Repo** – vor Änderungen von dort holen (raw.githubusercontent.com ist erreichbar).
- Ablauf:
  1. Änderung lokal in diesem Ordner, Sicherung, Prüfung (siehe 6).
  2. **André lädt die HTML-Seiten und PDFs selbst hoch** (zu gross für den Connector, PDFs gar nicht möglich). Claude bereitet höchstens README/index.html vor.
  3. Kommt eine Seite dazu oder weg: README und index.html nachführen (Zählwörter, Beschreibungen, Stand).
  4. Pages braucht 1–2 Minuten; danach im Browser-Pane prüfen (privates Fenster/Cache beachten). github.io ist aus der Cloud gesperrt.
- Vor jedem Upload prüfen, ob der Inhalt öffentlich gedacht ist – gelöschte Dateien bleiben in der Git-Historie.
- Der Bot läuft extern (Backend auf onrender.com, Frontend im Repo von Hans); Änderungen am Bot-Verhalten laufen über Hans.
