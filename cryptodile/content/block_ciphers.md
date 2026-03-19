# Cifrari a blocchi

## Concetti di base
- **Blocchi:** gruppi di byte di lunghezza fissa, il plaintext viene diviso in blocchi di questa dimensione.
- **Padding:** se l'ultimo blocco non raggiunge la lunghezza di bit prefissata, bisogna aggiungere del padding. Il padding consiste nel riempire il blocco con dati inutili attraverso schemi standard per raggiungere la lunghezza desisderata. Negli esempi verrà usato lo standard PKCS#7.

## Advanced Encryption Standard
È uno dei più famosi algoritmi di cifratura simmetrica a blocchi. Lavora su blocchi di 128 bit con chiavi di dimensioni da 16, 24 o 32 byte (note come AES-128, AES-192, AES-256). Se implementato correttamente è uno degli algoritmi più sicuri disponibili ai giorni d'oggi, inoltre le versioni a chiave più lunga offrono un ottimo livello di sicurezza anche rispetto alla potenza di futuri computer quantistici.

#### Come funzionano i round nell' AES
AES prende un blocco di 16 byte e lo trasforma con una sequenza ripetuta di operazioni (round). Immaginando un blocco come una matrice 4x4

``
a b c d
e f g h
i j k l
m n o p
``

definiamo le operazioni principali eseguite ad ogni round:

- **SubBytes:** ogni byte viene sostituito con un altro byte tramite una tabella detta S-box. Questa tabella è fissa ed identica per ogni tipo di AES.
- **ShiftRows:** i byte di ogni riga vengono shiftati ciclicamnete verso sinistra per mescolare i byte tra colonne (nella riga 0 rimarranno uguali, nella riga 1 verranno spostati di 1 e così via)
- **MixColumns:** ogni colonna di 4 byte viene trasformata con operazioni su GF(2^8), mescolando i valori e aumentando la diffusione. Questa operazione non viene fatta nell'ultimo round per rendere più facile la decifrazione e per mantenere la simmetria nell'output.
- **AddRoundKey:** dalla chiave principale, AES produce una chiave derivata differente per ogni round (si avranno tante round key quanti sono i round). Durante questa operazione la matrice viene XORata con la round key. In questo modo la chiave non è direttamente visibile e l'algoritmo viene protetto da attacchi di criptoanalisi lineare o differenziale.

In sintesi AES è un algoritmo molto solido e, per ora, privo di vulnerabilità note a livello strutturale. Le più grandi debolezze di AES non derivano infatti dal design dell'algoritmo, ma da cattive implementazioni.

## AES - ECB (Electronic Code Book)

### Come funziona 
Il modello ECB è il più semplice di tutti quelli adottati per AES:

1. si divide il plaintext in blocchi di 16 byte.
2. ogni blocco viene cifrato separatamente con la stessa chiave.
3. i blocchi cifrati vengono concatenati per formare il ciphertext.

### Esempio con libreria pycryptodome

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

#padding
def pad(msg, block_size=16):
    pad_len = block_size - (len(msg) % block_size) #utilizzo PKCS#7
    return msg + bytes([pad_len]) * pad_len

def unpad(msg):
    pad_len = msg[-1]
    return msg[:-pad_len]


def aes_ecb_encrypt(key, plaintext):
    cipher = AES.new(key, AES.MODE_ECB)
    plaintext = pad(plaintext)
    return cipher.encrypt(plaintext)

def aes_ecb_decrypt(key, ciphertext):
    cipher = AES.new(key, AES.MODE_ECB)
    return unpad(cipher.decrypt(ciphertext))


def demo():
    key = get_random_bytes(16)
    print("KEY:", key.hex(), "\n")

    plaintext = b"Ciao!" 
    print("Plaintext:", plaintext)

    ciphertext = aes_ecb_encrypt(key, plaintext)
    print("\nCiphertext (ECB):", ciphertext.hex()) #utilizzo hex() per rendere leggibile il risultato

    recovered = aes_ecb_decrypt(key, ciphertext)
    print("\nRecovered:", recovered)

if __name__ == "__main__":
    demo()
