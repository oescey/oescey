# RSA Low Exponent Attack

## Funzionamento

In questa challenge viene utilizzato l’algoritmo RSA per cifrare una flag.
Vengono forniti il modulo $N$, l’esponente pubblico $e$ e il ciphertext $c$.
Recuperare la flag originale a partire dai seguenti parametri RSA:

```
N = 235166090841732345213129346322673973159
e = 3
c = 6369690780153
```

Una volta ottenuto il messaggio come numero, si suggerisce di trasformarlo in forma leggibile con
```python
from Crypto.Util.number import long_to_bytes
```

## Suggerimenti

**Hint 1**
L’esponente pubblico è molto piccolo e il padding è assente, inoltre Il ciphertext è numericamente piccolo rispetto a $N$

**Hint 2**
Il messaggio cifrato potrebbe essere più piccolo del modulo. Quindi potrebbe non essere necessario fattorizzare $N$, che sarebbe molto complesso.

**Hint 3**
Prova a chiederti se il ciphertext sia davvero il risultato di un’operazione modulo o magari è semplicemente una potenza.

## Verifica

Hai trovato la flag? Verificala qui!

<div class="flag-checker">
    <input type="text" class="flag-input" placeholder="CTF{...}" />
    <button class="flag-check-btn">Controlla</button>
    <p class="flag-result"></p>
</div>
