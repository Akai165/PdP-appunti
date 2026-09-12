## Compilazione + interpretazione
Java implementa un linguaggio intermedio cambiando compilazione e interpretazione, chiamato bytecode. Questo linguaggio viene eseguito dalla JVM (interpreta).
- bytecode è usabile in ogni architettura per cui sia disponibile una JVM. 
- Consente controlli statici e dinamici insieme. 

## Struttura di un programma Java
Un programma Java è un insieme di classi. 
- Ogni classe viene salvata in un file separato (stesso nome della classe con estensione .java)
- Una delle classi dovrà avere il metodo main da cui parte l'esecuzione del programma

## Nucleo imperativo 
Il corpo dei metodi è codice imperativo con sintassi simile al C. 
Ha dei tipi imperativi che usano 1 byte per questioni di indirizzamento, però le più famose sono le seguenti: 

![[Pasted image 20260910192259.png]]
Esiste pure un tipo String ma non è primitivo (è una classe di libreria) ma gestito in modo speciale. Le stringhe hanno tipo String e si possono concatenare con + come se fosse un tipo primitivo (perfetta implementazione con i tipi primitivi). 
Non esistono variabili globali (possiamo però definire variabili visibili al livello di classe). 
Gli Array sono simili al c con qualche differenza di sintassi
```java
int[] numeri

int[] numeri = new int[10];
```

Non esistono puntatori (ma ci sono riferimenti ad oggetti). Gli array possono essere acceduti solo tramite indice e non come puntatori. A run time prima di accedere ad un array la JVM controlla che l'indice sia compreso tra 0 e length-1, altrimenti si solleva un'eccezione. 

Il corpo dei metodi è codice imperativo con sintassi molto simile a quella C. 
Si usano metodi che possono essere ricorsivi anche mutualmente. 
Si possono descrivere funzioni anonime con la seguente sintassi
```java
x -> espressione
x -> { blocco }
```
Non essendo però un linguaggio funzionale, il funzionamento di queste funzioni è particolare. 

## Classi
Una classe è un insieme di variabili e metodi e può prevedere uno o più costruttori per l'inizializzazione delle variabili. 
Ogni entità identificabile nel programma caratterizzata da un comportamento autonomo dovrebbe essere rappresentata da una classe distinta. 

ex. programma che fa operazioni su un conto corrente
- Banca: modella la banca e contiene il main 
- ContoCorrente: descrive gli oggetti che rappresentano conti correnti
```java
public class Banca {
	public static void main(Stringp[] args)
		ContoCorrente conto1 = new ContoCorrente(1000);
		ContoCorrente conto2 = new ContoCorrente(200);
		
		if (conto1.saldo >= 700) {
			conto1.preleva(700);
			conto2.preleva(700;)	
		}	
		System.out.println("Saldo primo conto: " + conto1.saldo)
		System.out.println("Saldo secondo conto: " + conto2.saldo)
	}
}


public class ContoCorrente {
	public double saldo;
	
	public ContoCorrente(double saldoIniziale) {
		saldo = saldoIniziale;
	}
	
	public void versa(double somma) {
		saldo += somma;
	}
	public void preleva(double somma) {
		saldo -= somma;
	}
}
```

Abbiamo usato main di Banca senza creare oggetti di questa classe
- Metodo statico, opssia può essere richiamato anche senza istanziare la classe
- il main è sempre statico.

La classe Banca può accedere a tutti i membri di ContoCorrente 
- Sono pubblici 
- Per evitare la cosa bisognerebbe usare private
## Interfaccia pubblica di una classe
Se ora andessimo a modificare la classe ContoCorrente non andrebbe ad influenzare l'interazione tra le due classi. 
La classe banca potrà continuare a chiamare i metodi come faceva prima. 
Se non modifichiamo l'interfaccia pubblica di una classe, possiamo modificare e ricompilare la classe senza compromettere il resto del programma (COMPOSIZIONALITÀ)
Ad ora ContoCorrente può essere modificato dalla Banca, senza nessun problema, per andare a risolvere questa cosa andiamo a considerare saldo come variabile `private`
```java
private double saldo;

public double getSaldo() { return saldo; }
```

Ad ora però non è possibile andare a modificare direttamente gli attributi di conto corrente, per questo andiamo a creare dei setter per poterli modificare.

