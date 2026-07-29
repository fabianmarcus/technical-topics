# Vokabeln

Technische Vokabeln, die mir beim Recherchieren zu den Themen begegnet sind und unklar waren. Ich habe sie hier gesammelt. Die Erläuterungen sind nicht immer vollständig oder präzise korrekt, aber sie helfen mir, die Begriffe zu verstehen und einzuordnen.

| Verzeichnis |
| --- |
| [Backpropagation](#backpropagation) |
| [Beam/Greedy Search](#beamgreedy-search) |
| [Chunk](#chunk) |
| [Deterministisch, nicht-deterministisch](#deterministisch-nicht-deterministisch) |
| [Embedding-Vektor](#embedding-vektor) |
| [Embedding-Matrix](#embedding-matrix) |
| [Fine-Tuning](#fine-tuning) |
| [Gewicht, Gewichtung](#gewicht-gewichtung) |
| [Inferenz (Vorhersage)](#inferenz-vorhersage) |
| [Optimizer (Trainingsprogramm)](#optimizer-trainingsprogramm) |
| [Prompting Template](#prompting-template) |
| [Sampling](#sampling) |
| [Semantik, semantisch](#semantik-semantisch) |
| [Token](#token) |
| [Token-ID](#token-id) |
| [Transformer](#transformer) |

---

## Backpropagation

## Beam/Greedy Search

## Chunk

Antwort von Copilot:

Ein Chunk ist ein kleiner, zusammenhängender Textabschnitt eines großen Dokuments.

**Beispiel:** Ein 1000-Seiten-Dokument wird nicht als Ganzes verarbeitet, sondern z. B. in Abschnitte von 300 bis 1000 Tokens zerlegt. Jeder dieser Abschnitte ist ein Chunk.

**Warum man das macht:**

- Das Kontextfenster ist begrenzt.
- Suche wird präziser, weil man gezielt passende Teilstellen findet.
- Verarbeitung wird schneller und günstiger.

**Typische Eigenschaften eines Chunks:**

- Feste oder halb-feste Größe (z. B. 500 Tokens).
- Oft mit Overlap zum nächsten Chunk (z. B. 50 bis 100 Tokens), damit beim Schnitt keine wichtigen Infos verloren gehen.
- Metadaten wie Quelle, Seitenzahl, Kapitel, Überschrift.
Mini-Beispiel:

Chunk 1: Seite 1 bis 2, Tokens 1-500
Chunk 2: Seite 2 bis 3, Tokens 450-950
Chunk 3: Seite 3 bis 4, Tokens 900-1400

So kann das System später genau die Chunks in den Prompt holen, die semantisch zur Frage passen.

**Typische Strategien sind:**

- Größenregel: Text wird auf z. B. 300 bis 1000 Tokens pro Chunk geschnitten, damit Chunks ins Kontextbudget passen.
- Sprachliche Grenzen: Wenn möglich, wird an Satz-, Absatz- oder Überschriften-Grenzen getrennt statt mitten im Satz.
- Struktur des Dokuments: Bei PDFs/Docs nutzt man oft Kapitel, Unterkapitel, Listen, Tabellenblöcke oder Seitenblöcke als natürliche Grenzen.
- Overlap: Chunks überlappen sich bewusst etwas (z. B. 10 bis 20 %), damit Informationen am Rand nicht verloren gehen.
- Nachgelagerte Qualitätskontrolle: Viele Pipelines prüfen anschließend, ob Chunks „zu heterogen“ oder „zu kurz“ sind, und mergen/splitten sie nach.
- Semantisches Chunking (fortgeschritten): Statt nur nach Länge trennt man dort, wo sich das Thema ändert. Das passiert z. B. über Embeddings oder Ähnlichkeits-Schwellen.

**Wichtig: Es gibt kein universell perfektes Chunking.** Die beste Methode hängt von Dokumenttyp und Use Case ab:

- Gesetzestext: eher strukturtreu (Paragraphen)
- Handbuch: Kapitel/Abschnitte
- Chat-Logs: zeitliche oder thematische Blöcke
- Scans/OCR: oft kürzere Chunks wegen Fehlerrauschen

## Deterministisch, nicht-deterministisch

## Embedding-Vektor

## Embedding-Matrix

## Fine-Tuning

## Gewicht, Gewichtung

## Inferenz (Vorhersage)

## Optimizer (Trainingsprogramm)

## Prompting Template

## Sampling

Sampling ist ein Teilbereich der Vorhersage (Inferenz) und wird in der Praxis bei LLMs eingesetzt, um die Kreativität bei der Vorhersage des Modells zu steuern. Sampling bedeutet konkret, durch eine bestimmte Methode (siehe folgende) einen Ausschnitt aus der Menge aller Tokens zu bestimmen. Aus diesem Ausschnitt kann anschließend das nächste Token per Zufall (Weighted Random Sampling) ausgewählt bzw. vorhergesagt werden.

### Top-K

Top-K ist eine von mehreren Sampling-Methoden, die bei der Vorhersage des nächsten Tokens verwendet werden.

Bei Top-K werden nur die K wahrscheinlichsten Token für die Vorhersage behalten, alle anderen werden verworfen. Wenn K auf 20 Token gesetzt wird, sind bei dieser Methode bis zu 20 Token zur Auswahl möglich. Aus diesen verbleibenden Token wird dann das nächste Token ausgewählt.

### Top-P

Top-P ist eine von mehreren Sampling-Methoden, die bei der Vorhersage des nächsten Tokens verwendet werden.

Bei Top-P werden die Token zunächst nach Wahrscheinlichkeit absteigend sortiert. Danach werden die Wahrscheinlichkeiten solange addiert, bis ein vorgegebener Schwellenwert P erreicht oder überschritten wird. Alle Token, die in dieser Addition (Kumulation) enthalten sind, werden behalten, alle anderen verworfen.

Beispiel: Nehmen wir an, P ist auf eine Wahrscheinlichkeit von 0,9 (also 90%) gesetzt. Die ersten sortierten Token haben Wahrscheinlichkeiten von 0,4, 0,3, 0,15, 0,1 und 0,05. Die ersten drei Token werden behalten (0,4 + 0,3 + 0,15 = 0,85), das vierte Token wird ebenfalls noch behalten (0,85 + 0,1 = 0,95 > 0,9). Das fünfte Token wird verworfen. Aus den verbleibenden vier Token wird dann das nächste Token ausgewählt.

### Temperature

Die Temperature ist eine von mehreren Sampling-Methoden, die bei der Vorhersage des nächsten Tokens verwendet werden.

Der Wert steuert die Kreativität des Modells bei der Vorhersage eines Tokens, indem er die Wahrscheinlichkeiten zwischen den Token aufbläst oder abflacht. Bei einem Wert von 1 werden die Wahrscheinlichkeiten belassen, wie sie sind. Bei einem Wert kleiner als 1 sinkt die Kreativität (es werden eher die wahrscheinlichsten Token ausgewählt), bei einem Wert größer als 1 steigt die Kreativität (es werden auch unwahrscheinlichere Token ausgewählt).

- Bei einer Temperatur von 0 wird für ein Prompt quasi immer die gleiche sichere Antwort generiert.
- Bei einer Temperatur über 1.5 kommt es zu starken Halluzinationen.

#### Ein anschauliches Beispiel für Temperature (aus Gemini)

Die KI soll den Satz vervollständigen: *"Der Hund bellt den..."*

| Wort | Basis-Chance (Temp = 1.0) | Niedrige Temp (0.2) | Hohe Temp (1.5) |
| --- | ---: | ---: | ---: |
| Postboten | 60 % | 98 % | 35 % |
| Baum | 25 % | 1,9 % | 25 % |
| Staubsauger | 14 % | 0,1 % | 22 % |
| Mond | 1 % | 0,0 % | 18 % |

- Bei Temp = 0.2 wird die KI mit an Sicherheit grenzender Wahrscheinlichkeit „Postboten“ wählen.
- Bei Temp = 1.5 hat der „Mond“ oder der „Staubsauger“ plötzlich eine fast ebenso große Chance, gewählt zu werden. Der Satz wird unerwarteter.

Wann nutzt man was?

- Niedrige Werte (0.0 - 0.3): Programmcode schreiben, mathematische Aufgaben lösen, Daten extrahieren, Zusammenfassungen von Fakten.
- Mittlere Werte (0.7 - 0.9): Allgemeine E-Mails schreiben, Chatbots für den Kundenservice, Blogbeiträge verfassen.
- Hohe Werte (1.1 - 1.4): Brainstorming für Marketing-Slogans, Gedichte schreiben, kreatives Storytelling.

## Semantik, semantisch

## Token

## Token-ID

## Transformer
