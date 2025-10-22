---
title: Effektive Nutzung von Ressourcen
teaching: 10
exercises: 20
---




::::::::::::::::::::::::::::::::::::::: objectives

- Job-Statistiken nachschlagen.
- Stellen Sie genauere Ressourcenanforderungen in Jobskripten auf der Grundlage von Daten, die die vergangene Leistung beschreiben.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Wie kann ich vergangene Aufträge überprüfen?
- Wie kann ich dieses Wissen nutzen, um ein genaueres Einreichungsskript zu erstellen?

::::::::::::::::::::::::::::::::::::::::::::::::::

Wir haben alle Fertigkeiten behandelt, die Sie für die Interaktion mit einem HPC-Cluster benötigen: Anmeldung über SSH, Laden von Softwaremodulen, Übermittlung paralleler Aufträge und Auffinden der Ausgabe. Jetzt geht es darum, die Ressourcennutzung abzuschätzen und zu verstehen, warum das wichtig sein kann.

## Schätzung der benötigten Ressourcen durch den Scheduler

Auch wenn wir die Anforderung von Ressourcen beim Scheduler bereits mit dem π-Code behandelt haben, woher wissen wir, welche Art von Ressourcen die Software überhaupt benötigt und welchen Bedarf sie jeweils hat? Im Allgemeinen wissen wir nicht, wie viel Arbeitsspeicher oder Rechenzeit ein Programm benötigen wird, es sei denn, die Software-Dokumentation oder Benutzerberichte geben uns eine Vorstellung davon.

::::::::::::::::::::::::::::::::::::::::: callout

## Lesen Sie die Dokumentation

Die meisten HPC-Einrichtungen verfügen über eine Dokumentation in Form eines Wikis, einer Website oder eines Dokuments, das Ihnen bei der Registrierung für ein Konto zugeschickt wird. Werfen Sie einen Blick auf diese Ressourcen und suchen Sie nach der Software, die Sie verwenden möchten: Vielleicht hat jemand eine Anleitung geschrieben, wie Sie das Beste daraus machen können.

::::::::::::::::::::::::::::::::::::::::::::::::::

Eine bequeme Methode, um herauszufinden, welche Ressourcen für die erfolgreiche Ausführung eines Jobs benötigt werden, besteht darin, einen Testjob abzuschicken und dann den Scheduler mit `sacct -u yourUsername` nach dessen Auswirkungen zu fragen. Sie können dieses Wissen nutzen, um den nächsten Job mit einer genaueren Abschätzung seiner Belastung des Systems einzurichten. Eine gute allgemeine Regel ist es, den Scheduler um 20 bis 30 % mehr Zeit und Speicher zu bitten, als Sie erwarten, daß der Job benötigt. Dadurch wird sichergestellt, dass geringfügige Schwankungen in der Laufzeit oder im Speicherbedarf nicht dazu führen, dass Ihr Auftrag vom Scheduler abgebrochen wird. Wenn Sie zu viel verlangen, kann es sein, dass Ihr Auftrag nicht ausgeführt werden kann, obwohl genügend Ressourcen zur Verfügung stehen, weil der Scheduler darauf wartet, dass andere Aufträge beendet werden und die Ressourcen freigeben, die für die von Ihnen verlangte Menge benötigt werden.

## Statistiken

Da wir `amdahl` bereits zur Ausführung im Cluster eingereicht haben, können wir den Scheduler abfragen, um zu sehen, wie lange unser Job gedauert hat und welche Ressourcen verwendet wurden. Wir werden `sacct -u yourUsername` verwenden, um Statistiken über `parallel-job.sh` zu erhalten.

```bash
[yourUsername@login1 ~] sacct -u yourUsername
```

```output
       JobID    JobName  Partition    Account  AllocCPUS      State ExitCode
------------ ---------- ---------- ---------- ---------- ---------- --------
7               file.sh cpubase_b+ def-spons+          1  COMPLETED      0:0
7.batch           batch            def-spons+          1  COMPLETED      0:0
7.extern         extern            def-spons+          1  COMPLETED      0:0
8               file.sh cpubase_b+ def-spons+          1  COMPLETED      0:0
8.batch           batch            def-spons+          1  COMPLETED      0:0
8.extern         extern            def-spons+          1  COMPLETED      0:0
9            example-j+ cpubase_b+ def-spons+          1  COMPLETED      0:0
9.batch           batch            def-spons+          1  COMPLETED      0:0
9.extern         extern            def-spons+          1  COMPLETED      0:0
```

Hier werden alle Aufträge (oder Jobs) angezeigt, die heute ausgeführt wurden (beachten Sie, dass es mehrere Einträge pro Auftrag gibt). Um Informationen über einen bestimmten Auftrag zu erhalten (z. B. 347087), ändern wir den Befehl leicht ab.

```bash
[yourUsername@login1 ~] sacct -u yourUsername -l -j 347087
```

Hier werden viele Informationen angezeigt, und zwar jede einzelne Information, die der Scheduler über Ihren Job sammelt. Es kann sinnvoll sein, diese Informationen nach `less` umzuleiten, um sie leichter einsehen zu können (verwenden Sie die Pfeiltasten links und rechts, um durch die Felder zu blättern).

```bash
[yourUsername@login1 ~] sacct -u yourUsername -l -j 347087 | less -S
```

:::::::::::::::::::::::::::::::::::::: discussion

## Diskussion

Diese Ansicht hilft beim Vergleich der angeforderten und tatsächlich genutzten Zeit, der Verweildauer in der Warteschlange vor dem Start und des Speicherbedarfs auf dem/den Rechenknoten.

Wie genau waren unsere Schätzungen?


::::::::::::::::::::::::::::::::::::::::::::::::::

## Verbesserung der Ressourcenanfragen

Aus der Job-Historie geht hervor, dass die Ausführung von `amdahl` Jobs in höchstens ein paar Minuten abgeschlossen war, sobald sie versandt wurden. Die von uns im Auftragsskript angegebene Zeit war viel zu lang! Dadurch wird es für das Warteschlangensystem schwieriger, genau abzuschätzen, wann Ressourcen für andere Aufträge frei werden. In der Praxis bedeutet dies, dass das Warteschlangensystem mit der Abfertigung unseres Auftrags `amdahl` wartet, bis das gesamte angeforderte Zeitfenster geöffnet wird, anstatt ihn in ein viel kürzeres Zeitfenster zu "schieben", in dem der Auftrag tatsächlich beendet werden könnte. Wenn Sie die erwartete Laufzeit im Einreichungsskript genauer angeben, können Sie die Überlastung des Clusters verringern und Ihren Auftrag möglicherweise früher abfertigen lassen.

::::::::::::::::::::::::::::::::::::::: challenge

## Eingrenzung der Zeitabschätzung

Bearbeiten Sie `parallel_job.sh`, um eine bessere Zeitschätzung zu erhalten. Wie nah können Sie herankommen?

Hinweis: Verwenden Sie `-t`.

::::::::::::::: solution

## Lösung

Die folgende Zeile teilt Slurm mit, dass unser Auftrag innerhalb von 2 Minuten beendet sein soll:

```bash
#SBATCH -t 00:02:00
```

:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::


:::::::::::::::::::::::::::::::::::::::: keypoints

- Präzise Jobskripte helfen dem Warteschlangensystem bei der effizienten Zuweisung gemeinsamer Ressourcen.

::::::::::::::::::::::::::::::::::::::::::::::::::