## Incapsulamento
I modificatori `public` e `private` consentono di applicare l'incapsulamento. 
- Le variabili rimangono private
- L'accesso esterno è consentito solo tramite un certo numero di metodi pubblici
- Dall'esterno si può non conoscere la rappresentazione degli attributi
- È possibile definire metodi privati (usabili solo dalla classe stessa)

## Interfacce 
L'interfaccia pubblica di una classe può essere esplicitata usando le interfacce. Un'interfaccia Java contiene una specifica astratta della classe, indica quali membri pubblici la classe deve contenere. 
Rende le classi una ADT (Abstract Data Type).

Ex. specifichiamo un'interfaccia per il conto corrente (contiene solo intestazioni di membri pubblici, niente costruttori)
```java
public interface BankAccount {
	public double getSaldo();
	public void versa(double somma);
	public boolean preleva(double somma);
}
```
Ora dichiaro esplicitmente che ContoCorrente implementa l'interfaccia appena creata (`implements BankAccount`)
```java
public class ContoCorrente implements BankAccount {
	private double saldo; 
	public ContoCorrente(double saldoIniziale) {...}
	public void versa(double somma) {...}
	public double getSaldo() { return saldo }
	public boolean preleva(double somma) {
		if (saldo > = somma) {...}
		else return false
	}	
}
```
Ora nell'esecuzione del programma non cambia niente, però è esplicito che il conto corrente è un tipo di dato astratto e la sua segnatura è nell'interfaccia. 
La classe ContoCorrente è obbligata a implementare tutti i metodi dell'interfaccia (controllato dal compilatore). 
ex. Conto che consente di fare un numero limitato di movimenti
```java
public class ContoLimitato implements BankAccount {
	private double saldo;
	private int mov; // movimenti rimasti
	public ContoLimitato(double saldoIniziale, int movimenti) {
		saldo = saldoIniziale; mov = movimenti;
	}
	public void versa(double somma) { if (mov>0) { mov--; saldo+= somma; }}
	public double getSaldo() { return saldo; }
	public int getMov() { return mov; } // in più rispetto all’interfaccia
	public boolean preleva(double somma) {
		if (saldo>=somma && mov>0) { mov--; saldo-=somma; return true; }
		else return false;
	}
}
```

Con più implementazioni alternative di BankAccount si può scegliere di volta in volta quale usare. 
Questo è un caso di Nominal Subtyping. 
La relazione di sottotipo nasce dal fatto che le 2 classi nominano esplicitamente BankAccount
Il compilatore controlla anche la proprietà strutturale, ma quello che conta è che ci sia scritto `implements BankAccount`

## Tipo apparente vs effettivo
Il compilatore fa i suoi controlli statici sul tipo apparente, in quanto il tipo effettivo potrebbe non essere noto a tempo di compilazione. 
In queste situazioni si può testre il tipo effettivo con un controllo run time e forzare il compilatore con un type cast. 
```java
if (conto istanceof ContoLimitato) ((ContoLimitato) conto).getMov();
```
Il cast (coercion) tra due classi, interfacce o entrambe si pùo fare solo se tra le due esiste una relazione di sottotipo (esplicitamente implementata o estensione)

Nulla vieta che una classe implementi più interfacce
Esempio un condo di deposito, si può versare più volte e poi chiudere una volta per tutte riscattando il saldo. 

## Membri statici e d'istanza
Il `main` è un metodo statico. Può essere richiamato anche se non ci sono istanze dell'oggetto della classe. 
Distinguiamo: 
- membri (variabili e metodi) d'istanza: codificano lo stato di ogni singolo oggetto e implementano operazioni che lavorano su tale stato. 
- membri (variabili e metodi) statici: codificano informazioni e operazioni di classe, ossia variabili condivise tra tutti gli oggetti che son o istanze di quella classe e metodi che non operano sullo stato dei singoli oggetti. 
Operativamente:
- Una variabile d'istanza è presente in memoria in tante copie quanti sono gli oggetti
- Una variabile statica è presente una sola volta in memoria prima che venga creato un oggetto
- Un metodo d'istanza è richiamato su un oggetto e può accedere sia alle variabili d'istanza che statiche
- Un metodo statico può accedere solo alle variabili statiche e può essere richiamato anche se non ci sono oggetti di quella classe. 
La differenza sostanziale è se quando è creata la classe alla variaible è inserito il valore oppure no. 

