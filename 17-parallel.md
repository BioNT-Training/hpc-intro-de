---
title: Ausführen eines parallelen Auftrags
teaching: 30
exercises: 60
---




::::::::::::::::::::::::::::::::::::::: objectives

- Installieren Sie ein Python-Paket mit `pip`
- Bereiten Sie ein Auftragsskript für das parallele Programm vor.
- Starten Sie Aufträge mit paralleler Ausführung.
- Aufzeichnung und Zusammenfassung des Zeitplans und der Genauigkeit von Aufträgen.
- Beschreiben Sie die Beziehung zwischen Job-Parallelität und Leistung.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Wie kann man eine Aufgabe parallel ausführen?
- Welche Vorteile ergeben sich aus der parallelen Ausführung?
- Wo liegen die Grenzen des Gewinns bei paralleler Ausführung?

::::::::::::::::::::::::::::::::::::::::::::::::::

Wir haben jetzt die Werkzeuge, die wir brauchen, um einen Multiprozessor-Job auszuführen. Dies ist ein sehr wichtiger Aspekt von HPC-Systemen, da die Parallelität eines der wichtigsten Werkzeuge ist, um die Leistung von Berechnungsaufgaben zu verbessern.

Wenn Sie die Verbindung getrennt haben, melden Sie sich wieder am Cluster an.

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
```

## Installieren Sie das Amdahl-Programm

Nachdem wir den Amdahl-Quellcode auf dem Cluster haben, können wir ihn installieren, was den Zugriff auf die ausführbare Datei `amdahl` ermöglicht. Wechseln Sie in das extrahierte Verzeichnis und verwenden Sie dann den Package Installer für Python oder `pip`, um es in Ihrem ("user") Home-Verzeichnis zu installieren:

```bash
[yourUsername@login1 ~] cd amdahl
[yourUsername@login1 ~] python3 -m pip install --user .
```

::::::::::::::::::::::::::::::::::::::::: callout

## Amdahl ist Python Code

Das Amdahl-Programm ist in Python geschrieben, und um es zu installieren oder zu benutzen, muss die ausführbare Datei `python3` auf dem Anmeldeknoten gefunden werden. Wenn es nicht gefunden werden kann, versuchen Sie, die verfügbaren Module mit `module avail` aufzulisten, laden Sie das entsprechende Modul und versuchen Sie den Befehl erneut.


::::::::::::::::::::::::::::::::::::::::::::::::::

### MPI für Python

Der Amdahl-Code ist von einem Prozess abhängig: **mpi4py**. Wenn es nicht bereits auf dem Cluster installiert ist, wird `pip` versuchen, mpi4py aus dem Internet zu laden und für Sie zu installieren. Wenn dies aufgrund einer Einweg-Firewall fehlschlägt, müssen Sie mpi4py auf Ihrem lokalen Rechner abrufen und hochladen, so wie wir es für Amdahl getan haben.

:::::::::::::::::::::::::::::::::::::: discussion

## Abrufen und Hochladen von `mpi4py`

Wenn die Installation von Amdahl fehlgeschlagen ist, weil mpi4py nicht installiert werden konnte, rufen Sie das Tarball von <https://github.com/mpi4py/mpi4py/tarball/master> ab, synchronisieren Sie es anschließend per `rsync` mit dem Cluster, extrahieren Sie es und installieren Sie es:

```bash
[you@laptop:~]$ wget -O mpi4py.tar.gz https://github.com/mpi4py/mpi4py/releases/download/3.1.4/mpi4py-3.1.4.tar.gz
[you@laptop:~]$ scp mpi4py.tar.gz yourUsername@cluster.hpc-carpentry.org:
# or
[you@laptop:~]$ rsync -avP mpi4py.tar.gz yourUsername@cluster.hpc-carpentry.org:
```

```bash
[you@laptop:~]$ ssh yourUsername@cluster.hpc-carpentry.org
[yourUsername@login1 ~] tar -xvzf mpi4py.tar.gz  # extract the archive
[yourUsername@login1 ~] mv mpi4py* mpi4py        # rename the directory
[yourUsername@login1 ~] cd mpi4py
[yourUsername@login1 ~] python3 -m pip install --user .
[yourUsername@login1 ~] cd ../amdahl
[yourUsername@login1 ~] python3 -m pip install --user .
```

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::: discussion

## Wenn `pip` eine Warnung auslöst...

`pip` kann eine Warnung ausgeben, dass die Binärdateien Ihres Benutzerpakets nicht in Ihrem PATH sind.

```warning
WARNING: The script amdahl is installed in "${HOME}/.local/bin" which is
not on PATH. Consider adding this directory to PATH or, if you prefer to
suppress this warning, use --no-warn-script-location.
```

Um zu überprüfen, ob diese Warnung ein Problem darstellt, verwenden Sie `which`, um nach dem Programm `amdahl` zu suchen:

```bash
[yourUsername@login1 ~] which amdahl
```

Wenn der Befehl keine Ausgabe liefert und eine neue Eingabeaufforderung anzeigt, bedeutet dies, dass die Datei `amdahl` nicht gefunden wurde. Sie müssen die Umgebungsvariable mit dem Namen `PATH` aktualisieren, um den fehlenden Ordner aufzunehmen. Bearbeiten Sie Ihre Shell-Konfigurationsdatei wie folgt, melden Sie sich dann vom Cluster ab und wieder an, damit die Änderung wirksam wird.

```bash
[yourUsername@login1 ~] nano ~/.bashrc
[yourUsername@login1 ~] tail ~/.bashrc
```

```output
export PATH=${PATH}:${HOME}/.local/bin
```

Nachdem Sie sich wieder bei cluster.hpc-carpentry.org angemeldet haben, sollte `which` in der Lage sein, `amdahl` ohne Schwierigkeiten zu finden. Wenn Sie ein Python-Modul laden mussten, laden Sie es erneut.


::::::::::::::::::::::::::::::::::::::::::::::::::

## Hilfe!

Viele Befehlszeilenprogramme enthalten eine "Hilfe"-Meldung. Probieren Sie es mit `amdahl`:

```bash
[yourUsername@login1 ~] amdahl --help
```

```output
usage: amdahl [-h] [-p [PARALLEL_PROPORTION]] [-w [WORK_SECONDS]] [-t] [-e] [-j [JITTER_PROPORTION]]

