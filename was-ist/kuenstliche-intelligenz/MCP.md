# MCP

MCP steht für Model Context Protocol.

MCP ist das HTTP für KI-Modelle (LLMs). Es standardisiert die Kommunikation zwischen MCP Clients und Servern. Es definiert ein Protokoll, das es Agenten ermöglicht, auf gleiche Weise mit verschiedenen Tool-Anbietern zu interagieren, ohne sich um die spezifischen Implementierungen der einzelnen MCP-Server kümmern zu müssen.

Das Protokoll definiert standardisiertes JSON(-RPC) für die Kommunikation zwischen Client und Server. Ähnlich zu HTTP, das standardisierte Methoden (GET, POST, PUT, DELETE) und Statuscodes definiert.

Eine ziemlich ausführliche Dokumentation findet sich auf der [offiziellen Website](https://modelcontextprotocol.io).

## Pseudo-Beispiel

Pseudo-Beispiel für eine Anfrage eines Agenten mit MCP Client an einen beliebigen MCP-Server, um eine Liste der verfügbaren Tools zu erhalten.

Zunächst erfolgt der Handshake, also die gegenseitige Abstimmung was möglich ist:

Anfrage des Clients:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-03-26",
    "capabilities": {
      "roots": { "listChanged": true },
      "sampling": {}
    },
    "clientInfo": {
      "name": "my-agent",
      "version": "1.0.0"
    }
  }
}
```

Antwort des Servers:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "protocolVersion": "2025-03-26",
    "capabilities": {
      "tools": { "listChanged": true },
      "resources": { "subscribe": true, "listChanged": true },
      "prompts": { "listChanged": true }
    },
    "serverInfo": {
      "name": "weather-mcp-server",
      "version": "0.3.2"
    }
  }
}
```

Client und Server haben sich auf die Protokollversion geeinigt und die Fähigkeiten des Servers ausgetauscht. Der Client weiß nun, dass er Tools abrufen kann, dass er Ressourcen abonnieren kann und dass er [Prompts](./Vokabeln.md#prompt-template) abrufen kann.

Er sendet nun eine Benachrichtigung, dass die Initialisierung abgeschlossen ist:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized",
  "params": {}
}
```

Der Client kann nun die Tools abrufen, die der Server anbietet. Dazu sendet er eine Anfrage an den Server, um die Liste der verfügbaren Tools zu erhalten.

```json
{
  "id": 1,
  "jsonrpc": "2.0",
  "method": "tools/list",
  "params": {}
}
```

Der MCP-Server antwortet dem Client bzw. Agenten dann beispielsweise wie folgt:

(Die Tools unterscheiden sich von Server zu Server, die Struktur ist aber grundsätzlich gleich.)

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      {
        "name": "weather.get_current",
        "description": "Liefert das aktuelle Wetter fuer eine Stadt",
        "inputSchema": {
          "type": "object",
          "properties": {
            "city": {
              "type": "string",
              "description": "Name der Stadt, z.B. Berlin"
            },
            "unit": {
              "type": "string",
              "enum": ["celsius", "fahrenheit"],
              "default": "celsius"
            }
          },
          "required": ["city"],
          "additionalProperties": false
        }
      }
    ]
  }
}
```

Der Agent bereitet diese Informationen für das LLM auf und arbeitet sie in den Kontext ein. So weiß das LLM, welche Tools verfügbar sind und wie sie aufgerufen werden können.

Fragt nun ein Nutzer nach dem Wetter, weiß das LLM, dass es hierauf aktuell antworten kann. Es bittet den Agenten, das Tool aufzurufen, indem er eine Anfrage an den MCP-Server sendet. Der MCP-Server ruft das Tool auf, erhält die Antwort und gibt sie an den Agenten zurück. Der Agent übergibt sie anschließend weiter an das LLM.

```json
{
  "id": 2,
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "weather.get_current",
    "arguments": {
      "city": "Berlin",
      "unit": "celsius"
    }
  }
}
```

## Tool Calling

Tool Calling ist ein Konzept, das LLMs ermöglicht, den zuständigen Agenten anzuweisen, für ihre Antwort Informationen aus externen Tools oder APIs abzurufen und an das LLM zurückzugeben. Das LLM kann so auf aktuellere Daten zugreifen, die es selbst noch nicht kennt.

Der Agent teilt dem LLM am Anfang der Interaktion mit, welche Tools oder APIs er aufrufen kann, und das LLM kann dann entscheiden, welche Tools es für die Beantwortung der Anfrage benötigt. Das LLM kann auch mehrere Tools in einer Anfrage kombinieren, um eine umfassendere Antwort zu generieren.

Bittet das LLM den Agenten, ein Tool aufzurufen, ruft der Agent die für das Tool hinterlegte Funktion mit den erforderlichen Parametern auf, die er vom LLM erhalten hat. Das Ergebnis geht für die weitere Verarbeitung zurück ans LLM.

Beispielsweise kann ein LLM eine Anfrage erhalten, um das Wetter in einer bestimmten Stadt zu überprüfen. Das LLM kennt die Antwort aufgrund der Aktualität des Wetters nicht, da das Modell auf älteren Trainingsdaten basiert. Stattdessen kann das LLM den zuständigen Agenten anweisen, eine Wetter-API aufzurufen und die aktuellen Wetterdaten abzurufen. Der Agent ruft die API auf, erhält die Daten und sendet sie zurück an das LLM, das die Daten in seine Textantwort einfügt und dem Benutzer präsentiert.

