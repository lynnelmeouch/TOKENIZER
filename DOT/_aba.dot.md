```C
digraph AEF {

node [fontsize=6]

// == AEF NAME
  node [shape=plain]
    AEF [fontsize=10, fontcolor=blue, label="(a|b)*.aba"]

// == STATE
  node [shape=circle]

// == ACCEPTING STATE 
  node [peripheries=2]
    ACCEPT [style=filled, fillcolor=green4, fontcolor=white]
    
// == NON ACCEPTING STATE
  node [peripheries=1]
    REJECT [style=filled, fillcolor=black, fontcolor=white]
    INIT

// == INITIAL STATE
  edge [fontsize=12]
    AEF -> INIT
    
// == TRANSITIONS
  edge [fontcolor=green4]
  INIT -> INIT [label="a"] 

// == COMPLETION   
  edge [color=gray, fontcolor=gray]
    REJECT -> REJECT [label="(_)"]
}
```
