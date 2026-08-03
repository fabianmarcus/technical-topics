# Halluzinationen in LLMs

## Arbeitsweise eines LLMs

Wie in [Was ist ein LLM?](LLM.md) beschrieben, generiert ein LLM Antworten auf Basis von Wahrscheinlichkeiten. Es **berechnet**, welches Wort (bzw. Token) als nächstes im Satz am wahrscheinlichsten ist, und hängt dieses an die bisherige Antwort an.

Es gibt also keinen feststehenden Textstring zurück wie: `Die Tür hat eine Klinke.`, der irgendwo genau so gespeichert ist. Sondern es berechnet auf Grundlage des Prompts zunächst, dass `Die` das wahrscheinlichste erste Token der Antwort ist. Danach berechnet es ein wahrscheinlich passendes nächstes Token: `Tür`, und so weiter, bis die Antwort fertig ist: `Die Tür hat eine Klinke`.

## Halluzination

Wie kommt es jetzt, dass dir ein LLM voller Inbrunst ins Gesicht lügt? Das liegt daran, dass das LLM gar nicht weiß, dass es etwas Falsches sagt. Ein LLM hat in der Regel keinen festen, globalen Wissens-Schwellenwert nach dem Muster "ab X weiß ich es, darunter weiß ich es nicht". Es berechnet bei jedem Schritt nur Wahrscheinlichkeiten für das nächste Token.

Ob dann eine Unsicherheitsantwort wie "Dazu habe ich nicht genug Informationen" erscheint, hängt stärker vom Gesamtsystem drumherum ab als vom LLM: etwa von Anweisungen, Feintuning, Sicherheitsregeln und der gewählten Generierungsstrategie. Das LLM selbst hingegen antwortet auf Grundlage seiner Trainingsdaten immer.

**Extrembeispiel**: Nehmen wir folgendes an:

- Es gibt kein im Vorfeld prüfendes Gesamtsystem, alle Prompts gehen direkt an das LLM.
- Die Trainingsdaten des Modells beruhen einzig auf dem Satz: `Der Türrahmen ist aus Holz.`.
- An dieses Modell wird der Prompt gestellt: `Wie lauten die Regeln beim Fußball?`.

Das LLM wird trotzdem eine Antwort generieren, die auf Grundlage der Trainingsdaten plausibel klingt, aber inhaltlich gar keinen Sinn macht. Es könnte zum Beispiel antworten: `Der Türrahmen ist aus Holz. Die Regeln sind aus Holz. Der Türrahmen ist die Regel beim Fußball`. Das LLM halluziniert hier, weil der eine trainierte Türrahmen-Satz sowie der Text des Prompts die einzigen Informationen sind, die es hat. Es gibt keine anderen wahrscheinlicheren Vorhersagen.

Aber auch ein weniger extremes Beispiel wird zu Halluzinationen führen. Nehmen wir an, das LLM hat in seinen Trainingsdaten nur gelernt, dass eine Tür eine Klinke hat; von Türen ohne Klinken weiß es nichts. Wird ihm nun der Prompt gestellt: `Wie sehen Türen aus?`, wird es möglicherweise antworten: `Alle Türen haben eine Klinke.`. Das ist zwar plausibel, aber nicht korrekt, da es auch Türen ohne Klinke gibt. Das LLM halluziniert hier, weil es nur auf Grundlage der Trainingsdaten antwortet und keine weiteren Informationen hat.

Wie gut das LLM richtige Antworten gibt bzw. wie häufig es halluziniert, hängt von mehreren Faktoren ab:

### Qualität der Trainingsdaten

Wenn das LLM auf umfassenden, qualitativ hochwertigen Daten trainiert wurde, die viele korrekte Informationen enthalten, ist die Wahrscheinlichkeit geringer, dass es halluziniert. Wenn die Trainingsdaten jedoch zu gering, fehlerhaft oder ungenau sind, wird das LLM diese Fehler häufig als Wahrheit wiedergeben. Es denkt, es sagt die Wahrheit, ist in Wahrheit aber falsch informiert; was nichts anderes als eine Halluzination ist.

### Trainingsverfahren

Die Art und Weise, wie das LLM trainiert wurde, beeinflusst ebenfalls die Wahrscheinlichkeit von Halluzinationen. Modelle, die auf robusten Trainingsmethoden basieren, die auf Genauigkeit und Konsistenz abzielen, neigen weniger zu Halluzinationen. Konkret gemeint ist, dass das Training robuste Kontextrepräsentationen lernt, damit die Vorhersage des nächsten Tokens verlässlicher wird.

### Prompt-Design

Die Art und Weise, wie eine Anfrage formuliert wird, kann die Wahrscheinlichkeit von Halluzinationen beeinflussen.

Klare, präzise und gut strukturierte Prompts können dazu beitragen, dass das LLM relevantere und genauere Antworten liefert. Vieldeutige oder unklare Prompts können hingegen zu Halluzinationen führen, da das Modell versucht, die Lücken zu füllen. Ein simples Beispiel: die Angabe einer API- oder Programm-Version in einem Prompt kann zum Beispiel verhindern, dass das LLM eine Antwort auf Grundlage einer älteren Version generiert, die nicht mehr korrekt ist oder mehrere Versionen miteinander vermischt.