Die vom Agenten aufgerufene Funktion kann ein schlanker Adapter für einen API-, DB- oder SDK-Call sein. Bei kleinen Tools kann sie aber auch die konkrete Implementierung einer direkten Logik beinhalten - z.B. Berechnungen, Mappings oder Formatierungen. Zu komplex sollte die Funktion aber nicht werden. Best practice ist, sie schlank zu halten und komplexe Logik in einen Dienst auszulagern.

### Praktisches Beispiel

Low Level Code Beispiel, um einen wirklichen Eindruck davon zu bekommen, wie Tool Calling in der Praxis funktioniert. Das Beispiel nutzt Gemini, die anderen Anbieter funktionieren analog.

```ts
import { GoogleGenAI } from "@google/genai";

const ai = new GoogleGenAI({ apiKey: process.env.GEMINI_API_KEY! });

const functionDeclarations = [
  {
    name: "get_weather",
    description: "Liefert Wetterdaten fuer eine Stadt",
    parameters: {
      type: "OBJECT",
      properties: {
        city: { type: "STRING", description: "Name der Stadt" }
      },
      required: ["city"]
    }
  },
  {
    name: "get_air_quality",
    description: "Liefert Luftqualitaetsdaten fuer eine Stadt",
    parameters: {
      type: "OBJECT",
      properties: {
        city: { type: "STRING", description: "Name der Stadt" }
      },
      required: ["city"]
    }
  }
];

type FunctionCall = { name: string; args?: Record<string, unknown> };

async function executeTool(name: string, args: Record<string, unknown> = {}) {
  if (name === "get_weather") {
    return { city: args.city, tempC: 24, condition: "sonnig" };
  }
  if (name === "get_air_quality") {
    return { city: args.city, aqi: 42, level: "gut" };
  }
  throw new Error(`Unknown tool: ${name}`);
}

// SDK-kompatibles Auslesen von Function Calls (je nach Version unterschiedlich)
function extractFunctionCalls(response: any): FunctionCall[] {
  if (Array.isArray(response.functionCalls) && response.functionCalls.length > 0) {
    return response.functionCalls;
  }
  const parts = response?.candidates?.[0]?.content?.parts ?? [];
  const calls = parts
    .filter((p: any) => p.functionCall)
    .map((p: any) => p.functionCall);

  return calls;
}

export async function runGeminiToolLoop(userPrompt: string) {
  const contents: any[] = [
    { role: "user", parts: [{ text: userPrompt }] }
  ];

  for (let step = 0; step < 10; step++) {
    const response = await ai.models.generateContent({
      model: "gemini-1.5-pro",
      contents,
      config: {
        tools: [{ functionDeclarations }]
      }
    });

    const functionCalls = extractFunctionCalls(response);

    // Keine Tool-Anforderung mehr -> finale Antwort
    if (functionCalls.length === 0) {
      const finalText =
        response.text ??
        response?.candidates?.[0]?.content?.parts
          ?.map((p: any) => p.text)
          ?.filter(Boolean)
          ?.join("\n") ??
        "";
      return finalText;
    }

    // Modellnachricht + Tool-Antworten in den Verlauf eintragen
    for (const call of functionCalls) {
      const args = (call.args ?? {}) as Record<string, unknown>;
      const toolResult = await executeTool(call.name, args);

      contents.push({
        role: "model",
        parts: [{ functionCall: { name: call.name, args } }]
      });

      contents.push({
        role: "user",
        parts: [
          {
            functionResponse: {
              name: call.name,
              response: toolResult
            }
          }
        ]
      });
    }
  }

  throw new Error("Maximale Anzahl an Tool-Iterationen erreicht.");
}

// Beispielaufruf
runGeminiToolLoop(
  "Gib mir Wetter und Luftqualitaet fuer Berlin und sage, ob Joggen sinnvoll ist."
)
  .then((answer) => console.log(answer))
  .catch((err) => console.error(err));
```

## Statelessness

Bislang war der MCP-Ablauf `stateful`. Dabei hält der Server eine Session vor, in die alle notwendigen Informationen wie Kontext und Tool-Informationen gespeichert werden. Der Vorteil dabei ist, dass bei mehreren Requests nur noch die Session-ID sowie die neuen Informationen übertragen werden müssen. Den Rest holt sich der Server über die Session-ID aus der Session. Die Nachteile sind, dass der Server die Sessions speichern und verwalten muss, der Client darauf angewiesen ist, dass der Server die Session zur Verfügung hat und dass eine Session einen Client an genau einen Server bindet. Im Fall von Load Balancing oder Failover kann das zu Problemen führen.

Deshalb wurde MCP in seiner Spezifikation vom 28.06.2026 auf `stateless` umgestellt.

Jetzt verhält sich der MCP-Ablauf wie HTTP. Der Client überträgt alle notwendigen Informationen bei jedem Request, der Server speichert keine Sessions mehr. Somit können mehrere Requests vom selben Client ohne Probleme an verschiedene Server gehen. Load Balancing und Failover durch horizontale Skalierung sind so einfacher möglich. Fällt ein Server aus, kann der Client einfach einen anderen Server ansprechen.

## Eigene Erfahrungen

Wie viele andere habe ich MCP mit Tool Calling wahrscheinlich schon tausend Mal genutzt, ohne es zu wissen; der Vorgang läuft für Nutzer ja komplett transparent ab. Als Entwickler damit gearbeitet habe ich allerdings noch nicht. Mir war aber wichtig, das Prinzip zu verstehen. Vielleicht baue ich irgendwann mal ein kleines Projekt, um Tool Calling in der Praxis zu testen.
