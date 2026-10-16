# King County: Was bestimmt den Verkaufspreis? Eine EDA für Charles Christensen

Explorative Datenanalyse (EDA) der Hausverkäufe in King County (Seattle und Umgebung) von Mai 2014 bis Mai 2015. Projekt im Data-Science-Bootcamp von neuefische, Woche 4.

## Kunde und Fragestellung

**Charles Christensen** ist Verkäufer und Investor. Er will hohe Renditen und fragt:

- Lohnt sich eine Renovierung vor dem Verkauf?
- Welche Lage zahlt sich aus?
- Wann verkauft man am besten?

## Daten

- **21.597 Verkäufe** von 21.420 Häusern, 02.05.2014 bis 27.05.2015, King County (Washington, USA)
- Quelle: Kurs-Datenbank, Schema `eda`, zwei Tabellen (Hausdetails und Verkäufe), verbunden über `house_id = id`
- Spaltenbeschreibung: [column_names.md](column_names.md)
- Die Rohdaten liegen **nicht** im Repository. [03_fetching_the_data_eda.ipynb](03_fetching_the_data_eda.ipynb) lädt sie aus der Datenbank nach `data/king_county.csv` (siehe Setup).

## Hypothesen und Ergebnisse

| # | Hypothese | Ergebnis |
|---|---|---|
| H1 | Renovierte Häuser verkaufen sich besser als unrenovierte. | **Teilweise bestätigt, als Zusammenhang.** Roh +24 % beim Preis pro m². Bei vergleichbarer Größe, Lage, Alter, Wasserlage und Verkaufsmonat bleiben rund **+6 %** beim Gesamtpreis (95-%-Intervall 4 bis 8 %). Keine Rendite, da Renovierungskosten fehlen. |
| H2 | Je näher am Zentrum von Seattle, desto höher der Preis pro m². | **Bestätigt als enger Zusammenhang.** Bis 5 km vom Zentrum rund 3.980 $/m², über 30 km rund 1.800 $/m². Teuer sind das Zentrum, der Norden Seattles und das Ostufer des Lake Washington (Bellevue, Medina, Mercer Island). Lage ist dabei mehr als Entfernung: Auch die PLZ zählt. |
| H3 | Die Verkaufspreise im Frühjahr sind höher als im Herbst. | **Nur beschreibend.** Roh +4 %, aber bei gleicher Zusammensetzung der Verkäufe sind die Preise bis Januar 2015 nahezu flach und steigen ab Februar 2015. Saison und allgemeiner Preisanstieg lassen sich mit einem Jahr Daten nicht trennen. |

**Vertiefung zur Lage (Infrastruktur):** Fluglärm am Flughafen Seattle-Tacoma, Nähe zu den Boeing-Werken und Nähe zur Stadtbahn Central Link (Stand 2015). Bei allen drei Merkmalen gibt es sichtbare Rohpreisunterschiede, zum Beispiel -16 % in der Fluglärmzone gegenüber dem direkten Umfeld. Nach Berücksichtigung von Hausmerkmalen und Lage werden sie deutlich kleiner oder hängen stark vom Modell ab. Ein eigenständiger Effekt lässt sich mit diesen Daten nicht robust nachweisen.

## Erkenntnisse

1. **Die Lage prägt den Preis pro m² am stärksten (geografisch).** Zentrum, Norden und Ostufer sind teuer, der Süden ist günstiger. Zwischen den PLZ liegt bis zum Faktor 3,9.
2. **Renovierung hängt mit höheren Preisen zusammen**, aber weit weniger als im rohen Vergleich: rund +6 % bei vergleichbaren Häusern.
3. **Einzelne Infrastrukturmerkmale lassen sich schwer isoliert nachweisen.** Fluglärm, Bahn- und Werksnähe zeigen keine einfachen, pauschalen Preiseffekte.

## Empfehlungen für Charles

1. **Lagevergleich priorisieren:** Das Haus vor allem mit ähnlichen Häusern im direkten Umfeld vergleichen.
2. **Renovierung wirtschaftlich prüfen:** Vor der Entscheidung Kosten, Umfang und mögliche Preiswirkung realistisch kalkulieren. Die Daten zeigen einen Zusammenhang, keine garantierte Rendite.
3. **Keine pauschalen Auf- oder Abschläge ansetzen:** Bei Lärmzone, Werksnähe oder Bahnanbindung immer lokale Vergleichsverkäufe heranziehen.