```

### Vantaggi
- Estrema semplicità.
- Cifratura e decifratura molto veloci.
- Facilmente parallelizzabile (ogni blocco è indipendente).

### Debolezze

La semplicità di questa modalità favorisce la velocità di cifratura e decifratura, ma è estremamente vulnerabile poiché tutti i blocchi sono cifrati in modo indipendente con la stessa chiave, portando al determinismo
Infatti ECB non fornisce **sicurezza semantica**: blocchi identici di plaintext producono blocchi identici di ciphertext. Questo rende visibili pattern e ripetizioni nei dati cifrati e permette attacchi come _byte-at-a-time_.


## AES - CBC (Cipher Block Chaining)

### Come funziona
La modalità CBC introduce dipendenza tra i blocchi per eliminare i pattern che invece sono visibili in ECB. Infatti viene itnrodotto l'utilizzo di un vettore di inizializzazione della dimensione del blocco cifrato (**IV**), utilizzato per randomizzare il primo blocco. 

1. si idvide il plaintext in blocchi da 16 byte.
2. ogni blocco di plaintext viene **XORato** con il ciphertext del blocco precedente.
3. il risultato viene cifrato con AES usando la stessa chiave.
4. i blocchi cifrati vengono concatenati per formare il ciphertext.

Si avrà quindi una struttura di questo tipo, dove il primo blocco dipende dall’IV e ogni blocco successivo dipende dal precedente.
``
C1 = AES_encrypt(P1 XOR IV, key)
C2 = AES_encrypt(P2 XOR C1, key)
C3 = AES_encrypt(P3 XOR C2, key)
...
``

### Esempio con libreria PyCryptodome

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

# padding PKCS#7
def pad(msg, block_size=16):
    pad_len = block_size - (len(msg) % block_size)
    return msg + bytes([pad_len]) * pad_len

def unpad(msg):
    pad_len = msg[-1]
    return msg[:-pad_len]

def aes_cbc_encrypt(key, iv, plaintext):
    cipher = AES.new(key, AES.MODE_CBC, iv)
    return cipher.encrypt(pad(plaintext))

def aes_cbc_decrypt(key, iv, ciphertext):
    cipher = AES.new(key, AES.MODE_CBC, iv)
    return unpad(cipher.decrypt(ciphertext))

def demo():
    key = get_random_bytes(16)
    iv  = get_random_bytes(16)

    print("KEY:", key.hex())
    print("IV :", iv.hex(), "\n")

    plaintext = b"Ciao!"
    print("Plaintext:", plaintext)

    ciphertext = aes_cbc_encrypt(key, iv, plaintext)
    print("\nCiphertext (CBC):", ciphertext.hex())

    recovered = aes_cbc_decrypt(key, iv, ciphertext)
    print("\nRecovered:", recovered)

if __name__ == "__main__":
    demo()
```

### Vantaggi

- L'IV casuale (e non fisso!) assicura che cifrare due messaggi uguali non produca lo stesso ciphertext, garantendo sicurezza semantica.
- Il chaining (la concatenazione tra blocchi) implica che modificare un bit del plaintext provoca cambiamenti nei blocchi successivi.
- Nasconde ripetizioni e pattern nel plaintext al contrario della modalità ECB.

### Debolezze

Se l’implementazione di CBC restituisce messaggi o comportamenti diversi in base al tipo di errore, ’attaccante può usare questa informazione come un oracolo. In questo scenario non è necessaria la conoscenza della chiave: le informazioni restituite dal servizio permettono di recuperare il plaintext blocco dopo blocco (_padding oracle attack_).
Inoltre, CBC non fornisce integrità: un attaccante può modificare il ciphertext causando cambiamenti controllabili nel plaintext (_bit flipping attack_).
 

## AES - CTR (Counter Mode)

### Come funziona

La modalità CTR elimina i pattern cifrando i dati tramite uno stream di byte pseudo‑casuali, trasformando AES da cifrario a blocchi a cifrario a flusso.
Non viene cifrato direttamente il plaintext, AES viene infatti utilizzato per generare un keystream che viene poi combinato con il messaggio tramite XOR.

1. si divide il plaintext in blocchi da 16 byte.
2. per ogni blocco si cifra con AES la concatenazione `Nonce || Counter.`
3. il risultato (keystream) viene XORato con il blocco di plaintext.
4. i blocchi ottenuti vengono concatenati per formare il ciphertext.

Si avrà quindi una struttura del tipo:
``
S1 = AES_encrypt(Nonce || Counter1, key)
C1 = P1 XOR S1

S2 = AES_encrypt(Nonce || Counter2, key)
C2 = P2 XOR S2

S3 = AES_encrypt(Nonce || Counter3, key)
C3 = P3 XOR S3
...
``

In CTR non viene utilizzato padding poichè essendo un cifrario a flusso si può tranquillamente operare su dati di lunghezza arbitraria.


### Esempio con PyCryptodome

```python
from Crypto.Cipher import AES
from Crypto.Random import get_random_bytes

def aes_ctr_encrypt(key, nonce, plaintext):
    cipher = AES.new(key, AES.MODE_CTR, nonce=nonce)
    return cipher.encrypt(plaintext)

def aes_ctr_decrypt(key, nonce, ciphertext):
    cipher = AES.new(key, AES.MODE_CTR, nonce=nonce)
    return cipher.decrypt(ciphertext)

key = get_random_bytes(16)
nonce = get_random_bytes(8)

msg = b"Messaggio segreto in CTR"
ct  = aes_ctr_encrypt(key, nonce, msg)
pt  = aes_ctr_decrypt(key, nonce, ct)

print(pt)
```

### Vantaggi

- Non è richiesto padding
- È molto veloce, efficiente e facilmente parallelizzabile.
- Nasconde ripetizioni e pattern nel plaintext al contrario della modalità ECB (come CBC).

### Debolezze

Se la modalità CTR viene utilizzata con lo stesso nonce e la stessa chiave per cifrare messaggi diversi, il keystream generato sarà identico. In questo scenario non è necessaria la conoscenza della chiave: un attaccante può combinare i ciphertext per ottenere informazioni sui plaintext (_keystream reuse / two‑time pad attack_).

Inoltre, CTR non fornisce integrità: un attaccante può modificare il ciphertext e provocare cambiamenti prevedibili nel plaintext durante la decifratura (_bit flipping attack_).
