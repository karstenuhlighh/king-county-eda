# Review-Vergleich: `04_eda.ipynb` → `04_eda_reviewed.ipynb`

Kunde: Charles Christensen (renditeorientierter Verkäufer). Datensatz: King County House Sales, 21.597 Verkäufe, 02.05.2014 bis 27.05.2015.

## Rollen

| Rolle | Wer |
|---|---|
| Ausgangsnotebook `04_eda.ipynb` | Karsten Uhlig, erstellt mit Unterstützung von Claude Code (in dieser Arbeitssitzung) |
| Review | ChatGPT/Codex, auf Basis von Notebook-Code, gespeicherten Ausgaben und Diagrammen, ohne Zugriff auf die CSV |
| Gegenprüfung an den Daten und Überarbeitung | Claude Code, mit dem Review als Input (sequenziell, kein unabhängiger Blindvergleich) |
| Fachliche Bewertung und Verantwortung | Karsten Uhlig |

## Vorgehen und Reproduzierbarkeit

- Das Original `04_eda.ipynb` ist unverändert (SHA-256 vor und nach der Arbeit identisch: `3d6e9cf9…5c7e85`).
- Das Original wurde als temporäre Kopie ausgeführt: Alle 26 Codezellen liefern dieselben Textausgaben wie gespeichert. Die Originalergebnisse sind damit reproduziert.
- `04_eda_reviewed.ipynb` läuft mit frischem Kernel von oben nach unten fehlerfrei durch (40 Codezellen, ca. 10 Sekunden).
- Versionen: pandas 3.0.3, numpy 2.4.6, matplotlib 3.10.9, seaborn 0.13.2, statsmodels 0.15.0 (neu hinzugefügt), plotly 6.8.0. Python 3.13.
- Bootstrap-Verfahren mit festem Seed 42, gezogen werden ganze Immobilien (Mehrfachverkäufe bleiben zusammen).
- Neue Diagramme liegen in `bilder/review/`, die Diagramme des Originals zum Vergleich dort als `original_*.png`. Das Original schreibt weiter nach `bilder/`.

Zellnummern im Original: Index ab 0 in `04_eda.ipynb`, Markdown- und Codezellen mitgezählt. Abschnitte im neuen Notebook: Überschriftennummern.

## Übersicht

| # | Thema | Kategorie | Status |
|---|---|---|---|
| A1 | Renovierung nach dem Verkauf in der 10-Jahres-Gruppe | 1 Code-/Datenfehler | bestätigt |
| A2 | Renovierungsjahr wird bei erneutem Ausführen nochmals geteilt | 1 Code-/Datenfehler | bestätigt |
| A3 | Falsche Erklärung für wenige Verkäufe im Mai 2015 | 1 Code-/Datenfehler | bestätigt |
| A4 | Beschriftung „Häuser mit mehreren Verkäufen: 177“ | 1 Code-/Datenfehler | bestätigt (eigener Befund) |
| A5 | Nord-Süd-Vergleich ohne sichtbare Berechnung | 1 Code-/Datenfehler | teilweise bestätigt |
| B1 | Größe und Lage nur getrennt berücksichtigt | 2 Methodik | bestätigt |
| B2 | Preis pro m² gleicht Größe nicht aus | 2 Methodik | bestätigt |
| B3 | Weitere Gruppenunterschiede (Alter, Wasser, Qualität, Zeitpunkt) | 2 Methodik | bestätigt |
| B4 | Mindestfallzahl nur für die renovierte Gruppe | 2 Methodik | bestätigt |
| B5 | Median der PLZ-Aufschläge gewichtet jede PLZ gleich | 2 Methodik | bestätigt |
| B6 | 47 und 24 PLZ nicht direkt vergleichbar | 2 Methodik | bestätigt |
| B7 | Keine Unsicherheit, keine Empfindlichkeitsprüfung | 2 Methodik | bestätigt |
| B8 | Abhängigkeit von Mehrfachverkäufen | 2 Methodik | bestätigt |
| B9 | Ausschluss „unbekannt“ kann H1 verzerren | 2 Methodik | teilweise bestätigt |
| B10 | 33-Zimmer-Verkauf vollständig gelöscht | 2 Methodik | bestätigt |
| B11 | H3: Saison, Trend und Zusammensetzung vermischt | 2 Methodik | bestätigt |
| B12 | Keine Analysen zu Größe, Qualität, Bädern, Baujahr | 2 Methodik | bestätigt |
| C1 | Renovierungsbudget 13.500 bis 31.500 $ | 3 Interpretation | bestätigt |
| C2 | Hoher Preis pro m² als Rendite gelesen | 3 Interpretation | bestätigt |
| C3 | „Lage ist der stärkste Preisfaktor“ | 3 Interpretation | teilweise bestätigt |
| C4 | PLZ-Faktor mit Renovierungsaufschlag verglichen | 3 Interpretation | bestätigt |
| C5 | Spearman -0,62 als „deutlicher Zusammenhang“ | 3 Interpretation | teilweise bestätigt |
| C6 | „Innerhalb 10 Jahren“ = „frisch / kurz vor dem Verkauf“ | 3 Interpretation | bestätigt |
| C7 | Winter als schlechteste Verkaufszeit, „wenigste Käufer“, Inseratsmonat | 3 Interpretation | bestätigt |
| C8 | Mai 2015 gegen Mai 2014 als Marktanstieg | 3 Interpretation | teilweise bestätigt |
| D1–D5 | Diagramme | 4 Darstellung | bestätigt bzw. umgesetzt |
| N1–N4 | Duplikate, Plausibilität, Reproduzierbarkeit, Kategorien | – | nicht bestätigt (kein Problem gefunden) |

