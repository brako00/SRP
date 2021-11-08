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
    filename = "3f7699d1bc4ee53a3e8f24bfd2577a150260f938f45b8d6a538819129263bd13.encrypted"
    # Reading from a file
    with open(filename, "rb") as file:
        ciphertext = file.read()

    ctr = 0
    while True:
        key_bytes = ctr.to_bytes(32, "big")
        key = base64.urlsafe_b64encode(key_bytes)

        if not (ctr + 1) % 1000:
            print(f"[*] Keys tested: {ctr + 1:,}", end="\r")

        try:    
            plaintext = Fernet(key).decrypt(ciphertext)
            
            header = plaintext[:32]
            if test_png(header):
                print(f"[+] KEY FOUND: {key}")
                # Writing to a file
                with open("BINGO.png", "wb") as file:
                    file.write(plaintext)         
                break

        except Exception:
            pass
            
        ctr += 1


if __name__ == "__main__":
    # hash_value = hash("cagalj_mario")
    # print(hash_value)
    brute_force()
```

**Rješenje dekripcije izazova**
