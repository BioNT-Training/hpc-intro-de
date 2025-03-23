---
title: Zugriff auf Software über Module
teaching: 30
exercises: 15
---




::::::::::::::::::::::::::::::::::::::: objectives

- Ein Softwarepaket laden und verwenden.
- Erklären Sie, wie sich die Shell-Umgebung verändert, wenn der Modulmechanismus Pakete lädt oder entlädt.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Wie laden und entladen wir Softwarepakete?

::::::::::::::::::::::::::::::::::::::::::::::::::

Auf einem Hochleistungsrechnersystem ist die Software, die wir benutzen wollen, selten verfügbar, wenn wir uns anmelden. Sie ist zwar installiert, aber wir müssen sie erst "laden", bevor sie ausgeführt werden kann.

Bevor wir jedoch anfangen, einzelne Softwarepakete zu verwenden, sollten wir die Gründe für diesen Ansatz verstehen. Die drei wichtigsten Faktoren sind:

- Software-Inkompatibilitäten
- Versionierung
- Abhängigkeiten

Software-Inkompatibilität ist ein großes Problem für Programmierer. Manchmal führt das Vorhandensein (oder Fehlen) eines Softwarepakets dazu, dass andere Pakete, die davon abhängen, nicht mehr funktionieren. Zwei bekannte Beispiele sind Python und C-Compiler-Versionen. Python 3 bietet bekanntlich einen `python`-Befehl, der im Widerspruch zu dem von Python 2 steht. Software, die mit einer neueren Version der C-Bibliotheken kompiliert und dann auf einem Rechner ausgeführt wird, auf dem ältere C-Bibliotheken installiert sind, führt zu einem unangenehmen `'GLIBCXX_3.4.20' not found`-Fehler.

Software Versionierung ist ein weiteres häufiges Problem. Ein Team könnte für sein Forschungsprojekt auf eine bestimmte Paketversion angewiesen sein - wenn sich die Softwareversion ändert (z.B. wenn ein Paket aktualisiert wird), könnte dies die Ergebnisse beeinflussen. Durch den Zugriff auf mehrere Softwareversionen kann eine Gruppe von Forschern verhindern, dass Softwareversionsprobleme ihre Ergebnisse beeinträchtigen.

Von Abhängigkeiten spricht man, wenn ein bestimmtes Softwarepaket (oder sogar eine bestimmte Version) vom Zugriff auf ein anderes Softwarepaket (oder sogar eine bestimmte Version eines anderen Softwarepakets) abhängig ist. Zum Beispiel kann die VASP-Materialwissenschaftssoftware davon abhängen, dass eine bestimmte Version der FFTW-Softwarebibliothek (Fastest Fourier Transform in the West) verfügbar ist, damit sie funktioniert.

## Umgebungsmodule

Umgebungsmodule sind die Lösung für diese Probleme. Ein *Modul* ist eine in sich geschlossene Beschreibung eines Softwarepakets - es enthält die Einstellungen, die zum Ausführen eines Softwarepakets erforderlich sind, und kodiert in der Regel auch die erforderlichen Abhängigkeiten von anderen Softwarepaketen.

Es gibt eine Reihe von verschiedenen Implementierungen von Umgebungsmodulen, die häufig auf HPC-Systemen verwendet werden: die beiden häufigsten sind *TCL-Module* und *Lmod*. Beide verwenden eine ähnliche Syntax und die Konzepte sind die gleichen, so dass Sie, wenn Sie lernen, eines davon zu verwenden, dasjenige verwenden können, das auf dem von Ihnen verwendeten System installiert ist. In beiden Implementierungen wird der Befehl `module` verwendet, um mit Umgebungsmodulen zu interagieren. Normalerweise wird dem Befehl ein zusätzlicher Unterbefehl hinzugefügt, um anzugeben, was Sie tun wollen. Für eine Liste von Unterbefehlen können Sie `module -h` oder `module help` verwenden. Wie für alle Befehle können Sie die vollständige Hilfe auf den *man*-Seiten mit `man module` aufrufen.

Bei der Anmeldung können Sie mit einem Standardsatz von geladenen Modulen oder mit einer leeren Umgebung starten; dies hängt von der Einrichtung des von Ihnen verwendeten Systems ab.

### Auflistung der verfügbaren Module

Um verfügbare Softwaremodule zu sehen, verwenden Sie `module avail`:

```bash
[yourUsername@login1 ~] module avail
```

