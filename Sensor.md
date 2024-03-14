## Automatisierte Vorgänge mithilfe von CronJob und Bash

Um die Anforderungen zu erfüllen, benötigen Sie ein Bash-Skript, das die XML-Dateien von einem Quellordner in einen Zielordner kopiert und sie anschließend im Quellordner archiviert. Zudem müssen Sie einen Cronjob einrichten, um dieses Skript automatisch auszuführen. Hier ist eine mögliche Lösung:

### Schritt 1: Bash-Skript erstellen

###########################################

Erstellen Sie eine Datei mit dem Namen sensor.sh und fügen Sie folgenden Inhalt ein:

##########################################
```bash
# !/bin/bash

# Quellordner für XML-Dateien

SOURCE_DIR="/home/nazizi/shared/sensor_files_incomming"

# Zielordner für archivierte XML-Dateien

ARCHIVE_DIR="$SOURCE_DIR/archived"
EXPORT_DIR="/home/nazizi/shared/sensor_files_exports"

# Überprüfen, ob der Archivordner existiert, andernfalls erstellen

if [ ! -d "$ARCHIVE_DIR" ]; then
    mkdir -p "$ARCHIVE_DIR"
fi

# Durchsuchen des Quellordners nach XML-Dateien

for file in "$SOURCE_DIR"/*.xml; do
    if [ -f "$file" ]; then
        # Extrahieren von Dateiname und Erweiterung
        filename=$(basename -- "$file")
        extension="${filename##*.}"
        filename="${filename%.*}"
        # Zeitstempel hinzufügen
        timestamp=$(date +"%Y%m%d_%H%M%S")
        # Neuer Dateiname mit Zeitstempel
        new_filename="$timestamp"_"$filename.$extension"
        # Kopieren der Datei in den Zielordner mit dem neuen Namen
        cp "$file" "$EXPORT_DIR"
        mv "$file" "$ARCHIVE_DIR/$new_filename"
        # Optional: Ausgabe für Protokollierung
        echo "Datei $filename.xml wurde erfolgreich archiviert als $new_filename"
    fi
done
```

###################################################

## Schritt 2: Cronjob einrichten

######################
Öffnen Sie die Cronjobs-Konfigurationsdatei mit dem Befehl crontab -e und fügen Sie die folgenden Zeilen am Ende der Datei hinzu:

#########################
```Bash
# Führe das Skript jede Minute aus (Testzwecke)

* * * * * /bin/bash /pfad/zum/skript/sensordaten.sh

# Führe das Skript alle fünf Minuten zur vollen Stunde aus

5 ** ** /bin/bash /pfad/zum/skript/sensordaten.sh
```
###########################
Stellen Sie sicher, dass Sie /pfad/zum/skript/sensordaten.sh durch den tatsächlichen Pfad zu Ihrem Bash-Skript ersetzen.
##########################
Python Script

########################
---
```python
# !/usr/bin/python

import os
import shutil
from datetime import datetime

SOURCE_DIR = "/home/nazizi/shared/sensor_files_incomming"
ARCHIVE_DIR = os.path.join(SOURCE_DIR, "archived")
EXPORT_DIR = "/home/nazizi/shared/sensor_files_exports"

for filename in os.listdir(SOURCE_DIR):
  if filename.endswith(".xml"):
    filepath = os.path.join(SOURCE_DIR, filename)
    timestamp = datetime.now().strftime("%Y%m%d_%H%M%S")
    new_filename = f"{timestamp}_{filename}"

    # Kopiere die Datei in den Export Ordner
    shutil.copy(filepath, os.path.join(EXPORT_DIR, filename))

    # Verschiebe die Datei in den Archiv Ordner
    shutil.move(filepath, os.path.join(ARCHIVE_DIR, new_filename))

    print(f"Datei {filename} wurde erfolgreich archiviert als {new_filename}")
```