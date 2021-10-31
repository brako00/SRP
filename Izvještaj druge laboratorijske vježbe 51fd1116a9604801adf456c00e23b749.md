# Izvještaj druge laboratorijske vježbe

### Zadatak

U sklopu vježbe student će riješiti odgovarajući *crypto* izazov, odnosno dešifrirati odgovarajući *ciphertext* u kontekstu simetrične kriptografije. Izazov počiva na činjenici da student nema pristup enkripcijskom ključu.

### Alati

**Uključivanje python cryptography biblioteke**

```jsx
**from** **cryptography.fernet** **import** Fernet
```

**Generiranje ključa**

```jsx
key = Fernet.generate_key()
f = Fernet(key)
```

**Enkripcija plaintext-a u ciphertext**

```jsx
ciphertext = f.encrypt(b"hello world")
```

**Dekripcija ciphertext-a**

```jsx
f.decrypt(ciphertext)
```

**Program (my_challenge.py) za otkrivanje imena datoteke za izazov**

```jsx
from cryptography.hazmat.primitives import hashes

def hash(input):
	if not isinstance(input, bytes):
		input = input.encode()

	digest = hashes.Hash(hashes.SHA256())
	digest.update(input)
	hash = digest.finalize()

	return hash.hex()

if __name__=="__main__":
	h=hash('rako_bruna')
	print(h)
```

**Pokretanje my_challenge.py**

```jsx
python my_challenge.py
```

```jsx
a4feefd4047888b2d9de1b4a19fe3bda755fab9890fb0858c273f3a2efe98646
```

**Realiziranje dekripcije pomoću brute force (brute_force.py)**

```jsx
import base64
from cryptography.fernet import Fernet

def brute_force():
# Reading from a file
filename = "a4feefd4047888b2d9de1b4a19fe3bda755fab9890fb0858c273f3a2efe98646.encrypted"
with open(filename, "rb") as file:
	ciphertext = file.read()
ctr = 0
while True:
	key_bytes = ctr.to_bytes(32, "big")
	print(key_bytes)
	key = base64.urlsafe_b64encode(key_bytes)

	try:
		fernet = Fernet(key)
		plaintext = fernet.decrypt(ciphertext)
		print(key,plaintext)
		break
	except Exception:
		pass

	ctr += 1

if __name__ == "__main__":
	brute_force()
```

**Rješenje dekripcije izazova**