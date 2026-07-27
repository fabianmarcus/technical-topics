# React

[Rules of React](https://react.dev/reference/rules)

## React Compiler

Der React Compiler ist in React 18 eingeführt worden. Er ist zurzeit noch optional, wird aber irgendwann Pflicht sein. Er ist bereits gut getestet und bei Meta produktiv im Einsatz. Der Compiler ist im Prinzip ein Babel Plugin, das den eigentlichen Compiler ummantelt. Er läuft mit allen gängigen Build Tools und wird während der Build Time ausgeführt.

Die Aufgabe des Compilers ist es, die Performance und Stabilität von React-Anwendungen zu verbessern, indem er das Rerendern von Komponenten optimiert. Er analysiert den Code und erkennt, welche Teile einer Komponente sich ändern und welche nicht. Dadurch kann er unnötige Rerenderings vermeiden und die Performance der Anwendung verbessern.

Was also zuvor durch `useMemo` oder `useCallback` manuell optimiert werden musste, kann nun automatisch vom Compiler erledigt werden. Man muss es nicht mehr selbst schreiben, sondern kann die Variable oder Funktion einfach normal definieren. Es sind zudem keine Änderungen an bestehendem Code notwendig, um den Compiler zu nutzen. Er optimiert nur da, wo es sinnvoll ist. Bestehende Memoization kann im Code belassen und weiterhin für spezielle Zwecke manuell genutzt werden.

Das Flag `"compiler": "react"` in der `package.json` (auf Wurzelebene) aktiviert den Compiler. In Vite ist er standardmäßig bereits aktiviert. Der Rest läuft transparent.

## State Management

State Management ist ein zentrales Thema in React-Anwendungen. Es gibt viele verschiedene Ansätze und Bibliotheken, um den State zu verwalten. Die Wahl des richtigen Ansatzes hängt von der Größe und Komplexität der Anwendung ab.

### Prop Drilling

Von Prop Drilling spricht man, wenn in einer hierarchisch hoch gelegenen Komponente ein lokaler State definiert wird, der dann mittels Komponenten-Eigenschaften (Props) über mehrere tiefer liegende Komponenten hinweg weitergereicht wird, bis er schließlich in der Komponente ankommt, die ihn benötigt.

Für kleinere Anwendungen mit wenigen Komponenten-Ebenen ist Prop Drilling oft unproblematisch und nicht falsch. Bei größeren Anwendungen mit vielen Komponenten-Ebenen kann es jedoch schnell unübersichtlich werden, Refactorings unnötig aufwendig machen und zu Problemen führen, wenn sich die Struktur der Komponenten ändert.

Alternativen sind der native React Context oder eine von zahlreichen State Management Bibliotheken wie Redux, Zustand, Jotai oder Recoil, die den State zentral verwalten und den Zugriff auf ihn vereinfachen.

Mit [Zustand](https://github.com/pmndrs/zustand) habe ich zuletzt eigene Erfahrungen gemacht. Der Einstieg war einfach und es ließ sich effizient damit arbeiten. Meine Berührungspunkte mit [Redux](https://redux.js.org/) sind zu lange her um sie hier als Erfahrung anzugeben.

### Custom Hook

Ein Custom Hook ist quasi nichts anderes als eine React-Komponente ohne Markup-Ausgabe. Er kapselt Logik und zustandsbehaftetes Verhalten, das so in mehreren Komponenten wiederverwendet werden kann oder große Komponenten durch das Auslagern von Code verschlankt. Custom Hooks sind somit eine gute Möglichkeit, den Code sauber und modular zu halten.

React erkennt Custom Hooks automatisch am `use`-Prefix im Funktionsnamen. Sie können nur innerhalb von React-Komponenten oder anderen Hooks aufgerufen werden. Eine Änderung eines States in einem Custom Hook führt wie gewohnt zu einem Rerendering der Komponente, die den Hook aufruft. Custom Hooks gelten aber nur pro Instanz, nicht global. Ruft man denselben Custom Hook in zwei Komponenten auf, haben beide Hook Instanzen ihre eigenen lokalen States in der Komponente. Sie sind kein Ersatz für einen React Context oder eine State Management Bibliothek.

### React Context

Ein [React Context](https://react.dev/reference/react/createContext) ist ein React nativer globaler State, der von allen Komponenten innerhalb des Context Providers genutzt werden kann. Er ist eine gute Möglichkeit, den State zentral zu verwalten und den Zugriff auf ihn zu vereinfachen.

Das Einrichten ist manchmal etwas verwirrend, einmal eingerichtet ist er aber einfach zu nutzen. Der Context kann auch in Custom Hooks genutzt werden, um den State zu kapseln und die Nutzung zu vereinfachen. Eine Änderung eines States im Context führt auch hier wie gewohnt zu einem Rerendering der Komponenten, die den Context nutzen.

### Rerendering

Rerendering ist der Prozess, bei dem React die Komponente neu rendert, die einen State Change erfahren hat, um die Änderungen in der Oberfläche widerzuspiegeln. Dabei werden auch alle abhängigen untergeordneten Komponenten neu gerendert.

Das Rerendering ist eine der Hauptursachen für Performance-Probleme in React-Anwendungen. Kleine Unachtsamkeiten, wie das Vergessen von `useMemo`, `useCallback` oder einer Dependency im `useEffect`, können zu unnötigen unzähligen Rerenderings führen, die die Performance der Anwendung beeinträchtigen. Der React Compiler, ein gut eingestellter Linter und eine unterstützende IDE können hier helfen, indem sie das Rerendering optimieren und auf Fehler aufmerksam machen.

### Eigene Erfahrungen

Ich habe React, Angular und Vue ausprobiert. React gefällt mir soweit am besten. Ich mag die Nähe zum nativen Code in JSX (ohne extra template engine). Und ich mag funktionale und modulorientierte Programmierung. React hat auch Schwachstellen und Brain Fucks, aber alles in allem ist es für mich die persönlich beste Wahl.
