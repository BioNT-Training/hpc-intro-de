---
title: Umgebungsvariablen
teaching: 10
exercises: 5
---




::::::::::::::::::::::::::::::::::::::: objectives

- Verstehen, wie Variablen in der Shell implementiert werden
- Den Wert einer bestehenden Variable auslesen
- Erstellen Sie neue Variablen und ändern Sie deren Werte
- Ändern Sie das Verhalten eines Programms mit Hilfe einer Umgebungsvariablen
- Erkläre, wie die Shell die Variable `PATH` benutzt, um nach ausführbaren Dateien zu suchen

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Wie werden Variablen in der Unix-Shell gesetzt und angesprochen?
- Wie kann ich Variablen verwenden, um den Ablauf eines Programms zu verändern?

::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::: callout

## Herkunft der Episode

Diese Folge wurde aus der [Shell-Extras-Folge über Shell-Variablen] (https://github.com/carpentries-incubator/shell-extras/blob/gh-pages/_episodes/08-environment-variables.md) und der [HPC-Shell-Folge über Skripte] (https://github.com/hpc-carpentry/hpc-shell/blob/gh-pages/_episodes/05-scripts.md) neu zusammengestellt.

::::::::::::::::::::::::::::::::::::::::::::::::::

Die Shell ist nur ein Programm, und wie andere Programme auch, hat sie Variablen. Diese Variablen steuern ihre Ausführung, so dass Sie durch Ändern ihrer Werte das Verhalten der Shell (und mit etwas mehr Aufwand auch das Verhalten anderer Programme) beeinflussen können.

Variablen sind eine gute Möglichkeit, Informationen unter einem Namen zu speichern, auf den man später zugreifen kann. In Programmiersprachen wie Python und R können Variablen so ziemlich alles speichern, was man sich vorstellen kann. In der Shell speichern sie normalerweise nur Text. Der beste Weg, um zu verstehen, wie sie funktionieren, ist, sie in Aktion zu sehen.

Beginnen wir damit, den Befehl `set` auszuführen und einige der Variablen in einer typischen Shell-Sitzung zu betrachten:

```bash
set
```

```output
COMPUTERNAME=TURING
HOME=/home/vlad
HOSTNAME=TURING
HOSTTYPE=i686
NUMBER_OF_PROCESSORS=4
PATH=/Users/vlad/bin:/usr/local/git/bin:/usr/bin:/bin:/usr/sbin:/sbin:/usr/local/bin
PWD=/home/vlad
UID=1000
USERNAME=vlad
...
```

Wie Sie sehen können, gibt es ziemlich viele - tatsächlich vier- oder fünfmal so viele, wie hier gezeigt werden. Und ja, `set` zu benutzen, um Dinge *anzuzeigen*, mag ein wenig seltsam erscheinen, selbst für Unix, aber wenn Sie ihr keine Argumente geben, kann sie Ihnen auch Dinge zeigen, die Sie einstellen *könnten*.

Jede Variable hat einen Namen. Alle Werte von Shell-Variablen sind Zeichenketten, auch solche (wie `UID`), die wie Zahlen aussehen. Es liegt an den Programmen, diese Zeichenketten bei Bedarf in andere Typen zu konvertieren. Wenn ein Programm zum Beispiel herausfinden wollte, wie viele Prozessoren der Computer hat, würde es den Wert der Variablen `NUMBER_OF_PROCESSORS` von einer Zeichenkette in eine ganze Zahl umwandeln.

## Den Wert einer Variablen anzeigen

Zeigen wir den Wert der Variable `HOME`:

```bash
echo HOME
```

```output
HOME
```

Das gibt nur "HOME" aus, was nicht das ist, was wir wollten (obwohl es das ist, wonach wir eigentlich gefragt haben). Versuchen wir stattdessen dies:

```bash
echo $HOME
```

```output
/home/vlad
```

Das Dollarzeichen sagt der Shell, dass wir den *Wert* der Variable wollen und nicht ihren Namen. Das funktioniert genau wie bei Wildcards: die Shell ersetzt die Variable *vor* der Ausführung des gewünschten Programms. Dank dieser Erweiterung ist das, was wir tatsächlich ausführen, `echo /home/vlad`, was das Richtige anzeigt.

## Erstellen und Ändern von Variablen

Eine Variable zu erstellen ist einfach - wir weisen einem Namen mit "=" einen Wert zu (wir müssen nur daran denken, dass die Syntax verlangt, dass *keine* Leerzeichen um das `=` herum sind!)

```bash
SECRET_IDENTITY=Dracula
echo $SECRET_IDENTITY
```

```output
Dracula
```

Um den Wert zu ändern, weisen Sie einfach einen neuen Wert zu:

```bash
SECRET_IDENTITY=Camilla
echo $SECRET_IDENTITY
```

```output
Camilla
```

## Umgebungsvariablen

Als wir den `set`-Befehl ausführten, sahen wir, dass es eine Menge Variablen gab, deren Namen in Großbuchstaben geschrieben waren. Das liegt daran, dass Variablen, die auch von *anderen* Programmen verwendet werden können, in Großbuchstaben benannt werden. Solche Variablen werden *Umgebungsvariablen* genannt, da es sich um Shell-Variablen handelt, die für die aktuelle Shell definiert sind und an alle untergeordneten Shells oder Prozesse vererbt werden.

Um eine Umgebungsvariable zu erstellen, müssen Sie eine Shell-Variable `export` angeben. Um zum Beispiel unser `SECRET_IDENTITY` für andere Programme, die wir von unserer Shell aus aufrufen, verfügbar zu machen, können wir folgendes tun:

```bash
SECRET_IDENTITY=Camilla
export SECRET_IDENTITY
```

Sie können die Variable auch in einem einzigen Schritt erstellen und exportieren:

```bash
export SECRET_IDENTITY=Camilla
```

::::::::::::::::::::::::::::::::::::::: challenge

## Verwendung von Umgebungsvariablen zur Änderung des Programmverhaltens

Setze eine Shell-Variable `TIME_STYLE` auf den Wert `iso` und prüfe diesen Wert mit dem Befehl `echo`.

Führen Sie nun den Befehl `ls` mit der Option `-l` aus (was ein langes Format ergibt).

`export` die Variable und führen Sie den Befehl `ls -l` erneut aus. Bemerkst du einen Unterschied?

::::::::::::::: solution

## Lösung

Die Variable `TIME_STYLE` wird von `ls` nicht *gesehen*, bis sie exportiert wird. Zu diesem Zeitpunkt wird sie von `ls` benutzt, um zu entscheiden, welches Datumsformat bei der Darstellung des Zeitstempels von Dateien zu verwenden ist.

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

Sie können den kompletten Satz von Umgebungsvariablen in Ihrer aktuellen Shell-Sitzung mit dem Befehl `env` sehen (der eine Teilmenge dessen zurückgibt, was der Befehl `set` uns gegeben hat). **Der komplette Satz von Umgebungsvariablen wird als *Laufzeitumgebung* bezeichnet und kann das Verhalten der von Ihnen ausgeführten Programme beeinflussen**.

::::::::::::::::::::::::::::::::::::::: challenge

## Job-Umgebungsvariablen

Wenn `Slurm` einen Job ausführt, setzt es eine Reihe von Umgebungsvariablen für den Job. Mit einer dieser Variablen können wir überprüfen, von welchem Verzeichnis aus unser Job-Skript eingereicht wurde. Die Variable `SLURM_SUBMIT_DIR` wird auf das Verzeichnis gesetzt, von dem aus unser Job eingereicht wurde. Ändern Sie Ihren Job mit Hilfe der Variable `SLURM_SUBMIT_DIR` so, dass er das Verzeichnis ausgibt, von dem aus der Job übermittelt wurde.

::::::::::::::: solution

## Lösung

```bash
[yourUsername@login1 ~] nano example-job.sh
[yourUsername@login1 ~] cat example-job.sh
```

```output
#!/bin/bash
#SBATCH -t 00:00:30

echo -n "This script is running on "
hostname

echo "This job was launched in the following directory:"
echo ${SLURM_SUBMIT_DIR}
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

Um eine Variable oder Umgebungsvariable zu entfernen, können Sie zum Beispiel den Befehl `unset` verwenden:

```bash
unset SECRET_IDENTITY
```

## Die `PATH` Umgebungsvariable

In ähnlicher Weise speichern einige Umgebungsvariablen (wie `PATH`) Listen von Werten. In diesem Fall ist die Konvention, einen Doppelpunkt ':' als Trennzeichen zu verwenden. Wenn ein Programm die einzelnen Elemente einer solchen Liste benötigt, muss es den String-Wert der Variablen in Stücke aufteilen.

Schauen wir uns die Variable `PATH` genauer an. Ihr Wert definiert den Suchpfad der Shell für ausführbare Programme, d.h. die Liste der Verzeichnisse, in denen die Shell nach ausführbaren Programmen sucht, wenn Sie einen Programmnamen eintippen, ohne anzugeben, in welchem Verzeichnis er sich befindet.

Wenn wir zum Beispiel einen Befehl wie `analyze` eingeben, muss die Shell entscheiden, ob sie `./analyze` oder `/bin/analyze` ausführen soll. Die Regel, die sie dabei anwendet, ist einfach: Die Shell prüft jedes Verzeichnis in der Variablen `PATH` der Reihe nach und sucht nach einem Programm mit dem gewünschten Namen in diesem Verzeichnis. Sobald sie eine Übereinstimmung findet, hört sie auf zu suchen und führt das Programm aus.

Um zu zeigen, wie das funktioniert, sind hier die Komponenten von `PATH` aufgelistet, eine pro Zeile:

```output
/Users/vlad/bin
/usr/local/git/bin
/usr/bin
/bin
/usr/sbin
/sbin
/usr/local/bin
```

Auf unserem Computer gibt es eigentlich drei Programme namens `analyze` in drei verschiedenen Verzeichnissen: `/bin/analyze`, `/usr/local/bin/analyze`, und `/users/vlad/analyze`. Da die Shell die Verzeichnisse in der Reihenfolge durchsucht, in der sie in `PATH` aufgelistet sind, findet sie `/bin/analyze` zuerst und führt es aus. Beachten Sie, dass sie das Programm `/users/vlad/analyze` *nie* finden wird, es sei denn, wir geben den vollständigen Pfad zum Programm ein, da das Verzeichnis `/users/vlad` nicht in `PATH` enthalten ist.

Das bedeutet, dass ich ausführbare Dateien an vielen verschiedenen Orten haben kann, solange ich daran denke, dass ich mein `PATH` aktualisieren muss, damit meine Shell sie finden kann.

Was ist, wenn ich zwei verschiedene Versionen desselben Programms ausführen möchte? Da sie den gleichen Namen haben, wird, wenn ich sie beide zu meinem `PATH` hinzufüge, immer die erste gefundene Version gewinnen. In der nächsten Folge werden wir lernen, wie wir Hilfsmittel verwenden können, die uns bei der Verwaltung unserer Laufzeitumgebung helfen, damit dies möglich ist, ohne dass wir eine Menge Buchhaltung darüber führen müssen, welchen Wert `PATH` (und andere wichtige Umgebungsvariablen) haben oder haben sollten.

:::::::::::::::::::::::::::::::::::::::: keypoints

- Shell-Variablen werden standardmäßig als Zeichenketten behandelt
- Variablen werden mit "`=`" zugewiesen und mit dem Variablennamen, dem "`$`" vorangestellt ist, abgerufen
- Verwenden Sie "`export`", um eine Variable für andere Programme verfügbar zu machen
- Die Variable `PATH` definiert den Suchpfad der Shell

::::::::::::::::::::::::::::::::::::::::::::::::::