---

## 1. Tatsächliche Code- oder Datenfehler

### A1 Renovierung nach dem Verkauf in der 10-Jahres-Gruppe
- **Fundstelle:** Abschnitt 3.2, Zelle 16, `(df.yr_renovated > 0) & (jahre_seit_renovierung <= 10)`
- **Original:** Renoviert in den letzten 10 Jahren, wenn Verkaufsjahr minus Renovierungsjahr ≤ 10.
- **Review:** Negative Abstände werden mitgezählt.
- **Prüfung:** Bestätigt. 6 Verkäufe aus 2014 haben das Renovierungsjahr 2015. Weitere 68 Verkäufe fallen ins Renovierungsjahr, dort ist die Reihenfolge unbekannt.
- **Umsetzung:** Abstand 0 bis 10 Jahre gilt als „ja“, negative Abstände als `unplausibel` (aus allen Vergleichen ausgeschlossen). 68 Fälle im selben Jahr bleiben in „ja“, mit Hinweis (neu 4.2, 4.3).
- **Ergebnis:** 283 → **277** Verkäufe. PLZ-Vergleich (Mindestzahl 5): Original 24 PLZ / 6,8 % (nur „ja“ geprüft) → neu 22 PLZ / 7,4 % (beide Gruppen geprüft).
- **Konsequenz:** Gering für die Zahl, aber die Definition ist jetzt korrekt.

### A2 Renovierungsjahr wird bei erneutem Ausführen nochmals geteilt
- **Fundstelle:** Abschnitt 3.1, Zelle 14, `df["yr_renovated"] = df["yr_renovated"] / 10`
- **Original:** Überschreibt die Rohspalte.
- **Review:** Erneutes Ausführen teilt nochmals durch 10. Format nur am Bereich geprüft.
- **Prüfung:** Bestätigt. Alle 744 positiven Werte sind Vielfache von 10, die Umrechnung ergibt ganze Jahre 1934 bis 2015. Keine Renovierung vor dem Baujahr.
- **Umsetzung:** Neue Spalte `renovierungsjahr` aus der unveränderten Rohspalte, Rohdaten in `df_roh` (neu 3, 4.1).
- **Ergebnis:** Zahlen unverändert, Zelle jetzt wiederholbar.
- **Konsequenz:** Keine für den Kunden.

