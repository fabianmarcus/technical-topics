# Agents

## Der Chatbot

Angefangen hat alles mit den Chatbots. Ein Chatbot ist ein System, das auf eine Eingabe reagiert und eine Antwort generiert.

Der Benutzer hat über die Benutzeroberfläche des Anbieters (ChatGPT, Gemini, Claude, etc.) einen Text eingegeben, der wurde an das Modell geschickt, das Modell hat die Antwort generiert und an den Benutzer zurückgeschickt. Fertig. Das ist ein Chatbot. Irgendwann kam noch [Tool Calling](MCP.md) dazu, das Prinzip blieb aber dasselbe.

Die Weiterentwicklung des Chatbots ist der Agent.

## Der Agent

Allgemein ausgedrückt ist ein Agent ein Chatbot, der zur Lösung von Problemen schlussfolgern und handeln kann. Ihm ist es möglich, nicht nur zu reagieren, sondern durch Planen und das Treffen von Entscheidungen ein Ziel zu verfolgen. Ein Agent kann damit selbstständig Aufgaben erledigen, die über das reine Beantworten von Fragen hinausgehen. Dafür ist nicht einmal eine konkrete Benutzerinteraktion notwendig.

Das kann er durch seine agentischen Fähigkeiten und Eigenschaften:

- **Mehrschrittigkeit (Loop):** Der Agent wiederholt seine Ausführung in einer Schleife (z. B. `while`) bis eine von mehreren Abbruchbedingungen erfüllt ist. Bei jedem Schritt wird, falls möglich, mit dem Ergebnis des vorherigen Schrittes weitergearbeitet.
- **Zustandsführung:** Es wird ein Zustandsobjekt definiert. Im unteren Code Snippet enthält dieses zum Beispiel den aktuellen Schritt, die Höchstanzahl von Schritten, das gewünschte Ziel, das (erweiterte) [Prompt](./Vokabeln.md#prompt-template) bzw. den [Kontext](./Kontext.md), und ein Flag, ob das Ziel bereits erreicht ist. Diese Metadaten werden bei jedem Schritt aktualisiert. Im nächsten Schritt wird auf dem neuen Stand weitergearbeitet.
- **Tool-Nutzung**: Wie auch der Chatbot mit Tool Calling kann der Agent auf Tools zugreifen, um seine Aufgaben zu erledigen. Das können z. B. Websuche, Codeausführung, Datenbankabfragen oder andere Agenten sein.
- **Planung:** Der Agent kann aufgrund von programmatisch festgelegten Regeln (`if/else`) die nächste Aktion bestimmen oder den aktuellen Kontext an das Modell schicken, um eine Entscheidung treffen zu lassen. Das kann zum Beispiel die Auswahl des nächsten Tools sein, das Aufrufen eines anderen Agenten oder das Generieren von Text.
- **Abbruchbedingung:** Damit der Agent nicht ewig in seiner Schleife läuft, werden Abbruchbedingungen definiert. Das kann zum Beispiel die Erreichung des Ziels, das Erreichen der maximalen Schrittzahl oder ein Fehler sein.

Technisch ausgedrückt ist ein Agent ein Chatbot mit einer wesentlich ausführlicheren und komplexeren Implementierung. Er kann die durch die Tool Calls erlangten Informationen in einem iterativen Prozess in den bestehenden Kontext einarbeiten und dadurch mehrschrittig auf ein ihm übergebenes Ziel hinarbeiten, das er dann erreicht hat, wenn sein momentaner Zustand das Ziel ausreichend abbildet.

Zustandsführung und Abbruchbedingungen sind mit dem Loop einhergehende Eigenschaften, um die Ausführung stabil und zuverlässig zu machen. Sie stellen weniger konkrete Fähigkeiten des Agenten dar, sondern sind notwendige Begleiterscheinungen seiner Implementierung. Ohne sie würde der Agent nicht richtig funktionieren.

**Wie gut der Agent arbeitet, hängt also von der Qualität der Entscheidungslogik ab sowie von den zur Verfügung stehenden Tools.**

Ein Agent kann auch mehrere Agenten aufrufen, um seine Aufgabe zu erledigen. Das ist dann ein Multi-Agenten-System.

Unten ist ein konkretes, simples Code Beispiel für einen Agenten abgebildet (siehe _Agent Code Beispiel_).

## Agent Loop

Wie oben angedeutet, bewirkt der Agent Loop die wiederholte Ausführung des Agenten und ermöglicht dadurch mehrschrittiges, aufeinander aufbauendes Arbeiten.

Konzeptionell gehört der Loop nicht direkt zur Kernfunktionalität eines Agenten, sondern zu einer äußeren steuernden und orchestrierenden Schicht. Implementatorisch ist der Loop aber oft ein direkter Teil der Implementierung des Agenten (siehe unten _Agent Code Beispiel_). Im Beispiel ist das eine einfache `while`-Schleife. In der Praxis kann das auch ein komplexeres System sein.

Funktional kann man es so trennen:

- Agent-Kern: Die reine Entscheidungslogik, welche Aktion als nächstes ausgeführt werden soll. Der Agent selbst ist nicht für die Iteration zuständig, sondern liefert nur die Entscheidung. Siehe `decideNextAction()` im Code Beispiel unten.
- Agent-System: Die Steuerungsschicht, die den Agenten in einer Schleife ausführt, die Ergebnisse sammelt und den Kontext aktualisiert. Siehe `runAgent()` im Code Beispiel unten.

## Agent Harness

Der Harness besteht aus der gesamten Laufzeit- und Ausführungsumgebung, die den Agenten bzw. das Agenten-System arbeitsfähig macht. Er umfasst also alles, was um den Agenten-Kern herum gebaut ist.

Im unteren Code Beispiel gehören dazu vor allem `runAgent()`, `executeAction()`, `updateState()` und `shouldStop()` sowie die konkret angebundenen Tools. Nicht eindeutig nur dem Harness oder nur dem Agenten-Kern zuzuordnen sind gemeinsame Strukturen wie `Action`, `ToolResult`, `AgentState` oder `ToolMap`, da sie die Schnittstelle zwischen Entscheidungslogik und Laufzeitumgebung beschreiben.

Zum Harness gehören neben direkten Agenten-Funktionalitäten wie Loop, Zustand und Abbruchbedingungen auch vor- oder nachgelagerte Tools wie OCR, Websuche, Markdown-Interpreter, API-Client, Dateileser und Ähnliches. Also alles, was der Agent für seine Arbeit benötigt, aber nicht direkt zu seiner Entscheidungslogik gehört.

Bildliches Beispiel: Zwei Personen. Eine Person trägt ein kabelloses Headset im Ohr. Die andere Person kann ihr darüber Anweisungen geben, was sie als nächstes sagen oder tun soll. Die Person mit dem Headset ist quasi der Harness bzw. das Agent-System. Die andere Person ist der Agent-Kern, also die Entscheidungslogik. Sie kann nur sagen, was als nächstes passieren soll, aber nicht selbst handeln, dafür braucht sie die andere Person.

## Eigene Erfahrungen

Bislang habe ich Agenten nur transparent über Benutzeroberflächen genutzt. Beruflich wie privat arbeite ich viel mit GitHub Copilot für VS Code. Das Prompten von Features bzw. das hinein generieren lassen von Code in die Codebasis ist ein klassischer Fall für Agentensysteme.

Man gibt ein gewünschtes Ziel vor (z. B. "Implementiere eine Funktion, die X macht") und der Agent arbeitet in mehreren Schritten darauf hin, bis das Ziel erreicht ist. Dabei kann er auf verschiedene Tools zugreifen, wie z. B. die Codebasis, Dokumentation oder externe APIs. Auch das Einbeziehen von Instructions und Skills wird über das Agentensystem gesteuert. Copilot Agent in VS Code ist also ein Agentensystem, das auf ein Modell zugreift und die Tool Calls für die Codebearbeitung orchestriert.

## Agent Code Beispiel

```ts
type Intent = "general_question" | "status_request" | "account_request";

type Action =
  | { type: "search"; query: string }
  | { type: "callApi"; endpoint: string; payload?: Record<string, unknown> }
  | { type: "askUser"; question: string }
  | { type: "final"; answer: string };

type ToolResult = {
  ok: boolean;
  source: "search" | "api";
  data?: unknown;
  error?: string;
};

type AgentState = {
  goal: string;
  step: number;
  maxSteps: number;
  intent: Intent;
  confidence: number;
  hasSearched: boolean;
  hasApiData: boolean;
  lastToolResult?: ToolResult;
  userId?: string;
  messages: Array<{ role: "user" | "assistant" | "tool"; content: string }>;
};

type ToolMap = {
  search: (query: string) => Promise<ToolResult>;
  callApi: (
    endpoint: string,
    payload?: Record<string, unknown>,
  ) => Promise<ToolResult>;
};

async function decideNextAction(state: AgentState): Promise<Action> {
  if (state.step >= state.maxSteps - 1) {
    return { type: "final", answer: "Ich stoppe wegen Schrittlimit." };
  }

  if (state.lastToolResult?.ok === false) {
    return {
      type: "askUser",
      question: `Beim letzten Schritt gab es einen Fehler (${state.lastToolResult.error ?? "unbekannt"}). Soll ich es erneut versuchen?`,
    };
  }

  const needsApi =
    state.intent === "status_request" || state.intent === "account_request";
  if (needsApi && !state.hasApiData) {
    if (!state.userId) {
      return {
        type: "askUser",
        question: "Bitte gib mir deine Benutzer-ID für die API-Abfrage.",
      };
    }

    const endpoint =
      state.intent === "status_request"
        ? `/api/status/${state.userId}`
        : `/api/account/${state.userId}`;

    return { type: "callApi", endpoint };
  }

  const needsSearch =
    state.intent === "general_question" &&
    state.confidence < 0.7 &&
    !state.hasSearched;
  if (needsSearch) {
    return { type: "search", query: state.goal };
  }

  return {
    type: "final",
    answer:
      "Hier ist meine zusammengefasste Antwort auf Basis der verfügbaren Daten.",
  };
}

async function executeAction(
  action: Action,
  tools: ToolMap,
): Promise<ToolResult | null> {
  switch (action.type) {
    case "search":
      return tools.search(action.query);

    case "callApi":
      return tools.callApi(action.endpoint, action.payload);

    case "askUser":
      return {
        ok: true,
        source: "api",
        data: { needsUserInput: true, question: action.question },
      };

    case "final":
      return null;
  }
}

function updateState(
  state: AgentState,
  action: Action,
  result: ToolResult | null,
): AgentState {
  const nextState: AgentState = {
    ...state,
    step: state.step + 1,
    lastToolResult: result ?? state.lastToolResult,
  };

  if (action.type === "search") {
    nextState.hasSearched = true;
    nextState.confidence = Math.min(1, state.confidence + 0.25);
    nextState.messages = [
      ...state.messages,
      { role: "tool", content: `Search ausgeführt: ${action.query}` },
    ];
  }

  if (action.type === "callApi" && result?.ok) {
    nextState.hasApiData = true;
    nextState.confidence = Math.min(1, state.confidence + 0.4);
    nextState.messages = [
      ...state.messages,
      { role: "tool", content: `API aufgerufen: ${action.endpoint}` },
    ];
  }

  if (action.type === "askUser") {
    nextState.messages = [
      ...state.messages,
      { role: "assistant", content: action.question },
    ];
  }

  if (action.type === "final") {
    nextState.messages = [
      ...state.messages,
      { role: "assistant", content: action.answer },
    ];
  }

  return nextState;
}

function shouldStop(state: AgentState, action: Action): boolean {
  if (action.type === "final") return true;
  if (state.step >= state.maxSteps) return true;
  return false;
}

export async function runAgent(
  goal: string,
  intent: Intent,
  tools: ToolMap,
): Promise<string> {
  let state: AgentState = {
    goal,
    step: 0,
    maxSteps: 6,
    intent,
    confidence: intent === "general_question" ? 0.4 : 0.8,
    hasSearched: false,
    hasApiData: false,
    messages: [{ role: "user", content: goal }],
  };

  while (true) {
    const action = await decideNextAction(state);
    const result = await executeAction(action, tools);
    state = updateState(state, action, result);

    if (shouldStop(state, action)) {
      break;
    }
  }

  const finalMessage = [...state.messages]
    .reverse()
    .find((message) => message.role === "assistant");
  return finalMessage?.content ?? "Kein Ergebnis.";
}
```