## Modello della memoria
Vediamo che cosa succede nella memoria della JVM eseguendo il programma della Banca

In un primo modello astratto possiamo identificare 3 aree
- Ambiente delle classi: contiene il codice delle classi inclusi metodi e variabili statiche
- Stack: contiene i record di attivazione dei metodi in esecuzione con le variabili locali e parametri
- Heap: contiene gli oggetti creati dai programmi raggiungibili tramite riferimenti con le loro variabili d'istanza


# Ereditarietà per estensione
In Java è possibile definire una classe come estensione di un'altra usando extends. 
`class B extends A {...}`
In questo caso A è super-classe mentre B è sottoclasse
Una classe può implementare più interfacce ma può estendere una sola super-classe.

## Classe Object 
Una classe che non estende altre classi implicitamente estende la classe di default Object. 
Fornisce alcuni metodi disponibili per tutte le classi come
- toString per una rappresentazione testuale di un oggetto
- equals(obj) confronta l'oggetto corrente con obj
- clone() crea una copia dell'oggetto

equals è importante perché confronta 2 oggetti nei contenuti. Ogni classe può però overridare equals per realizzare un confronto sensato per il tipo di dato rappresentato. 
Confrontare con == invece significa valutare se i riferimenti sono uguali (alias)

N.B. le stringhe con String sono immutabili, quando le modifico non vengono cambiate ma restituiscono una nuova istanza di String. 

## Visibilità
Dipende anche qua dal private public, la sotto-classe non può accedere alle variabili private della super-classe. 
`protected` fa una cosa particolare, rende le variabili visibili alle sotto-classi ma non alle classi esterne

## Classi astratte
Con la gerarchia, può avere senso avere classi parzialmente definite (classi astratte)

Esempio con un solido generico che va a creare variabili di tipo `abstract` per volume e superficie, poi che saranno implementate effettivamente dalla sotto-classe di implementazione,. 

## Gerarchia di classi e polimorfismo 
`extends` consente di creare una gerarchia di classi organizzata ad albero. 

## Overloading overriding e dynamic dispatch
Cosa succede quando invochiamo un metodo su un oggetto? 
La chiamata deve superare i controlli statici operati dal compilatore
- il metodo deve essere presente nella classe che descrive il tipo appartenente
- I parametri attuali devono essere compatibili con il tipo dei parametri formali del metodo. 


In una stessa classe possiamo definire più metodi con lo stesso nome. I metodi in overloading però devono avere firme diverse (parametri diversi in numero o tipo. )

Se il metodo non viene trovato si verifica un errore in compilazione, il type checker può applicare la regola di inferenza per garantire che il metodo esista. 

Se cercando un metodo si va su per la gerarchia, si va a considerare il primo Overiding che il compilatore trova. 
L'effettiva ricerca ogni volta dell'albero delle gerarchie è inefficiente, per risolvere questo problema JVM usa un approccio ottimizzato basato su 
- tabelle di metodi (strutture dati con puntatori al codice dei metodi)
- sharing strutturale (tabella di una sotto-classe che riprende la struttura della tabella della super-classe aggiungendo righe per i metodi nuovi)
- 

## Ereditarietà multipla e Mixin
Abbiamo delle volte bisogno che una classe erediti caratteristiche da più classi senza dover copiare codice in più classi diverse. Come possiamo fare?
Diamond Problem: Se ho una classe A con un metodo foo, B e C sono sotto-classi di A e fanno entrambe l'override di foo. Se creo una classe D che eredita da entrambe, quale dei 2 foo verrà eseguito?

Ci sono vari modi per risolvere questa cosa: 

## C++ ed ereditarietà multipla
C++ ha eredità multipla senza restrizioni. Quando c'è ambiguità il compilatore si rifiuta semplicemente di eseguire dando errore. Va specificato a codice quale delle copie di funzione vogliamo eseguire. 

A runtime ogni oggetto ha  un descrittore che punta a più vtable (una per superclasse) quindi il dispatching resta efficiente e a tempo costante. Per l'ereditarietà ripetuta c++ offre la parola virtual. 
```c++
class B : virtual public A {};
class C : virtual public A {};
class D : public B, public C {};
```
Così D avrà una sola copia delle variabili di A condivisa tra B e C. Ci sarà più overhead però si leva il problema della memoria sprecata. 

