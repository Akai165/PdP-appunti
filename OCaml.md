## Accortezze varie

## Variabili
 Dichiarate con ``var`` , sono tipizzate in automatico come python, non possono iniziare con una lettera maiuscola o carattere speciale, solo `_` o lettera minuscola

## Tipizzazione
Linguaggio fortemente tipizzato, ci sono i seguenti tipi 
- int: interi
- float: numeri con virgola
- bool: booleani `true` `false`
- char: singoli caratteri `'a'`
- string: stringhe racchiuse tra virgolette `"abcd"`
- unit: simile a void, consente l'unico valore `()`

## Operazioni `int` vs `float`
Le operazioni con interi sono identiche a quelle normali, quelle per i float invece hanno bisogno del `.` dopo il carattere dell'operazione. Uguale per i numeri interi in operazioni float
ex. `5. +. 3.2;;`

## Conversioni
devono essere esplicite
- `float_of_int 3;;`
- `int_of_float 3.0;;`

## Operazioni bool
I bool si gestiscono come in tutti i linguaggi di programmazione, i confronti no. 
Per l'uguaglianza si usa `=` e per vedere se 2 espressioni sono diverse si usa `<>`
- `1 = 1 -> true`
- `1 <> 1 -> false`

## Stringhe e char
Il convertire un char a int porterà al suo valore ascii
- `let x = 'a';;`
- `int_of_char a -> 97`
La concatenazione di stringhe si fa con `^`
```OCaml
let x = "hello";;
let r = x ^ "world";; -> "helloworld"
```

## Tuple
Ocaml ha diversi tipi strutturati tra cui le tuple
```OCaml
let t = (10, "Hello", 12.5);; -> val t : int * string * float
```

## Pattern matching 
```OCaml
p = (3.0, 5.0);;
let(x, y) = p;;
x +. y;; -> float = 8.0
```

## Funzioni
Simile a $\lambda$ calcolo
```OCaml
fun x -> x + 1;;
se volessi usare ora x = 10
(fun x -> x +1)10;;
esattamente come in lambda-calcolo
possono avere più parametri ed essere annidate
(fun f -> fun n -> f n + 1)(fun x -> 2 * x) 10 ;; -> 21
(fun x y -> x+y) 3 4 ;; -> 7
```

Si possono anche dichiarare funzioni con `let`
```OCaml
let somma = fun x y -> x + y;;
somma 3 4;; -> int = 7
```

## Scoping 
Una dichiarazione di variabile aggiorna l'ambiente toplevel gestito e diventa usabile da tutte le espressioni successive. 

Il costrutto `let ...` dichiara variabili il cui scope è una sola espressione

`in` serve ad indicare che l'espressione appena dichiarata vive solo per l'espressione successiva, non intaccando lo scope del toplevel

## Funzioni Curryed
Il nome dlela funzione e i parametri sono separati da spazi senza parentesi e virgole
Le funzioni con questa notazione si dicono Curryed
Nulla vieta di usare però anche le parentesi e le vigole. 
La differenza è che le funzioni Curryed seguono Lambda-calcolo, nel momento in cui metto un solo parametro il risultato è una funzione che aspetta altri input per essere eseguita. 
## Flusso d'esecuzione
Al contrario dei linguaggi imperativi che hanno if e while (controlli su variabili dichiarate). 
OCaml e la programmazione funzionale invece hanno solo 
- `if`
- `ricorsione`
Non esistendo le operazioni di assegnamento, le variabili una volta dichiarate sono immutabili, e quindi il `while` non funzionerebbe. 

## If 
L'if è un'espressione semplicissima e simile ad altri linguaggi di programmazione
```OCaml
let x = 23;;
let y = if x<10 then 1 else 0;;
```

## ricorsione
Per definire funzioni ricorsive è necessario usare il costrutto `let rec`
```OCaml
let rec fact n = 
	if n <= 0 then 1 
			else n * fact (n-1);;
```
Diciamo in anticipo al compilatore che la funzione sarà ricorsiva. Senza va in errore. 

