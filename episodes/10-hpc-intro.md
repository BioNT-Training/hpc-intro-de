---
title: Warum einen Cluster verwenden?
teaching: 15
exercises: 5
---


::::::::::::::::::::::::::::::::::::::: objectives

- Beschreiben Sie, was ein HPC-System ist
- Ermitteln Sie, welchen Nutzen ein HPC-System für Sie haben könnte.

::::::::::::::::::::::::::::::::::::::::::::::::::

:::::::::::::::::::::::::::::::::::::::: questions

- Warum sollte ich mich für High Performance Computing (HPC) interessieren?
- Was kann ich von diesem Kurs erwarten?

::::::::::::::::::::::::::::::::::::::::::::::::::

Häufig wachsen Forschungsprobleme, die mit Hilfe von Computern bearbeitet werden, über
die Möglichkeiten des Desktop- oder Laptop-Computers hinaus, auf dem sie begonnen haben:

- Ein Statistikstudent möchte ein Modell kreuzvalidieren. Dazu muss das Modell 1000 Mal
  ausgeführt werden, aber jeder Durchlauf dauert eine Stunde. Die Ausführung des Modells
  auf einem Laptop würde über einen Monat dauern! Bei diesem Forschungsproblem werden
  die Endergebnisse berechnet, nachdem alle 1000 Modelle gelaufen sind, aber
  normalerweise wird immer nur ein Modell (in **Serie**) auf dem Laptop ausgeführt. Da
  jeder der 1000 Durchläufe unabhängig von den anderen ist, ist es theoretisch möglich,
  sie alle gleichzeitig (**parallel**) laufen zu lassen, wenn genügend Computer
  vorhanden sind.
- Ein Genomforscher hat bisher mit kleinen Datensätzen von Sequenzdaten gearbeitet, wird
  aber bald eine neue Art von Sequenzierungsdaten erhalten, die zehnmal so groß sind. Es
  ist bereits eine Herausforderung, die Datensätze auf einem Computer zu öffnen - die
  Analyse dieser größeren Datensätze wird ihn wahrscheinlich zum Absturz bringen. Bei
  diesem Forschungsproblem könnten die erforderlichen Berechnungen nicht parallelisiert
  werden, aber ein Computer mit **mehr Speicher** wäre erforderlich, um den viel
  größeren zukünftigen Datensatz zu analysieren.
- Ein Ingenieur verwendet ein Strömungsdynamik-Paket, das über eine Option zur
  parallelen Ausführung verfügt. Bislang wurde diese Option auf einem Desktop nicht
  genutzt. Bei der Umstellung von 2D- auf 3D-Simulationen hat sich die Simulationszeit
  mehr als verdreifacht. Es könnte nützlich sein, diese Option oder Funktion zu nutzen.
  Bei diesem Forschungsproblem sind die Berechnungen in jeder Region der Simulation
  weitgehend unabhängig von den Berechnungen in anderen Regionen der Simulation. Es ist
  möglich, die Berechnungen in jeder Region gleichzeitig (**parallel**) auszuführen,
  ausgewählte Ergebnisse je nach Bedarf an benachbarte Regionen weiterzugeben und die
  Berechnungen zu wiederholen, um zu einem endgültigen Satz von Ergebnissen zu
  konvergieren. Beim Übergang von einem 2D- zu einem 3D-Modell nimmt **sowohl die
  Datenmenge als auch die Menge der Berechnungen stark zu**, und es ist theoretisch
  möglich, die Berechnungen auf mehrere Computer zu verteilen, die über ein gemeinsames
  Netzwerk kommunizieren.

In all diesen Fällen ist der Zugang zu mehr (und größeren) Computern erforderlich. Diese
Rechner sollten gleichzeitig nutzbar sein, um **parallel** die Probleme vieler Forscher
zu lösen.

## Jargon Busting Präsentation

Öffnen Sie den [HPC Jargon Buster](../files/jargon#p1) in einer neuen Registerkarte. Um
den Inhalt zu präsentieren, drücken Sie `C`, um eine **c**lone in einem separaten
Fenster zu öffnen, und drücken Sie dann `P`, um den **p**Darstellungsmodus umzuschalten.

:::::::::::::::::::::::::::::::::::::: challenge

## Ich habe noch nie einen Server benutzt, oder?

Nehmen Sie sich eine Minute Zeit und überlegen Sie, für welche Ihrer täglichen
Interaktionen mit einem Computer Sie möglicherweise einen entfernten Server oder sogar
einen Cluster benötigen, um Ergebnisse zu erhalten.

::::::::::::::: solution

## Einige Ideen

- Abrufen von E-Mails: Ihr Computer (möglicherweise in Ihrer Hosentasche) kontaktiert
  einen entfernten Rechner, authentifiziert sich und lädt eine Liste neuer Nachrichten
  herunter; außerdem werden Änderungen des Nachrichtenstatus hochgeladen, z. B. ob Sie
  die Nachricht gelesen, als Junk markiert oder gelöscht haben. Da Ihr Konto nicht das
  einzige ist, ist der Mailserver wahrscheinlich einer von vielen in einem
  Rechenzentrum.
- Bei der Online-Suche nach einem Begriff wird der Suchbegriff mit einer umfangreichen
  Datenbank aller bekannten Websites verglichen und nach Übereinstimmungen gesucht.
  Diese "Abfrage" kann ganz einfach sein, aber der Aufbau dieser Datenbank ist eine
  [monumentale Aufgabe][mapreduce]! Bei jedem Schritt sind Server beteiligt.
- Bei der Suche nach einer Wegbeschreibung auf einer Karten-Website werden (A) Start-
  und (B) Endpunkt durch [Durchqueren eines Graphen][Dijkstra] verbunden, um den
  "kürzesten" Weg nach Entfernung, Zeit, Kosten oder einer anderen Metrik zu finden. Die
  Umwandlung einer Karte in die richtige Form ist relativ einfach, aber die Berechnung
  aller möglichen Routen zwischen A und B ist aufwendig.

Das Abrufen von E-Mails könnte seriell sein: Ihr Rechner verbindet sich mit einem Server
und tauscht Daten aus. Eine Suche durch Abfrage der Datenbank nach Ihrem Suchbegriff
(oder Endpunkten) könnte ebenfalls seriell sein, da ein Rechner Ihre Anfrage erhält und
das Ergebnis zurückgibt. Das Zusammenstellen und Speichern der gesamten Datenbank
übersteigt jedoch bei weitem die Möglichkeiten eines einzelnen Rechners. Daher werden
diese Funktionen parallel von einer großen, ["hyperscale"][hyperscale] Sammlung von
Servern ausgeführt, die zusammenarbeiten.



:::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::



[mapreduce]: https://en.wikipedia.org/wiki/MapReduce
[dijkstra]: https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm
[hyperscale]: https://en.wikipedia.org/wiki/Hyperscale_computing


:::::::::::::::::::::::::::::::::::::::: keypoints

- Beim High Performance Computing (HPC) wird in der Regel eine Verbindung zu sehr großen
  Rechensystemen an anderen Orten der Welt hergestellt.
- Diese anderen Systeme können für Arbeiten verwendet werden, die auf kleineren Systemen
  entweder unmöglich oder sehr viel langsamer wären.
- HPC-Ressourcen werden von mehreren Benutzern gemeinsam genutzt.
- Die Standardmethode zur Interaktion mit solchen Systemen erfolgt über eine
  Befehlszeilenschnittstelle.

::::::::::::::::::::::::::::::::::::::::::::::::::



