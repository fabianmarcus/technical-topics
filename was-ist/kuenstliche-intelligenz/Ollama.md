# Ollama

Ollama ist ein KI-Startup, das sich auf die Bereitstellung von Sprachmodellen spezialisiert hat. Dazu entwickelt und vertreibt es seine gleichnamige Software.

Ollama bietet einerseits seine [Internet-Plattform](https://ollama.com/search) mit zahlreichen Sprachmodellen. Manche Modelle können heruntergeladen werden, andere laufen in der Cloud. Cloud-Modelle kennt man von ChatGPT, Claude oder Gemini. Für mich sind die lokal laufenden Modelle, die heruntergeladen und auf dem eigenen Rechner ausgeführt werden können, hier interessanter.

Dafür gibt es andererseits die kostenlose [Ollama-App](https://ollama.com/download), die auf macOS und Windows läuft. Die Installation bringt ebenfalls eine CLI mit und stellt ein Web-API unter [http://localhost:11434](http://localhost:11434/) bereit.

In der App bzw. CLI können sowohl die heruntergeladenen Modelle als auch verschiedene Cloud-Modelle eingestellt werden. Cloud-Modelle erfordern in der Regel ein kostenpflichtiges Abo, während lokale Modelle kostenlos genutzt werden können, da sie auf dem eigenen Gerät laufen.

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

Die Größe eines Modells variiert nach Parameteranzahl. Ein glm-4.7-flash ist im Download circa 20GB groß. Viele der angebotenen Modelle bewegen sich um diese Größe. Unter ein paar Gigabyte kommt kaum ein leistungsfähiges Modell. Nach oben gibt es kaum Grenzen.

### Performance

Das Chatten in der Ollama App mit einem größeren lokalen Modell funktioniert noch recht angenehm. Die Antwort braucht etwas länger als in der Cloud, ist aber immer noch akzeptabel. Das Kontextfenster lässt sich in der App von 4k bis 256k einstellen; dementsprechend fällt auch die Antwort aus.

Stelle ich allerdings ein lokales Modell im VS Code Copilot ein, ist die Leistung fast nicht mehr zu gebrauchen. Einfachste Code-Anweisungen dauern ewig. Häufig bricht der Prozess auch mit einem Fehler ab. Das liegt wahrscheinlich daran, dass noch die Extension noch viele weitere Informationen in das Kontextfenster lädt, wodurch es schnell zu groß wird und die Hardware nicht mehr hinterher kommt.

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
