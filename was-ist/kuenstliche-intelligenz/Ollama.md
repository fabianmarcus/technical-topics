# Ollama

Ollama ist ein KI-Startup, das sich auf die Bereitstellung von Sprachmodellen (LLMs und VLMs) spezialisiert hat. Dazu entwickelt und vertreibt es seine gleichnamige Software.

Ollama bietet auf seiner [Internet-Plattform](https://ollama.com/search) zahlreiche Sprachmodelle an. Manche Modelle können heruntergeladen werden, andere laufen in der Cloud. Cloud-Modelle kennt man von ChatGPT, Claude oder Gemini.

Für mich sind die lokal laufenden Modelle, die heruntergeladen und auf dem eigenen Rechner ausgeführt werden können, hier interessanter. Dafür gibt es zudem die kostenlose [Ollama-App](https://ollama.com/download), die auf macOS und Windows läuft. Die Installation bringt ebenfalls eine CLI mit und stellt ein Web-API unter [http://localhost:11434](http://localhost:11434/) bereit.

In der App bzw. CLI können sowohl die heruntergeladenen Modelle als auch verschiedene Cloud-Modelle eingestellt werden. Cloud-Modelle erfordern in der Regel ein kostenpflichtiges Abo, während lokale Modelle kostenlos genutzt werden können, da sie auf dem eigenen Gerät laufen.

Darüber hinaus ist [Ollama auch ein SDK](https://github.com/ollama/ollama-js), das in Node.js und Python genutzt werden kann. Damit können eigene Anwendungen auf Basis der Ollama-Modelle entwickelt werden. Weitere SDKs wie LlamaIndex oder LangChain können ebenfalls auf Ollama-Modelle zugreifen.

## Befehle

Die gängigen CLI-Befehle:

| Befehl | Beschreibung |
| -------- | -------------- |
| ollama list | Zeigt alle lokal installierten Modelle an. |
| ollama pull *name* | Lädt ein Modell aus der Online-Library herunter. |
| ollama run *name* | Startet ein Modell (lädt es ggf. vorher herunter). |
| ollama rm *name* | Löscht ein lokales Modell, um Speicherplatz freizugeben. |
| ollama create *name* -f *file* | Erstellt ein neues Modell aus einem Modelfile. |
| ollama help | Zeigt die Hilfe an. |

## Erfahrung & Eindrücke

Beim Herumspielen mit lokalen Modellen wird einem erst wirklich bewusst, wie viel Rechenleistung und Speicherplatz benötigt wird, um ein größeres Sprachmodell flüssig auszuführen. Insbesondere, wenn agentische Fähigkeiten wie das Generieren von Code dazu kommt.

### Hardware

Ich habe ein MacBook Pro von 2024 mit M4-Chip und 48GB RAM. Macs sind wohl prädestiniert für das Ausführen von KI-Modellen, aufgrund der Unified Memory Architecture (UMA). Dabei kann die GPU nicht nur auf ihren eigenen Arbeitsspeicher zugreifen, sondern kann auch den System-RAM nutzen. Bei Windows sind die Speicher strikt getrennt. Meiner GPU stehen also bis zu 48GB RAM zur Verfügung, was für die meisten Modelle ausreichend ist.

### Modellgröße

Die Größe eines Modells variiert nach Parameteranzahl. Ein glm-4.7-flash ist im Download circa 20GB groß. Viele der angebotenen Modelle bewegen sich um diese Größe. Unterhalb von ein paar Gigabyte kommt kaum ein leistungsfähiges Modell. Nach oben gibt es kaum Grenzen.

### Performance

Das Chatten in der Ollama App mit einem größeren lokalen Modell funktioniert noch recht angenehm. Die Antwort braucht etwas länger als in der Cloud, ist aber immer noch akzeptabel. Das Kontextfenster lässt sich in der App von 4k bis 256k einstellen; dementsprechend fällt auch die Antwort aus. Nach längerer Konversation passiert aber auch hier ein stetiger Leistungsabfall, da immer mehr Kontext verarbeitet werden muss. Das schlägt lokal spürbar mehr ins Gewicht als bei einem Cloud-Modell.

Stelle ich in VS Code ein lokales Modell im Copilot Chat ein, ist die Leistung fast nicht mehr zu gebrauchen. Einfachste Code-Anweisungen dauern ewig. Häufig bricht der Prozess auch fehlerbedingt ab. Das liegt wahrscheinlich daran, dass die Extension noch viele weitere Informationen in das Kontextfenster lädt, wodurch es schnell zu groß wird und die Hardware nicht mehr hinterher kommt.

Hieran hat das lokale Modell zum Beispiel ziemlich rumgerödelt:

```text
[Prompt]
Schreibe eine Funktion ein, die eine for-Schleife von 10 Iterationen ausführt und den momentanen Index loggt.
```

```ts
/**
 * Führt eine Schleife von 10 Iterationen aus und loggt den aktuellen Index.
 */
export const logIterationIndex = () => {
  for (let i = 0; i < 10; i++) {
    logger.debug(`Aktueller Index: ${i}`);
  }
};
```

Vom Gedanken, mit einem lokalen Modell in VS Code zu entwickeln, sollte man sich erst mal verabschieden. Bei einer größeren Code Basis und komplexeren Aufgabenstellungen ist die Leistung weit weg von ausreichend.

Für Chat-Anwendungen ist es aber durchaus brauchbar. Möglich ist auch, verschiedene Modelle für unterschiedliche Aufgaben hintereinander zu nutzen und jedem den Kontext des vorherigen Modells zu geben. So kann von den Stärken der einzelnen kleineren, aber spezialisierten Modelle profitiert werden, wobei die Informationsverarbeitung kostenlos und lokal auf dem eigenen Rechner stattfindet. Keine Daten gehen in die Cloud.

Ein Bild, PDF oder eine Voice-Nachricht kann zum Beispiel zunächst von einem darauf spezialisierten Modell zu Text umgewandelt werden. Der Inhalt des Textes wird dann von einem anderen Modell analysiert. Die Analyse wird anschließend von einem weiteren Modell zu einem Ergebnistext verarbeitet.

### Möglichkeiten

Aus einem Haufen von der Community erstellter Modelle auswählen zu können, bietet auch die Möglichkeit, verschiedene Modelle zu vergleichen. Wie antwortet zum Beispiel ein Modell, dessen Guardrails (so gut) wie runtergefahren sind.

Ich habe eins ausprobiert. Es hatte keine Hemmungen, auf Fragen zu antworten, die andere Modelle ablehnen oder mit einer beschwichtigenden Antwort inklusive Hilfsangebot beantworten würden. Das zeigt, wie wichtig ein ordentliches Training frei verfügbarer Modelle ist, da in der Zukunft wahrscheinlich immer mehr Menschen mit Chatbots über ihre Probleme sprechen werden.

## HuggingFace

HuggingFace ist das GitHub der KI-Community. Hier veröffentlichen Leute ihre eigens trainierten Modelle, um sie auch anderen zur Verfügung zu stellen. Nicht nur Sprachmodelle, sondern auch multimodale Modelle für Bild- und Audioinhalte. Ollama bietet einige der dort angebotenen Sprachmodelle auch in seiner [Library](https://ollama.com/library) an, aber nicht alle.

Ein Modell auf HuggingFace kann aber genauso heruntergeladen und lokal in Ollama ausgeführt werden - zumindest, wenn es im gguf-Format vorliegt. Dafür braucht es allerdings einen extra Konfigurationsschritt, da Ollama diese Modelle nicht direkt einspielen kann. Es muss ein sogenanntes Modelfile erstellt werden, das die Konfiguration für das Modell enthält. Der Aufwand dafür ist überschaubar.

### Anleitung

- **Schritt A:** Das Modell herunterladen
Auf Hugging Face nach einem Modell im GGUF-Format suchen, die gewünschte Datei (z. B. mein-modell.gguf) herunterladen und in einem passenden Ordner ablegen.

- **Schritt B:** Ein Modelfile erstellen
In demselben Ordner, in dem die .gguf-Datei liegt, eine neue Textdatei erstellen und sie einfach *Modelfile* nennen (ohne Dateiendung wie .txt). In die Datei wird folgendes eingefügt:

```bash
# Pfad zur heruntergeladenen GGUF-Datei
FROM ./mein-modell.gguf

# Optional: System Prompt festlegen (wie sich das Modell verhalten soll)
SYSTEM "Du bist ein hilfreicher Assistent, der auf Deutsch antwortet."

# Optional: Parameter wie Temperatur einstellen (0.0 ist sehr präzise, 1.0 ist kreativ)
PARAMETER temperature 0.7
```

- **Schritt C:** Das Modell in Ollama registrieren
Ollama mit folgendem Terminal-Befehl mitteilen, dass es dieses Modelfile als neues Modell behandeln soll:

```bash
# Ersetze mein-neues-modell durch den Namen, den du dem Modell geben willst.
ollama create mein-neues-modell -f Modelfile
```

- **Schritt D:** Das Modell nutzen
Sobald der Vorgang abgeschlossen ist, kann das Modell in Ollama genutzt werden.

```bash
ollama run mein-neues-modell
```