#### Fibonacci
```OCaml
let rec fibonacci n = 
	if n = 0 || n = 1 then n
	else fibonacci (n-1) + fibonacci (n-2);;
```

Massimo comune divisore 
```OCaml
let rec mcd n m = 
	if m = 0 then n else mcd m (n mod m);;
```

## Funzioni mutuamente ricorsive (`and`)
Una funzione può richiamare solo funzioni definite precedentemente. Funzioni mutualmente ricorsive vanno definite una dopo l'altra separate da `and`

```OCaml
let rec pari n = 
	match n with 
		| 0 -> true
		| x -> dispari (x-1)
and dispari n = 
	match n with
		| 0 -> false
		| x -> pari (x-1)
```

## Liste 
In OCaml sono un tipo di dato predefinito 
Una sequenza finita e immutabile di valori dello stesso tipo. 
```OCaml
let numeri = [3; 5; -1; 9; 14; 21] ;;
```
Tutti gli elementi della lista devono essere tipizzati uguali e la lista sarà di quel tipo. 
la lista vuota `[]` è di tipo generico. 

Le liste possono avere qualsiasi tipo all'interno a patto che gli elementi siano uguali, anche funzioni. 
E' possibile pure creare liste di liste. 

OCaml inferisce il tipo di una lista unificando i tipi dei suoi elementi. 

Una lista può essere costruita da un'altra usando `::` ("cons")
**Definizione cons`::`** Data una lista l di tipo T list e un elemento e di tipo T, si denota con `e :: l` la lista in cui il primo elemento è `e` seguito dagli elementi in l. 
Si potrebbe dire che è un'inserimento in testa però essendo gli elementi immutabili, `e :: l ` non è altro che un'altra lista contenente e e gli elementi in l. Essendo 2 liste diverse non c'é spreco di memoria (come se in c usassimo i puntatori)

### Concatenazione di liste (`@`) 
Date 2 liste `lis1` e `lis2`, l'operazione `lis1 @ lis2` descrive la loro concatenazione in un'unica lista. 

```ocaml
let lis1 = [1;2;3] ;;
let lis2 = [4;5;6] ;;
let lis3 = lis1 @ lis2 ;;
```


### Modulo `List`
Il modulo `List` delle API OCaml fornisce funzioni per l'elaborazione di liste

#### `length`
Lunghezza della lista
```ocaml
List.length [5;2;1];; -> 3
```

#### `hd tl e rev`
Head e tail dicono rispettivamente testa e coda della lista
```ocaml
List.hd [5;2;1];; -> 5
List.tl [5;2;1];; -> [2; 1]
```
Rev stampa la lista al contrario
```ocaml
List.rev [5;2;1];; -> [1; 2; 5]
```


## Pattern Matching liste
Per accedere agli elementi di una lista è necessaria una destrutturazione. 
![[Pasted image 20260820143351.png]]

Considerando i tipi di base, tuple e liste, la sintassi dei pattern è data da: 
- valori base
- variabili 
- tuple
- liste di lunghezza fissa
- liste con cons
- wildcard `_`
Un valore `v` fa match con un pattern `P` se:
- `P=_`
- `P=v`
- esiste un modo di istanziare le variabili in `P` ottenendo `P'` tale che `P'=v` ![[Pasted image 20260820143728.png]]

Usiamo l'esempio di fibonacci 
```ocaml
let rec fibonacci n = 
	match n with
		| 0 -> 0
		| 1 -> 1
		| _ -> fibonacci (n-1) * fibonacci (n-2)
```
in questo caso la wildcard `_` è stata messa in fondo per indicare tutti gli altri casi. 

E' opportuno che i pattern siano esaustivi. Per qualunque valore ci deve essere almeno un pattern con cui fare match

## forall
Funzione higher-order che testa un predicato su tutti gli elementi della lista
```ocaml
let rec forall p lis =
	match list with
	| [] -> true
	| x::lis' -> if p x then forall p lis'
				else false;;
```

Questo ci può aiutare come supporto per altre funzioni come il test dei positivi, negativi ecc in una lista. 

