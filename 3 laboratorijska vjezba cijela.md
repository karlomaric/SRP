# 3.laboratorijska vjezba

kreiramo virutalno okruzenje python -venv srp

cd Scripts

activate 

cd .. x2 

otvaramo code sa: "code ."

### IZAZOV 1:

Kod: 

```python
from cryptography.hazmat.primitives import hashes, hmac

def generate_MAC(key, message):

	if not isinstance(message, bytes):

		message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())

	h.update(message)

	signature = h.finalize()

		return signature

if __name__ == "__main__":

	key=b"moj privatni kljuc"

	with open("message.txt", "rb") as file:

		content = file.read()

	mac = generate_MAC(key, content)

	print(mac.hex())
```

ispis u terminalu (kad smo pozvali: .\message_integrity.py): c1ad736b88a5a1cb6772ccc670fe9e7d3d75e6faaa26a737a934c97f25ba9c32 —> dobili smo naš MAC (Message Authentication Code)

```python
mac = generate_MAC(key, content)

with open("message.sig", "wb") as file:

	file.write(mac)
```

—> Stvaramo message.sig file —> tu zapisujemo svoju poruku koju želimo zaštititi

Dodajemo 4.korak:  

```python
from cryptography.exceptions import InvalidSignature

def verify_MAC(key, mac, message):

if not isinstance(message, bytes):

message = message.encode()

h = hmac.HMAC(key, hashes.SHA256())

h.update(message)

try:

h.verify(mac)

except InvalidSignature:

return False

else:

return True
```

```python
with open("message.sig", "rb") as file:

	mac = file.read()

	is_authentic = verify_MAC(key, mac, content)

		print(is_authentic)
```

→ ispis u terminalu: "True"

Koristili smo HMAC mehanizam iz Python biblioteke - cryptography.

Prikazali smo kako se generira MAC za poruku uz pomoć ključa te smo provjeravali je li očuvan integritet poruke.

Funkcija verify_MAC nam omogućuje vidjeti je li očuvan integritet poruke ili ne, naime ako je return u konzoli bio "True" integritet je bio očuvan, a kada bi, naprimjer, promijenili tekst poruke (kojoj želimo provjeriti integritet) te pokretanjem u programa, u konzoli bi ispis bio "False".

### IZAZOV 2:

Samostalan rješajemo zadatak.

Sa lokalnog servera skinili smo 10 poruka i 10 MAC-ova pomoću wget.

Trebali smo učitati preuzete file-ove, te provjeriti je li porukama narušen integritet. Na izlazu trebamo ispisati sadržaj poruke te poklapa li se naš MAC za određenu poruku, sa MAC-om kojeg smo preuzeli za tu poruku.

Cijeli zadatak:

```python
from cryptography.hazmat.primitives import hashes, hmac

from cryptography.exceptions import InvalidSignature

import os

def verify_MAC(key, mac, message):

	if not isinstance(message, bytes):

		message = message.encode()

	h = hmac.HMAC(key, hashes.SHA256())

	h.update(message)

	try:

		h.verify(mac)

	except InvalidSignature:

		return False

	else:

		return True

if __name__ == "__main__":

	key="maric_karlo".encode()

	path = os.path.join("challenge", "maric_karlo", "mac_challenge")

	print(path)

	for ctr in range(1, 11):

		msg_filename = f"order_{ctr}.txt"

		sig_filename = f"order_{ctr}.sig"

		print(msg_filename)

		print(sig_filename)

		path_msg = os.path.join(path, msg_filename)

		path_sig = os.path.join(path, sig_filename)

		with open(path + "\\" + msg_filename, "rb") as file:

			content_file = file.read()

		with open(path + "\\" + sig_filename, "rb") as file:

			signature = file.read()

		is_authentic = verify_MAC(key, signature, content_file)

		print(f'Message {content_file.decode():>45} {"OK" if is_authentic else "NOK":<6}')
```

### DIGITAL SIGNATURES USING PUBLIC-KEY CRYPTOGRAPHY ****

U 4.laboratorijskim vježbama, završavamo zadatak sa trećih labova —>

Zadatak izazova je bio odrediti između dvije slike koja je od njih autentična.

Iz repozitorija smo preuzeli slike, digitalni potpise te javni ključ. Javni ključ nam je potreban jer se radi o public-key kriptografiji.

Deseralizacija kljuca —> kod:

```python
from cryptography.hazmat.primitives import serialization
from cryptography.hazmat.backends import default_backend

def load_public_key():
	with open(PUBLIC_KEY_FILE, "rb") as f:
		PUBLIC_KEY = serialization.load_pem_public_key(
			f.read(),
			backend=default_backend()
		)
	return PUBLIC_KEY

print(load_public_key)
```

Sa

```python
print(load_public_key)
```

provjeravamo je li ključ uredan.

Kopiramo dio koda, kojim ćemo provjeriti ispravnost digitalnog potpisa —>

```python
from cryptography.hazmat.primitives.asymmetric import padding
from cryptography.hazmat.primitives import hashes
from cryptography.exceptions import InvalidSignature

def verify_signature_rsa(signature, message):
		PUBLIC_KEY = load_public_key()
		try:
				PUBLIC_KEY.verify(
						signature,
						message,
						padding.PSS(
								mgf=padding.MGF1(hashes.SHA256()),
								salt_length=padding.PSS.MAX_LENGTH
						),
				hashes.SHA256()
				)
		except InvalidSignature:
				return False
		else:
				return True
```

Učitavamo sadržaj kako bi funkciji "verify_signature_rsa" poslali argumente —>

```python
with open("image_2.sig", "rb") as file:
		signature = file.read()

with open("image_2.png", "rb") as file:
		image = file.read()
```

I na kraju provjeravamo autentičnost—>

```python
is_authentic = verify_signature_rsa(signature, image)
print(is_authentic)
```