### A3 Falsche Erklärung für wenige Verkäufe im Mai 2015
- **Fundstelle:** H3, Zelle 57, `print("Hinweis: Mai 2014 beginnt am 02.05., Mai 2015 endet am 27.05. …")`
- **Original:** Der niedrige Balken im Mai 2015 sei ein unvollständiger Monat bis 27.05.
- **Review:** Das maximale Datum erklärt die geringe Zahl nicht automatisch.
- **Prüfung:** Bestätigt. Bis 14.05.2015 gibt es werktäglich Verkäufe, danach nur noch je einen am 15., 24. und 27.05. Wochen: 419 (bis 10.05.), 132 (bis 17.05.), 1, 1. Die Reihe bricht also Mitte Mai ab. Die Ursache ist aus den Daten nicht erkennbar.
- **Umsetzung:** Tages- und Wochenzählung, Diagramm `h3_abdeckung.png`, Text unterscheidet beobachtete Abdeckung von vermuteter Ursache (neu 8.1).
- **Ergebnis:** 646 Verkäufe im Mai 2015 unverändert, Erklärung korrigiert.
- **Konsequenz:** „Frühjahr“ ist kürzer beobachtet als „Herbst“.

### A4 Beschriftung „Häuser mit mehreren Verkäufen: 177“ (eigener Befund)
- **Fundstelle:** Abschnitt 3.4, Zelle 20, `print("Häuser mit mehreren Verkäufen:", df.id.duplicated().sum())`
- **Original:** 177 als Zahl der Häuser.
- **Review:** Häuser, Transaktionen und zusätzliche Verkäufe unterscheiden.
- **Prüfung:** Bestätigt. `duplicated()` zählt zusätzliche Verkäufe, nicht Häuser. Richtig: 176 Immobilien, 353 Transaktionen, 177 zusätzliche Verkäufe, höchstens 3 je Immobilie.
- **Umsetzung:** Alle drei Zahlen getrennt ausgewiesen (neu 4.5).
- **Konsequenz:** Keine für den Kunden.

### A5 Nord-Süd-Vergleich ohne sichtbare Berechnung
- **Fundstelle:** H2, Zelle 50 (Markdown), „im Norden im Median rund 2.900 $, im Süden rund 2.390 $“
- **Original:** Zahl im Text, ohne Code im Notebook.
- **Review:** Reproduzieren oder entfernen.
- **Prüfung:** Teilweise bestätigt. Die Berechnung fehlte im Notebook, die Zahl ist aber korrekt. Mit 10 bis 20 km Luftlinie und „Norden = nördlich der Breite von Downtown (47,6062)“: 2.900 $ (n = 4.349) gegen 2.394 $ (n = 3.412).
- **Umsetzung:** Berechnung mit Definition ergänzt, Grenzen der groben Einteilung benannt (neu 7.4).
- **Ergebnis:** unverändert, jetzt nachvollziehbar.

## 2. Methodische Einschränkungen

### B1 Größe und Lage nur getrennt berücksichtigt
- **Fundstelle:** H1, Zellen 31 (Größenklassen) und 34 (`aufschlag_je_plz`)
- **Original:** Größenklassen (Aufschlag 9 bis 38 %) und PLZ-Vergleich (3,4 %) getrennt.
- **Review:** Größe und Lage gleichzeitig berücksichtigen, überschaubares Modell.
- **Prüfung:** Bestätigt. Lineares Modell für den logarithmierten Gesamtpreis, schrittweise erweitert, Standardfehler nach Immobilie gruppiert.
- **Umsetzung:** Neu 6.3, Diagramm `h1_modelle.png`. Die Größenklassen-Tabelle entfällt, weil das Modell sie ersetzt.
- **Ergebnis:**

| Modell | Unterschied | 95-%-Intervall |
|---|---|---|
| nur Renovierung | +36,1 % | 30,1 bis 42,5 % |
| + Wohnfläche | +24,4 % | 20,6 bis 28,4 % |
| + PLZ | +8,5 % | 6,2 bis 10,8 % |
| + Alter, Monat | +8,4 % | 6,0 bis 10,8 % |
| **+ Wasserlage (Hauptmodell)** | **+6,2 %** | **4,1 bis 8,4 %** |
| + grade, condition (Prüfung) | +7,5 % | 5,6 bis 9,5 % |

- **Konsequenz:** Der Zusammenhang ist robuster und etwas größer als der PLZ-Median des Originals (3,4 %). Er bleibt ein Zusammenhang, keine Rendite.