## Präsentation

Folien (10 Minuten, für Nicht-Techniker): [praesentation/EDA_Charles_Christensen.pdf](praesentation/EDA_Charles_Christensen.pdf)

## Aufbau des Repositorys

| Datei / Ordner | Inhalt |
|---|---|
| [04_eda_reviewed.ipynb](04_eda_reviewed.ipynb) | **Hauptanalyse.** Datenqualität, Bereinigung, H1 bis H3, Erkenntnisse und Empfehlungen |
| [05_infrastruktur_hypothesen.ipynb](05_infrastruktur_hypothesen.ipynb) | Vertiefung zur Lage: Fluglärm (Hauptteil), Boeing, Central Link |
| [04_eda.ipynb](04_eda.ipynb) | Erste Fassung der Analyse, unverändert zum Nachvollziehen |
| [review_vergleich.md](review_vergleich.md) | Was sich zwischen erster und überarbeiteter Fassung geändert hat und warum |
| [03_fetching_the_data_eda.ipynb](03_fetching_the_data_eda.ipynb) | Daten aus der Kurs-Datenbank laden |
| [bilder/](bilder/) | Diagramme und Karten. `bilder/review/` gehört zu 04_eda_reviewed, `bilder/infrastruktur/` zu 05 |
| [infrastruktur_daten/](infrastruktur_daten/) | Lärmkonturen, Boeing-Standorte, Stationsliste mit Quellenangaben |
| [01_assignment.md](01_assignment.md), [02_workflow.md](02_workflow.md) | Aufgabenstellung und empfohlener Ablauf |

## Vorgehen und Einsatz von KI

Die Analyse ist mit KI-Unterstützung entstanden, die Verantwortung für Inhalte und Schlussfolgerungen liegt beim Autor.

1. **Erste Analyse** (`04_eda.ipynb`) mit Claude Code.
2. **Kritisches Review** der Methodik durch ChatGPT/Codex.
3. **Gegenprüfung an den Daten** und Überarbeitung mit Claude Code (`04_eda_reviewed.ipynb`, `review_vergleich.md`). Jeder Review-Punkt wurde an den Daten geprüft, nicht ungeprüft übernommen. Dabei wurden echte Fehler gefunden und korrigiert (zum Beispiel 6 Renovierungen nach dem Verkaufsdatum, die mitgezählt wurden), mehrere Aussagen wurden präziser eingeschränkt.

Die Zusammenarbeit war nacheinander, kein unabhängiger Blindvergleich.

## Grenzen

- Nur ein Jahr Daten. Saisoneffekte und allgemeine Preisentwicklung sind nicht trennbar.
- Keine Kaufpreise, Renovierungskosten oder Inseratsdaten, daher keine Aussagen über Rendite oder Ursachen.
- Alle Ergebnisse sind Zusammenhänge in Beobachtungsdaten, keine kausalen Effekte.
- Die Fluglärmzonen sind aus einer Karte von 2013 angenähert, nicht amtlich.

## Setup

Voraussetzungen: [uv](https://docs.astral.sh/uv/), Zugang zur Kurs-Datenbank.

```bash
git clone git@github.com:karstenuhlighh/king-county-eda.git
cd king-county-eda
uv sync
cp .env.example .env   # Zugangsdaten der Kurs-Datenbank eintragen, .env nie committen
```

Dann in VS Code aus dem Projektordner öffnen, als Kernel die `.venv` wählen und die Notebooks in dieser Reihenfolge ausführen:

1. `03_fetching_the_data_eda.ipynb` (erzeugt `data/king_county.csv`)
2. `04_eda_reviewed.ipynb`
3. `05_infrastruktur_hypothesen.ipynb`

Python 3.13, wichtigste Pakete: pandas, numpy, matplotlib, seaborn, plotly, statsmodels, shapely, pyproj.

## Quellen

- King County House Sales: [Kaggle](https://www.kaggle.com/datasets/harlfoxem/housesalesprediction), bereitgestellt über die Kurs-Datenbank
- Lärmkarte: Seattle-Tacoma International Airport, Noise Exposure Map 2013
- Stationen: Sound Transit 2015. Koordinaten © OpenStreetMap contributors, ODbL
- Kartenhintergrund der interaktiven Karte: © CARTO, © OpenStreetMap contributors
