# Lab. vježbe 2 - Crypto challenge

# Uvod

U drugoj laboratorijskoj vježbi trebali smo dekriptirati personaliziranu sliku, koristeći Python (biblioteka cryptography i high-level sustav za simetričnu enkripciju iz nje - Fernet). 

# Postavljanje

Prije izvođenja samog izazova, potrebno je bilo pronaći ime datoteke koju treba dekriptirati, koje se sastojalo od našeg imena i prezimena. 

```python
from cryptography.hazmat.primitives import hashes

def hash(input):
    if not isinstance(input, bytes):
        input = input.encode()

    digest = hashes.Hash(hashes.SHA256())
    digest.update(input)
    hash = digest.finalize()

    return hash.hex()

filename = hash('tomic_ivan') + ".encrypted"
```

Rezultat gore napisanog koda je 

```
3844d4cdf2c45dc6a52f427620ad168882023fa8cfba5cd84ecaa5393031db25.encrypted
```

što je ime datoteke koja se nalazi na serveru. 

# Izvođenje

Samo dekriptiranje izvodi se na brute force način, isprobavanjem svih mogućih ključeva (2^22) dok se početak Headera dekriptiranog .png filea ne podudara s očekivanim vrijednostima. 

```python
import base64
from cryptography.hazmat.primitives import hashes
from cryptography.fernet import Fernet

def hash(input):
if not isinstance(input, bytes):
input = input.encode()

digest = hashes.Hash(hashes.SHA256())
digest.update(input)
hash = digest.finalize()

return hash.hex()

def test_png(header):
if header.startswith(b"\211PNG\r\n\032\n"):
return True

def brute_force():
filename = "3844d4cdf2c45dc6a52f427620ad168882023fa8cfba5cd84ecaa5393031db25.encrypted"
with open(filename, "rb") as file:
ciphertext = file.read()

ctr = 0
while True:
    key_bytes = ctr.to_bytes(32, "big")
    key = base64.urlsafe_b64encode(key_bytes)

    if not (ctr + 1) % 1000:
        print(f"[*] Keys tested: {ctr + 1:,}", end="\\r")

    try:
        plaintext = Fernet(key).decrypt(ciphertext)

        header = plaintext[:32]
        if test_png(header):
            print(f"[+] KEY FOUND: {key}")
            with open("BINGO.png", "wb") as file:
                file.write(plaintext)
            break

    except Exception:
        pass

    ctr += 1

if **name** == "**main**":
brute_force()
```

Slika BINGO.png izgleda ovako:

<figure><img src="images/Untitled.png"></figure>
