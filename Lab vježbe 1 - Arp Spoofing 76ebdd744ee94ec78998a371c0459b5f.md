# Lab. vježbe 1 - Arp Spoofing

# Uvod

Prvom laboratorijskom vježbom demonstrirali smo ranjivost ARP protokola, koji mapira IP adrese s MAC adresama računala u mreži. Napadi koje smo izveli bili su man in the middle i denial of service napadi. 

U primjeru na slici, računalo 4 presreće komunikaciju između računala 2 i 5, odnosno predstavlja se kao računalo 5 da bi "prisluškivalo" komunikaciju među njima. 

# Postavljanje

Nakon pokretanja potrebnih servisa (Windows i Ubuntu terminali), kloniranjem github repozitorija:

```
git clone [https://github.com/mcagalj/SRP-2021-22](https://github.com/mcagalj/SRP-2021-22)
```

u direktoriju smo dobili '[start.sh](http://start.sh)' i '[stop.sh](http://stop.sh)' skripte za pokretanje (zaustavljanje) virtualnog mrežnog scenarija. Pokrećemo docker:

```
docker exec -it <station-name> bash 
```

Nakon što smo saznali IP i MAC adrese svakog računala u mreži pomoću naredbe 

```
ifconfig -a
```

saznajemo sljedeće podatke o trima računalima koje koristimo:

**station-1**

 `IP: 172.22.0.2
 
 MAC: 02:42:ac:16:00:02`

**station-2**

 `IP: 172.22.0.4
 
 MAC: 02:42:ac:16:00:04`

**evil-station**

 `IP: 172.22.0.3
 
 MAC: 02:42:ac:16:00:03`

# Izvođenje

Uspostavljanjem veze između station-1 i station-2 pomoću netstata, simuliramo "razgovor" između dva računala. Kako bi napadač evil-station mogao "prisluškivati", pokrećemo naredbu

```
tcpdump
```

odnosno  za ARP spoofing

```
arpspoof -t station-1 -r station-2
```

čime evil-station ima uvid u komunikaciju koja se odvija između station-1 i station-2. To smo postigli mijenjanjem MAC adrese evil-stationa u MAC adresu station-2 (`02:42:ac:16:00:04`). 

Na kraju, da bismo napravili denial of service napad koristili smo naredbu 

```
echo 0 > /proc/sys/net/ipv4/ip_forward
```

čime smo spriječili komunikaciju između station-1 i station-2.
