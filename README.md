# Life2090

Conway's [Game of Life](https://de.wikipedia.org/wiki/Conways_Spiel_des_Lebens) auf dem Busch Microtronic 2090

![Life](/pics/life.jpg)

## Hinweis

Gleich vorweg: Du solltest irgendetwas haben, das die Ereignisse an den Ausgängen des Microtronic 2090 sinnvoll interpretiert und darstellt. Und außerdem den _Takt_ vorgibt, denn die Übermittlung der einzelnen Bit-Sequenzen ist zeitkritisch. Diese Aufgaben übernimmt das [ESP2090-Studio](https://github.com/rab-berlin/ESP2090) - was du dazu wissen musst, findest du auf der entprechenden Github-Seite.

Wenn du das ESP2090-Studio noch nicht hast, dann empfehle ich, das erst einmal zusammenzubauen - es sei denn, du sagst: "Es reicht mir, algorithmische Schönheit in Gedanken zu bestaunen", dann auch gut. Aber zusammenlöten, anfassen und austesten macht mehr Spaß. Isso. Schreib mir, vielleicht hab ich noch ne Platine und Teile bei mir rumliegen.

Zum Nachlesen hier das - wie immer liebevoll dokumentierte - [Programm Life2090](https://github.com/rab-berlin/Life2090/blob/main/program/Life.pdf). Und hier die [MIC-Datei](https://github.com/rab-berlin/Life2090/blob/main/program/life.mic), die du mit geeigneten Mitteln auf deinen Microtronic bringen musst.

## Hintergrund

_Life_ kennst du, oder? Nein? Dann bist du kein Nerd der 80er-Jahre. Oder gar kein Nerd. Aber was machst du dann auf dieser Seite? Geh weg... Nur Spaß. Bleib natürlich, wenn du magst. Aber lies erstmal in Ruhe, [was es mit _Life_ auf sich hat](https://de.wikipedia.org/wiki/Conways_Spiel_des_Lebens). Es ist ein sehr interessantes Stück Computer-Geschichte. 

In den 80er-Jahren lief dieses Programm auf jedem Heimcomputer mindestens einmal in irgendeiner Variante. Wir erfreuten uns daran, wie der Computer immer neue Generationen ausrechnete und darstellte. Bewunderten vielleicht manchmal die Geschwindigkeit, in der sich das emsige Treiben auf dem Bildschirm abspielte. Und mancher - wir wollen keine Namen nennen - versuchte womöglich, die philosopischen Aspekte dieser Simulation zu ergründen... Woher kommen wir? Wohin gehen wir? Hat alles ein Ende? ... Ok, genug... Ich hab´s wieder im Griff :-)

Mein erster Computer, der Busch Microtronic 2090, konnte das leider nicht. Denn er hatte ja keinen Bildschirm... Bis jetzt!

## Bedienung

_Life_ ist ja eigentlich kein echtes Spiel. Du bestimmst die Anfangskonfiguration und schaust danach mehr oder weniger interessiert zu, welche immer neuen Figuren und Muster daraus entstehen. Insofern ist die Interaktion mit den Programm sehr trivial.

### Anordnung der Zellen

Die Zellen in der LED-Matrix sind wie der positive Quadrant eines Koordinatensystems angeordnet. Spalten gehen von links nach rechts (x-Achse), Reihen gehen von unten nach oben (y-Achse). Die Register 0-F folgen dieser Anordnung, das niedrigstwertige Bit eines Registers (LSB) ist ganz links, das höchstwertige (MSB) ganz rechts.

Als Beispiele ein paar Zellen im Life-Spielfeld:

- 🟡 Zelle (4, 4) befindet sich in Register 6 - und ist darin das höchste Bit (also der Wert 8).
- 🟣 Zelle (1, 7) befindet sich in Register C - und ist darin das niedrigste Bit (also der Wert 1).
- 🟢 Zelle (6, 5) befindet sich in Register 9 - und ist darin das zweit-niedrigste Bit (also der Wert 2).

| Register - Bits | Bits - Register |
|:---:|:---:|
| E&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; F |
| C&nbsp;&nbsp;&nbsp;&nbsp; :purple_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; D |
| A&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; B |
| 8&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;:green_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; 9 |
| 6&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:yellow_circle: | :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; 7 |
| 4&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; 5 |
| 2&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; 3 |
| 0&nbsp;&nbsp;&nbsp;&nbsp; :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle: | :white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;:white_circle:&nbsp;&nbsp;&nbsp;&nbsp; 1 |

### Start

Aus vorherigen Programmläufen können noch Werte in den Registern liegen, deshalb solltest du vor Programmstart einmal die **grüne RESET-Taste** drücken. Auf der **Weboberfläche des ESP2090-Studios** muss der Button **LED Start** gedrückt sein, sonst siehst du nix. Dann startest du das Programm mit **HALT-NEXT-00-RUN** und wählst aus, ob du die Werte schon eingegeben hast, ob du sie noch eingeben willst, oder ob du eine im Programm hinterlegte Figur zum Start laden willst.

- Taste 0 - die Werte wurden bereits über HALT-REG-x in die Register 0-E eingegeben (Register F ist immer 0)
- Taste 1 - Uhr
- Taste 2 - Kröte
- Taste 3 - Bipole
- Taste 4 - Gleiter
- Taste 5 - Segler
- Taste 6 - Pentomino
- Taste F - die Werte werden anschließend erfasst, jedes Register von 0 bis F separat

## Entwicklung

Wie üblich - der erste Gedanke war: Müsste doch vielleicht irgendwie gehen... Der zweite Gedanke: Nee, kann nicht klappen, zu viele Programmschritte, zu wenig Register.

Zunächst einmal haben wir - bedingt durch die LED-Matrix - eine 8x8-Welt mit 64 Zellen, das passt ja eigentlich genau in eine Registerbank (Arbeits- oder Speicherregister). Die anderen Register könnte man dann doch zur Berechnung der Folgegeneration verwenden...

### Erster Ansatz

Den allerersten Ansatz habe ich nach kurzer Überlegung gar nicht weiter verfolgt: Jedes einzelne Register mit den jeweils fünf angrenzenden Registern vergleichen und die Bits dann entsprechend setzen. Um etwa Register 2 auszuwerten, müssten Vergleiche mit den Registern 0, 1, 3, 4 und 5 durchgeführt werden:

| Register<br>Bits | Register<br>Bits |
|:---:|:---:|
| E<br>:white_circle: :white_circle: :white_circle: :white_circle: | F<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| C<br>:white_circle: :white_circle: :white_circle: :white_circle: | D<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| A<br>:white_circle: :white_circle: :white_circle: :white_circle: | B<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 8<br>:white_circle: :white_circle: :white_circle: :white_circle: | 9<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 6<br>:white_circle: :white_circle: :white_circle: :white_circle: | 7<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 4<br>:red_circle: :red_circle: :red_circle: :red_circle: | 5<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 2<br>:green_circle: :green_circle: :green_circle: :green_circle: | 3<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 0<br>:red_circle: :red_circle: :red_circle: :red_circle: | 1<br>:red_circle: :red_circle: :red_circle: :red_circle: |

Für Register 6 aber Vergleiche mit den Registern 4, 5, 7, 8 und 9:

| Register<br>Bits | Register<br>Bits |
|:---:|:---:|
| E<br>:white_circle: :white_circle: :white_circle: :white_circle: | F<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| C<br>:white_circle: :white_circle: :white_circle: :white_circle: | D<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| A<br>:white_circle: :white_circle: :white_circle: :white_circle: | B<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 8<br>:red_circle: :red_circle: :red_circle: :red_circle: | 9<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 6<br>:green_circle: :green_circle: :green_circle: :green_circle: | 7<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 4<br>:red_circle: :red_circle: :red_circle: :red_circle: | 5<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 2<br>:white_circle: :white_circle: :white_circle: :white_circle: | 3<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 0<br>:white_circle: :white_circle: :white_circle: :white_circle: | 1<br>:white_circle: :white_circle: :white_circle: :white_circle: |

Andere Register bedingen aber anderen Code. Denn leider **können wir Register nicht indirekt adressieren** (also elegant Register-Pointer auf die jeweils relevanten Register mitlaufen lassen) - dafür gibt es im Befehlssatz des Microtronic schlicht keine Instruktionen. Das bedeutet, jedes Register bekäme seinen eigenen Code-Teil im Programm. Also 16 mal _irgendwie fast das gleiche_, aber mit jeweils wechselnden Registern. Das hätte wahrscheinlich eine knappe Million Instruktionen erfordert.

### Zweiter Ansatz

Die nächste Idee: Auszuwertende Register aus der unteren Speicherhälfte werden nacheinander in ein Testregister in der oberen Hälfte geschoben. Die fünf relevanten Nachbarregister werden ebenfalls nach oben geschoben. Dann wird die (stets identische) Auswertungsroutine für das Testregister ausgeführt und das Ergebnis zurück an die Stelle des gerade ausgewerteten Registers geschrieben - diesmal aber in das Speicherregister als neue Generation.

Wenn z.B. das Register 4 ausgewertet werden soll:

| Register<br>Bits | Register<br>Bits |
|:---:|:---:|
| TEST (r4)<br>:green_circle: :green_circle: :green_circle: :green_circle: | NEBEN (r5)<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| UNTEN (r2)<br>:red_circle: :red_circle: :red_circle: :red_circle: | OBEN (r6)<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| NEBEN-U (r3)<br>:red_circle: :red_circle: :red_circle: :red_circle: | NEBEN-O (r7)<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 8<br>:white_circle: :white_circle: :white_circle: :white_circle: | 9<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 6<br>:white_circle: :white_circle: :white_circle: :white_circle: | 7<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 4<br>:green_circle: :green_circle: :green_circle: :green_circle: | 5<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 2<br>:white_circle: :white_circle: :white_circle: :white_circle: | 3<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 0<br>:white_circle: :white_circle: :white_circle: :white_circle: | 1<br>:white_circle: :white_circle: :white_circle: :white_circle: |

Zwar gab es in diesem Ansatz nur noch einen einzigen, immer gleichen Code-Teil für die Auswertung, aber die zahlreichen MOV-Operationen machten das Programm insgesamt trotzdem nicht kürzer, eher im Gegenteil. Ich hatte mal drauflos programmiert... und der Programmspeicher war bereits voll, als ich noch nicht einmal die Hälfte der Register codiert hatte. Diese Idee passte also auch nicht in die verfügbaren 256 Programmschritte. Zudem wurde die Menge der verfügbaren Register knapp, im Prinzip standen nur noch die beiden Register 8 und 9 zur Verfügung. Bedenklich.







### Dritter Ansatz

Die Idee, nur einen einzigen Code-Teil für die Auswertung zu haben, fühlte sich intuitiv aber richtig an. Denn dann müsste ich lediglich das **immer gleiche Register** mit den **immer gleichen Nachbarregistern** vergleichen, was den Code deutlich verschlanken würde. Das Problem lag nur noch darin, dass für jedes Register zu viele und - schlimmer noch - jeweils andere Verschiebe-Operationen durchgeführt werden mussten, insbesondere das Verschieben der fünf Nachbarregister machte den Ansatz sehr _teuer_. 

Wie wäre es denn, wenn wir nur ein einziges, immer gleiches Register in der unteren Hälfte des Arbeitsspeichers betrachten und durch einen _clever durchdachten Verschiebebahnhof_ dafür sorgen, dass alle fünfzehn anderen Register nacheinander in diesem speziellen Register landen, um dort mit der immer gleichen Routine ausgewertet zu werden? Die Frage ist natürlich rhetorisch... es zeigte sich, dass das die Lösung war. 

Dieses spezielle Register sollte fortan Register 0 sein:

| Register<br>Bits | Register<br>Bits |
|:---:|:---:|
| UNTEN-L<br>:red_circle: :red_circle: :red_circle: :red_circle: | UNTEN-R<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| C<br>:white_circle: :white_circle: :white_circle: :white_circle: | D<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| A<br>:white_circle: :white_circle: :white_circle: :white_circle: | B<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| OBEN-L<br>:white_circle: :white_circle: :white_circle: :white_circle: | OBEN-R<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 6<br>:white_circle: :white_circle: :white_circle: :white_circle: | 7<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 4<br>:white_circle: :white_circle: :white_circle: :white_circle: | 5<br>:white_circle: :white_circle: :white_circle: :white_circle: |
| 2<br>:red_circle: :red_circle: :red_circle: :red_circle: | 3<br>:red_circle: :red_circle: :red_circle: :red_circle: |
| 0<br>:green_circle: :green_circle: :green_circle: :green_circle: | 1<br>:red_circle: :red_circle: :red_circle: :red_circle: |


## Geschwindigkeit

Das Programm ist ziemlich rechenintensiv. Die Operationen selbst sind nicht komplex, innerhalb der Schleife werden eigentlich nur ein paar Bits hin- und hergeschoben, ein paar Register getauscht, ein bisschen addiert und verglichen. Aber die Menge macht´s... 

Um die neue Generation eines **einzelnen Registers** zu berechnen, müssen etwa **177 Instruktionen** abgearbeitet werden. Da der Microtronic mehr als doppelt so schnell rechnet, wenn das Display ausgeschaltet bleibt, ist DISOUT praktisch ein Muss. Dann braucht ein Befehl immer noch 9 ms, also sind 177 Befehle in 1,6 Sekunden durchlaufen. 

Alle 16 Register werden somit - theoretisch - innerhalb von 25,5 Sekunden ausgewertet. Nach jeweils vier Registern kommen allerdings noch (relativ aufwändige) Register-Tauschereien dazu, so dass am Ende etwa **32 Sekunden** für Berechnung und Anzeige einer kompletten **Folgegeneration** herausspringen. Ein ziemlich gemütlicher Bildschirmschoner.

Testweise habe ich eine Version geschrieben, die auf den Tausch von Rechts und Links verzichtet und stattdessen Register 0 und Register 1 gemeinsam in der inneren Schleife auswertet. Das spart eine Schleifenebene, macht das Programm aber deutlich länger, da nun acht statt vier Bits auszuwerten sind. Gemessen habe ich dann 28 Sekunden für die Berechnung einer Generation, also 12,5% Geschwindigkeitssteigerung. Ordentlich, aber zieht jetzt auch nicht die Wurst vom Teller. Ich wollte lieber mehr Platz für ein paar Testfiguren zur Auswahl haben.


## Programmtricks

### Verschachtelte Schleife(n)

Schachteln und Schleifen erinnern ja eigentlich an Geburtstagsgeschenke. Und ein bisschen wie ein Geschenk ist auch diese Konstruktion...

Um alle Register auszuwerten, müssen wir sie nacheinander in das Testregister 0 befördern. Dazu dient die folgende Schleifenanordnung:

- Innere Schleife: Nach jedem Durchlauf sind alle vier Bits des Registers 0 ausgewertet, Register 2 wird über **Ringtausch** in das Register 0 geschoben und die anderen Register entsprechend.
- LR-Schleife: Nach jedem vierten Durchlauf tauschen die **linken Register** (0, 2, 4, 6) mit den **rechten Registern** (1, 3, 5, 7). 
- OU-Schleife: Nach jedem achten Durchlauf tauschen die **oberen Register** 8-F mit den **unteren Registern** 0-7.
- Äußere Schleife: Nach jedem 16. Durchlauf werden die Register auf der **LED-Matrix dargestellt**.

Ohne groß nachzudenken, würde man dafür in einer Hochsprache einfach drei FOR-Schleifen mit drei unterschiedlichen Index-Variablen ineinander legen (= verschachteln). Register sind beim Microtronic aber kostbar und in diesem Fall schon fast alle belegt. Ich musste mit nur einem Index-Register _SCHLEIFE_ auskommen.

Das geht aber verhältnismäßig einfach und elegant, wenn man das Bitmuster der Schleifenregisters auswertet. Denn bei jedem vierten Durchlauf (und nur dann) sind die Bits 1 und 2 des Schleifenregisters gesetzt, bei jedem achten Durchlauf die Bits 1, 2 und 3 und bei jedem 16. Durchlauf alle 4 Bits. Mit _ANDI_ blenden wir die anderen Bits aus und können dann entscheiden, ob die Schleife in die nächsthöhere Ebene verlassen werden soll. 

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

Bitweise Rotation funktioniert besser mit _SHL_ (Verdoppeln), weil das höchstwertige Bit (MSB) herausgeschoben wird und direkt als Carry wieder addiert werden kann:
```
Rotate     SHL ERGREG
           ADC ERGREG
```
Mit _SHR_ (Halbieren) würde das niedrigstwertige Bit (LSB) herausfliegen, dieses müsste als Wert 8 wieder zum Register addiert werden. Man müsste also schreiben
```
Rotate     SHR ERGREG
           BRC Add8
           GOTO Ende
Add8       ADDI #8,ERGREG
Ende       ...
```
Daher beginnt die innere Schleife mit dem Most Significant Bit (MSB), also mit Bit 4. Das kehrt zwar die intuitive Reihenfolge um, spart aber zwei Befehle.

### Abkürzung

Den ADC-Befehl können wir auch zur Auswertung der links bei Bit 1 und rechts bei Bit 4 angrenzenden Register vorteilhaft nutzen.

Normalerweise wird das Register, dessen Bits wir zählen wollen, in TEST geschoben. Dann werden dort die Bits, die nicht in die Summe einfließen sollen, mit ANDI gelöscht und zum Schluss mit SHL, SHR und ADC gezählt (Unterprogramme _CountX_).

Bei Bit 1 geht das aber deutlich einfacher. Wir vergleichen nur, ob das angrenzende Register (der linke Nachbar von Bit 1) größer als 7 ist. Denn dann ist das vierte Bit gesetzt, also lebt der Nachbar und wird direkt mit ADC zur Summe addiert. 

```
           CMPI #7,UNTEN-R       Bit 4 in linkem Nachbarrregister gesetzt?
           ADC ANZAHL            Dann +1 in Anzahl Nachbarn
```

Bei Bit 4 brauchen wir einen Befehl mehr, denn mit CMPI können wir nicht einfach die Existenz von Bit 1 im angrenzenden rechten Register prüfen. Daher kopieren wir dieses Nachbarregister in TEST, schieben einmal bitweise nach rechts mit SHR und addieren das Carry, wenn es vorhanden ist, mit ADC.

```
           MOV UNTEN-R,TEST      Kopie in Register TEST erstellen
           SHR TEST              Bit 1 in rechtem Nachbarrregister herausschieben, Carry?
           ADC ANZAHL            Dann +1 in Anzahl Nachbarn
```


