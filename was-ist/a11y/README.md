# Barrierefreiheit (a11y)

Barrierefreiheit (engl. Accessibility, kurz a11y) in der Webentwicklung kümmert sich darum, dass auch Behinderte die Inhalte und Funktionen einer Webanwendung nutzen können. Dazu gehören zum Beispiel Menschen mit Sehbehinderungen, Hörbehinderungen, motorischen Einschränkungen oder kognitiven Beeinträchtigungen. Sie verwenden oft assistive Technologien wie Screenreader, Braille-Displays oder alternative Eingabegeräte, um auf digitale Inhalte zuzugreifen. Durch die Verwendung von [ARIA-Attributen](ARIA.md), semantischem HTML und anderen Techniken können Entwickler sicherstellen, dass ihre Webanwendungen von den genannten assistiven Technologien korrekt interpretiert werden und somit für alle Benutzer zugänglich sind.

a11y ist überigens ein sogenanntes Numeronym, das die 11 Buchstaben zwischen dem ersten und letzten Buchstaben des Wortes "Accessibility" repräsentiert. Kurzum: a11y = Accessibility.

## Grundlagen in 13 Tagen

Eine gute Übersicht zu den Grundlagen der Barrierefreiheit in der Webentwicklung bietet der 13-tägige a11y-Kalender von Kasey Bonifacio:

[http://a11ycalendar.kaseybon.com/](http://a11ycalendar.kaseybon.com/)

Eine kurze Info pro Tag reicht, um nach 13 Tagen eine fundierte Basis zu den Kriterien einer barrierefreien Webanwendung zu haben.

Darin ist auch leicht ersichtlich, was eine Webanwendung erfüllen muss, um ein bestimmtes A-11y-Level zu erreichen. Die Levels sind in A, AA und AAA unterteilt - in Anlehnung an die WCAG (Web Content Accessibility Guidelines von der W3C).

Auch dieser Blogartikel gibt einen guten Überblick zu den essentiellen Grundlagen der Barrierefreiheit für Frontend-Entwickler:

[https://martijnhols.nl/blog/accessibility-essentials-every-front-end-developer-should-know](https://martijnhols.nl/blog/accessibility-essentials-every-front-end-developer-should-know)

## Semantik HTML

Die Verwendung von semantischem HTML ist mehr als die halbe Miete für die Barrierefreiheit einer Webanwendung. Das korrekte Verwenden von HTML-Elementen, die die Bedeutung und Struktur des Inhalts widerspiegeln, ermöglicht es assistiven Technologien, den Inhalt besser zu interpretieren und den Benutzern eine bessere Erfahrung zu bieten.

Assistive Technologien wie Screenreader sind darauf optimiert, semantisches HTML zu erkennen und zu interpretieren. Wenn Entwickler die richtigen HTML-Elemente verwenden, können Screenreader den Inhalt korrekt vorlesen und den Benutzern eine klare Vorstellung von der Struktur und Bedeutung der Seite vermitteln.

Nur in bestimmten Fällen, in denen semantisches HTML nicht ausreicht, um die gewünschte Funktionalität zu erreichen, sollten [ARIA-Attribute](ARIA.md) verwendet werden.

```html
<!-- Semantisches HTML bzw. native Elemente statt ARIA Rollen -->

<!--
Statt eines EventListeners (z.B. `click`) auf ein <div role="button"> zu setzen,
lieber einfach die korrekten nativen Elemente dafür benutzen.
-->
<a href="#" title="Details">Link</a>
<button>Speichern</button>

<!--
Statt eines <div role="form"> zu verwenden,
lieber das native <form>-Element benutzen.
-->
<form action="/submit" method="post">
  <label for="name">Name:</label>
  <input type="text" id="name" name="name">
  <input type="submit" value="Absenden">
</form>

<!-- usw. -->
```

## Eigene Erfahrungen

Ich achte mittlerweile darauf, dass alle Frontends die ich entwickle, die Barrierefreiheit berücksichtigen. In manuellen und automatisierten Prüfprozessen wird a11y konsequent bedacht und eingefordert.

Im Zeitalter von KI (bzw. VS Code-Skills) ist es relativ leicht, die Barrierefreiheit einer Webanwendung zu prüfen und bei Notwendigkeit nachbessern zu lassen. Meist sind nur wenige Anpassungen nötig, um eine ausreichende Stufe der Barrierefreiheit zu erreichen.
