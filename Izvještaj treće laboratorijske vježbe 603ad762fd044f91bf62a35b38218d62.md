# Izvještaj treće laboratorijske vježbe

### Zadatak

Implementirajte zaštitu integriteta sadržaja dane poruke primjenom odgovarajućeg *message authentication code (MAC)* algoritma. Koristite pri tome HMAC mehanizam iz Python biblioteka `[cryptography](https://cryptography.io/en/latest/hazmat/primitives/mac/hmac/)`.

### Alati

**Generiranje mac value za zadanu poruku i ključ (message_integrity.py)**

```python
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):
   if not isinstance(message, bytes):
	   message = message.encode()

   h = hmac.HMAC(key, hashes.SHA256())
   h.update(message)
   signature = h.finalize()
   return signature

if __name__=="__main__":
	key = b "my secret"
	message = "hello world"
	mac_value = generate_MAC(key, message)

	print(mac_value.hex())
```

**Pokretanje programa**

```python
python .\message_integrity.py
```

```python
19292bea3f57d55c2e0c2b3f38110b6ebebbcdff61f5b8ed7807cc779a46eafb
```

**Čitamo datoteku message.txt i uz pomoć ključa generiramo mac value koji upisujemo u datoteku message.sig**

```python
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):
   if not isinstance(message, bytes):
	   message = message.encode()

   h = hmac.HMAC(key, hashes.SHA256())
   h.update(message)
   signature = h.finalize()
   return signature

if __name__=="__main__":
	key = b "my secret"
	
	with open("message.txt", "rb") as file:
    content = file.read()

	mac = generate_MAC(key, content)

	with open ("message.sig", "wb") as file:
		file.write(mac)
```

**Provjera je li došlo do povrede integriteta tj. jesu li poruka i potpis jednaki**

```python
from cryptography.hazmat.primitives import hashes, hmac

from cryptography.exceptions import InvalidSignature

def verify_MAC(key, signature, message):
    if not isinstance(message, bytes):
        message = message.encode()

    h = hmac.HMAC(key, hashes.SHA256())
    h.update(message)
    try:
        h.verify(signature)
    except InvalidSignature:
        return False
    else:
        return True

def generate_MAC(key, message):
   if not isinstance(message, bytes):
	   message = message.encode()

   h = hmac.HMAC(key, hashes.SHA256())
   h.update(message)
   signature = h.finalize()
   return signature

if __name__=="__main__":
	key = b "my secret"
	
	with open("message.txt", "rb") as file:
    content = file.read()

	with open("message.sig", "rb") as file:
    signature = file.read()

	is_valid = verify_MAC (key, signature, content)
	print(is_valid)
```