## Java: interfacce
Jav usa ereditarietà singola per classi + implementazione multipla con le interfacce. Prima di Java8 un'interfaccia aveva solo le firme, quindi nessun diamond problem. 

Il problema tecnico però resta, quindi Java risolve con la itable (interface table)
- Se chiamo un metodo con un tipo apparente = classe, il dispatching passa dalla vtable normale
- Se chiamo un metodo con un tipo apparente - interfacia, il dispatching passa dalla itable che deve scandire la mappa delle interfacce finché non trova quella giusta. 

## Python
Python è un linguaggio interpretato, senza compilazione statica, non può quindi precostruire vtable e itable. Deve risolvere il problema runtime risalendo la gerarchia delle classi, 
La gerarchia è un grafo, non un albero. La soluzione quindi è calcolare un MRO (Method Resolution Order), ovvero una lista lineare e univoca in cui le classi vanno controllare, usando l;'algoritmo C3. 
- Determinismo: ordinamento sempre unico
- Conservazione dell'ordinamento locale: se una class eestende più classi, l'ordine viene rispettato nell'MRO .
- Monotonia: se c1 è sottoclasse di c2, c1 viene sempre prima di c2 nell'ordine finale. 

## Mixin
Invece di continuare a scorrere l'ereditarietà multipla con trucchi vari, alcuni linguaggi usano composizione invece di ereditarietà. 

Un mixin è un blocco di comportamento riusabile che viene messo dentro una classe, ma non è una vera superclasse e non se ne creano oggetti a se assestanti. 


## Specifiche di programmazione e Principio di Sostituzione di Liskov

Prima di scrivere codice vero e proprio un buon processo di sviluppo è quello di seguire un ordine di
1) Definizione della gerarchia di classi e interfacce
2) Identificazione dei membri pubblici di ogni classe
3) Definizione delle specifiche di ogni metodo pubblico
4) Scrittura dei test basati su membri pubblici e specifiche
5) Definizione dei membri privati
6) Implementazione del codice 
## Requires ed Effects
Una specifica è un contratto scritto a commento, con scritto cosa deve garantire chi chiama il metodo. Viene usato il // REQUIRES per quanto riguarda le condizioni sui parametri che devono essere vere prima dell'esecuzione e il // EFFECTS per tutto ciò che succede dopo l'esecuzione. 
Java offre una sintassi standard per le specifiche tramite JavaDoc (da cui poi è possibile creare documentazione HTML con javadoc NomeClasse.java)

## Eccezioni in Java
Un'eccezione è un evento anomalo che interrompe il flusso di un programma. Java distingue le eccezioni in 2 categorie:
- Unchecked: il compilatore non obbliga a gestirle (errori di programmazione spesso)
- Checked: estendono exception ma non RuntimeException (come le Unchecked), il compilatore obbliga a gestirle con 2 opzioni
	- try-catch
	- dichiararle nell'intestazione con throws, delegandone la gestione al chiamante

La gestione con throws è obbligatoria solo se il metodo non gestisce l'eccezione internamente. 

## Precondizioni, postcondizioni e notazione formale
La coppia REQUIRES/EFFECTS può essere formalizzata. Se la precondizione P è vera all'inizio, allora dopo l'esecuzione del metodo m deve essere vera la postcondizione Q. 
$$\{P\} \ m \ \{Q\}$$
Specificare un metodo significa definire coppie (P, Q) e poi scrivere un metodo che le rispetti. Questa formula si chiama Tripla di Hoare

## Dalla specifica al testing all'implementazione
Essendo che la specifica descrive completametne il comportamento osservabile di un metodo, è possibile scrivere un tester prima ancora di implementare la classe. Bast achiamare i metodi pubblici e verificare che rispettino P e Q. 
Essendo che i test non sono esaustivi esistono 
- strumenti di generazione automatica dei test
- tecniche di analisi statica e model checking, dimostrano matematicamente la correttezza del codice. 

## Principio di sostituzione di Liskov
L'esistenza di specifiche permette di ragionare sul rapporto tra superclasse e sottoclasse dal punto di vista del comportamento. 
LSP: un oggetto di un sottotipo deve poter sostituire un oggetto del supertipo senza alterare il comportamento dei programmi che usano il supertipo. 

