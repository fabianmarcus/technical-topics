# GitHub Copilot

## Visual Studio Code Extension

Zum KI unterstützten Entwickeln nutze ich GitHub Copilot Chat - eine Visual Studio Code Extension. Mit CLI-Versionen habe ich bislang noch nicht viel gearbeitet; das war aufgrund der umfangreichen Funktionalität und der tiefen Integration der Extension in die IDE nicht notwendig. Die Extension ist für mich die beste Möglichkeit, KI-gestützt zu entwickeln. Die Bedienung ist meistens sehr intuitiv.

- Inline Vorschläge sind quasi nativ vorhanden.
- Einfacher Wechsel zwischen verschiedenen Modellen, egal ob Claude, GPT oder Gemini.
- Einfaches Umschalten zwischen "Ask"-, "Plan"- und "Agent"-Modus.
- Hinterlegbare Copilot Instructions und Skills, um Code Style und Code Generierung regelbasiert vorhersagbar zu machen.
- und einiges mehr...

## Vibe Coding vs. Iteratives Feature Prompting

Ich "Vibe Code" eigentlich nicht. Dabei rödelt der Agent wohl stundenlang, in der Hoffnung, dass am Ende ein funktionierendes Ergebnis herauskommt. Entweder ist anschließend unklar, was enthalten ist oder es muss ein riesiger Merge Request mit vielen Dateien und noch mehr Zeilen Code überprüft werden. Die Korrektur dessen erscheint mir persönlich aufwendiger, als direkt am Code zu bleiben.

Ich bevorzuge iteratives Feature Prompting. Hierbei wird eine Anwendung oder ein größeres Feature Schritt für Schritt gepromptet. Die Instructions und Skills sind so hinterlegt, dass der Code im gewünschten Stil generiert wird. Jede Iteration bringt eine in sich abgeschlossene Code-Änderung mit sich, ändert aber nur wenige Dateien, die anschließend direkt reviewed werden. Wird dabei klar, dass der Agent das Prompt anders verstanden hat oder die Umsetzung nicht korrekt ist, kann direkt korrigiert werden.

Die Änderungen jeder Iteration werden durch Git Diff gut nachvollziehbar sichtbar. Danach wird committed und die nächste Iteration angegangen. So entsteht ein kontinuierlicher Entwicklungsprozess, der den Code Schritt für Schritt erweitert und verbessert. Das brigt zwar nicht den Luxus, einfach abwarten und etwas anderes tun zu können, während der Agent arbeitet. Der Arbeitsprozess wird aber dennoch extrem beschleunigt, da das Tippen, Recherchieren und Refaktorieren durch den Agenten übernommen wird. Der Entwickler kann sich komplett auf die fachliche bzw. theoretische Umsetzung konzentrieren.

Der [Plan-Modus](https://code.visualstudio.com/docs/agents/run/planning) der Copilot Extension vereinfacht das sogar weiter. Wird dem Agenten mitgeteilt, dass er den Plan wie oben beschrieben gestalten und speichern soll, kann jederzeit wieder in die angedachte Umsetzung eingestiegen werden. Der Agent kennt quasi schon vorher das Große-Ganze und kann auf Änderungen reagieren, ohne dass der Entwickler alles neu erklären muss.

## Instructions

["Copilot Instructions"](https://code.visualstudio.com/docs/agent-customization/custom-instructions) sind ein Feature der [GitHub Copilot-Extension](https://code.visualstudio.com/docs/agents/overview). Damit lassen sich generelle Anweisungen für Agenten hinterlegen, die sie bei jeder Code-Generierung berücksichtigen, indem sie sie in den Prompt-Kontext laden. Das kann Code Style, Code Struktur und sonstige Anweisungen aller Art betreffen.

Meine Instructions sind hier hinterlegt: [Copilot Instructions](https://github.com/fabianmarcus/copilot/blob/main/copilot-instructions.md)

Sie liegen in einem eigenen [Repository](https://github.com/fabianmarcus/copilot) und können als Git Submodule in jedes Projekt eingebunden werden. So kann ich den Code Style und die Code Struktur meiner privaten Projekte projektübergreifend einheitlich halten.

## Skills

Wie [meine Copilot Instructions](#instructions) sind auch meine Agent Skills in dem [Repository](https://github.com/fabianmarcus/copilot) hinterlegt. [Copilot Agent Skills](https://code.visualstudio.com/docs/agent-customization/agent-skills) funktionieren ähnlich wie Instructions, sind aber auf konkrete Anwendungsfälle zugeschnitten und werden nur bei Bedarf in den Prompt-Kontext eingebunden. Sie können z. B. ganze konkrete Anweisungen für die Code-Generierung für ein bestimmtes Framework enthalten, die für die allgemeinen Instructions zu speziell wären. Auch die Skills lassen sich über ein Git Submodule in jedes Projekt einbinden.

Meine Skills sind auch hier hinterlegt: [Copilot Skills](https://github.com/fabianmarcus/copilot)
