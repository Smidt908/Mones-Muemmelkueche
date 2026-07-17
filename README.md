# Mones Mümmelküche

Ein Kochbuch als App: Rezepte per Foto oder Link aufnehmen, nach Kategorien
sortiert wiederfinden, bewerten, mit Notizen versehen — und beim Kochen
automatisch auf die gewünschte Portionszahl umrechnen lassen.

Alles läuft im Browser. Die Rezepte bleiben auf dem Gerät und werden nirgends
hochgeladen.

---

## Was die App kann

**Rezepte aufnehmen — auf vier Wegen**

- **Foto oder Screenshot.** Die Seite aus dem Kochbuch abfotografieren. Die App
  liest den Text, sortiert ihn in Titel, Portionen, Zutaten und Schritte und
  legt das Ergebnis zum Prüfen vor.
- **Link.** Adresse einer Rezeptseite einfügen. Chefkoch, EAT SMARTER und die
  meisten anderen hinterlegen ihre Rezepte maschinenlesbar im Quelltext — die
  übernimmt die App vollständig und genau. **Dieser Weg hängt aber an fremden
  Diensten und fällt gelegentlich aus** (siehe unten).
- **Rezepttext einfügen.** Auf der Rezeptseite alles markieren (Strg+A),
  kopieren, einfügen. Die App sucht sich Titel, Portionen, Zutaten und Schritte
  selbst heraus und wirft Navigation, Sternchen und Kommentare weg. Dieser Weg
  braucht **keine fremden Dienste** und funktioniert deshalb immer — die
  Rückfallebene, wenn der Link klemmt.
- **Von Hand.** Für Omas Rezepte, die nirgends stehen.

**Portionen umrechnen.** Beim Anlegen wird eingetragen, für wie viele das
Rezept reicht. Beim Kochen stellt man die gewünschte Zahl ein, und alle Mengen
rechnen sich mit. Die App rundet dabei auf küchentaugliche Werte und wechselt
die Einheit, wenn es sich besser liest:

| im Rezept (4 Portionen) | für 6 Portionen | für 1 Portion |
| ----------------------- | --------------- | ------------- |
| `1 kg Kürbis`           | `1,5 kg`        | `250 g`       |
| `800 ml Gemüsebrühe`    | `1,2 l`         | `200 ml`      |
| `2 Zehen Knoblauch`     | `3 Zehen`       | `½ Zehe`      |
| `1/2 TL Salz`           | `¾ TL`          | `⅛ TL`        |

Zeilen ohne Menge (`Salz und Pfeffer`) bleiben unangetastet. Eine Zeile mit
Doppelpunkt am Ende (`Für den Teig:`) wird zur Zwischenüberschrift und nicht
mitgerechnet.

**Kategorien.** Die App ordnet jedes Rezept selbst ein — von Frühstück über
Suppe, Eintopf, Braten und Auflauf bis Kuchen, Torte und Eingemachtes
(24 Kategorien). In der Leiste links erscheinen nur die Kategorien, in denen
auch etwas steht; innerhalb jeder Kategorie sind die Rezepte alphabetisch
sortiert. Die Zuordnung lässt sich beim Anlegen und später jederzeit ändern.

**Bewerten und notieren.** Ein bis fünf Sterne, von *Nicht unser Geschmack* bis
*Super lecker!* — noch einmal auf denselben Stern tippen nimmt die Bewertung
zurück. Das Notizfeld speichert von allein.

**Suchen.** Über Titel, Zutaten und Notizen.

**Teilen aus Chrome.** Auf dem Handy in Chrome auf „Teilen" tippen und die
Mümmelküche auswählen: Der Link landet direkt im Import.

---

## Einrichten auf GitHub Pages

Damit Android die App wirklich auf den Startbildschirm legt, braucht sie eine
echte `https://`-Adresse. Aus einer lokalen Datei (`file://`) heraus geht das
nicht.

1. Auf GitHub ein **öffentliches** Repository anlegen (Pages ist im Gratis-Tarif
   nur für öffentliche Repos verfügbar).
2. Den Inhalt dieses Ordners hineinschieben:

   ```bash
   git init
   git add .
   git commit -m "Mones Mümmelküche"
   git branch -M main
   git remote add origin https://github.com/<name>/<repo>.git
   git push -u origin main
   ```

3. Im Repository unter **Settings → Pages** als Quelle `main` / `/ (root)`
   wählen.
4. Nach ein bis zwei Minuten ist die App unter
   `https://<name>.github.io/<repo>/` erreichbar.
5. Dort in Chrome öffnen → Menü → **Zum Startbildschirm hinzufügen**.

Alle Pfade im Code sind relativ (`./`), die App läuft deshalb auch im
Unterordner.