In pratica se un programma funziona con Rectangle, deve continuare a funzionare allo stesso modo se al posto di Rectangle uso una sottoclasse Square, a patto che chi chiama i metodi rispetti le precondizioni della superclasse. 

- Esempio corretto: IntSet / FlexIntSet -> FlexIntSet implementa un set con la possibilità di poter ridimensionarsi automaticamente, questo è positivo in quanto va a funzionare in tutte le specifiche che utilizzano IntSet e anche di più. 
- Esempio scorretto: Rectangle / Square -> Rectangle e Square sono entrambe utilizzabili da tutti ma vanno a variare il funzionamento in quanto Square cambia le formule per area e altezza. 
  Per fare in modo che questo esempio sia corretto andrebbe implementata una superclasse Shape dalla quale devono ereditare in modo separato Rectangle e Square. 
Verificare se il LSP vale tra 2 classi in generale è indecidibile, per questo non è una cosa che il compilatore può fare da solo e ci si appoggia a regole pratiche di sviluppo. 

## Le tre regole dell'LSP
### 1) Segnatura
La sottoclasse deve avere tutti i metodi della superclasse (extends in Java)
In caso di Overriding il metodo della sottoclasse deve: 
- avere las tesa firma
- Può sollevare meno eccezioni
- Può avere un tipo di ritorno più specifico (covarianza di tipo)
Se il tipo del metodo della superclasse è S -> T1, quello della sottoclasse può essere S -> T2 con T2 <: T1 

### 2) Regola dei metodi
Un sottotipo può :
- Indebolire le precondizioni (accettare più casi)
- Rafforzare le postcondizioni (garantire di più ) ma solo entro i limiti garantiti dal supertipo. 

Esempio se la superclasse ammette numeri solo superiori o uguali a 0, non posso fare che la sottoclasse accetti solo numeri maggiori o uguali a 10, in quanto ci sarebbe un restringimento delle condizioni, posso invece fare che accetti numeri maggiori o uguali a -10, andando ad estendere il range della superclasse. 

### 3) Regola delle proprietà
Un  sottotipo deve preservare tutte le proprietà dimostrabili sugli oggetti del supertipo 
- Proprietà invarianti: sempre vere durante la vita dell'oggetto
- Proprietà di Evoluzione: descrivono come lo stato può cambiare nel tempo


Tutte queste proprietà nascono dal modello del tipo di dato astratto e vanno documentate nell'OVERVIEW della classe


## Java Generics 
## Perché servono i Generics 
Se dobbiamo scrivere una struttura dati (per esempio una coda) che funzioni su tipi diversi, senza uno strumento apposito abbiamo 3 strade
- Duplicare il codice per ogni tipo (viola il principio di astrazione)
- Usare Object come tipo generico Universale (perde ogni informazione sul tipo reale)
- Controllare con instanceof prima di ogni cast (sicuro ma va ripetuto manualmente)

I Generics risolvono il problema permettendo di scrivere classi, interfacce e metodi parametrici rispetto al tipo. Il compilatore controlla staticamente che i tipi siano coerenti, eliminando la necessità di cast e spostando gli errori dal runtime al momento della compilazione. 

## Sintassi 
Una classe o interfaccia generica si dichiara aggiungendo una lista di variabili di tipo tra parentesi angolari. 
```java
class Name<TypeVar1, ..., TypeVarM> {...}
interface Name<TypeVar1, ..., TypevarN> {...}
```
Le variabili di tipo (T, E, K, V) sono degnaposto che verranno sostituiti da un tipo concreto solo quando la classe viene istanziata. 
Esempio: Collection ''T'' definisce operazioni che agiscono sul tipo generico T. Quando dichiaro 'Collectionstring il compilatore blocca c a contenere solo String: c.add("hi") compila, c.add(2) genera un errore di compilazione. 
Le variabili di tipo compaiono anche dentro il corpo della classe, garantendo coerenza. LE variabili sono pure dichiarabili al livello di singolo metodo.