```output
~~~ /cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/modules/all ~~~
  Bazel/3.6.0-GCCcore-x.y.z              NSS/3.51-GCCcore-x.y.z
  Bison/3.5.3-GCCcore-x.y.z              Ninja/1.10.0-GCCcore-x.y.z
  Boost/1.72.0-gompi-2020a               OSU-Micro-Benchmarks/5.6.3-gompi-2020a
  CGAL/4.14.3-gompi-2020a-Python-3.x.y   OpenBLAS/0.3.9-GCC-x.y.z
  CMake/3.16.4-GCCcore-x.y.z             OpenFOAM/v2006-foss-2020a

[removed most of the output here for clarity]

  Where:
   L:        Module is loaded
   Aliases:  Aliases exist: foo/1.2.3 (1.2) means that "module load foo/1.2"
             will load foo/1.2.3
   D:        Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".
```

### Auflistung der aktuell geladenen Module

Sie können den Befehl `module list` verwenden, um zu sehen, welche Module Sie derzeit in Ihrer Umgebung geladen haben. Wenn Sie keine Module geladen haben, werden Sie eine Meldung sehen, die Ihnen dies mitteilt

```bash
[yourUsername@login1 ~] module list
```

```output
No Modulefiles Currently Loaded.
```

## Laden und Entladen von Software

Um ein Softwaremodul zu laden, verwenden Sie `module load`. In diesem Beispiel werden wir Python 3 verwenden.

Anfangs ist Python 3 nicht geladen. Wir können dies mit dem Befehl `which` testen. der Befehl `which` sucht auf die gleiche Weise wie die Bash nach Programmen, so dass wir ihn verwenden können, um uns zu sagen, wo ein bestimmtes Stück Software gespeichert ist.

```bash
[yourUsername@login1 ~] which python3
```

Wenn der Befehl `python3` nicht verfügbar wäre, würden wir folgende Ausgabe sehen

```output
/usr/bin/which: no python3 in (/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin:/opt/software/slurm/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/puppetlabs/bin:/home/yourUsername/.local/bin:/home/yourUsername/bin)
```

Beachten Sie, dass dieser Text in Wirklichkeit eine Liste ist, mit Werten, die durch das Zeichen `:` getrennt sind. Die Ausgabe sagt uns, dass der Befehl `which` die folgenden Verzeichnisse nach `python3` durchsucht hat, ohne Erfolg:

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin
/opt/software/slurm/bin
/usr/local/bin
/usr/bin
/usr/local/sbin
/usr/sbin
/opt/puppetlabs/bin
/home/yourUsername/.local/bin
/home/yourUsername/bin
```

In unserem Fall haben wir jedoch ein vorhandenes `python3` zur Verfügung, so dass wir sehen

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin/python3
```

Wir benötigen jedoch ein anderes Python als das vom System bereitgestellte, also laden wir ein Modul, um darauf zuzugreifen.

Wir können den Befehl `python3` mit `module load` laden:

```bash
[yourUsername@login1 ~] module load Python
[yourUsername@login1 ~] which python3
```

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Python/3.x.y-GCCcore-x.y.z/bin/python3
```

Was ist gerade passiert?

Um die Ausgabe zu verstehen, müssen wir zuerst die Natur der Umgebungsvariablen `$PATH` verstehen. `$PATH` ist eine spezielle Umgebungsvariable, die steuert, wo ein UNIX-System nach Software sucht. Genauer gesagt ist `$PATH` eine Liste von Verzeichnissen (getrennt durch `:`), die das Betriebssystem nach einem Befehl durchsucht, bevor es aufgibt und uns mitteilt, dass es ihn nicht finden kann. Wie bei allen Umgebungsvariablen können wir sie mit `echo` ausgeben.

```bash
[yourUsername@login1 ~] echo $PATH
```

```output
/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Python/3.x.y-GCCcore-x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/SQLite/3.31.1-GCCcore-x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Tcl/8.6.10-GCCcore-x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/GCCcore/x.y.z/bin:/cvmfs/pilot.eessi-hpc.org/2020.12/compat/linux/x86_64/usr/bin:/opt/software/slurm/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/opt/puppetlabs/bin:/home/user01/.local/bin:/home/user01/bin
```

Sie werden eine Ähnlichkeit mit der Ausgabe des Befehls `which` feststellen. In diesem Fall gibt es nur einen Unterschied: das andere Verzeichnis am Anfang. Als wir den Befehl `module load` ausführten, fügte er ein Verzeichnis am Anfang unserer `$PATH` hinzu. Schauen wir uns an, was dort steht:

```bash
[yourUsername@login1 ~] ls /cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/software/Python/3.x.y-GCCcore-x.y.z/bin
```

```output
2to3              nosetests-3.8  python                 rst2s5.py
2to3-3.8          pasteurize     python3                rst2xetex.py
chardetect        pbr            python3.8              rst2xml.py
cygdb             pip            python3.8-config       rstpep2html.py
cython            pip3           python3-config         runxlrd.py
cythonize         pip3.8         rst2html4.py           sphinx-apidoc
easy_install      pybabel        rst2html5.py           sphinx-autogen
easy_install-3.8  __pycache__    rst2html.py            sphinx-build
futurize          pydoc3         rst2latex.py           sphinx-quickstart
idle3             pydoc3.8       rst2man.py             tabulate
idle3.8           pygmentize     rst2odt_prepstyles.py  virtualenv
netaddr           pytest         rst2odt.py             wheel
nosetests         py.test        rst2pseudoxml.py
```

Um es auf den Punkt zu bringen: `module load` fügt Software zu Ihrem `$PATH` hinzu. Es "lädt" Software. Ein spezieller Hinweis dazu - je nachdem, welche Version des Programms `module` bei Ihnen installiert ist, wird `module load` auch erforderliche Software-Abhängigkeiten laden.

Zur Veranschaulichung wollen wir `module list` verwenden. `module list` zeigt alle geladenen Softwaremodule an.

```bash
[yourUsername@login1 ~] module list
```

```output
Currently Loaded Modules:
  1) GCCcore/x.y.z                 4) GMP/6.2.0-GCCcore-x.y.z
  2) Tcl/8.6.10-GCCcore-x.y.z      5) libffi/3.3-GCCcore-x.y.z
  3) SQLite/3.31.1-GCCcore-x.y.z   6) Python/3.x.y-GCCcore-x.y.z
