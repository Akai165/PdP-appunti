## Ciclo di interpretazione 
Esegue operazioni elementari in sequenza
Le fasi di *scanning/parsing* possono essere svolte a livello di 
- intero programma
- singole istruzioni/espressioni

Anche qua valgono i controlli di tipo 
- analisi statica (intero programma)
- controlli dinamici (esecuzione per ogni istruzione)
- analisi statica + controlli dinamici

Gli esempi vanno in base al caso. 
Javascript prima controlla la sintassi generale ed esegue solo se tutto è corretto, OCaml invece va espressione per espressione. 


## Interprete di espressioni aritmetiche
Sintassi delle operazioni aritmetiche
```
Exp ::= n | Exp op Exp | (Exp)
op ::= + | - | * | / |
```
Quindi possiamo definire dei tipi algebrici per identificare la sintassi 
```ocaml 
type op = Add | Sub | Mul | Div ;;
type exp = 
	| Val of int
	| Op of op*exp*exp
```
Scriviamo una funzione di utilità che trasforma l'astratto in testo
```ocaml 
let rec to_string e =
	let symbol o = 
		match o with | Add -> "+" | Sub -> "-" | Mul -> "*" | Div -> "/"
	in 
	match e with 
	| Val n -> string_of_int n
	| Op (o, e1, e2) -> "(" ^ (to_string e1) ^ (symbol o) ^ (to_string e2) ^ ")" ;; 
```

## Lo Scanner (tokenizzatore)
```ocaml
type token = 
	| Tkn_NUM of int
	| Tkn_OP of string
	| Tkn_LPAR 
	| Tkn_RPAR
	| Tkn_END;;
```
Lo scanner trasforma: 
- la rappresentazione testuale della stringa
- in una lista di token
nell'eseguire questa trasformazione los canner controlla che nell'espressione non siano stati usati simboli non previsti 

Lo scanner non effettua un controllo grammaticale. `)(3++(88` risulta come corretto

## Scanner (funzione `tokenize`)
Scandisce ricorsivamente la stringa un carattere per volta. 
Se trova più simboli numerici li aggrega in un token numero unico (`Tkn_NUM 231`).
Solleva l'eccezione `ParseError`
```ocaml
exception ParseError of string*string;;
```
```ocaml
let tokenize s = 
	let rec tokenize_rec s pos = 
		if pos=String.length s then [Tkn_END]
		else 
			let c = String.sub s pos 1
			in 
			let tokens = tokenize_rec s (pos+1)
			in 
				match c with 
				| " " -> tokens
				| "(" -> Tkn_LPAR::tokens
				| ")" -> TknRPAR::tokens
				| "+" | "-" | "*" | "/" -> (Tkn_OP c)::tokens
				| "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9" ->
					(match tokens with 
					| Tkn_NUM n::tokens' -> 
						Tkn_NUM (int_of_string (c^(string_of_int n)))::tokens'
					| _ -> Tkn_NUM (int_of_string c)::tokens)
					| _ -> raise (ParseError ("Tokenizer", "unknown symbol:"^c))
		in 
			tokenize_rec s 0;
```
Per ogni carattere di un'espressione lo prendo e faccio la tokenizzazione. Per i numeri a più cifre controllo il numero alla sua destra, se c'é un operazione allora lo salvo come token numero altrimenti lo aggiungo al token numero del numero a destra. 

## Problemi di implementazione dello scanner
La funzione `tokenize_rec` è ricorsiva ma non tail-recursive. 
Problemi con espressioni molto lunghe. 

## Il Parser
Controlla che l'espressione sia sintatticamente corretta (appartenga al linguaggio BNF) e genera l'abstract syntax tree (AST)
Abbiamo bisogno delle seguenti caratteristiche: 
- Grammatica non ambigua
- Se linguaggi semplici, implemento un parser a discesa ricorsiva
- Se linguaggi non semplici, uso un parser generator (software che genera il codice del parser a partire dalla grammatica)

Definisco la grammatica delle espressione in modo non ambiguo
```
Exp ::= Term + Exp | Term - Exp | Term
Term ::= Factor * Term | Factor / Term | Factor
Factor ::= n | (Exp)
```

