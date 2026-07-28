# Vokabeln

Technische Vokabeln, die mir beim Recherchieren zu den Themen begegnet sind und unklar waren. Ich habe sie hier gesammelt. Die Erläuterungen sind nicht immer vollständig oder präzise korrekt, aber sie helfen mir, die Begriffe zu verstehen und einzuordnen.

## Backpropagation

## Beam/Greedy Search

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
