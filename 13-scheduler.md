---
title: Grundlagen des Schedulers
teaching: 45
exercises: 30
---




::::::::::::::::::::::::::::::::::::::: objectives

- Übermittelt ein einfaches Skript an den Cluster.
- Überwachen Sie die Ausführung von Aufträgen mit Hilfe von Kommandozeilenwerkzeugen.
- Überprüfen Sie die Ausgabe- und Fehlerdateien Ihrer Aufträge.
- Finden Sie den richtigen Ort, um große Datensätze auf dem Cluster abzulegen.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Was ist ein Scheduler und warum braucht ein Cluster einen?
- Wie starte ich ein Programm zur Ausführung auf einem Rechenknoten im Cluster?
- Wie kann ich die Ausgabe eines Programms erfassen, das auf einem Knoten im Cluster ausgeführt wird?

::::::::::::::::::::::::::::::::::::::::::::::::::

## Job Scheduler

Ein HPC-System kann Tausende von Knoten und Tausende von Benutzern haben. Wie wird entschieden, wer was und wann bekommt? Wie wird sichergestellt, dass eine Aufgabe mit den erforderlichen Ressourcen ausgeführt wird? Diese Aufgabe wird von einer speziellen Software, dem *Scheduler*, übernommen. Auf einem HPC-System verwaltet der Scheduler, welche Aufgaben wo und wann ausgeführt werden.

Die folgende Abbildung vergleicht diese Aufgaben eines Job Schedulers mit denen eines Kellners in einem Restaurant. Wenn Sie sich vorstellen können, dass Sie eine Weile in einer Schlange warten mussten, um in ein beliebtes Restaurant zu kommen, dann verstehen Sie jetzt vielleicht, warum Ihr Job manchmal nicht sofort startet, wie bei Ihrem Laptop.

![](fig/restaurant_queue_manager.svg){alt="Vergleiche einen Job Scheduler mit einem Kellner in einem Restaurant" max-width="75%"}

Der in dieser Lektion verwendete Scheduler ist Slurm. Obwohl Slurm nicht überall verwendet wird, ist die Ausführung von Aufträgen ziemlich ähnlich, unabhängig davon, welche Software verwendet wird. Die genaue Syntax kann sich ändern, aber die Konzepte bleiben die gleichen.

## Ausführen eines Batch-Jobs

Die einfachste Anwendung des Schedulers ist die nicht-interaktive Ausführung eines Befehls. Jeder Befehl (oder eine Reihe von Befehlen), den Sie auf dem Cluster ausführen möchten, wird als *Job* bezeichnet, und der Prozess der Verwendung eines Schedulers zur Ausführung des Jobs wird *Batch Job Submission* genannt.

In diesem Fall handelt es sich bei dem Auftrag, den wir ausführen wollen, um ein Shell-Skript - im Wesentlichen eine Textdatei mit einer Liste von UNIX-Befehlen, die nacheinander ausgeführt werden sollen. Unser Shell-Skript wird aus drei Teilen bestehen:

- In der allerersten Zeile fügen Sie `#!/bin/bash` ein. Das `#!` (ausgesprochen "hash-bang" oder "shebang") sagt dem Computer, welches Programm den Inhalt dieser Datei verarbeiten soll. In diesem Fall teilen wir ihm mit, dass die folgenden Befehle für die Kommandozeilen-Shell geschrieben sind (in der wir bisher alles gemacht haben).
- Irgendwo unterhalb der ersten Zeile fügen wir einen `echo`-Befehl mit einer freundlichen Begrüßung ein. Wenn es ausgeführt wird, gibt das Shell-Skript im Terminal alles aus, was nach `echo` kommt.
  - `echo -n` druckt alles, was folgt, *ohne* die Zeile mit einem Zeilenumbruch zu beenden.
- In der letzten Zeile rufen wir den Befehl `hostname` auf, der den Namen des Rechners ausgibt, auf dem das Skript ausgeführt wird.

```bash
[yourUsername@login1 ~] nano example-job.sh
```

```bash
#!/bin/bash

echo -n "This script is running on "
hostname
```

::::::::::::::::::::::::::::::::::::::: challenge

