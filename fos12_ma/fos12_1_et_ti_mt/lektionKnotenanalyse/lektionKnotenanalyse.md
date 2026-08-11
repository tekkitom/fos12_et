# Überblick
Die Knotenanalyse dient der Berechnung von Spannungen in komplexeren oder komplexen Schaltungen. Sie kann mit Widerständen, Spulen und Kondensatoren verwendet werden. Nicht lineare Bauelemente wie z. B. Transistoren können als Ersatzschaltungen dargestellt werden.
In der Erklärung verwende ich als Beispiel eine Schaltung mit Widerständen.

Es ist nicht notwendig eine Anordnung in Dreiecksschaltung oder Sternschaltung in die andere Form umzuwandeln, um die Schaltung in Reihenschaltungen oder Parallelschaltungen auflösen zu können.

## Vorteile
- wenige Knoten ergibt wenige Gleichungen zur späteren Berechnung.
- Berechnung der Spannungen in einem Zweig (zwischen dem Knoten und dem Bezugsknoten)
- Gut einsetzbar, wenn Stromquellen in der Schaltung vorhanden sind.
- Es wird keine Baumstruktur festgelegt, keine Maschen notwendig.
- Das Verfahren wird von Simulationsprogrammen (Spice) häufig verwendet.

## Nachteile
- Viele Knoten ergeben viele Gleichungen, aufwändigere Berechnung
- Wenn nur Spannungsquellen in der Schaltung sind, müssen diese umgewandelt werden.
- nicht geeignet, wenn nichtlineare Bauteile in der Schaltung verbaut sind (hier ggf. Iterationsverfahren verwenden, Simulation mit Spice - KICAD o. Ä. Oberfläche)


# Bezugsknoten festlegen
Bei der Knotenanalyse wird im ersten Schritt ein Bezugsknoten festgelegt. Der Bezugsknoten kann in der Regel frei gewählt werden. Es ist hilfreich einen Knoten zu wählen, an dem möglichst viele Zweige angeschlossen sind. Alternativ kann man den Knoten wählen, an dem das Masse-Potential anliegt. Später werden Spannungen oder Potenzialdifferenzen (Potenziale) berechnet. Alle Potenziale beziehen sich auf den Bezugsknoten.

# Weitere Knoten
Wenn du den Bezugsknoten festgelegt hast, legst du alle andern Knoten fest. Wenn zwischen zwei Knoten kein Bauteil (hier ein Widerstand oder eine Quelle) ist, gehören beide Knoten als ein Knoten zusammen. Du kannst an beide Stellen dieselbe Markierung (z. B. K2) schreiben. Alternativ kannst du mit einer Ellipse markieren, dass die beiden (Teil-)Knoten zusammen gehören.

# Matrix - Hauptdiagonale und Vektoren (Quellen und Potenziale)
Im nächsten Schritt lege ich das Gleichungssystem an. Jeder Knoten bekommt eine Gleichung, eine Zeile. Ich verwende hier die Schreibweise als Matrix.

Wichtig ist, dass man Leitwerte statt Widerständen verwendet. Wenn in der Schaltung Widerstände eingetragen sind, muss ich sie zuerst in Leitwerte umrechnen. Alternativ kann ich auch 1/R mit dem entsprechenden Wert für R schreiben. Leitwerte haben die Einheit Siemens statt Ohm.

Auf der Hauptdiagonalen stehen die Leitwerte, die an dem betreffenden Knoten direkt angeschlossen sind. Am Knoten 1 sind die Leitwerte G2 und G3 angeschlossen, am Knoten 2 G3, G4 und G6. Der Knoten oberhalb der Quelle I02 ist ein Teil des Knotens K2. Am Knoten K3 (unterhalb der Quelle I02) sind G6 und G9 angeschlossen. 

Am Knoten "0" sind G2, G4 und G9 angeschlossen. Für den Bezugsknoten (Knoten "0") wird jedoch keine Gleichung aufgestellt.

Jetzt kann ich die Hauptdiagonale der Matrix aufstellen: \( \left( \begin{array}{rrrr} \text{G}2+\text{G}3 &  & \\ & \text{G}3+\text{G}4+\text{G}6 &  \\  &  & \text{G}6+\text{G}9\\ \end{array}\right) * \left(\begin{array}{c} \text{U}_{K1}\\ \text{U}_{K2}\\ \text{U}_{K3} \end{array} \right) = \left( \begin{array}{c} Quelle An Knoten 1 \\ Quelle an Knoten 2\\ Quelle an Knoten 3 \\ \end{array}\right) \)

Im letzten Vektor (rechts vom Gleichheitszeichen) stehen die Stromquellen, die direkt an den Knoten angeschlossen sind. Wenn der Strom in den Knoten hinein fließt, trage ich die Quelle positiv ein, sonst negativ.

# Info Matrix - Nebendiagonalen - Kopplung

Nach der Hauptdiagonalen der Matrix benötige ich noch die Leitwerte, die die Knoten verbinden. Wenn ein Leitwert zwei Knoten direkt miteinander verbindet, wird er an der Kreuzung eingetragen. Dieser Leitwert bekommt immer ein negatives Vorzeichen. Damit sollte die Matrix vollständig sein. Wenn du noch symbolische Werte (G1, G2, ...) in der Matrix hast, solltest du jetzt die symbolischen Werte durch die Zahlenwerte ersetzen. Anschließend kannst du die Potenziale ausrechnen. 

Zur Berechnung der Knotenpotentiale kannst du die Werte der Matrix in den Taschenrechner eingeben oder das Gleichungssystem mit octave lösen lassen. Wie du die Daten in octave eingibst, erkläre ich auf der nächsten Seite.

# optional: Eingabe in octave
octave ist eine kostenlose Software, mit der man mathematische Aufgaben lösen lassen kann. Ich verwende sie in diesem Beispiel, um das Gleichungssystem der Knotenpotentialanalyse zu berechnen. Genauer, um die Potenziale der Knoten zum Bezugsknoten zu berechnen. 

Die Matrix nenne ich A. In der Definition von octave muss ich schreiben: A = \[wert11, wert12, wert13; wert21, wert22, wert23; wert31, wert32, wert33\]; Die erste Zahl ist die Zeile, die zweite die Spalte. Zwischen den Zeilen wird ein ; eingegeben, zwischen Werten der Zeile ein , das Dezimaltrennzeichen ist ein . (3.14 statt 3,14). Die Eingabe schließe ich mit Enter (Return-Taste) ab. Wenn kein Prompt kommt, prüfe, ob du das ; am Ende der Zeile eingegeben hast. Falls nicht, kannst du es hier nachholen.

Beim Vektor für die Quellen ist es ähnlich. octave behandelt den Vektor als Matrix mit einer Spalte und mehreren Zeilen.

b =\[quelle1; quelle2; quelle3 \]; Auch diese Eingabe wird mit Enter abgeschlossen.

Zur Berechnung gibst du x=A/b; ein. Als Antwort wird der Vektor mit den Werten der Potenziale ausgegeben. octave verwendet die mathematische Schreibweise 23e-4 bedeutet, dass es eine Spannung von 23 mV ist. Oder 0.0023 V oder 2.3*10^-4 V.
