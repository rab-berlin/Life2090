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

Den allerersten Ansatz habe ich nach kurzer Überlegung gar nicht weiter verfolgt: Jedes einzelne Register mit den jeweils angrenzenden Registern vergleichen und die Bits dann entsprechend zu setzen, hätte wahrscheinlich eine knappe Million Instruktionen erfordert. Denn wir können Register nicht indirekt adressieren, dafür gibt es im Befehlssatz keine Instruktionen. Das bedeutet, jedes Register müsste seinen eigenen Code-Teil im Programm bekommen. Um etwa Register 2 auszuwerten, müssten Vergleiche mit den Registern 0, 1, 2, 3, 4 und 5 durchgeführt werden. 

| Register | Register |
|:---:|:---:|
| E<br>.... | F<br>.... |
| C<br>.... | D<br>.... |
| A<br>.... | B<br>.... |
| 8<br>.... | 9<br>.... |
| 6<br>.... | 7<br>.... |
| 4<br>.... | 5<br>.... |
| 2<br>.... | 3<br>.... |
| 0<br>.... | 1<br>.... |


Für Register 6 aber Vergleiche mit den Registern 4, 5, 6, 7, 8 und 9. Ganz andere Register, ganz anderer Code.





## Geschwindigkeit

Das Programm ist ziemlich rechenintensiv. Die Operationen selbst sind nicht komplex, innerhalb der Schleife werden eigentlich nur ein paar Bits hin- und hergeschoben, ein paar Register getauscht, ein bisschen addiert und verglichen. Aber die Menge macht´s... 

Um die neue Generation eines **einzelnen Registers** zu berechnen, müssen etwa **220 Instruktionen** abgearbeitet werden. Da der Microtronic mehr als doppelt so schnell rechnet, wenn das Display ausgeschaltet bleibt, ist DISOUT praktisch ein Muss. Dann braucht ein Befehl immer noch 9 ms, also sind 220 Befehle in 2 Sekunden durchlaufen. 

Alle 16 Register werden somit - theoretisch - innerhalb von 32 Sekunden ausgewertet. Nach jeweils vier Registern kommen allerdings noch (relativ aufwändige) Register-Tauschereien dazu, so dass am Ende etwa **40 Sekunden** für Berechnung und Anzeige einer kompletten **Folgegeneration** herausspringen. Ein ziemlich gemütlicher Bildschirmschoner.

Testweise habe ich eine Version geschrieben, die auf den Tausch von Rechts und Links verzichtet und stattdessen Register 0 und Register 1 gemeinsam in der inneren Schleife auswertet. Das spart eine Schleifenebene, macht das Programm aber deutlich länger, da nun acht statt vier Bits auszuwerten sind. Gemessen habe ich dann 36 Sekunden für die Berechnung einer Generation, also 10% Geschwindigkeitssteigerung. Nicht berauschend. Ich wollte lieber mehr Platz für ein paar Testfiguren zur Auswahl haben.


## Programmtricks

### Bitweise Rotation

Bitweise Rotation funktioniert besser mit SHL (Verdoppeln), weil das herausgeschobene Bit direkt als Carry wieder addiert werden kann:
```
Rotate     SHL ERGREG
           ADC ERGREG
```
Mit SHR (Halbieren) würde das erste Bit herausfliegen, dieses müsste als Wert 8 wieder zum Register addiert werden. Man müsste also schreiben
```
Rotate     SHR ERGREG
           BRC Add8
           GOTO Ende
Add8       ADDI #8,ERGREG
Ende       ...
```
Daher beginnt die innere Schleife mit dem Most Significant Bit (MSB), also mit Bit 4. Das kehrt zwar die intuitive Reihenfolge um, spart aber zwei Befehle.