### B2 Preis pro m² gleicht Größe nicht aus
- **Fundstelle:** Abschnitt Annahmen und H1, Zelle 4 und 26
- **Prüfung:** Bestätigt. Spearman Wohnfläche zu Preis pro m²: -0,18. Renovierte Häuser sind größer (Median 204 gegen 177 m²). Der m²-Vergleich benachteiligt sie leicht. Das erklärt, warum der PLZ-Median (2,6 bis 3,4 %) unter dem Modell (6,2 %) liegt.
- **Umsetzung:** Hinweis in den Annahmen, Diagramm `groesse_preis.png`, Modell auf den Gesamtpreis mit Wohnfläche (neu 2, 5, 6.3).

### B3 Weitere Gruppenunterschiede
- **Fundstelle:** H1, Zelle 27
- **Prüfung:** Bestätigt. Renoviert gegen nicht renoviert: Baujahr 1942 gegen 1977, Entfernung 10,4 gegen 16,8 km, `grade` 8 gegen 7, **Wasserlage 4,4 % gegen 0,6 %** (eigener zusätzlicher Befund).
- **Umsetzung:** Alter, Monat und Wasserlage im Hauptmodell, fehlende Wasserlage als eigene Kategorie. `grade` und `condition` nur als Prüfung, weil sie auch Folge einer Renovierung sein können (neu 6.1, 6.3).

### B4 Mindestfallzahl nur für die renovierte Gruppe
- **Fundstelle:** H1, Zelle 34, `t = t[t["size"]["ja"] >= mindestens]`
- **Prüfung:** Bestätigt.
- **Umsetzung:** Mindestzahl für beide Gruppen, Standard 10 (Median aus 5 Werten zu instabil, ab 20 entfallen zu viele PLZ), 5 und 20 als Prüfung (neu 6.2).
- **Ergebnis:** jemals renoviert: Mindestzahl 5 → 47 PLZ / 3,4 %, 10 → **29 PLZ / 2,6 %**, 20 → 14 PLZ / 2,8 %. Bei 20 bleibt für die 10-Jahres-Definition keine PLZ übrig.

### B5 Median der PLZ-Aufschläge gewichtet jede PLZ gleich
- **Fundstelle:** H1, Zelle 35 (Markdown)
- **Prüfung:** Bestätigt. Er beschreibt die typische PLZ, keinen allgemeinen bereinigten Effekt.
- **Umsetzung:** Erläuterung in 6.2, Modell als eigentlicher Maßstab.

### B6 47 und 24 PLZ nicht direkt vergleichbar
- **Fundstelle:** H1, Zellen 35 und 38
- **Prüfung:** Bestätigt.
- **Umsetzung:** Vergleich beider Definitionen auf gemeinsamen PLZ (neu 6.2).
- **Ergebnis:** Mindestzahl 5: 22 gemeinsame PLZ, 3,2 % gegen 7,4 %. Mindestzahl 10: 11 PLZ, 6,2 % gegen 7,2 %.

### B7 Keine Unsicherheit, keine Empfindlichkeitsprüfung
- **Prüfung:** Bestätigt.
- **Umsetzung:** Bootstrap-Intervall für den PLZ-Median, Intervalle im Modell, Mindestzahlen 5/10/20, Renovierungsfenster 5/10/15/20 Jahre. Varianten vorab festgelegt, nicht nach Ergebnis gewählt.
- **Ergebnis:** PLZ-Median 2,6 %, **95-%-Intervall -0,4 bis 6,4 %** (schließt 0 ein), positiv in 17 von 29 PLZ. Fenster gegenüber nie renovierten Häusern: 5 J. +12,5 % (158 Verkäufe), 10 J. +11,6 % (277), 15 J. +11,3 % (385), 20 J. +11,0 % (449), Intervalle überlappen stark.
- **Konsequenz:** Der PLZ-Vergleich allein trägt keine Aussage über die Größe des Aufschlags.

### B8 Abhängigkeit von Mehrfachverkäufen
- **Prüfung:** Bestätigt (176 Immobilien mit 353 Transaktionen).
- **Umsetzung:** Gruppierte Standardfehler nach Immobilie, Bootstrap über Immobilien. Mehrfachverkäufe bleiben im Datensatz.

