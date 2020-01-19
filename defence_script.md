# PhD Verteidigung - Skript

## Willkommen
- vielen Dank für die Einführung
- wie angesprochen möchte ich heute meine Arbeit über konvektionsdom. Grenzschichten und deren Simulation näherbringen.

## Einleitung, Motivation
- Stoffübergang an aufsteigenden Blasen spielt eine zentrale Rolle in einer vielzahl von Mehrphasenreaktoren die in der chem. Industrie zum Einsatz kommen.
- Solche Reaktoren gibt es vielfältigen Größen und Formen. Sie können wenige Millimeter groß sein wie links oder auch so hoch wie ein mehrstöckiges Haus sein.
- Die Möglichkeiten die Eigenschaften dieser Reaktoren mit numerischen Werkzeugen vorherzusagen ist immernoch ziemlich limitiert und ein ganz wesentlicher Grund dafür ist die Mehrskaligkeit der Transportprozesse im Innteren der Reaktoren.
- Diese Mehrskaligkeit sieht man sehr schön im Nachlauf der Taylorblase hier links.
- Wir haben hier eine Sauerstoffblase die sich auflöst und der Sauerstoff reagiert dann mit einem Kupferkomplex in der Flüssigkeit und führt zu dem Farbausschlag den sie sehen.
- Jetzt sieht es so aus als der Sauerstoff am unteren Ende der Blase austritt. Tatsächlich geht aber an der gesamten Blasenoberfläche Sauerstoff in die Flüssigphase über und wird dann in einer sehr dünnen Schicht ans untere Ende der Blase transportiert und erst durch die Strömungsablösung die am unteren Ende auftritt wird der gelöste Sauerstoff sichtbar.

<hr>

- Diese Grenzschicht kann man sichtbar machen indem man eine hochauflösende Kamera verwendet und sehr sehr nah an die Grenzfläche heranzoomt.
- wir sehen, dass die Grenzschicht im Bild nur wenige Mikrometer dick ist und das stellt numerische Verfahren vor große Probleme bezüglich der Auflösung.
- In der Literatur ist in diesem Zusammenhang häufig von dem high-Pe number problem die Rede.
- Warum das so ist möchte ich ihnen an einer kurzen Beispielrechnung darlegen.

<hr>

- Wir betrachten dazu eine 1mm große Sauerstoffblase die in Wasser bei Raumtemperatur aufsteigt.
- Die Pe-Zahl ist definiert als ...
- darin sind Sc und Re definiert als ...
- Für die Re-Zahl ergibt sich bei einer solchen Blasengröße ein Wert von 250.
- Die Dicke der Grenzschicht skaliert mit ...; etwas 20 Mal kleiner als Durchmesser
- Die Grenzschicht des gelösten Sauerstoffes ist nochmal deutlich dünner.
- Das Verhältnis skaliert nämlich mit ...
- Limit

## Übersicht

- Ich möchte Ihnen heute Modellierungsansätze vorstellen mit denen man diese Genzschichten auch für physikalische Materialparameter sehr genau annähren kann.
- Ein ganz wesentlicher Schritt ist dabei zu verstehen, was eigentlich bei der numerischen Standardlösung schiefläuft, um dann darauf aufbauend eine geeignete SGS Modellierung zu entwickeln.
- Dank eines solchen Modells kann man dann auch experimentelle Ergebnisse reproduzieren und verstehen, was ich zum Ende des Vortrags kurz anreisen werde.

## Folgen unzureichender Gitterauflösung

- Abbildung erklären
- 1D Schnitt durch Lösung

<hr>

- Verlauf exaktes Profil
- In FV nur mittelwerte, hier 2 Zellen in BL
- Bei Diskretisierung von Diffusion, Konvektion werden Ableitungen und Werte auf Zellflächen benötigt
- Typischerweise lineare Rekonstruktion
- Unterschätzung an Grenzfläche, Überschätzung an erstes Zellfläche
- Interpolationsfehler auflösungsabhängig

<hr>

