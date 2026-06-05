# DEV 5 Bezpečný kód

> [!abstract] Metody autentizace a řízení přístupu. Biometrické metody autentizace, jejich dopady a problémy. Elektronický podpis a jeho použití. Autentizace strojů a aplikací. Zásady a principy bezpečného kódu. Typické bezpečnostní chyby na úrovni kódu, souběžnost, ošetření vstupů. Detekce bezpečnostních zranitelností, penetrační testování. Příklady z praxe pro vše výše uvedené. (PV157, PV286 || PV017 || PV276)

> [!tip] Jak se na to ptá komise (souhrn z reportů)
> 
> - **Vždy chtějí rozlišit autentizaci × autorizaci × řízení přístupu** a 4 faktory autentizace.
> - U **biometrik** chtějí: typy, FAR/FRR (umět nakreslit graf!), živost, hlavní nevýhoda (nejde změnit, není tajná), multimodalita.
> - **Digitální/elektronický podpis** je nejčastější dotaz vůbec: jak funguje (hash → podpis privátním klíčem), proč se hashuje, vlastnosti hashe, PKI, CA, ověření platnosti certifikátu, **revokace**, co dělat při kompromitaci privátního klíče.
> - **Bezpečný kód:** typické chyby (SQLi, buffer overflow, XSS), **stack vs heap**, souběžnost (race condition), ošetření vstupu, statická × dynamická analýza, penetrační testování.
> - Zkoušející typicky **napovídají**, chtějí příklady z praxe, ocení nakreslené schéma.

---

## 0. CIA triáda — základní rámec bezpečnosti

> [!important] CIA triáda (komise s tím často otevírá — Macák 2026) Tři základní vlastnosti bezpečného systému; vše ostatní v této otázce slouží k jejich zajištění.
> 
> |Vlastnost|Co znamená|Jak zajistit|Útok narušující|
> |---|---|---|---|
> |**Confidentiality** (důvěrnost)|data vidí jen oprávnění|šifrování, řízení přístupu, autentizace|odposlech, únik dat, eskalace práv|
> |**Integrity** (integrita)|data nelze nepozorovaně změnit|hashe, MAC/HMAC, digitální podpis|modifikace, MiTM, tampering|
> |**Availability** (dostupnost)|systém je dostupný oprávněným|redundance, zálohy, rate-limiting|DoS/DDoS, ransomware, výpadek|

Často se doplňuje o **autenticitu** (ověření původu — autentizace, podpis) a **nepopiratelnost** (non-repudiation — digitální podpis, audit logy). Mapování na hrozby viz **STRIDE** v §8.5.
## 1. Metody autentizace

### 1.1 Čtyři faktory autentizace

Autentizace = **ověření identity** entity. Cíl: co nejjednodušší pro oprávněné, co nejtěžší pro neoprávněné. Na autentizaci navazuje **řízení přístupu** (access control).

|Faktor|Princip|Příklad|
|---|---|---|
|**Něco, co známe**|znalost|heslo, PIN, passphrase|
|**Něco, co máme**|vlastnictví tokenu|čipová karta, klíč, autentizační kalkulátor, telefon|
|**Něco, co jsme**|inherence / biometrika|otisk prstu, duhovka, hlas|
|**Někde, kde jsme**|kontext / lokalita|GPS, IP, blízkost zařízení|

