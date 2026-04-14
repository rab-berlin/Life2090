# Life2090
Conway´s Game of Life auf dem Busch Microtronic 2090

### [Game of Life](https://de.wikipedia.org/wiki/Conways_Spiel_des_Lebens)

...

Ein paar Hinweise...

Das Programm ist ziemlich rechenintensiv. Die Operationen selbst sind nicht komplex, innerhalb der Schleife werden eigentlich nur ein paar Bits hin- und hergeschoben, ein paar Register getauscht, ein bisschen addiert und verglichen. Aber die Menge macht´s... 

Um die neue Generation eines **einzelnen Registers** zu berechnen, müssen etwa **220 Instruktionen** abgearbeitet werden. Da der Microtronic mehr als doppelt so schnell rechnet, wenn das Display ausgeschaltet bleibt, ist DISOUT praktisch ein Muss. Dann braucht ein Befehl immer noch 9 ms, also sind 220 Befehle in 2 Sekunden durchlaufen. 

Alle 16 Register werden somit innerhalb von 32 Sekunden ausgewertet. Nach vier, acht, zwölf und 16 Registern kommen noch (relativ aufwändige) Verschiebeoperationen dazu, so dass am Ende etwa **40 Sekunden** für Berechnung und Anzeige einer kompletten **Folgegeneration** herausspringen. Ein ziemlich gemütlicher Bildschirmschoner.




Bitweise Rotation funktioniert besser mit SHL, weil das herausgeschobene Bit direkt als Carry wieder addiert werden kann:
```
Rotate     SHL ERGREG
           ADC ERGREG
```
Mit SHR würde das erste Bit herausfliegen, dieses müsste als Wert 8 wieder zum Register addiert werden. 

Daher beginnt die innere Schleife mit dem Most Significant Bit (MSB), also mit Bit 4. Das kehrt zwar die intuitive Reihenfolge um, spart aber zwei Befehle.