### B9 Ausschluss „unbekannt“ kann H1 verzerren
- **Fundstelle:** Abschnitt 3.2, Zelle 15 (Markdown)
- **Prüfung:** Teilweise bestätigt. Das Risiko ist richtig benannt, in diesen Daten aber gering. Mit und ohne Renovierungsjahr: Median-Preis jeweils 450.000 $, Preis pro m² 2.632 gegen 2.639 $, `grade` 7 gegen 7. Anteil fehlend je Monat 14,9 bis 19,2 %. Im Modell liegt „unbekannt“ bei +0,4 % (-0,4 bis +1,1 %) gegenüber nie renoviert, „ja“ bleibt bei +6,1 %.
- **Umsetzung:** Prüfung in 4.7, „unbekannt“ als eigene Gruppe im Modell (6.3).

### B10 33-Zimmer-Verkauf vollständig gelöscht
- **Fundstelle:** Abschnitt 3.3, Zellen 17 und 18, `df = df[df.bedrooms != 33]`
- **Original:** „vermutlich ein Tippfehler für 3“, Zeile gelöscht.
- **Prüfung:** Bestätigt. Übrige Angaben plausibel (1.620 sqft, 1,75 Bäder, Baujahr 1947, 640.000 $). Ob 3 gemeint war, ist nicht belegt. Die Schlafzimmerzahl wird nicht verwendet.
- **Umsetzung:** Zeile bleibt, Merkmal markiert (`schlafzimmer_verdaechtig`), keine Korrektur auf 3 (neu 4.4).
- **Ergebnis:** roher H1-Unterschied +24,2 % mit und ohne diesen Verkauf. 21.596 → 21.597 Zeilen.

### B11 H3: Saison, Trend und Zusammensetzung vermischt
- **Fundstelle:** H3, Zellen 56 bis 61
- **Original:** Frühjahr +4,3 % gegenüber Herbst; Hinweis auf Trend über Mai-Vergleich.
- **Prüfung:** Bestätigt. „Frühjahr“ = Mai 2014 und März bis Mitte Mai 2015. Monatsindex bei gleicher Zusammensetzung (Modell mit Wohnfläche, PLZ, Alter, Wasserlage, `grade`): Juni 2014 bis Januar 2015 +0,3 bis +2,0 % gegenüber Mai 2014, dann Februar +3,5 %, März +6,1 %, April +8,5 %, Mai 2015 +10,9 %.
- **Umsetzung:** Abdeckungstabelle je Jahreszeit, bereinigter Monatsindex, Diagramm `h3_monate.png` (neu 8.1 bis 8.3).
- **Konsequenz:** Der niedrige Winterwert (roh -4,6 % im Dezember) verschwindet bereinigt weitgehend (+1,2 %).

### B12 Keine Analysen zu Größe, Qualität, Bädern, Baujahr
- **Prüfung:** Bestätigt.
- **Umsetzung:** Pearson und Spearman für Gesamtpreis und Preis pro m², Erklärungsanteil (R²) einzelner Merkmale, Diagramme `h2_merkmale.png`, `groesse_preis.png`. `id`, `sale_id` und PLZ nicht als Zahlen korreliert (neu 7.2).
- **Ergebnis (Spearman):** Gesamtpreis: Wohnfläche 0,64, Qualität 0,66, Bäder 0,50, Baujahr 0,10, Entfernung -0,35. Preis pro m²: Entfernung -0,62, Baujahr -0,24, Wohnfläche -0,18.

## 3. Zu weit gehende Interpretationen

### C1 Renovierungsbudget 13.500 bis 31.500 $
- **Fundstelle:** Empfehlungen, Zelle 63, „etwa 13.500 bis 31.500 $“
- **Prüfung:** Bestätigt. Kosten, Umfang und der Preis desselben Hauses ohne Renovierung fehlen.
- **Umsetzung:** Budget entfernt. Empfehlung 2 verweist auf Kostenvoranschläge und Vergleichsverkäufe (neu 10).
- **Konsequenz:** Die Empfehlung wird vorsichtiger, nicht falscher.

