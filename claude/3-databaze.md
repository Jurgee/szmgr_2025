# 3 Databáze

> [!abstract] Principy ukládání dat, databáze. Architektura relačních databází, dotazovací jazyk SQL a jeho části (definice, manipulace, transakce). Jazyk definice datového schématu, DDL. Jazyk manipulace s daty, DML. Relační algebra, integritní omezení, řízení transakcí. Indexování, hašování. Příklady z praxe.
---

## 0. Základní pojmy (rychlý přehled)

- **Databáze** — kolekce dat, která se zpracovávají a jsou zajímavá pro nějakou činnost/business. Skládá se z relací (tabulek), integritních omezení, indexů atd.
- **DBMS (Database Management System)** — kolekce nástrojů pro ukládání a zpracování dat (systém řízení báze dat, SŘBD).
- **Relační model** — struktura: data v relacích (tabulkách); operace: dotazování a aktualizace.
- **Databázové schéma vs. databázová instance** — schéma je popis struktury (návrh tabulek, typů, omezení); instance je konkrétní obsah dat v daném okamžiku.
- **Neprocedurální (deklarativní) přístup** — uživatel říká _„co“_ chce, ne _„jak“_ to získat. Jak se k datům dostat, řeší optimalizátor DBMS.

---

## 1. Principy ukládání dat

### 1.1 Souborově vs. databázově orientované systémy

**Souborově orientované systémy** (každá aplikace má vlastní soubory):

- _Výhody:_ optimalizace datových struktur přesně pro daný problém, menší systémové nároky.
- _Nevýhody:_ aplikační závislost (aplikace musí znát interní strukturu souborů), obtížné zajištění konzistence, obtížné konkurentní operace (zamykání souborů), špatná dokumentovatelnost, obtížné transakce, obtížné řízení přístupových práv.

**Databázově orientované systémy** (aplikace → RDBMS → file system):

- _Výhody:_ aplikační „nezávislost“, snadné zajištění konzistence, konkurentní operace, snadná čitelnost a dokumentovatelnost modelu, standardizovaný vývoj IS (CASE nástroje, přechod konceptuální → fyzický model), neprocedurální přístup k datům.
- _Nevýhody:_ obtížná implementace nestandardních přístupových technik a složitějších datových struktur (nutná normalizace), neprocedurální přístup — někdy je člověk chytřejší než strojová optimalizace.

### 1.2 Hierarchie paměti (storage hierarchy)

Odshora dolů roste kapacita a atomická jednotka, klesá rychlost a cena:

1. **Primární paměť** — cache (CPU), hlavní paměť (RAM). Rychlá, drahá, volatilní.
2. **Sekundární paměť** — disk, flash/SSD. Perzistentní.
3. **Terciární paměť** — pásky, optické disky; pro zálohy.

DBMS pracuje s daty po **blocích/stránkách** (typicky 4–8 KiB). Data se mezi diskem a RAM přenášejí přes **buffer (cache)** spravovaný _Buffer Managerem_.

### 1.3 Optimalizace diskových I/O

Tři hlavní páky:

- **Přístupové techniky** — minimalizovat náhodné přístupy (random access je drahý kvůli seek time), preferovat sekvenční čtení.
- **Objem dat** — vhodná velikost bloku.
- **Organizace úložiště** — disková pole (IOPS).

### 1.4 RAID (Redundant Array of Independent Disks)

Spojení více disků pro lepší výkon a/nebo dostupnost. Data se dělí na **chunky (strides / stripe units)**.

|Úroveň|Princip|Vlastnosti|
|---|---|---|
|**RAID0**|Block striping, bez redundance|Vysoký výkon, **žádná** zvýšená dostupnost, plná kapacita|
|**RAID1**|Zrcadlení (mirroring)|Kapacita 1/n, rychlé čtení, zápis jako 1 disk; vhodné pro DB logy (sekvenční zápis)|
|**RAID2**|Bit-striping + Hammingův kód|Obnova 1 disku; chybu nedetekuje disk|
|**RAID3**|Byte-striping + 1 paritní disk|Obnova přes XOR ostatních disků|
|**RAID5**|Block-striping + **distribuovaná** parita|Každý zápis ≈ 2 čtení + 2 zápisy; nahradil RAID2/3/4|
|**RAID6**|Dvojitá parita|Spolehlivější než RAID5, vhodný pro velkokapacitní disky|
|**RAID1+0 (10)**|RAID0 nad RAID1 poli|Mnohem rychlejší zápis než RAID5, dražší (nižší kapacita), pro hodně zápisů|

**Praktická volba:** RAID1+0 vs. RAID5. Pro aplikace s mnoha zápisy (web servery) → RAID1+0. Logy ideálně na samostatném disku.

### 1.5 Komponenty DBMS

- **Storage Manager** — spravuje bloky na disku a buffery/cache.
    - _Buffer Manager_ — přenos stránek RAM ↔ disk.
    - _Index/Record Manager_ — práce se záznamy a indexy.
- **Query Processor** — překlad dotazu, optimalizace, vykonání.
    - _DDL Compiler_, _Query Compiler_, _Execution Engine_.
- **Transaction Manager** — atomicita, konzistence, izolace, trvanlivost.
    - _Concurrency Control + Lock Table_, _Logging & Recovery_.

---

## 2. Architektura relačních databází

### 2.1 Relační model (E. F. Codd, 1970)

- Datové struktury jsou **n-ární relace** nad doménami $M_1, \dots, M_n$: relace $R \subseteq M_1 \times \dots \times M_n$.
- Relace se reprezentují **tabulkami** — výčtem „pravdivých n-tic“.
- Nad relacemi jsou definované operace **relační algebry**.

### 2.2 Třístupňová (ANSI/SPARC) architektura

Cíl: **datová nezávislost** — oddělení toho, jak data vidí uživatel, od toho, jak jsou fyzicky uložena.

1. **View level (pohledy)** — jednotlivé pohledy `view 1 … view n`; co vidí konkrétní uživatel/aplikace.
2. **Logical level (logická úroveň)** — celé schéma databáze (jaká data a vztahy existují).
3. **Physical level (fyzická úroveň)** — jak jsou data skutečně uložena (soubory, indexy, bloky).

### 2.3 Datové modelování (konceptuální úroveň)

