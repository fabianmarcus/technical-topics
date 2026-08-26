# Cloud Allgemein

Cloud Computing ist eine weitere Abstraktion im Client-Server-Modell.

Es gibt mehrere Möglichkeiten seine Anwendung ins Internet zu bringen.

- Sie kann auf einem eigenen oder gemieteten physischen Server laufen.
- Sie kann auf einem verwalteten physischen Server neben weiteren Anwendungen laufen (Shared Hosting).
- Sie kann in einer autarken virtuellen Maschine laufen, die gemeinsam mit anderen virtuellen Maschinen auf einem physischen Server läuft (Virtualisierung).
- Sie kann in einem Container laufen, der gemeinsam mit anderen Containern auf einem physischen Server läuft (Containerisierung).
- Sie kann ausschließlich Serverless Functions bereitstellen, die nur bei Bedarf bzw. auf Anfrage ausgeführt werden. (Serverless Computing).

Nach meinem Verständnis bedeutet Cloud Computing, sich seine eigene Infrastruktur per grafischer Oberfläche oder per CLI **detailliert und filigran** zusammenzustellen, zu konfigurieren und zu verwalten, ohne Kenntnis über die darunterliegende physische Hardware haben zu müssen. Von der virtuellen Maschine mit Betriebssystem bis runter zu einer einzelnen virtuellen Funktion, die über eine URL aufgerufen wird, kann alles gebucht werden. Je nach Bedarf können weitere Dienste dazugebucht werden.

Ein weiterer Aspekt des Cloud Computing ist die mögliche bedarfsorientierte Abrechnung. Die bereitgestellten Dienste können sehr fein nach Nutzung abgerechnet werden - pro Sekunde oder noch genauer. Es kann aber auch klassisch eine monatliche Pauschale sein, die sich nach der Anzahl der bereitgestellten Dienste richtet.

Das Prinzip ist im Endeffekt gleich geblieben, nur ist es wesentlich einfacher, detaillierter und umfangreicher geworden, Ressourcen bedarfsgerecht zusammenstellen und mieten zu können.

## Serverless Functions / Serverless Computing

Serverless Functions sind, wie der Name verrät, Funktionen ohne Server. Was natürlich nicht ganz stimmt. Sie laufen schon auf einem Server; von dem hat man selbst nur keine Ahnung. Er wird vom Cloud-Anbieter transparent bereitgestellt und verwaltet.

In der Regel lassen sich solche Funktionen über CLI-Befehle oder eine Web-GUI anlegen, konfigurieren und deployen. Sie werden dann über eine URL aufgerufen und laufen nur für die Dauer der Ausführung. Die Serverless Function tut also nur genau das was sie soll, so lange wie es dauert das zu tun. Danach wird sie wieder beendet und es fallen keine Kosten mehr an. Sie ist technisch nur für die Dauer der Ausführung aktiv.

## Skalierung

Cloud-Dienste bieten die Möglichkeit, Anwendungen und Dienste automatisch zu skalieren. Einerseits können Ressourcen einfach über eine grafische Oberfläche manuell hoch- und runter skaliert werden. Andererseits ist es auch möglich, die Skalierung automatisch durch den Cloud-Dienst durchführen zu lassen. Bei mehr oder weniger Last werden dann automatisch mehr oder weniger Ressourcen bereitgestellt.

## Eigene Erfahrungen

Ich bin meistens in der Google Cloud Platform unterwegs. Damit habe ich bisher die meisten Erfahrungen gesammelt. Ich habe aber auch schon mal in eine Azure Umgebung reingeguckt. Das Prinzip sollte aber überall gleich sein. Die Unterschiede liegen eher in der Bedienung und den Angeboten.