## Sottotipo e varianza
Sappiamo che `Integer` è un sottotipo di `Number`, Quindi viene naturale pensare che una lista di interi sia un sottotipo di una lista di number, ma non è così, i generici sono invarianti. 
Per capire perché si definiscono 3 regole
- Covariante: se `T <: S` allora `A(T) <: A(S)`
- Controvariante: Se T <: S  allora A(S) <: A(T) la relazione si inverte
- Invariante: ne covariante ne controvariante. Nessuna relazione di sottotipo tra A(T) e A(S) anche se T e S sono in relazione

I generici in Java sono invarianti, quindi le liste dei 2 tipo non hanno relazioni tra di loro. 

Si usa questa scelta per garantire sicurezza del tipo a runtime. 

### Caso degli array, la covarianza è permessa

A differenza dei tipi generici, gli array sono covarianti. Questa è una scelta storica (gli array esistevano da prima dei tipi generici, e cambiare le regole avrebbe rotto la compatibilità rispetto a codici già scritti). 
- Array: il controllo è dinamico, ogni array ricorda il proprio tipo a runtime quindi la JVM può rilevare incompatibilità
- Generics: il controllo è statico. 

## Vincoli sui tipi generici extends e super
A volte non basta dire che T è un tipo qualsiasi, serve poter fare  operazioni specifiche su T quindi bisogna vincolare quali tipi possono sostituire T. Si usa extends per fissare un limite superiore nella gerarchia
```java
class Name<TypeVar1 extends Type1, ...., TypeVarN extends TypeN> {...}
```
ex.
```java
interface List1<E extends Object> {...}   // E può essere qualunque tipo (Object è il limite di default)
interface List2<E extends Number> {...}   // E deve essere Number o un suo sottotipo

List1<Date>   // OK: Date è sottotipo di Object
List2<Date>   // ERRORE di compilazione: Date non è sottotipo di Number
```

## Wildcard (?)
Le wildcard rappresentano un tipo sconosciuto quando serve usare quel tipo una sola volta, e non in teressa darli un nome. 
- ? -> tipo sconosciuto qualsiasi
- ? extends Type -> un sottotipo non specificato di Type
- ? super Type -> un supertipo non specificato di Type

`List<?>` e `List<Object>` possono sembrare simili ma sono molto diversi
- `List<?>` può essere istanziato con qualunque tipo concreto
- `List<Object>` è molto più restrittivo, non posso passare una List di string dove è richiesta una List Object. 
## Type Erasure
Alla fine della compilazione, tutta l'informazione sui tipi generici viene rimossa e sostituita da Object. questo processo si chiama type erasure. 

Questo garantisce compatibilità con versioni precedenti di Java. 
A runtime tutte le istanziazioni di una classe generica hanno lo stesso identico tipo. 
Il compilatore usa i generici solo per un controllo statico più accurato durante la compilazione, poi scarta questa informazione. 

Cast non controllati: la JVM in runtime può verificare solo che lg sia una List, non che sia specificatamente una ListString. Se il cast fosse concettualmente sbagliato, l'errore di eccezione emergerebbe solo più avanti. 

## Java Collection Framework
Java mette a disposizione il JCF che fornisce tante strutture dati diverse, ciascuna pensata per esigenze di verse. 
Il JCF è organizzato come una gerarchia di interfacce astratte e di classi concrete. 
Le interfacce principali derivano tutte direttamente o indirettamente da `Iterable<E>`, tra queste Collection, List, Queue, Deque, Set e Sorted Set. 
`Map<K, T>` invece è un'associazione chiave-valore e non estende Collection, da lei deriva la Sorted Map. 
Le interfacce sono astratte, per usarle servono le classi concrete che le implementino. 
- `ArrayList<E> e Vector<E>` implementano List con un array dinamico di supporto. Accesso rapito tramite indice. La differenza tra le due è nella gestione in contesti multi-thread in quanto vector è sincronizzato. 
- `LinkedList<E>` implementa List con una lista doppiamente concatenata composta da nodi con campi prev item e next. 
- ``TreeSet<E>`` implementa Set mantenendo gli elementi in ordine crescente basandosi su un albero di ricerca bilanciato
- ecc...
- ![[Pasted image 20260912174342.png]]

## Itereazione astratta e interfaccia `Iterable<E>`
L'iteratore serve per scorrere elementi senza saperne la loro lunghezza.
Interfaccia implementata da tutte le collezioni del JCF, espone il metodo ``iterator()`` che restituisce un oggetto per scorrere gli elementi. 