- **Entita (entitní typ)** — typ objektu zájmu IS, jednoznačně identifikovatelný atributy (např. osoba, dům, prodej auta).
- **Vztah (relationship)** — entity mohou být svázány vztahy (auto „bylo prodáno“ osobě).
- Entity i vztahy mohou mít **atributy**.
- Zachycuje se **ER diagramem** — entity = obdélníky, vztahy = spojnice; vztahy mají **kardinalitu** (1:1, 1:n, m:n).
- Přechod konceptuální → logický (relační) model je z velké části automatizovatelný.

### 2.4 Normalizace (stručně)

Cílem je odstranit redundanci a anomálie aktualizace.

- **0NF** — bez omezení.
- **1NF** — všechny atributy atomické (nedělitelné). Bez 1NF nelze efektivně indexovat, vázat referenční integritou, ani psát efektivní SQL.
- **2NF** — žádný neklíčový atribut nezávisí jen na části složeného klíče.
- **3NF** — neklíčové atributy nezávisí na jiných neklíčových atributech (zákaz tranzitivní závislosti).
- U rozsáhlých systémů nelze 3NF vždy dodržet; **řízená redundance** je někdy užitečná (např. rodné číslo nese i datum narození a pohlaví), ale konzistenci je pak nutné hlídat (triggery, CHECK omezení).

---

## 3. Jazyk SQL a jeho části

### 3.1 Historie a standardy

- IBM vyvinula **SEQUEL** (Structured English Query Language), z něj vznikl **SQL** (Structured Query Language).
- Standardizace: **ANSI, ISO/IEC** (SQL92 a další).
- Univerzální standard reálně neexistuje — implementace se liší (Oracle, MS-SQL, Informix, DB2, MySQL, PostgreSQL, Sybase).

### 3.2 Požadavky na jazyk relační databáze

Vytváření/modifikace/rušení relací, dotazy nad tabulkami (implementace RA), vkládání/změna/mazání řádků, garance konzistence dat, řízení přístupových práv, řízení transakcí.

### 3.3 Tři části SQL

|Část|Zkratka|Účel|
|---|---|---|
|Definiční|**DDL** (Data Definition Language)|definice schématu — CREATE, ALTER, DROP|
|Manipulační|**DML** (Data Manipulation Language)|práce s daty — SELECT, INSERT, UPDATE, DELETE|
|Řízení transakcí|**TCC** (Transaction Control)|COMMIT, ROLLBACK, SAVEPOINT, SET TRANSACTION|

**Procedurální nadstavby:** Transact-SQL (MS-SQL, Sybase), PL/SQL (Oracle).

---

## 4. DDL — Data Definition Language

### 4.1 CREATE TABLE

```sql
CREATE TABLE Zam (
    id       NUMBER,
    Jmeno    VARCHAR2(64),
    Prijmeni VARCHAR2(64),
    Odd      VARCHAR2(16) DEFAULT 'Prov'
);
```

- Datové typy se mírně liší dle výrobce názvem (`int` vs `integer`) i implementací (`numeric`, `float`, `double`).
- `DEFAULT` klausule — dosadí hodnotu, pokud není při INSERTu dodána.

### 4.2 ALTER TABLE

```sql
ALTER TABLE Zam ADD rc VARCHAR2(32);          -- přidání sloupce
ALTER TABLE emp MODIFY date_of_birth (26);    -- změna typu
ALTER TABLE emp DROP COLUMN date_of_birth;    -- odebrání sloupce
```

### 4.3 Sekvence (SEQUENCE)

Pro generování jednoznačných celých čísel (umělý primární klíč) nezávisle na transakcích.

```sql
CREATE SEQUENCE SEQ1 START WITH 32 INCREMENT BY 100;
-- pseudosloupce: SEQ1.CURRVAL (aktuální), SEQ1.NEXTVAL (následující)
```

V PostgreSQL ekvivalent: datový typ `bigserial` (automaticky poslední + 1).

### 4.4 Pohledy (VIEW)

`CREATE VIEW jmeno AS SELECT …` — uložený příkaz SELECT.

- **SELECT** nad view — funguje jako nad tabulkou.
- **UPDATE/DELETE/INSERT** — možné jen tehdy, když řádky view odkazují na právě jednu **key-preserved** tabulku (tabulka, jejíž primární klíč je zároveň klíčem ve view).

---

## 5. DML — Data Manipulation Language

### 5.1 INSERT

```sql
INSERT INTO tabulka (sloupec1, sloupec2) VALUES (h1, h2);  -- doporučeno (sloupce uvedeny)
INSERT INTO tabulka VALUES (h1, h2, h3);                   -- pořadí dle definice, křehké
```

Při INSERTu se **kontrolují všechna integritní omezení**. PK/UNIQUE sloupce jsou indexované → kontrola rychlá.

### 5.2 UPDATE

```sql
UPDATE tabulka SET sloupec1 = h1 WHERE (VEK > 40) AND (VZDELANI = 'MUNI');
```

Bez `WHERE` mění **všechny** řádky. Kontrolují se dotčená integritní omezení.

### 5.3 DELETE

```sql
DELETE FROM tabulka;                  -- POZOR: smaže VŠE
DELETE FROM tabulka WHERE podminka;
```

Mazání z tabulky, na kterou odkazuje cizí klíč, vynutí kontrolu „detailové“ tabulky → **proto se vždy doporučuje index na každý cizí klíč**.

### 5.4 SELECT

**Strategie (logické) vyhodnocení:**

1. `WHERE` — vyberou se řádky splňující podmínku (selekce).
2. `GROUP BY` — vytvoří se skupiny.
3. `HAVING` — vyřadí skupiny nesplňující podmínku.
4. (`SELECT` projekce, `ORDER BY` řazení).

**WHERE klausule** (realizuje selekci z RA). Libovolný výraz lze nahradit jednořádkovým-jednosloupcovým poddotazem.

```sql
WHERE ADRESA_OBEC = (SELECT ID FROM P01_OBEC WHERE nazev='Praha');   -- poddotaz
WHERE ADRESA_OBEC IN (SELECT ID FROM P01_OBEC WHERE pocet_obyv>2000); -- IN
WHERE RC BETWEEN 5800000000 AND 5899999999;                          -- rozsah
WHERE PRIJMENI LIKE 'Nov%';                                          -- řetězce
WHERE EXISTS (SELECT NULL FROM …);                                   -- existence
WHERE ADRESA_OBEC = SOME(3701,3801);  /  <> ALL(3701,3801);          -- kvantifikace
WHERE TITUL_PRED IS NOT NULL;                                        -- NULL test
```

