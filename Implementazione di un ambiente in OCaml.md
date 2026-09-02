PREMESSA: ambiente minimale ovvero 
- senza blocchi
- senza record di attivazione
- solo una pila di binding

## Ambiente (semantica)
Un ambiente $\Sigma$ è una collezione di binding
- Astrattamente un ambiente è una funzione di tipo $$\Sigma: Ide \rightarrow Value + Unbound$$
- L'uso della costante Unbound permette di rendere la funzione totale
Dato l'ambiente $\Sigma(x)$ denota il valore v associato ad x nell'ambiente o il valore speciale Unbound
$\Sigma[x=v]$ indica l'ambiente esteso così definito
$$\Sigma[x=v](y) =$$
- v se y = x
- $\Sigma(y)$ se $y \neq x$ 

## Implementazione alternativa (funzione polimorfa)
Un ambiente è una funzione che associa ad ogni identificatore un valore di tipo ``
`t` dove t rappresenta i valori possibili nel linguaggio. 
Serve un ambiente vuoto come punto di partenza per le valutazioni. `emtpyenv` è una funzione che prende un identificatore e restituisce il valore speciale UnBound, indicando che ambiente non ha alcun legame iniziale. $$[](x) = Unbound$$
L'operazione di referencing sx rappresenta la ricerca del valore associato a un identificatore x nell'ambiente s
$$\Sigma(x)$$
- s è una funzione
- x è un identificatore
- s x è il valore associato a x
![[Pasted image 20260902170832.png]]

La funzione `bind` aggiorna l'ambiente s associando un identificatore x a un valore v 
$$\Sigma' = \Sigma[x=v]$$
`bind` restituisce una nuova funzione (ambiente) che:
- associa x al valore v
- per ogni altro identificatore $i \neq x$ usa l'ambiente originale s

# MiniCaml
Consideriamo il nucleo di un linguaggio funzionale
- Sottoinsieme di ML, m, senza tipi ne pattern matching. 
L'Obiettivo è esaminare tutti gli aspetti relativi all'implementazione 
- Interprete
- Supporto a run time per il linguaggio

## Espressioni
Ogni costruttore rappresenta un tipo di espressione del linguaggio. Gli argomenti sono sottoespressioni. Questa definizione permette a eval di usare pattern matching per riconoscere e valutare i vari casi. 
```ocaml
type exp =
| EInt of int
| CstTrue
| CstFalse
| Sum of exp * exp
| Diff of exp * exp
| Prod of exp * exp
| Div of exp * exp
| Eq of exp * exp
| IsZero of exp
| Or of exp * exp
| And of exp * exp
| Not of exp
| Ifthenelse of exp * exp * exp
```
![[Pasted image 20260902171246.png]]

## Ciclo interprete
```ocaml
let rec eval (e: exp) =
match e with
| EInt(n) -> Int(n)
| CstTrue -> Bool(true)
| CstFalse -> Bool(false)
| IsZero(e) -> ???
| Den(i) -> ???
```

## Valori esprimibili 
Valori che possono essere calcolati e restituiti durante la valutazione. 
Per ora costanti intere o booleane. 
```ocaml
type evT = Int of int 
| Bool of bool
| Unbound
```
```ocaml
type tname =
| Tint
| TBool
...
```


## Ambiente 
Associazione `ide evT env`. L'ambiente è una funzione che dato un nome restituisce il valore esprimibile associato a quel nome. 
Nell'ambiente di un interprete i valori devono avere anche l'informazione sul tipo per consentire il type checking dinamico (tname descritto in precedenza).

## Determinare il tipo 
La funzione `getType` prende un valore x di tipo `evT` e restituisce un valore di tipo tname che rappresenta il tipo. Restituisce il descrittore di un valore esprimibile `evT` di `x`
```ocaml
let getType (x: evT) : tname = 
	match x with
	| Int(n) -> TInt
	| Bool(b) -> TBool
	...
```

