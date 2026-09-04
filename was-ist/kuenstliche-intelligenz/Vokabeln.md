<!-- markdownlint-disable MD033 -->

# Vokabeln

Technische Vokabeln, die mir beim Recherchieren zu den Themen begegnet sind und unklar waren. Ich habe sie hier gesammelt. Die Erläuterungen sind nicht immer vollständig oder präzise korrekt, aber sie helfen mir, die Begriffe zu verstehen und einzuordnen.

| Verzeichnis | Unterpunkte |
| --- | --- |
| [Backpropagation](#backpropagation) | |
| [Beam/Greedy Search](#beam--greedy-suche) | |
| [Chunk](#chunk) | |
| [Deterministisch](#deterministisch-nicht-deterministisch) | |
| [Embedding-Vektor](#embedding-vektor) | |
| [Embedding-Matrix](#embedding-matrix) | |
| [Fine-Tuning](#fine-tuning) | |
| [Gewicht, Gewichtung](#gewicht-gewichtung) | |
| [Inferenz (Vorhersage)](#inferenz-vorhersage) | |
| [Optimizer (Trainingsprogramm)](#optimizer-trainingsprogramm) | |
| [Prompting Template](#prompt-template) | |
| [Sampling](#sampling) | [Top-K](#top-k), [Top-P](#top-p), [Temperature](#temperature) |
| [Semantik, semantisch](#semantik-semantisch) | |
| [Token](#token) | [Tokenisierung](#tokenisierung), [Tokenizer](#tokenizer), [Token-ID](#token-id), [Beispiel](#beispiel) |
| [Transformer](#transformer) | |

---

## Backpropagation

<details>
<summary>Backpropagation ist der Trainingsschritt, mit dem ein neuronales Netz aus Vorhersagefehlern lernt.</summary>

Backpropagation ist ein wichtiger Teilprozess des Trainings eines neuronalen Netzes. Der dabei angewandte Algorithmus ist entscheidend dafür, wie gut das neuronale Netz am Ende trainiert ist. Es ist das Verfahren, das dafür sorgt, dass beispielsweise ein LLM bei der Generierung einer Antwort plausible Token-Vorhersagen trifft.

Einordnung in den Trainingsprozess am Beispiel eines LLMs:

1. **Vorwärtsdurchlauf (Forward Pass):** Das LLM verarbeitet die Eingabe ([Prompt](./Prompt.md)) und generiert eine Vorhersage für das nächste Token.
2. **Fehlerberechnung (Loss Calculation):** Es wird berechnet, wie falsch die Vorhersage des Modells war, indem sie mit dem tatsächlichen nächsten Token verglichen wird.
3. **Rückwärtsdurchlauf (Backward Pass / Backpropagation):** Der Fehler wird (durch das Netz) zurückverfolgt. Dabei wird für jedes Gewicht berechnet, wie viel Schuld es an dem Fehler trägt. Das ist der Kern der Backpropagation.
4. **Gewichtsaktualisierung (Weight Update):** Die Gewichte werden auf Basis der Ergebnisse der Backpropagation angepasst, um den Fehler zu minimieren. Dies geschieht in der Regel mit einem Optimierungsalgorithmus (z. B. Adam, SGD).

Backpropagation ist also der Trainingsschritt eines LLM bzw. neuronalen Netzes, der Fehler in den Beziehungen von Information aufdeckt, um sie anschließend korrigieren lassen zu können.

Bildlich gesprochen: Alle Spiele einer Fußballmannschaft werden auf Video aufgenommen. Anschließend schaut sich ein Taktik-Experte (Backpropagation) die Spiele an und analysiert, durch welche Fehler von welchen Spielern es zum gegnerischen Tor (falsche Vorhersage) kommen konnte. Der Trainer (Optimierer) überlegt sich anschließend Übungen für das nächste Training (Trainingsdurchlauf), damit die Spieler (Gewichte) diese Fehler in Zukunft vermeiden (korrigierte Repräsentation im Vektorraum).

</details>

## Beam & Greedy Suche

<details>
<summary>Beam Search und Greedy Search sind deterministische Verfahren zur Auswahl des nächsten Tokens.</summary>

Beam und Greedy sind zwei Suchverfahren, die bei der [Vorhersage (Inferenz)](./Vokabeln.md#inferenz-vorhersage) zur Auswahl des nächsten Tokens angewendet werden.

Im Gegensatz zum [Sampling](./Vokabeln.md#sampling), das Kollege Zufall zu Rate zieht, werden sowohl bei der Beam- als auch bei der Greedy-Suche Token mit der höchsten Wahrscheinlichkeit ausgewählt. Sie arbeiten also [deterministisch](./Vokabeln.md#deterministisch-nicht-deterministisch), bei gleichen Eingaben liefern sie immer das gleiche Ergebnis.

Der Unterschied zwischen Beam und Greedy liegt in der Anzahl laufender Vorhersage-Pfade.

### Greedy Search

Die Greedy Suche (deutsch: gierige Suche) wählt als nächstes Token einfach immer das Token aus, das vom Forward Pass die höchste Wahrscheinlichkeit bekommen hat und hängt es an die aktuelle Tokensequenz. Die anderen Token mit hoher Wahrscheinlichkeit werden verworfen. Es gibt also genau eine Lösung.

Greedy Search arbeitet dadurch sehr effizient und ressourcenschonend, liefert aber unzuverlässig gute Ergebnisse, da durch das Verwerfen der anderen Token keine Korrektur mehr möglich ist. Die laufende Vorhersage kann dadurch leicht in einer [Sackgasse](./Vokabeln.md#das-sackgassen-problem) enden.

#### Das Sackgassen-Problem

Das "Sackgassen-Problem" beschreibt die Situation, dass die Greedy Suche das wahrscheinlichste Token auswählt, die Wahrscheinlichkeiten der darauf folgenden Token aber plötzlich einbrechen. Es folgen für die momentan zusammengestellte Tokensequenz keine wirklich plausiblen Token mehr. Die Vorhersage hat sich quasi verhaspelt. Sie kann aber auch nicht mehr zurück, da der Algorithmus nur einen Vorhersage-Pfad vorsieht, andere berechnete Wahrscheinlichkeiten wurden verworfen. Es geht also einfach weiter mit der Auswahl, selbst mit sehr unwahrscheinlichen Token. Das kann zu mehreren Ergebnissen führen:

- **Endlosschleifen:** Der Algorithmus berechnet immer wieder die gleiche Abfolge von Token (*Ich bin ein Bibabutzemann..., Ich bin ein Bibabutzemann..., usw.*). Da die hinzugefügten Token Teil des Kontextes werden, werden sie unter Umständen auch immer wieder als wahrscheinlichste Token ausgewählt.
- **Wirrwarr:** Da die Tokens immer unwahrscheinlicher werden, entsteht eine Abfolge nicht zusammenpassender Token, die grammatikalisch nur noch Kauderwelsch ergeben. (*Ich bin ein Bibabutzemann, Wald grün hat geklettert oben irgendwas...*)
- **Halluzinationen:** Vielleicht passen die Tokens grammatikalisch noch zusammen, machen inhaltlich aber keinen Sinn mehr. (*Ich bin ein Bibabutzemann, der auf dem Mond tanzt und mit einem Staubsauger singt. Deshalb ist die Erde eine Scheibe.*)

### Beam Search

Die Beam Suche (deutsch: Strahlensuche) verfolgt im Vergleich zur Greedy Suche mehrere Vorhersage-Pfade.

Das funktioniert ungefähr so:

- Ein Start-Token wird ausgewählt.
- Die wahrscheinlichsten nächsten Tokens werden berechnet.
- Für jedes dieser Tokens wird eine neue Sequenz gebildet (bestehende Sequenz + neues Token).
- Für alle nun bestehenden Sequenzen wird ein Score berechnet.
- Die K bestbewerteten Sequenzen (nach Score) werden behalten, der Rest verworfen (geprunt).
- Der Prozess wiederholt sich ab Punkt 2, bis ein End-Token generiert wird oder die maximale Länge erreicht ist.

Es laufen also mehrere Vorhersage-Pfade parallel, wobei der, der am Ende die Antwort bilden soll, von Durchlauf zu Durchlauf wechseln kann. Dadurch ist die Beam Suche wesentlich robuster als die Greedy Suche, arbeitet aber auch langsamer und benötigt mehr Rechenleistung und Speicher. Das Sackgassen-Problem kann bei der Beam Suche zwar auch auftreten, aber die Wahrscheinlichkeit ist wesentlich geringer, da mehrere Vorhersage-Pfade parallel laufen. Wenn ein Pfad in einer Sackgasse endet, können die anderen Pfade trotzdem noch plausibel weiterlaufen.

- [Bildliche Veranschaulichung - I](https://towardsdatascience.com/wp-content/uploads/2021/04/1tEjhWqUgjX37VnT7gJN-4g-768x449.png)
- [Bildliche Veranschaulichung - II](https://www.researchgate.net/profile/Johannes-Rieke-2/publication/374031557/figure/fig2/AS:11431281189909429@1695211392614/Beam-search-Aus-moeglichen-Token-Sequenzen-wird-die-wahrscheinlichste-Sequenz-ausgewaehlt.png)

</details>

## Chunk

<details>
<summary>Ein Chunk ist ein kleiner, zusammenhängender Textabschnitt eines größeren Dokuments.</summary>

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

**Wichtig:** Es gibt kein universell perfektes Chunking. Die beste Methode hängt von Dokumenttyp und Use Case ab:

- Gesetzestext: eher strukturtreu (Paragraphen)
- Handbuch: Kapitel/Abschnitte
- Chat-Logs: zeitliche oder thematische Blöcke
- Scans/OCR: oft kürzere Chunks wegen Fehlerrauschen

</details>

## Deterministisch, nicht-deterministisch

<details>
<summary>Deterministische Prozesse liefern bei gleicher Eingabe immer dasselbe Ergebnis, nicht-deterministische Prozesse nicht.</summary>

- Deterministisch bedeutet, dass ein Prozess bei gleichen Eingaben immer das gleiche Ergebnis liefert.
- Nicht-deterministisch bedeutet, dass ein Prozess bei gleichen Eingaben unterschiedliche Ergebnisse liefern kann.

Da ein LLM durch [Sampling](#sampling) explizit unterschiedliche Ergebnisse für dieselbe Eingabe ([Prompt](./Prompt.md)) liefern soll, ist es grundsätzlich nicht-deterministisch. Entfällt das [Sampling](#sampling), ist es deterministisch; mehrere Anfragen mit demselben [Prompt](./Prompt.md) würden immer die gleiche Antwort liefern.

</details>

## Embedding-Vektor

<details>
<summary>Ein Embedding-Vektor ist eine numerische Repräsentation von Text in einem hochdimensionalen Raum.</summary>

"Vektor", "Embedding" und "Embedding-Vektor" werden im KI-Umfeld oft synonym verwendet.

Text wird zu Tokens, Tokens werden zu Vektoren (siehe [LLM](./LLM.md)). Die Vektoren liegen in einem hochdimensionalen Vektorraum. X- und Y-Achse beschreiben einen 2-dimensionalen Raum. Fügt man die Z-Achse hinzu, hat man einen dreidimensionalen Raum. Embedding-Vektoren im KI-Umfeld liegen in einem hochdimensionalen Raum mit hunderten oder tausenden Dimensionen. Das ist bildlich nicht darstellbar.

Über diese Dimensionen setzen sie die erzeugten Tokens bzw. ihre IDs miteinander in Beziehung. Vereinfacht gesagt, je ähnlicher sich zwei Texte sind, desto näher liegen ihre Vektoren im Vektorraum beieinander. Je unähnlicher sie sind, desto weiter entfernt liegen ihre Vektoren voneinander. Das stimmt nicht immer, aber im Grundsatz.

So eine Vektorrepräsentation sieht z. B. so aus:

```text
Kaffee: [0.12, 0.45, 0.78, 0.33, 0.91, 0.02, 0.67, 0.88, 0.21, 0.55, ...]
Tee: [0.11, 0.44, 0.79, 0.32, 0.90, 0.01, 0.66, 0.87, 0.20, 0.54, ...]
Milch: [0.05, 0.20, 0.60, 0.15, 0.80, 0.03, 0.65, 0.85, 0.18, 0.50, ...]
Wasser: [0.02, 0.10, 0.50, 0.05, 0.70, 0.01, 0.60, 0.80, 0.15, 0.45, ...]
```

Jede Zahl in den eckigen Klammern ist eine Dimension des Vektors. Die Länge des Vektors (Anzahl der Dimensionen) hängt vom verwendeten Embedding-Modell ab. Diese Vektoren können miteinander verrechnet werden, um den Abstand zwischen ihnen bzw. die semantische Ähnlichkeit zwischen den Texten zu bestimmen.

</details>

## Embedding-Matrix

<details>
<summary>Eine Embedding-Matrix ordnet jedem Token des Vokabulars einen Embedding-Vektor zu.</summary>

Eine Embedding-Matrix ist die Anordnung der [Embedding-Vektoren](#embedding-vektor) in einer Tabelle - ähnlich einer Tabelle in einer relationalen Datenbank. Jeder Vektor stellt ein Token dar. Jedes Token ist eine Zeile in der Tabelle. Die Zahlenwerte des Vektors (seine Dimensionen) sind die Spalten. Der Begriff Embedding-Matrix beschreibt also einfach eine Darstellungsform der Menge aller Embedding-Vektoren. Sie repräsentiert das gesamte Vokabular eines Modells in Form einer Tabelle.

</details>

## Fine-Tuning

<details>
<summary>Fine-Tuning ist das Nachtrainieren eines bereits trainierten Modells für einen speziellen Zweck.</summary>

Beim Fine-Tuning (zu deutsch: *Feinabstimmung*) wird ein bereits trainiertes LLM mit zusätzlichen, meist spezifischen Daten nachtrainiert, um es auf einen bestimmten Anwendungsbereich zu optimieren, anstatt ein LLM von grundauf neu zu trainieren. Das spart Zeit, Rechenleistung und dadurch Geld. Dabei kann sowohl das Wissen erweitert, als auch das Verhalten des LLM angepasst werden. Dennoch ist Fine-Tuning wesentlich aufwendiger und teurer als RAG. Deshalb wird Fine-Tuning heutzutage eher in Anwendungsbereichen eingesetzt, die viel Wissen und/oder sehr spezifische Anforderungen haben, wie z. B. in der Medizin, im Recht oder in der Finanzbranche; für die meisten Anwendungsfälle ist RAG ausreichend. Fine-Tuning wird auch oft in Kombination mit RAG eingesetzt, um die Vorteile beider Ansätze zu nutzen. Nichtsdestotrotz wird auch der Fine-Tuning-Ansatz immer weiterentwickelt, um ihn einfacher, effizienter und kostengünstiger zu machen. Nennbare Cloud-Produkte sind: OpenAI Fine-Tuning API, Azure AI Foundry, AWS Bedrock, Google Vertex AI.

Ein umfassender Artikel von IBM dazu: [Fine-Tuning](https://www.ibm.com/de-de/think/topics/fine-tuning)

Ich selbst habe noch nicht gefinetuned und werde mich auch erst einmal nicht mit diesem Bereich beschäftigen.

### Fine-Tuning vs. RAG

Der Unterschied zwischen [RAG](./RAG.md) und Fine-Tuning ist, dass beim Fine-Tuning das LLM selbst erweitert oder zumindest angepasst wird, während bei RAG zusätzliche Informationen nur temporär in den Anfrage-Kontext eingespeist werden, ohne das LLM selbst zu verändern. Das LLM "lernt" also beim Fine-Tuning, während es bei RAG für genau eine Antwort kurzfristig zusätzliche Informationen zugeschustert bekommt.

</details>

## Gewicht, Gewichtung

<details>
<summary>Gewichte sind gelernte Zahlenwerte, mit denen ein neuronales Netz Eingabevektoren verrechnet.</summary>

Ein Gewicht ist ein einzelner Zahlenwert (Skalar) innerhalb eines Gewichtsvektors. Auch ein Gewichtsvektor besteht, wie der Embedding-Vektor, aus sehr vielen Zahlenwerten. Im Training von LLMs sind es diese Gewichtsvektoren bzw. ihre Gewichte, die angepasst werden, um die [Vorhersage](./Vokabeln.md#inferenz-vorhersage) des Modells zu verbessern. Gewichtsvektoren sind nicht zu verwechseln mit [Embedding-Vektoren](#embedding-vektor). Embedding-Vektoren sind die konkreten Repräsentationen von Tokens im Vektorraum, die mit den Gewichtsvektoren während einer Antwort verrechnet werden, um zu bestimmen, wie wichtig eine Token-Repräsentation für die Vorhersage des nächsten Tokens ist. Gewichte haben keine textliche Repräsentation; sie sind eher numerische Richtungspfeile durch das neuronale Netz. Sie prüfen, wie sehr sich ein Embedding-Vektor (Token) für eine bestimmte Behauptung (Vorhersage) triggern lässt. Je mehr er anschlägt, desto mehr lohnt es sich, mit ihm weiterzumachen.

### Beispiel

```text
Embedding-Vektor (Eingabe): e = [0.2, 0.8, -0.1]
Gewichtsvektor (eine „Richtung“ im Netz): w = [0.5, -0.3, 0.9]
Bias: b = 0.4
```

Verrechnung mit dem Gewichtsvektor und dem Bias, um die Vorhersage des nächsten Tokens zu bestimmen:

```text
z = e * w + b
z = (0.2 * 0.5) + (0.8 * -0.3) + (-0.1 * 0.9) + 0.4
z = 0.1 - 0.24 - 0.09 + 0.4 = 0.17
```

Ergebnis: *z = 0.17*. Dieser Wert wird anschließend durch eine Aktivierungsfunktion (z. B. Softmax) geschickt, um die prozentuale Wahrscheinlichkeit für das nächste Token zu bestimmen.

Das Ergebnis hat für sich alleine kaum Bedeutung. Wird der derselbe Embedding-Vektor aber mit weiteren Gewichtsvektoren verrechnet, können alle Ergebnisse miteinander verglichen werden und daraus die besten Wahrscheinlichkeiten für die nächsten Token bestimmt werden. *0.17* lässt aber bereits erahnen, dass dieser Embedding-Vektor in dieser Richtung (Gewichtsvektor) nicht die beste Wahl für die Vorhersage des nächsten Tokens ist.

Hierbei wird auch klar, wie aufwendig der ganze Prozess ist, da ein LLM hunderte Millionen bis mehrere Milliarden Gewichte hat, die alle pro Token des momentanen Kontextes verrechnet werden müssen, um die Vorhersage des nächsten Tokens zu bestimmen. Es handelt sich zwar um einfache mathematische Operationen, aber die Masse an Berechnungen ist enorm.

### Metapher

Man kann es sich vorstellen, als wenn man mit einem Taxi durch die Galaxis fliegt. Um dort anzukommen, wo man hin möchte, wird das Taxi vor der Fahrt auf die Gravitationseigenschaften der Sterne der Galaxis eingestellt. Danach muss das Taxi nicht mehr viel machen. Bestimmte Sterne ziehen das Taxi aufgrund seiner Einstellung automatisch auf die richtige Bahn, während andere Sterne das Taxi abstoßen. So bounced es von Stern zu Stern durch das Universum, bis der Taxifahrer anhält oder die maximale Fahrtzeit erreicht ist.

Die Metapher hinkt hier und da. Das ist aber nicht schlimm, denn sie verdeutlicht trotzdem gut:

- wie man sich ein neuronales Netz vorstellen muss: nicht nur links und rechts, sondern auch oben und unten, vorne und hinten, mehrdimensional in alle Richtungen.
- dass die Gewichte vorgegeben sind: so wie die bereits existierenden Sterne das Taxi anziehen und das Taxi somit nicht selbst fährt, werden Embedding-Vektoren mit bereits trainierten Gewichten verrechnet, um die Vorhersage des nächsten Tokens zu bestimmen.
- dass es kein definiertes Ziel gibt: das Taxi fährt nicht zu einem bestimmten Zielstern, sondern wird auf die Gravitationseigenschaften der Galaxis eingestellt und fährt so lange, bis der Taxifahrer anhält. Das Ziel eines LLMs ist die Vorhersage des nächsten Tokens, das sich aus den Gewichten und den Embedding-Vektoren ergibt - also kein vorher feststehender Endsatz. So wie die Sterne das Taxi auf der gewünschten Bahn halten, sorgen die Gewichte dafür, dass die Vorhersage des nächsten Tokens plausibel ist.
- dass die Eingabe (Prompt) die Fahrt beeinflusst: werden dem Taxi unpassende Gravitationswerte eingestellt, fliegt es wahrscheinlich völlig verkehrt durch die Galaxis. Genauso kann ein LLM durch einen unverständlichen Prompt zu einer völlig unpassenden Vorhersage des nächsten Tokens kommen. Ein LLM, das nur auf Englisch trainiert wurde, kann z. B. keine plausiblen Vorhersagen für einen deutschen Prompt liefern.

</details>

## Inferenz (Vorhersage)

<details>
<summary>Inferenz ist die laufende Berechnung des nächsten Tokens während der Antwortgenerierung.</summary>

Inferenz, auch Vorhersage, ist die Berechnung des nä chsten Tokens einer Antwort auf Basis des Kontextes und der bisher vorhergesagten Tokens. Der Prozess läuft dabei in mehreren Schritten streng sequenziell ab:

1. Das Modell analyisiert die zur Verfügung stehenden Daten und berechnet daraus einen "rohen Punktwert" (Logits) für jedes Token im Vokabular, das als nächstes Token kommen könnte.
2. Alle Logits werden anschließend durch eine Softmax-Funktion in konkrete Wahrscheinlichkeiten (0% - 100%) umgewandelt.
3. Auf Basis dieser Menge von Wahrscheinlichkeiten wird das nächste Token ausgewählt. Entweder per [Sampling](./Vokabeln.md#sampling) oder per [Suche](./Vokabeln.md#beamgreedy-search).
4. Das Token wird an die bisherige Sequenz angehängt und der Prozess wiederholt sich, bis das Modell ein End-Token generiert oder die maximale Länge erreicht ist.

- **Such-Verfahren** nutzt man für faktenbasierte Aufgaben (Programmierung, Übersetzung, mathematische Fragen).
- **Sampling-Verfahren** nutzt man für kreative Aufgaben (Geschichten schreiben, Brainstorming, Chatbots).

Hier etwas ausführlicher: [Funktionsweise eines LLMs](./LLM.md)

</details>

## Optimizer (Trainingsprogramm)

<details>
<summary>Ein Optimizer aktualisiert im Training die Gewichte des Modells, um Vorhersagefehler zu verringern.</summary>

Der Optimizer übernimmt die Gewichtsaktualisierung nach einer falschen Vorhersage. Dafür ist ihm ein bestimmter Algorithmus (in Form einer Rechenformel) hinterlegt. Es gibt verschiedene Algorithmen, die sich in Art und Weise unterscheiden, wie sie die Gewichte anpassen. Alle haben unterschiedliche Schwerpunkte mit Stärken und Schwächen. **(Mehr zu den Algorithmen weiß ich hier auch nicht.)**

Die Gewichtsaktualisierung erfolgt in der Regel nach der [Backpropagation](#backpropagation) und arbeitet mit ihren Ergebnissen. Der Optimizer ist damit quasi der letzte Schritt einer Trainingsiteration.

Unter [Backpropagation](#backpropagation) und in [LLM](./LLM.md) ist der Trainingsprozess näher beschrieben.

</details>

## Prompt Template

<details>
<summary>Ein Prompt Template ist eine vorbereitete Prompt-Struktur mit Platzhaltern für konkrete Werte.</summary>

Ein Prompt Template legt einen bestimmten Aufbau des Prompts sowie seinen Text bereits größtenteils fest. In dem vorfomulierten Text sind Platzhalter enthalten, die vor dem Absenden des Prompts bzw. vor Übergabe an das LLM durch konkrete Werte ersetzt werden.

Ein Prompt muss also nicht immer eine Freitexteingabe eines Benutzers sein. Der Benutzer kann zum Beispiel auch über Dropdowns, Checkboxen oder andere Eingabefelder Werte festlegen, die anschließend die Platzerhalter im Template ersetzen.

Nehmen wir an, in einem Spiel gibt es ein Formular zum Anlegen eines neuen Spielers. Darin kann er die Eigenschaften des Spielers auswählen: Geschlecht, Beruf, Größe, Gewicht, Haarfarbe, Augenfarbe und Superkraft.

Das Prompt Template könnte dann so aussehen:

```text
Prompt Template:
"Erstelle einen Charakter für ein Real Life Gesellschaftsrollenspiel. Der Charakter ist ein/e {{Geschlecht}} vom Verhaltenstyp {{Verhaltenstyp}}, sein Beruf ist {{Beruf}}.

Sein Aussehen hat folgende Eigenschaften:

- Name: {{Name}}
- Größe: {{Größe}}
- Gewicht: {{Gewicht}}
- Haarfarbe: {{Haarfarbe}}
- Augenfarbe: {{Augenfarbe}}
- Superkraft: {{Superkraft}}

Füge dem Charakter eine Hintergrundgeschichte hinzu, die zu den gewählten Eigenschaften passt. Beschreibe ihn in 3-4 Absätzen. Zudem sollen einige seiner Eigenschaften und sein Verhalten in gewissen Zügen dem Charakter {{Klon}} ähneln. 

Gib als Antwort ein JSON-Objekt zurück, das die genannten Eigenschaften und die Hintergrundgeschichte des Charakters enthält."
```

Die Platzhalter {{Platzhalter}} werden vor dem Absenden des Prompts durch die vom Benutzer gewählten Werte programmatisch ersetzt (z.B. mit *text.replace(...)*). Der Benutzer muss den Prompt also nicht selbst formulieren, sondern kann über die Eingabefelder die Werte festlegen. Das Template sorgt dafür, dass der Prompt immer in der richtigen Form an das LLM übergeben wird.

</details>

## Sampling

<details>
<summary>Sampling steuert, aus welchem Ausschnitt wahrscheinlicher Tokens das nächste Token ausgewählt wird.</summary>

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

</details>

## Semantik, semantisch

<details>
<summary>Semantik beschreibt im KI-Kontext die inhaltliche Bedeutung und Ähnlichkeit von Informationen.</summary>

Semantik ist das, worum sich das Thema der künstlichen Intelligenz hauptsächlich dreht. Bislang konnten Computer Informationen nur auf Übereinstimmung von Zeichenketten (Strings) prüfen; entweder ist die gesuchte Zeichenkette ein Teil des Textes oder nicht. Semantik bedeutet hier, dass Computer Informationen zueinander in Beziehung setzen können. Sie können sie semantisch gruppieren. Dadurch wird es möglich, Informationen auch auf Ähnlichkeit bzw. Zugehörigkeit prüfen zu können (z.B. Zimmer, Fenster, Tür), statt nur auf exakte Übereinstimmung.

</details>

## Token

<details>
<summary>Ein Token ist die kleinste Texteinheit, mit der ein LLM intern arbeitet.</summary>

Ein Modell versteht keine Wörter, sondern Tokens. Das Token ist die kleinste Einheit, die ein LLM versteht. Ein Token kann ein Wort, ein Teil eines Wortes oder auch nur ein einzelnes Zeichen sein. Es wird mit Tokens gearbeitet, weil sie als Vokabular wesentlich wiederverwendbarer sind und im Vektorraum effizienter abgebildet werden können als ganze Wörter. Die Menge aller zur Verfügung stehenden Tokens bildet das Vokabular des Modells.

### Tokenisierung

Tokenisierung ist das Zerlegen von Text in einzelne Teile bzw. Tokens.

### Tokenizer

Die Tokenisierung erfolgt durch den sogenannten *Tokenizer*. Der Tokenizer ist eine eigene Software und muss auch trainiert oder konfiguriert werden, damit er aus den Trainingsdaten ein immer gleiches Vokabular ableiten kann. Dazu wird ihm ein individueller Algorithmus hinterlegt. Das Vokabular für einen Textdatensatz ist nur pro Tokenizer immer gleich. Unterschiedlich eingestellte Tokenizer ergeben unterschiedliche Vokabulare. Das Training des Tokenizers erfolgt vor dem Training des LLMs auf Basis der Trainingsdaten.

### Token-ID

Da Tokens immer noch aus Textzeichen bestehen, für die mathematischen Verrechnungen der Vektoren aber Zahlen benötigt werden, arbeitet das Modell tatsächlich gar nicht mit den Tokens selbst, sondern mit ihren IDs. Jedes Token bekommt vom Tokenizer eine eindeutige numerische ID (meistens einfach hochgezählt), die das Token im Vokabular des Modells repräsentiert. Für alle weiteren Vektorberechnungen wird nur noch diese ID herangezogen.

### Beispiele für Tokenisierung

```text
Beispiel: Das Wort "Tokenisierung" könnte vom Algo des Tokenizers zerlegt werden in:
- "Token"
- "isier"
- "ung"

Beispiel: "Der Türrahmen ist aus Holz.":
- "Der"
- "Tür"
- "rahmen"
- "ist"
- "aus"
- "Holz"
- "."
```

</details>

## Transformer

<details>
<summary>Ein Transformer ist die Modellarchitektur, die Token-Kontext verarbeitet und daraus Vorhersagen ableitet.</summary>

Ganz grundsätzlich transformiert ein Transformer die Eingabe in eine Ausgabe.

Ein Transformer ist die Kernarchitektur bzw. die Kernfunktionalität innerhalb eines Modells. Die meisten heutigen LLMs basieren auf der Transformer-Architektur. Sie umfasst den mathematisch-rechnerischen Teil der Vorhersage. Dazu zählen:

- Berechnung der Embedding-Vektoren der Tokens im Kontext des Prompts.
- Ergänzung von Positionsinformationen, damit die Reihenfolge der Tokens berücksichtigt wird.
- Berechnung der Aufmerksamkeit (Attention) zwischen den Tokens.
- Berechnung der Wahrscheinlichkeiten (Logits) für das nächste Token (zumindest im Decoder-LLM-Kontext).

Vereinfacht: Ein Transformer ist ein sehr leistungsfähiger Kontext-Auswerter für Tokens. Genau deshalb eignet er sich so gut für Textgenerierung, Übersetzung, Zusammenfassung und viele andere Sprachaufgaben.

Zur besseren Abgrenzung: Das ganze Drumherum, wie z. B. Tokenisierung, Sampling, Vorhersage-Training (Loss, Backpropagation, Optimizer), RAG oder Chunking gehören nicht zur Transformer-Architektur, sondern sind zusätzliche Funktionalitäten, die ein LLM erst zu einem kompletten System machen.

### Decoder-only Transformer (GPT-Stil)

Es gibt verschiedene Architekturstile, eine Eingabe zu transformieren.

Die Decoder-only-Architektur ist der Stil, der bei GPT-Modellen verwendet wird. Viele heutige Chat-LLMs arbeiten ebenfalls decoder-only oder decoder-dominant. Exakte Architekturdetails werden je nach Anbieter aber oft nur teilweise veröffentlicht.

Bei dieser Variante wird die Eingabe Schritt für Schritt verarbeitet. Das Modell sagt immer nur das nächste Token voraus, basierend auf dem bisherigen Kontext. Dadurch ist sie besonders gut für die Textgenerierung geeignet. "Decoder-only" bezeichnet also die in [LLM](./LLM.md) beschriebene autoregressive Generationsweise: nächstes Token aus vorherigem Kontext.

</details>