**Agregační (skupinové) funkce** — vrací jednu hodnotu z více řádků (implementace operátoru Γ): `AVG`, `SUM`, `MIN`, `MAX`, `COUNT`.

```sql
SELECT AVG(salary), dept
FROM   employee
GROUP BY dept
HAVING avg(salary) > 10000;   -- HAVING filtruje až výsledky agregace
```

> Pravidlo: nepoužívej `HAVING` tam, kde stačí `WHERE` (WHERE filtruje řádky před agregací — efektivnější).

### 5.5 Spojování tabulek (JOIN)

Pravděpodobně nejčastější konstrukce. JOIN = **součin + selekce** v RA.

```sql
SELECT … FROM t1, t2 WHERE t1.i = t2.i;   -- inner join
```

- **Outer join** — vrátí povinně všechny řádky jedné tabulky; chybějící protějšky jsou `NULL`. Zápis přes `LEFT/RIGHT OUTER JOIN` (Oracle historicky `(+)`).

**Techniky spojování (fyzická realizace):**

- **Nested loops** — pro každý řádek vnější tabulky se hledá shoda ve vnitřní (přes index nebo plným průchodem).
- **Sort-merge join** — obě tabulky se setřídí dle spojovacího klíče, pak se procházejí současně.
- **Hash join** — z menší tabulky se postaví hashovací tabulka, větší se proti ní hledá.

### 5.6 Hodnota NULL

Zavádí **tříhodnotovou logiku** (true / false / NULL). Jakákoli operace s NULL vrací NULL (`1 + NULL = NULL`). Logika:

|p|q|p AND q|p OR q|
|---|---|---|---|
|true|null|null|true|
|false|null|false|null|
|null|null|null|null|

---

## 6. Relační algebra (RA)

### 6.1 Primitivní operátory

- **Projekce** $\pi(R, (M_x, …, M_y))$ — ponechá jen vyjmenované domény (sloupce).
- **Selekce** $\sigma(R, \varphi)$ — podmnožina relace $R$ splňující formuli $\varphi$.
- **Kartézský součin** $A \times B$ — všechny $(m+n)$-tice $(a_1,…,a_m,b_1,…,b_n)$, kde $(a_1,…,a_m)\in A$, $(b_1,…,b_n)\in B$.
- **Sjednocení** $A \cup B$ (relace stejného typu) — množinové sjednocení.
- **Rozdíl** $A - B$ (relace stejného typu) — množinový rozdíl.

### 6.2 Rozšiřující operátory

- **Přejmenování** — důležité při násobném výskytu téže relace v součinu (např. `name1`, `name2`).
- **Agregace a grupování** $\Gamma(R, \gamma, \alpha(x))$ — $\gamma$ je seskupovací atribut, $x$ agregovaný atribut, $\alpha$ agregační funkce (sum, avg, count…). Pro každou skupinu se stejnou hodnotou $\gamma$ spočítá $\alpha(x)$ a vrátí jako jeden prvek.
- **Spojení (JOIN)** = součin + selekce (lze odvodit).

### 6.3 Coddova věta a limity

- **Coddova věta (zjednodušeně):** vyjadřovací síla relační algebry je **ekvivalentní** vyjadřovací síle predikátové logiky 1. řádu (FOL).
- **Limity:** existují dotazy, které RA (ani FOL) nevyjádří — klasický příklad je **tranzitivní uzávěr** binární relace (nelze vyjádřit prostředky FOL). Proto SQL přidává rekurzivní dotazy (`WITH RECURSIVE`).

---

## 7. Integritní omezení (pravidla pro konzistenci dat)

|Omezení|Význam|
|---|---|
|**NOT NULL**|sloupec musí být vyplněn|
|**UNIQUE**|sloupec(ce) má unikátní hodnoty v celé tabulce|
|**PRIMARY KEY**|jednoznačná identifikace řádku (= NOT NULL + UNIQUE, automaticky indexováno)|
|**REFERENCES / FOREIGN KEY**|referenční integrita — hodnota je hodnotou PK/UNIQUE jiné (či téže) tabulky|
|**CHECK**|kontrola vkládané/měněné hodnoty|

```sql
ALTER TABLE ZAM ADD CONSTRAINT pk_emp PRIMARY KEY (id);
ALTER TABLE ZAM ADD CONSTRAINT fk_dept FOREIGN KEY (Odd) REFERENCES Odd(id);
ALTER TABLE ZAM ADD CONSTRAINT chk_id  CHECK (id > 0);
```

**Referenční integrita v praxi:**

- Při INSERT/UPDATE/DELETE se kontroluje korektnost vazeb. Kontrola na PK/UNIQUE straně je rychlá (indexováno), ale prohledávání **detailové** tabulky bývá pomalé → **vytvářej index na každý cizí klíč** (jinak se DELETE/UPDATE dramaticky zpomalí).

**ON DELETE klausule:**

- `CASCADE` — smazání řádku z nadřízené tabulky smaže i řádky podřízené.
- `SET NULL` — cizí klíče podřízené tabulky se nastaví na NULL.

**INITIALLY DEFERRED** — omezení se kontroluje až při `COMMIT` (ne hned po příkazu). Umožní např. povinnou vazbu 1:1, kde dvě tabulky odkazují na sebe navzájem.

**Triggery** — procedurální reakce na INSERT/UPDATE/DELETE (např. auditní log). Slouží i k zajištění řízené redundance. Neměly by „šifrovat“/tiše měnit data nečekaným způsobem.

---

## 8. Řízení transakcí

### 8.1 Definice a ACID

**Transakce** = posloupnost DML příkazů, které převedou schéma z jednoho **konzistentního stavu** do druhého.

- **A — Atomicity** (atomicita): provede se celá, nebo vůbec.
- **C — Consistency** (konzistence): na konci není porušeno žádné omezení.
- **I — Isolation** (izolovanost): operace jsou izolovány od jiných transakcí.
- **D — Durability** (trvanlivost): po dokončení jsou data trvale uložena.

### 8.2 TCC příkazy

```sql
COMMIT;                 -- potvrdí změny od začátku transakce
ROLLBACK [TO savepoint];-- odvolá změny
SAVEPOINT s1;           -- bod, ke kterému lze provést rollback
SET TRANSACTION READ ONLY;   -- zakáže INSERT/UPDATE/DELETE
```