optional arguments:
  -h, --help            show this help message and exit
  -p [PARALLEL_PROPORTION], --parallel-proportion [PARALLEL_PROPORTION]
                        Parallel proportion: a float between 0 and 1
  -w [WORK_SECONDS], --work-seconds [WORK_SECONDS]
                        Total seconds of workload: an integer greater than 0
  -t, --terse           Format output as a machine-readable object for easier analysis
  -e, --exact           Exactly match requested timing by disabling random jitter
  -j [JITTER_PROPORTION], --jitter-proportion [JITTER_PROPORTION]
                        Random jitter: a float between -1 and +1
```

Diese Nachricht sagt nicht viel darüber aus, was das Programm *tut*, aber sie teilt uns die wichtigen Flags mit, die wir beim Start des Programms verwenden sollten.

## Ausführen des Jobs auf einem Rechenknoten

Erstellen Sie eine Einreichungsdatei, die eine Aufgabe auf einem einzigen Knoten anfordert, und starten Sie sie.

```bash
[yourUsername@login1 ~] nano serial-job.sh
[yourUsername@login1 ~] cat serial-job.sh
```

```bash
#!/bin/bash
#SBATCH -J solo-job
#SBATCH -p cpubase_bycore_b1
#SBATCH -N 1
#SBATCH -n 1

# Load the computing environment we need
module load Python

