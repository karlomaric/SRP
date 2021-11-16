# 3.laboratorijska vjezba

kreiramo virutalno okruzenje python -venv srp

cd Scripts

activate 

cd .. x2 

otvaramo code sa: "code ."

### IZAZOV 1:

Kod: 

```jsx
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

```jsx
mac = generate_MAC(key, content)

with open("message.sig", "wb") as file:

	file.write(mac)
```

—> Stvaramo message.sig file —> tu zapisujemo svoju poruku koju želimo zaštititi

Dodajemo 4.korak:  

```jsx
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

```jsx
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

```jsx
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