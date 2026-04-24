# Life2090

Conway's [Game of Life](https://de.wikipedia.org/wiki/Conways_Spiel_des_Lebens) auf dem Busch Microtronic 2090

![Life](/pics/life.jpg)

## Hinweis

Gleich vorweg: Du solltest irgendetwas haben, das die Signale an den Ausgängen des Microtronic 2090 sinnvoll interpretiert und darstellt. Und außerdem den _Takt_ vorgibt, denn die Übermittlung der einzelnen Bit-Sequenzen ist zeitkritisch. Diese Aufgaben übernimmt das [ESP2090-Studio](https://github.com/rab-berlin/ESP2090) - was du dazu wissen musst, findest du auf der entprechenden Github-Seite.

Wenn du das ESP2090-Studio noch nicht hast, dann empfehle ich, das erst einmal zusammenzubauen - es sei denn, du sagst: "Es reicht mir, algorithmische Schönheit in Gedanken zu bestaunen", dann auch gut. Aber zusammenlöten, anfassen und austesten macht mehr Spaß. Isso. Schreib mir, vielleicht hab ich noch ne Platine und Teile bei mir rumliegen.

Zum Nachlesen hier das - wie immer liebevoll dokumentierte - [Programm Life2090](https://github.com/rab-berlin/Life2090/blob/main/program/Life.pdf). Und hier die [MIC-Datei](https://github.com/rab-berlin/Life2090/blob/main/program/life.mic), die du mit geeigneten Mitteln auf deinen Microtronic bringen musst.

## Hintergrund

_Life_ kennst du, oder? Nein? Dann bist du kein Nerd der 80er-Jahre. Oder gar kein Nerd. Aber was machst du dann auf dieser Seite? Geh weg... Nur Spaß. Bleib natürlich, wenn du magst. Aber lies erstmal in Ruhe, [was es mit _Life_ auf sich hat](https://de.wikipedia.org/wiki/Conways_Spiel_des_Lebens). Es ist ein sehr interessantes Stück Computer-Geschichte. 

In den 80er-Jahren lief dieses Programm auf jedem Heimcomputer mindestens einmal in irgendeiner Variante. Wir erfreuten uns daran, wie der Computer immer neue Generationen ausrechnete und darstellte. Bewunderten vielleicht manchmal die Geschwindigkeit, in der sich das emsige Treiben auf dem Bildschirm abspielte. Und mancher - wir wollen keine Namen nennen - versuchte womöglich, die philosophischen Aspekte dieser Simulation zu ergründen... Woher kommen wir? Wohin gehen wir? Hat alles ein Ende? ... Ok, genug... Ich hab´s wieder im Griff :-)

Mein erster Computer, der Busch Microtronic 2090, konnte das leider nicht. Denn er hatte ja keinen Bildschirm... Bis jetzt!

## Bedienung

_Life_ ist ja eigentlich kein echtes Spiel. Du bestimmst die Anfangskonfiguration und schaust danach mehr oder weniger interessiert zu, welche neuen Figuren und Muster daraus entstehen. Insofern ist die Interaktion mit den Programm sehr trivial.

### Anordnung der Zellen

Die Zellen in der LED-Matrix sind wie der positive Quadrant eines kartesischen Koordinatensystems angeordnet. Spalten gehen von links nach rechts (x-Achse), Reihen gehen von unten nach oben (y-Achse). Die Register 0-F folgen dieser Anordnung, das niedrigstwertige Bit eines einzelnen Registers (LSB) ist ganz links, das höchstwertige (MSB) ganz rechts.

Als Beispiel ein paar Zellen im Life-Spielfeld:

| Reihe | Reg. | Bits<br>LSB → MSB<br>1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4 | Bits<br>LSB → MSB<br>1&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;2&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;3&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;4 | Reg. | |
| :---: | :---: | :---: | :---: | :---: | :--- |
| **8** | E | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | F | |
| **7** | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| **6** | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| **5** | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | |
| **4** | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | |
| **3** | 4 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 5 | |
| **2** | 2 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 3 | |
| **1** | 0 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 1 | | 
| | **Spalte** | **1**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**2**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**3**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**4** | **5**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**6**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**7**&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**8** | | |

- Die Zellen (3,4), (4,4) und (5,4) leben - im Register 4 steht daher der Wert C (Bits 3 und 4 gesetzt) und im Register 5 der Wert 1 (Bit 1 gesetzt). 
- Die Zelle (5,6) lebt - im Register 7 steht der Wert 1 (Bit 1 gesetzt).
- Die Zelle (4,5) lebt - im Register 8 steht der Wert 8 (Bit 4 gesetzt).
- Alle anderen Zellen leben nicht, also steht der Wert 0 in den übrigen Registern.

### Start

Aus vorherigen Programmläufen können noch Werte in den Registern liegen, deshalb solltest du vor Programmstart einmal die **grüne RESET-Taste** drücken. Auf der **Weboberfläche des ESP2090-Studios** muss der Button **LED Start** gedrückt sein, sonst siehst du nix. Dann startest du das Programm mit **HALT-NEXT-00-RUN** und wählst aus, ob du die Werte schon eingegeben hast, ob du sie noch eingeben willst, oder ob du eine im Programm hinterlegte Figur zum Start laden willst.

- Taste 0 - die Werte wurden bereits über HALT-REG-x in die Register 0-E eingegeben (Register F ist immer 0)
- Taste 1 - Uhr
- Taste 2 - Kröte
- Taste 3 - Bipole
- Taste 4 - Gleiter
- Taste 5 - Segler
- Taste 6 - Pentomino
- Taste 7 - Suppe (ein zufälliges Startmuster)
- Taste F - die Werte werden anschließend erfasst, jedes Register von 0 bis F separat

### Eingabe der Register

Wenn du selbst ein Startmuster eingeben willst, dann hast du die Wahl. 

Entweder füllst du alle nötigen Register schon vor Programmstart (mit HALT-REG-x), startest dann das Programm und wählst 0, um die Berechnung sofort anzuwerfen. Beachte aber, dass Register F bei dieser Auswahl _bauartbedingt_ immer 0 sein wird.

Oder du startest das Programm und wählst F, um anschließend alle Register von 0 bis F der Reihe nach einzugeben. Ein Blick auf die Anordnung von Zellen und Registern in der Darstellung oben hilft dir dabei, die richtigen Register mit den richtigen Werten zu füllen. Probier einmal:

```
C, 3, 2, 4, 9, 9, 5, A, 1, 8, 5, A, 2, 4, C, 3 
```

## Entstehung

Wie üblich - der erste Gedanke war: Müsste doch vielleicht irgendwie gehen... Der zweite Gedanke: Nee, kann nicht klappen, zu viele Programmschritte, zu wenig Register.

Zunächst einmal haben wir - bedingt durch die LED-Matrix - eine 8x8-Welt mit 64 Zellen, das passt genau in die 16 Register einer Registerbank (Arbeits- oder Speicherregister). Die anderen Register könnte man dann doch zur Berechnung der Folgegeneration verwenden...

### Erster Ansatz

Den allerersten Ansatz habe ich nach kurzer Überlegung gar nicht weiter verfolgt: Jedes einzelne Register mit den jeweils fünf angrenzenden Registern vergleichen und die Bits dann entsprechend setzen. Um etwa Register 2 auszuwerten, müssten Vergleiche mit den Registern 0, 1, 3, 4 und 5 durchgeführt werden:

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| | E | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | F | |
| | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | |
| | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | |
| | 4 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 5 | |
| | 2 | :green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 3 | |
| | 0 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 1 | | 


Für Register 6 brauchen wir aber Vergleiche mit anderen Registern, nämlich 4, 5, 7, 8 und 9:

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| | E | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | F | |
| | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| | 8 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 9 | |
| | 6 | :green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 7 | |
| | 4 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 5 | |
| | 2 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 3 | |
| | 0 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 1 | | 

Andere Register bedingen aber anderen Code. Denn leider **können wir Register nicht indirekt adressieren** (also elegant Register-Pointer auf die jeweils relevanten Register mitlaufen lassen) - dafür gibt es im Befehlssatz des Microtronic schlicht keine Instruktionen. Das bedeutet, jedes Register bekäme seinen eigenen Code-Teil im Programm. Also 16 mal _irgendwie fast das gleiche_, aber mit jeweils wechselnden Registern. Das hätte wahrscheinlich eine knappe Million Instruktionen erfordert.

### Zweiter Ansatz

Die nächste Idee: Auszuwertende Register aus der unteren Speicherhälfte werden nacheinander in ein Testregister in der oberen Hälfte geschoben. Die bis zu fünf relevanten Nachbarregister werden ebenfalls nach oben geschoben. Dann wird die (stets identische) Auswertungsroutine für das Testregister ausgeführt und das Ergebnis zurück an die Stelle des gerade ausgewerteten Registers geschrieben - diesmal aber in das Speicherregister als neue Generation.

Wenn z.B. das Register 4 ausgewertet werden soll:

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| TEST | E | :green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | F | NEBEN |
| UNTEN | C | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | D | OBEN |
| NEBEN-U | A | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | B | NEBEN-O |
| | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | |
| OBEN ← | 6 | :orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | :orange_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | 7 | → NEBEN-O |
| TEST ← | 4 | :yellow_circle:&nbsp;&nbsp;&nbsp;:yellow_circle:&nbsp;&nbsp;&nbsp;:yellow_circle:&nbsp;&nbsp;&nbsp;:yellow_circle: | :orange_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | 5 | → NEBEN |
| UNTEN ← | 2 | :orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | :orange_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | 3 | → NEBEN-U |
| | 0 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 1 | | 

Zwar gab es in diesem Ansatz nur noch einen einzigen, immer gleichen Code-Teil für die Auswertung, da stets die Register A-F ausgewertet werden. Aber die zahlreichen MOV-Operationen machten das Programm insgesamt trotzdem nicht kürzer, eher im Gegenteil. 

Ich hatte mal drauflos programmiert... und der Programmspeicher war bereits voll, als ich noch nicht einmal die Hälfte der Register codiert hatte. Diese Idee passte also auch nicht in die verfügbaren 256 Programmschritte. Zudem wurde die Menge der noch übrigen Register knapp, im Prinzip standen nur noch die beiden Register 8 und 9 zur Verfügung. Bedenklich.

### Dritter Ansatz

Die Idee, nur einen einzigen Code-Teil für die Auswertung zu haben, fühlte sich aber richtig an. Denn dann muss ich lediglich das **immer gleiche Register** mit den **immer gleichen Nachbarregistern** vergleichen, was den Code für die Auswertung deutlich verschlankt. Das Problem lag nur noch darin, dass für jedes Register zu viele und - schlimmer noch - jeweils andere Verschiebe-Operationen durchgeführt werden mussten, insbesondere das Verschieben der fünf Nachbarregister machte den Ansatz sehr _teuer_. 

Wie wäre es denn, wenn wir - wie im zweiten Ansatz - nur ein einziges, immer gleiches Register in der unteren Hälfte des Arbeitsspeichers auswerten und durch einen _clever durchdachten Verschiebebahnhof_ dafür sorgen, dass alle fünfzehn anderen Register nacheinander in diesem speziellen Register landen, um dort mit der immer gleichen Routine bearbeitet zu werden? Die Frage ist natürlich rhetorisch... es zeigte sich, dass das die Lösung war. 

Dieses spezielle Register sollte fortan Register 0 sein:

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| UNTER-R0 | E | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | F | UNTER-R1 |
| | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | |
| | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | |
| | 4 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 5 | |
| | 2 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 3 | |
| | 0 | :green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 1 | | 

Die Berechnung der neuen Generation soll jeweils nur für eine Hälfte des Life-Feldes erfolgen: In den Arbeitsregistern 0-7 sind die auszuwertenden Register, in den Arbeitsregistern 8-F haben wir dann noch Platz für Schleife, Testregister, Zähler usw. Nach der ersten Hälfte werden Oben und Unten getauscht - und so die zweite Hälfte berechnet.

Wenn Register 0 ab jetzt immer das auszuwertende Register ist, dann brauchen wir in einem toroidalen Spielfeld auch die Register "darunter", also E und F. Diese Register liegen aber eigentlich in den Speicherregistern, wir müssen die Register E und F daher in die Arbeitsregister "einblenden", damit sie in die Auswertung einbezogen werden. Gleichzeitig werden wir später auch die Register "oberhalb" von Register 6 benötigen, die deswegen ebenfalls in die Arbeitsregister einzublenden sind.

Also befinden sich Kopien der Speicherregister 8, 9, E und F in den entsprechenden Arbeitsregistern, es bleiben dann nur noch die vier Register A-D für Operationen des Programms übrig. 

Mehr braucht der Algorithmus zur Auswertung aber auch nicht: 

- KOPIE-Register: Kopie des jeweils auszuwertenden Registers, wird durch Bitoperationen "verbraucht"
- ANZAHL-Register: Zähler für die Gesamtzahl der lebenden Nachbarn einer Zelle
- ERGEBNIS-Register: Ergebnis der Auswertung, wird schrittweise mit den berechneten Bits gefüllt und rotiert
- SCHLEIFE-Register: Schleifenzähler (Hinweis weiter unten)

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| UNTER-R0 | E | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | F | UNTER-R1 |
| KOPIE | C | :black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle: | :black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle: | D | SCHLEIFE |
| ANZAHL | A | :black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle: | :black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle:&nbsp;&nbsp;&nbsp;:black_circle: | B | ERGEBNIS |
| ÜBER-R6 | 8 | :orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | :orange_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:orange_circle: | 9 | ÜBER-R7 |
| | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | |
| | 4 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 5 | |
| | 2 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 3 | |
| | 0 | :green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 1 | | 

### Schiebung!

Wir erinnern uns: Das aktuelle Life-Spielfeld liegt in den **Speicher**registern 0-F. Nur zur Anzeige mit dem Unterprogramm _Frame_ werden die Speicher- mit den Arbeitsregistern getauscht (EXRL und EXRM) und schnell hintereinander auf die Ausgänge gelegt (DOT).

Für die Auswertung der neuen Generation schieben wir die untere Hälfte der Speicherregister-Bank durch EXRL in die **Arbeits**register 0-7. Außerdem blenden wir die angrenzenden vier Register UNTER-R0, UNTER-R1 sowie ÜBER-R6 und ÜBER-R7 in die Arbeitsregister 8, 9, E und F ein. Damit sind alle nötigen Arbeitsregister befüllt, um die untere Hälfte des Feldes vollständig zu berechnen. 

Wir werten stets nur das Register 0 aus, also müssen alle Register nacheinander in dieses Register geschoben werden. Nachdem also die erste Auswertung durchgeführt wurde, werden alle relevanten Register _ring-getauscht_, damit das nächste Register zur Auswertung im Testregister 0 landet. Register 2 kommt in Register 0, Register 4 in 2, Register 6 in 4, ÜBER-R6 in 4, UNTER-R0 in 8 und schließlich Register 0 in UNTER-R0. Das gleiche passiert auch mit allen Registern auf der "rechten Seite", also mit Register 1, 3, 5 und 7 sowie ÜBER-R7 und UNTER-R1.

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| UNTER-R0<br>(aus 0) | E | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | F | UNTER-R1<br>(aus 1) |
| | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| ÜBER-R6<br>(aus UNTER-R0) | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | ÜBER-R7<br>(aus UNTER-R1) |
| (aus ÜBER-R6) | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | (aus ÜBER-R7) |
| (aus 6) | 4 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 5 | (aus 7) |
| (aus 4) | 2 | :red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 3 | (aus 5) |
| (aus 2) | 0 | :green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle: | 1 | (aus 3) | 







### Wie werden die einzelnen Bits im Register 0 ausgewertet?

Wir haben einen Mechanismus gefunden, durch den nacheinander alle Register in das immer gleiche Test-Register 0 zur Auswertung gelangen. Die eigentliche Auswertung erfolgt dann Bit für Bit. 

Zum Beispiel werden für das vierte Bit (MSB) alle lebenden Nachbarzellen gezählt:

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| UNTER-R0 | E | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | F | UNTER-R1 |
| | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | |
| | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | |
| | 4 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 5 | |
| | 2 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 3 | |
| | 0 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:green_circle: | :red_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 1 | | 

Alle Nachbarregister sowie das Test-Register 0 landen nacheinander im Register KOPIE. Um nur die unmittelbaren Nachbarn zu zählen, werden die betreffenden Bits mit SHL und SHR aus dem Register KOPIE "herausgeschüttelt" und mit ADC zur ANZAHL der lebenden Nachbarzellen addiert.

```
           MOV UNTER-R1,KOPIE         rechter Rand unten in KOPIE
           CALL CountR1               zählt Bit 1
           MOV r1,KOPIE               rechter Rand mitte in KOPIE
           CALL CountR1               zählt Bit 1
           MOV r3,KOPIE               rechter Rand oben in KOPIE
           CALL CountR1               zählt Bit 1
           MOV UNTER-R0,KOPIE         unterer Rand in KOPIE
           CALL CountL2               zählt Bits 3 und 4
           MOV r0,KOPIE               Selbst in KOPIE
           SHL KOPIE                  schiebt Bit 4 heraus (ohne zu zählen)
           CALL CountL1               zählt also nur Bit 3 
           MOV r2,KOPIE               oberer Rand in KOPIE
           CALL CountL2               zählt Bits 3 und 4
           CALL ConwayRules           ermittelt neuen Zustand der Zelle
```

Die anderen Bits des Test-Registers 0 werden ebenfalls entsprechend ausgewertet. Ergebnis-Bits werden in das Register ERGEBNIS geschrieben.

Für die Bits 2 und 3 ist die Ermittlung übrigens weniger aufwändig, da nur drei Register (die beiden Register oberhalb und unterhalb des Testregisters sowie das Testregister selbst) berücksichtigt werden müssen - die Nachbarn rechts und links befinden sich bereits im Testregister 0:

| | Reg. | Bits | Bits | Reg. | |
| ---: | :---: | :---: | :---: | :---: | :--- |
| UNTEN-R0 | E | :white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | F | UNTEN-R1 |
| | C | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | D | |
| | A | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | B | |
| | 8 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 9 | |
| | 6 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 7 | |
| | 4 | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 5 | |
| | 2 | :white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 3 | |
| | 0 | :white_circle:&nbsp;&nbsp;&nbsp;:red_circle:&nbsp;&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;&nbsp;:red_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | 1 | | 

Da nur die Nachbarzellen, nicht aber die Testzelle selbst addiert werden sollen, wird dieses Bit im Register KOPIE vorher mit ANDI gelöscht (Bit-maskiert). 

```
           MOV UNTER-R0,KOPIE         unterer Rand in KOPIE
           CALL CountL3               zählt Bit 2, 3 und 4
           MOV r0,KOPIE               Selbst in KOPIE
           ANDI #A,KOPIE              maskiert Bit 3
           CALL CountL3               zählt also nur Bit 2 und 4
           MOV r2,KOPIE               oberer Rand in KOPIE
           CALL CountL3               zählt Bit 2, 3 und 4
           CALL ConwayRules           ermittelt neuen Zustand der Zelle
```

...

## Geschwindigkeit

Das Programm ist ziemlich rechenintensiv. Die Operationen selbst sind nicht komplex, innerhalb der Schleife werden eigentlich nur ein paar Bits hin- und hergeschoben, ein paar Register getauscht, ein bisschen addiert und verglichen. Aber die Menge macht's...

Um die neue Generation eines **einzelnen Registers** zu berechnen, müssen etwa **174 Instruktionen** abgearbeitet werden. Da der Microtronic mehr als doppelt so schnell rechnet, wenn das Display ausgeschaltet bleibt, ist DISOUT praktisch ein Muss. Dann braucht ein Befehl immer noch 9 ms, also sind 174 Befehle in 1,5 Sekunden durchlaufen. 

Alle 16 Register werden somit _theoretisch_ innerhalb von etwa 25 Sekunden ausgewertet. Nach jeweils vier Registern kommen allerdings noch (relativ aufwändige) Register-Tauschereien dazu, so dass am Ende etwa **31 Sekunden** für Berechnung und Anzeige der kompletten folgenden **Generation** herausspringen. Ein ziemlich gemütlicher Bildschirmschoner.

Testweise habe ich eine Version geschrieben, die auf den Tausch von Rechts und Links verzichtet und stattdessen Register 0 und Register 1 gemeinsam in der inneren Schleife auswertet. Das spart eine Schleifenebene, macht den Programmcode aber deutlich länger, da nun acht statt vier Bits auszuwerten sind. Gemessen habe ich dann xx Sekunden für die Berechnung einer Generation, also xx% Geschwindigkeitssteigerung. Ordentlich, aber zieht jetzt auch nicht die Wurst vom Teller. Ich wollte lieber mehr Platz für ein paar Testfiguren zur Auswahl haben.


## Programmtricks

### Verschachtelte Schleife(n)

Schachteln und Schleifen erinnern ja eigentlich an Geburtstagsgeschenke. Und ein bisschen wie ein Geschenk ist auch diese Konstruktion...

Um alle Register auszuwerten, müssen wir sie nacheinander in das Testregister 0 befördern. Dazu dient die folgende Schleifenanordnung:

- Innere Schleife: Nach jedem Durchlauf sind alle vier Bits des Registers 0 ausgewertet, Register 2 wird über **Ringtausch** in das Register 0 geschoben und die anderen Register entsprechend.
- LR-Schleife: Nach jedem vierten Durchlauf tauschen die **linken Register** (0, 2, 4, 6) mit den **rechten Registern** (1, 3, 5, 7). 
- OU-Schleife: Nach jedem achten Durchlauf tauschen die **oberen Register** 8-F mit den **unteren Registern** 0-7.
- Äußere Schleife: Nach jedem 16. Durchlauf werden die Register auf der **LED-Matrix dargestellt**.

Ohne groß nachzudenken, würde man dafür in einer Hochsprache einfach drei FOR-Schleifen mit drei unterschiedlichen Index-Variablen ineinander legen (= verschachteln). Register sind beim Microtronic aber kostbar und in diesem Fall schon fast alle belegt. Ich musste mit nur **einem Index-Register** _SCHLEIFE_ für **alle drei Schleifen gemeinsam** auskommen.

Das geht aber verhältnismäßig einfach und elegant, wenn man das Bitmuster des Schleifenregisters auswertet. Denn bei jedem vierten Durchlauf (und nur dann) sind die Bits 1 und 2 des Schleifenregisters gesetzt, bei jedem achten Durchlauf die Bits 1, 2 und 3 und bei jedem 16. Durchlauf alle 4 Bits. Mit ANDI blenden wir die anderen Bits aus und können dann entscheiden, ob die Schleife in die nächsthöhere Ebene verlassen werden soll. 

Exemplarisch für die innere Schleife:

```
           MOV SCHLEIFE,TEMP   	
           ANDI #3,TEMP             Nur Bits 1 und 2 berücksichtigen
           CMPI #3,TEMP             Fertig mit innerer Schleife? 
           BRZ RLRotate             ja, dann Schleife verlassen (bei 3, 7, B und F)
           GOTO Loop
RLRotate   ...	
```

### Bitweise Rotation

Bitweise Rotation funktioniert besser mit SHL (Verdoppeln), weil das höchstwertige Bit (MSB) herausgeschoben wird und direkt als Carry wieder addiert werden kann:
```
Rotate     SHL ERGEBNIS
           ADC ERGEBNIS
```
Mit SHR (Halbieren) würde das niedrigstwertige Bit (LSB) herausfliegen, dieses müsste als Wert 8 wieder zum Register addiert werden. Man müsste also schreiben
```
Rotate     SHR ERGEBNIS
           BRC Add8
           GOTO Ende
Add8       ADDI #8,ERGEBNIS
Ende       ...
```
Daher beginnt die Bit-Auswertung in der inneren Schleife mit dem Most Significant Bit (MSB), also mit Bit 4. Das kehrt zwar die intuitive Reihenfolge um, spart aber zwei Befehle.


### Abkürzung

Den ADC-Befehl können wir auch zur Auswertung der links bei Bit 1 angrenzenden Register vorteilhaft nutzen.

Normalerweise wird das Register, dessen Bits wir zählen wollen, in KOPIE geschoben. Dort werden die Bits, die in die Summe einfließen sollen, mit SHL, SHR und ADC gezählt (Unterprogramme _CountLx_ und _CountRx_).

Bei Bit 1 geht das aber deutlich einfacher. Wir vergleichen nur, ob das angrenzende Register (der linke Nachbar von Bit 1) größer als 7 ist. Denn dann ist das vierte Bit gesetzt, also lebt der Nachbar und wird direkt mit ADC zur Summe addiert. 

```
           CMPI #7,UNTEN-R       Bit 4 in linkem Nachbarregister gesetzt?
           ADC ANZAHL            Dann +1 in Anzahl Nachbarn
```

Bei Bit 4 geht das nicht, denn mit CMPI können wir nicht einfach die Existenz von Bit 1 im angrenzenden rechten Register prüfen. Daher nutzen wir hier die bekannte Zähl-Routine _CountR1_.


## Der Nutzen von Sätzen

… ist nicht zu unterschätzen.

Zunächst gab es nur ein Unterprogramm _Count_ für die Zählung aller vier Bits im Register KOPIE. Alle nicht zu zählenden Bits wurden in der inneren Schleife zuvor über ANDI gelöscht. Das funktionierte.

![Count Count](/pics/count_count.jpg)
*Foto: [Barry Stock](https://www.flickr.com/photos/74388673@N00) / [CC BY-SA 2.0](https://creativecommons.org/licenses/by-sa/2.0/)*

Als ich dann dokumentieren wollte, wie lange eine Generation braucht, um berechnet zu werden, musste ich messen. Und genau dabei kam dann die Überlegung auf: Wie kriege ich das ein bisschen schneller hin? Die Zähl-Routine _Count_ iterierte ja immer über alle vier Bits, also oft über Bits, die ich gerade vorher mit ANDI maskiert hatte. Unnötig eigentlich. 

Also stellte ich zwei Zähl-Routinen mit mehreren Einsprung-Adressen zur Verfügung - eine zählt x-mal nach links, eine zählt x-mal nach rechts: _CountLx_ und _CountRx_. Dadurch wurden dann immer nur noch so viele Bits wie nötig gezählt. Das funktionierte gut (minus 2 Sekunden).

Und beim Kommentieren stellte ich dann irgendwann fest, dass die ANDI-Operationen zur Maskierung nicht zu zählender Bits immer noch in der inneren Schleife waren. Das war zwar sehr ordentlich, aber inzwischen gar nicht mehr nötig. Weg damit. Das funktionierte noch besser (minus 2 Sekunden).

Hätte ich keine Notwendigkeit gehabt, alle Schritte nachvollziehbar zu kommentieren, wären diese überflüssigen Operationen sicher in der Schleife verblieben - und die Berechnung einer neuen Generation hätte jeweils 4 Sekunden länger gedauert.