# Execute the task
amdahl
```

```bash
[yourUsername@login1 ~] sbatch serial-job.sh
```

Verwenden Sie wie bisher die Statusbefehle Slurm, um zu überprüfen, ob Ihr Auftrag läuft und wann er endet:

```bash
[yourUsername@login1 ~] squeue -u yourUsername
```

Verwenden Sie `ls`, um die Ausgabedatei zu finden. Das Flag `-t` sortiert in umgekehrter chronologischer Reihenfolge: das Neueste zuerst. Wie lautete die Ausgabe?

::::::::::::::: spoiler

## Lesen der Job-Ausgabe

Die Cluster-Ausgabe sollte in eine Datei in dem Ordner geschrieben werden, aus dem Sie den Job gestartet haben. Zum Beispiel,

```bash
[yourUsername@login1 ~] ls -t
```

```output
slurm-347087.out  serial-job.sh  amdahl  README.md  LICENSE.txt
```

```bash
[yourUsername@login1 ~] cat slurm-347087.out
```

```output
Doing 30.000 seconds of 'work' on 1 processor,
which should take 30.000 seconds with 0.850 parallel proportion of the workload.

  Hello, World! I am process 0 of 1 on smnode1. I will do all the serial 'work' for 4.500 seconds.
  Hello, World! I am process 0 of 1 on smnode1. I will do parallel 'work' for 25.500 seconds.

Total execution time (according to rank 0): 30.033 seconds
```

:::::::::::::::::::::::::

Wie wir bereits gesehen haben, legen zwei der `amdahl`-Programmflags die Menge der Arbeit und den Anteil der parallelen Arbeit fest. Anhand der Ausgabe können wir sehen, dass der Code eine Vorgabe von 30 Sekunden Arbeit verwendet, die zu 85 % parallel ist. Das Programm lief insgesamt etwas über 30 Sekunden, und wenn wir die Zahlen durchgehen, stimmt es, dass 15 % davon als "seriell" und 85 % als "parallel" gekennzeichnet waren.

Da wir dem Job nur eine CPU zugewiesen haben, war dieser Job nicht wirklich parallel: derselbe Prozessor führte die "serielle" Arbeit 4,5 Sekunden lang aus, dann den "parallelen" Teil 25,5 Sekunden lang, und es wurde keine Zeit gespart. Der Cluster kann es besser, wenn wir ihn fragen.

## Ausführen des parallelen Jobs

Das Programm `amdahl` verwendet das Message Passing Interface (MPI) für die Parallelität - ein gängiges Werkzeug auf HPC-Systemen.

::::::::::::::::::::::::::::::::::::::::: callout

## Was ist MPI?

Das Message Passing Interface ist eine Reihe von Werkzeugen, die es mehreren gleichzeitig laufenden Aufgaben ermöglichen, miteinander zu kommunizieren. Typischerweise wird eine einzelne ausführbare Datei mehrfach ausgeführt, möglicherweise auf verschiedenen Rechnern, und die MPI-Werkzeuge werden verwendet, um jede Instanz der ausführbaren Datei über ihre Geschwisterprozesse und die jeweilige Instanz zu informieren. MPI bietet auch Werkzeuge für die Kommunikation zwischen Instanzen, um die Arbeit zu koordinieren, Informationen über Elemente der Aufgabe auszutauschen oder Daten zu übertragen. Eine MPI-Instanz hat normalerweise ihre eigene Kopie aller lokalen Variablen.


::::::::::::::::::::::::::::::::::::::::::::::::::

Während MPI-fähige ausführbare Programme im Allgemeinen als eigenständige Programme ausgeführt werden können, müssen sie, um parallel ausgeführt werden zu können, eine MPI *Laufzeitumgebung* verwenden, bei der es sich um eine spezielle Implementierung des MPI *Standards* handelt. Um die MPI-Umgebung zu aktivieren, sollte das Programm mit einem Befehl wie `mpiexec` (oder `mpirun`, oder `srun`, usw., je nach der zu verwendenden MPI-Laufzeitumgebung) gestartet werden, wodurch sichergestellt wird, dass die entsprechende Laufzeitunterstützung für Parallelität enthalten ist.

::::::::::::::::::::::::::::::::::::::::: callout

## MPI Laufzeitargumente

Befehle wie `mpiexec` können viele Argumente annehmen, die angeben, wie viele Maschinen an der Ausführung teilnehmen sollen, und Sie benötigen diese Argumente, wenn Sie ein MPI-Programm alleine ausführen möchten (z. B. auf Ihrem Laptop). Im Zusammenhang mit einem Warteschlangensystem ist es jedoch häufig der Fall, dass die MPI-Laufzeit die notwendigen Parameter vom Warteschlangensystem erhält, indem sie die Umgebungsvariablen untersucht, die beim Start des Auftrags gesetzt wurden.

::::::::::::::::::::::::::::::::::::::::::::::::::

Ändern wir das Jobskript, um mehr Kerne anzufordern und die MPI-Laufzeit zu nutzen.

```bash
[yourUsername@login1 ~] cp serial-job.sh parallel-job.sh
[yourUsername@login1 ~] nano parallel-job.sh
[yourUsername@login1 ~] cat parallel-job.sh
```

```bash
#!/bin/bash
#SBATCH -J parallel-job
#SBATCH -p cpubase_bycore_b1
#SBATCH -N 1
#SBATCH -n 4

