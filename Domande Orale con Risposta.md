### 1) Spiegare la differenza tra type-checking statico e dinamico, evidenziando i rispettivi vantaggi e svantaggi

La differenza tra type-checking statico e dinamico è il momento in cui il linguaggio verifica la correttezza del tipo dato. 
Nel type-checking statico i tipi delle variabili sono controllate al momento di compilazione, prima ancora che il programma venga eseguito. Si dichiara esplicitamente il tipo delle variabili (int, string ecc). Il vantaggio è la facilità nel trovare incongruenze di tipi dentro le operazioni, ma è molto più rigido e verboso. Bisogna scrivere più codice e più preciso, allungando i tempi di stesura del codice. type-checking usato in Java, C++, Typescript ecc...
Il type-checking dinamico invece si basa sul controllo di tipi a runtime, non dichiaro nel codice il tipo della variabile e posso anche far assumere alla variabile più tipi nel mezzo del programma. Aumenta la flessibilità di programmazione e la stesura, ma porta problemi di operazioni se sbagliamo a mettere due variabili con tipi diversi (esempio classico il sottrarre un numero ad una stringa). Viene usato dai linguaggi non tipizzati come Javascript, python. 

## 2) Nell'ambito della programmazione orientata agli oggetti, descrivere la differenza tra structural subtyping e nominal subtyping