> I `SELECT` je v jistém smyslu transakční — musí vrátit stav dat platný v okamžiku zadání.

### 8.3 Úrovně izolace (a jevy, kterým brání)

Od nejsilnější po nejslabší:

- **SERIALIZABLE** — plná izolace. (Oracle: pokud konkurentní transakce mění týž objekt a je potvrzena, naše transakce skončí chybou.)
- **REPEATABLE READ** — povoluje _phantom reads_ (nově vložené záznamy).
- **READ COMMITTED** (default v Oracle) — povoluje _non-repeatable reads_; čeká na uvolnění zámku, pak provede DML.
- **READ UNCOMMITTED** — povoluje _dirty reads_ (čtení nepotvrzených dat); zápisy jsou stále atomické.

|Úroveň|Dirty read|Non-repeatable read|Phantom|
|---|:-:|:-:|:-:|
|READ UNCOMMITTED|✓|✓|✓|
|READ COMMITTED|✗|✓|✓|
|REPEATABLE READ|✗|✗|✓|
|SERIALIZABLE|✗|✗|✗|

### 8.4 Zamykání a deadlock

```sql
SELECT … FOR UPDATE [NOWAIT];  -- uzamkne řádky do COMMIT/ROLLBACK
LOCK TABLE … IN EXCLUSIVE MODE; -- ROW SHARE / SHARE / EXCLUSIVE
```

- **Deadlock** (uváznutí) — dvě transakce na sebe čekají vzájemně (T1 drží A a chce B, T2 drží B a chce A). DBMS deadlock detekuje a jednu transakci „obětuje“ (vrátí chybu).
- **Optimistický vs. pesimistický** přístup k zamykání.

### 8.5 Recovery — žurnálování (logging)

Atomicita a trvanlivost se zajišťují **žurnálem (log)** = soubor záznamů o změnách (start, write, commit/abort, checkpoint…).

**Stabilní vs. nestabilní úložiště:** v paměti jsou DATABASE BUFFER a LOG BUFFER, na disku LOG + DATA.

- **Undo logging** — log obsahuje **starou** hodnotu. Pravidla:
    
    1. Pro každý `write(X,t)` vznikne log se starou hodnotou X.
    2. **Write-Ahead Logging (WAL):** dříve než se X zapíše na disk, musí být log na disku.
    3. `COMMIT` se do logu zapíše až po zápisu všech změn na disk.
    
    - _Recovery:_ transakce bez `commit` se vrací zpět (od nejnovější k nejstarší) na staré hodnoty.
- **Redo logging** — log obsahuje **novou** hodnotu.
    
    1. Log s novou hodnotou X.
    2. Dříve než se X zapíše do DB, musí být všechny log záznamy (včetně commit) na disku.
    
    - _Recovery:_ potvrzené transakce se znovu přehrají.
- **Undo/Redo logging** — log obsahuje starou i novou hodnotu (`<T,X,new,old>`); nejflexibilnější.
    
- **Checkpoint** — bod, od kterého stačí při obnově začít. Zkracuje čas obnovy a velikost logu, ale dočasně snižuje propustnost. Při obnově: najdi poslední checkpoint a odtud přehrávej.
    
- **2-phase commit** — protokol pro atomicitu v distribuovaných DB (commit-request fáze + commit fáze); zatěžuje výkon velkých systémů.
    

---

## 9. Indexování

### 9.1 Princip

**Index** = kolekce položek `<hodnota klíče, ukazatel na záznam/blok>`, která urychluje vyhledávání.

**Terminologie:**

- **Dense index** (hustý) — položka pro **každý** záznam.
- **Sparse index** (řídký) — položka jen pro některé záznamy (typicky první v každém bloku); funguje jen u souboru **setříděného** dle klíče.
- **Primary index** — nad klíčem, dle kterého je soubor fyzicky uspořádán.
- **Secondary index** (sekundární) — nad jiným klíčem; soubor není dle něj uspořádán → **musí používat ukazatele na záznamy** a je **hustý**.
- **Clustered index** — data v souboru jsou fyzicky uspořádána dle indexu (jeden na tabulku).
- **Non-clustered index** — pořadí dat nesouvisí s indexem.
- **Covering index** — index obsahuje všechny sloupce dotazu → není nutné sahat do tabulky.
- **Multilevel index** — víceúrovňový (index nad indexem).
- **Index-sequential file** — setříděný soubor + řídký index.

**Dense vs. Sparse index**
![[dense_vs_sparse_index.webp|619]]

### 9.2 B-strom / B+-strom

Základní vyhledávací metoda ve všech DB strojích. Vyvážený stromový index.

![[b_tree.png]]

**Vlastnosti B+-stromu (arita m):**

- každý uzel max. $m$ synů;
- každý uzel (kromě kořene a listů) min. $\lceil m/2 \rceil$ synů;
- kořen min. 2 syny (pokud není list);
- **všechny listy na stejné úrovni** (vyváženost);
- nelistový uzel s $k$ syny obsahuje $k-1$ klíčů, klíče vzestupně uspořádané;
- ukazatel $p_i$ vede na podstrom s klíči v intervalu $[key_i, key_{i+1})$.

**B-strom (bez +)** — ukazatele na záznamy jsou i v nelistových uzlech (žádná replikace klíčů). **B+-strom** — klíče (a ukazatele na data) jsou jen v listech, vnitřní uzly slouží k navigaci; listy bývají spojené v seznam (efektivní rozsahové dotazy).

**B+-strom jako soubor** — listy mohou uchovávat přímo záznamy. Duplicitní klíče → ukazatele do _bucketů_ (bloky se seznamem ukazatelů). Klíče proměnné délky (řetězce) → prefixová komprese.

**Operace:** vyhledání, vložení (při přeplnění _split_ uzlu), mazání (při podtečení _coalesce/merge_ se sousedem).

### 9.3 R-strom

Analogie B-stromu pro **prostorová data** — klíče jsou 2D (obecně nD) **obdélníky** (minimální opsané obálky, MBR). Synovský obdélník je uvnitř otcovského; listy na stejné úrovni. Používá se pro geometrické/geografické dotazy.

### 9.4 Kombinování indexů

Dotaz `WHERE dept='Toys' AND salary<10000` — každý index vrátí seznam kandidátů, výsledek = **průnik** seznamů.