## Implementazione del parser
Implementiamo un parser a discesa ricorsiva

- una funzione per ogni categoria sintattica (`exp, term, factor`)
- le funzioni sono mutualmente ricorsive
- le funzioni si richiamano l'una con l'altra e consumano token secondo quanto indicato dalla grammatica
- ogni chiamata di funzione restituisce un nodo dell'AST 
- L'AST corrisponde all'albero delle chiamate

```ocaml
let parse s = 
	riferimento tokenizzato dalla funzione scritta prima
	let tokens = ref (tokenize s) in
	
	prendo il primo token senza rimuoverlo
	let lookahed () = match !tokens with
		| [] -> raise (ParseError ("Parser", "lookahed error"))
		| t::_ -> t
	in 
	
	elimino il primo token
	let consume () = match !tokens with
		| [] -> raise (ParseError ("Parser", "consume error"))
		| t::tkns -> tokens := tkns
	in
	
	funzioni mutualmente ricorsive che seguono dalla grammatica
	let rec exp () = 
		let t1 = term() in 
		match lookahed () with
		| Tkn_OP "+" -> consume(); Op (Add, t1, exp())
		| Tkn_OP "-" -> consume(); Op (Sub, t1, exp())
		| _ -> f1
		
	and term () = 
		let f1 = factor() in 
		match lookahead() with 
		| Tkn_OP "*" -> consume(); Op(Mul, f1, term())
		| Tkn_Op "/" -> consume(); Op(Div, f1, term())
		| _ -> f1
		
	and factor () = 
		match lookahed() with
		| Tkn_NUM n -> consume; Val n
		| Tkn_LPAR -> consume(); let e = exp() in 
								(match lookahead() with 
								| Tkn_RPAR -> consume(); e
								| _ -> raise (ParseError ("Parser", "RPAR error"))
								
	in 
		let ast = exp() in 
		controllo che al termine ci sia solo TKNEND
		match lookahead() with 
		| Tkn_END -> ast
		| x -> print_tokenlist !tokens; raise (ParseError ("Parser", "RPAR error"));;
```

## Discussione sui parser
Dobbiamo:
- rendere la grammatica non ambigua (non sempre ovvio)
- con la discesa ricorsiva è facile imbattersi in situazioni di ricorsione infinita
- a volte non è sufficiente leggere un singolo token per capire quale regola grammaticale applicare

## Interprete
Si parte dalla definizione della semantica
#### Structural Operational Semantics SOS
- Semantics è una descrizione del significato del linguaggio. Il comportamento dei programmi scritti in quel linguaggio
- Operational descrive il comportamento dei programmi tramite una relazione di transizione $\rightarrow$ che cattura tutte le operazioni che vengono svolte passo-passo durante l'esecuzione
- Structural la relazione di transizione è definita usando regole di inferenza basate sulla sintassi (ogni costrutto definito dalla grammatica del linguaggio)

#### small-step
- Ogni passo della relazione di transizione si esegue una singola operazione
- Una computazione è una sequenza di passi
- LA COMPUTAZIONE SI SVILUPPA LUNGO LA SEQUENZA DI PASSI
#### big-step
- La relazione di transizione descrive in un solo passo l'intera computazione
- Le singole operazioni sono descritte nell'albero di derivazione di quella transizione
## Semantica delle espressioni
Semantica *small-step*:

$$
n \rightarrow_{ss} n 
\qquad 
\frac{E_1 \rightarrow_{ss} E_1'}{E_1 \, op \, E_2 \rightarrow_{ss} E_1' \, op \, E_2} 
$$

$$
\frac{E_2 \rightarrow_{ss} E_2'}{n \, op \, E_2 \rightarrow_{ss} n \, op \, E_2'} 
\qquad
\frac{n_1 \mbox{op } n_2 = n}{n_1 \, op \, n_2 \rightarrow_{ss} n} 
$$
Semantica *big-step*:

$$
n \rightarrow_{bs} n
\qquad
\frac{E_1 \rightarrow_{bs} n_1 \quad E_2 \rightarrow_{bs} n_2 \quad n_1 \mbox{op } n_2 = n}{E_1 \, op \, E_1 \rightarrow_{bs} n}
$$
Consideriamo la semantica big-step
interprete big-step
```ocaml
let rec eval e = 
	match e with
	| Val n -> Val n
	| Op (op, e1, e2) ->
		match (eval e1, eval e2) with
		| (Val n1, Val n2) -> (match op with 
					| Add -> Val (n1+n2)
					| Sub -> Val (n1-n2)
					| Mul -> Val (n1*n2)
					| Div -> Val (n1/n2)
					)
		| _ -> failwith "Errore impossibile che si verifichi" ;;
```

## Implementazione dell'interprete `eval_ss`
Consideriamo la small-step
```ocaml
let rec eval_ss e = 
	match e with 
	| Val n -> Val n
	| Op (op, e1, e2) -> 
		(match (e1, e2) with 
		| (Val n1, Val n2) -> Val (match op with
									| Add -> n1 + n2 | Sub -> n1 - n2 | Mul -> n1 * n2 | Div -> n1 / n2 
		| (Op (_,_,_),_) -> Op (op,(eval_ss e1), e2)
		| (Val _, Op (_,_,_)) -> Op (op, e1,(eval_ss e2))
		) ;;
```

## Dalla semantica SOS al codice, sistematicamente

- Si usa il pattern matching per considerare i nodi AST
- Ogni tipo di nodo corrisponde a un costrutto sintattico 
- Per ogni caso del pattern matching si identificano le regole della semantica relative ad esso 
- Si verificano le precondiz
## Lambda-calcolo
Definiamo il tipo degli identificatori e dell'AST delle espressioni: 
```ocaml
type id = string
type exp = 
	| Var of id
	| Lam of id * exp
	| App of exp * exp
```

## La semantica del lambda calcolo 
$$
(\lambda x . e_1) e_2 \rightarrow e_1 \{ x := e_2 \}
$$

$$
\frac{e_1 \rightarrow e_1'}{e_1 \, e_2 \rightarrow e_1' \, e_2}
\qquad 
\frac{e_2 \rightarrow e_2'}{e_1 \, e_2 \rightarrow e_1 \, e_2'}
$$

$$
\frac{e \rightarrow e'}{\lambda x . e \rightarrow \lambda x . e'}
$$
## Implementazione della capture-avoiding substitution
$$
\begin{array}{rcl}
x \{ x:= e \} & \equiv & e\\
y \{ x:= e \} & \equiv & y \mbox{ se } y \neq x\\
(e_1 e_2) \{ x:= e \} & \equiv & ( e_1 \{ x:= e \} ) ( e_2 \{ x:= e \} )\\
(\lambda y . e_1) \{ x:= e \} & \equiv & \lambda y . (e_1 \{ x:= e \}) \\
& & \mbox{ se } y \neq x \mbox{ e } y \not\in FV(e)\\
(\lambda y . e_1) \{ x:= e \} & \equiv & \lambda z . ((e_1 \{ y:= z \}) \{ x:= e \})\\
& & \mbox{ se } y \neq x \mbox{ e } y \in FV(e) \mbox{ e } z \mbox{ fresca}
\end{array}
$$
## Implementazione di Fv(e)
$$
\small
FV(x) = x \qquad FV(\lambda x.e) = FV(e) \setminus \{x\}
$$

$$
\small
FV(e_1\,e_2) = FV(e_1)\cup FV(e_2)
$$
```ocaml
let rec fvs e = 
	match e with
	| Var x -> [x]
	| Lam (x,e) -> List.filter (fun y -> x <> y) (fvs e)
	| App (e1, e2) -> (fvs e1) @ (fvs e2)
```

## Generatore di identificatori "freschi"
```ocaml
let newvar = 
	let x = ref 0 in 
	fun() -> 
		let c = !x in 
		incr x;
		"v"^(string_of_int c)
```
newvar è una funzione senza parametri con una variabile modificabile x nella sua chiusura