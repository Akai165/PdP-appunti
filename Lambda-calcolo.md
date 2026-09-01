c Do po i vari modelli di calcolo sviluppati da Turing, John von Newmann definisce per la prima volta un'architettura di elaborazione che è alla base del funzionamento degli attuali computer. 
Questa architettura prevede due componenti principali:
- la **memoria** dove sono tenuti sia i programmi da eseguire sia i dati da elaborare
- l'**unità centrale di elaborazione (CPU)**, che ha il compito di eseguire i programmi immagazzinati in memoria prelevando le istruzioni, interpretando il tutto e eseguire una volta dopo l'altra aggiornando di conseguenza la memoria. 
Il fatto che i primi computer fossero basati sull'architettura di Newmann, ha condizionato la definizione dei primi linguaggi di programmazione. I linguaggi in stile Turing-Neumann sono caratterizzati da:
- **variabili** che astraggono le locazioni di memoria
- **istruzioni di controllo** come `test-and-jump, goto, if, while...`, che modificano il flusso d'esecuzione di un programma
- **istruzioni di assegnamento** che modificano lo stato della macchina

I linguaggi definiti in questo modo sono detti imperativi, prevedono sequenze di coamndi da impartire alla macchina.  Alcuni esempi sono Fortran, Pascal, C e tutti i derivati del C. 
Parallelamente il $\lambda$-calcolo ha influenzato i linguaggi *funzionali*, permettono in modo simile a come si definiscono le funzioni matematiche, alcuni esempi sono LISP, ML e recenti Haskell, OCaml, Rust ecc..

## 1.2 $\lambda$-calcolo
La sintassi del $\lambda$-calcolo è minimale, è possibile solo scrivere espressioni che contengono dichiarazioni di funzione e applicazioni di funzioni. È possibile introdurre variabili che sono usate come parametri formali delle funzioni. 

 **Def**: Supponendo di avere un insieme infinito di possibili variabili $V = x, y, z, ...$ la sintassi del $\lambda$-calcolo consiste di espressioni definite dalla seguente grammatica:
  ```
	e ::= x     (variabile)
		| λx.e  (astrazione funzionale)
		| e e   (applicazione formale)
  ```
Nella quale x rappresenta in realtà una qualunque variabile in V. 
``λx.x``rappresenta la funzione che associa x a x, ovvero la funzione identità. 
(corrispettivo js `function x {return x}`)

``(λx.x)y`` Qua sto dicendo che esiste una funzione λx.x al quale passo come parametro y. 
Questa cosa poi è a cascata usando come scoping verso destra e l'uso delle parentesi. 
Alcuni esempi di sintassi corretta possono essere
![[Pasted image 20260814170533.png]]
### Convenzioni sintattiche
Per evitare di appesantire le espressioni di $\lambda$-calcolo, si assume che:
1) L'astrazione sia associativa da destra (lo *scope* del costruttore si estende il più a destra possibile). ex. $\lambda$x.$\lambda$y.xy = $\lambda$x.($\lambda$y.(xy))
2) l'applicazione sia associativa a sinistra. Ex. xyz = (xy)z
   ossia prima si applica x a y, poi si applica il risultato ottenuto a x. 
   Analogamente: ($\lambda$x.x)($\lambda$y.y)z corrisponde a (($\lambda$x.x)($\lambda$y.y))z

Considerando queste convenzioni, gli esempi sopra scritti possono essere riscritti come ![[Pasted image 20260814170418.png]]
Per quanto ancora non sappiamo come valutare, possiamo dire che 
- 5) Si applica la funzione identità ad un'altra definizione della stessa funzione con parametri diversi
- 6) Applico la funzione identità ad un'altra funzione identità con parametri diversi e il risultato verrà poi applicato a z
- 7) Applichiamo una funzione che riceve come primo parametro x e successivamente un secondo parametro y, agli argomenti z e k. 
- 8) Simile alla 7 ma il primo argomento passato è la funzione identità, che verrrà applicata a k come indicato dalla funzione. 

## 2 Alberi per rappresentare lambda espressioni
- L'albero per una variabile x è una foglia con etichetta x
- Un'astrazione $\lambda$x.e si rappresenta con un nodo $\lambda$ con 2 foglie, a sinistra il parametro x e a destra l'albero che rappresenta e. 
- L'applicazione di funzione $e_1e_2$ è rappresentato con un nodo $\circ$ con gli alberi relativi a $e_1$ e $e_2$

Ad esempio l'albero per ($\lambda$x.x)3 è:
![[Pasted image 20260814171302.png]]

La funzione identità può essere vista come una scatola con l'istruzione: Qualsiasi cosa mi dai io ti restituisco quella cosa. 
Se invece di Cosa sulla scatola scrivo Oggetto, restituisce lo stesso Oggetto. Quella parola che cambia viene chiamata variabile legata 
L'oggetto effettivo che metto dentro la scatola invece è la variabile libera. 

In lambda calcolo il simbolo $\lambda$ indica che stiamo per fare una funzione, quindi scrivere $\lambda$x.x e $\lambda$y.y è la stessa cosa (var. legate)
Se invece scrivo ($\lambda$x.x)z e ($\lambda$x.x)k, per quanto siano scritte uguali, la variabile libera è diversa e quindi non sarà la stessa cosa. Non sono legate da nessun $\lambda$. 

### 1.3 Insieme delle variabili libere
L'insieme delle variabili libere di una lambda espressione e denotata con $Fv(e)$ è definito ricorsivamente seguendo tre regole ricorsive precide:
- **Caso base** (variabile da sola): Insieme delle variabili libere di una variabile x presa da sola è semplicemente un insieme che contiene solo la X. $FV(x) = \{x\}$  
- **L'applicazione** (due cose vicine) Quando si ha un'applicazione di due espressioni, ti basta unire i loro due gruppi di variabili libere. La regola è: $FV(e_1e_2) = FV(e_1) \cup FV(e_2)$
- **L'Astrazione (la funzione con $\lambda$)**: Quando c'è un'astrazione funzionale, prendo l'insieme delle variabili libere che trovo nel corpo della funzione e ci togli la variabile usata come parametro formale. Succede perché il costruttore $\lambda$ lega quella specifica variabile e la rimuove dall'insieme di quelle ibere. La formula è $FV(\lambda x.e) = FV(e) \\ \{x\}$ 