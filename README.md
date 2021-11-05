# U-Labs Raspberry Pi Ansible
Dieses Repository enthält Ansible-Playbooks, um die Inhalte der U-Labs [ Portal-Artikel](https://u-labs.de/portal/category/raspberry-pi/) sowie [YouTube-Videos](https://www.youtube.com/channel/UCojIU5d16KMXYcsjPkovmqQ/videos) automatisiert auf euren Raspberry Pi (Raspberry Pi OS) zu bringen.

## Was ist Ansible?
Ein quelloffenes Automatisierungswerkzeug: Man definiert einen gewünschten Zielzustand, wie z.B. _Paket X soll installiert sein_ oder _Zeile Z soll an dieser Position in einer Konfigurationsdatei stehen_. Ansible verbindet sich per SSH, prüft den Zustand und nimmt ggf. die notwendigen Änderungen vor, um das Ziel zu erreichen. Weitere Software ist nicht erforderlich, neben einer SSH-Verbindung wird lediglich Python auf dem Zielsystem benötigt.

Durch die Automatisierung reduziert sich vor allem bei mehreren Systemen der Aufwand und alle werden identisch eingerichtet. Da Rollen und Playbooks in Yaml geschrieben werden, entsteht automatisch eine Dokumentation. In Kombination mit Git lassen sich Änderungen leicht versionieren und damit nachvollziehen.

## Erste Schritte: Wie kann ich Ansible nutzen?
Du benötigst einen Linux-Host. Das kann ein Arbeits-PC oder Laptop sein, alternativ tut es auch eine VM bzw. ein Server. Zur Not kannst du auch Ansible auf dem Pi selbst installieren, sodass der Pi sich selbst provisioniert. Auf der Arbeitsmaschine muss Ansible installiert sein:

```bash
# Arch und derivate
sudo pacman -Syu; sudo pacman -S ansible
# Debian/Ubuntu und Derivate
sudo apt install ansible
```
Am einfachsten und sichersten funktioniert Ansible mit SSH-Keys: Eure Arbeitsmaschine benötigt ein Schlüsselpaar und der öffentliche Schlüssel muss auf dem Pi eingetragen sein. Falls ihr kein Schlüsselpaar habt (standardmäßig in `~/.ssh`), zuvor eines erstellen:

```bash
ssh-keygen -t rsa -b 4096
```
Und den öffentlichen Schlüssel auf den Pi kopieren, wofür ihr im Regelfall einmal das Passwort eingeben müsst - außer ihr habt bereits einen SSH-Schlüssel beim Einrichten des Pis hinterlegt.

```bash
ssh-copy-id pi@raspberry
```
Nun sollte `ssh pi@raspberry` eine Verbindung über den SSH-Schlüssel auf den Pi ermöglichen, wobei `raspberry` entsprechend durch den Hostname oder die Ip-Adrese deines Pis ersetzt werden muss, falls dieser angepasst wurde.

### Starten des ersten Playbooks
Ansible verwaltet und Gruppiert die Systeme in einem Inventory. Für den Einstieg mit dem ersten Pi ist das nicht nötig und etwas Overkill, hier kann man stattdessen einfach den Hostname des Pi als Inventory angeben. Bei größeren Umgebungen würde ich aber empfehlen, ein Inventory anzulegen.

```bash
ansible-playbook -i raspberry, playbooks/14_docker.yml
```

`raspberry` ist wieder der Hostname, das Komma danach darf bei dieser Vereinfachung nicht vergessen werden. Anschließend folgt der Pfad zum gewünschten Playbook, hier nehmen wir beispielsweise das Docker-Playbook. Das Ausführen des Befehles installiert also Docker auf dem Raspberry Pi, sofern noch nicht vorhanden.

Anhand der Ausgabe und Zusammenfassung erkennt ihr, ob Ansible den gewünschten Zielzustand bereits vorgefunden hat (_Ok_), ob Aufgaben nicht notwendig waren und daher übersprungen werden konnten (_skipped_) oder natürlich ob Fehler aufgetreten sind. Vorgenommene Änderungen sind als _changed_ gekennzeichnet. In diesem Beispiel ist Docker bereits installiert und man sieht, dass alle Schritte _Ok_ oder _Übersprungen_ wurden, d.H. keine Änderungen:

```
PLAY [Docker auf dem Raspberry Pi OS installieren] ****************************************************************************************

TASK [Gathering Facts] ********************************************************************************************************************
ok: [testpi]

TASK [Pruefen, ob Docker installiert ist] *************************************************************************************************
ok: [testpi]

TASK [set_fact] ***************************************************************************************************************************
ok: [testpi]

TASK [Installationsskript herunterladen] **************************************************************************************************
skipping: [testpi]

TASK [Docker installieren] ****************************************************************************************************************
skipping: [testpi]

TASK [pi zur Docker-Berechtigungsgruppe hinzufuegen] **************************************************************************************
ok: [testpi]

PLAY RECAP ********************************************************************************************************************************
testpi                     : ok=4    changed=0    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0
```

Auf dem Pi kann man sich nun einloggen und die installierten Komponenten (hier im Beispiel Docker) nutzen.