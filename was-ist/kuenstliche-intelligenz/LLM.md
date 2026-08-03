# Large Language Models (LLM)

Die zentrale Grundlage der KI-Tools ist ein Large Language Model (LLM). Das LLM ist sozusagen das Gehirn der KI.

Wie entsteht so ein LLM und was ist drin?

## Trainingsdaten

Um das Gehirn mit Wissen zu füllen, haben unzählige Menschen und automatisierte Crawl-Prozesse natürliche Informationen strukturiert gesammelt, gesäubert, beschrieben und bewertet (ähnlich, wie Google es schon jeher für seine Suchmaschine macht). Dabei entsteht eine gigantische Textdatei (`.txt` oder `.json`), die oft mehrere Terabyte groß ist und Millionen von Textbausteinen enthält: **die sogenannten Trainingsdaten.** Diese Textdatei ist das Lehrbuch für das LLM, aber nicht das LLM selbst.

Die Inhalte stammen aus Online-Lexika (wie Wikipedia), Fachbüchern, Literaturbüchern, Foren, Blogs, Social-Media-Beiträgen, wissenschaftlichen Artikeln, Produktbeschreibungen, Software-Dokumentationen und vielem mehr. Dementsprechend unterschiedlich ist die Qualität der Daten. **Ein wichtiger Prozess vor dem Training ist daher auch die Datenaufbereitung**: Entfernung von Duplikaten, Spam, Werbung, toxischen Inhalten, persönlichen Daten, urheberrechtlich geschützten Inhalten und anderen unerwünschten Informationen. Außerdem werden die Daten inhaltlich bewertet und mit Metadaten versehen, um die Qualität der Trainingsdaten zu erhöhen.

Der Inhalt dieser Datei sieht in etwa so aus und beschreibt das Wort "Tür" in unterschiedlichen Kontexten:

```text
[...]

[QUELLE: WIKIPEDIA_DE_SAMPLE_4829]
Eine Tür ist ein bewegliches Bauelement zum Schließen von Öffnungen in Wänden. Sie besteht im Wesentlichen aus einer Zarge (Rahmen) und dem Türblatt. Zu den mechanischen Eigenschaften gehören die Einbruchhemmung und der Schallschutz.

[QUELLE: COMMON_CRAWL_FORUM_HANDWERK_9921]
Hallo zusammen, meine Zimmertür aus Holz schleift seit gestern auf dem Parkett. Die Scharniere sehen eigentlich fest aus. Hat jemand einen Tipp, wie ich die Tür höher einstellen kann oder muss ich sie unten abschleifen? Danke!

[QUELLE: ARCHITEKTUR_FACHBUCH_KAP_3]
Der Raumabschluss durch Türen erfordert im gewerblichen Bauwesen spezifische Brandschutzklassen (z. B. T30 für feuerhemmende Türen). Als Materialien kommen meist Stahl, Aluminium, massives Holz oder Verbundstoffe zum Einsatz.

[QUELLE: LITERATUR_PROJEKT_GUTENBERG_771]
Sie stand unschlüssig vor der schweren Eichentür. Das Schloss war alt und verrostet. Als sie den Schlüssel drehte, quietschte die Tür lautstark und gab den Blick in den dunklen Korridor frei.

[...]
```

**Nach dem Training ist diese Datei für die Arbeit (Inferenz) des LLM nicht mehr relevant.** Es wird mit Tokens, Vektoren und dem Vokabelheft gearbeitet, nicht mit den ursprünglichen Textdaten.

## Die Verarbeitung der Trainingsdaten

Der weitere Workflow geht ungefähr so:

- Tokenizer einstellen: der Algorithmus wird definiert, der die Trainingsdaten in Tokens zerlegt.
- Vokabular anlegen: der Tokenizer leitet aus den Trainingsdaten ein festes, numeriertes Token-Vokabular ab.
- Vektorisierung: die Token-IDs werden in Vektoren umgewandelt, um sie mehrdimensional vergleichen zu können.
- Training: die Vektoren werden dazu genutzt, das neuronale Netz zu trainieren, sodass das LLM die Zusammenhänge zwischen den Vektoren erkennt und daraus neue Informationen ableiten kann.

Was bedeuten diese ganzen Wörter...

### Tokens, Tokenisierung

