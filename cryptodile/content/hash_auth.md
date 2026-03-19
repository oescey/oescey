# Hash e autenticazione

## Funzioni di hash crittografiche

Una funzione di hash crittografica prende in input un messaggio di lunghezza arbitraria e produce un **digest** di lunghezza fissa.  
A differenza dei cifrari, le funzioni di hash **non sono invertibili** e non utilizzano chiavi.

Le funzioni di hash sono usate per:
- verificare l’integrità dei dati
- memorizzare password
- costruire MAC e firme digitali


### Proprietà di sicurezza

Una funzione di hash è considerata sicura se soddisfa le seguenti proprietà:

- **Preimage resistance** (Resistenza alla preimage)  
  Dato un valore di hash `h`, è computazionalmente difficile trovare un messaggio `m` tale che `hash(m) = h`.  
  Questo garantisce che la funzione di hash sia a senso unico (_one way_).

- **Second preimage resistance** (Resistenza alla seconda preimage)  
  Dato un messaggio `m1`, è difficile trovare un messaggio diverso `m2` tale che `hash(m1) = hash(m2)`.

- **Collision resistance** (Resistenza alle collisioni)  
  È difficile trovare due messaggi distinti che producano lo stesso hash, ma le possibilità non sono 0.  
  Questa proprietà è fondamentale per evitare attacchi su firme digitali e MAC.

**Dipendenze tra le proprietà**: Se un attacco di preimage è conosciuto, allora anche gli attacchi di second preimage e collision sono possibili. Tuttavia, un attacco di collision non implica necessariamente un attacco di preimage.

Nonostante una funzione di hash sia teoricamente non invertibile, per quanto perfetta, poiché l'output ha lunghezza fissa, esisteranno infiniti input che generano lo stesso output (_pigeonhole principle_). L'obiettivo è rendere il calcolo di questi attacchi sufficientemente difficile con la potenza computazionale disponibile.


## Perché cifrare non basta

La cifratura garantisce la confidenzialità, ovvero impedisce a un attaccante di leggere il contenuto di un messaggio.  
Tuttavia, la cifratura non garantisce l’integrità dei dati.

Un attaccante infatti può:
- intercettare un ciphertext
- modificarlo
- inviarlo al destinatario senza essere rilevato

In molte modalità di cifratura (CBC, CTR), il plaintext decifrato dipende direttamente dal ciphertext. Questo rende possibili attacchi di **modifica controllata**.


## Message Authentication Code (MAC)

Un **Message Authentication Code (MAC)** è un valore calcolato su un messaggio utilizzando una **chiave segreta condivisa**.

Un MAC permette di verificare che:
- il messaggio non sia stato modificato
- il messaggio provenga da qualcuno che conosce la chiave

A differenza delle funzioni di hash, un MAC **dipende da una chiave**.



### Perché serve una chiave

Senza una chiave segreta, chiunque potrebbe generare un valore di controllo valido.  
La chiave garantisce che solo i partecipanti legittimi possano autenticare i messaggi.



### HMAC (struttura generale)

**HMAC** è una costruzione standard che combina:
- una funzione di hash crittografica
- una chiave segreta

HMAC è progettato per essere sicuro anche se la funzione di hash sottostante presenta debolezze parziali.


## Combinare cifratura e autenticazione

Per ottenere un sistema sicuro è necessario combinare **confidenzialità e integrità**.

### Encrypt-then-MAC

1. Il messaggio viene cifrato.
2. Si calcola un MAC sul ciphertext.

Questo approccio:
- impedisce la modifica del ciphertext
- protegge da padding oracle e bit flipping
- è considerato **lo schema corretto**



### Encrypt-and-MAC

1. Il messaggio viene cifrato.
2. Si calcola un MAC sul plaintext.

Questo schema può introdurre vulnerabilità e **non è raccomandato**.



### Perché Encrypt-then-MAC è corretto

Encrypt-then-MAC garantisce che:
- solo ciphertext validi vengano decifrati
- ogni modifica venga rilevata prima della decifratura

Per questo motivo è lo schema adottato nelle implementazioni moderne o sostituito da modalità **autenticate** come **AES-GCM**.