> Index zrychluje čtení, ale zpomaluje zápis (INSERT/UPDATE/DELETE musí aktualizovat i indexy) a zabírá místo → indexovat uváženě.

---

## 10. Hašování (hashing)

### 10.1 Princip a terminologie

- **Hash funkce** — mapuje hodnotu klíče na adresu (číslo bucketu).
- **Kolize (collision)** — na vrácené adrese už je jiný záznam (není problém, pokud se na adresu vejde víc klíčů).
- **Overflow (přetečení)** — kapacita bucketu je vyčerpána → overflow bloky.
- **Statické vs. dynamické** hašování — podle toho, zda se mění velikost adresního prostoru.

### 10.2 Statické hašování — řešení kolizí

- **Closed addressing (= open hashing)** — vrácená adresa je pevná; při přetečení se alokuje nový blok (**overflow area**), bloky se zřetězují (_chaining_). Používá se v sekundární paměti.
- **Open addressing (= closed hashing)** — zavede se kolizní funkce (lineární, kvadratické zkoušení, double hashing). Používá se pro in-memory hash tabulky.

Nevýhoda statického: při přetečení nutná **reorganizace** (návrh nové hash funkce).

### 10.3 Dynamické hašování

**Extendible hashing (rozšiřitelné):**

- Používá **horních $i$ bitů** výstupu hash funkce.
- Přidává **adresář (directory)** — úroveň nepřímosti, velikost mocnina 2.
- Vložení: pokud je v bucketu místo → vlož; jinak **split bucketu** a redistribuce; pokud je adresář plný → **zdvojnásob** ho (o jeden bit víc).
- Mazání: prázdné buckety lze **slučovat** (sousedi se stejným prefixem), adresář se může zmenšit.
- _Výhody:_ zvládá rostoucí soubor, méně plýtvání místem, lokální reorganizace.
- _Nevýhody:_ nepřímost (adresář); zdvojnásobení adresáře se nemusí vejít do paměti.

**Linear hashing (lineární):**

- Používá **dolních $i$ bitů** adresy, **bez adresáře**, soubor roste **lineárně**.
- Sleduje se naplnění; při překročení prahu (např. >80 %) se **rozdělí bucket** (na pořadí $n - 2^{i-1}$), přidá se nový bucket, klíče se přerozdělí.
- _Výhody:_ roste plynule, méně plýtvání, lokální reorganizace, **žádná nepřímost**.
- _Nevýhody:_ overflow řetězce.

### 10.4 Hašování více atributů

**Partitioned hash function** — klíče se sloučí (konkatenace řetězců, násobení čísel) a hašuje se výsledek.

### 10.5 Index vs. hash — kdy co

- **Hash** — výborný pro **bodové dotazy** (rovnost =), O(1) průměrně.
- **B+-strom** — nutný pro **rozsahové dotazy** (`<`, `>`, `BETWEEN`, `ORDER BY`), protože udržuje uspořádání; hash uspořádání neumí.

---

## 11. Zpracování a optimalizace dotazů (navazuje)

### 11.1 Postup zpracování dotazu

1. SQL dotaz → kontrola syntaxe a sémantiky.
2. **Parse tree** → **logický dotazový plán** (v relační algebře).
3. Úpravy plánu (transformační pravidla), odhad velikostí relací.
4. **Fyzický plán** — konkrétní algoritmy operací (nested loops / sort-merge / hash join…), odhad nákladů.
5. Výběr „nejlepšího“ plánu → vykonání.

### 11.2 Optimalizace v relační algebře

Transformační pravidla musí zachovat ekvivalenci. Příklady:

- komutativita a asociativita kartézského součinu / sjednocení;
- protlačení selekce a projekce co nejblíže k datům (zmenšit objem dat brzy);
- kombinace projekce a přirozeného spojení.

### 11.3 Odhad nákladů (cost-based optimization)

- Náklady jsou v **abstraktních jednotkách**, ne milisekundách.
- Postup: odhadni velikost výsledku (CPU náklady) + počet I/O operací (rychlost). Pro každou operaci v plánu.
- Vychází ze **statistik** (metadata o tabulkách: počet řádků, počet různých hodnot $V(R,A)$ atd.).
- Nástroj `EXPLAIN` (PostgreSQL) / `EXPLAIN PLAN` (Oracle) ukáže zvolený plán, přístupovou metodu (FULL TABLE SCAN, INDEX), odhad nákladů a kardinalit.

---

## 12. Příklady z praxe (shrnutí)

- **Index na cizí klíče** — bez něj se DELETE/UPDATE v nadřízené tabulce dramaticky zpomalí, protože DBMS prochází detailovou tabulku plným scanem. _(praktický příklad PostgreSQL: tabulky hotel/room.)_
- **WHERE místo HAVING** — filtruj řádky před agregací, ne až výsledky agregace → méně zpracovávaných dat.
- **Volba RAID** — logy na samostatný (mirror) disk se sekvenčním zápisem; pro hodně zápisů RAID1+0, jinak RAID5.
- **NULL a tříhodnotová logika** — pozor na `= NULL` (vždy NULL); používej `IS NULL` / `IS NOT NULL`.
- **Řízená redundance** — povolená, ale jištěná `CHECK`/triggerem (např. rodné číslo vs. pohlaví/datum narození).
- **Hash join** pro velká rovnostní spojení; **B+-strom** index pro rozsahové dotazy a řazení.
- **OLTP úzká hrdla** — měření ukazují, že v klasickém DBMS jde čas hlavně do buffer poolu, zamykání a recovery (jen ~12 % je „skutečná práce“). Odtud motivace **NewSQL** / in-memory DBMS (H-Store): odstranění buffer manageru, zámků a logování → řádové zrychlení propustnosti.

---

## 13. Rychlé opakování — co umět říct k tabuli

