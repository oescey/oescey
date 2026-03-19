# CBC bit flipping Attack

## Funzionamento

Costruisci l'immagine:

`docker build -t cbc-bitflip .`

Esegui il container:

`docker run -p 5000:5000 cbc-bitflip`

Il server sarà disponibile su:

`http://localhost:5000/`

Endpoint vulnerabili:

1. POST /login
   Invia un nome utente arbitrario e ricevi un token cifrato.
   input: `{ "username": "test" }`
   output: 
    {
    `"iv": "<iv in hex>",`
    `"ciphertext": "<ciphertext in hex>"`
    }

    Il plaintext cifrato ha il seguente formato:
    `user=<username>;admin=0`

2. POST /check
    Invia un token cifrato per verificarne i privilegi.
    input:
    {
    `"iv": "<iv>",`
    `"ciphertext": "<ciphertext>"`
    }
    output: 
    Se utente normale
    {
    `"status": "NO",`
    `"plaintext": "..."`
    }
    Se utente admin
    {
    `"status": "OK",`
    `"flag": "CTF{...}"`
    }

Per comunicare con il server puoi usare una libreria HTTP client.
In Python è consigliata la libreria: `requests`

Funzioni utili:
- `requests.post()`
- metodo `.json()` per parsare la risposta

Il server restituisce `iv` e `ciphertext` in formato **hex**.
Per lavorare a livello di byte è necessario convertirli.

Funzioni utili:
- `bytes.fromhex()`
- `bytearray()` per modificare singoli byte

Dopo aver modificato il ciphertext ricostruisci il token e invialo all’endpoint `/check`.

## Suggerimenti

**Hint 1**
In CBC, modificando un byte del ciphertext di un blocco, si modifica il byte corrispondente del plaintext del blocco successivo. Per trasformare `0` in `1`, applica uno XOR mirato.

Funzioni utili:
- `ord()`
- operatore `^` (XOR)

**Hint 2**
AES-CBC utilizza blocchi da **16 byte**. Scegli uno `username` con lunghezza controllata in modo che la stringa `admin=0` cada interamente in un byte di un blocco specifico.
`"username="` occupa 9 byte e `;admin=` ne occupa 7, 9+7=.... ;)

**Hint 3**
Se i conti ancora non ti tornano, il byte contenente lo `0` che bisogna "flippare" è il settimo del secondo blocco.

## Verifica

Hai trovato la flag? Verificala qui!

<div class="flag-checker">
    <input type="text" class="flag-input" placeholder="CTF{...}" />
    <button class="flag-check-btn">Controlla</button>
    <p class="flag-result"></p>
</div>
