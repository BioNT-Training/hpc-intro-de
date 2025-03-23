---
title: Ihre nächsten Schritte
teaching: 10
exercises: 60
questions:
- How can I get started on using HPC?
- Where can I get help to start using HPC?
objectives:
- Get help to get your work up and running on an HPC system
- Understand where you can get help from in the future
keypoints:
- Understand the next steps for you in using HPC.
- Understand how you can access help and support to use HPC.
---


Jetzt wissen Sie genug über HPC, um zu erforschen, wie Sie es für Ihre Arbeit nutzen
können, oder um zu verstehen, welche potenziellen Vorteile es für Sie hat. Vielleicht
haben Sie auch Ideen, wo die Hindernisse und Schwierigkeiten liegen könnten und haben
weitere Fragen, wie Sie HPC in Ihrem Bereich einsetzen und/oder ausprobieren können.

Diese Sitzung soll Ihnen die Möglichkeit geben, diese Fragen und Probleme zu
untersuchen. Die Dozenten und Helfer des Kurses werden vor Ort sein, um Ihre Fragen zu
beantworten und die nächsten Schritte mit Ihnen zu besprechen.

::::::::::::::::::::::::::::::::::::::::: callout

## Mögliche Erörterungen

Zu den Dingen, die Sie mit den Ausbildern und Helfern diskutieren könnten, gehören:

- Ihr Arbeitsablauf und wo fortgeschrittenes Rechnen helfen könnte
- Wie erhalten Sie Zugang zu Einrichtungen für Ihre Arbeit?
- Wie erhalten Sie Hilfe und Unterstützung, um Ihre Arbeit mit Hilfe von Advanced
  Computing zum Laufen zu bringen. Zum Beispiel Softwareentwicklung, Fortbildung, Zugang
  zu lokalem Fachwissen

::::::::::::::::::::::::::::::::::::::::::::::::::

## Optionen für diese Sitzung

Es gibt eine Reihe von verschiedenen Möglichkeiten für die praktische Arbeit während
dieser Sitzung. Die folgenden Aufgaben umfassen: die Untersuchung Ihrer eigenen Arbeit;
ein erweitertes Beispiel unter Verwendung einer parallelen HPC-Anwendung; ein
erweitertes Beispiel unter Verwendung von Hochdurchsatzberechnungen bei mehreren
seriellen Analysen. Wenn Sie die Sitzung für etwas anderes nutzen möchten (z. B. um mit
den Dozenten/Helfern wie oben beschrieben zu diskutieren), dann können Sie dies gerne
tun. Die Idee der Sitzung ist es, Ihnen dabei zu helfen, Ihre Nutzung der
fortgeschrittenen Datenverarbeitung zu verbessern, und dies wird von Person zu Person
unterschiedlich sein!

::::::::::::::::::::::::::::::::::::::: challenge

## Erforschung Ihrer Arbeit mit HPC

Wenn Sie ein praktisches Beispiel aus Ihrem Arbeitsbereich haben, bei dem Sie Hilfe
benötigen, um es auf einem HPC-System zum Laufen zu bringen oder die Leistung eines
HPC-Systems zu untersuchen, ist das großartig! Diskutieren Sie mit uns und stellen Sie
Fragen (sowohl technische als auch nicht-technische).


::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: challenge

## Untersuchen der Leistung von GROMACS

