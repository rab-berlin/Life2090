# Life2090

Conway´s [Game of Life](https://de.wikipedia.org/wiki/Conways_Spiel_des_Lebens) auf dem Busch Microtronic 2090

## Hinweis

Gleich vorweg: Du solltest irgendetwas haben, das die Ereignisse an den Ausgängen des Microtronic 2090 sinnvoll interpretiert und darstellt. Und außerdem den _Takt_ vorgibt, denn die Übermittlung der einzelnen Bit-Sequenzen ist zeitkritisch. Diese zusätzliche Peripherie ist das [ESP2090-Studio](https://github.com/rab-berlin/ESP2090) - was du dazu wissen musst, findest du auf der entprechenden Github-Seite.

Wenn du sowas nicht hast, dann empfehle ich, das erst einmal zusammenzubauen. Wenn du aber sagst: "Es reicht mir, algorithmische Schönheit in Gedanken zu bestaunen", dann auch gut. Aber zusammenlöten, anfassen und austesten macht mehr Spaß. Isso. Schreib mir, vielleicht hab ich noch ne Platine und Teile bei mir rumliegen.

Zum Nachlesen hier das [Programm Life2090](https://github.com/rab-berlin/Life2090/blob/main/program/Life.pdf)

Und hier die [MIC-Datei](https://github.com/rab-berlin/Life2090/blob/main/program/life.mic), die du mit geeigneten Mitteln auf deinen Microtronic bringen musst.

## Bedienung

Game of Life ist ja eigentlich kein Spiel. Du bestimmst die Anfangskonfiguration und danach schaust du mehr oder weniger interessiert zu, was daraus entsteht. Insofern ist die Interaktion mit den Programm sehr trivial.

Im ESP2090-Studio muss der Button **LED Start** gedrückt sein, sonst siehst du nix.

Aus vorherigen Programmläufen können noch Werte in den Registern liegen, deshalb solltest du vor Programmstart einmal die grüne RESET-Taste drücken. Dann startest du das Programm mit HALT-NEXT-00-RUN und wählst aus, ob du die Werte schon eingegeben hast, ob du sie noch eingeben willst, oder ob du eine im Programm hinterlegte Figur zum Start laden willst.

- Taste 0 - die Werte wurden bereits über HALT-REG-x in die Register 0-E eingegeben (Register F ist immer 0)
- Taste 1 - Blinker
- Taste 2 - Uhr
- Taste 3 - Kröte
- Taste 4 - Bipole
- Taste 5 - Gleiter
- Taste 6 - Segler
- Taste 7 - Pentomino
- Taste F - die Werte werden anschließend erfasst, jedes Register von 0 bis F separat

## Hintergrund

Game of Life kennst du, oder? Nein? Dann bist du kein Nerd der 80er-Jahre. Oder gar kein Nerd. Aber was machst du dann auf dieser Seite? Geh weg... Nur Spaß. Bleib natürlich, wenn du magst. Aber lies erstmal in Ruhe, was es mit Life auf sich hat. Es ist ein sehr interessantes Stück Computer-Geschichte. 

In den 80er-Jahren lief dieses Programm praktisch auf jedem Heimcomputer in irgendeiner Variante mindestens einmal. Wir erfreuten uns daran, wie der Computer aus der bestehenden die neue Generation ausrechnete und darstellte. Bewunderten vielleicht manchmal die Geschwindigkeit, in der sich das emsige Treiben auf dem Bildschirm abspielte. Und mancher - wir wollen keine Namen nennen - versuchte womöglich, die philosopischen Aspekte dieser Simulation zu ergründen... Woher kommen wir? Wohin gehen wir? Hat alles ein Ende? ... Ok, genug. Ich hab´s wieder im Griff. :-)

Mein erster Computer, der Busch Microtronic 2090, konnte das nicht. Denn er hatte ja keinen Bildschirm... Bis jetzt!

## Entwicklung

Wie üblich - der erste Gedanke war: Müsste doch vielleicht irgendwie gehen... Der zweite Gedanke: Nee, kann nicht klappen, zu viele Programmschritte, zu wenig Register.

Zunächst einmal haben wir - bedingt durch die LED-Matrix - eine 8x8-Welt mit 64 Zellen, das passt ja eigentlich genau in eine Registerbank (Arbeits- oder Speicherregister). Die anderen Register könnte ich dann doch zur Berechnung der Folgegeneration verwenden. 

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

Andere Register bedingen aber anderen Code. Denn leider **können wir Register nicht indirekt adressieren** (also elegant Register-Pointer auf die jeweils relevanten Register mitlaufen lassen) - dafür gibt es im Befehlssatz schlicht keine Instruktionen. Das bedeutet, jedes Register bekommt seinen eigenen Code-Teil im Programm. Also 16 mal _irgendwie das gleiche_, aber mit jeweils wechselnden Registern machen. Das hätte wahrscheinlich eine knappe Million Instruktionen erfordert.

### Zweiter Ansatz

Auszuwertende Register aus der unteren Speicherhälfte werden nacheinander in ein Testregister in der oberen Hälfte geschoben. Die fünf relevanten Nachbarregister werden ebenfalls nach oben geschoben. Dann wird die (stets identische) Auswertungsroutine für das Testregister ausgeführt und das Ergebnis zurück an die Stelle des gerade ausgewerteten Registers geschrieben - diesmal aber in das Speicherregister als neue Generation.

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

Dadurch wurde zwar der Code für die Auswertung vereinheitlicht, aber die zahlreichen MOV-Operationen machten das Programm keineswegs kürzer, eher im Gegenteil. Ich hatte mal drauflos programmiert... und der Programmspeicher war bereits voll, als ich noch nicht einmal die Hälfte der Register codiert hatte. Dieser Ansatz passte also auch nicht in die verfügbaren 256 Programmschritte. Zudem wurde die Menge der verfügbaren Register knapp, im Prinzip standen nur noch die beiden Register 8 und 9 zur Verfügung. Bedenklich.







### Dritter Ansatz

Die Idee, die Auswertungsroutine zu standardisieren, fühlte sich intuitiv aber richtig an. Denn dann müsste ich lediglich das **immer gleiche Register** mit den **immer gleichen Nachbarregistern** vergleichen, was den Code deutlich verschlanken würde. Das Problem lag nur noch daran, dass für jedes Register zu viele Verschiebe-Operationen durchgeführt werden mussten, insbesondere die fünf Nachbarregister waren sehr "teuer". 

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

Bitweise Rotation funktioniert besser mit _SHL_ (Verdoppeln), weil das herausgeschobene Bit direkt als Carry wieder addiert werden kann:
```
Rotate     SHL ERGREG
           ADC ERGREG
```
Mit _SHR_ (Halbieren) würde das erste Bit herausfliegen, dieses müsste als Wert 8 wieder zum Register addiert werden. Man müsste also schreiben
```
Rotate     SHR ERGREG
           BRC Add8
           GOTO Ende
Add8       ADDI #8,ERGREG
Ende       ...
```
Daher beginnt die innere Schleife mit dem Most Significant Bit (MSB), also mit Bit 4. Das kehrt zwar die intuitive Reihenfolge um, spart aber zwei Befehle.

### Abkürzung

Den ADC-Befehl können wir auch zur Auswertung bei Bit 1 vorteilhaft nutzen.

Normalerweise schieben wir das Register, dessen Bits wir zählen wollen, in das Register TEST, löschen mit _ANDI_ die Bits, die nicht in die Summe einfließen sollen und zählen dann die Bits über _SHL_ und _ADC_.

Bei Bit 1 geht das aber deutlich einfacher. Wir vergleichen nur, ob das angrenzende Register (der linke Nachbar von Bit 1) größer als 7 ist. Denn dann ist das vierte Bit gesetzt, also existiert der Nachbar und wird direkt mit _ADC_ zur Summe addiert. 

```
           CMPI #7,UNTEN-R       Bit 4 gesetzt?
           ADC ANZAHL            Dann +1 in Anzahl Nachbarn
```


