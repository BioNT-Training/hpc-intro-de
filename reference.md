---
permalink: /reference/
title: Wissensdatenbank
---


### Kurzreferenz oder "Cheat Sheets" für Warteschlangensystem-Befehle

Suchen Sie online nach der für Sie passenden Lösung, aber hier sind einige für den
Anfang:

- [Slurm Zusammenfassung](https://slurm.schedmd.com/pdfs/summary.pdf) von SchedMD
- [Torque/PBS
  Zusammenfassung](https://gif.biotech.iastate.edu/torque-pbs-job-management-cheat-sheet)
  von Iowa State
- [Übersetzen zwischen Slurm und PBS](https://www.msi.umn.edu/slurm/pbs-conversion) von
  der Universität von Minnesota

### Einheiten und Sprache

Der Speicher und die Festplatte eines Computers werden in Einheiten gemessen, die
*Bytes* genannt werden (ein Byte sind 8 Bits). Da die heutigen Dateien und der Speicher
im Vergleich zu historischen Standards sehr groß geworden sind, werden die Volumen mit
[SI](https://en.wikipedia.org/wiki/International_System_of_Units) angegeben. 1000 Bytes
sind also ein Kilobyte (kB), 1000 Kilobytes sind ein Megabyte (MB), 1000 Megabytes sind
ein Gigabyte (GB) usw.

Die Geschichte und der allgemeine Sprachgebrauch haben diese Notation jedoch mit einer
anderen Bedeutung vermischt. Wenn die Leute "Kilobyte" sagen, meinen sie stattdessen
1024 Bytes. In diesem Sinne ist ein Megabyte 1024 Kilobytes.

Um diese Unklarheit zu beseitigen, standardisiert das [International System of
Quantities](https://en.wikipedia.org/wiki/International_System_of_Quantities) die
*binären* Präfixe (mit Basis 2<sup>10</sup>\=1024) durch die Präfixe Kibi (ki), Mebi
(Mi), Gibi (Gi), etc. Für weitere Einzelheiten siehe [hier]
(https://en.wikipedia.org/wiki/Binary_prefix).

### "No such file or directory" oder "symbol 0096" Fehler

`scp` und `rsync` können eine verwirrende Fehlermeldung über Dateien ausgeben, die sehr
wohl existieren. Eine Quelle für diese Fehler ist das Kopieren und Einfügen von
Befehlszeilenargumenten aus Webbrowsern, bei denen die Doppelstrich-Zeichenkette `--`
als em-dash-Zeichen "-" (oder en-dash "-", oder Querstrich `―`) dargestellt wird.
Anstatt die Übertragungsrate in Echtzeit anzuzeigen, schlägt beispielsweise der folgende
Befehl auf mysteriöse Weise fehl.

```bash
{{ site.local.prompt }} rsync —progress my_precious_data.txt {{ site.remote.user }}@{{ site.remote.login }}
rsync: link_stat "/home/{{ site.local.user }}/—progress" failed:
No such file or directory (2)
rsync error: some files/attrs were not transferred (see previous errors)
(code 23) at main.c(1207) [sender=3.1.3]
```

Der richtige Befehl, der sich nur durch zwei Zeichen unterscheidet, ist erfolgreich:

```bash
{{ site.local.prompt }} rsync --progress my_precious_data.txt {{ site.remote.user }}@{{ site.remote.login }}
```

Wir haben unser Bestes getan, um alle Befehle in Codeblöcke zu verpacken, was diese
subtile Umwandlung verhindert. Wenn Sie auf diesen Fehler stoßen, öffnen Sie bitte ein
Issue oder Pull Request im Lesson Repository, um anderen zu helfen, ihn zu vermeiden.

### Interaktives Übertragen von Dateien mit `sftp`

`scp` ist nützlich, aber was ist, wenn wir nicht genau wissen, wohin wir übertragen
wollen? Oder vielleicht sind wir einfach noch nicht sicher, welche Dateien wir
übertragen wollen.`sftp` ist eine interaktive Methode zum Herunter- und Hochladen von
Dateien. Verbinden wir uns mit einem Cluster, indem wir `sftp` benutzen - Sie werden
feststellen, dass es genauso funktioniert wie SSH:

```bash
{{ site.local.prompt }} sftp yourUsername@remote.computer.address
```

Dies startet etwas, das eine Bash-Shell zu sein scheint (obwohl unser Prompt `sftp>`
sagt). Wir haben jedoch nur Zugriff auf eine begrenzte Anzahl von Befehlen. Wir können
sehen, welche Befehle mit `help` verfügbar sind:

```bash
sftp> help
```

```output
Available commands:
bye                                Quit sftp
cd path                            Change remote directory to 'path'
chgrp grp path                     Change group of file 'path' to 'grp'
chmod mode path                    Change permissions of file 'path' to 'mode'
chown own path                     Change owner of file 'path' to 'own'
df [-hi] [path]                    Display statistics for current directory or
                                   filesystem containing 'path'
exit                               Quit sftp
get [-afPpRr] remote [local]       Download file
reget [-fPpRr] remote [local]      Resume download file
reput [-fPpRr] [local] remote      Resume upload file
help                               Display this help text
lcd path                           Change local directory to 'path'
lls [ls-options [path]]            Display local directory listing
lmkdir path                        Create local directory
ln [-s] oldpath newpath            Link remote file (-s for symlink)
lpwd                               Print local working directory
ls [-1afhlnrSt] [path]             Display remote directory listing

# omitted further output for clarity
```

Beachten Sie das Vorhandensein mehrerer Befehle, die sich auf local und remote beziehen.
Wir sind tatsächlich mit zwei Computern gleichzeitig verbunden (mit zwei
Arbeitsverzeichnissen!).

Um unser entferntes Arbeitsverzeichnis anzuzeigen:

```bash
sftp> pwd
```

```output
Remote working directory: /global/home/yourUsername
```

Um unser lokales Arbeitsverzeichnis anzuzeigen, fügen wir ein `l` vor den Befehl:

```bash
sftp> lpwd
```

```output
Local working directory: /home/jeff/Documents/teaching/hpc-intro
```

Das gleiche Muster folgt für alle anderen Befehle:

- `ls` zeigt den Inhalt unseres entfernten Verzeichnisses, während `lls` den Inhalt
  unseres lokalen Verzeichnisses anzeigt.
- `cd` ändert das entfernte Verzeichnis, `lcd` ändert das lokale.

Um eine Datei hochzuladen, tippen wir `put some-file.txt` (Tabulator-Vervollständigung
funktioniert hier).

```bash
sftp> put config.toml
```

```output
Uploading config.toml to /global/home/yourUsername/config.toml
config.toml                                  100%  713     2.4KB/s   00:00
```

Um eine Datei herunterzuladen, tippen wir `get some-file.txt`:

```bash
sftp> get config.toml
```

```output
Fetching /global/home/yourUsername/config.toml to config.toml
/global/home/yourUsername/config.toml        100%  713     9.3KB/s   00:00
```

Und wir können Dateien rekursiv setzen/holen, indem wir einfach `-r` hinzufügen.
Beachten Sie, dass das Verzeichnis vorher vorhanden sein muss.

```bash
sftp> mkdir content
sftp> put -r content/
```

```output
Uploading content/ to /global/home/yourUsername/content
Entering content/
content/scheduler.md              100%   11KB  21.4KB/s   00:00
content/index.md                  100% 1051     7.2KB/s   00:00
content/transferring-files.md     100% 6117    36.6KB/s   00:00
content/.transferring-files.md.sw 100%   24KB  28.4KB/s   00:00
content/cluster.md                100% 5542    35.0KB/s   00:00
content/modules.md                100%   17KB 158.0KB/s   00:00
content/resources.md              100% 1115    29.9KB/s   00:00
```

Um zu beenden, tippen wir `exit` oder `bye`.





