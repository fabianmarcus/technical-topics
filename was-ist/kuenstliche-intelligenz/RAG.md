# Retrieval-Augmented Generation (RAG)

## Grundlagen

Um RAG zu verstehen, sollte zunächst die Funktionsweise eines [LLM](./LLM.md) samt [Prompt](./Prompt.md) und [Kontext](./Kontext.md) bekannt und verstanden sein.

Darin wird unter anderem beschrieben, dass ein LLM ein fertig trainiertes neuronales Netz ist. Trainiert wurde es auf bereits feststehenden Trainingsdaten. "Fertig" meint hier, dass sich die Daten eines LLM nach dem Training nicht mehr ändern. Das bedeutet, dass ein LLM zur Beantwortung von Fragen nur auf das Wissen zurückgreifen kann, das es während des Trainings gelernt hat. Aktuelle Nachrichten, danach erstellte Informationen und spezielles Wissen sind dem Modell ohne weiteres Zutun unbekannt.

Zudem wird darin beschrieben, dass das LLM die ihm übergebene Eingabe genauso verarbeitet, wie die Informationen, die es schon hat. Das bedeutet, dass das LLM die Eingabe mit seinem Tokenizer in Tokens umwandelt, diese in einen Vektorraum überführt und ebenfalls zur Berechnung der Wahrscheinlichkeit für die nächste Token-Ausgabe nutzt. Speist man hier zusätzliches Wissen ein, statt nur einer Frage, kann das Wissen des LLM für diese Anfrage dynamisch über die Eingabe erweitert werden, da es dieses neue Wissen für die Antwort nutzen kann. Es speichert dieses Wissen aber nicht; nach der Beantwortung der Frage ist es wieder weg.

Das ist quasi die Grundlage und Idee von RAG.

### Beispiel

Man erhält ein umfangreiches PDF-Dokument mit allerhand Informationen zu einem Thema - zum Beispiel eine Betriebsanleitung für ein Gerät, dessen Inhalt nicht in den Trainingsdaten des LLM vorhanden ist. Nun braucht man gezielt Wissen zu diesem Gerät. Wie kann das LLM hier helfen?

Durch das zusätzliche Einspeisen des Textes der Betriebsanleitung in das Prompt an das LLM wird die gesamte Betriebsanleitung ebenfalls in LLM-verständliche Daten umgewandelt und für die Vorhersagen der nächsten Token-Ausgabe genutzt. Das LLM kann nun gezielt Fragen zu dem Gerät beantworten, da es die Betriebsanleitung in seinem Kontext vorliegen hat.

## RAG

