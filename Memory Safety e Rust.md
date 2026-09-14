Quando si lavora in memoria possono esserci comportamenti pericolosi come un puntatore che punta a una zona non valida ecc...
Un linguaggio di programmazione si dice safe quando impedisce per costituzione questi errori. 
Ci sono 2 proprietà principli da tener conto
1) Memory safety: ogni puntatore quando usato punta sempre a memoria valida. Se è violata il programma potrebbe avere comportamenti scorretti. 
2) Memory containment: ogni pezzo di memoria allocato è sempre raggiungibile dal root-set del programma, altrimenti viene deallocato in qualche modo. 

I linguaggi di programmazione sono divisi in due grandi famiglie rispetto alla gestione della memoria
- Linguaggi a basso livello: per esempio c/c++ offrono astrazioni minime e un controllo fine sui dettagli della memoria. Viene gestita manualmente dal programmatore e non hanno garbage colletion. Ottime prestazioni ma meno sicuri. 
- Linguaggi ad alto livello: Java, OCaml offrono astrazioni ricche, gestione della memoria automatica tramite GC. Il vantaggio è la sicurezza per il programmatore ma si perde il controllo diretto di memoria e un po' di prestazioni (il GC deve lavorare periodicamente)

RUST nasce per superare il compromesso e offrire controllo a basso livello e garanzie di sicurezza tramite regole verificate staticamente dal compilatore. 

Rust è molto popolare e ha come aspetto centrale il garantire type safety anceh in presenza di concorrenza e di gestione manuale della memoria. 
Le caratteristiche principali del linguaggio sono: 
- Ownership: ogni dato allocato ha un solo proprietario
- Lifetime: la validità di un dato nell'heap è determinata e controllata a compilazione tramite lifetime
- Traits: meccanismo simile a mixin, usato come modello per la OOP. 
- Immutabilità di default: le variabili sono immutabili a meno che non vengano dichiarate con mut
- Controllo statico dei tipi: i tipi sono verificati a tempo di compilazione con regole rigorose. 
- Pattern matching come nei programmi della famiglia ML
- Inferenza di tipo: il compilatore determina automaticamente i tipi 
- Generici realizzati con un meccanismo ispirato alel type classes di haskell
- Programmazione funzionale: Rust supporta funzioni come first-class citizen ed è un linguaggio expression-oriented, quasi tutto in Rust è un'espressione che restituisce un valore. 

Rust come altri linguaggi a basso livello distingue due zone di memoria caratteristiche diverse
- Stack contiene dati la cui dimensione e durata sono note già a tempo di compilazione. L'allocazione e la deallocazione su stack avvengono automaticamente. 
- Heap: contiene dati dimensionati dinamicamente, la cui durata non è fissata a priori. L'accesso ai dati sullo heap è leggermente più lento rispetto allo stack perché avviene tramite puntatore e non direttamente. 

Questa distinzione è fondamentale perché l'ownership di Rust si occupa di gestire in modo sicuro i dati allocati sullo heap, che sono i dati più a rischio di memory leak

## Sintassi 
La sintassi di Rust è ispirata a C, un programma è costruito da un insieme di funzioni tra cui una main. 
Una funzione è definita come un blocco di codice associato a un nome, con un proprio ambiente, parametri e un valore restituito (unit ()) se non restituisce nulla. 
```rust
fn nome(parametri) -> tipo_restituito
```
Un blocco di codice è chiuso in parentesi graffe ed è un'espressione. Contien definizioni di variabili e sotto-espressioni. Le espressioni sono separate dal punto e virgola, Rust distingue chiaramente tra espressioni e comandi. 

## Ownership
Rust sa anche chi possiede delle risorse associate a quella variabile. 
1) Ogni valore ha un proprietario 
2) Il proprietario è unico 
3) Quando il proprietario esce dallo scope, il valore viene eliminato
4) Rust chiama automaticamente alla chiusura dei blocchi una funzione speciale chiamata drop, inserita dal compilatore stesso. 
Per esempio
```rust
{
	let mut s = String::from("hello");
	s.push_str(", world");
	println!("{}", s);
}
```
In questo esempio s è l'unico proprietario della stringa allocata su heap. Quando il blocco {} termina, s esce dallo scope e Rust chiama il drop, liberando la memoria senza bisogno di GC. 

Questo garantisce memory safety e assenza di leak, tutto risolto a compilazione. 

Quando un dato sull'heap viene assegnato ad un'altra variabile, la proprietà si trasferisce, questo fenomeno si chiama move. L'assegnamento in Rust è superficiale, viene copiato solo l'indirizzo a cui punta il dato, non il dato stesso. Non viene creata una copia nuova in memoria, ma la nuova variabile eredita l'accesso al dato. La vecchia variabile non è più utilizzabile 
```rust
let x = String::from("hello");
let y = x; // la proprietà di x è trasferita a y

println!("{}, world!", y); // OK: tipo-checking corretto
println!("{}, world!", x); // ERRORE! x non è più valido
```
Stesso principio applicato al passaggio dei parametri di funzione (call by moving). 
```rust
fn main() {
    let s1 = String::from("hello");
    let s2 = id(s1);        // ownership di s1 trasferita al parametro di id
    println!("{}", s2);     // ownership di id(s1) trasferita a s2
    println!("{}", s1);     // ERRORE: s1 non è più valido
}

fn id(s: String) -> String {
    s // l'ownership di s torna al chiamante quando id restituisce il controllo
}
```

## Deep copy con clone()
Se si vogliono ottenere due copie indipendenti dello stesso dato, ciascuna con il proprio proprietario, rust mette a disposizione clone(). 
```rust
let x = String::from("hello");
let y = x.clone();
```
Per i tipi primitivi fa una copia dei bit, visto che non esiste distinzione pratica tra trasferire e copiare. 

## Borrowing e riferimenti
Spesso non serve trasferire la proprietà di un dato ma solo di prestarlo temporaneamente per alcuni calcoli. Per questo esiste l'operatore di borrowing usando &. Si crea un riferimetno esplicito non proprietario
- Riferimento immutabile &T . consente solo l'accesso in lettura al valore, il proprietario originale rimane tale ma non può liberare o modificare la risorsa finché in uso 
- Riferimento mutabile &mut T: consente l'accesso in scrittura. Può esistere un solo riferimento mutabile per volta su un dato. Questa regola previene conflitti di accesso concorrente.

## Dangling reference, Rust li previene
un riferimento pendente è un riferimento che punta a memoria che è già stata liberata. Rust previene questa cosa a compilazione grazie al type checker. 

## Lifetime
è il meccanismo con cui il type checker di Rust evita la creazione di dangling pointer. Si tratta di un parametro di tipo implicito che descrive lo scoper in cui un dato è valido. Se un riferimento r deve puntare ad un dato x, il lifetime di x deve coprire il lifetime di r. Se x esce dallo scope prima che r smetta di essere usato, il compilatore rifiuta il programma. 

## Aliasing, mutation e la regola d'oro di Rust
Molti bug in memoria pericolosi derivano dalla combinazione di 
- Aliasing: più riferimenti e puntatori diversi puntano allo stesso dato 
- Mutation: la modifica di un dato attraverso uno di questi riferimenti che può portare a liberare memoria ancora referenziata altrove. 
Il problema è quando aliasing e mutation avvengono contemporaneamente sullo stesso dato. Rust risolve vietando esplicitmente che le due cose accadano insieme tramite una regola: 
Un riferimento può essere o unico o mutabile, oppure condiviso e immutabile, più copie ma nessuna può modificare il dato. 