Genauso kann aber auch ein zu enger Prompt zu einem spezifischen Thema dazu führen, dass das LLM versucht, eine Antwort zu generieren, die nicht durch die Trainingsdaten gedeckt ist. Wer in einem Bereich arbeitet, der mit Informationen und Fachwissen arbeitet, das nur gering öffentlich zugänglich ist und kein RAG-System angebunden ist, wird feststellen, dass das LLM versucht, Wissen und Kenntnisse aus anderen ähnlichen Bereichen zu adaptieren und auf deren Grundlage eine Antwort zu generieren. Solche Antworten werden dann oft falsch oder zu ungenau sein. Auch das ist nichts anderes als eine Halluzination.

Gefährlich sind auch Widersprüche zwischen LLM-, Entwickler- und Nutzeranweisungen, da sie zu inkonsistenten oder erfundenen Aussagen führen können. Das ist so, als wenn drei Vorgesetzte unterschiedliche Anweisungen zur Erledigung derselben Aufgabe geben. Der eine sagt: "Mach da einfach was, hauptsache da steht was." (z.B. Benutzer-Prompt), der andere sagt: "Mach es gründlich und fundiert. Das muss stimmen." (z.B. System-Prompt) und der Dritte sagt: "Hier, ich hab schon mal angefangen, darauf kannst du aufbauen." und darin ist alles falsch (z.B. schlechte Trainings-/RAG-Daten). Hier sind Halluzinationen vorprogrammiert, weil das LLM versucht, die widersprüchlichen Anweisungen zu erfüllen und dabei auf ungenaue oder falsche Informationen zurückgreift.

### Kontext und Hintergrundinformationen

Wenn das LLM über ausreichend passenden Kontext verfügt, um die Anfrage zu verstehen, ist die Wahrscheinlichkeit geringer, dass es halluziniert. Wenn jedoch wichtige Informationen fehlen oder der (hinzugefügte) Kontext falsch ist, kann das Modell dazu neigen, falsche oder erfundene Antworten zu generieren, weil es den Kontext maßgeblich als Wissen für die Antwort nutzt. Deshalb ist [RAG](./RAG.md) und [Tool Calling](./MCP.md) so wichtig, um dem LLM die Möglichkeit zu geben, auf individuelle und aktuelle Informationen zuzugreifen. Wobei auch hier darauf zu achten ist, dass ordentliche, verlässliche RAG-Quellen vorliegen und die MCP-Tools so definiert sind, dass tatsächlich das richtige Tool für die jeweilige Anfrage aufgerufen wird. Ansonsten endet auch das in Halluzinationen.

## Eigene Erfahrungen

Ich nutze KI-Chatsysteme mittlerweile sehr häufig für alle möglichen Anfragen, beruflich wie privat. Ich möchte gar nicht wissen, wie oft ich schon angelogen wurde, die Information aber als korrekt wahrgenommen habe.

Je weniger man selbst über ein Thema weiß, desto schwieriger ist es logischerweise eine Halluzination zu erkennen. Denn die Antwort wird sich in den meisten Fällen plausibel anhören und mit einer Begründung versehen sein. Wer sich aber in einem Fachgebiet auskennt, wird durch eigenes Wissen oder Ausprobieren schnell merken, dass das LLM eine falsche Antwort generiert hat. Und das ist mir schon öfter passiert.

Auch tückisch ist, wenn das LLM, wie es Menschen auch tun, zwar korrekt auf eine Frage antwortet, wichtiges zusätzliches Wissen aber einfach weglässt, weil

- es für eine als ausreichend geltende Beantwortung der gestellten Frage nicht benötigt wird,
- das gewählte Kontextfenster nicht mehr als eine kurze Antwort zulässt, oder
- schlicht kein weiteres Wissen vorliegt.

So fühlt man sich gleich mit der ersten Antwort gut informiert, weiß aber nicht, dass das nur die halbe Wahrheit war (oder gar keine). Zudem hängt die Ausführlichkeit der Antwort auch stark vom verwendeten Modell ab. Wer wirklich umfangreich über ein Thema informiert werden möchte, sollte das LLM gezielt nach weiteren Details fragen und ein Modell mit großem Kontextfenster verwenden, damit die Antwort etwas länger ausfallen darf.

Bei unwichtigen Themen ist das nicht so schlimm, bei wichtigen Themen sollte man sich aber ein bisschen absichern:

- Im Prompt angeben, dass für die Antwort zuvor eine Websuche durchgeführt werden soll.
- Im Prompt angeben, dass die Antwort mit Quellenangaben versehen werden soll, damit klar ist, woher die Informationen stammen und überprüft werden können.
- Zusätzlich eine eigene Recherche via traditioneller Suchmaschine durchführen, um die Antwort zu validieren.
- Die Antwort bzw. deren Begründung gezielt auf Inkonsistenzen prüfen und diese beim LLM hinterfragen.
- Das Prompt mehrfach mit unterschiedlicher Formulierung und Präzision ans gleiche LLM stellen.
- Zusätzlich ein weiteres LLM befragen (mit anderem Kontextfenster und evtl. Reasoning-Fähigkeit).