# Load the computing environment we need
# (mpi4py and numpy are in SciPy-bundle)
module load Python
module load SciPy-bundle

# Execute the task
mpiexec amdahl
```

Senden Sie dann Ihren Auftrag ab. Beachten Sie, dass sich der Übermittlungsbefehl im Vergleich zur Übermittlung des seriellen Auftrags nicht wirklich geändert hat: Alle parallelen Einstellungen befinden sich in der Batch-Datei und nicht in der Befehlszeile.

```bash
[yourUsername@login1 ~] sbatch parallel-job.sh
```

Verwenden Sie wie bisher die Statusbefehle, um zu überprüfen, wann Ihr Auftrag läuft.

```bash
[yourUsername@login1 ~] ls -t
```

```output
slurm-347178.out  parallel-job.sh  slurm-347087.out  serial-job.sh  amdahl  README.md  LICENSE.txt
```

```bash
[yourUsername@login1 ~] cat slurm-347178.out
```

```output
Doing 30.000 seconds of 'work' on 4 processors,
which should take 10.875 seconds with 0.850 parallel proportion of the workload.

  Hello, World! I am process 0 of 4 on smnode1. I will do all the serial 'work' for 4.500 seconds.
  Hello, World! I am process 2 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.
  Hello, World! I am process 1 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.
  Hello, World! I am process 3 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.
  Hello, World! I am process 0 of 4 on smnode1. I will do parallel 'work' for 6.375 seconds.