- oben gleiche Abbildung wie zuvor allerdings mit zunehmender Anzahl von Zellen
- unten Interpolationsfehler
- wichtig: Monotonie und Vorzeichen
- Da Auflösung praktisch limitiert ist, brauchen wir ein Verfahren um Interpolationsfehler zu reduzieren zB mit SGS Modellierung

## Subgridskalenmodellierung

- Grundidee
- 3 Schritte

<hr>

- Bild erklären: konstante Überströmung einer planaren Grenzfläche
- wesentliche Transportterme

<hr>

- Lösung mit Term der Parameter wird
- Wahl eines geeignten Schnittes durch Grenzschicht
- Standard Vorgehen

<hr>

- Standard Korrektur
- spart eine Verfeinerungsstufe
- mit jeder Stufe nimmt Anzahl der Zellen etwa um eine Größenordnung zu (in 3D)
- mindest. 2 Mal Zeitschritte
- wir brauchen Ersparnis von 20 oder mehr Verfeinerungsstufen
- Ansatz meiner Arbeit: wie kann man Modell besser machen
- Lösung teilweise vorweggenommen

<hr>

- Idee
- nicht trifvial wegen versteckter Komplexität
- Hintergundgitter nicht immer an Grenzfläche orientiert, Beispiel
- für Flussberechnung in z muss Profilfunktion nicht an einer Stelle ausgewertet, sondern über straffierte Flächen gemittelt werden
- auch dann würde es nicht funktionieren, weil es häufig vorkommt, dass Profilfunktion Flüsse vorhersagt die Zelle übervoll machen oder mehr aus einer Zelle entnehmen als sie enhält
- um das zu verhindern muss man zusätzlich etwas an der Anpassung der Profilfunktion verändern

<hr>

- hier sehen wir das gleiche Szenario wie zuvor, allerdings wird jetzt eine veränderte Bedingung verwendet um delta zu ermitteln
- Diesmal interpretieren wir c_num als Mittelwert im FV Sinn.

<hr>

- die veränderte Anpassung und Korrekur erlauben es jetzt den gesamten relevanten Bereich bezüglicher der Schmidtzahl zu untersuchen
- Bild erklären
- passt auch quantitativ wie man hier anhand der globalen Sherwoodzahl sieht
- Sh definiert als
- Profilfkt. große Einschränkung; was tun bei chemischen Reaktionen?


## Datengetriebende Modellierung

- Um SGS allgemein anwendbar zu machen habe ich den Weg der datengetriebenen Modellierung gewählt
- die grundlegende Idee ist es die analytische Lösung durch ein datagetriebenes Modell zu ersetzen.
- 3 Schritte

<hr>

- Bevor ich genauer auf die datengetriebene Modellierung eine möchte ich eine ganz kurze Einführung in die ML Terminology geben die ich gleich verwende.
- Im ML ist meistens die Rede von Features und Labels.
- Features sind im Wesentlichen Funktionsargumente einer unbekannten Funktion die wir annähren möchten und Labels sind die zugehörigen Funktionswerte.
- Ein ML Modell ist eine Funktion mit freien Parametern mit der wir versuchen diese unbekannte Funktion anzunäheren.
- Das Ziel des ML ist es den Unterschied zwischen Vorhersage und gegebenen Labels in einer gegebenen Metrik möglichst klein zu machen.

<hr>
- im Vergleich zum analytischen SGS realistischeres Ersatzproblem
- Setup erklären
- Parameterstudie
- Tipps und Tricks

<hr>

- wie erzeugt man jetzt Ersatzmodell aus Daten
- in gesamter Arbeit MLP
- um Parameter anzupassen MSE
- Praktischer Tipp: 1 Modell pro Label + Beispiel
- PyTorch
- Validierung nicht einfach, Korrelation zu ungenau

## Hybrider Ansatz zur Modellbewertung

- um hochgenaue und Vergleichsdaten zu erzeugen die sehr nah an der tatsächlichen Lösung der 2-Phasenproblems mit Speziestransport sind, habe ich ein hybrides Simulationsverfahren entwickelt
- Idee
- wie mmacht man das

