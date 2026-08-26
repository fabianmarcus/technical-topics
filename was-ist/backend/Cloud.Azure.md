# Microsoft Azure

## Ressource

In Azure steht Ressource für einen eingerichteten Dienst jeglicher Art.

Bei uns zuletzt zum Beispiel ein Frontend als Statische Web App, ein ExpressJS-Backend als App Service, eine MongoDB und ein E-Mail-Dienst. Darunter fallen aber auch das Azure Abonnement selbst, eine eingerichtete DNS-Zone oder ein App Service-Plan.

## DNS-Zone

Bislang kannte ich es so, dass man für seine Domain bei einem externen Domain-Registrar (z. B. IONOS, GoDaddy, etc.) die IP-Adresse seines Servers hinterlegt, damit die Domain auf den Server zeigt. Bei einem Aufruf der Domain durch einen Browser wird dann der Nameserver des Domain-Registrars kontaktiert, der wiederum die IP-Adresse des eigenen Servers zurückliefert. Der Browser kann dann über die IP die Verbindung zum Server aufbauen. Bei Azure läuft das etwas anders. Man hat (auch) die Möglichkeit, eine sogenannte DNS-Zone einzurichten.

Eine DNS-Zone in Azure ist ein Namensraum für die Verwaltung von eigenen DNS-Einträgen wie A-Records, CNAME-Records, MX-Records usw. Sie ist quasi ein eigener kleiner Nameserver innerhalb der eigenen Azure Umgebung für eigene Domains.

Azure DNS, der globale DNS-Dienst von Azure, sorgt anschließend automatisch dafür, dass die DNS-Zone für alle verfügbar ist. Die DNS-Zone ist dann über eigene Nameserver-Adressen erreichbar. Diese Nameserver-Adressen müssen anschließend bei einem übergeordneten Nameserver oder dem Domain-Registrar, bei dem die eigene Domain registriert ist, hinterlegt werden.