```

```bash
[yourUsername@login1 ~] module load GROMACS
[yourUsername@login1 ~] module list
```

```output
Currently Loaded Modules:
  1) GCCcore/x.y.z                    14) libfabric/1.11.0-GCCcore-x.y.z
  2) Tcl/8.6.10-GCCcore-x.y.z         15) PMIx/3.1.5-GCCcore-x.y.z
  3) SQLite/3.31.1-GCCcore-x.y.z      16) OpenMPI/4.0.3-GCC-x.y.z
  4) GMP/6.2.0-GCCcore-x.y.z          17) OpenBLAS/0.3.9-GCC-x.y.z
  5) libffi/3.3-GCCcore-x.y.z         18) gompi/2020a
  6) Python/3.x.y-GCCcore-x.y.z       19) FFTW/3.3.8-gompi-2020a
  7) GCC/x.y.z                        20) ScaLAPACK/2.1.0-gompi-2020a
  8) numactl/2.0.13-GCCcore-x.y.z     21) foss/2020a
  9) libxml2/2.9.10-GCCcore-x.y.z     22) pybind11/2.4.3-GCCcore-x.y.z-Pytho...
 10) libpciaccess/0.16-GCCcore-x.y.z  23) SciPy-bundle/2020.03-foss-2020a-Py...
 11) hwloc/2.2.0-GCCcore-x.y.z        24) networkx/2.4-foss-2020a-Python-3.8...
 12) libevent/2.1.11-GCCcore-x.y.z    25) GROMACS/2020.1-foss-2020a-Python-3...
 13) UCX/1.8.0-GCCcore-x.y.z
```

In diesem Fall hat das Laden des Moduls `GROMACS` (ein Bioinformatik-Softwarepaket) auch `GMP/6.2.0-GCCcore-x.y.z` und `SciPy-bundle/2020.03-foss-2020a-Python-3.x.y` geladen. Lassen Sie uns versuchen, das Paket `GROMACS` zu entladen.

```bash
[yourUsername@login1 ~] module unload GROMACS
[yourUsername@login1 ~] module list
```

```output
Currently Loaded Modules:
  1) GCCcore/x.y.z                    13) UCX/1.8.0-GCCcore-x.y.z
  2) Tcl/8.6.10-GCCcore-x.y.z         14) libfabric/1.11.0-GCCcore-x.y.z
  3) SQLite/3.31.1-GCCcore-x.y.z      15) PMIx/3.1.5-GCCcore-x.y.z
  4) GMP/6.2.0-GCCcore-x.y.z          16) OpenMPI/4.0.3-GCC-x.y.z
  5) libffi/3.3-GCCcore-x.y.z         17) OpenBLAS/0.3.9-GCC-x.y.z
  6) Python/3.x.y-GCCcore-x.y.z       18) gompi/2020a
  7) GCC/x.y.z                        19) FFTW/3.3.8-gompi-2020a
  8) numactl/2.0.13-GCCcore-x.y.z     20) ScaLAPACK/2.1.0-gompi-2020a
  9) libxml2/2.9.10-GCCcore-x.y.z     21) foss/2020a
 10) libpciaccess/0.16-GCCcore-x.y.z  22) pybind11/2.4.3-GCCcore-x.y.z-Pytho...
 11) hwloc/2.2.0-GCCcore-x.y.z        23) SciPy-bundle/2020.03-foss-2020a-Py...
 12) libevent/2.1.11-GCCcore-x.y.z    24) networkx/2.4-foss-2020a-Python-3.x.y
