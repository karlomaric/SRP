# 2.laboratorijska vjezba

python --version  

pokrećemo virtualno okruženje u pythonu (da ne smetamo u globalnom, nego samo u tom pokrenutom, poslije ga izbrisemo)—> python -m venv Grupa3SRP (Grupa3SRP je ime)

cd Grupa3SRP

cd Scripts — 

Scripts>activate — 

pip install cryptography — library prisutan samo u ovom vir.okruzenju (instalirali smo ga)

zatim unosimo samo "python"

primjer pythona: 

print ("Hello world")
Hello world

> plaintext = "Hello world"
print(plaintext)
Hello world
> 

from cryptography.fernet import Fernet

Fernet.generate_key() — generira kljuc

`key = Fernet.generate_key()` — spremamo kljuc u varijablu key

plaintext = b"Hello world" — binarno kodirano (bynary format)

ciphertext = f.encrypt(plaintext)  — enkriptiramo plaintext

ciphertext — da nam ispise chipertext

→idemo u suprotnom smjeru, dekriptiramo ciphertext

f.decrypt(ciphertext)
b'Hello world'  — ispis

— sad radimo izazov —

code brute_force.py — pisat cemo skriptu u VS code

python brute_force.py — poziv skripte (trenutno je prazna)

kopiramo 3.korak u VS code —

from cryptography.hazmat.primitives import hashes

def hash(input):
if not isinstance(input, bytes):
input = input.encode()

```
digest = hashes.Hash(hashes.SHA256())
digest.update(input)
hash = digest.finalize()

return hash.hex()

```

dodajemo —> 

if __name__ == "__main__":

h= hash('maric_karlo')

print(h)

zatim pozivamo u terminalu —> python brute_force1.py

ispisuje nam ime odgovarajuceg filea (14b7e46871f9269d06ef04db75cf4fb380912416f513160d2ca357765e113654), tj. znam koji je točno file moj

spremam svoj file u svoj direktorij

`key = int.from_bytes(os.urandom(32), "big") & int('1'*KEY_ENTROPY, 2)` — kako se napravila enkripcija 

definirali smo u VS codu novu funkciju kojom radimo enkodiranje: 

def brute_force():

ctr = 0

while True:

key_bytes = ctr.to_bytes(32, "big")

key = base64.urlsafe_b64encode(key_bytes)

# Now initialize the Fernet system with the given key

# and try to decrypt your challenge.

# Think, how do you know that the key tested is the correct key

# (i.e., how do you break out of this infinite loop)?

ctr += 1

dodali smo malo pythona —>

if not (counter +1) % 1000:

print(f"[*] Keys tested: {ctr +1:,}", end="\r")

pozvali u terminalu opet sa: python brute_force1.py

cijeli kod u VS code:

```jsx
import base64
```

```jsx
from cryptography.fernet import Fernet
```

```jsx
from cryptography.hazmat.primitives import hashes
```

```jsx
def hash(input):
```

```jsx
if not isinstance(input, bytes):
```

```jsx
input = input.encode()
```

```jsx
digest = hashes.Hash(hashes.SHA256())
```

```jsx
digest.update(input)
```

```jsx
hash = digest.finalize()
```

```jsx
return hash.hex()
```

```jsx
def test_png(header):
```

```jsx
if header.startswith(b"\211PNG\r\n\032\n"):
```

```jsx
return True
```

```jsx
def brute_force():
```

```jsx
# Reading from a file
```

```jsx
filename = "14b7e46871f9269d06ef04db75cf4fb380912416f513160d2ca357765e113654.encrypted"
```

```jsx
with open(filename, "rb") as file:
```

```jsx
ciphertext = file.read()
```

```jsx
ctr = 0
```

```jsx
while True:
```

```jsx
key_bytes = ctr.to_bytes(32, "big")
```

```jsx
key = base64.urlsafe_b64encode(key_bytes)
```

```jsx
if not (ctr +1) % 1000:
```

```jsx
print(f"[*] Keys tested: {ctr +1:,}", end="\r")
```

```jsx
try:
```

```jsx
plaintext = Fernet(key).decrypt(ciphertext)
```

```jsx
header = plaintext[:32]
```

```jsx
if test_png(header):
```

```jsx
print(f"[+] KEY FOUND: {key}")
```

```jsx
# Writing to a file
```

```jsx
with open("BINGO.png", "wb") as file:
```

```jsx
file.write(plaintext)
```

```jsx
break
```

```jsx
except Exception:
```

```jsx
pass
```

```jsx
ctr += 1
```

```jsx
if __name__ == "__main__":
```

```jsx
brute_force()
```