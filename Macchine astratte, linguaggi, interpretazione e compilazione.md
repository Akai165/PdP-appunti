## Macchina Von Neumann
La base della struttura dei moderni pc
- MEMORIA: sono memorizzati i programmi e i dati 
- CPU: Dove vengnono eseguiti i programmi immagazzinati in memoria prelevando istruzioni e dati in codice macchina ed eseguendo un'operazione dopo l'altra
## Ciclo Fetch-Decode-Execute
- FETCH: Istruzione da eseguire prelevata dalla memoria e inserita nella CPU
- DECODE: L'istruzione viene interpretata e vengono avviate le operazioni interne 
- Data FETCH: Prendo i dati necessari per l'istruzione
- EXECUTE: Porto a termine l'esecuzione dell'operazione
- STORE: Memorizzo il risultato
## Macchine Astratte
L'interprete e il computer entrambi eseguono programmi espressi in un dato linguaggio. 
La macchina consente l'esecuzione step-by-step dei programmi, Astratta perché omette i dettagli delle macchine reali. 

#### visione fondazionale
Una macchina astratta è una collezione di strutture dati e algoritmi in grado di memorizzare ed eseguire programmi. 
I componenti sono: 
- Interprete
- Memoria
- Controllo
- Operazioni "primitive"
Di queste in una macchina reale la memoria fa parte della RAM, il resto rientra nella CPU
## Implementazione software macchina astratta
L'interprete è il programma che prende in ingresso il programma da eseguire (albero di sintassi astratta del programma) e lo esegue ispezionandone la struttura per vedere cosa deve essere fatto 
Gli interpreti di solito permettono al programmatore di fornire il programma in un formato testuale e ne ricavano l'albero. 
L'esecuzione del programma deve rispettare la semantica del linguaggio. 

## Componente di controllo
Una collezione di strutture dati e algoritmi per: 
- Acquisire la prossima istruzione
- acquisire gli operandi 
- gestire le chiamate e i ritorni
- gestire i thread
- mantenere le associazioni fra nomi e valori 
- gestire dinamicamente la memoria
## Operazioni primitive
Una collezione di strutture dati e algoritmi per svolgere ogni operazione prevista dal linguaggio. 

Ipotizzando di avere questo setup: 
- Linguaggio L 
- ML ovvero un computer immaginario progettato su misura per L 
- MO l'hardware vero e proprio 
Il trucco per implementare tutto e farli parlare è l'interprete. 
Ci sono 3 implementazioni: 
- **Interprete puro** è come un traduttore simultaneo. Il programma simula ML su MO traducendo ed eseguendo il codice in tempo reale. Ha lo svantaggio di essere più lento perché perde cicli di calcolo a decodificare istruzioni on the fly
- **Compilatore puro** prende un blocco di codice e lo traduce in un blocco in linguaggio macchina. La macchina astratta viene saltata. Ha il vantaggio che una volta tradotto, il programma è super veloce. 
- **Approccio misto** è un misto dei 2 precedenti. Si compila il codice iniziale in una via di mezzo più facile e poi si usa un interprete per eseguirlo. 
## Run-Time Support nei linguaggi compilati 
Anche i linguaggi compilati generano codice per una macchina intermedia
In questi casi cosa c'é di differenza tra una MI e una MO?

Il supporto a tempo di esecuzione o RTS è una collezione di strutture dati e sottoprogrammi che devono essere caricati su MO per permettere l'esecuzione del codice prodotto dal compilatore 
$$ M_i = M_o + RTS $$ Il linguaggio $L_{M_I}$ non è altro che linguaggio macchina di MO esteso con chiamate al supporto a tempo di esecuzione. 
#### A cosa serve il RTS
Prendiamo come esempio un programma C. E' possibile poter tradurre tutto il programma in un linguaggio macchina. Quel programma però per ogni input e output avrebbe centinaia di istruzioni in LM inutili e ripetitive. Possiamo dunque usare l'RTS per trasformare queste istruzioni una volta sola (routine) e la carichiamo su MO (andrà a far parte dell'RTS). 

## RTS del C
L'RTS del linguaggio C contiene: 
- Strutture dati usate per l'esecuzione
	- lo Stack 
		- ambiente, memoria, sottoprogrammi
	- la memoria a heap
		- puntatori, ...
- I sottoprogrammi che implementano le operazioni necessarie su tali strutture dati. 

## RTS in Java
E' invece un implementazione mista
- traduzione dei programmi Java in byte-code, linguaggio macchina di una macchina intermedia chiamata Java Virtual Machine (JVM)
- L'interprete della JVM opera su tutte le strutture dati 
- Gestione automatica del recupero della memoria a heap (garbage collector)

# Come è fatto un compilatore
Il compilatore ha degli aspetti critici da dover prendere in considerazione: 
- Riconoscere i programmi legali (sintatticamente corretti)
- Generare codice compatibile con il SO della macchina ospite con eventuali ottimizzazioni. 
## Front End
- Scanner: trasforma un programma sorgente nel lessico (token)
- Parser: legge i token e genera il codice intermedio
#### Token
La costruzione lessicale del linguaggio
- operatori e punteggiatura
- parole chiave
- identificatori 
- letterali
#### parser: output (IR)
Formato tipico della rappresentazione intermedia: albero di sintassi astratta. 
La struttura sintattica essenziale del programma

## Analisi semantica (statica)
- type checking
- varie analisi statiche
- varie ottimizzazioni del codice 

#### type checking 
Vengono svolti dal compilatore e prevengono errori nelle operazioni. Controlla che tutte le operazioni siano fatte su operandi del tipo corretto. 
Questi controlli vengono fatti a tempo di esecuzione.

#### type checking statico 
L'esempio è C. Se il programma supera i controlli sui tipi a tempo di esecuzione non ci saranno errori nelle operazioni. 
#### type checking dinamico
L'esempio è JavaScript. Se il programma supera i controlli sui tipi, a tempo di esecuzione non ci saranno errori nelle operazioni però l'interprete deve decidere se fare un'operazione o un'altra in base all'operazione che decidiamo

La differenza principale è la tipizzazione o no di un linguaggio di programmazione. I controlli dinamici sono meglio per la scrittura ma peggio per le performance, al contrario di quelli statici. 

## Back End
- traduce il codice nel linguaggio della macchina ospite o della macchina intermedia
- Necessaria una conoscenza profonda della macchina ospite o della macchina intermedia