[GROMACS](https://www.gromacs.org) ist ein weltweit führendes Paket zur biomolekularen
Modellierung, das auf HPC-Systemen auf der ganzen Welt stark genutzt wird. Die Wahl der
besten Ressourcen für GROMACS-Berechnungen ist nicht trivial, da sie von vielen Faktoren
abhängt, darunter:

- Die zugrunde liegende Hardware des verwendeten HPC-Systems
- Das tatsächliche System, das durch das GROMACS-Paket modelliert wird
- Das Verhältnis von Prozessen zu Threads, die für die parallele Berechnung verwendet
  werden

In dieser Übung sollten Sie versuchen, eine gute Auswahl an Ressourcen und Einstellungen
auf {{ site.remote.name }} für ein typisches biomolekulares System zu treffen. Dies
beinhaltet:

- Herunterladen der Eingabedatei für GROMACS von [{{ site.url }}{{site.baseurl
  }}/files/ion-channel.tpr]( {{ site.url }}{{site.baseurl }}/files/ion-channel.tpr)
- Schreiben eines Job-Submissions-Skripts zur Ausführung von GROMACS auf {{
  site.remote.name }} unter Verwendung der Systemdokumentation
- Variieren Sie die Anzahl der Knoten (von 1 bis 32 Knoten ist ein guter Ausgangspunkt),
  die für den GROMACS-Job verwendet werden, und messen Sie die Leistung (in ns/Tag)
- Verwenden Sie die Ergebnisse dieser Studie, um eine gute Ressourcenwahl für diese
  GROMACS-Berechnung vorzuschlagen

Wenn Sie über diese anfängliche Aufgabe hinausgehen möchten, gibt es eine Reihe
interessanter Möglichkeiten, dies zu tun. Zum Beispiel:

- Variieren Sie die Anzahl der pro Prozess verwendeten Threads
- Reduzieren Sie die Anzahl der pro Knoten verwendeten Kerne
- Erlauben Sie der Berechnung, symmetrisches Multithreading (SMT) zu verwenden, falls
  aktiviert

Bitte fragen Sie einen Helfer nach weiteren Informationen zu diesen Optionen!


::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::: challenge

## Parallele Ausführung vieler serieller BLAST+-Analysen

[BLAST+](https://blast.ncbi.nlm.nih.gov/Blast.cgi?CMD=Web&PAGE_TYPE=BlastDocs&DOC_TYPE=Download)
findet Regionen der Ähnlichkeit zwischen biologischen Sequenzen. Das Programm vergleicht
Nukleotid- oder Proteinsequenzen mit Sequenzdatenbanken und errechnet die statistische
Signifikanz.

In dieser Übung sollten Sie das bisher Gelernte anwenden, um einen Weg zu finden,
mehrere serielle BLAST+ Analysen parallel laufen zu lassen. Es gibt viele verschiedene
Möglichkeiten, dies zu tun, die einzeln oder in Kombination verwendet werden können.
Einige Ideen sind:

- Verwendung von {{ site.sched.name }} Job-Arrays zur Ausführung mehrerer Kopien auf
  verschiedenen Knoten
- Verwendung einer Bash-Schleife innerhalb eines Knotens
- Verwendung von GNU Parallel innerhalb eines Knotens

Wir haben einen Beispieldatensatz mit 100 zu analysierenden Sequenzen vorbereitet
(eigentlich sind es 10 Sequenzen, die 10 Mal wiederholt werden). Dieser Satz basiert auf
dem [BLAST GNU Parallel
example](https://github.com/LangilleLab/microbiome_helper/wiki/Quick-Introduction-to-GNU-Parallel)

Diese Übung beinhaltet:

- Herunterladen und Erweitern des Datensatzes auf das HPC-System von: [{{ site.url
  }}{{site.baseurl }}/files/parallel\_example.tar.gz]({{ site.url
  }}files/parallel_example.tar.gz)

- Schreiben eines Auftragsskripts zur Ausführung einer einzelnen Analyse unter
  Verwendung des Moduls `blast` und des folgenden Befehls:

  ```bash
  blastp -db pdb_blast_db_example/pdb_seqres.txt -query test_seq_0.fas
  -evalue 0.0001 -word_size 7  -max_target_seqs 10 -num_threads 1 \
  -out output_seq_0.blast -outfmt "6 std stitle staxids sscinames"
  ```

  wobei das Zeichen `\` dem Zeichen `bash` mitteilt, dass der Befehl in der nächsten
  Zeile fortgesetzt wird. Beachten Sie, dass es bei dieser Ausrichtung keine Ausgabe
  geben wird, wenn sie korrekt funktioniert).

- Auswahl einer Methode zur Ausführung mehrerer Kopien der Analyse, um alle 100
  Analyseaufgaben parallel auszuführen (es müssen nicht alle 100 gleichzeitig ausgeführt
  werden).

Sie können dieses Problem weiter erforschen, indem Sie verschiedene Möglichkeiten zur
Parallelisierung dieses Problems und/oder die Kombination mehrerer paralleler Strategien
untersuchen.

Sie könnten auch die Leistungsschwankungen untersuchen, wenn Sie mehrere Kopien auf
einem Knoten ausführen. An welchem Punkt wird die Hardware überlastet?


::::::::::::::::::::::::::::::::::::::::::::::::::