```

Die Verwendung von `module unload` "entlädt" also ein Modul, und je nachdem, wie eine Site konfiguriert ist, kann es auch alle Abhängigkeiten entladen (in unserem Fall nicht). Wenn wir alles auf einmal entladen wollten, könnten wir `module purge` ausführen (entlädt alles).

```bash
[yourUsername@login1 ~] module purge
[yourUsername@login1 ~] module list
```

```output
No modules loaded
```

Beachten Sie, dass `module purge` informativ ist. Es teilt uns auch mit, ob ein Standardsatz von "klebrigen" Paketen nicht entladen werden kann (und wie man diese tatsächlich entlädt, wenn man das wirklich möchte).

Beachten Sie, dass dieser Modulladeprozess hauptsächlich durch die Manipulation von Umgebungsvariablen wie `$PATH` erfolgt. Normalerweise ist nur ein geringer oder gar kein Datentransfer beteiligt.

Der Modulladeprozess manipuliert auch andere spezielle Umgebungsvariablen, einschließlich Variablen, die beeinflussen, wo das System nach Softwarebibliotheken sucht, und manchmal Variablen, die kommerziellen Softwarepaketen mitteilen, wo sie Lizenzserver finden.

Der Modulbefehl setzt auch diese Shell-Umgebungsvariablen in ihren vorherigen Zustand zurück, wenn ein Modul entladen wird.

## Software-Versionierung

Bis jetzt haben wir gelernt, wie man Softwarepakete lädt und entlädt. Das ist sehr nützlich. Wir haben uns jedoch noch nicht mit dem Thema der Softwareversionierung befasst. Irgendwann werden Sie auf Probleme stoßen, für die nur eine bestimmte Version einer Software geeignet ist. Vielleicht wurde ein wichtiger Fehler nur in einer bestimmten Version behoben, oder Version X ist nicht mehr mit einem von Ihnen verwendeten Dateiformat kompatibel. In jedem dieser Fälle ist es hilfreich, sehr genau zu wissen, welche Software geladen wird.

Schauen wir uns die Ausgabe von `module avail` näher an.

```bash
[yourUsername@login1 ~] module avail
```

```output
~~~ /cvmfs/pilot.eessi-hpc.org/2020.12/software/x86_64/amd/zen2/modules/all ~~~
  Bazel/3.6.0-GCCcore-x.y.z              NSS/3.51-GCCcore-x.y.z
  Bison/3.5.3-GCCcore-x.y.z              Ninja/1.10.0-GCCcore-x.y.z
  Boost/1.72.0-gompi-2020a               OSU-Micro-Benchmarks/5.6.3-gompi-2020a
  CGAL/4.14.3-gompi-2020a-Python-3.x.y   OpenBLAS/0.3.9-GCC-x.y.z
  CMake/3.16.4-GCCcore-x.y.z             OpenFOAM/v2006-foss-2020a

[removed most of the output here for clarity]

  Where:
   L:        Module is loaded
   Aliases:  Aliases exist: foo/1.2.3 (1.2) means that "module load foo/1.2"
             will load foo/1.2.3
   D:        Default Module

Use "module spider" to find all possible modules and extensions.
Use "module keyword key1 key2 ..." to search for all possible modules matching
any of the "keys".
```

::::::::::::::::::::::::::::::::::::::: challenge

## Verwendung von Software-Modulen in Skripten

Erstellen Sie einen Job, der in der Lage ist, `python3 --version` auszuführen. Denken Sie daran, dass standardmäßig keine Software geladen ist! Das Ausführen eines Jobs ist genau wie das Anmelden am System (Sie sollten nicht davon ausgehen, dass ein auf dem Anmeldeknoten geladenes Modul auch auf einem Rechenknoten geladen ist).

::::::::::::::: solution

## Lösung

```bash
[yourUsername@login1 ~] nano python-module.sh
[yourUsername@login1 ~] cat python-module.sh
```

```output
#!/bin/bash
#SBATCH 
r config$sched$comment` -t 00:00:30

module load Python

python3 --version
```

```bash
[yourUsername@login1 ~] sbatch  python-module.sh
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::: keypoints

- Software mit `module load softwareName` laden.
- Entladen von Software mit `module unload`
- Das Modulsystem kümmert sich automatisch um Softwareversionen und Paketkonflikte.

::::::::::::::::::::::::::::::::::::::::::::::::::