1. **Co je DBMS a jeho komponenty** (storage manager, query processor, transaction manager).
2. **Třístupňová architektura** a datová nezávislost.
3. **Relační model + 6 operátorů RA** + Coddova věta + limity (tranzitivní uzávěr).
4. **Tři části SQL** (DDL / DML / TCC) a umět napsat CREATE TABLE, SELECT s JOIN/GROUP BY/HAVING, INSERT/UPDATE/DELETE.
5. **5 typů integritních omezení** + referenční integrita + ON DELETE CASCADE/SET NULL + DEFERRED.
6. **ACID** + úrovně izolace + dirty/non-repeatable/phantom read + deadlock + undo/redo log + WAL + checkpoint.
7. **Indexy:** dense/sparse, primary/secondary, clustered, B+-strom (vlastnosti, vyvážení), kdy index pomáhá.
8. **Hašování:** kolize/overflow, statické (closed/open addressing), dynamické (extendible vs. linear), index vs. hash.
9. **Hierarchie paměti + RAID + optimalizace I/O.**
10. **Optimalizace dotazů** (logický → fyzický plán, cost-based, EXPLAIN).

---

## 14. Co se komise reálně ptá (z reportů studentů 2016–2026)

> Čísla otázky se mezi roky a programy mění (objevuje se jako 7S, 4C, SWE4, DEV2, „nově 4." apod.), ale obsah je konzistentní: **„Databáze / Relační databáze."** Velmi často to začíná **srovnáním s file systémem** a pak se zkoušející chytí jednoho podtématu a jde do hloubky.

### 14.1 Nejčastější scénář zkoušení

Typický průběh, který se opakuje napříč lety:

1. **Začni od základů** — proč databáze existují, **srovnání s file systémem** (výhody/nevýhody). Skoro každý zkoušející chce slyšet tohle jako úvod.
2. **Relační model** — co je relace (matematicky: podmnožina kartézského součinu / n-tice, množiny), jak to souvisí s tabulkami (atributy, záznamy), schéma databáze.
3. **Klíče** — uměj vyjmenovat a rozlišit: **kandidátní (candidate), superklíč (super key), primární (primary), cizí (foreign)**. ← objevuje se opakovaně (Batko, Rebok).
4. **Relační algebra** — vyjmenovat operace, umět **notaci** (zápis selekce, projekce, kartézského součinu) a vztah k SQL. Zkoušející často chce příklad zápisu + ekvivalentní `SELECT`.
5. **SQL** — DDL vs. DML, pořadí klauzulí v `SELECT`, příklad `SELECT` dotazu, `CREATE TABLE` + datové typy + integritní omezení.
6. Pak se obvykle „zabodnou" do **indexů / hašování / transakcí / vyhodnocení dotazu**.

> **Tip z reportů:** _Kreslení se vyplácí_ — zabere čas a komise pak nemusí stihnout doptávat se na záludnosti. Týká se hlavně indexů (dense/sparse, B+-strom) a ERD.

### 14.2 Detaily, které zkoušející konkrétně chtějí slyšet

Tyhle „chytáky" se v reportech opakují — stojí za to je umět přesně:

- **Arita B+-stromu závisí na velikosti bloku na disku** (Ošlejšek to chtěl explicitně slyšet). Čím větší blok, tím vyšší arita, tím nižší a širší strom → méně I/O.
- **Index a SQL dotaz — „čistý výraz":** index lze použít jen pro **sargable** predikát nad holým sloupcem. Pro `WHERE A > 5` index jde použít, pro `WHERE A + 5 > 3` (funkce/výraz nad sloupcem) **ne** (Ošlejšek/Dohnal).
- **Vyhodnocení dotazu — celý řetězec:** syntaktická kontrola → sémantická kontrola → **logický plán** (převod do relační algebry) → **fyzický plán** (konkrétní algoritmy) → výběr nejlevnějšího plánu. Optimalizace je **založená na statistikách** (metadata o tabulkách) — k tomu zkoušející navádějí (Batko, Pitner).
- **Výběr fyzického plánu závisí na typu DB:** u **klasické (disk) DB** se optimalizuje počet **diskových přístupů**, u **in-memory DB** se optimalizuje **čas procesoru** (Batko 2025).
- **Typy JOIN umět vyjmenovat a porovnat:** Nested Loop vs. Sort-Merge vs. Hash join (Nováček).
- **Proč kryptografická hash funkce není vhodná pro hash index:** je zbytečně drahá — pro indexování **nepotřebujeme odolnost vůči (druhému) vzoru** (first/second preimage resistance). Stačí rovnoměrné rozložení (Švenda).
- **Integritní omezení = i libovolné procedury/triggery:** když po tobě chtějí „další integritní omezení" po vyjmenování NOT NULL/UNIQUE/PK/FK/CHECK, chtějí slyšet, že lze použít **libovolnou proceduru / trigger** (Rebok).
- **Relační algebra slouží k optimalizaci** — zkoušející se ptají „proč se RA používá" a chtějí slyšet, že je to mezijazyk pro logický plán a aplikaci transformačních pravidel (Sedmidubský).

### 14.3 Co od koho čekat (zkoušející z reportů)

- **Batko** — základy (tabulky, atributy, záznamy), **klíče** (kandidátní/super/primární/cizí), **relační algebra** (kartézský součin, selekce, projekce + notace), **vyhodnocení dotazu + statistiky**, DML/DDL, složitost vyhledávání B-strom vs. hash, „jak přesně funguje hašování". Nechá mluvit, nelíbí se mu nepřesné definice z materiálů.
- **Ošlejšek** — **indexování do hloubky** (dense/sparse, B-stromy, B+-stromy, podmínky definující B+-strom), arita dle velikosti bloku, ukládání dat přes B+-stromy (výhody/nevýhody), použitelnost indexu v SQL.
- **Dohnal** — propojí DB se **škálovatelností, vysokou dostupností a RAID** (kdy mirroring, kdy striping).
- **Plhák** — jede přesně podle otázky: integritní omezení, ACID, transakce, indexy, hašování, B+-stromy. Hodně pomáhá, klidný.
- **Krhovjak** — srovnání s file systémem, relační model, schéma, výhody/nevýhody, **indexy do hloubky**, transakce (ACID + izolace povrchově), komprese; bonusy: **Bloom filtry** u indexů, šifrování DB (**homomorfní šifrování**).
- **Nováček** — proč DB vs. file system (File Control Block), indexace, hashe a hashovací funkce, relační algebra a vztah k SQL, **B+-stromy** (složitost, chování uvnitř), bitmapy, typy JOIN.
- **Sedmidubský** — DDL/DML, **pořadí klauzulí v SELECT**, JOINy, **architektura DB**, relační algebra (proč = optimalizace), **transakční zpracování**. Pozor — umí se zaseknout na relační algebře a jít „hloub a hloub".
- **Rebok** — základy + srovnání s file systémem, relace/n-tice/operace, definice schématu (`CREATE TABLE`, typy, integritní omezení — i procedury), **klíče** (primární/kandidátní/super/cizí), transakce (ACID).
- **Ročkai** — ukládání dat do **bloků**, indexy, joiny, **B+-strom detailně**, databázové transakce, statistika.
- **Švenda** — **data storage, indexy a hashe**: typy indexů, B+-strom (nakreslit), hash index, open/closed hashing, statické/dynamické hašování, proč ne kryptografická hash funkce; **transaction recovery** (ACID, serializace + graf závislostí, **WAL, úrovně izolace, MVCC, zámky, deadlocky, omezení/triggery**); SQL injection a obrana.
- **Pitner** — jak DB funguje, optimalizace dotazů, **table partitioning**, indexing.
- **Matyska / Zlatuška** — spíš obecně „relační databáze", nechají mluvit.

### 14.4 Doplňky, které stojí za doučení (objevily se jako podotázky)

- **Kandidátní klíč** — minimální superklíč (žádná jeho vlastní podmnožina už není superklíč). **Superklíč** — libovolná množina atributů jednoznačně identifikující řádek. Z kandidátních klíčů se vybírá **primární**.
- **ER vs. Class diagram** — Class diagram má navíc **metody** (ne funkce/procedury); ER má entity, vztahy, kardinalitu (Ráček chtěl srovnání + až **4NF**).
- **Bitmapový index** — pro atributy s málo různými hodnotami (nízká kardinalita, např. pohlaví); bitová mapa na hodnotu, rychlé `AND`/`OR` přes dotazy.
- **Bloom filtr** — pravděpodobnostní struktura pro rychlý test „prvek možná je / určitě není v množině"; používá se jako filtr před drahým přístupem (žádné false negatives, možné false positives).
- **MVCC (Multi-Version Concurrency Control)** — čtenáři nečekají na zapisovatele; udržují se verze řádků (čtení vidí konzistentní snapshot). Alternativa/doplněk k zamykání.
- **Table partitioning** — rozdělení velké tabulky na části (range/hash/list) kvůli výkonu a správě.
- **OLTP vs. OLAP / datové sklady** — OLTP = mnoho krátkých transakcí; OLAP = analytické dotazy nad agregovanými daty (roll-up, drill-down, datová kostka, dimenze); ETL plní data warehouse, aby se nezatěžovaly OLTP systémy. _(Bývá samostatná otázka „Zpracování dat / datové sklady", ale může přijít jako přesah.)_

---

## 15. Modelové odpovědi na typické otázky (Q&A)

> Stručné „co říct nahlas" k otázkám, které komise reálně pokládá. Jedna až pár vět na otázku — kostra, na které se dá stavět.

### A. Základy a ukládání dat

**Proč potřebujeme databáze? V čem jsou lepší než file systém?** DB poskytuje aplikační nezávislost (aplikace nemusí znát fyzickou strukturu), snadné zajištění konzistence, konkurentní přístup (zamykání, transakce), řízení přístupových práv a neprocedurální (deklarativní) přístup — říkám _co_ chci, ne _jak_. File systém je rychlý a jednoduchý pro jednu aplikaci, ale konzistenci, konkurenci a transakce řeší těžko a vede k aplikační závislosti.

**Jak se ukládají data (fyzicky)?** Data jsou na disku v **blocích/stránkách** (typicky 4–8 KiB), které jsou atomickou jednotkou přenosu mezi diskem a RAM. DBMS si je drží v **bufferu (cache)**, který spravuje Buffer Manager. Cílem je minimalizovat náhodné diskové přístupy (drahý seek). Hierarchie paměti: cache → RAM → disk/SSD → pásky.

**Co je RAID a kdy co použít?** Spojení více disků pro výkon a/nebo dostupnost; data se dělí na chunky (striping). **RAID0** = striping bez redundance (výkon, žádná ochrana). **RAID1** = zrcadlení (rychlé čtení, vhodné pro logy). **RAID5** = striping + distribuovaná parita (zápis = 2 čtení + 2 zápisy). **RAID1+0** = rychlejší zápis, pro hodně zápisů, ale dražší. Mirroring pro dostupnost/čtení, striping pro výkon.

### B. Relační model a klíče

**Co je relace (matematicky)?** Relace $R$ nad doménami $M_1,\dots,M_n$ je libovolná **podmnožina kartézského součinu** $R \subseteq M_1 \times \dots \times M_n$, tj. množina n-tic. V tabulce: řádek = n-tice (záznam), sloupec = atribut (doména).

**Vyjmenujte a rozlište typy klíčů.**

- **Superklíč** — libovolná množina atributů, která **jednoznačně identifikuje** řádek.
- **Kandidátní klíč** — **minimální** superklíč (žádná vlastní podmnožina už není superklíč).
- **Primární klíč** — jeden vybraný kandidátní klíč; implicitně `NOT NULL` + `UNIQUE`, automaticky indexovaný.
- **Cizí klíč (foreign key)** — atribut(y) odkazující na PK/UNIQUE jiné (či téže) tabulky → referenční integrita.

**Co je databázové schéma vs. instance?** Schéma = popis struktury (tabulky, typy, omezení), mění se zřídka. Instance = konkrétní obsah dat v daném okamžiku.

### C. Relační algebra

**Co je relační algebra a k čemu slouží?** Formální jazyk operací nad relacemi. Slouží jako **mezijazyk pro logický plán dotazu** — SQL se přeloží do RA a na ní se aplikují **transformační pravidla pro optimalizaci** (např. protlačení selekce blíž k datům).

**Vyjmenujte operace + notace.** Primitivní: **selekce** $\sigma_\varphi(R)$ (řádky splňující podmínku), **projekce** $\pi_{A,B}(R)$ (vybrané sloupce), **kartézský součin** $R \times S$, **sjednocení** $R \cup S$, **rozdíl** $R - S$. Rozšiřující: **přejmenování** $\rho$, **agregace/grupování** $\Gamma$, **spojení (join)** = součin + selekce.

**Coddova věta + limity?** Vyjadřovací síla RA je ekvivalentní predikátové logice 1. řádu (FOL). Limit: některé dotazy nelze vyjádřit — klasicky **tranzitivní uzávěr** (proto SQL přidává `WITH RECURSIVE`).

### D. SQL — DDL, DML, transakce

**Jaké jsou části SQL?** **DDL** (definice schématu: CREATE/ALTER/DROP), **DML** (práce s daty: SELECT/INSERT/UPDATE/DELETE), **TCC** (řízení transakcí: COMMIT/ROLLBACK/SAVEPOINT/SET TRANSACTION).

**Jak definujete schéma? Co jsou integritní omezení?** `CREATE TABLE` se sloupci, datovými typy a omezeními. Omezení: `NOT NULL`, `UNIQUE`, `PRIMARY KEY`, `FOREIGN KEY/REFERENCES`, `CHECK` — a navíc lze konzistenci vynutit **libovolnou procedurou / triggerem**.

**V jakém pořadí se vyhodnocují klauzule SELECTu?** Logicky: `FROM/JOIN` → `WHERE` (selekce řádků) → `GROUP BY` (skupiny) → `HAVING` (filtr skupin) → `SELECT` (projekce) → `ORDER BY` (řazení). `WHERE` filtruje před agregací, `HAVING` až výsledky agregace.

**Jaké jsou techniky JOINu?** **Nested loops** (pro každý řádek vnější tabulky hledej shodu ve vnitřní), **Sort-merge** (setřiď obě, procházej současně), **Hash join** (postav hash tabulku z menší relace, větší proti ní hledej).

### E. Transakce a recovery

**Co je transakce? Vysvětlete ACID.** Posloupnost DML příkazů převádějící DB z jednoho konzistentního stavu do druhého. **A**tomicity (vše, nebo nic), **C**onsistency (neporušení omezení), **I**solation (izolace od jiných transakcí), **D**urability (trvalé uložení po commitu).

**Úrovně izolace a jevy?** SERIALIZABLE (plná) → REPEATABLE READ (povolí phantom) → READ COMMITTED (povolí non-repeatable read) → READ UNCOMMITTED (povolí dirty read). Jevy: dirty read (čtení nepotvrzených), non-repeatable read (mezi čteními se hodnota změní), phantom (přibudou řádky).

**Jak se zajišťuje atomicita a trvanlivost (recovery)?** **Žurnálováním (log)**. **WAL (Write-Ahead Logging)** — log se zapíše na disk dříve než datová stránka. **Undo log** (stará hodnota → vrácení nepotvrzených), **redo log** (nová hodnota → přehrání potvrzených), **undo/redo** (obojí). **Checkpoint** zkracuje obnovu. Konkurenci řeší **zamykání** (pozor na **deadlock** — DBMS jednu transakci obětuje) nebo **MVCC** (verzování, čtenáři nečekají na zapisovatele).

### F. Indexování a hašování

**Co je index a jaké jsou typy?** Struktura `<klíč, ukazatel na záznam/blok>` urychlující vyhledání. **Dense** (položka na každý záznam) vs. **sparse** (jen na bloky, nutné setřídění). **Primary** (dle uspořádání souboru) vs. **secondary** (jiný klíč, nutné ukazatele, hustý). **Clustered** (data fyzicky uspořádaná dle indexu) vs. non-clustered. **Covering** (obsahuje vše pro dotaz).

**Popište B+-strom. Na čem závisí arita?** Vyvážený stromový index, **všechny listy na stejné úrovni**; vnitřní uzly jen navigují, klíče a ukazatele na data jsou v listech (často spojených v seznam → rychlé rozsahové dotazy). Uzel s $k$ syny má $k-1$ klíčů; min. zaplnění $\lceil m/2 \rceil$. **Arita závisí na velikosti diskového bloku** — větší blok = vyšší arita = nižší strom = méně I/O. Vyhledání ~ $O(\log n)$.

**Kdy index pomůže a kdy ne (SQL)?** Index se použije jen pro **predikát nad holým sloupcem**: `WHERE A > 5` ano, `WHERE A + 5 > 3` (funkce nad sloupcem) ne. Index zrychluje čtení, ale zpomaluje zápisy a zabírá místo.

**Index vs. hash?** **Hash** = bodové dotazy, průměrně $O(1)$, ale **neudrží uspořádání**. **B+-strom** nutný pro **rozsahové dotazy a `ORDER BY`**.

**Jak funguje hašování? Kolize, overflow?** Hash funkce mapuje klíč na adresu (bucket). **Kolize** = víc klíčů na stejnou adresu (nevadí, pokud se vejdou). **Overflow** = bucket plný. Řešení: **closed addressing** (overflow bloky, zřetězení — pro disk) vs. **open addressing** (kolizní funkce — pro in-memory).

**Statické vs. dynamické hašování?** Statické: pevný adresní prostor, při přetečení nutná reorganizace (nová hash funkce). Dynamické: **extendible** (horní bity + adresář, zdvojnásobení adresáře) a **linear** (dolní bity, bez adresáře, lineární růst, overflow řetězce).

**Proč ne kryptografická hash funkce pro index?** Je zbytečně drahá — pro indexování **nepotřebujeme odolnost vůči vzoru (preimage resistance)**, stačí rovnoměrné rozdělení klíčů.

### G. Zpracování a optimalizace dotazu

**Jak se vyhodnotí SQL dotaz?** Syntaktická kontrola → sémantická kontrola → **logický plán** (převod do relační algebry, transformační úpravy) → **fyzický plán** (konkrétní algoritmy, odhad nákladů) → výběr nejlevnějšího plánu → vykonání.

**Podle čeho se vybírá nejlepší plán?** Podle **odhadu nákladů založeného na statistikách** (metadata o tabulkách — počet řádků, počet různých hodnot atd.). Náklady jsou v abstraktních jednotkách. U **klasické (disk) DB** se minimalizují **diskové přístupy**, u **in-memory DB** se minimalizuje **čas procesoru**. Plán ukáže `EXPLAIN`.

### H. Příklady z praxe (rychlé)

- **Vždy indexuj cizí klíče** — jinak se DELETE/UPDATE v nadřízené tabulce dramaticky zpomalí (plný scan detailové tabulky).
- **WHERE místo HAVING**, když stačí — filtruje dřív, méně dat.
- **Řízená redundance** je OK, ale jisti ji `CHECK`/triggerem (např. rodné číslo vs. pohlaví).
- **Pozor na `= NULL`** — vždy NULL; používej `IS NULL`.
- **OLTP úzká hrdla** (buffer, zámky, recovery) → motivace pro in-memory / NewSQL.

---