Total execution time (according to rank 0): 10.888 seconds
```

::::::::::::::::::::::::::::::::::::::: challenge

## Ist es 4× schneller?

Der parallele Auftrag erhielt 4× mehr Prozessoren als der serielle Auftrag: Bedeutet das, dass er in ¼ der Zeit fertig wurde?

::::::::::::::: solution

## Lösung

Der parallele Auftrag hat *weniger* Zeit benötigt: 11 Sekunden sind besser als 30! Aber es ist nur eine 2,7-fache Verbesserung, nicht eine 4-fache.

Sehen Sie sich die Ausgabe des Jobs an:

- Während "Prozess 0" serielle Arbeit verrichtete, erledigten die Prozesse 1 bis 3 ihre parallele Arbeit.
- Während Prozess 0 seine parallele Arbeit nachholte, tat der Rest gar nichts.

Prozess 0 muss immer erst seine serielle Aufgabe beenden, bevor er mit der parallelen Arbeit beginnen kann. Damit wird eine untere Grenze für die Zeit gesetzt, die diese Aufgabe benötigt, egal wie viele Kerne man ihr zuordnet.

Dies ist das Grundprinzip des [Amdahl'schen Gesetzes][amdahl], das eine Möglichkeit ist, Verbesserungen der Ausführungszeit für eine **feste** Arbeitslast vorherzusagen, die bis zu einem gewissen Grad unterteilt und parallel ausgeführt werden kann.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

## Wie stark verbessert die parallele Ausführung die Leistung?

Theoretisch sollte die Aufteilung einer perfekt parallelen Berechnung auf *n* MPI-Prozesse zu einer Verringerung der Gesamtlaufzeit um den Faktor *n* führen. Wie wir soeben gesehen haben, benötigen reale Programme eine gewisse Zeit, damit die MPI-Prozesse kommunizieren und sich koordinieren können, und einige Arten von Berechnungen können nicht aufgeteilt werden: Sie laufen effektiv nur auf einer einzigen CPU.

Wenn die MPI-Prozesse auf verschiedenen physischen CPUs im Computer oder auf mehreren Rechenknoten arbeiten, wird sogar mehr Zeit für die Kommunikation benötigt, als wenn alle Prozesse auf einer einzigen CPU arbeiten.

In der Praxis ist es üblich, die Parallelität eines MPI-Programms wie folgt zu bewerten

- das Programm über einen Bereich von CPU-Zahlen laufen,
- Aufzeichnung der Ausführungszeit bei jedem Durchlauf,
- Vergleich der jeweiligen Ausführungszeit mit der Zeit bei Verwendung einer einzigen CPU.

Da "mehr besser ist" - eine Verbesserung ist leichter zu interpretieren, wenn eine bestimmte Größe zunimmt, als wenn sie abnimmt - werden Vergleiche mit dem Beschleunigungsfaktor *S* durchgeführt, der als die Ausführungszeit auf einer CPU geteilt durch die Ausführungszeit auf mehreren CPUs berechnet wird. Für ein perfekt paralleles Programm würde ein Diagramm des Beschleunigungsfaktors *S* gegen die Anzahl der CPUs *n* eine gerade Linie ergeben, *S* = *n*.

Lassen wir einen weiteren Job laufen, damit wir sehen können, wie nahe unser `amdahl`-Code einer geraden Linie kommt.

```bash
[yourUsername@login1 ~] nano parallel-job.sh
[yourUsername@login1 ~] cat parallel-job.sh
```

```bash
#!/bin/bash
#SBATCH -J parallel-job
#SBATCH -p cpubase_bycore_b1
#SBATCH -N 1
#SBATCH -n 8

# Load the computing environment we need
# (mpi4py and numpy are in SciPy-bundle)
module load Python
module load SciPy-bundle

# Execute the task
mpiexec amdahl
```

Senden Sie dann Ihren Auftrag ab. Beachten Sie, dass sich der Übermittlungsbefehl im Vergleich zur Übermittlung des seriellen Auftrags nicht wirklich geändert hat: Alle parallelen Einstellungen befinden sich in der Batch-Datei und nicht in der Befehlszeile.

```bash
[yourUsername@login1 ~] sbatch parallel-job.sh
```

Verwenden Sie wie bisher die Statusbefehle, um zu überprüfen, wann Ihr Auftrag läuft.

```bash
[yourUsername@login1 ~] ls -t
```

```output
slurm-347271.out  parallel-job.sh  slurm-347178.out  slurm-347087.out  serial-job.sh  amdahl  README.md  LICENSE.txt
```

```bash
[yourUsername@login1 ~] cat slurm-347178.out
```

```output
which should take 7.688 seconds with 0.850 parallel proportion of the workload.

  Hello, World! I am process 4 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 0 of 8 on smnode1. I will do all the serial 'work' for 4.500 seconds.
  Hello, World! I am process 2 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 1 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 3 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 5 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 6 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 7 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.
  Hello, World! I am process 0 of 8 on smnode1. I will do parallel 'work' for 3.188 seconds.

