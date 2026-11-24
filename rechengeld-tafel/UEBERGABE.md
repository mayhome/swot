# Rechengeld-Tafel – Übergabe

Interaktives Euro-Rechengeld für die Tafel (Grundschule, Thema Geld). Kinder ziehen Scheine und Münzen von Stapeln in ein Legefeld und lösen Aufgaben in drei Niveaustufen.

## Lieferumfang

| Datei | Inhalt |
|---|---|
| `rechengeld-tafel.html` | Das komplette Tool in einer Datei (ca. 350 KB). HTML, CSS, JavaScript, alle Geldbilder und die Schrift sind eingebettet. |
| `UEBERGABE.md` | Diese Beschreibung. |

Die Datei läuft ohne Internet, ohne Server und ohne Installation. Sie hat keine externen Abhängigkeiten und lädt keine Bibliotheken nach.

## Einbinden

**Direkt öffnen:** Datei im Browser öffnen (Chrome, Edge, Firefox, Safari).

**In ein anderes Programm einbetten (iframe):**

```html
<iframe src="rechengeld-tafel.html#s1"
        style="width:100%;height:100%;border:0"
        allow="fullscreen"></iframe>
```

**Startstufe über die Adresse wählen** (Anker am Ende der URL):

| Anker | Startet mit |
|---|---|
| `#s1` (oder keiner) | Stufe 1 – Anzahl Scheine |
| `#s2` | Stufe 2 – Möglichst wenig Teile |
| `#s3` | Stufe 3 – Wie viele Möglichkeiten? |

Ändert das umgebende Programm den Anker später (z. B. `iframe.src = "…#s2"`), wechselt das Tool die Stufe. Wechselt die Lehrkraft die Stufe über die Reiter, wird der Anker mitgeschrieben.

**Anforderungen an die Einbettung**
- Mindestgröße etwa 1000 × 700 px für die Tafel. Darunter stapelt sich das Layout einspaltig (funktioniert bis ca. 400 px Breite).
- Bedienung mit Maus, Finger und Stift (Pointer Events). Das umgebende Programm darf Zieh-Gesten im iframe nicht abfangen.
- Der Vollbild-Knopf braucht `allow="fullscreen"` am iframe. Fehlt das, passiert beim Klick einfach nichts.
- `localStorage` ist optional (merkt sich nur die Summen-Darstellung). Ist es gesperrt, läuft das Tool trotzdem.
- Helles und dunkles Farbschema folgen automatisch der Systemeinstellung (`prefers-color-scheme`). Erzwingen lässt es sich mit `data-theme="light"` oder `data-theme="dark"` am `<html>`-Element.

## Funktionen

**Geldleiste (unten, bleibt beim Scrollen sichtbar):** Stapel für 5, 10, 20, 50, 100, 200 € und 1, 2, 5, 10, 20, 50 ct, 1 €, 2 €. Ziehen ins Legefeld oder Antippen legt ein Stück hin. Nicht erlaubte Geldstücke sind ausgegraut.

**Legefeld (rechts):** Scheine und Münzen in getrennten Reihen. Ein Stück herausziehen oder antippen nimmt es weg. Bei mehr als 10 Stücken werden sie kleiner dargestellt.

**Statusleiste über dem Legefeld:**
- Summe in drei Darstellungen umschaltbar: `0,00 €` (47,85 €), `€ und ct` (47 € 85 ct), `nur ct` (4.785 ct)
- Zähler für Scheine, Münzen, Teile
- „Summe verbergen“ (zeigt dann `?`), „Zurück“ (Rückgängig, bis 200 Schritte), „Leeren“

**Aufgabenkarte (links):** Aufgabentext, Betrag (Eingabefeld und Schnellwahl), Einstellungen der Stufe, „Prüfen“, „Neue Aufgabe“ (Zufall), Lösungen anzeigen, Rückmeldung.

### Stufe 1 – Anzahl Scheine
- Aufgabe: „Lege 300 € mit genau N Scheinen“ (N = 2 bis 6).
- Nur Scheine erlaubt. Beträge nur in 5-€-Schritten.
- Richtige Lösungen werden gesammelt (Reihenfolge egal, doppelte werden erkannt). Danach leert sich das Legefeld nach 1,4 s für die nächste Lösung.
- „Alle Lösungen“ zeigt die Gesamtzahl und die noch nicht gefundenen. Antippen einer Lösung legt sie ins Feld.

### Stufe 2 – Möglichst wenig Teile
- Aufgabe: „Lege 333 € mit möglichst wenig Scheinen und Münzen.“
- Alle Scheine und Münzen erlaubt. Option „Neue Aufgaben mit Cent“.
- Prüfen meldet: Betrag falsch, Betrag richtig aber zu viele Teile, oder optimal.
- „Lösung zeigen“ nennt die kleinste Anzahl und die Zusammensetzung.

