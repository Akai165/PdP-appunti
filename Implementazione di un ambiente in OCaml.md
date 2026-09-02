PREMESSA: ambiente minimale ovvero 
- senza blocchi
- senza record di attivazione
- solo una pila di binding

## Ambiente (semantica)
Un ambiente $\Sigma$ è una collezione di binding
- Astrattamente un ambiente è una funzione di tipo $$\Sigma: Ide \rightarrow Value + Unbound$$
- L'uso della costante Unbound permette di rendere la funzione totale
- Dato l'ambiente $\Sigma$, 