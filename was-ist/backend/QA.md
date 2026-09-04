<!-- markdownlint-disable MD033 -->

# Questions & Answers

**Frage-Antwort-Runden mit Cope zum Thema Backend.**

## Inhaltsverzeichnis

- [Was ist ein Failover?](#was-ist-ein-failover)

## Fragen

### Was ist ein Failover?

<details>
<summary>Ein Failover ist die automatische Umschaltung von einem primären System auf ein Standby-System, sobald das Hauptsystem ausfällt.</summary>

Ein Failover (Ausfallsicherung) sichert die ständige Erreichbarkeit von Diensten ohne menschliches Eingreifen. Es wird eingesetzt, damit ein Ausfall nicht direkt zu einem nicht erreichbaren System führt.

So funktioniert ein Failover:

- **Primärsystem:** Erledigt im Normalbetrieb alle Aufgaben.
- **Standby-System:** Läuft im Hintergrund mit und wartet.
- **Heartbeat-Signal:** Prüft regelmäßig, ob das Hauptsystem erreichbar ist.
- **Umschaltung:** Fällt das Signal aus, übernimmt das Backup-System automatisch.

</details>
