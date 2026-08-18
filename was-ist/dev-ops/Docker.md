# Docker

Docker bietet die Möglichkeit Applikationen innerhalb von virtuellen Containern laufen zu lassen. Ein Container ist dabei eine Art abgespeckte, leichtgewichtige virtuelle Maschine, die nur die Ressourcen enthält, die für die Applikation notwendig sind. Der Sinn dahinter ist, dass sich diese Container auf jeder Plattform gleich verhalten. So werden Inkonsistenzen zwischen verschiedenen Entwicklungsumgebungen, aber auch zwischen Entwicklungs- und Produktionsumgebung vermieden, die zu schweren Fehlern führen können.

Um das zu erreichen, konfiguriert man ein "Dockerfile", das Docker nutzt, um den Container aufzuziehen. In diesem Dockerfile wird zunächst ein grundlegendes Betriebssystem gesetzt (meist ein schlankes Linux). Danach werden notwendige Pakete, Umgebungsvariablen und anderer Stuff definiert, der für die Applikation notwendig ist. Am Ende wird die Applikation selbst über Schnittstellenpfade zwischen Host und Container automatisch in den Container kopiert und gestartet.

Um Konfigurationen wiederverwendbar zu machen, kann man sogenannte "Images" bauen. Ein Image ist eine Vorlage für einen Container, der für seinen Use Case bereits fertig konfiguriert ist. Mit Images und Containern verhält es sich ähnlich wie mit Klassen und Objekten in der Programmierung. Images können in einem Registry-Server gespeichert werden, um sie mit anderen Entwicklern zu teilen. Docker selbst bietet dafür den "Docker Hub" an, es gibt aber auch andere Anbieter wie GitHub oder GitLab.

Um mehrere Container als Orchester miteinander laufen zu lassen, kann man "Docker Compose" nutzen, um sie zu orchestrieren. Dazu wird eine Konfigurationsdatei `docker-compose.yml` geschrieben, die beschreibt, welche Container gestartet werden sollen und wie sie miteinander kommunizieren können.

So viel zu meinem grundlegenden Verständnis von Docker.  
Nachfolgend soll es nur noch um konkrete Learnings gehen, die ich bei der Arbeit mit Docker mache.

## Docker Compose

### profiles

Das "profiles"-Feature von Docker Compose ermöglicht es, Container nur dann zu starten, wenn sie in der Konfiguration explizit angegeben werden. Das ist praktisch, wenn man z.B. einen Container für die Datenbank nur in der Entwicklungsumgebung starten möchte, aber nicht in der Produktionsumgebung.

```yaml
# [...]
nuxt-prod:
  profiles:
    - prod # Aufruf über "docker compose --profile nuxt-prod up"
    - # Weitere Profile...
  build:
    context: .
    target: prod
  ports:
    - "3000:3000"
  environment:
    NUXT_TELEMETRY_DISABLED: "1"
# [...]
```

Dabei kann ein Profil in mehreren Services angegeben werden und ein Service mehrere Profile haben.

## Eigene Erfahrungen

Meine Dockerkenntnisse beschränken sich größtenteils auf die Nutzung während der Entwicklung. Mir ist die Konfiguration des `Dockerfile` und der `docker-compose.yml` vertraut. Und ich kenne die grundlegenden Befehle, um die Docker-Umgebung zu verwalten (Container, Images, Volumes, Network, etc.). Mir ist zwar auch klar, wie Docker in der CI/CD-Pipeline eingesetzt werden kann und auf dem Produktionsserver läuft. Ich habe das aber bislang nur einmal in kleinem Rahmen bearbeitet.
