I linguaggi supportano vari modi per la modularizzazione dei programmi. 
## Funzionale
Sotto il profilo linguistico, con l'astrazione procedurale
## Per tipo
Sotto il profilo dei tipi di dato, con i tipi di dato astratti
## Organizzazione compilazione
Sotto il profilo delle tecniche di compilazione ed esecuzione, con la compilazione separata e il linking 

## Livelli di astrazione
La modularizzazione dei programmi consente di sviluppare un programma per livelli di astrazione. 
La libreria standard consente di scrivere programmi che operano su stringhe ignorando come le operazioni su stringhe siano implementate (astrazione).

Tutti i sistemi informatici complessi sono organizzati per astrazione
- Linguaggi di programmazione (codice sorgemte -> bytecode -> assembler)
- Sistemi operativi
- Protocolli di comunicazione su reti
L'astrazione permette di semplificare problemi complessi, focalizzandosi sugli aspetti essenziali. 
Sviluppare un programma in modo modulare consente di suddividere il lavoro. 
Si definiscono specifiche (interfacce). Ogni sviluppatore sviluppa la propria parte seguendo specifiche già create per fare in modo che i programmi poi si uniscano per bene. Si usano strumenti di ingegneria del software per definire specifiche in modo non ambiguo usando strumenti standard come i diagrammi UML. 

# Paradigma Object-Oriented
Sistema software -> insieme di oggetti che cooperano tra loro. 
Gli oggetti sono caratterizzati da:
- Uno stato che rappresenta la struttura interna dello stesso 
- Un insieme di Metodi che definiscono le operazioni che quell'oggetto può fare
## Stato
Lo stato di un oggeto è rappresentato da un insieme di proprietà (o attributi)
**Incapsulamento**: Idealmente lo stato di un oggetto non dovrebbe essere accessibile direttamente agli altri oggetti. 
Lo stato del programma corrisponde solamente all'insieme degli stati degli oggetti che lo compongono. 
Gli oggetti sono responsabili di mantenere il proprio stato interno. e l'interazione tra oggetti modifica lo stato complessivo del programma. 
In aggiunta ci sono strutture dati di sistema necessarie per l'esecuzione del supporto a run-time. 

## Funzionalità 
Le funzionalità di un oggetto sono rappresentate dai suoi metodi/funzioni che mette a disposizione degli altri oggetti come interfaccia di interazione. 
I metodi descrivono il comportamento dell'oggetto (il modo in cui risponde ad un messaggio ricevuto modificando il proprio stato oppure interagendo con altri oggetti per coordinare azioni o scambiare informazioni)
L'interazione tramite messaggi:
- Invio di un messaggio è di solito codificato come chiamata di un metodo
- La risposta ad un messaggio viene codificato come restituzione del risultato. 

## Caratteristiche
Oltre ad avere un ostato delle funzionalità, gli oggetti sono caratterizzati anche da 
- Id
- Ciclo di vita
- Locazione di memoria
Rispetto al paradigma imperativo
- Struttura del programma differente
- modello di esecuzione differente. 

# Concetti dell'OOP
Oltre ad avere una nozione di oggetto, questo paradigma di programmazione introduce altri concetti chiave
- **Incapsulamento** e **Astrazione**
- **Interfaccia** (cosa un oggetto mette a disposizione)
- **Ereditarietà**: come un oggetto acquisisce le funzionalità di un altro oggetto (estensioni ecc)
- **Principio di sostituzione** (quando un oggetto può essere usato al posto di un altro in maniera trasparente e controllata)
- **Polimorfismo**: come un oggetto può elaborare altri oggetti indipendentemente ancche di tipi diversi. 

## Strutture linguistiche per l'OOP
Dal punto di vista dei costrutti linguistici, i linguaggi di programmazione supportano i concetti dell'OOP seguendo 2 approcci
- Object-based (JS, VisualBasic, Self, Lua)
- Class-based (C++, Java, Scala)
- Entrambi (Javascript)
Anche OCaml segue un approccio misto. 

## Object-Based
Gli oggetti vengon otrattati nel linguaggio in modo simile ai record. 
I campi rappresentano i dati interni dell'oggetto e possono essere associati a funzioni. 
Una funzione in un oggetto (metodo) può accedere ai campi dell'oggetto stesso tramite riferimento `this`, che rappresenta l'istanza corrente. 
Alcuni linguaggi come JavaScript consentono di modificare la struttura dell'oggetto dinamicamente (offrendo flessibilità ma potenziali rischi per la robustezza)
ex. Js
```Javascript
let mario = {
	nome: "Mario", 
	cognome: "Rossi", 
	eta: 35,
	compleanno: function() {
		this.eta +=1;
		}
}
console.log(mario.nome); // Mario
console.log(mario.cognome) // Rossi
mario.compleanno();
console.log(mario.eta) // 36
```
Possiamo anche modificare dinamicamente gli oggetti
```javascript
mario.nomeCompleto = function() {
	return this.nome + " " this.cognome;
}
console.log(mario.nomeCompleto()); // Mario Rossi
```
Possiamo anche definire una funzione costruttore
```javascript
function Persona(n, c, e) {
	this.nome = n; this.cognome = c; this.eta = e;
	this.compleanno = function() { this.eta++; }
	this.nomeCompleto = function() {
		return this.nome + " " + this.cognome;
	}
}
anna = new Persona("Anna","Rossi",33) ;
console.log(anna.nomeCompleto()); // Anna Rossi
anna.compleanno();
console.log(anna.eta); // 34
```