<hr>

- Schritte anhand einer dimpled ellipsoidal Blase
- Test an einer vielzahl von relevanten Blasenformen, dimpled ellip. eine der komplizierteren
- Schritte 1-3
- ML für Schritt 2 sehr wichtig

<hr>

- Schritte 4-6
- Schritt 5 verwendet ML Modelle
- hochgenaue Vergleichsdaten

<hr>

- Anhand der Vergleichsdaten habe ich dann untersucht wie sich das SGS Modell bei verschieden Auflösungen verhält
- Mesh 3 ist ausreichend um Test mit Standarddiskret. direkt aufzulösen

<hr>

- kleiner Auszug der Tests für dimpled-ellipsoidal Blase
- Sie sehen hier die globalen Sh Zahlen für Physisorp. und eine Zerfallsreak. berechnet mit Standard-Disk. und SGS-Modell auf den 4 Gittern die wir eben gesehen haben.
- Referenz für relative Abweichung ist Wert ganz links
- SGS Modell im gesamten Bereich im niedrigen Prozentbereich während lineare Int. zu einem Fehler von 30-40 Prozent führt
- auch wichtig: SGS Modell stimmt mit Standardverfahren bei hoher Auflösung überein.


## Vergleich mit Experimenten

- mit Hilfe der SGS Modellierung können wir jetzt realistische Materialparameter untersuchen und damit auch einen quantitativen Vergleich mit Experimenten anstreben
- das haben wir gemacht in Zusammenarbeit mit der Gruppe von ...
- Untersuchungsgegenstand, Experiment ...
- Auf simulationsseite IT

<hr>

- IT implementiert Sharp Interface Modell
- Idee ist: 3 Bilanzräume
- Grenzfläche ohne Masse und Impuls
- stattdessen löst man Transportgl. für Masse und Impuls in beiden Phasen
- Kopplung der Gl. an der Grenzfl. durch Sprungbed.
- Marangoni-Term

<hr>

- variable Oberflächensp. durch Surfactant
- IT implementiert Bibliothek mit der man Surfactant-Ad. berücksichtigen kann
- Modelle für sigma(c)
- Sorbtionsmodell die sagen wieviel Surfactant wo Adsorbiert wird
- Sprungbed. zeigt Gradient normal zum Interface
- nur mit SGS Modell möglich
- ohne Modell numerische Instabilitäten und völlig falsche Aufstiegsgeschw.
- wozu braucht man Modell hier?

<hr>

- aus Didak. gründen baue ich Diagramm sonst auf, heute aus Zeitgründen alle Ergebnisse zusammen
- Geschw. erklären

<hr>

- Bild erklären
- Wirbel erkennen wir wieder ...

<hr>

- ... wenn wir auf Sauerstoffkonz. im Nachlauf schauen
- links Exp. rechts Sim.
- bessere Übereinst. möglich
- Vorteil von Simulation: Parametervariation, lokale Größen, Einfluss von Surfactant auf Verstärkungsfaktor

## Zusammenfassung und Ausblick

- damit bin ich mit dem Inhalt am Ende angelangt und möchte noch einmal zusammenfassen
- Ich habe heute etwas über das high-Pe Problem erzählt und welche Auswirkungen es auf die Qualität der numerischen Lösung hat
- darauf aufbauend habe ich dann erklärt wie man die numerische Lösung mit Hilfe einfacher Ersatzprobleme deutlich verbessern kann
- um die Generalisierung des Modells zu untersuchen habe ich außerdem einen hybriden Ansatz vorgestellt mit dem man hochgenaue Referenzdaten erzeugen kann
- Dank der SGS Modelle sind wir jetzt in der Lage relevante Parameterbereiche numerisch zu untersuchen und somit auch exp. Ergebnisse zu bestätigen
- was ich nicht gezeigt habe...

<hr>

- woran ich gerade arbeite...
- interessant wäre auch
