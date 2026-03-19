# RSA (Rivest–Shamir–Adleman)

## Concetti base

RSA è un algoritmo di crittografia **asimmetrica** basato sulla difficoltà computazionale del problema della fattorizzazione: dato un numero molto grande $N$, prodotto di due grandi numeri primi $p$ e $q$, risalire a $p$ e $q$ è considerato computazionalmente impraticabile. 
In RSA, ogni utente possiede una coppia di chiavi:
1. **Chiave pubblica** $(N,e)$: può essere distribuita liberamente ed è usata per cifrare i messaggi o verificare firme digitali.
   -   $N = p \cdot q$
   -   $e$, che è l'esponente pubblico
2. **Chiave privata** $d$: deve rimanere segreta ed è utilizzata per decifrare i messaggi o generare firme.
   - calcolata in modo che $d≡e^{−1} (mod φ(N))$
   - $φ(N)=(p−1)(q−1)$ è la funzione di Eulero

Grazie a questa separazione, RSA permette non solo la confidenzialità, ma anche la verifica dell'autenticità del mittente tramite firme digitali.

## Cifratura e decifratura

Dato un messaggio $m$ (interpretato come numero con $m < N$):

**Cifratura:**
$$c = m^e \bmod N$$

**Decifratura:**
$$m = c^d \bmod N$$

La sicurezza di RSA deriva dal fatto che solo chi conosce $d$ (e quindi $p$ e $q$) può eseguire correttamente la decifratura.
Bisogna notare che, nella pratica, RSA non viene usato per cifrare direttamente messaggi arbitrari. Prima della cifratura si applicano schemi di padding sicuri (es. _OAEP_) per prevenire attacchi matematici e garantire la sicurezza semantica.


## Generazione delle chiavi

La sicurezza di RSA dipende in modo particolare da come vengono generate o scelte le chiavi. Infatti una cattiva scelta può rendere il sistema vulnerabile, anche se le dimensioni del modulo sono teoricamente corrette.

### P e q troppo vicini

I numeri $p$ e $q$ devono essere, oltre che segreti:
- primi
- sufficientemente grandi
- casuali
- indipendenti tra loro

Se $p$ e $q$ sono numericamente troppo vicini, allora $N$ potrà essere fattorizzato abbastanza facilmente utilizzando il metodo della **fattorizzazione di Fermat**. Una volta ottenuti $p$ e $q$, anche se si hanno moduli di grandi dimensioni, un attaccante potrà facilmente calcolare $φ(N)$ e di conseguenza ricavare $d$.

### Esponente pubblico piccolo 

La scelta di un esponente pubblico $e$ **troppo piccolo** (ad esempio $e = 3$) può introdurre vulnerabilità se combinata con:
- mancanza di padding
- messaggi di piccole dimensioni
- stesso messaggio cifrato con più chiavi

Queste condizioni permettono attacchi come il _low exponent attack_.

### Moduli condivisi

Se due o più utenti utilizzano lo stesso modulo $N$ ma con esponenti pubblici $e$ diversi, se un attaccante intercetta lo stesso messaggio cifrato con lo stesso $N$ ed esponenti pubblici diversi e coprimi tra loro, sarà in grado di recuperare il plaintext senza conoscere la chiave privata, sfruttando le combinazioni lineari degli esponenti.

Questo tipo di vulnerabilità è sfruttato nei _common modulus attacks_.


## Padding e uso corretto di RSA

RSA, nella sua forma base, è un algoritmo **deterministico**: cifrare due volte lo stesso messaggio con la stessa chiave produce sempre lo stesso ciphertext. Questa caratteristica rende RSA un algoritmo poco sicuro se utilizzato da solo, in quanto non viene garantita la sicurezza semantica.
Come per ECB, l'assenza di casualità permette all'attaccante di:
- riconoscere messaggi uguali
- testare ipotesi sul plaintext
- sfruttare dizionari di messaggi già noti

### Padding

L'utilizzo del padding serve per introdurre casualità nel messaggio prima della cifratura, rendendo RSA non deterministico. Senza padding, RSA è vulnerabile a numerosi attacchi matematici, anche senza la conoscenza della chiave privata o la fattorizzazione di $N$. Con l'aggiunta del padding ci si può proteggere da low exponent attack, attacchi su messaggi piccoli facilmente decifrabili e dal recupero diretto del plaintext.

**OAEP (Optimal Asymmetric Encryption Padding)** è uno schema di padding progettato per rendere RSA sicuro contro attacchi di tipo adattivo. Ad oggi è cosniderato lo standard necessario per l'utilizzo sicuro di RSA nella cifratura. Viene implementato in questo modo:
1. si aggiunge un valore casuale (_seed_) al messaggio
2. messaggio e seed vengono combinati tramite hash e XOR
3. si costruisce un blocco finale della dimensione di $N$
4. infine si cifra il blocco con RSA