### C2 Hoher Preis pro m² als Rendite gelesen
- **Fundstelle:** Ergebnis H2 und Empfehlung 1, Zellen 53 und 63, „Der größte Hebel für hohe Renditen ist die Wahl des Viertels“
- **Prüfung:** Bestätigt. Teure Lagen kosten auch im Einkauf mehr, Kaufpreise fehlen.
- **Umsetzung:** Empfehlung 1 spricht vom Preisniveau, Rendite hängt am Kaufpreis.

### C3 „Lage ist der stärkste Preisfaktor“
- **Fundstelle:** Ergebnis H2 und Erkenntnis 1, Zellen 53 und 62
- **Prüfung:** Teilweise bestätigt. Erklärungsanteil (R²) allein: Preis pro m² (log) PLZ 0,61, Entfernung 0,34, Qualität 0,03, Wohnfläche 0,03. Gesamtpreis (log): PLZ 0,53, Qualität 0,50, Wohnfläche 0,46, Entfernung 0,12. Hinweis: PLZ hat 70 Kategorien.
- **Umsetzung:** Aussage auf den Preis pro m² beschränkt, für den Gesamtpreis „einer von mehreren ähnlich starken“ (neu 7.2, 7.6, 9).

### C4 PLZ-Faktor mit Renovierungsaufschlag verglichen
- **Fundstelle:** Ergebnis H2, Zelle 53, „Im Vergleich zu H1“
- **Prüfung:** Bestätigt. Faktor 3,9 zwischen Extrem-PLZ und Aufschlag innerhalb von PLZ messen verschiedene Dinge.
- **Umsetzung:** Vergleich entfernt, Hinweis in 7.6.

### C5 Spearman -0,62
- **Fundstelle:** H2, Zelle 44 (Markdown)
- **Prüfung:** Teilweise bestätigt. Das Original nannte es „deutlicher Zusammenhang“, nicht Ursache. Präzisiert: Rangzusammenhang, kein Erklärungsanteil (der liegt für die Entfernung bei R² 0,34). Grenzen von Luftlinie und PLZ in den Annahmen.
- **Ergebnis:** -0,62 unverändert.

### C6 „Innerhalb 10 Jahren“ = „frisch renoviert / kurz vor dem Verkauf“
- **Fundstelle:** H1, Zellen 36 und 38, Empfehlung 2
- **Prüfung:** Bestätigt. „nein“ enthält nie und früher renovierte Häuser.
- **Umsetzung:** Begriffe ersetzt, Inhalt von „nein“ erklärt (neu 4.3).

### C7 Winter als schlechteste Verkaufszeit
- **Fundstelle:** Ergebnis H3 und Empfehlung 3, Zellen 61 und 63, „Der Winter ist die schlechteste Verkaufszeit … die wenigsten Käufer … Inserat im Februar vorbereiten“
- **Prüfung:** Bestätigt. Bereinigt ist der Winter kaum niedriger als Sommer und Herbst (Dezember +1,2 %, Januar +1,3 % gegenüber Mai 2014). Weniger Abschlüsse belegen nicht weniger Käufer. Verkaufsdatum ist nicht Inseratsdatum.
- **Umsetzung:** H3 als beschreibender Befund für den Zeitraum, Liste der fehlenden Daten (neu 8.4, 10).
- **Konsequenz:** Empfehlung 3 ändert sich inhaltlich: keine feste Jahreszeitenregel mehr.

### C8 Mai 2015 gegen Mai 2014 als Marktanstieg
- **Fundstelle:** H3, Zelle 60, `Mai 2015 gegenüber Mai 2014`
- **Prüfung:** Teilweise bestätigt. Zwei Monatsmediane sind kein Preisindex (Mai 2015 nur bis Mitte Mai). Der bereinigte Index bestätigt den Anstieg aber, sogar stärker: +10,9 % statt +5,3 %.
- **Ergebnis:** +5,3 % (roh) unverändert, ergänzt um +10,9 % (bereinigt, 95 % von 8,8 bis 12,9 %).

## 4. Darstellung