## Class-Based
Un linguaggio class-based è un linguaggio dove il concetto di classe è fondamentale e viene supportato da specifici costrutti linguistici. 
Una classe definisce la struttura e il comportamento di un insieme di oggetti di un determinato tipo, specificando
- Variabili che rappresentano lo stato
- Metodi che definiscono le funzionalità e il comportamento dell'oggetto
Gli oggetti vengono creati come istanze di una certa classe, con il proprio stato e con accesso a metodi definiti dalla classe. 
ex Javascript 
```javascript
class PErsona {
	constructor(n, c, e) {
		this.nome=n; this.cognome=c; this.eta = e;
	}
	compleanno() { this.eta++; }
	nomeCompleto() {
		return this.nome = " " + this.cognome;
	}
}
rosa = new Persona("Rosa","Bianchi",25) ;
console.log(rosa.nomeCompleto()); // Rosa Bianchi
rosa.compleanno();
console.log(rosa.eta); // 26
```

## Object-based vs Class-based
#### object-based
- Consente al programmatore di lavorare con oggetti in modo flessibile
	- oggetti creati direttamente senza scrivere codice di classe
	- è possibile creare tante varianti di uno stesso tipo di oggetto senza dover scrivere tante classi diverse
- Permette di modificare la struttura dell'oggetto a tempo di esecuzione
- Rende difficile predire con precisione quello che sarà il tipo di un oggetto data l'assenza di classi predefinite
#### class-based
- Richiede al programmatore maggiore disciplina
	- deve definire ed implementare classi prima di poter creare oggetti. Classi predefinite con relazioni tra classi chiare e strutturate
- Consente di fare controlli di tipo statici sugli oggetti in quanto l'oggetto sarà determinato sin dall'inizio. 

## Ereditarietà
La scelta tra object-based e class-based ha un impatto significativo sui meccanismi di ereditarietà e tipatura del linguaggio
- prototype-based inheritance vs class-based inherictance 
- structural (sub)typing vs nominal (sub)typing

L'ereditarietà è una funzionalità realizzata tramite appositi costrutti linguistici. I linguaggi object-based per ogni oggetto mantengono una lista dei prototipi che sono tutti gli oggetti da cui esso eredita proprietà e metodi. 
La catena dei prototipi consente di creare strutture dinamiche dove un oggetto può estendersi e modificare le funzionalità ereditate senza dover definire una nuova classe. 

```javascript
// Costruttore di Studente che eredita da Persona
function Studente(m, n, c, e) {
	this.matricola = m;
	this.__proto__ = new Persona (n,c,e); // prototipo
}
let luigi = new Studente ("1231","Luigi","Verdi",22);
console.log(luigi.matricola); // 1231
console.log(luigi.nomeCompleto()); // Luigi Verdi
```

## Prototipi 
La gestione dei prototipi nei programmi diventa complicata perché ogni oggetto può avere un prototipo che a sua volta può avere il proprio prototipo ecc... Creando una catena di ereditarietà. risalire per capire da dove proviene una proprietà può essere complicato. 

I linguaggi class-based consentono di definire una classe come estensione di un'altra. 
La nuova classe eredita automaticamente tutti i membri della precedente , con la possibilità di aggiungerne altri o ridefinirne alcuni, tramite l'overriding. 
```javascript
// Classe Studente che estende la classe Persona
class Studente extends Persona {
	constructor(m,n,c,e) {
		super (n,c,e); // Studente acquisisce i campi di Persona
		this.matricola = m; // NO lista prototipi
	}
}
let giada = new Studente("7212","Giada","Neri",21);
console.log(giada.matricola); // 7212
console.log(giada.nomeCompleto()); // Giada Neri
```

## Inheritance e subtyping 
I meccanismi di inheritance induciono nozioni di sottotipo tra oggetti

Idealmente un oggetto B che è estensione di un altro oggetto A dovrebbe poter essere usato dovunque si possa usare A. 

## Structural subtyping
I linguaggi object-based solitamente usano subtyping-strutturale
Un oggetto B è sottotipo di A se contiene almeno tutti i membri pubblici che sono presenti in A. 

## Nominal subtyping
I linguaggi class-based usano una nozione si subtyping-nominale
Il tipo di un oggetto è determinato dalla classe da cui è stato istanziato. 
Il nome della classe diventa il nome del tipo.
Un tipo-classe B è sottotipo di un tipo-classe A se la classe B è stata definita esplicitamente come estensione di A. 

## structural vs nominal
#### structural
- maggior flessibilità
- supporto al polimorfismo 

#### nominal
- Maggior rigore
- Maggior semplicità di verifica per l'interprete