## filter
Altra operazione frequente (selezionare elementi secondo una condizione)
Restituisco la lista degli elementi che soddisfano l'operazione

```ocaml
let rec filter p lis = 
	match lis with
	| [] -> []
	| x::lis' -> if p x then x::filter p lis' 
				else filter p lis' ;;
```

## map
Serve per applicare un'operazione a tutti gli elementi di una lista

```ocaml
let rec map f lis = 
	match lis with
	| [] -> []
	| x::lis' -> f x::map f lis' ;;
```

## sum_lis
la somma di una lista completa è diversa rispetto al metodo imperativo dove posso ridichiarare tutte le variabili. Qua al posto di creare una variabile da rivalutare ogni volta, mi passo come parametro la somma appena ottenuta

```ocaml
let rec somma lis = 
	match lis with
	| [] -> 0
	| x::lis' -> x + somma lis' ;;	
	
```

## max e min di liste
Scegliamo come caso base il primo elemento e ce lo passiamo come parametro di return tra tutte le chiamate

```ocaml
let min_max lis = 
	let f x (min, max) =
		if x < min then (x, max)
		else if x > max then (min, x)
		else (min, max)
	in 
		match lis with
		| [] -> (0, 0)
		| x::lis' -> fold_right f lis' (x, x);;
```

## fold_left 
Versione migliore del fold_right in quanto minimizza il problema dello stack overflow
```ocaml
let rec fold_left f a lis =
	match lis with
	| [] -> a
	| x::lis' -> fold_left f (f a x) lis';;
```


## Tipi Algebrici
E' possibile definire nuovi tipi usando `type`
```ocaml
type data = int*int*int;;
```
Abbiamo dato un alias per `int*int*int`

```ocaml
let controlla_data (d:data) = 
	match d with
	| gg, mm, aaaa -> gg>0 && gg <= 31
					&& mm>0 && mm<=12
					&& aaaa>1900 && aaaa<2030;;
```
utilizzo del type appena sviluppato 

Le costruzioni di nuovi tipi sono ispirate alle operazioni insiemistiche
Prodotto cartesiano (tuple, record) Unione (somma di insiemi, variant). 
Ex. la tupla 7x4 è $(7, 4) \in N \mathbb{x}N$ 

### Record
simili a tuple ma possiamo dare nome agli elementi
```ocaml
type punto_2d = {x: float, y: float};;
```
e possiamo creare variabili di questo tipo: 
```ocaml
let p = {x = 3.; y= -4.}
```
Un valore record può essere decomposto usando pattern matching
Esempio calcolare il quadrante di un punto
```ocaml
let quadrante {x = x_pos; y = y_pos} = 
	match x_pos >= 0., y_pos>=0. with
	| true, true -> 1
	| false, true -> 2
	| false, false -> 3
	| true, false -> 4
```
e varianti
```ocaml
let quadrante p =
    match p.x>=0.,p.y>=0. with
    | true,true -> 1
    | false,true -> 2
    | false,false -> 3
    | true,false -> 4 ;;
```
```ocaml
let quadrante {x; y} =
    match x>=0.,y>=0. with
    | true,true -> 1
    | false,true -> 2
    | false,false -> 3
    | true,false -> 4 ;;
```
La dot notation è quella principale
ex. distanza tra 2 punti nel piano 
```ocaml
let distanza p1 p2 = 
	sqrt ( (p2.x -. p1.x)**2. +. (p2.y - p1.y)**2.);;
```
Possiamo quando le operazioni diventano verbose usare il with usando un altro costrutto già esistente. 

ex. 
```ocaml
type persona = {
	nome : string;
	cognome : string;
	eta : int;
	indirizzo : string;
	citta : string; 
}

let mario : persona = {
	nome = "Mario";
	cognome = "Rossi";
	eta = 45;
	indirizzo = "Via Roma 10";
	citta = "Pisa";
}

let bianca = {mario with nome = "Bianca"; eta=13}
```

