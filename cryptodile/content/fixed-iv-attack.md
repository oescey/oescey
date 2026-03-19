# Fixed IV Attack

## Funzionamento
Ti viene fornito un ciphertext ottenuto cifrando un messaggio segreto con AES in modalità CBC.
Alcuni parametri crittografici sono noti:
```
Key: "yellow banana!!!"
IV: 00…00 (16 byte)
Padding: PKCS#7
Ciphertext:
w7m/tXCk0nx5Qqo+p4bBX6rYNcdCw1UCq+xl5lfx4Fk=
```

Utilizzando la libreria `pycryptodome` assicurati di importare
```python
from Crypto.Cipher import AES
AES.new(key, AES.MODE_CBC, iv) #Crea un oggetto che rappresenta un’istanza dell’algoritmo AES
```
Inoltre, sapendo che viene applicato il padding PKCS#7, l’ultimo byte del blocco indicherà quanti byte di padding sono stati aggiunti.


## Suggerimenti

**Hint 1**
Il ciphertext fornito è stato convertito in un formato leggibile (Base64). Prima di poterlo decifrare, sarà necessario riportarlo nel formato originale di byte.
```python
import base64
cb = base64.b64decode("Inserisci il ciphertext qui")
```

**Hint 2**
Se non sai che funzioni utilizzare, è importante conoscere la differenza tra 
`.decrypt()`, che recupera i byte originali
`.decode()`, che trasforma i byte in testo leggibile

**Hint 3**
Se i conti non tornano, ricorda che alla fine della decifratura è necessario rimuovere il padding per ottenere il messaggio originale.


## Verifica

Hai trovato la flag? Verificala qui!

<div class="flag-checker">
    <input type="text" class="flag-input" placeholder="CTF{...}" />
    <button class="flag-check-btn">Controlla</button>
    <p class="flag-result"></p>
</div>