## for-each
Il ciclo ``for-each`` permette di scorrere automaticamente tutti gli elementi indipendentemente dal numero di elementi. 
```java
Iterable<E> coll = ...;
for (E elem : coll) System.out.println(elem);

// è equivalente a:
Iterator<E> it = coll.iterator();
while (it.hasNext()) System.out.println(it.next());
```
stesso vale per array. 

Ci sono alcune regole da rispettare quando si usa un iteratore
1) Con chiamate successive a next() si visitano tutti gli elementi una sola volta
2) next() lancia eccezione quando hasNext è false
3) L'ordine in cui gli elementi vengono restituiti dipende dall'implementazione dell'iteratore
4) Se si chiama remove senza aver chiamato next viene lanciata eccezione 
5) Modifiche strutturali alla collezione fuori dall'iteratore invalidano l'iteratore stesso
6) Per modificare una collezione durante l'iterazione, bisogna farlo solo con remove() 
7) Si possono avere più iteratori attivi contemporaneamente
```java
List<Integer> lst = new Vector<Integer>();
lst.add(1);                       // collezione con un solo elemento
Iterator<Integer> it = lst.iterator();
System.out.println(it.next());    // stampa 1
lst.add(4);                       // modifica ESTERNA all'iteratore!
it.next();                        // lancia ConcurrentModificationException
```

## Il problema della gestione della memoria
Ogni programma ha bisogno di memoria per contenere i dati con cui lavora. 
- Static Area: contiene i dati la cui dimensione e contenuto sono già definite in fase di compilazione. Area sicura e stabile spesso protetta dalle scritture accidentali. 
- RunTime stack: contiene i record di attivazione delle funzioni e procedure in esecuzione. Indispensabile nei linguaggi con ricorsione. 
- Heap: area dedicata all'allocazione dinamica della memoria. decisa in esecuzione tramite malloc e new. Serve quando ci sono oggetti di dimensione variabile.
Ci sono 2 filosofie di pensiero quando si gestisce la memoria, specialmente da liberare dell'heap alla fine del programma
- C: deallocazione manuale con free
- Gestione automatica: un componente della macchina virtuale (Garbage Collector) si occupa di individuare ed eliminare la memoria non usata. 

## Problemi nella gestione manuale della memoria
In c il programmatore deve esplicitamente allocare e deallocare la memoria delle variabili dinamiche che crea. Questo porta problemi 
- Memory leak: quando una cella di memoria allocata diventa irraggiungibile perché non deallocata correttamente, ma non è stata liberata con free. Non ho più quello spazio di memoria usabile. Problema specialmente in sistemi con poca memoria disponibile
- DAngling reference: problema opposto, quando un puntatore punta ad una zona che è liberata, e quindi punta a niente. 
## Come funziona un garbage collector
Abbiamo bisogno di introdurre due concetti
- Root set: l'insieme dei riferimenti memorizzati fuori dallo heap. 
- Raggiungibilità: un oggetto nell'heap è raggiungibile se esiste un cammino di puntatori che parte da una radice e arriva fino a quell'oggetto.
Il modello a grafo pensa le celle di memoria come nodi e i puntatori come gli archi. L'insieme delle celle di memoria raggiungibili dai puntatori sono i reachable active data. Tutto ciò che non è raggiungibile è garbage che può essere recuperato dal GC. 
Una cella si divide in 
- live se il suo indirizzo è memorizzato in una radice o in un'altra cella live
- dead quindi garbage. 
Durante l'esecuzione della GC, il programma viene temporaneamente sospeso per poter visitare in modo consistente il grafo. 

Un garbage collector perfetto dovrebbe avere:
- Trasparenza 
- GEneralità: funziona su ogni tipo di programma
- Precisione: individua solo il garbage in modo efficiente
- Nessun overhead: non appesantisce la gestione complessiva della memoria
- Nessuna frammentazione: gestisce lo heap in modo efficiente dal punto di vista dello spazio. 
Non esiste un garbage collector perfett e lo vedremo più avanti

## Gestione dello heap: allocazione e frammentazione
L'heap deve gestire l'allocazione fisica dei blocchi di memoria indipendentemente dal GC usato. 

