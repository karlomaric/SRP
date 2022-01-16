# 6.laboratorijska vjezba

## 1. i 2.dio

wsl - otvaramo Linux terminal

sa naredbom **id** ispisuje nam se u naš id, a naredbom **groups**

ispisuje nam sve grupe u kojima smo članovi

**sudo adduser alice5** - dodajemo novog usera iz pozicije admina, napravimo password

**su - alice5 -** logiramo se kao user alice5 

sa id —>saznamo id od alice

Za usera bob5 napravimo identičan postupak.

Splitamo window sa alt shift -, te se na jedan spojimo kao user alice5, a na drugi kao user bob5.

U home direktoriju napravimo srp direktorij naredbom **mkdir srp,** te potom i uđemo u njega naredbom **cd srp.**

**echo Hello world > security.txt**  —> zapisujemo “Hello world” u datoteku security.txt

Naredbom **ls -l** ili **getfacl security.txt** dobijamo ispis prava korisnika nad određenim file-om.

 Ispis:

```python
alice6@DESKTOP-7Q0BASR:~$ getfacl security.txt

file: security.txt
owner: alice6
group: alice6

user::rw-
group::rw-
other::r--
```

Naredbom **getfacl .** izlistaju se prava na trenutnom direktoriju.

Ispis:

```python
file: .
owner: alice6
group: alice6

user::rwx
group::r-x
other::r-x
```

**cat security.txt**  —>  ispisujemo sadržaj file-a

**chmod u-r security.txt**  —> zabranjujemo si pravo čitanja, znači user alice5 ne može pročitati sadržaj file-a

**chmod u+r security.txt**  —> vraćamo si pravo čitanja

**chmod u-x .** —> zabranjujemo si pravo executanja trenutnog direktorija

**cat /home/alice5/srp/security.txt** - user bob5 može pročitati file security.txt

**chmod o-r security.txt**  —> zabranjujemo pravo čitanja drugim korisnicima (grupi others)

Dodamo boba u grupu u kojoj je taj file, pa će dobiti prava od te grupe i moći će ga pročitati,

a to radimo kao administrator, te se ponovo sa bobon5 prijavimo, sada može pročitati sadržaj file-a:

**sudo usermod -aG alice5 bob5** —> dodamo usera bob5 u grupu alice5 kao administrator

Provjeravamo tko je član grupe: 

komanda —> **cat /etc/group | grep alice5**
ispis —> **alice6:x:1012:bob6**

Bob5 ne može čitati shadow —> 

**cat /etc/shadow - Permission denied**

 **getfacl /etc/shadow:**

```python
file: etc/shadow
owner: root
group: shadow

user::rw-
group::r--
other::---
```

Mičemo boba5 iz grupe alice5:

```python
sudo gpasswd -d bob5 alice5
```

## 3.dio

Getfacl i setfacl dodajemo u acl datoteke, dodamo novog korisnika.

sudo setfacl -m u:bob5:r /home/alice5/srp/security.txt —> dajemo pravo čitanja datoteke bobu5 kao useru.

Ponovimo komandu:

**getfacl /home/alice6/srp/security.txt**

Ispis:

```python
file: home/alice5/srp/security.txt
owner: alice5
group: alice5

user::rw-
user:bob5:r--
group::rw-
mask::rw-
other::---
```

te sad vidimo da i bob5 može pročitati sadržaj file-a.

**sudo groupadd alice_reading_group_5** —> kao administrator stvaramo grupu za čitanje alice5 file-ova

**sudo setfacl -m g:alice_reading_group_5:r /home/alice5/srp/security.txt** —> naredba sa kojom dajemo grupi pravo čitanja nad file-om security.txt

Ponovimo komandu **getfacl /home/alice6/srp/security.txt** da vidimo sada listu prava.

Ispis:

```python
file: home/alice5/srp/security.txt
owner: alice5
group: alice5

user::rw-
user:bob5:r--
group::rw-
group:alice_reading_group_5:r--
mask::rw-
other::---
```

te sad vidimo da je dodana grupa i da može pročitati sadržaj file-a.

## 4.dio

U pythonu napravimo skriptu: 

```python
import os

print('Real (R), effective (E) and saved (S) UIDs:')
print(os.getresuid())

with open('/home/alice6/srp/security.txt', 'r') as f:
print(f.read())
```

pokušamo u terminalu utipkati: **python [lab6karlo.py](http://lab6karlo.py/)**, a ispis je:

```python
Real (R), effective (E) and saved (S) UIDs:
(1000, 1000, 1000)
Traceback (most recent call last):
File "[lab6karlo.py](http://lab6karlo.py/)", line 6, in <module>
with open('/home/alice6/srp/security.txt', 'r') as f:
IOError: [Errno 13] Permission denied: '/home/alice6/srp/security.txt'
```

što znači da nemamo pristup jer smo administratori, a kada pokusamo sa komandom:  **sudo python [lab6karlo.py](http://lab6karlo.py/)** onda možemo pročitati te je ispis:

```python
Real (R), effective (E) and saved (S) UIDs:
(0, 0, 0)
Hello world
```

Kada pokušamo sa bobom5 napraviti isto, ispis je kao i kad pokušamo kao administratori, tj. uspješno smo pročitali jer bob5 ima prava.