## Erstellen unseres Testjobs

Führen Sie das Skript aus. Wird es auf dem Cluster oder nur auf unserem Anmeldeknoten ausgeführt?

::::::::::::::: solution

## Lösung

```bash
[yourUsername@login1 ~] bash example-job.sh
```

```output
This script is running on login1
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

Dieses Skript lief auf dem Anmeldeknoten, aber wir wollen die Vorteile der Rechenknoten nutzen: Wir brauchen den Scheduler, um `example-job.sh` in die Warteschlange zu stellen, damit er auf einem Rechenknoten läuft.

Um diese Aufgabe an den Scheduler zu senden, benutzen wir den Befehl `sbatch`. Dies erzeugt einen *Job*, der das *Skript* ausführt, wenn er an einen Rechenknoten *gesendet* wird, den das Warteschlangensystem als verfügbar für die Ausführung der Arbeit identifiziert hat.

```bash
[yourUsername@login1 ~] sbatch  example-job.sh
```


```output
Submitted batch job 7
```

Und das ist alles, was wir tun müssen, um einen Auftrag abzuschicken. Unsere Arbeit ist getan - jetzt übernimmt der Scheduler und versucht, den Auftrag für uns auszuführen. Während der Auftrag darauf wartet, ausgeführt zu werden, wird er in eine Liste von Aufträgen aufgenommen, die *Warteschlange*. Um den Status unseres Jobs (oder Auftrags) zu überprüfen, können wir die Warteschlange mit dem Befehl `squeue -u yourUsername` überprüfen.

```bash
[yourUsername@login1 ~] squeue -u yourUsername
```

```output
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
    9 cpubase_b example-   user01  R       0:05      1 node1
```

Wir können alle Details zu unserem Auftrag sehen, vor allem, dass er sich im Status `R` oder `RUNNING` befindet. Manchmal müssen unsere Aufträge in einer Warteschlange warten (`PENDING`) oder haben einen Fehler (`E`).

:::::::::::::::::::::::::::::::::::::: discussion

## Wo ist die Ausgabe?

Auf dem Login-Knoten gab dieses Skript eine Ausgabe auf dem Terminal aus -- aber jetzt, wenn `squeue` anzeigt, dass der Job beendet ist, wurde nichts auf dem Terminal ausgegeben.

Die Ausgabe eines Cluster-Jobs wird normalerweise in eine Datei in dem Verzeichnis umgeleitet, aus dem Sie ihn gestartet haben. Verwenden Sie `ls` zum Suchen und `cat` zum Lesen der Datei.

::::::::::::::::::::::::::::::::::::::::::::::::::

## Anpassen eines Jobs

Der Auftrag, den wir gerade ausgeführt haben, verwendete alle Standardoptionen des Schedulers. In einem realen Szenario ist das wahrscheinlich nicht das, was wir wollen. Die Standardoptionen stellen ein vernünftiges Minimum dar. Wahrscheinlich benötigen wir mehr Kerne, mehr Arbeitsspeicher, mehr Zeit und andere spezielle Überlegungen. Um Zugang zu diesen Ressourcen zu erhalten, müssen wir unser Jobskript anpassen.

Kommentare in UNIX-Shell-Skripten (gekennzeichnet durch `#`) werden normalerweise ignoriert, aber es gibt Ausnahmen. Zum Beispiel gibt der spezielle Kommentar `#!` am Anfang von Skripten an, welches Programm benutzt werden soll, um es auszuführen (typischerweise steht hier `#!/usr/bin/env bash`). Scheduler wie Slurm haben auch einen speziellen Kommentar, um spezielle Scheduler-spezifische Optionen zu kennzeichnen. Obwohl diese Kommentare von Scheduler zu Scheduler unterschiedlich sind, ist der spezielle Kommentar von Slurm `#SBATCH`. Alles, was dem Kommentar `#SBATCH` folgt, wird als Anweisung an den Scheduler interpretiert.

Lassen Sie uns dies an einem Beispiel verdeutlichen. Standardmäßig ist der Name eines Jobs der Name des Skripts, aber mit der Option `-J` kann man den Namen eines Jobs ändern. Fügen Sie eine Option in das Skript ein:

```bash
[yourUsername@login1 ~] cat example-job.sh
```

```bash
#!/bin/bash
#SBATCH -J hello-world

echo -n "This script is running on "
hostname
```

Übermitteln Sie den Auftrag und überwachen Sie seinen Status:

```bash
[yourUsername@login1 ~] sbatch  example-job.sh
[yourUsername@login1 ~] squeue -u yourUsername
```

```output
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   10 cpubase_b hello-wo   user01  R       0:02      1 node1
```

Fantastisch, wir haben den Namen unseres Jobs erfolgreich geändert!

### Ressourcenanfragen

Was ist mit wichtigeren Änderungen, wie der Anzahl der Kerne und des Speichers für unsere Jobs? Eine Sache, die bei der Arbeit an einem HPC-System absolut entscheidend ist, ist die Angabe der für die Ausführung eines Auftrags erforderlichen Ressourcen. So kann der Scheduler die richtige Zeit und den richtigen Ort für die Ausführung unseres Auftrags finden. Wenn Sie die Anforderungen (z. B. die benötigte Zeit) nicht angeben, werden Sie wahrscheinlich mit den Standardressourcen Ihrer Website arbeiten, was wahrscheinlich nicht das ist, was Sie wollen.

Es folgen mehrere wichtige Ressourcenanfragen:

- `--ntasks=<ntasks>` oder `-n <ntasks>`: Wie viele CPU-Kerne benötigt Ihr Job insgesamt?

- `--time <days-hours:minutes:seconds>` oder `-t <days-hours:minutes:seconds>`: Wie viel reale Zeit (Walltime) wird Ihr Job für die Ausführung benötigen? Der Teil `<days>` kann weggelassen werden.

- `--mem=<megabytes>`: Wie viel Speicher auf einem Knoten benötigt Ihr Auftrag in Megabyte? Sie können auch Gigabytes angeben, indem Sie ein kleines "g" anhängen (Beispiel: `--mem=5g`)

- `--nodes=<nnodes>` oder `-N <nnodes>`: Auf wie vielen separaten Rechnern muss Ihr Auftrag laufen? Beachten Sie, dass, wenn Sie `ntasks` auf eine Zahl setzen, die größer ist als die, die eine Maschine bieten kann, Slurm diesen Wert automatisch einstellt.

Beachten Sie, dass das bloße *Anfordern* dieser Ressourcen Ihren Auftrag nicht schneller laufen lässt und auch nicht unbedingt bedeutet, dass Sie alle diese Ressourcen verbrauchen werden. Es bedeutet nur, dass sie Ihnen zur Verfügung gestellt werden. Es kann sein, dass Ihr Auftrag am Ende weniger Speicher, weniger Zeit oder weniger Knoten benötigt, als Sie angefordert haben, und er wird trotzdem ausgeführt.

Es ist am besten, wenn Ihre Anfragen die Anforderungen Ihres Auftrags genau widerspiegeln. In einer späteren Folge dieser Lektion werden wir mehr darüber sprechen, wie Sie sicherstellen können, dass Sie die Ressourcen effektiv nutzen.

::::::::::::::::::::::::::::::::::::::: challenge

## Übermittlung von Ressourcenanfragen

Ändern Sie unser `hostname`-Skript so, dass es eine Minute lang läuft, und senden Sie dann einen Job dafür an den Cluster.

::::::::::::::: solution

## Lösung

```bash
[yourUsername@login1 ~] cat example-job.sh
```

```bash
#!/bin/bash
#SBATCH -t 00:01 # timeout in HH:MM

echo -n "This script is running on "
sleep 20 # time in seconds
hostname
```

```bash
[yourUsername@login1 ~] sbatch  example-job.sh
```

Warum sind die Slurm Laufzeit und die `sleep` Zeit nicht identisch?



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

Ressourcenanforderungen sind normalerweise verbindlich. Wenn Sie diese überschreiten, wird Ihr Auftrag abgebrochen. Nehmen wir die Walltime als Beispiel. Wir fordern 1 Minute Walltime an und versuchen, einen Auftrag zwei Minuten lang laufen zu lassen.

```bash
[yourUsername@login1 ~] cat example-job.sh
```