> **Das Repository ist öffentlich lesbar.** Der Code steht darin — die Rezepte
> nicht. Die liegen ausschließlich auf dem Gerät.

---

## Wo die Rezepte liegen

In der **IndexedDB des Browsers**, also auf dem Gerät. Kein Konto, keine Cloud,
kein Server. Das hat zwei Folgen:

- **Handy und Tablet haben getrennte Bestände.** Wer beide nutzt, tauscht über
  *Sicherung speichern* / *Sicherung laden* aus.
- **Die Sicherung ist die einzige Sicherung.** Wird der Browser-Speicher
  gelöscht („Browserdaten löschen", App deinstallieren), sind die Rezepte weg.
  Die App bittet Android zwar, den Speicher zu schützen (`storage.persist()`),
  aber verlassen sollte man sich darauf nicht.

*Sicherung speichern* schreibt eine JSON-Datei mit allen Rezepten samt Bildern.
*Sicherung laden* fügt sie wieder ein: Bekanntes wird aufgefrischt, Neues
kommt dazu — es geht nichts verloren.

---

## Was nach außen geht

Die App kommt fast ohne Netz aus. Nur an zwei Stellen nicht:

- **Link-Import.** Der Browser darf fremde Seiten aus Sicherheitsgründen nicht
  direkt lesen. Die Adresse geht deshalb über einen Weiterleitungsdienst
  (`cors.lol`, ersatzweise `allorigins.win`, `codetabs.com`). Dorthin geht
  ausschließlich die Rezept-Adresse. Ohne Netz funktioniert dieser Weg nicht.
- **Sonst nichts.** Die Texterkennung liegt vollständig im Ordner `ocr/` und
  läuft auf dem Gerät. Foto-Import, Texteinfügen und alles Übrige funktionieren
  offline.

---

## Aufbau

| Datei                  | Inhalt                                                     |
| ---------------------- | ---------------------------------------------------------- |
| `index.html`           | Gerüst aller vier Ansichten                                 |
| `styles.css`           | Tischdecken-Design; ab 820 px feste Leiste, darunter Menü   |
| `app.js`               | Kategorien, Mengenrechner, Speicher, Foto- und Link-Import  |
| `sw.js`                | Service Worker für Offline-Betrieb                          |
| `manifest.webmanifest` | App-Angaben, Icons, Teilen-Ziel                             |
| `ocr/`                 | Tesseract 7 + deutsche Sprachdatei (~10 MB)                 |
| `icons/`               | App-Icons                                                   |

### Beim Ändern zu beachten

**Die Zahl in `sw.js` (`const CACHE`) hochzählen.** Sonst behalten bereits
installierte Geräte die alten Dateien.

Der Ordner `ocr/` ist ein unverändertes Fremdpaket
([Tesseract.js](https://github.com/naptha/tesseract.js), Apache 2.0) und wird
absichtlich nicht beim Einrichten vorgeladen — 10 MB wären zu viel. Er wandert
beim ersten Foto-Import in den Cache und ist danach offline verfügbar.

---

## Die Grenzen, ehrlich gesagt

- **Die Texterkennung verhört sich.** Sie arbeitet ohne KI, rein auf Mustern.
  Bei sauberen Vorlagen liest sie fast fehlerfrei, aber `1 l` wird gern zu `11`,
  und bei krummen Fotos, Schnörkelschrift oder Text auf Bildern wird es
  ungenau. Deshalb kommt nach jedem Foto-Import das Prüfformular — es ist
  Bestandteil des Ablaufs, kein Notbehelf. **Vor allem die Mengen prüfen.**
- **Der Link-Import ist die wackeligste Stelle.** Nicht wegen der Rezeptseiten
  — die geprüften (Chefkoch, EAT SMARTER) geben ihre Rezepte sauber heraus und
  werden exakt übernommen. Sondern wegen der Weiterleitungsdienste: Der Browser
  darf fremde Seiten nicht selbst lesen, also muss ein fremder Dienst dazwischen,
  und die sind kostenlos, launisch und sterblich. Beim Prüfen am 17.07.2026 war
  von vier bekannten Diensten genau einer erreichbar, und der drosselte nach
  wenigen Anfragen. Für ein paar Rezepte pro Woche reicht das meist — aber wenn
  es klemmt, ist das der Grund. **Deshalb gibt es „Rezepttext einfügen":
  gleiches Ergebnis, ohne fremde Dienste.** Die Liste steht in `app.js` unter
  `BOTEN` und lässt sich austauschen.
- **Die Kategorie ist geraten.** Nach Schlagwörtern im Titel, hilfsweise in den
  Zutaten. „Kürbissuppe" trifft sie, bei „Mones Sonntagsessen" landet sie bei
  *Sonstiges*. Ändern ist ein Fingertipp.