La differenza tra nominal e structural subtyping riguarda il criterio con cui il typechecker stabilisce se un tipo S è sottotipo di un tipo T (S <: T) garantendo il rispetto del principio di sostituibilità di Liskov. 
Il nominal subtyping si basa esclusivamente sulla dichiarazione esplicita del sottotipo con costrutti di linguaggio specifici (guarda extends in Java). S e sottotio di T solo e solo se S dichiara esplicitamente la cosa nel codice. Se i due tipi hanno esattamente la stessa interfaccia ma nomi diversi e nessuna gerarchia definita, allora sono tipi incompatibili (cosa che vedremo non vera per lo structural). Questa metodologia porta il vantaggio di avere più sicurezza semantica (evitando equivalenza accidentale tra concetti diversi) e typechecking rapido. Gli esempi principali di questo subtyping sono Java e C++.
Lo structural subtyping si basa esclusivamente sulla forma e la struttura interna dei tipi. S è sottotipo di T solo e soltanto contiene almeno tutti i membrti definiti di T, con tipi compatibili in covarianza/controvarianza). Non servono dichiarazioni esplicite. Il sottotipo strutturale viene vcerificato staticamente a tempo di compilazione. Questa metodologia è molto flessibili, e il disaccoppiamento tra moduli è presente (un tipo può soddisfare un'interfaccia creata in una libreria terza senza importarla). È un comportamento tipico di linguaggi come TypeScript e Go. 

## 3) In Java, Integer è un sottotipo di Number. Che tipo di relazione c'è tra ``List<Integer>`` e ``List<Number>?`` Per quale motivo?

Tra `List<Integer> e List<Number>` non c'è nessuna relazione di sottotipo. I tipi generici di Java sono invarianti. Nonostante la relazione Integer <: Number, vale che strutture più complesse che utilizzano questi tipi non hanno relazioni di sottotipaggio. 
Non posso assegnare un oggetto di tipo ListInteger a un riferimento di tipo ListNumber ne viceversa. Il motivo per questa scelta in Java è l'assicurazione della type safety in presenza di strutture dati mutabili. Ci potrebbero essere problemi come l'assegnazione di un booleano ad una lista di interi, che porterebbe a errori in runtime. A differenza dei generics però gli array in Java sono covarianti. Si tratta di una scelta storica più che pratica, in quanto avrebbe portato a problemi di compatibilità a sistemi più vecchi. 

## 4) Descrivere brevemente le problematiche tipiche dell'ereditarietà multipla (ex. problema del diamante) e la soluzione adottata in C++

L'ereditarietà multipla consente ad una classe di derivare contemporaneamente da più classi base. Offre grande espressività ma porta a problemi a livello sintattico e semantico e di layout di memoria. Il problema principale a cui si fa riferimento è il Diamond Problem. Se abbiamo due classi diverse B e C ed entrambe ereditano da A. Nel momento in cui una classe D eredita sia da B che da C, il compilatore non sa da quale due andare a prendere i riferimenti, generando errori di ambiguità a compile-time. La soluzione adottata da C++ è tramite la parola virtual. Quando andiamo a far ereditare ad una classe con virtual, il compilatore garantisce che tutte le classi che ereditano in questo modo abbiano un'istanza unica condivisa di A, eliminando duplicazioni e ambiguità di accesso. È compito del costruttore della classe più derivata (D) invocare direttamente il costruttore della base virtuale A, scavalcando le chiamate di B e C. Questa soluzione elimina gli accessi tramite offset ma aggiunge puntatori nascosti per risolvere l'indirizzo della base comune. 

## 5) Date le seguenti classi, disegnare le tabelle dei metodi delle due classi, spiegandone il funzionamento e il vantaggio dello sharing strutturale
```Java
class Prima {
	int val = 0; 
	int getVal() { return val; }
	void update(int x) { val = x; }
}

class Seconda extends Prima {
	int old = 0;
	void update(int x) { old = val; val = x; }
	void reset() { val = 0; old = 0; }
}
```
Disegno la tabella dei metodi
```
Tabella dei metodi di Prima (CLASS Prima):
+-------+-------------------+
| Index | Metodo            |
+-------+-------------------+
|   0   | Prima::getVal     |
|   1   | Prima::update     |
+-------+-------------------+

Tabella dei metodi di Seconda (CLASS Seconda):
+-------+-------------------+
| Index | Metodo            |
+-------+-------------------+
|   0   | Prima::getVal     |  <-- ereditato (stesso puntatore)
|   1   | Seconda::update   |  <-- sovrascritto (puntatore aggiornato)
|   2   | Seconda::reset    |  <-- metodo aggiunto dalla sottoclasse
+-------+-------------------+
```
Il compilatore conosce solo il tipo apparente della variabile. Il compilatore vede che update si trova all'indice statico 1 nella tabella Prima e quindi prende quello nel caso di `Prima obj = new Seconda(); obj.update(10)`A tempo di esecuzione invece il descrittore dell'oggetto puntato da obj viene analizzato dalla JVM per ricavarne il tipo effettivo (in questo caso Seconda). 
Poiché update ha avuto un Override da parte di Seconda, vado a prendere quella funzione lì, realizzando un dynamic dispatch senza dover risalire la catena di ereditarietà. 
Lo sharing strutturale consiste nel fare in modo che la tabella della sottoclasse riprenda la struttura (ordine delle righe e indici) della tabella della superclasse, aggiungento in coda i nuovi metodi. 
Questo permette condivisione dle codice per metodi non ridefiniti come getVal e risoluzione in tempo costante O(1) a tempo di compilazione. 

## 6) Descrivere il meccanismo delle iTable per la gestione delle interfacce in Java. Dato il codice disegnare la tabella dei metodi della classe Prova e la relativa iTable, spiegando brevemente il funzionamento al momento dell'invocazione. Cosa cambia chiamando il metodo mario su un oggetto con tipo apparente Prova rispetto a uno con tipo apparente I2?
```Java
interface I1 {
    public void pippo();
    public void pluto();
}
interface I2 {
    public void mario();
    public void luigi();
}
class Prova implements I1, I2 {
    public Prova() { System.out.println("C"); }
    public void pippo() { System.out.println("Pippo"); }
    public void pluto() { System.out.println("Pluto"); }
    public void mario() { System.out.println("Mario"); }
    public void luigi() { System.out.println("Luigi"); }
    public void foo() { }
}
```
In presenza di ereditarietà singola tra classi, la JVM organizza i metodi in una tabella chiamata Dispatch Vector o tabella dei metodi. Grazie al meccanismo dello sharing strutturale la tabella di un a sottoclasse mantiene esattamente l'ordine e gli offset dei metodi originali della superclasse. Il compilatore può dunque calcolare staticamente l'indice del metodo e la chiamata avviene in O(1) tramite l'istruzione bytecode `invokevirtual`. Quanto una classe invoca più interfacce, lo sharing strutturale non funziona più in quanto entrambe le classi vorranno indici che andranno a cozzare tra di loro. JVM applica la iTable ossia una struttura interna associata alla classe che mappa ogni interfaccia implementata al relativo sotto-vettore di metodi dedicati. 
```
+------------------------------------+
|       Interface Map (iTable)       |
+--------------+---------------------+
| Interfaccia  | Metodi / Puntatori  |
+--------------+---------------------+
| "I1"         | pippo -> Prova.pippo|
|              | pluto -> Prova.pluto|
+--------------+---------------------+
| "I2"         | mario -> Prova.mario|
|              | luigi -> Prova.luigi|
+--------------+---------------------+
```
Quando viene chiamato un metodo su un obj la JVM accede al descrittore dell'oggetto dell'heap per identificare la clsase a runtime e recuperare il riferimetno della classe/tabelle dei metodi. In base al tipo apparente con cui è referenziato l'oggetto, il bytecode userà un'istruzione differente per risolvere l'indirizzo del metodo da eseguire. 
La differenza alla chiamata mario è che nel caso di `Prova` ha la tabella dei metodi pronta e quindi l'esecuzione sarà a O(1). Nel caso di oggetto I2 il compilatore conosce solamente l'interfaccia I2 ma non conosce a priori quale classe ha a runtime il riferimento. La JVM esegue il puntatore della iTable della classe concreta. Fa una scansione/ricerca nell'interface Map fino a prendere la voce I2, una volta trovata l'interfaccia accede al relativo offset. Ha un overhead maggiore e per limitare questa cosa la JVM sfrutta tecniche di inline caching. 


## 7) Descrivere caratteristiche e funzionamento degli iteratori nel Java Collection Framework. Nel codice seguente, quale problematica si verifica a runtime e come va risolta?
```Java
void pulisciLista(List<String> lista, String s) {
    Iterator<String> iterator = lista.iterator();
    while (iterator.hasNext()) {
        String elemento = iterator.next();
        if (elemento.equals(s))
            lista.remove(elemento);
    }
}
```
Gli Iteratori nel JCF implementano l'oggetto Iterator che serve per scorrere sequenzialmente gli oggetti di una collezione astraendo dalla sua struttura dati effettiva. Fornisce 3 metodi cardine, hasNext per verificare la presenza di altri elementi, next per restituire l'elemento successivo e remove per eliminare in sicurezza l'ultimo elemento restituito. 
L'algoritmo è separato dalla logica esterna della collezione, ma la collezione nel momento in cui ha un iterator attivo, non può essere modificata se non con il metodo remove dello stesso. 
Nel codice infatti all'interno dell'if la modifica strutturale con remove è fatta tramite la Lista e non tramite Iterator. Questo porta ad un disallineamento rispetto alla lista modificata dell'iteratore e porta ad un'eccezione nel codice. Per risolvere andrebbe scritto al posto di `lista.remove(elemento)` `iterator.remove()`. 

## 8) Descrivere l'approccio di garbage collection "mark and sweep" indicandone vantaggi e svantaggi

L'approccio mark and sweep del garbage collector funziona a 2 fasi, come da nome la fase mark e la fase sweep. 
La fase mark è quella iniziale, tutte le celle di memoria puntate sono categorizzate itrinsicamente in una struttura dati a grafo dove i nodi sono le celle di memoria e gli archi sono i puntatori a tali celle. In questa fase il GC partendo da tutte le radici va a controllare tutti i nodi di questo grafo, andandoli a segnare in una frazione di quella cella di memoria. La fase di sweep è la fase successiva, andando a controllare in tutto l'heap tutte le celle di memoria non segnate dalla fase precedente, eliminandole. Questo ciclo di iterazioni viene fatto on-demand quando la memoria libera sta per esaurirsi (esempio malloc o new) oppure periodicamente in background. Questa metodologia ha il vantaggio di levare problematiche come puntatori a ciclo (problema importante della metodologia counting reference), però porta ad avere tempi di esecuzione più lunghi in quanto il programma deve essere temporaneamente interrotto per poter permettere l'esecuzione delle due fasi. Un'altro svantaggio è che non gestisce la frammentazione della memoria dell'heap. 

## 9) Descrivere la tecnica di garbage collection denominata "copying collection", discutendone vantaggi e svantaggi. 

La tecnica copying collection è una tecnica del garbage collector basata su mark and sweep che va a risolvere il problema di frammentazione dell'heap. 
La tecnica si basa non su uno ma su due heap separati. Uno attuale e uno di copia. 
Il procedimento è: 
- Fase di Mark and Sweep sull'heap attivo, quindi ricerca a partire da tutte le radici del grafo dei puntatori di tutte le celle attualmente utilizzate (che hanno un puntatore attivo valido partendo da variabili globali o metodi globali) per segnarle (Mark) 
- Fase di Copying: Ogni elemento dopo che viene segnato come utilizzato, viene poi copiato dentro l'heap copia, tutti questi elementi saranno copiati insieme e vicini tra di loro, permettendo all'heap copia di essere non frammentato e più ottimizzato dell'heap attuale. Dopo che tutti i nodi marked saranno stati copiati, a quel punto si scambia il ruolo dei due heap. 
Il vantaggio di questo approccio è una frammentazione nulla dell'heap, in quanto ad ogni ciclo di esecuzione si avrà come risultato un heap non frammentato e ottimizzato. Gli svantaggi di questa tecnica sono il maggior consumo di memoria (non uno ma ben 2 heap) e, visto il suo utilizzo di Mark and Sweep, la velocità di esecuzione in quanto sarà necessario bloccare temporaneamente il programma per effettuare le operazioni di pulizia. 

## 10) Descrivere l'approccio basato su reference counting con vantaggi e svantaggi. Scrivere un breve esempio di codice/pseudo-codice in cui emerge il problema principale di questo modello.

L'approccio reference counting per il Garbage Collector si basa su un contatore dentro la locazione di memoria che segna il numero di puntatori che puntano ad essa. Ogni volta che un puntatore punta o non punta più ad una locazione, il contatore viene aggiornato. Nel momento in cui il contatore tocca 0, la locazione viene liberata. 
- L'approccio ha il vantaggio di essere molto veloce e real-time, non a bisogno di cicli di interruzione del programma per funzionare.
- L'approccio però porta anche degli svantaggi, non va a considerare la frammentazione dell'heap, e in primis porta al problema dei puntatori ciclici. 
Per problema di puntatori ciclici si intende 2 Locazioni (variabili o classi) che si puntano a vicenda. In questo caso se lo scoping non va ad utilizzarli, essi dovrebbero essere cancellati, però puntandosi a vicenda entrambi avranno 1 sul contatore del GC e quindi non verranno mai eliminati. 
Un esempio in pseudo-codice della cosa potrebbe essere
```
class amici = {amico}

function scoping () = {
	amico amico1 = new amici;
	amico amico2 = new amici; 
	amico1.amico = amico2;
	amico2.amico = amico1;
	//uscita dallo scope
}
```
In questa funzione abbiamo amico1 e amico2 che si puntano a vicenda. Nel momento in cui lo scoping andrà fuori dalla funzione per il suo completamento, amico1 e amico2 non verranno cancellati in quanto avranno il loro puntatore a vicenda che segna 1 sul counter. 

## 11) Perché servono i meccanismi di mutua esclusione (mutex/lock)? Quale problema può verificarsi a livello assembler se due thread eseguono in parallelo assegnamenti alla stessa variabile come `x := x +1 || x := x + 7`?

I meccanismi di mutua esclusione sono necessari per evitare problemi di perdita di operazioni o altri comportamenti non previsti all'interno della programmazione parallela. Un operazione di assegnamento per esempio a livello assembler è divisa in 3 operazioni, una di lettura, addizione e memorizzazione della variabile. Il problema sorge nel momento in cui queste operazioni vengono fatte in parallelo tra due thread. E' possibile che entrambi i thread facciano nello stesso momento la fase di lettura (non esattamente nello stesso momento in quanto la lettura è atomica, quindi assumiamo che nello stesso momento sia sequenziale), quindi ipotizzando nell'esempio un valore iniziale di x = 0, in entrambi i thread l'operazione sarà 0. A questo punto in base a quale thread farà l'operazione per ultimo, avremo due risultati diversi (o 1 o 7). I meccanismi di mutua esclusione servono proprio a questo, servono a poter permettere l'accesso ad una cella di memoria solamente uno alla volta, permettendo ai 2 thread di accedere alla cella, bloccarla,  fare le loro operazioni e poi sbloccarla. In questo modo indipendentemente da quale delle due operazioni verrà eseguita prima, il risultato sarà sempre 8. 

## 12) Motivare l'importanza dei lock e spiegare la differenza tra locking coarse-grained e fine-grained. 

