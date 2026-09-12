# Oggetti, classi e tipi oggetto

- Un oggetto in OCaml è un valore, costituito da campi e metodi. 
- Gli oggetti possono essere creati direttamente senza specificare una classe
- Il tipo di un oggetto è determinato dai metodi
```ocaml
let s = object
	val mutable v = [0; 2]
	
	method pop = 
		match v with
		| hd :: tl ->
			v <- tl;
			Some hd
		| [] -> None
		
		method push hd = 
			v <- hd :: v
end;;
```

## Type weakening

Se dentro un oggetto inseriamo una lista vuota, bisogna andare a capire come si comporterà il tipo di quella lista. Al momento di dichiarazione verrà applicata una lista di tipo concreto t, quindi un abbassamento temporaneo al tipo di lista. Alla prima modifica o aggiunta di un oggetto alla iista, essa andrà a prendere un nuovo tipo istanziandolo definitivamente. 

## Costruzione di oggetti tramite funzioni

Gli oggetti possono essere costruiti tramite funzioni
```ocaml
let stack init = object
	val mutable v init 
	
	method pop = match v with 
	| hd :: tl -> 
		v <- tl;
		Some hd
	| [] -> None
	
	method push hd = 
		v <- hd :: v
end ;;
```

Quando si definisce una funzione che prende un oggetto come parametro, il tipo dell'oggetto viene inferito dai metodi che la funzione chiama su di esso. 

## Polimorfismo di oggetti: structural subtyping
La notazione con i puntini `< width : int, ...> ` usata dall'interprete OCaml nell'inferire il tipo del parametro formale di `area` enfatizza lo structural subtyping. 
- La funzione accetta un qualunque sottotipo di width : int, ossia qualunque oggetto che contenga almeno il metodo width. 
- Rappresenta una nuova forma di polimorfismo che consente di tratare diversi oggetti con la stessa funzione, purché abbiano i metodi richiesti. 
- I puntini possono essere considerati come una variabile di tipo, che si può istanziare con una lista di altri metodi potenzialmente da aggiungere a width.
## Coerction di tipi oggetto :>
Serve una type coerction (conversione di tipo) esplicita per inserire oggetti in oggetti generali. Forza il type checker a trattare l'espressione inserita come se fosse del tipo di dove la voglio mettere. Questo tipo dovrà essere un supertipo, con metodi in meno. 

## Polimorfismo di oggetti vs principio di sostituzione
- Polimorfismo: una funzione che prende oggetti con almeno i metodi richiesti
- Sostituzione: un oggetto di un tipo più specifico si può usare ovunque serva un oggetto di un tipo più generale (studente vs persona)
Sebbene tra loro collegati, in OCaml vengono trattati in due modi diversi. 
Il primo è supportato direttamente tramite subtyping strutturale, il secondo è richiesto la type coercion esplicita. 
Questo perché il type checker di OCaml, non effettua conversioni implicite. 

## OCaml vs Java
In Java è possibile effettuare delle coercizioni di tipo da un supertipo a un sottotipo. 
Questo è possibile tramite i controlli dinamici di tipo eseguiti dalla JVM a run time, usando nominal subtyping

## Classi
OCaml consente di lavorare direttamente con gli oggetti, tuttavia i meccanismi di forza della programmazione OO derivano dall'ereditarietà. 
OCaml per questo introduce anche dei costrutti di classe. 
```ocaml
class istack = object 
	val mutable v = [0; 2]
	method pop =
		match v with
		| hd::tl ->
			v<-t t1;
			Some hd
		| [] -> None
	method push hd = 
		v <- hd :: v
end ;;
```

## Classi parametrche e polimorfe
Una classe può prevedere parametri di 
- Costruzione: (init) da passare nel momento dell'istanziazione
- tipo: che la rendono polimorfa
```ocaml
class [‘ ] stack init = object (* classe polimorfa per stack *)
	val mutable v : ‘ init (* init è parametro costruttore *)
	method pop =
		match v with
			| hd :: tl ->
			v <- tl;
			Some hd
		| [] -> None
	method push hd =
		v <- hd :: v
end ;;
```

## Classi e tipi oggetto
La definizione di una classe introduce anche un tipo con lo stesso nome
Si tratta però di un alias del tipo-oggetto che si otterrebbe costruendo gli oggetti direttamente. 