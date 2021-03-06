<!--

author:   Sebastian Zug & André Dietrich & Fabian Bär
email:    sebastian.zug@informatik.tu-freiberg.de & andre.dietrich@informatik.tu-freiberg.de & fabian.baer@student.tu-freiberg.de
version:  0.0.5
language: de
narrator: Deutsch Female

import:  https://raw.githubusercontent.com/liascript-templates/plantUML/master/README.md
         https://raw.githubusercontent.com/LiaTemplates/DigiSim/master/README.md

-->

# 06 - Schaltwerke

**TU Bergakademie Freiberg - Wintersemester 2020 / 21**

Link auf die aktuelle Vorlesung im Versionsmanagementsystem GitHub

[https://github.com/TUBAF-IfI-LiaScript/VL_EingebetteteSysteme/blob/00_Einfuehrung](https://github.com/TUBAF-IfI-LiaScript/VL_EingebetteteSysteme/blob/dev/00_Einfuehrung)

Die interaktive Form ist unter diese Link zu finden ->
[TODO]( )

---------------------------------------------------------------------

** Fragen an die Veranstaltung**

+


<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii

                Abstraktionsebenen

           +----------------------------+ -.
  Ebene 6  | Problemorientierte Sprache |  |
           +----------------------------+  |
                                           ⎬ Anwendungssoftware
           +----------------------------+  |
  Ebene 5  | Assemblersprache           |  |
           +----------------------------+ -.

           +----------------------------+
  Ebene 4  | Betriebssystem             |     Systemsoftware
           +----------------------------+

           +----------------------------+
  Ebene 3  | Istruktionsset             |     Maschinensprache
           +----------------------------+

           +----------------------------+  -.
  Ebene 2  | Mikroarchitektur           |   |
           +----------------------------+   |
                                            ⎬ Automaten, Speicher, Logik
           +----------------------------+   |       ╔═══════════════╗
  Ebene 1  | Digitale Logik             |   |    ◀══║ HIER SIND WIR!║
           +----------------------------+  -.       ╚═══════════════╝

           +----------------------------+
  Ebene 0  | E-Technik, Physik          |     Analoge Phänomene
           +----------------------------+                                      .
```


---------------------------------------------------------------------

## Motivation

**In der kombinatorischen Logik:**

+ werden Gatter als verzögerungsfrei angenommen (Idealisierung, die oft zu Problemen führt !)
+ sind keine Rückkopplungen gestattet
+ werden Schaltungen als Schaltnetze bezeichnet
+ können Schaltungen als gerichteter azyklischer Graph dargestellt werden

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii

Eingaben       +------------------+
  ------------>| Schaltnetz       |            Ausgaben
  ------------>|                  |------------>
  ------------>| kombinatorische  |------------>
  ------------>| Logik            |
               +------------------+                                            .
```


**In der sequentiellen Logik:**

+ sind Rückkopplungen gestattet
+ werden Schaltungen werden Schaltwerke bezeichnet
+ können Schaltungen als gerichteter zyklischer Graph dargestellt werden

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii

Eingaben       +------------------+           Ausgaben
  ------------>| Schaltnetz       |------------>
  ------------>|                  |------------>
  ------------>| kombinatorische  |------+
          +--->| Logik            |----+ |
          |    +------------------+    | |
          |                            | |
          |    +------------------+    | |
          +----| Zustandsspeicher |<---+ |
               |                  |<-----+
               +------------------+                                        .
```

> Und wo brauche ich sequentielle Logik? Die Abarbeitung von Befehlen im Rechner basiert darauf. In diesem Fall wird über die Eingabesteuerleitungen ein Befehl codiert und der Zustandspeicher beinhaltet den erreichten Realisierungsschritt. **Damit wird jeder Rechner zu einem Schaltwerk!**


### Wiederholung: Beschreibung von Flip-Flops

__Wahrheitstafel__ zeigt die Eingaben, die notwendig sind, um eine bestimmte Zustandsänderung herbeizuführen.

| $R(t)$ | $S(t)$ | $Q(t+1)$      |
| ------ | ------ | ------------- |
| 0      | 0      | $Q$           |
| 0      | 1      | 1             |
| 1      | 0      | 0             |
| 1      | 1      | nicht erlaubt |

__Invertierte Wahrheitstafel__ zeigt die Eingaben, die notwendig sind, um eine bestimmte Zustandsänderung herbeizuführen.

| $Q(t)$ | $Q(t+1)$ | $S$ | $R$ |
| ------ | -------- | --- | --- |
| 0      | 0        | 0   | d   |
| 0      | 1        | 1   | 0   |
| 1      | 0        | 0   | 1   |
| 1      | 1        | d   | 0   |

> Merke: Die invertierte Wahrheitstafel beantwortet die Frage, wie die Eingänge beschaltet werden müssen, um einen bestimmten Zustand zu realsieren.

Stellen Sie die invertierten Wahrheitstafeln für den JK und den D Flip-Flop auf!

### Grundkonzept

Wie kann man systematisch ein synchrones Schaltwerk ausgehend von
der Problembeschreibung entwerfen ?

Automat ist gekennzeichnet durch:

+ beliebige (jedoch endliche) Menge von Zuständen
+ Zustandsübergänge in jedem Takt abhängig von Eingangssignalen
+ Ausgangssignale werden durch ein Schaltnetz generiert

Mathematisch kann ein Deterministischer Endlicher Automat als Tupel $A = (Q, \Sigma, \delta, q_0, F)$

+ Q ist eine endliche Zustandsmenge.
+ $\Sigma$ ist das endliche Eingabealphabet, also die Menge erlaubter Eingabesymbole.
+ $\delta : Q \times \Sigma \rightarrow Q $ ist die Übergangsfunktion (oder Transitionsfunktion). Sie ordnet jedem Paar bestehend aus einem Zustand $q\in Q$ und einem Eingabesymbol $a\in \Sigma$ einen Nachfolgezustand $p\in Q$ zu.
+ $q\in Q$ ist der Startzustand (auch Anfangszustand oder Initalzustand).
+ $F\subseteq Q$ ist die Menge der akzeptierenden Zustände, die sogenannten Finalzustände (oder Endzustände).

Beispiele:

__Zustand einer Tür__

Darstellung als Graph

```text @plantUML
@startuml
digraph finite_state_machine {
    rankdir=LR;

    node [shape = point ]; qi
    node[shape=circle]
    A[label="{auf}"];
    B[label="{zu}"];

    qi -> A;
    A  -> A  [ label = "schließen" ];
    A  -> B  [ label = "öffnen" ];
    B  -> B  [ label = "öffnen" ];
    B  -> A  [ label = "schließen" ];
}
@enduml
```
@plantUML


Darstellung in einer Übergangstabelle

|                     | "Öffnen"            | "Schließen"               |
| ------------------- | ------------------- | ------------------------- |
| Zustand offen       | offen (unverändert) | geschlossen               |
| Zustand geschlossen | offen               | geschlossen (unverändert) |

__Codeschloss für die Erfassung der Sequenzfolge `7022`__


```text @plantUML
@startuml
digraph finite_state_machine {
    node [shape = point ]; qi
    node[shape=Mrecord]
    A[label="{A|geschlossen}"];
    B[label="{B|geschlossen}"];
    C[label="{C|geschlossen}"];
    D[label="{D|geschlossen}"];
    E[label="{E|geöffnet}"];

    qi -> A;
    A  -> A  [ label = "E != 7" ];
    A  -> B  [ label = "E == 7" ];
    B  -> A  [ label = "E != 0" ];
    B  -> C  [ label = "E == 0" ];
    C  -> D  [ label = "E == 2" ];
    C  -> A  [ label = "E != 2" ];
    D  -> E  [ label = "E == 2" ];
    D  -> A  [ label = "E != 2" ];
    E  -> A  [ label = "" ];
}
@enduml
```
@plantUML

Welche Beschränkungen sehen Sie in diesem Entwurf? Was passiert bei der Sequenzfolge `707022`?


**Wie realisieren wir nun aber das theoretische Modell des endlichen Automaten mit realen Bauteilen?**

### Beispiel

Binärsequenzdetektor der drei aufeinander folgende `1` erkennt. Dabei gehen wir davon aus, dass die Übernahme der Werte mit den steigenden Flanken des Taktsignales erfolgt.

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
      |   +---+   +---+   +---+   +---+   +---+   +---+   +---+   +---+   +---+
Takt  |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |   |
      |---+   +---+   +---+   +---+   +---+   +---+   +---+   +---+   +---+   +
      |
      | +------+        +------+        +-------------------------------+
E     | | 1    | 0      | 1    |  0     | 1       1       1       1     | 0
      |-+      +--------+      +--------+                               +-------
      |
      |                                                 +---------------+
Y     |                                                 | 1       1     | 0
      |-------------------------------------------------+               +-------
      +-------------------------------------------------------------------------->
```


```text @plantUML
@startuml
digraph finite_state_machine {
    rankdir=LR;

    node [shape = point ]; qi
    node[shape=circle]
    A[label="A \n Y=0"];
    B[label="B \n Y=0"];
    C[label="C \n Y=0"];
    D[label="D \n Y=1"];

    qi -> A;
    A  -> A  [ label = "E=0" ];
    A  -> B  [ label = "E=1" ];
    B  -> A  [ label = "E=0" ];
    B  -> C  [ label = "E=1" ];
    C  -> A  [ label = "E=0" ];
    C  -> D  [ label = "E=1", fontcolor=blue ];
    D  -> D  [ label = "E=1", fontcolor=blue ];
    D  -> A  [ label = "E=0" ];
}
@enduml
```
@plantUML

- Zustand `A`: die letzte Eingabe war keine `1` (`0` oder Start)  
- Zustand `B`: die letzte Eingabe war `1`, die davor war nicht `1`  
- Zustand `C`: die letzten beiden Eingaben waren `1`, die davor war nicht `1`  
- Zustand `D`: mindestens die letzten drei Eingaben waren `1`  


Zustandsübergänge

| aktueller Zustand | Eingabe E | neuer Zustand |
| ----------------- | --------- | ------------- |
| A                 | 0         | A             |
| B                 | 0         | A             |
| C                 | 0         | A             |
| D                 | 0         | A             |
| A                 | 1         | B             |
| B                 | 1         | C             |
| C                 | 1         | D             |
| D                 | 1         | D             |


Kodierung

Für unsere digitalen Bauteile müssen wir diese Zustände aber mit `1` und `0` kodieren.

| Zustände | Flip-Flops | Mögliche Zustände | Ungenutzte Zustände |
| -------- | ---------- | ----------------- | ------------------- |
| 1        | 0          | 1                 | 0                   |
| 2        | 1          | 2                 | 0                   |
| 3        | 2          | 4                 | 1                   |
| 4        | 2          | 4                 | 0                   |
| 5        | 3          | 8                 | 3                   |
| ...      |            |                   |                     |
| 8        | 3          | 8                 | 0                   |
| 9        | 4          | 16                | 7                   |
| ...      |            |                   |                     |

Da wir insgesamt 4 Zustände haben braucht es $\lceil log_2(4)\rceil = 2$ Speicherelemente, also Flip-Flops.
Die zwei Flip-Flops werden im folgenden als `F` und `G` bezeichnet. Die Ausgabe bezeichnen wir mit `X`.

Mit unserem Schaltwerk wollen wir also eine Funktion abbilden, die die Ausgabe `X` in Abhängigkeit von (historischen) `E` Eingaben generiert. Um letztgenannten Anteil zu integrieren, braucht es die Zustände die in `F` und `G` erfasst sind.

| Zustand | Flip-Flop F | Flip-Flop G |
| ------- | ----------- | ----------- |
| A       | 0           | 0           |
| B       | 0           | 1           |
| C       | 1           | 0           |
| D       | 1           | 1           |

Damit ergibt sich dann eine neue Zustandstabelle

| Zustand | F   | G   | E   | Zustand' | F'  | G'  |
| ------- | --- | --- | --- | -------- | --- | --- |
| A       | 0   | 0   | 0   | A        | 0   | 0   |
| B       | 0   | 1   | 0   | A        | 0   | 0   |
| C       | 1   | 0   | 0   | A        | 0   | 0   |
| D       | 1   | 1   | 0   | A        | 0   | 0   |
| A       | 0   | 0   | 1   | B        | 0   | 1   |
| B       | 0   | 1   | 1   | C        | 1   | 0   |
| C       | 1   | 0   | 1   | D        | 1   | 1   |
| D       | 1   | 1   | 1   | D        | 1   | 1   |

Realisierung der Schaltfunktion

![Bild](./images/06_Schaltwerke/Schaltwerk_Detektor.png)

Um die entsprechende Schaltfunktionen für die Änderung der Zustände und die Ausgabe aufzustellen, brauchen wir die invertierte Wahrheitstafel des intendierten Flip-Flops.

> __Merke:__  Unterschiedliche Flip-Flops für die Speicherung der Zustände führen zu  unterschiedlichen Beschaltungen!

<table><tr>
<td>
Zustandstabelle

| F   | G   | E   | F'                                    | G'                                    |
| --- | --- | --- | ------------------------------------- | ------------------------------------- |
| 0   | 0   | 0   | 0                                     | 0                                     |
| 0   | 1   | 0   | 0                                     | 0                                     |
| 1   | 0   | 0   | 0                                     | 0                                     |
| 1   | 1   | 0   | 0                                     | 0                                     |
| 0   | 0   | 1   | 0                                     | <span style="color: #ff0000">1</span> |
| 0   | 1   | 1   | <span style="color: #ff0000">1</span> | 0                                     |
| 1   | 0   | 1   | <span style="color: #ff0000">1</span> | <span style="color: #ff0000">1</span> |
| 1   | 1   | 1   | <span style="color: #ff0000">1</span> | <span style="color: #ff0000">1</span> |

</td>
<td>
Ausgaben

| F   | G   | A                                     |
| --- | --- | ------------------------------------- |
| 0   | 0   | 0                                     |
| 0   | 1   | 0                                     |
| 1   | 0   | 0                                     |
| 1   | 1   | <span style="color: #ff0000">1</span> |

</td>
</tr>
<tr>
<td>
$$
\begin{aligned}
F` &= \overline{F}GE + F\overline{G}E + FGE  \\
   &= GE + FE \\
G` &= \overline{F}\overline{G}E + F\overline{G}E + FGE  \\
   &= \overline{G}E + FE
\end{aligned}
$$
</td>
<td>
$$
\begin{aligned}
A = FG
\end{aligned}
$$
</td>
</tr>
</table>

Simulation

Bitte mit Eingabe 0 starten, um Startzustand korrekt zu setzen!

```json @DigiSim.evalJson
{"devices":{"e":{"label":"E","type":"Button","propagation":0,"position":{"x":-160,"y":10}},"clk":{"label":"Clk","type":"Button","propagation":0,"position":{"x":-160,"y":-50}},"not":{"label":"~G","type":"Not","propagation":0,"bits":1,"position":{"x":-135,"y":110}},"and1":{"label":"FE","type":"And","propagation":0,"bits":1,"position":{"x":30,"y":190}},"and2":{"label":"GE","type":"And","propagation":0,"bits":1,"position":{"x":10,"y":20}},"and3":{"label":"(~G)E","type":"And","propagation":0,"bits":1,"position":{"x":20,"y":100}},"and4":{"label":"FG","type":"And","propagation":0,"bits":1,"position":{"x":245,"y":175}},"or1":{"label":"GE + FE","type":"Or","propagation":0,"bits":1,"position":{"x":165,"y":15}},"or2":{"label":"(~G)E + FE","type":"Or","propagation":0,"bits":1,"position":{"x":175,"y":90}},"fff":{"label":"F FlipFlop","type":"Dff","propagation":0,"polarity":{"clock":true},"bits":1,"initial":"x","position":{"x":335,"y":30}},"ffg":{"label":"G FlipFlop","type":"Dff","propagation":0,"polarity":{"clock":true},"bits":1,"initial":"x","position":{"x":340,"y":95}},"a":{"label":"A","type":"Lamp","propagation":0,"position":{"x":395,"y":180}}},"connectors":[{"from":{"id":"or1","port":"out"},"to":{"id":"fff","port":"in"}},{"from":{"id":"or2","port":"out"},"to":{"id":"ffg","port":"in"}},{"from":{"id":"not","port":"out"},"to":{"id":"and3","port":"in2"}},{"from":{"id":"e","port":"out"},"to":{"id":"and2","port":"in1"}},{"from":{"id":"e","port":"out"},"to":{"id":"and3","port":"in1"}},{"from":{"id":"and2","port":"out"},"to":{"id":"or1","port":"in1"}},{"from":{"id":"and3","port":"out"},"to":{"id":"or2","port":"in1"}},{"from":{"id":"and1","port":"out"},"to":{"id":"or2","port":"in2"}},{"from":{"id":"and1","port":"out"},"to":{"id":"or1","port":"in2"}},{"from":{"id":"e","port":"out"},"to":{"id":"and1","port":"in1"}},{"from":{"id":"ffg","port":"out"},"to":{"id":"not","port":"in"},"vertices":[{"x":410.95,"y":240},{"x":325,"y":240}]},{"from":{"id":"ffg","port":"out"},"to":{"id":"and2","port":"in2"},"vertices":[{"x":440,"y":210},{"x":405,"y":240},{"x":-90,"y":195},{"x":-145,"y":140},{"x":-120,"y":85}]},{"from":{"id":"fff","port":"out"},"to":{"id":"and1","port":"in2"},"vertices":[{"x":460,"y":255}]},{"from":{"id":"clk","port":"out"},"to":{"id":"fff","port":"clk"},"vertices":[{"x":270,"y":-35}]},{"from":{"id":"clk","port":"out"},"to":{"id":"ffg","port":"clk"},"vertices":[{"x":275,"y":-35}]},{"from":{"id":"and4","port":"out"},"to":{"id":"a","port":"in"}},{"from":{"id":"fff","port":"out"},"to":{"id":"and4","port":"in1"},"vertices":[{"x":460,"y":165}]},{"from":{"id":"ffg","port":"out"},"to":{"id":"and4","port":"in2"},"vertices":[{"x":390,"y":240},{"x":225,"y":240}]}],"subcircuits":{}}
```

## Allgemeines Vorgehen

1. Schritt: Spezifikation des Zustandsdiagramms
2. Schritt: Transformation in eine Zustandstabelle
3. Schritt: Zuordnung von Zuständen zu Flip-Flop Belegungen
4. Schritt: Erstellung der Wahrheitstafel für Zustände und Ausgaben
5. Schritt: Ableitung einer KNF oder DNF
6. Schritt: Minimierung

## Automaten Typen

**Moore-Automat**

Edward Forrest Moore (1925 – 2003, Bell Labs)

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
  +------------------------------------------+
  |   +--------------+     +--------------+  |   +--------------+
  +-->|              |     |              |  |   |              |   Y
      | Eingabelogik |---->| Speicher     |--+-->| Ausgabelogik |---->
----->| (Schaltnetz) |     | (Flip-Flops) |      | (Schaltnetz) |
 E    +--------------+     +--------------+      +--------------+
                                                                               .
```

```text @plantUML
@startuml
digraph finite_state_machine {
    rankdir=LR;

    node [shape = point ]; qi
    node[shape=circle]
    A[label="A, 0"];
    B[label="B, 0"];
    C[label="C, 0"];
    D[label="D, 1"];

    qi -> A;
    A  -> A  [ label = "E=0" ];
    A  -> B  [ label = "E=1" ];
    B  -> A  [ label = "E=0" ];
    B  -> C  [ label = "E=1" ];
    C  -> A  [ label = "E=0" ];
    C  -> D  [ label = "E=1"];
    D  -> D  [ label = "E=1"];
    D  -> A  [ label = "E=0" ];
}
@enduml
```
@plantUML

**Die Ausgabelogik bestimmt Ausgabe Y hängt nur vom aktuellen Zustand ab.**

**Mealy-Automat**

George H. Mealy (1927 – 2010, IBM)

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
  +------------------------------------------+
  |   +--------------+     +--------------+  |
  +-->|              |     |              |  |   +--------------+
      | Eingabelogik |---->| Speicher     |--+-->|              |    Y
---+->| (Schaltnetz) |     | (Flip-Flops) |      | Ausgabelogik |---->
 E |  +--------------+     +--------------+      | (Schaltnetz) |
   +-------------------------------------------->|              |
                                                 +--------------+

                                                                               .
```

```text @plantUML
@startuml
digraph finite_state_machine {
    rankdir=LR;

    node [shape = point ]; qi
    node [shape = point ]; qi1
    node[shape=circle]
    A[label="A"];
    B[label="B"];
    C[label="C"];
    D[label="D"];

    qi -> A;
    A  -> A  [ label = "E=0 \n A=0" ];
    A  -> B  [ label = "E=1 \n A=0" ];
    B  -> A  [ label = "E=0 \n A=0" ];
    B  -> C  [ label = "E=1 \n A=0" ];
    C  -> A  [ label = "E=0 \n A=0", fontcolor=green];
    C  -> D  [ label = "E=1 \n A=1", fontcolor=blue ];
    D  -> D  [ label = "E=1 \n A=1", fontcolor=blue ];
    D  -> A  [ label = "E=0 \n A=0", fontcolor=green];
}

@enduml
```
@plantUML

**Die Ausgabe Y hängt jedoch die Ausgabelogik vom aktuellen Zustand und vom Eingabesignal E ab**

Welche Vereinfachungsmöglichkeiten sehen Sie?

```text @plantUML
@startuml
digraph finite_state_machine {
    rankdir=LR;

    node [shape = point ]; qi1
    node[shape=circle]
    A1[label="A"];
    B1[label="B"];
    C1[label="C"];

    qi1 -> A1;
    A1  -> A1  [ label = "E=0 \n A=0" ];
    A1  -> B1  [ label = "E=1 \n A=0" ];
    B1  -> A1  [ label = "E=0 \n A=0" ];
    B1  -> C1  [ label = "E=1 \n A=0" ];
    C1  -> A1  [ label = "E=0 \n A=0", fontcolor=green ];
    C1  -> C1  [ label = "E=1 \n A=1", fontcolor=blue ];
}

@enduml
```
@plantUML

- Zustand `A`: die letzte Eingabe war keine `1`  
- Zustand `B`: die letzte Eingabe war `1`, die davor war nicht `1`  
- Zustabd `C`: mindestens die letzten zwei eingaben waren `1`  

Welche Bedeutung hat dies für den technischen Entwurf des Schaltwerkes?

Der Mealy-Automat ist die generellere Form. Der Moore-Automat unterbindet den Einfluss des Einganges. Eine weitere Spezialisierung ist der sogenannte Medwedew-Automat, bei dem ganz auf die Ausgabelogik verzichtet wird.

|          | Mealy-Automat                                            | Moore-Automat                                                                                           |
| -------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Vorteile | schnellere Reaktion auf Veränderung der Eingabesignale E | taktsynchrone Ausgabe A, asynchron auftretende Störungen der Eingabesignale wirken sich nicht auf A aus |
|          | Realisierung ist mit einer kleineren Anzahl an Zuständen möglich, wenn mehrere Zustandsübergänge zu einem Zustand verschiedene Ausgaben erfordern                                                          |  geringerer Schaltungsaufwand für Ausgabelogik, wenn Ausgabe A eigentlich nur vom Zustand abhängt                                                                                                       |

Noch mal zurück zum Beispiel des Binärsequenzdetektors. Welche Konsequenzen hätte die Umsetzung als Mealy-Automat?


```text @plantUML
@startuml
digraph finite_state_machine {
    rankdir=LR;

    node [shape = point ]; qi1
    node[shape=circle]
    A1[label="A"];
    B1[label="B"];
    C1[label="C"];

    qi1 -> A1;
    A1  -> A1  [ label = "E=0 \n A=0" ];
    A1  -> B1  [ label = "E=1 \n A=0" ];
    B1  -> A1  [ label = "E=0 \n A=0" ];
    B1  -> C1  [ label = "E=1 \n A=0" ];
    C1  -> A1  [ label = "E=0 \n A=0", fontcolor=green ];
    C1  -> C1  [ label = "E=1 \n A=1", fontcolor=blue ];
}

@enduml
```
@plantUML


<table>
<tr>
<td>
Zustandstabelle

| Zustand | F   | G   | E   | Zustand'  | F'  | G'  |
| ------- | --- | --- | --- | --------- | --- | --- |
| A       | 0   | 0   | 0   | A         | 0   | 0   |
| B       | 0   | 1   | 0   | A         | 0   | 0   |
| C       | 1   | 0   | 0   | A         | 0   | 0   |
| D       | 1   | 1   | 0   | dont care | d   | d   |
| A       | 0   | 0   | 1   | B         | 0   | 1   |
| B       | 0   | 1   | 1   | C         | 1   | 0   |
| C       | 1   | 0   | 1   | C         | 1   | 0   |
| D       | 1   | 1   | 1   | dont care | d   | d   |
</td>
<td>
Ausgaben

| F   | G   | E   | A   |
| --- | --- | --- | --- |
| 0   | 0   | 0   | 0   |
| 0   | 1   | 0   | 0   |
| 1   | 0   | 0   | 0   |
| 1   | 1   | 0   | d   |
| 0   | 0   | 1   | 0   |
| 0   | 1   | 1   | 0   |
| 1   | 0   | 1   | 1   |
| 1   | 1   | 1   | d   |
</td>
</tr>
<tr>
<td>
$$
\begin{aligned}
F` &= \overline{F}GE + F\overline{G}E \\
G` &= \overline{F}\overline{G}E \\
\end{aligned}
$$

</td>
<td>

$$
\begin{aligned}
A = F\overline{G}E
\end{aligned}
$$

</td>
</tr>
</table>

Wir nutzen die `dont care` Zustände noch weiter aus und ermöglichen eine zuätzliche Vereinfachung.

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
         __   _          _                        __   _          _
 F'      FG   FG   FG   FG                A       FG   FG   FG   FG
     _  +----+----+----+----+                 _  +----+----+----+----+
     E  |    |    | d  |    |                 E  |    |    | d  |    |
        +----+----+----+----+                    +----+----+----+----+
     E  |    | 1  | d  | 1  |                 E  |    |    | d  | 1  |
        +----+----+----+----+                    +----+----+----+----+
         __   _          _
 G'      FG   FG   FG   FG
     _  +----+----+----+----+
     E  |    |    | d  |    |
        +----+----+----+----+
     E  | 1  |    | d  |    |
        +----+----+----+----+                                                  .
```

$$
\begin{aligned}
F` &= \overline{F}GE + F\overline{G}E \\
   &= GE + FE \\
G` &= \overline{F}\overline{G}E  \\
A  &= F\overline{G}E + FGE = FE
\end{aligned}
$$

Damit ergibt sich eine alternative Realisierung unseres Schaltwerkes.

Bitte mit Eingabe 0 starten, um Flipflops zurück zu setzen!

```json @DigiSim.evalJson
{"devices":{"e":{"label":"E","type":"Button","propagation":0,"position":{"x":-160,"y":10}},"clk":{"label":"Clk","type":"Button","propagation":0,"position":{"x":-160,"y":-50}},"not1":{"label":"~G","type":"Not","propagation":0,"bits":1,"position":{"x":-135,"y":110}},"not2":{"label":"~F","type":"Not","propagation":0,"bits":1,"position":{"x":110,"y":145}},"and1":{"label":"FE","type":"And","propagation":0,"bits":1,"position":{"x":30,"y":190}},"and2":{"label":"GE","type":"And","propagation":0,"bits":1,"position":{"x":10,"y":20}},"and3":{"label":"(~G)E","type":"And","propagation":0,"bits":1,"position":{"x":20,"y":100}},"or1":{"label":"GE + FE","type":"Or","propagation":0,"bits":1,"position":{"x":165,"y":15}},"and4":{"label":"(~F)(~G)E","type":"And","propagation":0,"bits":1,"position":{"x":175,"y":90}},"fff":{"label":"F FlipFlop","type":"Dff","propagation":0,"polarity":{"clock":true},"bits":1,"initial":"x","position":{"x":335,"y":30}},"ffg":{"label":"G FlipFlop","type":"Dff","propagation":0,"polarity":{"clock":true},"bits":1,"initial":"x","position":{"x":345,"y":90}},"a":{"label":"A","type":"Lamp","propagation":0,"position":{"x":340,"y":180}}},"connectors":[{"from":{"id":"or1","port":"out"},"to":{"id":"fff","port":"in"}},{"from":{"id":"not1","port":"out"},"to":{"id":"and3","port":"in2"}},{"from":{"id":"e","port":"out"},"to":{"id":"and2","port":"in1"}},{"from":{"id":"e","port":"out"},"to":{"id":"and3","port":"in1"}},{"from":{"id":"and2","port":"out"},"to":{"id":"or1","port":"in1"}},{"from":{"id":"and1","port":"out"},"to":{"id":"or1","port":"in2"}},{"from":{"id":"e","port":"out"},"to":{"id":"and1","port":"in1"}},{"from":{"id":"ffg","port":"out"},"to":{"id":"not1","port":"in"},"vertices":[{"x":430,"y":215},{"x":325,"y":240}]},{"from":{"id":"ffg","port":"out"},"to":{"id":"and2","port":"in2"},"vertices":[{"x":440,"y":210},{"x":405,"y":240},{"x":-90,"y":195},{"x":-145,"y":140},{"x":-120,"y":85}]},{"from":{"id":"and1","port":"out"},"to":{"id":"a","port":"in"}},{"from":{"id":"fff","port":"out"},"to":{"id":"and1","port":"in2"},"vertices":[{"x":460,"y":255}]},{"from":{"id":"clk","port":"out"},"to":{"id":"fff","port":"clk"},"vertices":[{"x":270,"y":-35}]},{"from":{"id":"not2","port":"out"},"to":{"id":"and4","port":"in2"}},{"from":{"id":"and3","port":"out"},"to":{"id":"and4","port":"in1"}},{"from":{"id":"fff","port":"out"},"to":{"id":"not2","port":"in"},"vertices":[{"x":460,"y":255},{"x":90,"y":225}]},{"from":{"id":"and4","port":"out"},"to":{"id":"ffg","port":"in"}},{"from":{"id":"clk","port":"out"},"to":{"id":"ffg","port":"clk"},"vertices":[{"x":275,"y":-35}]}],"subcircuits":{}}
```

In der Simulation sehen Sie dass wir gegenüber dem Moore-Automaten ...


## Bedeutung des Flip-Flop Typs

Nehmen wir an, dass die Realisierung nicht mit einem D sondern einen JK-Flip-Flop erfolgen soll.

| $J$ | $K$ | $Q(t+1)$          |
| --- | --- | ----------------- |
| 0   | 0   | $Q(t)$            |
| 0   | 1   | 0                 |
| 1   | 0   | 1                 |
| 1   | 1   | $\overline{Q(t)}$ |

Der JK-Flip-Flop wechselt beim setzen von $J$ in einen 1 Zustand und kann mit K resetet werden. Eine gleichzeitige Aktivierung beider Eingäng führt zu einem Togglen des Zustandes.

| $Q(t)$ | $Q(t+1)$ | $J$ | $K$ |
| ------ | -------- | --- | --- |
| 0      | 0        | 0   | $d$ |
| 0      | 1        | 1   | $d$ |
| 1      | 0        | $d$ | 1   |
| 1      | 1        | $d$ | 0   |

Der Wechsel von $Q(t)=0$ nach $Q(t+1)=1$ kann entweder über ein Setzen ($J=1$) oder ein Togglen ($J=K=1$) umgesetzt werden. Daher spielt der Zustand des Einganges $K$ keine Rolle.

Zwar haben wir es nun mit jeweils zwei Eingängen für den Flip-Flop zu tuen (im Unterschied zum D-Flip-Flop). Dies äußert sich in einer zusätzlichen Spalte der Zustandsübergangstabelle. Die _don't care_ Konfigurationen ermöglichen aber eine höhere Flexibilität beim Entwurf.

Wie muss also die Beschaltung vorgenommen werden, um die bereits bekannte Zustandsübergangstabelle mit dem JK-Flip-Flop umzusetzen? Beginnen wir zunächst mit unserem ersten Flip-Flop F und seinen Eingängen JF und KF.

| F                                     | G   | E   | F'                                    | G'  | JF  | KF  | JG  | KG  |
| ------------------------------------- | --- | --- | ------------------------------------- | --- | --- | --- | --- | --- |
| <span style="color: #ff0000">0</span> | 0   | 0   | <span style="color: #00ff00">0</span> | 0   | 0   | d   |     |     |
| <span style="color: #ff0000">0</span> | 1   | 0   | <span style="color: #00ff00">0</span> | 0   | 0   | d   |     |     |
| <span style="color: #ff0000">1</span> | 0   | 0   | <span style="color: #00ff00">0</span> | 0   | d   | 1   |     |     |
| <span style="color: #ff0000">1</span> | 1   | 0   | <span style="color: #00ff00">0</span> | 0   | d   | 1   |     |     |
| <span style="color: #ff0000">0</span> | 0   | 1   | <span style="color: #00ff00">0</span> | 1   | 0   | d   |     |     |
| <span style="color: #ff0000">0</span> | 1   | 1   | <span style="color: #00ff00">1</span> | 0   | 1   | d   |     |     |
| <span style="color: #ff0000">1</span> | 0   | 1   | <span style="color: #00ff00">1</span> | 1   | d   | 0   |     |     |
| <span style="color: #ff0000">1</span> | 1   | 1   | <span style="color: #00ff00">1</span> | 1   | d   | 0   |     |     |

Analog wird die Zustandsübergangstabelle für JG und KG befüllt.

| F   | G                                     | E   | F'  | G'                                    | JF  | KF  | JG  | KG  |
| --- | ------------------------------------- | --- | --- | ------------------------------------- | --- | --- | --- | --- |
| 0   | <span style="color: #ff0000">0</span> | 0   | 0   | <span style="color: #00ff00">0</span> | 0   | d   | 0   | d   |
| 0   | <span style="color: #ff0000">1</span> | 0   | 0   | <span style="color: #00ff00">0</span> | 0   | d   | d   | 1   |
| 1   | <span style="color: #ff0000">0</span> | 0   | 0   | <span style="color: #00ff00">0</span> | d   | 1   | 0   | d   |
| 1   | <span style="color: #ff0000">1</span> | 0   | 0   | <span style="color: #00ff00">0</span> | d   | 1   | d   | 1   |
| 0   | <span style="color: #ff0000">0</span> | 1   | 0   | <span style="color: #00ff00">1</span> | 0   | d   | 1   | d   |
| 0   | <span style="color: #ff0000">1</span> | 1   | 0   | <span style="color: #00ff00">0</span> | 1   | d   | d   | 1   |
| 1   | <span style="color: #ff0000">0</span> | 1   | 0   | <span style="color: #00ff00">1</span> | d   | 0   | 1   | d   |
| 1   | <span style="color: #ff0000">1</span> | 1   | 0   | <span style="color: #00ff00">1</span> | d   | 0   | d   | 0   |

<!--
style="width: 80%; min-width: 420px; max-width: 720px;"
-->
```ascii
         __   _          _                        __   _          _
JF       FG   FG   FG   FG                JG      FG   FG   FG   FG
     _  +----+----+----+----+                 _  +----+----+----+----+
     E  |    |    | d  | d  |                 E  |    | d  | d  |    |
        +----+----+----+----+                    +----+----+----+----+
     E  |    | 1  | d  | d  |                 E  | 1  | d  | d  | 1  |
        +----+----+----+----+                    +----+----+----+----+

         __   _          _                        __   _          _
KF       FG   FG   FG   FG                KG      FG   FG   FG   FG
     _  +----+----+----+----+                 _  +----+----+----+----+
     E  | d  | d  | 1  | 1  |                 E  | d  | 1  | 1  | d  |
        +----+----+----+----+                    +----+----+----+----+
     E  | d  | d  |    |    |                 E  | d  | 1  |    |    |
        +----+----+----+----+                    +----+----+----+----+         .
```

Damit lassen sich folgende Funktionen ablesen:

$$
\begin{aligned}
JF &= GE \\
KF &= \overline{E} \\
JG &= E \\
KG &= \overline{E} + \overline{F}
\end{aligned}
$$

Und die Ausgabe? Die bleibt ja unabhängig von der konkreten Umsetzung mit Flip-Flops. Entsprechend gilt $A = EF$

Damit ergibt sich folgendes Simulationsbild:

Bitte am Anfang `start` auf AN, dann `clk` puls, dann `start` auf AUS, um JK Flipflops in den Startzustand zu versetzen.

```json @DigiSim.evalJson
{"devices":{"e":{"label":"E","type":"Button","propagation":0,"position":{"x":19.341666221618652,"y":0}},"start":{"label":"start","type":"Button","propagation":0,"position":{"x":205,"y":-85}},"clk":{"label":"clk","type":"Button","propagation":0,"position":{"x":19.341666221618652,"y":100}},"a":{"label":"A","type":"Lamp","propagation":1,"position":{"x":730,"y":105}},"not1":{"label":"not","type":"Not","propagation":1,"bits":1,"position":{"x":100,"y":55}},"not2":{"label":"not","type":"Not","propagation":1,"bits":1,"position":{"x":75,"y":225}},"and1":{"label":"GE","type":"And","propagation":1,"bits":1,"position":{"x":240,"y":40}},"and2":{"label":"~E + ~F","type":"Or","propagation":1,"bits":1,"position":{"x":240,"y":200}},"and3":{"label":"EF","type":"And","propagation":1,"bits":1,"position":{"x":575,"y":25}},"jkffF":{"label":"JKFF F","type":"Subcircuit","propagation":0,"celltype":"jkffms","position":{"x":400,"y":35}},"jkffG":{"label":"JKFF G","type":"Subcircuit","propagation":0,"celltype":"jkffms","position":{"x":400,"y":175}}},"connectors":[{"from":{"id":"start","port":"out"},"to":{"id":"jkffF","port":"start"}},{"from":{"id":"start","port":"out"},"to":{"id":"jkffG","port":"start"},"vertices":[{"x":330,"y":-20},{"x":330,"y":238}]},{"from":{"id":"and1","port":"out"},"to":{"id":"jkffF","port":"J"}},{"from":{"id":"and2","port":"out"},"to":{"id":"jkffG","port":"K"}},{"from":{"id":"not2","port":"out"},"to":{"id":"and2","port":"in2"}},{"from":{"id":"e","port":"out"},"to":{"id":"not1","port":"in"}},{"from":{"id":"not1","port":"out"},"to":{"id":"and2","port":"in1"}},{"from":{"id":"e","port":"out"},"to":{"id":"and1","port":"in1"}},{"from":{"id":"jkffF","port":"q"},"to":{"id":"not2","port":"in"},"vertices":[{"x":525,"y":130},{"x":550,"y":310}]},{"from":{"id":"jkffG","port":"q"},"to":{"id":"and1","port":"in2"},"vertices":[{"x":375,"y":140}]},{"from":{"id":"clk","port":"out"},"to":{"id":"jkffF","port":"clk"},"vertices":[{"x":295,"y":115}]},{"from":{"id":"clk","port":"out"},"to":{"id":"jkffG","port":"clk"}},{"from":{"id":"e","port":"out"},"to":{"id":"jkffG","port":"J"},"vertices":[{"x":74.34,"y":180}]},{"from":{"id":"and3","port":"out"},"to":{"id":"a","port":"in"}},{"from":{"id":"jkffF","port":"q"},"to":{"id":"and3","port":"in2"}},{"from":{"id":"e","port":"out"},"to":{"id":"and3","port":"in1"}},{"from":{"id":"not1","port":"out"},"to":{"id":"jkffF","port":"K"}}],"subcircuits":{"jkffms":{"devices":{"j":{"label":"J","type":"Input","propagation":0,"bits":1,"net":"J","position":{"x":15,"y":0}},"k":{"label":"K","type":"Input","propagation":0,"bits":1,"net":"K","position":{"x":20,"y":105}},"clk":{"label":"clk","type":"Input","propagation":0,"bits":1,"net":"clk","position":{"x":15,"y":50}},"start":{"label":"start","type":"Input","propagation":0,"bits":1,"net":"start","position":{"x":15,"y":150}},"q":{"label":"q","type":"Output","propagation":1,"bits":1,"net":"q","position":{"x":960,"y":45}},"qnot":{"label":"~q","type":"Output","propagation":1,"bits":1,"net":"qnot","position":{"x":960,"y":105}},"or1":{"label":"or","type":"Or","propagation":1,"bits":1,"position":{"x":190,"y":-40}},"or2":{"label":"or","type":"Or","propagation":1,"bits":1,"position":{"x":195,"y":160}},"and1":{"label":"and","type":"And","propagation":1,"bits":1,"position":{"x":225,"y":15}},"and2":{"label":"and","type":"And","propagation":1,"bits":1,"position":{"x":220,"y":95}},"nand1":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":370,"y":25}},"nand2":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":380,"y":90}},"nand3":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":805,"y":45}},"nand4":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":680,"y":30}},"nand5":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":675,"y":85}},"nand6":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":805,"y":95}},"nand7":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":525,"y":30}},"nand8":{"label":"nand","type":"Nand","propagation":1,"bits":1,"position":{"x":525,"y":85}},"not1":{"label":"not","type":"Not","propagation":1,"bits":1,"position":{"x":390,"y":170}}},"connectors":[{"from":{"id":"nand3","port":"out"},"to":{"id":"q","port":"in"}},{"from":{"id":"nand6","port":"out"},"to":{"id":"qnot","port":"in"}},{"from":{"id":"j","port":"out"},"to":{"id":"and1","port":"in2"}},{"from":{"id":"k","port":"out"},"to":{"id":"and2","port":"in1"}},{"from":{"id":"start","port":"out"},"to":{"id":"or2","port":"in2"}},{"from":{"id":"start","port":"out"},"to":{"id":"or1","port":"in1"}},{"from":{"id":"clk","port":"out"},"to":{"id":"nand1","port":"in2"}},{"from":{"id":"clk","port":"out"},"to":{"id":"nand2","port":"in1"}},{"from":{"id":"clk","port":"out"},"to":{"id":"not1","port":"in"}},{"from":{"id":"not1","port":"out"},"to":{"id":"nand5","port":"in1"},"vertices":[{"x":635,"y":190}]},{"from":{"id":"nand7","port":"out"},"to":{"id":"nand8","port":"in1"}},{"from":{"id":"nand8","port":"out"},"to":{"id":"nand7","port":"in2"}},{"from":{"id":"not1","port":"out"},"to":{"id":"nand4","port":"in2"},"vertices":[{"x":635,"y":190}]},{"from":{"id":"nand2","port":"out"},"to":{"id":"nand8","port":"in2"}},{"from":{"id":"nand1","port":"out"},"to":{"id":"nand7","port":"in1"}},{"from":{"id":"and1","port":"out"},"to":{"id":"nand1","port":"in1"}},{"from":{"id":"and2","port":"out"},"to":{"id":"nand2","port":"in2"}},{"from":{"id":"or2","port":"out"},"to":{"id":"and2","port":"in2"}},{"from":{"id":"or1","port":"out"},"to":{"id":"and1","port":"in1"},"vertices":[{"x":310,"y":5}]},{"from":{"id":"nand3","port":"out"},"to":{"id":"nand6","port":"in1"}},{"from":{"id":"nand6","port":"out"},"to":{"id":"nand3","port":"in2"},"vertices":[{"x":765,"y":145}]},{"from":{"id":"nand4","port":"out"},"to":{"id":"nand3","port":"in1"}},{"from":{"id":"nand5","port":"out"},"to":{"id":"nand6","port":"in2"}},{"from":{"id":"nand8","port":"out"},"to":{"id":"nand5","port":"in2"}},{"from":{"id":"nand7","port":"out"},"to":{"id":"nand4","port":"in1"}},{"from":{"id":"nand3","port":"out"},"to":{"id":"or2","port":"in1"},"vertices":[{"x":815,"y":295}]},{"from":{"id":"nand6","port":"out"},"to":{"id":"or1","port":"in2"},"vertices":[{"x":425,"y":-60}]}]}}}
```

Welche Unterschiede sehen Sie gegenüber der Realisierung mit D-Flip-Flops?

## Übungsaufgaben

+ Recherchieren Sie die Einsatzmöglichkeiten des Medwedew-Automaten. In welchem Rahmen macht dessen Beschränkung Sinn?
+