Dieser Ansatz wird mit der Zeit sehr aufwendig; denn in der Praxis ist es mit einer Betriebsanleitung nicht getan. Da geht es eher um dutzende, hunderte, vielleicht sogar tausende Dokumente, die dem LLM zusätzlich zur Verfügung gestellt werden sollen. Ohne Mechanismus, diese Daten automatisiert und gezielt bereitstellen, abfragen und einbinden zu können, würde das [Kontextfenster](./Kontext.md#kontextfenster) des LLM nicht mal annäherend ausreichen.

### Datenbereitstellung

Die zusätzlichen eigenen Informationen werden von einer Komponente des RAG-Systems in [Chunks](./Vokabeln.md#chunk) zerlegt. Diese Chunks werden anschließend von einem Embedding-Modell tokenisiert und in Vektoren überführt bzw. in einem Vektorraum abgebildet, so wie beim LLM auch funktioniert.

Zusätzlich werden die erzeugten Vektoren (Embeddings) der Chunks, einige Metadaten und der Chunk-Text selbst in einer Vektordatenbank abgelegt, um mit einer Ähnlichkeitssuche nach ihnen suchen zu können.

Beide Technologien, also das Embedding-Modell sowie die Vektordatenbank, bilden oft separat ansprechbare Dienste. Das Embedding-Modell wird für die Vektorisierung der eigenen Informationen und der Benutzeranfragen genutzt, während die Vektordatenbank für die Suche nach den passenden Chunks herangezogen wird. Sie können aber auch als Bibliotheken in die Anwendung integriert werden.

Der Ablauf kurz & knapp:

> Dokumente chunken → Embedding-Modell → Vektoren in Vektordatenbank speichern.

#### Index

Wie eine SQL-Datenbank, hat auch eine Vektordatenbank einen Index, um die Suche nach passenden Chunks zu verbessern.

Der Index ist im RAG-Kontext die suchbare Struktur innerhalb (oder im Umfeld) der Vektordatenbank. Er ist nicht einfach ein Synonym für die gesamte Datenbank, sondern der Teil, der die Ähnlichkeitssuche effizient macht.

Vereinfacht läuft es so: Beim Schreiben werden Vektoren und Metadaten indexiert, beim Lesen wird der Suchvektor der Benutzeranfrage mittels eines bestimmten mathematischen Index-Algorithmusses (z.B. HNSW oder IVF) gegen diesen Index abgeglichen. Aus den Treffern werden dann die passenden Chunks (inklusive Klartext/Referenzen) zurückgegeben und für den Prompt verwendet.

#### Metadaten

In den Metadaten werden Informationen zu den Chunks gespeichert, die für die Suche und die spätere Nutzung relevant sind. Laut Copilot gehören dazu zum Beispiel:

- Quelle
Dokumentname, URL, Dateipfad oder System, aus dem der Chunk stammt.
- Position im Original
Kapitel, Seitenzahl, Absatznummer, Chunk-Index, Start/Ende im Text.
- Zeitbezug
Erstellt am, zuletzt geändert am, Index-Version, Embedding-Zeitpunkt.
- Fachlicher Kontext
Thema, Tags, Sprache, Dokumenttyp (FAQ, Handbuch, Ticket, Richtlinie).
- Zugriffsregeln
Mandant, Rollen, Gruppen, Sichtbarkeit (intern, vertraulich, öffentlich).
- Technische Felder
Dokument-ID, Chunk-ID, Embedding-Modellname, Vektordimension, Hash.

Einfaches, konkretes Beispiel:

```text
doc_id: HR-Policy-2026-07
chunk_id: HR-Policy-2026-07#chunk-014
title: Reisekostenrichtlinie
source_url: intranet/hr/reisekosten
page: 12
section: Spesen bei Auslandsreisen
language: de
tags: hr, travel, policy
updated_at: 2026-07-21T09:14:00Z
access_groups: hr, finance
embedding_model: text-embedding-3-large
tenant: company-a
```

### Datenabruf

Deshalb wird dieser ganze Prozess, dem Prompt an das LLM eigene Daten hinzuzufügen, der eigentlichen Anfrage vorgeschaltet.

Der Ablauf mit RAG ist wie folgt (Gemini):

1. **User-Query:** Der Nutzer stellt eine Frage an das LLM ([Prompt](./Prompt.md)). Diese Anfrage wird zunächst an das RAG-System geleitet.
2. **Vektorisierung:** Das RAG-System leitet die rohe Benutzeranfrage durch dasselbe Embedding-Modell, das auch für die im RAG-System hinterlegten Dokumente verwendet wurde, um sie in einen Suchvektor umzuwandeln.
3. **Ähnlichkeitssuche (Retrieval):** Mit diesem Suchvektor wird in der Vektordatenbank gesucht (z. B. durch Berechnung der Kosine-Ähnlichkeit), um die [TOP-K](./Vokabeln.md#top-k) passenden Dokumenten-[Chunks](./Vokabeln.md#chunk) zu bestimmen.
4. **Chunk-Rückgabe:** Diese [Chunks](./Vokabeln.md#chunk) liegen in der Vektordatenbank (oder einer anderen Datenbank) als Klartext vor und werden zurückgegeben.
5. **Prompt-Erweiterung (Augmentation):** Das RAG-System (oder ein anderes) baut nun den finalen Prompt zusammen. Dieser besteht aus dem System-Prompt, der User-Query, den zurückgegebenen Dokumenten-[Chunks](./Vokabeln.md#chunk) und sonstigen relevanten Informationen. Dieser Prompt wird dann an das LLM weitergeleitet.
6. **LLM-Vorhersage (Inferenz):** Das LLM verarbeitet den finalen Text-Prompt auf herkömmliche Weise mit seinem Tokenizer, überführt die Tokens in seinen Vektorraum und berechnet die Wahrscheinlichkeit für die nächste Token-Ausgabe.
7. **Ausgabe:** Das Ergebnis wird an den Nutzer zurückgegeben.

## Einordnung

RAG greift also nicht in den Ablauf des LLM ein, sondern erweitert vorab lediglich den Prompt um zusätzliche Informationen. Das LLM selbst bleibt unverändert und kann weiterhin nur auf das Wissen zurückgreifen, das es während des Trainings gelernt hat. Das RAG-System und das Modell müssen auch nicht zwingend etwas über sich wissen, da die Vektorisierung und die Suche in der Vektordatenbank unabhängig vom LLM erfolgen. Das LLM muss lediglich den finalen Prompt verarbeiten können.

Aufgrund dieser unabhängigen Vorschaltung ist es relativ einfach, dem LLM ständig aktuelle Informationen bereitzustellen. Alles was getan werden muss ist, dem RAG-System weitere Chunks hinzuzufügen, die anschließend automatisiert wie oben beschrieben verarbeitet und bereitgestellt werden. Das geht über moderne Tools und Plattformen mit ein paar Mausklicks. Periodisch laufende Crawl-Prozesse können das auch automatisiert erledigen, um beispielsweise tagesaktuelle Nachrichten hinzuzufügen.

## Qualitätseinstellungen

Für die Qualität von RAG sind zusätzlich wichtig:

1. **[Chunking-Strategie](./Vokabeln.md#chunk) (Größe, Overlap, semantische Grenzen)**  
Die Aufteilung entscheidet, wie gut Wissen wiedergefunden wird: zu große Chunks sind unscharf, zu kleine verlieren Kontext. Ein sinnvoller Overlap und Grenzen entlang von Absätzen, Überschriften oder Sinnabschnitten erhöhen die Trefferqualität deutlich.
2. **[Retrieval-Strategie](./Vokabeln.md#sampling) (Top-K, Hybrid Search mit Keyword + Vektor)**  
Top-K legt fest, wie viele Treffer ins Prompt gehen: zu wenig verpasst Relevantes, zu viel bringt Rauschen. Hybrid Search kombiniert semantische Nähe (Vektor) mit exakten Begriffen (Keyword) und ist in der Praxis robuster als nur eine Methode.
3. **Re-Ranking (zweite Auswahlstufe für bessere Treffer)**  
Nach der ersten Suche werden Kandidaten erneut bewertet, meist mit einem stärkeren Modell. Dadurch rutschen wirklich passende Chunks nach oben und Grenzfälle oder Zufallstreffer nach unten.
4. **Aktualität des Index (Re-Embedding bei Datenänderungen)**  
Wenn Inhalte geändert, ergänzt oder gelöscht werden, muss der Index nachgezogen werden, sonst arbeitet RAG mit veraltetem Wissen. Re-Embedding und saubere Update-Prozesse halten Suche und Antworten konsistent.
5. **Quellenangaben im Prompt, damit Antworten nachvollziehbar bleiben**  
Wenn der Antworterzeugung die Fundstellen mitgegeben werden, kann das Modell präziser und überprüfbarer antworten. Für Nutzer steigt damit Transparenz, Vertrauen und die Möglichkeit, Aussagen schnell zu validieren.