| # | Diagramm | Änderung |
|---|---|---|
| D1 | Renovierungs-Boxplot | Fallzahlen (n = 17.005 / 744) im Bild, Hinweis auf ausgeblendete Ausreißer und 3.848 unbekannte Fälle |
| D2 | Entfernungsdiagramm | Titel ohne „Stadtrand“, Achse „Luftlinie zu Downtown Seattle“. „Über 30 km“ umfasst Vororte und ländliche Gebiete |
| D3 | Karte | Farbskala begrenzt auf 1.500 bis 5.000 $/m² erklärt (7,6 % darunter, 5,5 % darüber), leere Waben = weniger als 3 Verkäufe |
| D4 | Monatsdiagramm | Titel auf Mai 2014 bis Mai 2015 beschränkt, roher und bereinigter Verlauf mit Intervall, Mai 2015 grau mit „nur bis Mitte Mai“ |
| D5 | Neu | Verteilungen, Größe-Preis, Modellvergleich H1, Merkmale H2, PLZ mit 95-%-Intervallen, Wochenabdeckung |

Vergleichbarkeit: Boxplot und Entfernungsdiagramm haben gleiche Achsen und Gruppen wie im Original. Das Monatsdiagramm zeigt jetzt den Unterschied zu Mai 2014 in % statt Dollar pro m², weil roher und bereinigter Verlauf sonst nicht auf eine Achse passen.

## Nicht bestätigte Review-Punkte und Prüfungen ohne Befund

| # | Prüfung | Ergebnis |
|---|---|---|
| N1 | Vollständige Duplikate, auch ohne `sale_id`; gleiche Immobilie, gleiches Datum, gleicher Preis | jeweils 0 |
| N2 | Preis und Wohnfläche positiv, übrige Flächen nicht negativ, oberirdisch + Keller = Wohnfläche | alles erfüllt (Keller bei 452 fehlend) |
| N3 | Kategorien `condition`, `grade`, `view`, `waterfront`, `floors` | alle im erwarteten Wertebereich |
| N4 | Baujahr gegenüber Verkaufsjahr | 12 Verkäufe vor dem Baujahr (Verkauf vor Fertigstellung, plausibel), markiert, Alter auf 0 gesetzt |

## Unveränderte Ergebnisse

| Ergebnis | Original | Neu |
|---|---|---|
| Roher Unterschied Preis pro m², renoviert | +24,2 % | +24,2 % |
| Spearman Entfernung zu Preis pro m² | -0,62 | -0,62 |
| Median bis 5 km / über 30 km | 3.977 / 1.805 $ | 3.977 / 1.805 $ |
| Faktor teuerste zu günstigster PLZ | 3,9 | 3,9 |
| Nord / Süd bei 10 bis 20 km | 2.900 / 2.390 $ | 2.900 / 2.394 $ |
| Frühjahr gegenüber Herbst (roh) | +4,3 % | +4,3 % |
| Median PLZ-Unterschiede Frühjahr/Herbst | +4,8 % | +4,8 % |
| Mai 2015 gegen Mai 2014 (roh) | +5,3 % | +5,3 % |

## Konsequenzen für die Empfehlungen an Charles

| Empfehlung | Original | Neu |
|---|---|---|
| 1 Lage | „Rendite über die Lage steuern“ | Lage als wichtigster Treiber des Preises pro m² einplanen, Rendite am Kaufpreis prüfen |
| 2 Renovierung | Renovieren, wenn Kosten unter 13.500 bis 31.500 $ | Möglicher Preisvorteil (+6 % im Modell), Entscheidung nur mit Kosten und Vergleichsverkäufen |
| 3 Timing | Nicht im Winter, Inserat im Februar, online im März | Keine feste Jahreszeitenregel; im Datenjahr stiegen die Preise ab Februar 2015 |

## Verbleibende Grenzen

- Ein Jahr Daten, dicht nur bis Mitte Mai 2015. Saison und Marktentwicklung lassen sich nicht trennen.
- Keine Kaufpreise, Kosten, Renovierungsumfänge, Inserats- oder Angebotsdaten: keine Rendite- und keine Ursachenaussagen.
- Das Modell ist eine Beschreibung bei gleichen erfassten Merkmalen. Nicht erfasste Unterschiede zwischen renovierten und nicht renovierten Häusern bleiben möglich.
- PLZ und Luftlinie bilden Lage nur grob ab.