```bash
#!/bin/bash
#SBATCH -J long_job
#SBATCH -t 00:01 # timeout in HH:MM

echo "This script is running on ... "
sleep 240 # time in seconds
hostname
```

Senden Sie den Auftrag ab und warten Sie, bis er beendet ist. Sobald er beendet ist, überprüfen Sie die Protokolldatei.

```bash
[yourUsername@login1 ~] sbatch  example-job.sh
[yourUsername@login1 ~] squeue -u yourUsername
```

```bash
[yourUsername@login1 ~] cat slurm-12.out
```

```output
This script is running on ...
slurmstepd: error: *** JOB 12 ON node1 CANCELLED AT 2021-02-19T13:55:57
DUE TO TIME LIMIT ***
```

Unser Auftrag wurde abgebrochen, weil er die angeforderte Menge an Ressourcen überschritten hat. Obwohl dies hart erscheint, handelt es sich dabei um eine Funktion. Die strikte Einhaltung der Ressourcenanforderungen ermöglicht es dem Scheduler, den bestmöglichen Platz für Ihre Jobs zu finden. Noch wichtiger ist jedoch, dass dadurch sichergestellt wird, dass ein anderer Benutzer nicht mehr Ressourcen verwenden kann, als ihm zugewiesen wurden. Wenn ein anderer Benutzer einen Fehler macht und versehentlich versucht, alle Kerne oder den gesamten Speicher eines Knotens zu nutzen, wird Slurm entweder seinen Job auf die angeforderten Ressourcen beschränken oder den Job ganz beenden. Andere Jobs auf dem Knoten sind davon nicht betroffen. Das bedeutet, dass ein Benutzer nicht die Erfahrungen anderer Benutzer durcheinander bringen kann, die einzigen Jobs, die von einem Fehler in der Planung betroffen sind, sind die eigenen.

## Abbrechen eines Jobs

Manchmal machen wir einen Fehler und müssen einen Auftrag abbrechen. Das kann man mit dem Befehl `scancel` machen. Lassen Sie uns einen Job abschicken und ihn dann mit seiner Jobnummer abbrechen (denken Sie daran, die Walltime so zu ändern, dass er lange genug läuft, damit Sie ihn abbrechen können, bevor er beendet wird).

```bash
[yourUsername@login1 ~] sbatch  example-job.sh
[yourUsername@login1 ~] squeue -u yourUsername
```

```output
Submitted batch job 13

JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
   13 cpubase_b long_job   user01  R       0:02      1 node1
```

Stornieren Sie nun den Auftrag mit seiner Auftragsnummer (die in Ihrem Terminal ausgedruckt wird). Eine saubere Rückkehr Ihrer Eingabeaufforderung zeigt an, dass die Anfrage zum Abbrechen des Auftrags erfolgreich war.

```bash
[yourUsername@login1 ~] scancel 38759
# It might take a minute for the job to disappear from the queue...
[yourUsername@login1 ~] squeue -u yourUsername
```

```output
JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
```

::::::::::::::::::::::::::::::::::::::: challenge

## Abbrechen mehrerer Jobs

Wir können auch alle unsere Aufträge auf einmal mit der Option `-u` löschen. Damit werden alle Aufträge für einen bestimmten Benutzer (in diesem Fall für Sie selbst) gelöscht. Beachten Sie, dass Sie nur Ihre eigenen Aufträge löschen können.

Versuchen Sie, mehrere Aufträge einzureichen und sie dann alle abzubrechen.

::::::::::::::: solution

## Lösung

Übermitteln Sie zunächst ein Trio von Jobs:

```bash
[yourUsername@login1 ~] sbatch  example-job.sh
[yourUsername@login1 ~] sbatch  example-job.sh
[yourUsername@login1 ~] sbatch  example-job.sh
```

Dann brechen Sie sie alle ab:

```bash
[yourUsername@login1 ~] scancel -u yourUsername
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Andere Arten von Jobs

Bis zu diesem Punkt haben wir uns auf die Ausführung von Jobs im Batch-Modus konzentriert. `Slurm` bietet auch die Möglichkeit, eine interaktive Sitzung zu starten.

Es gibt sehr häufig Aufgaben, die interaktiv erledigt werden müssen. Ein ganzes Job-Skript zu erstellen, wäre vielleicht übertrieben, aber die Menge der benötigten Ressourcen ist zu groß für einen Login-Knoten. Ein gutes Beispiel hierfür ist die Erstellung eines Genomindexes für das Alignment mit einem Tool wie [HISAT2][hisat]. Glücklicherweise können wir diese Art von Aufgaben einmalig mit `srun` ausführen.

`srun` führt einen einzelnen Befehl auf dem Cluster aus und beendet sich dann. Lassen Sie uns dies demonstrieren, indem wir den Befehl ``hostname`` mit `srun` ausführen. (Wir können einen `srun`-Job mit ``Ctrl-c`` abbrechen.)

```bash
[yourUsername@login1 ~] srun hostname
```

```output
smnode1
```

`srun` akzeptiert die gleichen Optionen wie `sbatch`. Allerdings werden diese Optionen nicht in einem Skript angegeben, sondern auf der Kommandozeile, wenn ein Job gestartet wird. Um einen Job zu starten, der 2 CPUs benutzt, könnte man z.B. den folgenden Befehl verwenden:

```bash
[yourUsername@login1 ~] srun -n 2 echo "This job will use 2 CPUs."
```

```output
This job will use 2 CPUs.
This job will use 2 CPUs.
```

Typischerweise wird die resultierende Shell-Umgebung die gleiche sein wie die für `sbatch`.

### Interaktive Jobs

Manchmal braucht man viele Ressourcen für die interaktive Nutzung. Vielleicht führen wir zum ersten Mal eine Analyse durch oder versuchen, einen Fehler zu beheben, der bei einem früheren Auftrag aufgetreten ist. Glücklicherweise macht es Slurm einfach, einen interaktiven Job mit `srun` zu starten:

```bash
[yourUsername@login1 ~] srun --pty bash
```

Sie sollten nun eine Bash-Eingabeaufforderung erhalten. Beachten Sie, dass sich die Eingabeaufforderung wahrscheinlich ändert, um Ihren neuen Standort wiederzugeben, in diesem Fall den Rechenknoten, an dem wir angemeldet sind. Sie können dies auch mit `hostname` überprüfen.

::::::::::::::::::::::::::::::::::::::::: callout

## Erstellen von Remote-Grafiken

Um die grafische Ausgabe innerhalb Ihrer Aufträge zu sehen, müssen Sie die X11-Weiterleitung verwenden. Um diese Funktion zu aktivieren, verwenden Sie die Option `-Y`, wenn Sie sich mit dem Befehl `ssh` anmelden, z.B. `ssh -Y yourUsername@cluster.hpc-carpentry.org`.

Um zu demonstrieren, was passiert, wenn Sie ein Grafikfenster auf dem entfernten Knoten erstellen, verwenden Sie den Befehl `xeyes`. Ein relativ hübsches Augenpaar sollte auftauchen (drücken Sie `Ctrl-C`, um zu stoppen). Wenn Sie einen Mac verwenden, müssen Sie XQuartz installiert (und Ihren Computer neu gestartet) haben, damit dies funktioniert.

Wenn Ihr Cluster das [slurm-spank-x11](https://github.com/hautreux/slurm-spank-x11) Plugin installiert hat, können Sie die X11-Weiterleitung innerhalb interaktiver Jobs sicherstellen, indem Sie die Option `--x11` für `srun` mit dem Befehl `srun --x11 --pty bash` verwenden.

::::::::::::::::::::::::::::::::::::::::::::::::::

Wenn Sie mit dem interaktiven Job fertig sind, geben Sie `exit` ein, um Ihre Sitzung zu beenden.

[hisat]: https://daehwankimlab.github.io/hisat2/

:::::::::::::::::::::::::::::::::::::::: keypoints

- Der Scheduler verwaltet die Aufteilung der Rechenressourcen zwischen den Benutzern.
- Ein Job ist einfach ein Shell-Skript.
- Fordern Sie *geringfügig* mehr Ressourcen an, als Sie brauchen werden.

::::::::::::::::::::::::::::::::::::::::::::::::::



