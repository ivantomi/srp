# Lab. vježbe 6 - Linux permissions and ACLs

# Uvod

Cilj vježbe bio je upoznati se s načinom upravljanja korisničkim računima na Linux-u, a pogotovo na kontroli pristupa datotekama i ostalim resursima Linux-a.

# Provedba

U Linuxu svaka datoteka ili program imaju vlasnika kojem je dodijeljen jedinstveni User ID, a svaki korisnik mora pripadati barem jednoj grupi (svaka grupa također ima jedinstveni Group ID), pri čemu može biti više korisnika u svakoj grupi. 

Kako bismo mogli kreirati dva nova korisnika (u našem slučaju alice5 i bob5), morali smo imati administratorska prava (tj. morali smo biti član grupe sudo) i to smo učinili na sljedeći način:

```bash
sudo adduser alice5
sudo adduser bob5
```

Nakon što smo se ulogirali kao neki korisnik (npr. alice5), kreirali smo direktorij i dokument security.txt s proizvoljnim tekstom:

```bash
mkdir

echo "Hello world" > security.txt

cat security.txt
```

Korištenjem naredbi *ls -l* i *getfacl*, možemo dobiti informacije o direktoriju i datoteci, odnosno možemo vidjeti vlasnike resursa, kao i dopuštenja definirana na njima:

```bash
ls -l srp
ls -l srp/security.txt

getfacl srp
getfacl srp/security.txt
```

Ako vlasniku (u našem slučaju alice5) oduzmemo pravo čitanja dokumenta naredbom:

```bash
chmod u-r security.txt
```

svi ostali korisnici (za naš primjer to je bob5) i dalje imaju pravo čitanja. 

Kako bismo postigli isti rezultat, a bez da oduzimamo dopuštenja čitanja vlasniku, upisujemo naredbu:

```bash
chmod u-x security.txt
```

čime smo vlasniku zabranili ulazak u direktorij. 

Ako želimo da samo jedan od korisnika (npr. bob5) dobije prava, a ostali korisnici ne, potrebno je dodati bob5 u grupu u kojoj se nalazi alice5 naredbom:

```bash
usermod -aG alice5 bob5
```

Zatim smo pokušali pristupiti datoteci */etc/shadow,* u kojoj Linux pohranjuje hash vrijednosti lozinki. Budući da nijedan od naših korisnika ne pripada grupi shadow, ne možemo pristupiti toj datoteci, osim ako ih ne pridružimo u grupu. 

Kako bismo mogli pregledati i modificirati ACL resurse koristimo naredbe getfacl i setfacl. Ne moramo više dodavati pojedine korisnike u grupe, već možemo jednostavno dodati novog korisnika u ACL datoteke security.txt s read ovlastima:

```bash
setfacl -m u:bob5:r security.txt
```

Konačno, proučili smo način funkcioniranja Linux procesa - programa koji se trenutno izvršavaju u odgovarajućem adresnom prostoru. Svaki proces ima svog vlasnika i jedinstveni identifikator procesa.

Kako bismo demonstrirali da neki novi korisnik (npr. bob5) nema pristup sadržaju datoteke security.txt, kreirali smo Python skriptu:

```python
import os

print('Real (R), effective (E) and saved (S) UIDs:') 
print(os.getresuid())

with open('/home/alice5/srp/security.txt', 'r') as f:
    print(f.read())
```

Error koji smo dobili kaže *permission denied*, što je i očekivano jer bob5 nema prava nad datotekom. Ako se prijavimo kao alice5, neće biti nikakvih problema, budući da ona kao vlasnik ima pravo čitanja.