## Typechecking dinamico
Quando un'operazione primitiva riceve dei valori, non può sapere a priori se sono del tipo corretto. 
Si chiama quindi la funzione typecheck che prende una coppia (type, typeDescriptor) in cui
- type è un valore di tipo tname (tipo atteso del valore per l'operazione)
- typeDescriptor è un valore di tipo evT da verificare
- controlla se il valore è del tipo atteso
```ocaml
let typecheck (type, typeDescriptor) = 
	match type with
	| TInt ->
		(match typeDescriptor with
		| Int(u) -> true
		| _ -> false)
	| TBool -> 
		(match typeDescriptor with
		| Bool(u) -> true
		| _ -> false)
	| _ -> failwith("not a valid type")
```
Questa funzione fa il controllo su bool e int

## Operazioni di base
Controllo di variabile se è 0
```ocaml
let is_zero x = match (typecheck(TInt, x), x) with
| (true, Int(y)) -> Bool(y=0)
| (_,_) -> failwith("run-time error");;
```

Controllo se 2 numeri sono uguali
```ocaml
let int_eq(x,y) = 
	match (typecheck(TInt, x), typecheck(TInt, y), x, y) with
	| (true, true, int(v), Int(w)) -> Bool(v = w)
	| (_, _, _, _) -> failwith("run-time error")
```

Somma di 2 numeri
```ocaml
let int_plus(x, y) = 
	match (typecheck(TInt, x), typecheck(TInt, y), x, y) with
	| (true, true, int(v), int(w)) -> Bool(v = w)
	| (_, _, _, _) -> failwith("run-time error")
```

## Ciclo interprete aggiornato
Aggiungo ora le operazioni appena spiegate e altre
```ocaml
let rec eval (e:exp) (s: evT env) : evT =
match e with
| EInt(n) -> Int(n)
| CstTrue -> Bool(true)
| CstFalse -> Bool(false)
| IsZero(e) -> is_zero(eval e s)
| Eq(e1, e2) -> int_eq((eval e1 s), (eval e2 s))
| Sum(e1, e2) -> int_plus ((eval e1 s), (eval e2 s))
| Diff(e1, e2) -> int_sub ((eval e1 s), (eval e2 s))
| Prod(e1,e2) -> int_times((eval e1 s), (eval e2 s))
| Div(e1,e2) -> int_div((eval e1 s), (eval e2 s))
| And(e1, e2) -> bool_and((eval e1 s), (eval e2 s))
| Or(e1, e2) -> bool_or ((eval e1 s), (eval e2 s))
| Not(e) -> bool_not((eval e s))
```

## Condizionale, regole operazionali 
La sintassi astratta del condizionale è `IfThenElse of exp*exp*exp`![[Pasted image 20260902180633.png]]

```ocaml
...
| IfThenElse (cond, e1, e2) -> 
	let g = eval cond s in
		match (typecheck(Tbool, g), g) with
		| (true, Bool(true)) -> eval e1 s
		| (false, Bool(false)) -> eval e2 s
		| (_, _) -> failwith ("non boolean guard")
```

## Binding identificatori
La regola semantica è $$\Sigma \rightarrow Den(i) \rightarrow \Sigma(i)$$
Den(i) valuta un identificatore cercandone il valore nell'ambiente s
```ocaml
let rec eval (e:exp) (s: evT env) : evT = 
	match e with
	| ...
	| Den(i) -> (s i)
```
Esempio: 
L'espressione `x+1` diventa `Sum(Den("x"), EInt(1))`

## Blocco: Let(i, e, e_body)

Con `let` possiamo cambiare l'ambiente in punti arbitrari all'interno di una espressione. 
- L'ambiente nuovo vale soltanto durante la valutazione del corpo del blocco (e_body)
- lo stesso nome può denotare entità distinte in blocchi diversi
I blocchi possono essere annidati
- l'ambiente locale di un blocco più esterno può essere visibile e utilizzabile nel blocco più interno
Il blocco
- porta naturalmente a una gestione dinamica della memoria lcoale semplice (stack dei record)
- si sposa con la regola dello scoping statico
$$ Let \ of \ ide * exp * exp$$
![[Pasted image 20260902182935.png]]
- Si valuta e nell'ambiente corrente $\Sigma$ ottenendo v
- Si estende $\Sigma$ aggiungendo l'associazione i = v 
- Si valuta e_body nel nuovo ambiente 
- il risultato è v'

## Regola dell'interprete
```ocaml
let rec eval (e:exp) (s: evT env) : evT =
	match e with
	| …
	| Let(i, e, ebody) -> 
		eval ebody(bind s i (eval e s))
```
- Si valuta e nell'ambiente corrente s ottenendo val
- si estende s con l'associazione i = val ottenendo s'
- si valuta l'espressione ebody nell'ambiente esteso s'

## Semantica operazionale del blocco
Il let si implementa
- aggiungendo un nuovo record di attivazione sulla pila degli ambienti
- rimuovendo il record alla fine della valutazione di e_body
## Funzioni
- Astrazione funzionale (dichiarazione)
	- Fun of ide * exp
- Applicazione di funzione (chiamata)
	- Apply of exp * exp

## Astrazione di funzione 
Funzioni anonime 
- `Fun( arg, ebody)`
- `arg` parametro formale
- `ebody` corpo della funzione
Usiamo come esempio il seguente
```ocaml
let f x = x+7 in f 2 

diventa

Let("f", Fun("x", Sum(Den("x"), EInt(7))), Apply(Den("f"), EInt(2)))
```

Per semplicità assumiamo che l'applicazione funzionale sia del primo ordine (funzioni non possono essere passate o restituite). 
- Il primo argomento dell'applicazione funzionale deve essere il nome della funzione da invocare
- `Apply(eF, eArg)` deve avere la forma `Apply(Den("f"), eArg)`
- Non consideriamo al momento funzioni ricorsive

La funzione deve essere stata dichiarata all'interno di un Let (in quando il corpo è un ambiente a parte). 
```ocaml
Let("f", Fun(""x", Sum(Den("x"), EInt(7))), Apply(Den("f"), EInt(2)));;
```
Quindi usando la sintassi astratta delle funzioni, possiamo dire che ide diventa `Apply of Den("f")` trasformando la sintassi come segue
![[Pasted image 20260902192225.png]]