Total execution time (according to rank 0): 7.697 seconds
```

:::::::::::::::::::::::::::::::::::::: discussion

## Nicht-lineare Ausgabe

Wenn wir den Job mit 4 parallelen Workern ausgeführt haben, hat der serielle Job zuerst seine Ausgabe geschrieben, dann haben die parallelen Prozesse ihre Ausgabe geschrieben, wobei Prozess 0 zuerst und zuletzt kam.

Bei 8 Workern ist dies nicht der Fall: Da die parallelen Worker weniger Zeit benötigen als die seriellen, ist es schwer zu sagen, welcher Prozess seine Ausgabe zuerst schreiben wird, außer dass es *nicht* Prozess 0 sein wird!

::::::::::::::::::::::::::::::::::::::::::::::::::

Fassen wir nun die Zeit zusammen, die jeder Job für seine Ausführung benötigt hat:

| Number of CPUs | Runtime (sec) |
| -------------- | ------------- |
| 1              | 30\.033       |
| 4              | 10\.888       |
| 8              | 7\.697        |

Dann verwenden Sie die erste Zeile, um die Geschwindigkeitssteigerung $S$ zu berechnen, indem Sie Python als Befehlszeilenrechner und die Formel

$$ S(t_{n}) = \frac{t_{1}}{t_{n}} $$

```bash
[yourUsername@login1 ~] for n in 30.033 10.888 7.697; do python3 -c "print(30.033 / $n)"; done
```

| Number of CPUs | Speedup | Ideal |
| -------------- | ------- | ----- |
| 1              | 1\.0    | 1     |
| 4              | 2\.75   | 4     |
| 8              | 3\.90   | 8     |

Aus den Job-Ausgabedateien geht hervor, dass dieses Programm 85 % seiner Arbeit parallel ausführt und 15 % seriell abläuft. Dies scheint recht hoch zu sein, aber unsere schnelle Untersuchung der Beschleunigung zeigt, dass wir 8 oder 9 Prozessoren parallel verwenden müssen, um eine 4fache Beschleunigung zu erreichen. In realen Programmen wird der Beschleunigungsfaktor beeinflusst durch

- CPU-Design
- Kommunikationsnetz zwischen Rechenknoten
- Implementierungen der MPI-Bibliothek
- Einzelheiten zum MPI-Programm selbst

Mit Hilfe des Amdahlschen Gesetzes können Sie beweisen, dass es *unmöglich* ist, mit diesem Programm eine 8-fache Beschleunigung zu erreichen, egal wie viele Prozessoren Sie zur Verfügung haben. Die Einzelheiten dieser Analyse mit den entsprechenden Ergebnissen werden in der nächsten Klasse des HPC Carpentry Workshops *HPC Workflows* behandelt.

In einer HPC-Umgebung versuchen wir, die Ausführungszeit für alle Arten von Aufträgen zu reduzieren, und MPI ist eine sehr gängige Methode, um Dutzende, Hunderte oder Tausende von CPUs zur Lösung eines einzigen Problems zu kombinieren. Um mehr über Parallelisierung zu erfahren, lesen Sie die Lektion [parallel-novice][parallel-novice].


[amdahl]: https://en.wikipedia.org/wiki/Amdahl\'s_law
[parallel-novice]: https://www.hpc-carpentry.org/hpc-parallel-novice/


:::::::::::::::::::::::::::::::::::::::: keypoints

- Parallele Programmierung ermöglicht es Anwendungen, die Vorteile paralleler Hardware zu nutzen.
- Das Warteschlangensystem erleichtert die Ausführung von parallelen Aufgaben.
- Leistungssteigerungen durch parallele Ausführung skalieren nicht linear.

::::::::::::::::::::::::::::::::::::::::::::::::::


