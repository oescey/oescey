# ECB byte-at-a-time Attack


Ti sei imbattuto in un piccolo servizio un po’ ingenuo...
È molto fiero del suo lavoro, estremamente diligente e anche un po' stakanovista: non può fare a meno di rispondere a ogni tuo input. Ovviamente si tratta di un servizio **SUPER** sicuro che non rivelerà **MAI** i suoi segreti.

## Funzionamento
Questa challenge è pensata per essere risolta localmente usando il container Docker fornito. Non sarà necessario modificare il server, tutto l’attacco avviene dal lato client.
Costruisci l'immagine:

`docker build -t ecb-oracle .`

Esegui il container:

`docker run -p 1337:1337 ecb-oracle`

Il server sarà disponibile su:

`http://localhost:1337/`

L'oracolo di cifratura risponderà su `POST /ecnrypt`.
Inviando ogni tipo di input, il server restituisce:

`(user_input || SECRET)`

L'oracolo utilizza AES in modalità ECB utilizzando blocchi da **16 byte**.

Le richieste POST possono essere fatte in diversi modi, tra cui:

```bash
curl -X POST http://localhost:1337/encrypt --data "Hello!"
```

```python
import requests

r = requests.post("http://localhost:1337/encrypt", data=b"Hello!")
print(r.content)
```


## Suggerimenti

**Hint 1**
Ricorda che il servizio è **deterministico**: blocchi di input identici producono blocchi di output identici. È possibile che l'output contenga pattern ripetuti?

**Hint 2**
Prova a a inviare input di **lunghezze diverse** e osserva come cambia la lunghezza dell’output. AES lavora a blocchi di dimensione fissa, quindi provando a capire cosa succede quando il tuo input “riempie” esattamente un blocco può aiutare.

**Hint 3**
Se due porzioni di plaintext sono uguali, anche le rispettive porzioni di ciphertext lo saranno. Prova a costruire input che differiscono per un solo byte alla fine, mantenendo il resto allineato con i blocchi.

**Hint 4**
L’output dipende sia dal tuo input sia da informazioni interne gestite dal servizio, seguendo lo schema `input_utente || SECRET`.

**Hint 5**
Puoi costruire una tabella di confronto dei ciphertext per byte singoli:  
1. Fissa una parte dell’input in modo da allineare la porzione sconosciuta all’inizio di un blocco.  
2. Per ogni possibile valore di un byte, invia l’input al servizio e memorizza il blocco cifrato risultante.  
3. Poi invia l’input originale senza il byte di prova e confronta il blocco corrispondente.  
4. Se i blocchi coincidono, il byte che hai provato corrisponde a quello sconosciuto nel blocco.
Quando riuscirai a trovare una corrispondenza tra la tabella e la parte di output sconosciuta, salva il valore come parte della soluzione.

## Verifica

Hai trovato la flag? Verificala qui!

<div class="flag-checker">
    <input type="text" class="flag-input" placeholder="CTF{...}" />
    <button class="flag-check-btn">Controlla</button>
    <p class="flag-result"></p>
</div>

