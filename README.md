# Sistem za upravljanje liftom, SIL/HIL simulacija 

**Fakultet tehničkih nauka, Novi Sad**

Predmet: Upravljački algoritmi u realnom vremenu - Projektni zadatak br. 10

---

## ● O projektu

Aplikacija simulira upravljanje liftom u zgradi sa šest spratova, razvijena u LabVIEW okruženju kroz dve faze rada. U prvoj fazi (SIL - Software in the Loop) kompletan sistem: model lifta i upravljačka aplikacija, testiran je zajedno, softverski, na personalnom računaru. U drugoj fazi (HIL - Hardware in the Loop) sistem je raspoređen na dva fizički odvojena National Instruments kontrolera koji međusobno komuniciraju preko mreže: **cRIO**, na kom se izvršava upravljačka aplikacija, i **sbRIO**, na kom se izvršava model dinamike lifta.

Fizika lifta modelovana je kao sistem prvog reda, dok se pozicioniranje kabine ostvaruje preko PI regulatora, ugrađenog u klasičan automat stanja.

---

## ● Hardver i softver

- **cRIO** kontroler (National Instruments) - upravljačka aplikacija (automat stanja + regulator)
- **sbRIO** kontroler (National Instruments) - model dinamike lifta
- **LabVIEW** - razvojno okruženje
- **Network-Published Shared Variables** - komunikacija između kontrolera i korisničkog interfejsa

---

## ● Ključne funkcionalnosti

- **Model dinamike lifta**: diskretizovana jednačina sistema prvog reda (Formula Node), sa parametrima podešenim tako da odziv liči na realno kretanje kabine.
- **PI regulacija pozicije**: pojačanja Kp i Ki izvedena analitički, metodom postavljanja polova zatvorenog sistema.
- **Automat sa četiri stanja**: Idle, Moving, DoorOpen, Shutdown, sa Guard Clause mehanizmom koji garantuje bezbedno zaustavljanje sistema na Stop komandu ili grešku.
- **Raspodela na hardver**: upravljačka logika izvršava se na cRIO kontroleru, simulacija fizike lifta na sbRIO kontroleru, razmenjujući podatke isključivo preko mrežnih deljenih promenljivih.
- **Korisnički panel**: poziv lifta sa svakog sprata (gore/dole) i izbor destinacije iznutra, sa blokiranjem dugmadi koja u datom trenutku nemaju smisla (npr. izbor sprata pre nego što je lift uopšte pozvan, ili izbor u pogrešnom smeru u odnosu na poziv).
- **Vizuelna povratna informacija**: pokazivač trenutne pozicije, prikaz kretanja kroz spratove, LED signalizacija aktivnog stanja automata.

---

## ● Napomena o eksternim zavisnostima

Projekat koristi Regular Timer Action Engine VI, biblioteku razvijenu u okviru predmeta Upravljački algoritmi u realnom vremenu (UARV) na Fakultetu tehničkih nauka, Univerzitet u Novom Sadu. Zbog autorskih prava univerzitetskog materijala, ova komponenta nije uključena u repozitorijum.

---

## ● Pokretanje aplikacije

1. Povezati cRIO i sbRIO kontrolere (fizičko povezivanje uređaja preko odgovarajućih kablova/mreže).
2. U **NI MAX**-u proveriti da su oba uređaja prepoznata i dostupna na mreži.
3. Otvoriti LabVIEW projekat i, u Project Exploreru, dodati oba uređaja (cRIO i sbRIO) u stablo projekta.
4. Postaviti VI upravljačke aplikacije (automat) na **cRIO**, VI modela lifta na **sbRIO**.
5. Deploy-ovati biblioteku deljenih promenljivih (Shared Variable Library).
6. Pokrenuti VI-jeve redosledom: **model (sbRIO) → upravljačka aplikacija (cRIO) → korisnički panel (PC)**.

---

## ● Deljene promenljive

Komunikacija između cRIO, sbRIO i korisničkog panela ostvarena je isključivo preko mrežnih deljenih promenljivih, definisanih u zajedničkoj Shared Variable Library. Svaki VI pristupa potrebnoj promenljivoj tako što se ona direktno prevuče iz biblioteke na blok dijagram, nakon čega se, u zavisnosti od toga da li dati deo aplikacije treba da čita ili upisuje vrednost, čvor podesi u odgovarajući režim (Read/Write).