**Tokenisierung** ist das Zerlegen von Text in einzelne Teile.  
**Ein Token** kann ein Wort, nur ein Teil eines Wortes oder auch nur ein Satzzeichen sein.

***Tokenizer:*** Die Tokenisierung erfolgt durch den sogenannten *Tokenizer*. Der Tokenizer selbst muss auch trainiert oder konfiguriert werden, damit er aus den Trainingsdaten ein immer gleiches Vokabular ableiten kann. Dazu wird ihm ein individueller Algorithmus hinterlegt. Das Vokabular für einen Textdatensatz ist nur pro Tokenizer immer gleich. Unterschiedlich eingestellte Tokenizer ergeben unterschiedliche Vokabulare. Das Training des Tokenizers erfolgt vor dem Training des LLMs auf Basis der Trainingsdaten.

[Online Tokenizer zum Ausprobieren](https://tiktokenizer.vercel.app/?model=gpt-3.5-turbo)

#### Simples Beispiel

```text
Der Türrahmen ist aus Holz.
```

Nehmen wir an, der Tokenizer ist darauf eingestellt, Text in einsilbige Wörter und Satzzeichen zu zerlegen. Dann ergibt das:

```text
["Der", "Tür", "rah", "men", "ist", "aus", "Holz", "."]
```

Jedes dieser Tokens bekommt anschließend eine numerische ID. Zum Beispiel könnte `Tür` die ID `1234` bekommen (in der Regel wird einfach hochgezählt).

In den nächsten Schritten wird nur noch mit dieser numerischen ID gearbeitet, nicht mehr mit dem Token selbst.

### Vokabular

Die Liste der abgeleiteten Tokens samt ihrer numerischen ID wird in einer Datei gespeichert. Diese Datei fungiert als Wörterbuch (Vokabelheft), das später noch gebraucht wird.

### Vektorisierung

Die ID von `Tür`, `1234` wird nun in einen Embedding-Vektor umgewandelt. Ein Vektor ist eine mathematische Darstellung von Informationen. Ein Vektor sieht dann z.B. so aus:

```text
ID 1234 = Vektor [0.1, 0.2, 0.3, 0.4]
```

In der realen Praxis sind die Vektoren viel größer. Jede Zahl ist eine Position in diesem multidimensionalen Raum. Aus der Schule kennt man den zweidimensionalen Raum (x,y) und den dreidimensionalen Raum (x,y,z). Ein Vektor hat in unserem KI-Kontext aber eher hunderte oder tausende Dimensionen, die man sich nur schwer vorstellen kann. *(mehr weiß ich hierüber auch nicht)*

**Der Clou an der ganzen Sache ist,** dass diese Vektoren mathematisch miteinander verglichen werden können. Je ähnlicher zwei Vektoren sind, je näher sie im Vektorraum beieinanderliegen, desto ähnlicher sind auch die Informationen, die sie repräsentieren. So kann das LLM später erkennen, dass `Tür` und `Fenster` ähnliche Konzepte sind, während `Tür` und `Hund` sehr unterschiedliche Konzepte sind.

**Dadurch ist es möglich, semantische Ähnlichkeit und nicht nur Übereinstimmung vergleichen zu können. Das ist die Errungenschaft dieses ganzen KI-Themas!**

### Das LLM-Training

Nun, da die gesammelten Textdaten in Tokens und Vektoren umgewandelt vorliegen, kann das eigentliche Training des LLMs beginnen. Es lernt, welche Tokens in welchen Kontexten typischerweise zusammen auftreten. Dabei werden die Embedding-Vektoren und weitere interne Gewichte schrittweise angepasst, um Vorhersagefehler zu verringern. Es entstehen Vektorrepräsentationen, in denen ähnliche Kontexte oft näher beieinanderliegen, während das Modell gleichzeitig die nächste-Token-Vorhersage verbessert.

**Im Training verfeinert das LLM die statistischen Zusammenhänge zwischen den Tokens, um in einer Antwort das nächste Token eines Satzes besser vorhersagen zu können.**

Der Ablauf ist ungefähr so:

1. Das Trainingsprogramm lädt tokenisierte Trainingsdaten
2. Es bestimmt sehr viele Trainingsausschnitte aus den Trainingsdaten (als Tokensequenzen)
3. Es schneidet die Ausschnitte für das Training des Modells zurecht (zum Beispiel wird das letzte Token entfernt)
4. Es übergibt diese zurechtgeschnittenen Ausschnitte dem LLM
5. Das LLM versucht, die fehlenden Tokens vorherzusagen und die Sequenz wieder zu vervollständigen
6. Das Trainingsprogramm vergleicht die Vorhersage des LLMs mit dem tatsächlichen, zuvor abgeschnittenen Token
7. Das Trainingsprogramm berechnet die Abweichung (Fehler, Loss, [Backpropagation](./Vokabeln.md#backpropagation)) der Vorhersage
8. Das Trainingsprogramm passt die Einstellung (Embeddings, Gewichte) des LLMs an, um die Vorhersage zu verbessern

Dieser Prozess wird viele Millionen Mal für sehr viele tokenisierte Textausschnitte wiederholt, bis das LLM die Vorhersage des nächsten Tokens in den Trainingsausschnitten ausreichend beherrscht oder das Budget alle ist. Dann ist das LLM fertig trainiert und kann für die Vorhersage (Inferenz) genutzt werden.

### Antwortgenerierung durch Vorhersage (Inferenz)

Genauso, wie ein LLM die Trainingsdaten verarbeitet und im Training das nächste Token vorhersagt, reagiert es auch auf Benutzeranfragen (Prompts). Das Prompt des Benutzers wird tokenisiert, in Vektoren umgewandelt und durch das LLM geschickt. Die Daten des Prompts sind danach im Vektorraum repräsentiert. Mit diesem Wissen über den Prompt kann nun Schritt für Schritt die Antwort generiert und an die bisherige Ausgabe (Prompt + neue Tokens) angehängt werden.

Da das LLM Antworten nicht einfach als gespeicherte Sätze zurückgibt, setzt es sie schrittweise per Vorhersage zusammen. Es lädt also nicht den Textstring: `Die Tür hat eine Klinke.` von irgendwoher und gibt ihn zurück. Sondern es berechnet zunächst, dass `Die` das wahrscheinlichste erste Token der Antwort ist. Danach berechnet es ein wahrscheinlich passendes nächstes Token: `Tür`, und so weiter, bis die Antwort fertig ist: `Die Tür hat eine Klinke`.

Der generelle Ablauf ist also ungefähr so:

- Der Benutzer gibt ein Prompt ein, z.B. `Nenne mir 3 Eigenschaften einer Tür.`
- Das Prompt wird tokenisiert (vom Tokenizer in Tokens zerlegt)
- Die Tokens werden in Vektoren umgewandelt (Embeddings)
- Die Vektoren werden durch das LLM geschickt, das die Vorhersage des nächsten Tokens berechnet
- Ein passendes nächstes Token wird ausgewählt und an die bisherige Tokensequenz (Prompttokens + neue Tokens) angehängt
- Diese erweiterte Tokensequenz wird erneut durch das LLM verarbeitet, um das nächste Token vorherzusagen
- Dieser Prozess wird wiederholt, bis das LLM ein Endtoken vorhersagt oder die maximale Länge der Antwort erreicht ist ([Kontextfenster](./Kontext.md#kontextfenster)).

Dieser ganze Prozess der Vorhersage (Inferenz) ist ein Teilbereich des Buzzwords NLP, Natural Language Processing, das oft im Zusammenhang mit Künstlicher Intelligenz und Large Language Models fällt. Damit beschäftige ich mich noch gesondert.

#### Sampling (Gewichtete Zufallsauswahl)

Da die Vorhersagen der Tokens mathematisch berechnet werden und Mathematik eindeutig ist, stellt sich die Frage, warum ein LLM für dasselbe Prompt unterschiedliche Antworten generiert. Das liegt am sogenannten [Sampling](./Vokabeln.md#sampling).

Sampling bedeutet im KI-Kontext, mit einer bestimmten Methode einen Ausschnitt von Tokens aus einer größeren Menge von Tokens zu bestimmen. Dadurch wird eingeschränkt, welche Tokens für die Vorhersage des nächsten Tokens überhaupt zur Auswahl stehen. Denn je nach Einstellung der [Sampling-Parameter](./Vokabeln.md#sampling) wird das Modell den Ausschnitt eher strenger (präzise, vorhersagbar, deterministisch) oder eher lockerer (kreativ, unvorhersehbar, nicht-deterministisch) zuschneiden. Häufig angewandte Sampling-Methoden sind [Top-K, Top-P und Temperature](./Vokabeln.md#sampling).

Aus diesem Ausschnitt wird dann das nächste Token per gewichtetem Zufall (Weighted Random Sampling) ausgewählt. Gewichtet bedeutet, dass die Wahrscheinlichkeit, dass ein Token ausgewählt wird, von seiner berechneten Wahrscheinlichkeit abhängt. Ein Token mit einer Wahrscheinlichkeit von 60% wird also häufiger ausgewählt als ein Token mit einer Wahrscheinlichkeit von 1%.

Diese Zufallsauswahl, aus einem zuvor bestimmten Ausschnitt aller Tokens, ist die eigentliche Vorhersage (Inferenz) des nächsten Tokens. Das LLM berechnet also nicht das wahrscheinlichste nächste Token, sondern es berechnet die Wahrscheinlichkeiten aller Tokens, sortiert sie, grenzt anschließend die Auswahl ein und wählt daraus letztlich per Zufall eines aus.

### Diagrammartig zusammengefasst

```text
 [1. TEXTDATEI]     -->  „Die Tür hat eine Klinke.“
                               |
                               | (Tokenisierung)
                               v
 [2. TOKENS]        -->  [„Die“, „ Tür“, „ hat“, „ eine“, „ Klinke“, „.“]
                         [ 345,   1402,    281,    402,     8931,    12 ]
                               |
                               | (Embedding / Einbettung)
                               v
 [3. VEKTOREN]      -->   1402  =  [ 0.23, -0.81,  0.45,  0.01, ...,  0.67 ]
                          8931  =  [ 0.21, -0.79,  0.41,  0.05, ...,  0.59 ]
                               |
                               | (Training)
                               v
 [4. LLM]           --> Gewichte/Parameter anpassen über viele Schritte
                     (inkl. Embedding-Matrix und restlicher Netzgewichte),
                     sodass das Modell das nächste Token besser vorhersagt
                          und Vorhersagefehler minimiert werden.
```

## Das wirkliche LLM

Das wirklich konkrete Large Language Model (LLM) ist also ein auf Grundlage der oben beschriebenen Textdatei fertig **trainiertes neuronales Netzwerk**. Auch dieses ist technisch eine einzige gigantische Datei. Allerdings nicht im Format `.txt` oder `.json`, sondern in einem binären Format `.safetensors` oder `.bin`.

Die Datei besteht aus zwei Hauptkomponenten:

1. Die interne Embedding-Matrix: das Vokabelheft, das jedem Wort-Token einen festen (Start-)Vektor zuordnet.
2. Die Gewichte bzw. Parameter: das sind Milliarden von mathematischen Zahlen. *"Diese Zahlen steuern, wie die Vektoren auf ihrem Weg durch das Netzwerk miteinander verrechnet werden."* ¯\\\_(ツ)_/¯

**Größe:** Bei einem modernen, kompakten Modell (wie Llama 3 mit 8 Milliarden Parametern) ist diese Datei etwa 16 Gigabyte groß. Bei sehr großen Modellen wie GPT-x sind es mehrere hundert Gigabyte.

**Statisch:** Die trainierten Zahlen (Gewichte) bleiben im normalen Betrieb unverändert. Deshalb kennt ein LLM aus sich heraus keine Ereignisse, die nach dem Training passiert sind. Aktuelle Informationen kann es nur nutzen, wenn sie zur Laufzeit als Kontext bereitgestellt werden, z.B. über RAG oder Tool Calling (etwa eine Websuche). Auch diese externen Inhalte werden als Text in den Prompt eingebracht, tokenisiert und dann vom LLM in der Antwort verarbeitet.

## Ende

Und hier, lieber Leser, sind wir zunächst am Ende der ganzheitlichen Einarbeitung in die Funktionsweise eines LLMs angekommen. Wie ein neuronales Netzwerk funktioniert, schaue ich mir vielleicht später einmal an; vielleicht auch nicht. Wie die Mathematik dahinter genau funktioniert, werde ich mir nicht mehr anschauen. Ich möchte mich lieber auf die praktische Arbeit mit KI-Tools konzentrieren.
