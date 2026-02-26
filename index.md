---
site: sandpaper::sandpaper_site
---

::::::::::::::::::::::::::::::::::::::::::  callout

## Haftungsausschluss

Dieses Schulungsmaterial dient ausschließlich zu Bildungs- und
Informationszwecken. Es erläutert die Nutzung einer HPC-Umgebung auf Basis von
[SLURM](https://slurm.schedmd.com/overview.html),
[modules](https://lmod.readthedocs.io)
und verwandten Technologien, stellt jedoch keinen Zugang zu der zur Durchführung
der Übungen erforderlichen Recheninfrastruktur bereit.

Die Teilnehmenden sind selbst dafür verantwortlich, sich geeigneten Zugang zu
entsprechenden Rechenressourcen zu verschaffen.

Als Alternative können Nutzerinnen und Nutzer in Betracht ziehen, eine lokale
Testumgebung mit dem Open-Source-Projekt
[slurm-docker-cluster](https://github.com/giovtorres/slurm-docker-cluster)
einzurichten. Ein ausführliches Anwendungsbeispiel dieses Projekts wird in
einem [Blogbeitrag eines Drittanbieters von Thomas Sandmann](https://tomsing1.github.io/blog/posts/slurm_docker_cluster/)
beschrieben.

Die Nutzung von Werkzeugen oder Dokumentationen Dritter erfolgt nach eigenem
Ermessen und auf eigenes Risiko.

::::::::::::::::::::::::::::::::::::::::::::::::::

Dieser Workshop ist eine Einführung in die effektive Nutzung von Hochleistungsrechnersystemen. Wir können nicht jeden Fall abdecken oder einen erschöpfenden Kurs über parallele Programmierung in nur zwei Tagen Unterricht geben. Stattdessen soll dieser Workshop den Studierenden eine gute Einführung und einen Überblick über die verfügbaren Werkzeuge und deren effektive Nutzung geben.

:::::::::::::::::::::::::::::::::::::::::: prereq

## Voraussetzungen

Kommandozeilenkenntnisse sind für diese Lektion erforderlich. Wir empfehlen den Teilnehmern, [shell-novice](https://swcarpentry.github.io/shell-novice/) durchzuarbeiten, wenn sie neu in der Kommandozeile (auch bekannt als Terminal oder Shell) sind.

::::::::::::::::::::::::::::::::::::::::::::::::::

Am Ende dieses Workshops werden die Teilnehmer wissen, wie man:

- Identifizieren Sie Probleme, die ein Cluster lösen kann
- Verwenden Sie die UNIX-Shell (auch bekannt als Terminal oder Kommandozeile), um eine Verbindung zu einem Cluster herzustellen.
- Übertragen von Dateien auf einen Cluster.
- Einreichen und Verwalten von Aufträgen in einem Cluster mit Hilfe eines Schedulers.
- Beobachten Sie die Vorteile und Grenzen der parallelen Ausführung.

::::::::::::::::::::::::::::::::::::::::: callout

## Erste Schritte

Um zu beginnen, folgen Sie bitte den [Setup Instructions](learners/setup.md), um sicherzustellen, dass Sie ein Terminal und eine SSH-Anwendung haben.

::::::::::::::::::::::::::::::::::::::::::::::::::

Beachten Sie, dass dies der Entwurf der HPC Carpentry Version ist. Kommentare und Rückmeldungen sind willkommen.

::::::::::::::::::::::::::::::::::::::::: callout

## Für Ausbilder

Wenn Sie diese Lektion in einem Workshop unterrichten, lesen Sie bitte die [Instructor notes](instructors/instructor-notes.md).

::::::::::::::::::::::::::::::::::::::::::::::::::


