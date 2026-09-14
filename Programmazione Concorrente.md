Un programma concorrente sono due o più processi (thread) che collaborano per realizzare un'applicazione. Ogni processo è un programma sequenziale, e più di questi flussi sequenziali sono attivi, più bisogna impegnarsi per lo sharing e lo scambio dati. 

## Concorrenza vs Parallelismo
- Un programma concorrente ha una sola CPU e i programmi si alternano rapidamente
- Programmazione parallela: N CPU e N task fatte in contemporanea
Un dettaglio pratico è quando abbiamo N CPU con M task ma N<M, in questo caso non tutto è parallelo e bisogna implementare l'interleaving per i task in eccesso, quindi spesso concorrenza e parallelismo vivono insieme. 

## Sistema di transizioni
Per analizzare rigorosamente i comportamenti di un programma concorrenziale, si usa un sistema di transizioni, rappresentato come un grafo. Ogni nodo è uno stato del programma e ogni arco è un passo di esecuzione possibile. 
- Deriva dalla semantica del linguaggio
- Descrive tutti i passi di computazione che un programma può fare
- Tiene conto di tutte le combinazioni di processi possibili 
- Non è deterministico
Il non determinismo del sistema di transizioni astrae dal criterio reale usato dallo scheduler del sistema operativo.

## Operazioni atomiche
L'analisi fino a ora da per scontato che tutte le operazioni siano atomiche, ovvero non interrompibili ed elementari. Il compilatore traduce un singolo comando del linguaggio di alto livello in una sequenza di operazioni nel linguaggio macchina. 

L'interleaving dunque accade a livello assembler, non al codice vero e proprio, però rimane costante 

## Parallelismo 
Con due processi veramente paralleli comunque non si perde la sequenzialità di accesso alle variabili. L'hardware garantisce che al più un processo per volta possa scrivere su una stessa locazione di memoria. 
I processi quindi sono veramente paralleli solamente se vanno su due porzioni di memoria differenti. 

L'analisi corretta è quella a basso livello in quanto spezzettando operazioni atomiche in più piccole, è possibile avere risultati altrimenti impossibili con l'analisi ad alto livello ma comunque reali. 

Per far si che l'analisi sia comunque completa posso simulare il comportamento con la separazione tra lettura e scrittura usando variabili temporanee locali. 

## Shared Memory vs Message Passing
Quando devono coordinarsi più processi o thread, esistono 2 modi di comunicazione
- Shared memory: i processi accedono alle stesse aree di memoria e comunicano leggendo e scrivendo variabili condivise usando metodi di sincronizzazione
- Message Passing: i processi hanno aree di memoria separate ma comunicano inviandosi messaggi tramite IPC forniti dal sistema operativo 

## Processi vs Thread
#### Thread
- Hanno lo stesso spazio di memoria e risorse del processo a cui appartengono
- Comunicazione più semplice e veloce dei processi
- Più leggeri da creare e gestire rispetto ai processi
- Possono girare in parallelo su core diversi
- Un errore in un thread può compromettere l'intero processo perché condivide memoria. 
#### Processi
- Isolamento ognuno ha la sua memoria
- Comunicazione lenta tramite IPC 
- Pesanti da gestire in quanto si allocano risorse indipendenti per ciascuno
- Possono girare su core diversi
- Un errore in un processo non influenza gli altri processi. 

## Producer-consumer
Il modello producer-consumer è uno schema classico della programmazione concorrente. Il producer genera dati e il consumer li preleva, interagendo tramite una risorsa condivisa. 
Il consumer attende testando ogni x ms il valore della variabile (busy waiting) che il producer abbia scritto qualcosa. Questa strategia funziona ma non è efficiente perché il consumer consuma tempo di CPU
è possibile anche usare servizi di segnalazione, sleep() sospende il consumer fino a quando il producer non lo sveglia con un wakeup(). 

Possiamo anche farlo con message passing usando receive sul consumer che lo mette in attesa di messaggio e send(x) dal producer per mandare il valore. Il trasferimento è gestito dal sistema operativo. 
- Thread: i meccanismi di sincronizzazione possono essere realizzati a livello di runtime del linguaggio. 
- Per i processi invece deve essere il sistema operativo a sincronizzare o comunicare. 

Generalmente il grafico è 
- OS che mantiene i processi in esecuzione
- Ogni processo ha uno o più thread che condividono la stessa memoria
- Sia i processi sia i loro thread possono essere eseguiti in parallelo su più CPU. 