Esistono 2 modelli di hep
### Heap a blocchi di memoria
- Diviso in blocchi della stessa dimensione
- tutti i blocchi all'inizio sono collegati in una lista libera LL
- L'allocazione si prende uno o più blocchi contigui dalla LL e si rimuove da essa
- Deallocando il blocco torna nella LL
- Abbiamo il rischio di esaurire l'heap se le richieste superano il limite fissato
### Heap a blocchi di dimensione variabile
- All'inizio singolo blocco grande quanto tutto l'heap
- Ad ogni richiesta si cerca un blocco di dimensione adatta nella LL
- First fit: prendo il primo blocco abbastanza grande. Più veloce ma porta a una peggiore occupazione della memoria
- Best fit: prendo il blocco che più si avvicina al mio bisogno. Meno veloce ma migliore occupazione della memoria. 
- Se un blocco scelto è più grande del necessario viene diviso in due, parte usata e parte restituita alla LL
- Alla deallocazione se un blocco libero adiacente esiste, i due vengono fusi
- Per velocizzare la ricerca si possono usare liste libere multiple, suddivise per fascia di dimensione. 
esiste con queste metodologie il problema della frammentazione interna e esterna. 

### Le tre tecniche del garbage collector

1) Reference counting 
2) Tracing che si divide in 
	1) Mark & sweep
	2) Copying collection
3) Generational GC 

## Reference Counting
Per ogni oggetto conto con un contatore numerico quanti puntatori lo puntano, quando arriva a 0 può essere restituito alla LL. Unix li usa. È incrementale, facile da implementare, permette il riuso immediato delle celle e coesiste con la deallocazione esplicita. Se due o più oggetti però si puntano a vicenda, ma nessuno di loro è più raggiungibile, non è possibile fare l'eliminazione. 

## Tracing Mark % Sweep
Prendo periodicamente le radici e visito il grafo seguendo i puntatori. 
1) Mark: parto da ogni radice ed estendo la visita del grafo marcando ogni cella che vedo con un bit. 
2) Sweep: Controllo tutto l'heap ed elimino tutte le celle non marcate. 
Gestisco correttamente le strutture cicliche ma richiede la sospensione del programma. Non va a intervenire sulla frammentazione dell'heap ma non ci sono overhead di spazio. 

## Tracing: Copying collection
Per risolvere la frammentazione, uso l'algoritmo di Cheney. 
Divido l'heap in 2 metà:
- from-space: parte attiva per le allocazioni
- to-space: parte inattiva che riceve una copia dei dati vivi durante la GC. 
Allocazione solo nel from-space, attivo il GC e copio tutte le celle vive raggiungibili in modo compatto per evitare frammentazione, e scambi poi i ruoli delle due metà andando a cancellare quella vecchia. 
Duplica lo spazio heap e funziona bene solo con tanta memoria disponible, però leva il problema della frammentazione ed è efficace per porzioni di dimensioni differenti. 

## Generational Garbage Collection
Spesso gli oggetti che diventano garbage lo diventano poco dopo essere stati creati. 
L'idea è dividere un heap in generazioni in base a quanto a lungo un oggetto è sopravvissuto
- Young generation: area piccola dove ci sono io nuovi oggetti, controllata molto spesso
- Old generation: area grande dove vengono spostati gli oggetti che sopravvivono a x cicli di GC. Viene controllata meno frequentemente

La procedura tipica è che si copiano i blocchi vivi trovati in Young dentro Old, poi si ripulisce completamente Young, e si continua ad allocare nuovi oggetti in Young. 
Per pulire Young si può usare qualsiasi tecnica vista prima (tipicamente mark & sweep). È possibile avere più di 2 generazioni, con uno schema a cascata. 

## Linguaggi reali e GC
Java di default usa l'algoritmo con tre generazioni (0, 1, 2)
- Nelle generazioni 0 e 1 si applica copy collection
- Nella generazione 2 si applica mark & sweep con meccanismi di anti-frammentazione
- La JVM offre anche 4 GC alternativi selezionabili a riga di comando (multithreading e bassa latenza). 
Python combina reference counting con tracing per indivduare e risolvere casi complessi dei riferimetni circolari . 
C# ha una GC integrata simile a Java. 
C/c++ Non hanno GC automatica e la memoria va gestita manualmente. 

