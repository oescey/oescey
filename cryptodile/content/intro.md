# Introduzione alla crittografia

## Obiettivi della crittografia
La crittografia si basa su tre principi che guidano le policies utilizzate per la protezione di dati sensibili, in inglese CIA (_Confidentiality, Integrity, Availability_):
1. **Confidenzialità**: rendere i dati accessibili solo a chi autorizzato.
2. **Integrità**: mantenere i dati inalterati, accurati e coerenti durante la loro conservazione o durante la loro trasmissione a terzi.
3. **Disponibilità**: assicurare sempre l'accesso ai dati quando necessari.

### Terminologia di base
- **Plaintext**: il messaggio originale leggibile.
- **Ciphertext**: il messaggio cifrato risultante.
- **Chiave segreta**: chiave condivisa tra mittente e destinatario utilizzata sia per cifrare che decifrare. Utilizzata nei sistemi simmetrici.
- **Chiave pubblica**: chiave pubblicamente disponibile usata per cifrare messaggi o verificare firme digitali, per la decifratura o la firma vera e propria è necessaria la corrispondente chiave privata. Utilizzata nei sistemi asimmetrici.
- **IV (_Initialization Vector_)**: valore iniziale utilizzato nelle modalità di cifratura a blocchi per introdurre casualità.
- **Nonce (_Number Used Once_)**: valore che deve essere unico per ogni operazione di cifratura effettuata con la stessa chiave.

## Tipi di cifrario
I cifrari si dividono in due grandi categorie:

- **Simmetrici**: la stessa chiave viene usata sia per cifrare che decifrare. A loro volta si dividono in cifrari a blocchi e cifrari a flusso.
  - A blocchi (_block cipher_): si lavora su gruppi di bit di lunghezza predefinita organizzati in blocchi. Ogni blocco è cifrato separatamente.
  - A flusso (_stream cipher_): ogni singolo elemento che compone il plaintext viene cifrato indipendentemente, uno alla volta. Nonostante sia una procedura tendenzialmente più veloce, sono maggiormente suscettibili a problemi di sicurezza.
- **Asimmetrici**: viene usata una coppia di chiavi (pubblica/privata), si basano sul mantenere segreta la chiave privata.

#### Cifrari a flusso vs cifrari a blocchi
Un **cifrario a flusso** cifra il plaintext un bit o un byte alla volta, mentre un **cifrario a blocchi** cifra il plaintext in blocchi di dimensione fissa, generalmente di 128 o 256 bit.

- I cifrari a flusso sono spesso utilizzati in applicazioni real-time, dove i dati vengono trasmessi in modo continuo, come nelle comunicazioni wireless o nello streaming audio e video.  
Sono efficienti in termini di calcolo e utilizzo della memoria e permettonodi cifrare i dati rapidamente.  
Esempi di cifrari a flusso sono  A5/1 e RC4.

- I cifrari a blocchi, invece, vengono utilizzati per cifrare grandi quantità di dati. Offrono generalmente maggiore sicurezza, ma richiedono maggiori risorse di calcolo e memoria.  
Esempi di cifrari a blocchi sono AES e DES.

In entrambi i casi, il processo di cifratura e decifratura avviene tramite una **chiave segreta condivisa**, garantendo che solo le parti autorizzate possano accedere ai dati cifrati.
Inoltre, è importante notare che sia i cifrari a flusso che quelli a blocchi possono essere utilizzati in diverse modalità di operazione. Ogni modalità presenta caratteristiche e proprietà di sicurezza differenti, che influenzano il livello di protezione complessivo del sistema.

## XOR (⊕)
Lo XOR (_exclusive OR_) è un'operazione logica che, presi due valori (nel nostro caso bit), restituisce:
- 1 se i bit sono **diversi** 
- 0 se i bit sono **uguali**.

Lo XOR gode di diverse proprietà matematiche: commutatività, associatività, elemento neutro e invertibilità.
L'**invertibilità** è essenziale in crittografia, in quanto determina che applicare due volte XOR con lo stesso valore permette di recuperare il dato originale.
`(Testo ⊕ Key) ⊕ Key = Testo`
Infatti molti algoritmi di cifratura moderni utilizzano XOR per combinare il plaintext con una chiave o un keystream. Ovviamente XOR permette una cifratura veloce ed efficiente, ma non fornisce sicurezza di per sé, la sicurezza dipende interamente dalla qualità del keystream e da una implementazione corretta.
