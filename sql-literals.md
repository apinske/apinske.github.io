# SQL Performance - Literal Values vs. Parameters

Wer in Java-Anwendungen per JDBC Datenbankabfragen ausführt, benutzt fast immer "dynamisches SQL". Das bedeutet, 
dass die Datenbank den Statement-Text zur Ausführungszeit in einen Zugriffsplan übersetzen muss. Die folgenden 
beiden Statements erzeugen fachlich dasselbe Ergebnis, verhalten sich aber bzgl. Performance nicht zwangsläufig 
gleich.

Wenn im Statement ein Prädikatswert mit einem Literal (String, Zahl, Datum, ...) angegeben wird, erstellt die 
Datenbank (unter normalen Bedingungen) den Plan genau für diesen Text und damit für den explizit angegebenen Wert.

```sql
/* 1 */ select attribute1 from entity where attribute2 = 4711
```

Wenn dieses Statement mit unterschiedlichen Werten ausgeführt wird, muss jedes mal ein neuer Plan erstellt werden.
Dafür wird aber genau für diesen Wert optimiert.

Wird im Statement hingegen ein Platzhalter angegeben, muss für das Statement nur einmal ein Ausführungsplan erstellt
werden, der dann für alle Werte wiederverwendet werden kann. 

```sql
/* 2 */ select attribute1 from entity where attribute2 = ? -- bind ?1 to 4711
```

Man sollte also immer genau zwischen den Varianten abwägen. In einem Statement können Literale und Parameter beliebig
kombiniert werden. Ein Literal bietet sich an, wenn sich der Wert nicht oft unterscheidet (z.B. bei einem Status/Flag)
oder durch den vorab bekannten Wert die Selektivität aus den Statistiken genauer eingeschätzt werden kann.

Wenn im Beispiel der Wert 4711 eher selten vorkommt, kann eine Abfrage mit Literal Performanceverbessungen bringen, da
der Optimizer bereits bei der Planerstellung weiß, dass nur wenige Zeilen betroffen sind. Wenn die Spalte attribute2
hingegen viele Werte annehmen kann und diese eher gleichmäßig verteilt sind, kann durch einen Parameter vermieden werden,
dass zu viele Ausführungspläne (pro Wert einer) vorgehalten werden müssen.

Ein sehr vereinfachtes Beispiel zur Demonstration: 
Gäbe es 5% Zeilen mit Wert 4711 und 95% Zeilen mit Wert 0815, müsste der Plan mit Parameter annehmen, dass beide Werte 
gemeint sein könnten und z.B. einen Table-Scan vorziehen, da im Fall 0815 sowieso fast alle Zeilen gelesen werden müssten.
Würde hingegen das Literal 4711 verwendet, kann der Optimizer die Anzahl genauer vorhersagen und würde ggf. einen Indexzugriff
bevorzugen.

Die meisten Datenbanksysteme haben zusätzlich noch Optionen um die Erzeugung des Ausführungsplans weiter zu beeinflussen.