Nel corso ci si basa su Multithreading con shared memory assumendo di avere un singolo processo e una singola CPU. 

## Modello programmazione concorrente
Per capire tutti i possibili intrecci di esecuzione serve un modello matematico, questo modello si chiama sistema di transizioni, un grafo in cui ogni nodo è una fotografia dello stato del programma in un certo istante e ogni arco è un passo di calcolo possibile. Il sistema di transizioni è gestito dalla semantica operazionale del linguaggio. 

## Linguaggio di base (sequenziale)
Si definisce un linguaggio imperativo minimale, puramente sequenziale. Le espressioni possono essere numeri interi, somme, lettura di una locazione di memoria (|qualcosa) scrittura in una locazione, l'istruzione vuota skip e la sequenza di due espressioni e;e. 
Il linguaggio ha solo due tipi, int per interi e unit per le istruzioni che non producono un valore utile. L-assegnamento è visto solo come un'esppressione di tipo unit. 

Le regole di tipo servono a controllare che l'espressione sia ben formata. 
La semantica small step invece descrive passo passo come si esegue il programma. Ogni passo trasforma una configurazione es in una nuova configurazione e's'. 

## Estensione concorrente con thread
per modellare l'esecuzione concorrente si aggiunge un nuovo operatore di composizione parallela scritto e1 | e2 rappresenta 2 thread che condividono la stessa memoria e vengono eseguiti con interleaving. 
- condividono memoria
- avanzano un passo alla volta in ordine non deterministico
- non hanno un'identità propria
- la loro terminazione non è osservabile dall'esterno e non restituiscono risultato finale
La regola semantica è che per ogni istante si può far avanzare o uno o l'altro thread. portando a scelta non deterministica. 
Le operazioni atomiche sono solo somma, dereferenziazione !, assegnamento := e skip. Lettura e scrittura in memoria sono operazioni atomiche separate. 

## Sincronizzazione tramite mutex
Un mutex serve a poter bloccare una locazione di memoria mentre un'altro la sta modificando.
- Ad ogni area di memoria è associato un mutex
- Un thread che vuole accedere all'area protetta lo blocca con block m 
- Se il mutex è libero il thread lo acquisisce e procede
- Se il mutex è occupato resta bloccato finché non viene rilasciato
- Terminato l'uso il thread esegue unlock per liberare il mutex. 

La configurazione dunque diventa una tripla <e, s, M> dove M è un mutex true = occupato e false = libero. 

## Graining 
Una volta deciso di usare un mutex bisogna scegliere quale delle 2 strategie aprocciare

#### Coarse-grained
Un unico mutex o pochi muteex che proteggono tutte le locazioni. Riduce fortemente la concorrenza. Bloccare quel mutex blocca tutti gli altri thread, anche quelli che accedono a locazioni completamente diverse. 

#### Fine-grained
Un mutex diverso per ogni locazione. Permette più libertà di esecuzione concorrente. I thread possono progredire in parallelo su locazioni protette da mutex diversi. Lo svantaggio è la complessità di gestione e il rischio di deadlock. 

## Deadlock
E' possibile che quando due o più thread acquisiscono mutex in ordine inverso, si crea un loop circolare. 
Ex. 
	lock m1; lock m2 e l'altro lock m2; lock m1. Entrambi bloccano la prima e rimangono in attesa per la seconda reciproca, ma non verranno mai liberate. Il programma si blocca per sempre. 

Ci sono tre strategie per prevenire il deadlock 
- Deadlock prevention: si impongono regole verificabili staticamente dal compilatore per esempio sull'uso dei lock, che garantiscono che non si possa creare un attesa circolare. 
- Deadlock avoidance: supporto a runtime del linguaggio monitora l'esecuzione e si accorge quando il programma sta per entrare in deadlock, cambia dinamicamente l'ordine di esecuzione dei thread per evitarlo 
- Deadlock recovery: il deadlock avviene ma il runtime lo rileva e interviene dopo per riportare il sistema ad uno stato senza deadlock interrompendo o riavviando un thread. 

## 2-phase locking
Si definisce un ordinamento totale predefinito dei mutex, e si impone che ogni thread acquisisca i muter sempre nello stesso ordine crescente, e li rilasci in ordine decrescente. I mutex dopo averli presi devi rilasciarli tutti insieme, non solo alcuni per poi bloccarne altri. 
