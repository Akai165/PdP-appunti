## Cambiamenti dell'ambiente
L'ambiente subisce variazioni anche durante l'esecuzione del programma. 
- Entrando in un blocco: si creano le associazioni tra i nomi locali e gli oggetti corrispondenti. 
- Uscendo dal blocco: si distruggono le associazioni locali e si riattivano quelle nascoste
Il tempo di vita degli oggetti non coincide necessariamente con il tempo di vita della singola associazione. 
## Ambiente di un interprete
L'ambiente di un interprete è ciò che permette al linguaggio di capire a cosa si riferiscono i nomi usati nel programma. 
In ogni momento tiene l'associazione tra nomi ed entità a cui sono legati (variabili, funzioni, tipi, oggetti). 
## Entità denotabili 
Sono elementi del linguaggio a cui si può assegnare un nome. 
- Entità i cui nomi sono definiti dal linguaggio di programmazione
- Entità i cui nomi sono definiti dall'utente. 
## Binding e Scope
- un binding è un legame (associazione) tra un nome e un'entità del linguaggio (funzione, oggetto ecc)
- Lo scope di un binding definisce quale parte del programma quel binding è attivo. 
## Binding Statico e Dinamico
- Binding Statico. legami nome-entità sono definiti prima dell'esecuzione del programma (lo scope è determinato lessicalmente, ovvero nella posizione del nome nel testo del programma) 
- Binding Dinamico: i legami nome-entità sono definiti durante l'esecuzione del programma (lo scope dipende dallo stato del programma in quel momento)

L'ambiente è definito come l'insieme dei binding nome-entità esistenti a run-time in uno specifico punto del programma. 
Nella macchina astratta del linguaggio l'ambiente ha il compito di determinare per ogni nome e per ogni sezione l'associazione corretta. 

## Blocchi
Un blocco è una regione testuale del programma che può contenere dichiarazioni (esempio le parentesi graffe)
Un blocco può essere
- associato ad una funzione (corpo della funzione)
- in-line: raggruppamento dei comandi (per esempio ciclo) o limitare la visibilità delle variabili. 
## Tipi di ambiente
- Locale: associazioni dichiarate localmente al blocco
- Non Locale: associazione dei nomi che sono visibili all'interno del blocco ma non dichiarati nel blocco
- Globale: associazione di nomi visibili e usati da tutte le componenti del programma
## Scope
Lo scope di un binding definisce quella parte del programma nella quale il binding è attivo 
- Statico o Lessicale: determinato dalla struttura sintattica del programma
- Dinamico: determinato dalla struttura a tempo di esecuzione

## Realizzazione dell'ambiente come stack nel RTS
## Stack
E' una struttura dati usata dal RTS per implementare le funzionalità legate all'ambiente. 
E' una pila di record di attivazione, uno per ogni blocco o funzione attiva. 
Ogni record di attivazione rappresenta l'ambiente locale del blocco corrispondente e contiene le informazioni necessaire per riprendere l'esecuzione al termine del blocco. 
Lo stack serve a gestire gli ambienti locali associati alle chiamate di funzione. 

Per blocchi associati a funzioni il record di attivazione contiene: 
- Variabili locali del blocco
- Parametri della funzione 
- Link al record di attivazione del chiamante
- Link al record di attivazione del blocco più esterno (sintatticamente)
- Punto nel codice del chiamante in cui tornare al termine della chiamata

Per i blocchi in-line non associati a funzioni, contiene: 
- La struttura più semplice (variabili locali)
- Si impilano sul record di attivazione della funzione che li contiene 