## Record vs oggetti
I record assomigliano ai dizionari di Javascript, ma la differenza rispetto agli oggetti è che i recordo sono immutabili. 
I campi funzione non possono accedere agli altri campi (variabili d'istanza)
ex. 
 ```ocaml
 let obj = { n = 10; update = fun x -> x}
 obj.n = 11;; (*non posso assegnare*)
 ```

I campi funzione non possono accedere agli altri campi del record, come invece negli oggetti
```ocaml
let obj = { n = 10; update = fun x -> n + x} -> (*unbound value n*)
```

## Unione: variant
L'unione di `t1` e `t2` è un nuovo tipo t che include tutti i valori di tipo `t1` e `t2`

Mentre su Typescript si possono unire due tipi direttamente, in Ocaml l'unione prevede di etichettare i valori dei tipi da unire. Ex. per `string` e `int` dobbiamo scegliere 2 etichette (`Num` e `Txt` per esempio) e usarle: 
```ocaml
type numero_testo = 
	| Txt of string
	| Num of int;;
```
queste etichette sono i costruttori. 
Un costruttore deve iniziare con la maiuscola.Usando solo i costruttori si possono definire tipi enumerazione
ex. 
```ocaml
type giorno = Lun | Mar | Mer | Gio | Ven | Sab | Dom ;;

Gio ;; (* - : giorno = Gio*)
```
Un esempio più complesso può essere un int esteso con `NaN` e $+ \infty$ e $- \infty$
```ocaml
type int_ext = 
| Num of int
| NaN
| Plus_inf
| Minus_inf ;;
```

```ocaml
let sum x y =
    match x,y with
    | NaN,_ | _,NaN -> NaN
    | Plus_inf,Minus_inf | Minus_inf,Plus_inf -> NaN
    | Plus_inf,_ | _,Plus_inf -> Plus_inf
    | Minus_inf,_ | _,Minus_inf -> Minus_inf
    | Num n1,Num n2 -> Num (n1+n2) ;;
```

## Opzione e tipi polimorfi
Un tipo variant molto usato e built-in in OCaml è il tipo opzione. 
Consente di specificare un valore che può essere assente. 
Usa i costruttori `Some` e `None`
```ocaml
Some 4;; -> int option = Some 4

None ;; -> a option = None
```
I tipi opzione consentono di definire funzioni che restituiscono `None` in casi particolari
```ocaml 
let rec massimo lis = 
	match lis with 
	| [] -> None
	| x::lis' -> match massimo lis' with 
				| None -> Some x
				| Some max -> if x>max then Some x 
				else Some max ;;
```
I tipi opzione sono predefiniti 
```ocaml
type 'a option = 
	| Some of 'a
	| None ;;
```
## Tipi record e variant insieme
Si possono creare tipi strutturati combinando a piacere record, variant, tuple
```ocaml
type punto_multidimensionale = 
	| DueDim of {x: float; y: float}
	| TreDim of {x: float, y: float, z: float}
	
	
let p1 = DueDim {x=10.; y=10.}
let p2 = TreDim {x=4.; y=5.; z=7.}
```

## Tipi Ricorsivi
Un importante generalizzazione dei tipi unione sono i tipi ricorsivi
Sono variant definiti in termini di se stessi

```ocaml
type lista_di_int = 
	| Nil
	| Elem of int * lista_di_int
let lst = Elem (3 ,Elem (4, Elem (6, Nil)))
```
Ora possiamo definire una funzione che corrisponde all'operatore :: 
```ocaml
let cons x lis = 
	Elem (x, lis)
cons 5 lst
```
E possiamo definire altre operazioni su lista_di_int
```ocaml
let rec somma_lista lis = 
	match lis with
	| Nil -> 0
	| Elem (x, lis') -> x + somma_lista lis' ;;
	
somma_lista ( Elem (3, Elem (4, Elem (6, Nil))))
```
Il tipo `a' list` in effetti è un caso particolare di variant ricorsivo polimorfo
```ocaml
type 'a my_list = 
	| Empty
	| Cons of 'a * 'a my_list
```

## Tipi ricorsivi e Alberi
Tramite questi tipi ricorsivi è semplice definire gli alberi. 
L'idea è quella illustrata nell'esempio precedente. Descriviamo il tipo degli alberi binari conv alori interi `albero_bin`. Andiamo a descrivere le varie tipologie di elementi e andiamo ricorsivamente al tipo `albero_bin`

```ocaml
type albero_bin = 
	| Nodo of int*albero_bin*albero_bin
	| Foglia of int
```
Definiamo ora uno alla volta i valori delle foglie
```ocaml
let n1 = Foglia 4
let n2 = Foglia 6
let n3 = Nodo(2, n1, n2)
let n4 = Foglia 8
let n5 = Nodo(5, n3, n4)
```

Per esempio: 
```ocaml
let rec visita_ant a = 
	match a with
	| Foglia v -> [v]
	| Nodo (v, sx, dx) -> v::((visita_ant sx)@(visita_ant dx))
	
visita_ant n5;;
```

```ocaml
let rec somma_albero a = 
	match a with
	| Foglia v -> v
	| Nodo (v, sx, dx) -> v + somma_albero sx + somma_albero dx
	
somma_albero n5
```

## Tipi ricorsivi e AST
```ocaml
type op = Add | Sub | Mul | Div | Mod
type exp = 
	| Val of int
	| Op of op*exp*exp
	| UMin of exp
```

Esempio per l'operazione $3*7-5$
```ocaml
let exp1 = Op (Sub, (Op (Mul, Val 3, Val 7), Val 5))
```
E in base alla scrittura valgono le parentesi
$3 * (7-5)$
```ocaml
let exp2 = Op (Mul, UMin (Val 3), Op(Sub, Val 7 Val 5)))
```
Ed è possibile farlo anche con i simboli grafici
```ocaml
let symbol o =
    match o with
    | Add -> "+" | Sub -> "-" | Mul -> "*" | Div -> "/" | Mod -> "%" ;;
let rec to_string e =
    match e with
    | Val n -> string_of_int n
    | Op (o,e1,e2) -> "("^ (to_string e1) ^ (symbol o) ^ (to_string e2) ^")"
    | UMin e' -> "(-"^(to_string e')^")" ;;
```

# Programmazione imperativa in OCaml

Ocaml è concepito per la programmazione funzionale ma include tutti i costrutti imperativi come array, record, riferimenti, cicli ed eccezioni
Inoltre sono presenti pure classi e oggetti. 

## Array
GLi array contengono elementi dello stesso tipo, hanno lunghezza fissata e possono essere acceduti tramite indici numerici
```ocaml
let a = [|3;5;2|]

Array.make 10 0;; 
(*[|0;0;0;0;0;0;0;0;0;0|])*)

let n = Array.length a;;
```
Per accedere agli elementi si usa la sintassi `.(i)`
```ocaml
let e = arr.(3)
val e : int = 5
```
In scrittura con la sintassi `<-`
```ocaml
a.(1) <- 6;
a;;
-: int array = [|3; 6; 2|]
```

`<-` è il comando di assegnamento, prima volta che modifichiamo qualcosa. Non è l'unico costrutto di assegnamento. Il tipo dei comandi OCaml è `unit`. Sono espressioni con side effect, simili a void. Descrive le cose che non possono essere valutate a valori. L'unico valore di `unit` è `()`

## Record mutabili 
E' possibile rendere mutabili uno o più campi tramite il modificatore `mutable`
```ocaml
type persona = 
{
	nome: string; 
	cognome: string; 
	mutable eta: int;
}

let mario = {nome="mario"; cognome = "rossi"; eta=30};;
mario.eta <- 31;;

let invecchia p = 
	p.eta <- p.eta + 1
	
invecchia mario
```
Sopra alcunie sempi di riassegnazione sia come operazione che in una funzione

## Refs 
Oltre ad array e record mutabili possiamo anche definire singole variabili mutabili usando `ref`
```ocaml
let x = ref 12 ;; (*int = 12*)
x.contents ;; (* unit = ()*)
x.contents <- 13;; 
!x (*corrisponde a x.contents - int = 13*)
x := 14 (*corrisponde a x.contents <- 14*)
```
L'operazione `!` è detta deferenziazione
con `ref` si  creano dei riferimenti (puntatori) Copiando il riferimento di una nuova variabile, entrambi punteranno alla stessa area di memoria
```ocaml
let x = ref 0
let y = x
y:100
!x

(*
val x : int ref = {contents = 0}
val y : int ref = {contents = 0}
- : unit = ()
- : int = 100
  modificando y anche x cmabia
*)
```

## Sequenze di comandi e cicli 
Una sequenza di comandi si definisce con `;`
```ocaml
let x = ref 0
let y = x
x := !x+1 ; y = !y+1 ; (!x, !y);;)
(*(2, 2*)
```
le espressioni vengono valutate in sequenza. 
Tutte le espressioni precedenti all'ultima dovrebbero avere tipo `unit` e le espressioni causano solo side effects (esempio modificando strutture dati mutabili)
A calcolare veramente è solo l'ultima espressione. 
Sfruttando `print_endline` che stampa una stringa su console, il sequenziamento ; può essere usato per stampare messaggi di debug
```ocaml
let abs x = 
	if x>0 then (print_endline "ramo Then" x)
	else (print_endline "ramo ELSE"; -x)
abs (-5);;
```

## `;` vs `in`
Viene la tentazione di usare `;` per separare dichiarazioni di variabili dal loro uso ma invece dobbiamo usare `in`

## cicli `for`
La sintassi del for è la seguente 
```
for <variable> = <start> to <end> do 
	...
done

oppure 
for <variable> = <start> downto <end> do
	...
done
```
```ocaml 
for i = 1 to 10 do 
	print_endline (string_of_int i)
done;;
```
```
1
2
...
10
```
Esempio con media valori array
```ocaml 
let media arr = 
	let s = ref 0 in 
	for i = 0 to (Array.length arr)-1 do
		s := !s + arr.(i)
	done;
	! s / Array.length arr;;
```

## Cicli `while`
La sintassi è 
```
while <condizione> do 
	..
done
```
```ocaml
let x = ref 10 in 
while !x>10 do 
	print_endline (string_of_int !x) ;
	x := !x/2
done
```
```
10
5
2
1
```
Esempio restituire la posizione del primo valore negativo di un array
```ocaml
let primo_negativo arr = 
	let pos = ref 0 in 
	let trovato = ref false in
	while !pos < Array.length arr && not !trovato do 
		if arr.(pos)<0 then trovato := true
		else pos := !pos + 1
	done;
	if(!trovato) then Some !pos
	else None
	
```

## Cicli
OCaml non incoraggia l'uso di cicli, sintassi non friendly, impossibile interromperli bruscamente e il for non è flessibile (no incredmento di 2 per volta)

Incoraggia fortemente l'uso di un approccio funzionale. Ottimizzazione delle funzioni tail-recursive.

## Eccezioni
Esistono varie eccezioni per esempio per la divisione per 0. Esistono eccezioni predefinite e possiamo anche crearle noi. 
```ocaml
exception Lista_vuota;;
exception Stringa_errata of string;;
```
Le eccezioni si sollevano con `raise`
```ocaml
raise Lista_vuota

raise (Stringa_errata "test");
```
Possiamo usarle per esempio per interrompere una funzione in caso di errore
```ocaml
let minimo lis = 
	let rec minimo_ric m lis' = 
		match lis' with 
		| [] -> m
		| x::lis' -> minimo_ric (if x<m then x else m) lis'
	in
	match lis with
	| [] -> raise Lista_vuota
	| x::lis' -> minimo_ric x lis'
```

E' possibile intercettare e gestire eccezioni con `try ... with`
```
try <espressione> with
| <pat1> -> <esp1>
| <pat2> -> <esp2> 
...
| <patN> -> <espN>
```
Se espressione causa un'eccezione che fa match con il pattern `<patI>` Viene valutata l'espressione `<espI>`