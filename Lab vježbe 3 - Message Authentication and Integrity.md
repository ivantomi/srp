# Lab. vježbe 3 - Message Authentication and Integrity

# Uvod

Cilj treće laboratorijske vježbe bila je primjena znanja o osnovnim kriptografskim mehanizmima za autentikaciju i zaštitu integriteta poruka. U tu svrhu, koristili smo message authentication code (MAC).

# 1. zadatak

Nakon što smo kreirali datoteku proizvoljnog sadržaja čiji integritet želimo zaštititi, generiramo MAC pomoću sadržaja poruke i proizvoljnog ključa.

Kako bismo provjerili autentičnost, provjeravamo stvoreni MAC s onim koji smo poslali kao argument u funkciju. 

```python
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature

def generate_MAC(key, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    mac = h.finalize()
    return mac

def verify_MAC(key, mac, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(mac)
    except InvalidSignature:
        return False
    else:
        return True

if __name__ == "__main__":
    key = b"Oooooogromna tajna"

    with open("message.txt", "rb") as file:
        message = file.read()

    # mac = generate_MAC(key, message)

    # with open("message.sig", "wb") as file:
    #     file.write(mac)

    with open("message.sig", "rb") as file:
        mac = file.read()

    isAuthentic = verify_MAC(key, mac, message)

    print(isAuthentic)
```

# 2. zadatak

Zadatak je tražio utvrđivanje vremenski ispravnog slijeda transakcija s odgovarajućim dionicama. 

Nakon preuzimanja potrebnih datoteka pomoću GNU Wget programa, slijedi provjera je li traženi MAC dobiven kombinacijom odgovarajuće poruke i ključa. Ako je, spremamo sadržaj poruke. 
Konačno, sortiramo sve transakcije. 

```python
from os import read
from cryptography.hazmat.primitives import hashes, hmac
from cryptography.exceptions import InvalidSignature
import os
from datetime import datetime

def generate_MAC(key, message):

	if not isinstance(message, bytes):
			message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())
	h.update(message)
	mac = h.finalize()

	return mac

def verify_MAC(key, mac, message):

	if not isinstance(message, bytes):
		message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())
	h.update(message)
	try:
		h.verify(mac)
	except InvalidSignature:
		return False
	else:
		return True

def extractOrderDateTime(order):

	first = order.index('(')
	second = order.index(')')
	orderDate = order[first+1: second]

	return datetime.strptime(orderDate, "%Y-%m-%dT%H:%M")

def checkIfAuthentic(path, key):

	valid = []

	for ctr in range(1,11):

		msgFile = os.path.join(path, f"order_{ctr}.txt")
		sigFile = os.path.join(path, f"order_{ctr}.sig")

		with open(msgFile, "rb") as file:
			message = file.read()

		with open(sigFile, "rb") as file:
			mac = file.read()

		is_authentic = verify_MAC(key, mac , message)

		if is_authentic:
			valid.append(message.decode("utf-8"))
			valid.sort(key = extractOrderDateTime)

	print(valid)

if __name__ == "__main__":

	key = "tomic_ivan".encode()
	path = os.path.join("challenges","tomic_ivan", "mac_challenge")
	checkIfAuthentic(path,key)
```