> [!note] Vícefaktorová autentizace (MFA/2FA) Kombinace **různých** faktorů. Pozor: PIN + heslo NENÍ 2FA (oboje „co známe"). Token + PIN je 2FA. Třífaktorová = token + PIN + biometrika. Klasická chyba u zkoušky (Bartek 2019): při popisu hesla zapomenout, že lze kombinovat s dalším faktorem.

### 1.2 Autentizace tajnou informací — hesla a PINy

**Tři kategorie tajemství:**

1. Krátké/slabé (pro lidi) — hesla, PINy.
2. Dostatečně dlouhé — kryptografické klíče → protokoly **výzva–odpověď**.
3. Protokoly s nulovým rozšířením znalosti (**zero-knowledge**).

**Požadavky na bezpečnou tajnou informaci:**

- musí být **opravdu tajná** (ne jméno psa, datum narození),
- vybraná z **velkého prostoru** hodnot,
- pravděpodobnost všech hodnot pokud možno **rovnoměrná**,
- při kompromitaci musí jít **nastavit novou** (klíčová vlastnost — biometrika tohle nesplňuje!).

**Ukládání hesel** (od nejhoršího k nejlepšímu):

- **Otevřený tvar** — NE (důvěra v admina, kopie souboru).
- **Šifrovaná** — ochrana souboru → ochrana klíče, klíč musí být dostupný systému. Použití jen když potřebujeme otevřený tvar hesla.
- **Hashovaná** — ✅ standard. Ukládá se jen výsledek hashe.
    - **Jednosměrnost** hashe — z hashe nezískám heslo.
    - **Pomalá funkce** (key stretching) — ztížení útoku hrubou silou (bcrypt, scrypt, Argon2, PBKDF2).
    - **Sůl (salt)** — náhodná hodnota přidaná k heslu, ukládá se `userid, sůl, f(sůl,heslo)`. Řeší: stejná hesla → různé haše, brání rainbow tables, prodlužuje efektivní heslo.
    - **Pepř (pepper)** — _tajná_ sůl uložená odděleně (např. v aplikaci/HSM), ne v DB.

> [!example] UNIX/Windows ukládání hesel
> 
> - **UNIX:** dříve `crypt` (modifikovaný DES, jen 8 znaků!). Dnes `$6$sůl$haš` = SHA-512 (`$1`=MD5, `$5`=SHA-256, `$6`=SHA-512), v `/etc/shadow`.
> - **Windows:** LM hash (starý, slabý — uppercase, 14 znaků děleno na dvě 7-znakové půlky → snadný útok), NT hash (MD4 nad Unicode), dnes **NTLMv2**.

**Komunikace heslem:** neposílá se heslo, ale jeho **haš** (kdo odposlechne, nezíská heslo přímo — ale haš lze zneužít pro „pass-the-hash"). Lépe: **challenge-response** (Alice pošle náhodné číslo, Bob ho podepíše/zahešuje s tajemstvím — odposlech výzvy i odpovědi útočníkovi nepomůže).

**Útoky na hesla:**

- **Cílený × plošný** (konkrétní uživatel × kdokoli ze skupiny).
- **Online** (tipy na verifikátor — lze zpomalovat, limit pokusů, 3 pokusy u bankomatu) × **offline** (útočník má soubor s hesly).
- **Hádání:** slovníkový útok, permutace (l33t náhrady I→1, S→5), uživatelské info, hrubá síla, **rainbow tables**, paralelizace.
- Nástroje: **John the Ripper, hashcat, RainbowCrack, Aircrack-NG**.
- **Obnova hesla** — útok na slabší „záložní" autentizaci (reset přes e-mail/SMS).
- **Krádež** — keylogger, malware, sociální inženýrství, kamera, odposlech.

**Kvalitní hesla:** lehce zapamatovatelné, těžko uhodnutelné → **passphrase** (`PlSeMk,VTCO;o24zI`). Výzkum: vynucená expirace hesel je **málo účinná** (lidé volí slabší hesla, MojeHeslo09→MojeHeslo10). Lepší investice = jednorázová volba kvalitního hesla + **blacklist** (slovníky, prozrazená hesla, haveibeenpwned). Pro běžné uživatele je vhodný **správce hesel**.

### 1.3 Autentizace tokenem (něco, co mám)

**Dilema:** cena výroby (co nejnižší, klesá v sériích) × cena padělání (co nejvyšší). Padělání závisí na motivaci útočníka, době přístupu k originálu, legislativním postihu.

**Typy tokenů v IT:**

- **Karta s magnetickým proužkem** — ~250 B, snadno se kopíruje, falešné bankomaty, „libanonská smyčka".
- **Čipové karty:**
    - Paměťové (chipcard) / paměťové s logikou / **procesorové (smartcard)**.
    - **Kontaktní** (čtečka napájí) × **bezkontaktní** (omezený výkon, operace bez vědomí uživatele; elektronické pasy).
    - Logická struktura souborů: **MF** (master file = kořen), **DF** (dedicated = adresář), **EF** (elementary = soubor).
    - **Aktivní prvek:** může počítat krypto operace; **privátní klíč nikdy neopustí kartu** (podpis se vytváří na kartě). Symetrické algoritmy snadno, asymetrické vyžadují koprocesor.
- **Autentizační kalkulátory** (s tajemstvím, s hodinami → TOTP).

**PIN management:** PINy v samostatných EF, počet pokusů omezen (3–5) → blokace → odblokování přes **PUK** (u SIM). I počet odblokování omezen. Analýza 3,4 mil. PINů: nejčastější `1234` (10,7 %), `1111`, `0000`. PIN lze odpozorovat (kamera, teplo z kláves — pokus na FI: 35 % s krytem, 80 % bez krytu PINpadu).

**Bezpečnost karet:**

- **Fyzická bezpečnost** — překážky proti přístupu.
- **Tamper resistance** (odolnost vůči narušení) — chrání proti modifikaci.
- **Tamper evidence** (zjistitelnost narušení) — narušení je viditelné.

---

## 2. Řízení přístupu (Access Control)

Funkce řídící, který **subjekt** (uživatel, proces) má jaký přístup k jakému **objektu** (soubor, DB, tiskárna). Vychází z bezpečně implementované **identifikace & autentizace**, prosazuje ji **referenční monitor**.

**Pojmy:** vlastník (owner), správce (custodian), uživatel (user). **Typy práv k objektu:** `R` (read), `W` (write), `RW`, `X` (execute) + omezení časem/místem/obsahem/typem služby. **Správa:** centralizovaná (1 správce, konzistence, vysoká režie) × decentralizovaná (vlastník = správce, vysoká odpovědnost, hůře se prosazuje politika).

### 2.1 Matice přístupových práv a její reprezentace

Matice subjekty × objekty (i 3D s rozměrem „program"). V praxi obrovská a řídká → ukládáme po řádcích nebo sloupcích:

|Reprezentace|Ukládá matici po|Výhoda|Nevýhoda|
|---|---|---|---|
|**ACL** (Access Control List)|objektech|snadno zjistím, kdo má přístup k objektu; omezení na jednotlivce|těžko zjistím, k čemu má přístup uživatel|
|**C-list** (Capabilities)|subjektech|efektivní kontrola při přístupu|těžko zjistím, kdo má přístup k objektu (dnes často certifikáty)|

> [!example] Implementace ACL
> 
> - **UNIX klasický:** `rwx` pro vlastníka/skupinu/ostatní. Problém: nelze zakázat přístup jednotlivci ze skupiny; nelze zajistit důvěrnost. **SUID/SGID** bity → program běží s právy vlastníka (např. `passwd` zapisuje do `/etc/shadow`). SUID programy musí být psány bezpečně (vstupy nedůvěryhodné!). Příznaky `chattr`: `i` (immutable), `a` (append-only — vhodné pro logy).
> - **POSIX ACL:** `getfacl`/`setfacl`, výjimky pro `ACL_USER`, `ACL_GROUP`, maska.
> - **Linux Capabilities:** rozmělnění práv roota (`CAP_NET_BIND_SERVICE`, `CAP_CHOWN`…).
> - **Windows:** jemnější práva, dědičnost, Povolit/Odepřít + auditování, UAC.

### 2.2 Politiky řízení přístupu

> [!important] DAC × MAC × RBAC
> 
> - **DAC (volitelné, discretionary):** o přístupu rozhoduje **vlastník** objektu. Jednoduché, flexibilní, ale **neodolné Trojským koním** (systém se nestará o další využití dat). Typicky podporováno OS.
> - **MAC (povinné, mandatory):** rozhoduje **systémová politika** nezávisle na vůli subjektů. Kategorie subjektů (důvěryhodnost) × klasifikace objektů (důvěrnost). Bezpečnější, ale složité a drahé na správu.
> - **RBAC (role-based):** samostatná kategorie. Uživatelům přiřazeny **role**, role = sady práv (jemnější než ACL). Role jsou specifické pro každý IS. (Macák 2026 chtěl: role-based + ACL + matice oprávnění + typy přístupu r/w/rw/x.)

### 2.3 Bell-LaPadula (důvěrnost) a Biba (integrita)

**Bell-LaPadula (BLP)** — primární MAC model (1973, US Air Force), chrání **důvěrnost**.

- Bezpečnostní úroveň `L = (C, S)`: `C` = klasifikace (TS > S > C > U), `S` = množina kategorií.
- **Dominance:** `L1 ≥ L2 ⟺ C1 ≥ C2 ∧ S1 ⊇ S2`.
- Stav systému `S = (b, M, f)`: aktivní přístupy, matice práv, úrovňová funkce.

> [!important] Dva axiomy BLP
> 
> - **ss-property (No Read Up, NRU):** subjekt nesmí číst data na **vyšší** úrovni, než je jeho oprávnění.
> - ***-property (No Write Down, NWD):** subjekt nesmí zapisovat data na **nižší** úroveň. (Brání úniku přes Trojského koně.)
> - - **discretionary property** (musí mít právo i v matici M). Stav je bezpečný, jsou-li splněny všechny tři.

**Biba** — „převrácený" BLP, chrání **integritu**: číst lze jen data **vyšší** úrovně (spolehlivější), zapisovat jen „dolů". (Win Vista+ integritní úrovně: Untrusted < Low < Medium < High < System < Installer; nižší nemodifikuje vyšší.)

**Problémy MLS:** jak klasifikovat data, tendence k příliš striktní klasifikaci, drahý vývoj, snížení klasifikace, propojení neekvivalentních systémů (USA × UK). **Skrytý kanál (covert channel):** zneužití sdíleného prostředku ke komunikaci mezi úrovněmi (zaplnění disku, zátěž CPU, zamykání souborů). Obrana: snížení šířky pásma, šum, kvóty. **Skutečné MLS systémy:** SELinux (vyvinuto s NSA, trojice `identita:role:typ` + MLS, enforcing × permissive), AppArmor, Trusted Solaris, TrustedBSD.
### 2.4 Principy řízení přístupu (klíčové pro „bezpečný kód")

- **Princip nejmenších privilegií** (least privilege) — subjekt má jen práva nezbytná pro svoji roli.
- **Default deny** — povoleno jen to, co je explicitně nastaveno (typicky firewall/iptables; blacklisty jsou náchylné na chyby, lepší whitelist).
- **Separace oprávnění** (separation of duties) — akci musí schválit více osob (velká bankovní transakce = 2 úředníci); často nutná podpora až v aplikaci.
- **Spouštění nedůvěryhodného kódu:** sandbox (JVM applety), minimální práva (`nobody`), autentizace kódu (Authenticode), kontrola/heuristiky (obecně nerozhodnutelné).
- **HW podpora:** režimy procesoru (ringy ochrany — změna jen v ring 0), fence/segmentové adresování, DEP/ASLR (viz §6).

---

## 3. Biometrické metody autentizace, dopady a problémy

> [!definition] Biometriky = automatizované metody identifikace/ověření identity na základě měřitelných **fyziologických** nebo **behaviorálních** vlastností člověka.

### 3.1 Režimy a proces

- **Verifikace (1:1)** — identita známa, ověřujeme ji.
- **Identifikace (1:n)** — identita neznámá, prohledáváme celou DB (náročnější; clustering).
- **Proces:** 1) **Registrace** (enrollment) — prvotní nasnímání, kontrola kvality, vytvoření a uložení registračního vzorku. 2) **Verifikace/identifikace** — nové snímání + srovnání.

> [!warning] Zásadní rozdíl oproti heslu/tokenu — VARIABILITA Biometrická data **nikdy nejsou 100% shodná** → musíme povolit určitou míru tolerance. 100% shoda = buď útok (útočník má registrační data) nebo chyba snímače. To je zdroj chybovosti.

### 3.2 Chyby biometrických systémů (komise se ptá VŽDY)

> [!important] FAR / FRR / EER
> 
> - **FAR (False Acceptance Rate)** = nesprávné **přijetí** = chyba I. druhu = přijmu podvodníka (bezpečnostní riziko).
> - **FRR (False Rejection Rate)** = nesprávné **odmítnutí** = chyba II. druhu = odmítnu oprávněného (komfortní problém, naštvaní uživatelé).
> - **EER (Equal Error Rate)** = bod, kde FAR = FRR (společná metrika porovnání).
> - Nastavením prahu se FAR a FRR vyvažují: přísnější práh → nižší FAR, vyšší FRR.
> - **FTE** (Failure To Enroll) — nepodařilo se registrovat (poškozený/chybějící orgán). **FTA/FTC** (Failure To Acquire/Capture) — nepodařilo se sejmout při použití.
> - FPIR = FAR, FNIR = FRR + FTA + jiné.
> - ⚠️ Samotné FAR/FRR bez znalosti podmínek testu **neříkají nic** a nemusí být srovnatelná.
### 3.3 Typy biometrik

**Dělení:** fyziologické (statické: otisk, geometrie ruky) × behaviorální (dynamické, vyžadují akci: podpis, hlas). Genotypické (DNA) × fenotypické (otisk — ovlivněn vývojem).

| Biometrika                       | Přesnost                  | Poznámka                                                                                           |
| -------------------------------- | ------------------------- | -------------------------------------------------------------------------------------------------- |
| **Otisk prstu**                  | FAR ~0,1 % při FRR 2–20 % | nejstarší, snímače optické/kapacitní/ultrazvukové/tepelné; markanty (ISO 19794-4); možnost podvodu |
| **Geometrie ruky**               | FAR i FRR 3–5 %           | málo přesné, není jedinečné, nevhodné pro identifikaci (OH Atlanta 1996)                           |
| **Oční duhovka**                 | FAR ~0 při FRR ~3 %       | velmi přesné, vhodné pro identifikaci, IrisCode 256 B, miliony srovnání/s                          |
| **Oční sítnice**                 | nízké FAR, vyšší FRR      | cévy, IR světlo, 96 B, nepříjemné snímání                                                          |
| **Rozpoznání obličeje**          | FAR/FRR jednotky %        | mění se v čase, problém osvětlení/pozadí; výrazné zlepšení v posl. letech                          |
| **Hlas**                         | FAR/FRR <2 % ideálně      | ovlivněno šumem; možnost kontinuální + challenge-response                                          |
| **Dynamika podpisu**             | FAR/FRR desítky %         | nízká přesnost, ale populární (historický význam podpisu)                                          |
| **Dynamika psaní na klávesnici** | —                         | bez speciálního HW, **kontinuální autentizace**                                                    |

### 3.4 Dopady a problémy (klíčová část otázky!)

> [!warning] Hlavní poznatky o biometrikách
> 
> 1. **Nejsou tajné** — obličej, hlas, otisky necháváme všude. Kopírování není triviální, ale ani obtížné.
> 2. **Nejdou změnit** — při kompromitaci nelze „resetovat" jako heslo (Batko 2021 to chtěl slyšet!). Jedna charakteristika navíc bývá použita ve více systémech.
> 3. **Citlivé osobní údaje** — GDPR (zvláštní kategorie), ochrana soukromí.
> 4. **Spolehlivost** — nelze zapomenout/ztratit/snadno předat.
> 5. **Nikdy ne 100% bezchybné** — použití nemusí automaticky znamenat vyšší bezpečnost.
> 6. **Nová ochrana → nové útoky** (bezpečnostní „klasika").

**Živost (liveness):** Pochází vzorek od **živé** osoby a od **té správné**? Testování živosti → větší senzor, vyšší cena, vyšší FRR, žádná metoda není 100% (Matsumoto 2002: gumové prsty z želatiny obelstily snímače).

**Forenzní × komerční/IT:** komerční = nízká přesnost, automatizované, miniaturizace, levné, ukládá jen charakteristiky, výsledek v sekundách. Forenzní = vysoká přesnost, manuální zásah expertů, ukládá i původní vzorky, registrace nelze opakovat, výsledek i dny.

**Biometriky a kryptografie:** generovat krypto klíče z biometrik je v podstatě **nesmysl** (omezený prostor, není tajné, problém s prozrazením/změnou). Správná role: biometriky chrání **přístup k tajnému klíči** (ideálně + tajná informace), autentizují **uživatele**, ne počítač/data. Nejlépe jako **doplňková** metoda.

**„Biometrický podpis"** — víc variant, nutno se ptát na detaily: (1) podpis na _vlastním_ tabletu v důvěryhodné aplikaci → dynamika + dokument se podepíší privátním klíčem. (2) podpis na _cizím_ tabletu = uživatel neví, co se děje s daty → problém.

---

## 4. Elektronický podpis a jeho použití

### 4.1 Jak digitální podpis funguje

> [!important] Princip (komise chce slyšet přesně) **V teorii:** `Privátní klíč + dokument → podpis`; `Veřejný klíč + podpis + dokument → ano/ne`. **V realitě:**
> 
> 1. Dokument se **zahashuje** (proč? rychlost — asymetrika je pomalá; pevná délka; nepodepisuje se celý soubor).
> 2. Hash se **zašifruje privátním klíčem** podepisujícího → digitální podpis.
> 3. Ověření: příjemce **dešifruje podpis veřejným klíčem** → získá hash, sám si **spočítá hash** dokumentu a **porovná**. Zajišťuje: **integritu** (změna dat → jiný hash), **autenticitu** (jen vlastník privátního klíče), **nepopiratelnost** (non-repudiation). ⚠️ „Ve skutečnosti nepodepisuje člověk, ale **počítač**" — privátní klíč je v PC/kartě, odemyká se PIN/heslem; trojský kůň nebo admin může klíč zneužít při použití.

**Vlastnosti hashovací funkce** (Zlatuška se ptal, pozor!):

- jednosměrnost (preimage resistance),
- odolnost vůči 2. vzoru (second-preimage),
- bezkoliznost (collision resistance) — _pozn.: Zlatuška 2023 odmítal frázi „ideálně bezkolizní"; lépe mluvit o 1. a 2. preimage_,
- pevná délka výstupu, lavinový efekt.

**Hashování s klíčem × bez klíče** (Krhovjak 2021): bez klíče = běžný hash (SHA-2/3); s klíčem = **MAC/HMAC** (autenticita + integrita pomocí sdíleného tajemství).

### 4.2 PKI, certifikáty, CA

> [!important] PKI (Public Key Infrastructure)
> 
> - **Veřejný klíč je kritický pro ověření** — musí být zajištěna jeho **integrita** → **certifikát veřejného klíče** (Matyášova zkušební otázka: integritu veřejného klíče zajistíme certifikátem).
> - **Certifikační autorita (CA)** podepisuje certifikáty (váže identitu na veřejný klíč). CA tvoří **strom autorit** (root CA → intermediate → koncové). Každý ověřovatel důvěřuje root CA.
> - **RA (Registration Authority)** — ověřuje totožnost žadatele před vydáním certifikátu.
> - **Ověření platnosti certifikátu** (Ošlejšek/Přenosil to chtěli!): platná podpisová cesta k důvěryhodné CA + **kontrola, že certifikát nevypršel a nebyl revokován**.
> - **Revokace:** **CRL** (Certificate Revocation List) nebo **OCSP** (online dotaz na stav).

> [!warning] Kompromitace privátního klíče (Plhák/Batko/Plhák 2026)
> 
> - Pokud unikne **privátní klíč uživatele** → útočník může podepisovat jeho jménem → nutno klíč **revokovat** (zneplatnit certifikát), vydat nový pár, dříve vytvořené podpisy zpochybněny.
> - Pokud unikne **privátní klíč CA** → útočník může vydávat **falešné certifikáty** komukoli → kompromitace celé hierarchie pod CA (katastrofa).

### 4.3 Právní rámec — eIDAS (PV017/PV157 L01)

Nařízení **eIDAS** (EU 910/2014, nahradilo směrnici 99/93/ES a zákon o el. podpisu; nyní **eIDAS 2.0** + evropská peněženka digitální identity). Tři oblasti: el. identifikace (eID), služby vytvářející důvěru (podpis, **pečeť**, časové razítko, doporučené doručování, autentizace webů), el. dokumenty.

> [!important] Úrovně elektronického podpisu (čl. 3 eIDAS)
> 
> |Úroveň|Charakteristika|Právní účinek|
> |---|---|---|
> |**(Prostý) elektronický podpis**|data v el. podobě připojená k jiným datům, použitá k podepsání (i podpis v e-mailu)|nesmí být upírán právní účinek jen kvůli el. podobě|
> |**Zaručený (AdES), čl. 26**|jednoznačně spojen s podepisujícím, umožňuje identifikaci, vytvořen pod **výhradní kontrolou** podepisujícího, detekuje následnou změnu dat|„něco mezi"|
> |**Kvalifikovaný (QES), čl. 28**|zaručený + vytvořen **kvalifikovaným prostředkem (QSCD)** + založen na **kvalifikovaném certifikátu**|rovnocenný vlastnoručnímu podpisu|
> 
> **Uznávaný el. podpis** (ČR specifikum) = zaručený podpis založený na kvalifikovaném certifikátu (nebo QES) — používán pro komunikaci s orgány veřejné moci. **Elektronická pečeť** = obdoba podpisu pro **právnické osoby**. **Elektronické časové razítko** = důkaz existence dat v čase.


## 5. Autentizace strojů a aplikací

### 5.1 Stroje a zařízení

- **Challenge-response se sdíleným tajemstvím** — GSM/SIM: tajný symetrický klíč `Ki` v SIM i v AuC operátora; operátor pošle náhodné `RAND`, funkce A3 vypočte `SRES` na obou stranách a porovná → 2FA (SIM = token + PIN).
- **Vzájemná autentizace** přes certifikáty / TLS handshake (autentizace serveru, příp. i klienta klientským certifikátem).
- **HSM (Hardware Security Module)** — bezpečné úložiště klíčů a krypto operací; banky si nedůvěřují → HSM + 3DES klíče mezi přepínači.

### 5.2 EMV — platební karty (příklad z praxe)

Standard **EMV** (Europay, MasterCard, VISA). **Offline autentizace dat** detekuje padělané karty pomocí RSA + PKI (terminál má veřejný klíč CA, ta certifikuje banky):

- **SDA (statická):** podepsaná statická data v kartě; detekuje změnu po personalizaci, ale **neřeší kopírování karty**.
- **DDA (dynamická):** karta má vlastní RSA pár (privátní klíč nikdy neopustí kartu), challenge-response s náhodnou výzvou terminálu → **detekuje padělky/duplikáty**.
- **CDA (kombinovaná):** DDA + aplikační kryptogram.

**Online verifikace PINu:** PIN se zašifruje do **PIN-bloku**, přenáší se mezi bankami, na přepínačích se přešifrovává; originální PIN se v bance negeneruje při registraci, ale počítá z čísla účtu + tajného klíče v HSM.

### 5.3 Autentizace aplikací a vývojářů (PV286 secure coding)

> [!tip] Nejlepší rada z praxe **NEDĚLEJTE si vlastní autentizační systém!** Použijte existující protokoly a služby.

- **OpenID / OIDC** — autentizace („kdo jsem", Sign in via Google). OIDC = OpenID Connect nad OAuth 2.0.
- **OAuth 2.0** — autorizace („aplikace jedná mým jménem", post na Twitter).
- **SAML** — XML standard pro výměnu autentizačních/autorizačních dat (assertions: Subject, AuthnStatement, AttributeStatement, AuthorizationStatement). Omezení: jen prohlížeč, ne mobilní/REST API.
- **SSO** (Single Sign-On) — přes OpenID nebo SAML.
- **API tokeny / JWT** — token nese identitu/oprávnění; pozor: lockdown účtu musí zneplatnit i tokeny.
- **MFA/2FA**, role-based model (admin × user × support), impersonace, reset hesla.

> [!warning] Vývojář = vysoce hodnotný cíl Má root na serverech, R/W do DB, přístup ke zdrojákům, CI pipeline, nasazením, releasům, 3rd party API (platební brány), kopie dat. Časté chyby: sdílené API klíče, žádná access policy, žádné audit logy, tajemství v config souborech. Řešení: **secret management** (HashiCorp Vault / OpenBao) — dočasná tajemství, automatická expirace/revokace, audit logy, role & policies.

### 5.4 Autentizační protokoly (PV157 L08)

> [!definition] **Protokol** = vícestranný algoritmus daný posloupností kroků. **Autentizační protokol** dává jedné (nebo oběma) stranám jistotu o identitě protistrany. **Protokol pro ustavení klíče** ustaví sdílené tajemství; **autentizovaný** navíc s ověřenou identitou.

**Naivní autentizace heslem/hašem a její útoky:**

- **Impersonace** — Emil odposlechne heslo a vydává se za Alici.
- **Útok přehráním (replay)** — Emil nezná heslo, ale přehraje odposlechnutý **haš** (proto haš sám nestačí).
- **Útok prolínáním (interleaving)** — Eva kombinuje zprávy z více běhů protokolu a vydává se za Alici vůči Bobovi.
- **Slovníkový** (na hesla), **known-key** (na ustavení klíče ze starých klíčů).

> [!important] Challenge-response (výzva–odpověď) Bob ověří identitu Alice **bez získání jejího tajemství**. Odposlech výzvy i odpovědi útočníkovi nepomůže. Proti replay slouží **časově proměnné parametry**:
> 
> - **Náhodné číslo (nonce, `r`)** — nepredikovatelné, zajišťuje čerstvost.
> - **Sekvenční číslo (`n`)** — monotónně rostoucí, detekuje přehrání.
> - **Časové razítko (`t`)** — vyžaduje synchronizované hodiny (jinak útok změnou hodin).
> 
> Techniky: **symetrické** (šifrování `Eₖ`, MAC `hₖ` — ISO 9798-2/4, protokoly SKID), **asymetrické** (dešifrování privátním klíčem, **digitální podpis** — ISO 9798-3). Příklad oboustranné: `A←B: r_B`; `A→B: certA, r_A, "B", S_A(r_A, r_B, "B")`; `A←B: certB, "A", S_B(r_B, r_A, "A")`.

> [!important] Kerberos (síťová autentizace, projekt Athena, MIT) Symetrická šifra + **důvěryhodná třetí strana** (autentizační server T). Každá strana sdílí klíč jen se serverem (`K_AT`, `K_BT`), klienti mezi sebou klíče nesdílí.
> 
> - **KDC** (Key Distribution Center) — server generuje a distribuuje klíče. **KTC** (Key Translation Center) — klíč dodá jedna strana, server jen distribuuje.
> - **Ticket** = `E_{K_BT}(k, "A", L)`, **authenticator** = `E_k("A", T_A)`, `L` = doba platnosti.
> - Zjednodušeně: (1) `A→T: "A","B",n_A` → (2) `A←T: ticketB, E_{K_AT}(k, n_A, L, "B")` → (3) `A→B: ticketB, auth` → (4) `A←B: E_k(T_A)`.
> - Cíl: autentizace A vůči B + ustavení relačního klíče `k` (volí T).
---

## 6. Zásady a principy bezpečného kódu

### 6.1 Co znamená bezpečnost software (PV286)

- **Safety × Security:** safety = náhodné chyby (CRC stačí); security = **úmyslný útočník** (přepočítá CRC po změně).
- **Security je proces, ne produkt** → **Secure Development Lifecycle (SDL)** (Microsoft), Seven Touchpoints (Cigital).
- **Defensive programming** (Kernighan & Plauger 1981) — „psát program tak, aby zvládl malé katastrofy"; chyby jsou nevyhnutelné. Cílí hlavně na neúmyslné chyby.
- **„Security features ≠ Secure features"** — psaní bezp. funkcí je jen ~10 % práce; 90 % je zajistit, aby zbytek kódu byl bezpečný.
- **„Secure software does what it is supposed to do, and nothing else."** (Ivan Arce)
- **Usability:** snadné použít správně, těžké zneužít (API security, secure defaults, limit dopadu při chybě).

> [!important] Klíčové principy
> 
> - **Princip nejmenších privilegií, default deny, separace oprávnění, defense in depth** (více vrstev obrany).
> - **Ošetření vstupu** — veškerý vstup je nedůvěryhodný (parametry, stdin, env proměnné, síť, soubory). Validace, sanitizace, **whitelist** (ne blacklist).
> - **Nevynalézat kolo** — nepsat vlastní krypto/auth knihovny (opakování cizích chyb). Použít prověřené (libsodium `crypto_secretbox_easy()` místo vlastního kódu).
> - **Secure by default** jazyky (Rust, Go) a hardened funkce (`gets_s` místo `gets`, smart pointery v C++, `Math.addExact` v Javě, Secure C library ISO/IEC 9899:2011).
> - **Coding standards** (CERT C Coding Standard).
> - **Testovatelnost** — „kód, který není testován, nefunguje" (Kent Beck); dekompozice, unit testy, mock objekty.
> - **Software supply chain** — monitorovat závislosti (Dependabot), reproducible builds; útoky: typosquatting, dependency confusion, malicious dependency, package/account takeover (Axios 2026, npm postinstall hooky).

### 6.2 Životní cyklus zranitelnosti (terminologie)

- **Bug** (buffer overflow) → **Vulnerability** (možnost spustit malicious code) → **PoC** (proof of concept, shodí program) → **Exploit** (spustí payload, vytvoří root účet).
- **Zero-day**, responsible/coordinated disclosure, disclosure deadline, **bug bounty**.
- **Whitehats / blackhats, red team / blue team.**
- Trh se zranitelnostmi: až miliony $ za nezveřejněný exploit (Zerodium, NSO Group).

---

## 7. Typické bezpečnostní chyby na úrovni kódu, souběžnost, ošetření vstupů

### 7.1 Kde se učit o chybách

- **CWE** (Common Weakness Enumeration) — _taxonomie_ typů zranitelností (kategorie, příklady, mitigace). CWE Top 25.
- **CVE** (Common Vulnerabilities and Exposures) — _seznam konkrétních_ reálných zranitelností.
- **OWASP Top 10** (web; + OWASP Top 10 for LLM). FindSecurityBugs (Java).
- **Cíle útočníka:** ukrást data, obejít ochranu (auth/access), spustit malicious kód (ROP), způsobit DoS. Reálný útok kombinuje více kroků.

### 7.2 Injection (SQL injection, XSS)

> [!example] SQL Injection (OWASP #2, CWE Top 25)
> 
> ```java
> String query = "SELECT * FROM accounts WHERE custID='" + request.getParameter("id") + "'";
> ```
> 
> Útočník zadá `id = ' or '1'='1` → `... WHERE custID='' or '1'='1'` → vrátí vše. **Mitigace:** NE vlastní filtrování vstupu! Použít **parametrizované dotazy / PreparedStatement**, dedikované bezpečné API. (Komise: Lexa 2025 chtěl SQLi + XSS jako příklady útoků.)

- **XSS (Cross-Site Scripting)** — vložení škodlivého skriptu do stránky zobrazené jiným uživatelům; mitigace: výstupní enkódování, CSP.
- Obecný princip: **chyba je v důvěře nedůvěryhodnému vstupu**.

### 7.3 Buffer overflow (klíčové — komise často: Spurný, Jakubíček, Krhovják)

> [!important] Buffer overflow + stack vs heap Zápis za hranice bufferu. Klasika:
> 
> ```c
> char buffer[20];
> bool isAdmin = false;
> gets(buffer);   // gets nekontroluje délku → přepíše sousední paměť
> ```
> 
> **Process memory layout:** text (kód) | data/BSS | **heap** (roste nahoru, dynamická alokace `malloc`/`new`) | **stack** (roste dolů, lokální proměnné, návratové adresy).
> 
> - **Stack overflow:** přepíše **návratovou adresu** → útočník přesměruje běh (vloží shellcode nebo ROP).
> - **Heap overflow:** přepíše data/metadata na haldě.
> - **Související:** use-after-free, double free, dangling pointer, off-by-one, buffer underwrite (CWE-124).
### 7.4 Type overflow (přetečení typu)

Typy mají omezený rozsah (`char` 256 hodnot, `int` 2³²). `unsigned char i = 250 + 10` → přetečení. Klasická chyba:

```c
for (unsigned char i = 10; i >= 0; i--) { ... }  // nekonečný cyklus! unsigned nikdy < 0
```

- Signed × unsigned (pozor: `char` může být signed na x64, unsigned na ARM).
- CPU nastaví overflow flag, ale bez aktivní kontroly se to neprojeví → vede k buffer overflow, chybným výpočtům (peníze, hlasování, rakety — Ariane 5).
- **Mitigace:** `__builtin_add_overflow`/`__builtin_mul_overflow` (GCC), `SafeInt` (MSVC), `Math.addExact` (Java → ArithmeticException).

### 7.5 Format string vulnerability

```c
printf(argv[1]);  // útočník zadá %x%x%x... → vypíše obsah zásobníku
```

- Vede k **úniku informací** (číst paměť: krypto klíče, hesla, security cookie, návratová adresa, narušení ASLR). Při `%n` i zápis.
- **Mitigace:** `printf("%s", argv[1])` — nikdy nedovolit útočníkovi vlastní formátovací řetězec.

### 7.6 Ošetření řetězců — neterminované funkce

`strncpy(buf, src, sizeof(buf))` — pokud je zdroj ≥ velikost, **NEpřidá `\0`** → následné čtení přeteče. Bezpečnější varianty: `strncpy_s`, `strlcpy`.

### 7.7 Souběžnost (concurrency) — race conditions

> [!important] Race condition (Jakubíček 2024 se ptal explicitně) Výsledek závisí na **pořadí/časování** operací, které není atomické. Při neočekávaném prokládání vláken/procesů → nepředvídatelné chování.
> 
> - **TOCTOU (Time-Of-Check to Time-Of-Use):** ověření přístupových práv a jejich použití není atomické → mezi kontrolou a použitím útočník podmění objekt (zneužitelné u SUID/SGID programů, CGI). (Zmiňuje i PV157 AC.)
> - Další: deadlock, data race nad sdílenou pamětí, nekonzistence při souběžném zápisu.
> - **Mitigace:** zámky/mutexy, atomické operace, transakce, vyhnout se sdílenému stavu, používat bezpečné jazyky/knihovny (detekce: Valgrind **Helgrind**).

### 7.8 Heartbleed (příklad z praxe — Static checking lecture)

OpenSSL TLS heartbeat: zkopíruje `length` bajtů payloadu, ale `length` přebírá z útočníkova paketu bez kontroly proti skutečné délce (`in_packet_len != length + 3`) → `memcpy` přečte až 64 kB cizí **paměti haldy** (klíče, hesla). Postihl 17 % SSL serverů. Lekce: **nedůvěřuj délkám ze vstupu**.

---

## 8. Detekce zranitelností a penetrační testování

### 8.1 Jak hledat chyby

- **Manuální:** code review / **security code review**, manuální dynamické testování.
- **Automatická bez spuštění:** **statická analýza (SAST)**.
- **Automatická se spuštěním:** **dynamická analýza (DAST)**.
- **Fuzzing.**
- ⚠️ **Žádný jediný nástroj nezachytí všechny chyby.**

### 8.2 Statická analýza (SAST)

> [!important] Statická × dynamická
> 
> - **Statická:** zkoumá kód **bez spuštění** (zdrojový i binární; zdrojový má víc metadat). Lze na **nedokončený** kód, i před commitem. Manuální audit je „statická" analýza.
> - **Dynamická:** kód se **spouští**, dodávají se vstupy, sleduje se paměť. Nutná kompilace + **vysoké pokrytí kódem (code coverage)**.

**Typy statické analýzy:**

- **Type checking** (kompilátor) — chyby/varování typů (`-Wall -Wextra`).
- **Style checking** (lint, PMD, Checkstyle, cpplint).
- **Formální verifikace** (matematický model, FSM — IA169).
- **Code metrics** — cyklomatická složitost (počet lineárně nezávislých cest) → hotspoty.
- **Bug finding** — pattern matching, **taint analysis** (sledování propagace nedůvěryhodných dat: `user input → exec(data)`), **Control Flow Graph (CFG)**, symbolic execution.
- **Security review.**
- Nástroje: **Coverity (Synopsys), CodeQL, Veracode, FindBugs, cppcheck, Fortify.**

> [!warning] False positives × false negatives
> 
> - **False positive** = nahlášená chyba, která není reálná → příliš konzervativní analýza, otravné, vývojáři přestanou věřit nástroji.
> - **False negative** = reálná chyba **nenahlášená** → chybějící pravidla.
> - **Limity:** nástroj nechápe architekturu programu, sémantiku aplikace (vrací se string uživateli? může obsahovat hesla?), ani sociální kontext.
> - **Kompilátor** nesmí hlásit chybu, která není (standard) + nízké false positives (běžní vývojáři). Nástroj pro **manuální review/pentest** může mít víc false positives (filtruje expert).

### 8.3 Dynamická analýza

**Techniky:** debugger, vkládání dat na vstupní body (fuzzing), proxy mezi programem a knihovnou, `strace`, instrumentace, lehká VM (**Valgrind**), taint analysis, mocking, omezení prostředí.

> [!example] Valgrind
> 
> - **Memcheck** (nejpoužívanější): memory leaks, korupce paměti, dangling pointers, neinicializované proměnné. Nahradí alokátor vlastním + guard bloky.
>     - Leaky: _definitely lost_ / _indirectly lost_ / _possibly lost_.
>     - Detekuje invalid read/write **jen na haldě** (ne na zásobníku/statických proměnných!).
> - **Helgrind** — detekce souběžnostních chyb (race conditions).
> - **Massif** — heap profiler, **Callgrind** — call graphs. Kompilovat s `-g` (debug symboly). Pozor: v **release** verzi vždy **stripovat debug symboly** (pomáhají útočníkovi — NSAKey 1998).
> - **Sanitizery** (kompilátorové): AddressSanitizer (ASan), UBSan, ThreadSanitizer — instrumentace za běhu.

### 8.4 Fuzzing

> [!important] Fuzzing Automatické generování (často náhodných/zmutovaných) vstupů a sledování, zda aplikace selže (**crash, signál, výjimka, chyba**).
> 
> - **Blackbox** (bez znalosti vnitřku) × **coverage-guided/greybox** (sleduje pokrytí kódu — **AFL, libFuzzer**).
> - **Mutation-based** (mutuje validní vzorky — Radamsa) × **generation-based** (generuje podle modelu/gramatiky — **Peach**, protokolově aware).
> - **Fuzz vektory** = známé problematické hodnoty.
> - **Stavové protokoly:** fuzzing přes **proxy** (modifikuje validní tok).
> - Co fuzzovat: soubory, síť, funkce, HTTP (OWASP **ZAP**), API (**RESTler**), krypto knihovny (**CryptoFuzz** — differential fuzzing, **tlsfuzzer**).
> - **Výhody:** opakovatelnost (crash inputy se ukládají, bucketing). **Nevýhody:** najde hlavně parser bugy, potřebuje pokrytí.
> - **OSS-Fuzz** (Google) — kontinuální fuzzing open-source projektů.
> - **Detekce „hitu":** crash/signál/výjimka → uložit seed a crashing input.

### 8.5 Threat modeling (PV286 PA193)

> [!important] Threat modeling Systematický proces identifikace hrozeb. **Riziko = průnik aktiv, hrozeb a zranitelností.** Kroky: identifikace **aktiv** → **hrozeb** → **zranitelností** → **mitigace/protiopatření**.
> 
> - **Attack trees** (stromy útoků) — kořen = cíl útoku, větve = způsoby; hledání mitigací.
> - **STRIDE** (Microsoft) — taxonomie hrozeb:
>     - **S**poofing (identita) → autentizace
>     - **T**ampering (integrita) → hashe/podpisy
>     - **R**epudiation (popírání) → audit logy, non-repudiation
>     - **I**nformation Disclosure (důvěrnost) → šifrování
>     - **D**enial of Service (dostupnost) → rate limiting
>     - **E**levation of Privilege (autorizace) → least privilege
> - **DREAD** — hodnocení rizik (Damage, Reproducibility, Exploitability, Affected users, Discoverability).
> - **Data Flow Diagrams** + trust boundaries, attack surface.

### 8.6 Penetrační testování

> [!important] Penetrační testování (Lexa 2025: „načo sú penetračné testy") Simulace reálného útoku → praktické ověření bezpečnosti běžícího systému; jde dál než automatizovaná analýza (lidská kreativita, řetězení zranitelností).
> 
> - **Black-box** (bez znalostí) / **grey-box** (částečná znalost) / **white-box** (přístup ke zdrojákům, architektuře).
> - Fáze: **reconnaissance → scanning/enumerace → exploitace → post-exploitation (eskalace, laterální pohyb) → reporting.**
> - Nástroje: Nmap, **Metasploit**, Burp Suite, **OWASP ZAP**, nessus.
> - Součást **CI/CD** a SDL: statickou analýzu pouštět už před commitem, dynamickou/fuzzing „přes noc" na serveru, kontinuální monitoring závislostí (Dependabot).

---

## 9. Hardening kompilátorem a platformou (proč BO není tak snadné dnes)

|Útok|Ochrana|
|---|---|
|Spustit kód na zásobníku (BO)|**DEP** (Data Execution Prevention) — non-executable bit, CPU kontroluje|
|Zjistit, kam zapsat/číst|**ASLR** (Address Space Layout Randomization) — náhodné adresy při každém běhu|
|Přepsat návratovou adresu (**ROP**)|**Control Flow Integrity** — graf povolených skoků, vynucen za běhu|
|Přepsat návratovou adresu na zásobníku|**Stack canary / cookie** (`/GS`) — kontrolní hodnota před návratovou adresou|
|—|Sandboxing, HW izolace, secure-by-default jazyky|

---

# 10. Q&A — otázky komise a odpovědi

> [!info] Pozn. Otázky jsou vytaženy z reportů studentů (Excel komise), týkající se oblasti „Bezpečný kód". Číslování v Excelu neodpovídá zadání. U každé je shrnuto, co zkoušející chtěl slyšet, + stručná modelová odpověď.

### Autentizace, autorizace, řízení přístupu

**Q (Dohnal, Pitner): Vysvětlete rozdíl mezi autentizací a autorizací, jaké jsou metody autentizace, ACL.** A: **Autentizace** = ověření, _kdo jsi_ (heslo, token, biometrika, kontext). **Autorizace** = určení, _co smíš_ (jaká práva). **Řízení přístupu** to prosazuje přes referenční monitor (ACL/capabilities, DAC/MAC/RBAC). 4 faktory: něco co znám/mám/jsem/kde jsem. ACL = matice práv uložená po objektech (kdo má přístup k objektu); capabilities = po subjektech.

**Q (Macák 2026): Vlastnosti bezpečného systému + řízení přístupu + typy přístupu.** A: **CIA** — Confidentiality (šifrování), Integrity (hashe/podpisy), Availability (redundance, rate-limiting). Útoky narušují jednotlivé vlastnosti (odposlech, modifikace, DoS). Řízení přístupu: **RBAC** (role), **ACL**, **matice oprávnění**. Typy přístupu k objektu: `r`, `w`, `rw`, `x`.

**Q (Dohnal): Jak ukládat hesla? Hashování, solení.** A: Nikdy v otevřeném tvaru. **Hash** (jednosměrný), **pomalá** funkce (bcrypt/Argon2), **sůl** (náhodná, proti rainbow tables a stejným heslům), případně **pepř** (tajná sůl odděleně). UNIX: `$6$sůl$haš` (SHA-512).

### Biometrika

**Q (Bartek 2018, Oujezský 2024, Batko 2021): Typy biometrik, jak se dají zneužít, hlavní nevýhoda, živost, FAR/FRR.** A: Typy: fyziologické (otisk, duhovka, sítnice, geometrie ruky, obličej) × behaviorální (podpis, hlas, klávesnice). **Hlavní nevýhoda: nejdou změnit** při kompromitaci (na rozdíl od hesla) a **nejsou tajné**. **FAR** = nesprávné přijetí (bezpečnostní riziko), **FRR** = nesprávné odmítnutí (komfort); vyvažují se prahem, **EER** = bod rovnosti (umět nakreslit graf). **Živost** brání podvodu gumovým prstem/fotkou. Multimodalita (více biometrik) zvyšuje bezpečnost; biometrika nejlépe jako **doplňková** metoda.

**Q (Batko 2024): Biometrika vs hesla.** A: Biometrika nelze zapomenout/ztratit, ale nelze ji změnit a není tajná; má variabilitu (FAR/FRR), heslo má binární shodu a lze ho měnit/resetovat.

### Elektronický/digitální podpis, PKI

**Q (Švenda, Ošlejšek, Sedmidubský, Krhovják 2024): Jak funguje digitální podpis? Proč se dokument hashuje? Vlastnosti hashe? CA, PKI, RA, revokace.** A: Dokument se **zahashuje** (rychlost — asymetrika je pomalá, pevná délka), hash se **zašifruje privátním klíčem** → podpis. Ověření: **veřejným klíčem** dešifruji podpis a porovnám s vlastním hashem dokumentu. Zajišťuje integritu, autenticitu, nepopiratelnost. **PKI:** integritu veřejného klíče zajišťuje **certifikát** podepsaný **CA** (strom autorit), **RA** ověřuje žadatele. **Ověření certifikátu** = cesta k důvěryhodné CA + není vypršelý + není revokovaný (**CRL/OCSP**).

**Q (Krhovjak 2021): Druhy podpisů — elektronický, zaručený, uznávaný, digitální.** A: Dle eIDAS: **(prostý) elektronický** → **zaručený (AdES)** (jednoznačné spojení, výhradní kontrola, detekce změny) → **kvalifikovaný (QES)** (QSCD + kvalifikovaný certifikát = rovnocenný vlastnoručnímu). **Uznávaný** (ČR) = zaručený na kvalifik. certifikátu / QES pro úřady. „Digitální podpis" = technický pojem (asymetrická krypto), „elektronický" = právní pojem.

**Q (Plhák 2025/2026, Batko 2021): Co se stane při kompromitaci privátního klíče?** A: **Klíč uživatele:** útočník podepisuje jeho jménem → nutná **revokace** certifikátu, nový pár; dříve vytvořené podpisy zpochybněny. **Klíč CA:** útočník vydává falešné certifikáty → kompromitace celé hierarchie.

**Q (Zlatuška 2023): Vlastnosti hashovací funkce.** A: Jednosměrnost (preimage), odolnost vůči 2. vzoru, kolizní odolnost, pevná délka, lavinový efekt. (Zlatuška neměl rád frázi „ideálně bezkolizní" — raději mluvit o 1./2. preimage.)

**Q (Krhovjak): Hashování s klíčem vs bez klíče.** A: Bez klíče = běžný hash. S klíčem = **MAC/HMAC** — integrita + autenticita pomocí sdíleného tajemství.

### Autentizace strojů a aplikací

**Q (Rebok 2025): Autentizace počítačů, kdo rozdává klíče v asymetrické kryptografii, podepisování.** A: Stroje: challenge-response se sdíleným tajemstvím (GSM/SIM `Ki`), vzájemná autentizace přes TLS/certifikáty. Veřejné klíče „rozdává"/certifikuje **CA** v rámci PKI. Podepisování = privátním klíčem nad hashem.

**Q (secure coding): Jak řešit autentizaci v aplikaci?** A: Nedělat vlastní systém! Použít **OIDC/OAuth/SAML/SSO**, MFA, API tokeny/JWT, role-based model, secret management (Vault).

### Bezpečný kód

**Q (Spurný/Ročkai 2024): Typické bezpečnostní chyby, SQL injection, buffer overflow, stack vs heap.** A: SQLi (vstup do dotazu → mitigace PreparedStatement), buffer overflow (zápis za buffer). **Stack** = lokální proměnné + návratová adresa (přepsání → přesměrování běhu, ROP); **heap** = dynamická alokace. Ochrany: DEP, ASLR, stack canary, CFI.

**Q (Jakubíček 2024): Typické chyby + souběžnost + buffer overflow.** A: BO = nedostatečná kontrola délky vstupu (`gets`), souvisí s pamětí (C, OS). **Souběžnost** = race condition (neatomické operace, **TOCTOU** — mezi kontrolou práv a použitím), deadlock; mitigace zámky/atomické operace, detekce Valgrind Helgrind.

**Q (Lexa 2025): Principy bezpečného kódu, penetrační testy, příklady útoků.** A: Principy: **validace vstupu**, ukončit zpracování při chybě, ošetřit výjimky, least privilege, default deny, defense in depth, nevynalézat krypto. **Penetrační test** = simulace reálného útoku pro praktické ověření. Útoky: **SQL injection, XSS**, buffer overflow.

**Q (Krhovják 2024): Bezpečný kód — podpis, hash, statická a dynamická analýza, injection, buffer overflow, elektronická známka.** A: viz výše + **statická** analýza (SAST, bez spuštění, taint/CFG, false positives, Coverity/CodeQL) × **dynamická** (DAST, spuštění + pokrytí, Valgrind, fuzzing). Injection = vstup interpretovaný jako kód/příkaz. **Elektronická známka** ≈ elektronická pečeť (podpis právnické osoby).

**Q (Plhák 2024/2026): Proč šifrování/hash/digitální podpis a v jakém pořadí?** A: Typicky **hash → podpis (šifrování hashe privátním klíčem) → šifrování zprávy** (symetricky, klíč chráněn asymetricky). Hash zajišťuje integritu, podpis autenticitu/nepopiratelnost, šifrování důvěrnost. Pořadí: podepsat (hash) a pak šifrovat (nebo dle schématu).

**Q (Batko 2025): Módy blokových šifer, útoky na šifry.** A: Módy (ECB/CBC/CTR/GCM), GCM/CCM zajišťují autenticitu (AEAD). Proudové × blokové. Útoky: pasivní/aktivní, kryptoanalýza, **MiTM**, **replay attack**.

**Q (L08, Batko 2025 replay): Autentizační protokoly, challenge-response, replay attack, Kerberos.** A: **Challenge-response** ověří identitu bez vyzrazení tajemství; proti **replay** se používá nonce/sekvenční číslo/časové razítko. **Kerberos** = symetrická autentizace přes důvěryhodný server (**KDC**), který vydá **ticket** a relační klíč; klienti spolu klíče nesdílí. Útoky: impersonace, replay, prolínání, MiTM. **Diffie-Hellman** ustaví klíč, ale bez autentizace je zranitelný na MiTM. **Zero-knowledge** (Fiat-Shamir) dokazuje znalost bez odhalení. 

---

## 11. Rychlé opakování před zkouškou (checklist)

- [ ] 4 faktory autentizace + co je/není 2FA
- [ ] Ukládání hesel: hash + sůl + pepř + pomalá funkce; UNIX/Windows
- [ ] ACL vs capabilities; DAC/MAC/RBAC; Bell-LaPadula (NRU/NWD); Biba
- [ ] Principy: least privilege, default deny, separation of duties, defense in depth
- [ ] FAR/FRR/EER (umět nakreslit), FTE/FTA, živost, biometrika nejde změnit/není tajná
- [ ] Digitální podpis: hash → podpis privátním klíčem → ověření veřejným; proč hash
- [ ] PKI: certifikát, CA (strom), RA, ověření platnosti, revokace (CRL/OCSP)
- [ ] eIDAS úrovně: prostý / zaručený (AdES) / kvalifikovaný (QES); uznávaný; pečeť
- [ ] Kompromitace privátního klíče uživatele × CA
- [ ] Autentizace strojů: GSM/SIM challenge-response, EMV (SDA/DDA/CDA), HSM
- [ ] Autentizace aplikací: OAuth/OIDC/SAML/JWT, nedělat vlastní auth
- [ ] Typické chyby: SQLi (PreparedStatement), XSS, buffer overflow (stack vs heap), type overflow, format string, Heartbleed
- [ ] Souběžnost: race condition, TOCTOU
- [ ] SAST vs DAST, taint analysis, false positive/negative, Valgrind, fuzzing (AFL/coverage), sanitizery
- [ ] Threat modeling: STRIDE, attack trees, DREAD
- [ ] Penetrační testování: black/grey/white-box, fáze, nástroje
- [ ] Hardening: DEP, ASLR, stack canary, CFI
- [ ] CWE vs CVE vs OWASP Top 10