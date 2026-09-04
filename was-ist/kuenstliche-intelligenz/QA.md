<!-- markdownlint-disable MD033 -->

# Question & Answer (QA)

**Frage-Antwort-Runden mit Cope zum Thema KI.**

Hier stelle ich einige meiner Fragen samt Antworten ein, die ich mir von Zeit zu Zeit bestimmt noch einmal durchlesen möchte. Während die anderen Texte im Repo weitgehend von mir selbst verfasst wurden, sind diese hier einfach kopiert.

## Inhaltsverzeichnis

- [Erkläre "kontextabhängige Repräsentation"](#erkläre-kontextabhängige-repräsentation)
- [Sammeln die großen LLM-Anbieter die Trainingsdaten selbst oder arbeiten sie mit den selben Trainingsdaten von einem Zulieferer?](#sammeln-die-großen-llm-anbieter-die-trainingsdaten-selbst-oder-arbeiten-sie-mit-den-selben-trainingsdaten-von-einem-zulieferer)
- [Wenn ich einem Modell die Aufgabe gebe, mir 3 Eigenschaften einer Tür zu nennen. Wie bestimmt es, dass es 3 Eigenschaften genannt hat und nicht zwei oder 4?](#wenn-ich-einem-modell-die-aufgabe-gebe-mir-3-eigenschaften-einer-tür-zu-nennen-wie-bestimmt-es-dass-es-3-eigenschaften-genannt-hat-und-nicht-zwei-oder-4)

## Fragen

### Erkläre "kontextabhängige Repräsentation"

<details>
<summary>Eine kontextabhängige Repräsentation bedeutet, dass ein Token je nach Satz- oder Prompt-Kontext intern anders dargestellt wird.</summary>

Ein Wort oder Token bekommt im Modell nicht einfach immer dieselbe Bedeutung, sondern seine interne Darstellung hängt davon ab, **in welchem Satz und Umfeld** es steht.

Nimm das Wort `Bank`.

```text
Ich sitze auf der Bank.
Ich überweise Geld an die Bank.
```

Das Wort ist gleich, aber die Bedeutung ist unterschiedlich:

```text
Bank = Sitzmöbel
Bank = Finanzinstitut
```

Ein LLM verarbeitet deshalb nicht nur das isolierte Token `Bank`, sondern auch die Tokens davor und danach. Daraus entsteht intern eine neue Vektordarstellung für `Bank`, die zum aktuellen Kontext passt.

Sehr vereinfacht:

```text
Token:
"Bank"

Start-Embedding:
immer ungefähr gleich

Kontext im Satz:
"sitze auf der ..."

kontextabhängige Repräsentation:
eher Sitzmöbel-Bedeutung
```

Und im anderen Fall:

```text
Token:
"Bank"

Start-Embedding:
immer ungefähr gleich

Kontext im Satz:
"überweise Geld an die ..."

kontextabhängige Repräsentation:
eher Finanzinstitut-Bedeutung
```

Der wichtige Unterschied ist also:

```text
Start-Embedding:
feste Grunddarstellung eines Tokens

kontextabhängige Repräsentation:
veränderte interne Darstellung nach Verarbeitung des Satzkontexts
```

Bei einem Transformer passiert das vor allem durch **Attention**. Das Modell schaut vereinfacht gesagt: „Welche anderen Wörter im Satz sind für dieses Token gerade wichtig?“ Dadurch wird die Darstellung eines Tokens abhängig von den anderen Tokens im Prompt.

Noch ein Beispiel:

```text
Der Schlüssel liegt auf dem Tisch.
Der Schlüssel zum Problem ist Geduld.
```

`Schlüssel` startet vielleicht mit einer allgemeinen Darstellung. Nach dem Kontext wird daraus intern einmal eher `Gegenstand zum Öffnen` und einmal eher `Lösung/entscheidender Hinweis`.

Darum ist die Aussage „Tür und Fenster liegen als Vektoren nahe beieinander“ zwar als Einstieg brauchbar, aber bei LLMs etwas verkürzt. Das Modell arbeitet nicht nur mit festen Wortvektoren, sondern erzeugt während der Verarbeitung laufend neue, kontextabhängige Vektoren.

Kurz gesagt:

```text
kontextabhängige Repräsentation =
die Bedeutung eines Tokens als Vektor, nachdem der aktuelle Satz-/Prompt-Kontext eingerechnet wurde
```

</details>

### Sammeln die großen LLM-Anbieter die Trainingsdaten selbst oder arbeiten sie mit den selben Trainingsdaten von einem Zulieferer?

<details>
<summary>Kurz: Beides kommt vor, aber große Anbieter sammeln und kuratieren ihre Trainingsdaten überwiegend selbst.</summary>

Kurzbild aus der Praxis:

1. **Eigene Datensammlungen:** Große Anbieter bauen eigene Pipelines für Web-Crawls, Lizenzdaten, Code, Dokumentation und proprietäre Quellen auf.
2. **Lizenzierte Daten von Dritten:** Zusätzlich kaufen oder lizenzieren sie Datensätze, z. B. aus Verlagen, Bild-/Textarchiven oder Spezialdomänen.
3. **Öffentliche Standardquellen überschneiden sich:** Viele Anbieter nutzen ähnliche öffentlich verfügbare Quellen. Daher gibt es Überschneidungen zwischen Modellen.
4. **Es gibt keinen gemeinsamen „Zulieferer-Standarddatensatz“:** Es gibt keinen einzigen zentralen Lieferanten, von dem alle Anbieter dasselbe Komplett-Set beziehen.
5. **Der Unterschied entsteht durch Kuratierung:** Starke Unterschiede kommen durch Filterung, Deduplikation, Qualitätsregeln, Sicherheitsfilter, Mischung der Quellen und Trainingstaktik.

Also: Es gibt Überschneidungen bei öffentlichen Daten, aber kein einheitliches gemeinsames Trainingspaket für alle großen Anbieter.

</details>

### Wenn ich einem Modell die Aufgabe gebe, mir 3 Eigenschaften einer Tür zu nennen. Wie bestimmt es, dass es 3 Eigenschaften genannt hat und nicht zwei oder 4?

<details>
<summary>Kurz: Es zählt nicht wie ein Programm mit sicherer Schleife, sondern folgt gelernten Mustern plus deinem Prompt.</summary>

Was passiert praktisch:

1. **Dein Prompt setzt ein Muster:** `Nenne 3 Eigenschaften ...` aktiviert im Modell typische Antwortmuster wie nummerierte Listen mit drei Punkten.
2. **Die Antwort entsteht Token für Token:** Bei jedem Schritt wählt das Modell das nächste wahrscheinliche Token. Nach `1.` ist `2.` wahrscheinlich, nach `2.` ist `3.` wahrscheinlich, danach oft ein Abschluss statt `4.`.
3. **Kein harter Zähler garantiert die drei Punkte:** Das Modell hat keine zwingende Kontrolllogik „stoppe exakt nach 3 Items“, außer wenn eine äußere Regel dazukommt.

Warum es trotzdem oft klappt:

- Solche Anweisungen sind im Training häufig vorgekommen.
- Listenformate sind stark gelernt.
- Gute Prompt-Formulierung erhöht die Treue.

Warum es manchmal trotzdem 2 oder 4 werden:

- Sampling/Temperatur kann das Muster aufweichen.
- Unklarer Prompt.
- Modell driftet im Verlauf.

Wie du es robuster machst:

- „Antworte mit **genau** 3 Stichpunkten.“
- „Wenn es mehr gäbe, nenne trotzdem nur 3.“
- Optional Formatvorgabe: JSON mit genau 3 Array-Elementen.
- Danach optional per nachgelagerter Prüfung validieren (bei kritischen Workflows).

</details>
