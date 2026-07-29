# Kontext

Der Kontext beinhaltet alle Informationen, die dem Modell für eine Antwort übergeben werden. Neben der eigentlichen akuten Benutzeranfrage ([dem Prompt](./Prompt.md)) werden der Anfrage in der Regel weitere Informationen mitgegeben. Der Benutzer bekommt davon nur nichts mit.

Das sind zum Beispiel:

- Die bisherige Konversation: Wurden bereits Fragen gestellt und Antworten gegeben, werden diese bei einer weiteren Anfrage innerhalb dieser Konversation wieder mitgeschickt.
- Das System-Prompt der Anwendung: Das System-Prompt ist eine Art "grundlegende Anweisung" an das Modell. Es wird vom Anbieter der Anwendung selbst formuliert und dient dazu, das Modell so zu instruieren, dass es das tut, was der Anbieter möchte.
- Nutzerpräferenzen: In vielen Chatsystemen können Benutzer Informationen über sich selbst hinterlegen. Auch kann zum Beispiel hinterlegt werden, in welchem Stil das Modell antworten soll (z. B. sachlich, humorvoll, kreativ).
- Für die Antwort relevante Dokumente (RAG): In manchen Anwendungen können Dokumente hochgeladen werden, die das Modell bei der Beantwortung berücksichtigen soll.
- Im Entwicklungsbereich können Instruktionen hinterlegt werden, wie Code generiert und strukturiert werden soll. Auch diese Informationen werden bei jeder Anfrage erneut übergeben.

Kurzum: Der Kontext ist **alles**, was das Modell über den aktuellen [Prompt](./Prompt.md) hinaus wissen soll, um eine passende Antwort zu generieren. Das Modell selbst reagiert nämlich nur auf die Informationen, die übergeben werden. Würde dieser Overhead nicht dazukommen, hätte es keine Kenntnis darüber, was bereits stattgefunden hat. So aber weiß es, in welchem Rahmen und mit welchem Vorwissen eine Anfrage gestellt wird und kann die Antwort darauf aufbauen.

## Kontextfenster

Das Kontextfenster ist die technische Spezifikation eines Kontextes eines LLMs. Es entscheidet darüber, wie gut es zuhört, wie gesprächig es antwortet und wie teuer es deshalb ist. Bei Autos spricht man über PS und autonomes Fahren, bei LLMs über maximal mögliche Tokens und Reasoning-Fähigkeit. Das Kontextfenster bezieht sich auf die gesamten Informationen, die zum Zeitpunkt einer einzelnen Anfrage an das LLM übergeben werden und auf deren Antwort durch das LLM.

Bekannt sein dürfte, dass es unterschiedliche LLMs auf dem Markt gibt; nicht nur von unterschiedlichen Anbietern, sondern auch verschiedene Versionen desselben LLMs vom selben Anbieter, die letztlich nur unterschiedlich konfiguriert wurden. Eine der wichtigsten Konfigurationen ist die Größe des Kontextfensters.

## Begrenzung

Das Kontextfenster definiert die maximale Anzahl an Tokens, die das LLM gleichzeitig berücksichtigen bzw. verarbeiten kann, um daraus die Vorhersage des nächsten Tokens bzw. Wortes zu treffen. Zudem legt es auch eine maximale Anzahl an Tokens fest, die das LLM für die Antwort ausgeben kann.

Da ein Prompt in Chatsystemen in der Regel nicht nur die aktuelle Benutzereingabe, sondern auch die gesamte vorherige Konversation inklusive hinzugefügter Dokumente enthält, wird das Kontextfenster schnell ausgeschöpft. Im einfachsten Fall vergisst es dann den Anfang oder die Mitte (Stichwort: *Lost in the Middle*-Effekt) der Konversation. Deshalb wurden einige Strategien entwickelt, um das zur Verfügung stehende Kontextfenster trotz Begrenzung optimal auszunutzen. Die wichtigsten sind Trunkierung, Zusammenfassungen und Priorisierung.

Trotz der Optimierungen ist es aber oft hilfreich, ab einem bestimmten Punkt die Konversation zu beenden und eine neue zu starten, um wieder ein frisches Kontextfenster zu haben. Besonders, wenn während der Konversation oft abgeschweift wurde und der Kontext dadurch viele irrelevante Informationen enthält, die für weitere Anfragen nicht von Bedeutung sind.

## Trunkierung

"Trunkieren" bedeutet grundsätzlich "abschneiden", "kürzen".

Im Rahmen von LLMs bedeutet Trunkierung, dass der Kontext so gekürzt wird, dass er wieder innerhalb der maximalen Tokenanzahl liegt. Der Zuschnitt kann dabei am Anfang, in der Mitte oder am Ende des Kontextes erfolgen. Das kann hart oder smart passieren.

Bei einem harten Zuschnitt wird einfach am Limit des Kontextfenster abgeschnitten. Falls notwendig auch mitten in einem Wort, wobei moderne APIs bis zum Satzzeichen verlängern.

Bei einem smarten Zuschnitt wird versucht, den Kontext vor dem Trunkieren so zu verändern, dass die wichtigsten Informationen erhalten bleiben. Der Kontext kann zum Beispiel zuvor inhaltlich zusammengefasst werden (Stichwort: *LLMLingua*), irrelevante Informationen können entfernt werden oder die Reihenfolge der Informationen kann verändert werden, um die wichtigsten Informationen an den Anfang zu bringen (Stichworte: *System-Prompt-Ankern* & *Hierarchische Zusammenfassung*).

Wenn das LLM also während einer Konversation Inhalte nicht mehr zu wissen scheint, kann es an einem Zuschnitt des Kontextes im Hintergrund liegen. Für den Benutzer läuft dieser Vorgang transparent ab.

## Beispielspezifikation

So könnte eine Spezifikation eines LLMs aussehen:

| Merkmal | Wert |
| ---------- | ------ |
| Modell | GPT-4 |
| Kontextfenster | 8.192 Tokens |
| Maximale Antwortlänge | 1.024 Tokens |
| Reasoning-Fähigkeit | Hoch |

Hier können bis zu 8.192 Tokens an Kontextinformationen (Prompt, vorherige Konversation, Dokumente, u.a.) an das LLM geschickt werden. Das LLM kann dann eine Antwort mit bis zu 1.024 Tokens generieren. Abrupte Abbrüche treten heute aber seltener auf, weil moderne Systeme die Antwortlänge besser steuern.