### Stufe 3 – Wie viele Möglichkeiten?
- Aufgabe: „Lege 400 €. Wie viele Möglichkeiten findest du?“
- Die Lehrkraft wählt das erlaubte Geld (Standard: 50, 100, 200 €; dann gibt es für 400 € genau 9 Möglichkeiten).
- Sammeln wie in Stufe 1. „Alle Lösungen“ zeigt die Gesamtzahl; die einzelnen Lösungen werden nur bis 80 Stück aufgelistet.

### Rückmeldungen (Wortlaut)
- „Es fehlen noch 50 €.“ / „Das sind 50 € zu viel.“
- „Der Betrag stimmt! Aber du hast 3 Scheine gelegt, nicht 2.“
- „Richtig! Das ist eine Möglichkeit. Leg die nächste!“
- „Diese Möglichkeit hast du schon gefunden. Findest du eine andere?“
- „Super! 5 Teile – weniger geht nicht.“ / „Der Betrag stimmt! Du hast 8 Teile. Es geht aber mit weniger.“
- „Du hast Geld benutzt, das bei dieser Aufgabe nicht erlaubt ist.“

## Aufbau des Codes

Alles steht in `rechengeld-tafel.html`: erst `<style>`, dann das HTML-Gerüst, dann ein `<script>`. Kein Framework, kein Build-Schritt.

**Beträge werden intern immer in Cent gerechnet** (200 € = `20000`), damit keine Rundungsfehler entstehen.

| Teil | Zweck |
|---|---|
| `IMG` | Geldbilder als Data-URIs, Schlüssel = Wert in Cent. Scheine JPEG, Münzen WebP mit transparentem Rand. |
| `DEN` | Liste aller Geldstücke: Wert `v`, Typ `t` (`n` Schein / `c` Münze), Beschriftung. |
| `S` | Gesamter Zustand: `mode` (`s1`/`s2`/`s3`), `field` (gelegte Werte), `hist` (Rückgängig), `target` (Zielbetrag je Stufe), `n` (Scheinanzahl Stufe 1), `allowed3` (erlaubtes Geld Stufe 3), `found` (gefundene Lösungen), `fmt` (Summen-Darstellung), `showSum`, `reveal`, `fb` (Rückmeldung). |
| `enumerate(ziel, werte, anzahl)` | Zählt alle Kombinationen auf (optional mit fester Anzahl Teile). |
| `countWays(ziel, werte)` | Zählt Möglichkeiten per dynamischer Programmierung (BigInt), auch wenn es sehr viele sind. |
| `greedy(ziel)` | Kleinste Stückzahl. Beim Euro-System liefert das gierige Verfahren immer das Optimum. |
| `check()` / `saveFound()` | Prüflogik und Sammeln der Lösungen. |
| `newTask()` | Zufallsaufgaben je Stufe (Stufe 1 nur lösbare Kombinationen). |
| `renderTill()`, `renderBoard()`, `renderTask()` | Zeichnen Geldleiste, Legefeld und Aufgabenkarte neu aus `S`. |
| Pointer-Handler (`pointerdown`/`move`/`up`) | Ziehen & Ablegen; eine Bewegung unter 8 px gilt als Antippen. |

**Typische Anpassungen**
- Schnellwahl-Beträge: Aufrufe von `presetChips([...])` in `renderTask()` (Werte in Cent).
- Anzahl Scheine in Stufe 1: Liste `[2,3,4,5,6]` in `renderTask()`.
- Zufallsbereiche: `newTask()`.
- Texte der Rückmeldungen: `check()` und `saveFound()`.
- Größe der Geldstücke: CSS-Variable `--mm` (Pixel pro Millimeter) bei `.money`, `.field .money`, `.field.many .money`. Scheine und Münzen sind untereinander maßstabsgetreu; Münzen sind zusätzlich um den Faktor 2,2 vergrößert, damit sie an der Tafel gut greifbar sind.
- Farben: CSS-Variablen in `:root` (hell) und in den beiden Dunkel-Blöcken.
- Geldbilder austauschen: Einträge in `IMG` durch neue Data-URIs ersetzen. Seitenverhältnis der Scheine ist durch die echten Maße in CSS festgelegt (`.n5` … `.n200`).

## Hinweise

- Die Geldbilder sind aus einem Foto des vorhandenen Unterrichtsmaterials ausgeschnitten und daher leicht schräg. Bessere Vorlagen (Scan, PDF) können ersetzt werden, siehe oben.
- Schrift: Lexend (SIL Open Font License), eingebettet.
- Es gibt noch keine Schnittstelle zur Fernsteuerung (z. B. `postMessage`). Das Programm kann nur über den Anker die Startstufe wählen. Aufgaben stellen, Geld legen oder Ergebnisse auslesen von außen wäre eine Erweiterung.
