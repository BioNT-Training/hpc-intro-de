---
title: Einrichtung
---


Es gibt einige Software, die Sie vor dem Workshop installieren müssen. Obwohl Sie
während des Workshops Hilfe bei der Installation erhalten, empfehlen wir Ihnen, diese
Programme bereits vorher zu installieren (oder zumindest herunterzuladen).

1. [Eine Terminalanwendung oder
   Befehlszeilenschnittstelle](#where-to-type-commands-how-to-open-a-new-shell)
2. [Eine Secure Shell-Anwendung](#ssh-for-secure-connections)

:::::::::::::::::::::::::::::::::::::::::: prereq

## Bash und SSH

Diese Lektion erfordert eine Terminalanwendung (`bash`, `zsh`, oder andere) mit der
Fähigkeit, eine sichere Verbindung zu einem entfernten Rechner (`ssh`) herzustellen.


::::::::::::::::::::::::::::::::::::::::::::::::::

## Wo man Befehle eingibt: Wie man eine neue Shell öffnet

Die Shell ist ein Programm, das es uns ermöglicht, Befehle an den Computer zu senden und
Ausgaben zu erhalten. Sie wird auch als Terminal oder Kommandozeile bezeichnet.

Einige Computer enthalten ein Standard-Unix-Shell-Programm. Die folgenden Schritte
beschreiben einige Methoden zur Identifizierung und zum Öffnen eines
Unix-Shell-Programms, wenn Sie bereits eines installiert haben. Es gibt auch
Möglichkeiten, ein Unix-Shell-Programm, einen Linux/UNIX-Emulator oder ein Programm für
den Zugriff auf eine Unix-Shell auf einem Server zu identifizieren und herunterzuladen.

### Unix-Shells unter Windows

Auf Computern mit Windows-Betriebssystemen ist nicht automatisch ein Unix-Shell-Programm
installiert. In dieser Lektion empfehlen wir Ihnen die Verwendung eines Emulators, der
in Git für Windows enthalten ist und Ihnen Zugang sowohl zu Bash-Shell-Befehlen als auch
zu Git bietet. Wenn Sie an einem Software Carpentry-Workshop teilgenommen haben, haben
Sie wahrscheinlich bereits Anweisungen zur Installation von Git für Windows erhalten.

Nach der Installation können Sie ein Terminal öffnen, indem Sie das Programm Git Bash
aus dem Windows-Startmenü starten.

#### Shell-Programme für Windows

- [Git für Windows][git4win] -- *Empfohlen*
- [Windows Subsystem für Linux][wsl] -- erweiterte Option für Windows 10

:::::::::::::::::::::::::::::::::::::: discussion

## Alternativen zu Git für Windows

Für die Ausführung von Bash-Befehlen unter Windows gibt es andere Lösungen. Es gibt
jetzt ein Bash-Shell-Befehlszeilentool für Windows 10. Außerdem können Sie Bash-Befehle
auf einem Remote-Computer oder -Server, der bereits über eine Unix-Shell verfügt, von
Ihrem Windows-Rechner aus ausführen. Dies kann normalerweise über einen Secure Shell
(SSH)-Client erfolgen. Ein solcher Client, der für Windows-Computer kostenlos erhältlich
ist, ist [PuTTY][putty]. Informationen zur Installation und Verwendung von PuTTY, zur
Verwendung des Windows 10-Befehlszeilentools oder zur Installation und Verwendung eines
Unix/Linux-Emulators finden Sie in der folgenden Referenz.

Für fortgeschrittene Benutzer können Sie eine der folgenden Alternativen wählen:

- Installieren Sie das [Windows Subsystem für Linux][wsl]
- Verwenden Sie die Windows [PowerShell][ms-shell]
- Informieren Sie sich über [Verwendung eines Unix/Linux-Emulators][unix-emulator]
  (Cygwin) oder Secure Shell (SSH) Client (PuTTY)

::::::::::::::::::::::::::::::::::::::: challenge

## Warnung

Die Befehle im Windows Subsystem für Linux (WSL), in der PowerShell oder in Cygwin
können sich geringfügig von denen unterscheiden, die in der Lektion gezeigt oder im
Workshop vorgestellt werden. Bitte fragen Sie nach, wenn Sie eine solche Abweichung
feststellen - Sie sind wahrscheinlich nicht allein.



::::::::::::::::::::::::::::::::::::::::::::::::::

::::::::::::::::::::::::::::::::::::::::::::::::::

### Unix-Shell auf macOS

Unter macOS ist die Standard-Unix-Shell durch Ausführen des Terminal-Programms aus dem
Ordner `/Application/Utilities` im Finder zugänglich.

Um Terminal zu öffnen, versuchen Sie eine oder beide der folgenden Möglichkeiten:

- Wählen Sie im Finder das Menü "Go" und dann "Utilities". Suchen Sie Terminal im Ordner
  "Utilities" und öffnen Sie es.
- Verwenden Sie die Mac 'Spotlight' Computer-Suchfunktion. Suchen Sie nach: `Terminal`
  und drücken Sie <kbd>Return</kbd>.

Für eine Einführung, siehe [How to Use Terminal on a Mac][mac-terminal].

### Unix-Shell unter Linux

Bei den meisten Linux-Versionen ist die Standard-Unix-Shell über das [(Gnome)
Terminal](https://help.gnome.org/users/gnome-terminal/stable/) oder [(KDE)
Konsole](https://konsole.kde.org/) oder [xterm](https://en.wikipedia.org/wiki/Xterm)
zugänglich, die über das Anwendungsmenü oder die Suchleiste gefunden werden können.

### Sonderfälle

Wenn keine der oben genannten Optionen auf Ihre Situation zutrifft, versuchen Sie eine
Online-Suche nach: `Unix shell [your operating system]`.

## SSH für sichere Verbindungen

Alle Schüler sollten einen SSH-Client installiert haben. SSH ist ein Werkzeug, mit dem
wir uns mit einem entfernten Computer verbinden und ihn wie unseren eigenen benutzen
können.

### SSH für Windows

Git für Windows wird mit vorinstalliertem SSH geliefert: Sie müssen nichts weiter tun.

:::::::::::::::::::::::::::::::::::::: discussion

## GUI-Unterstützung für Windows

Wenn Sie wissen, dass die Software, die Sie auf dem Cluster ausführen werden, eine
grafische Benutzeroberfläche benötigt (ein GUI-Fenster muss geöffnet sein, damit die
Anwendung richtig läuft), installieren Sie bitte
[MobaXterm](https://mobaxterm.mobatek.net) Home Edition.


::::::::::::::::::::::::::::::::::::::::::::::::::

### SSH für macOS

macOS kommt mit vorinstalliertem SSH: Sie müssen nichts tun.

:::::::::::::::::::::::::::::::::::::: discussion

## GUI-Unterstützung für macOS

Wenn Sie wissen, dass die Software, die Sie ausführen werden, eine grafische
Benutzeroberfläche benötigt, installieren Sie bitte [XQuartz](https://www.xquartz.org).


::::::::::::::::::::::::::::::::::::::::::::::::::

### SSH für Linux

Linux kommt mit vorinstallierter SSH- und X-Window-Unterstützung: Sie müssen nichts tun.

<!-- links -->

[git4win]: https://gitforwindows.org/
[wsl]: https://docs.microsoft.com/en-us/windows/wsl/install-win10
[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/
[ms-shell]:
https://docs.microsoft.com/en-us/powershell/scripting/learn/remoting/ssh-remoting-in-powershell-core?view=powershell-7
[unix-emulator]: https://www.cygwin.com/
[mac-terminal]:
https://www.macworld.co.uk/feature/mac-software/how-use-terminal-on-mac-3608274/




