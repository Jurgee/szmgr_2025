# DEV 4 Zpracování dat

> [!abstract] Základní pojmy a principy datových skladů, datové analytiky a business intelligence. Životní cyklus datového skladu. Analytika velkých dat, jazyky pro realizaci analytických úloh, analytika na úrovni databází. Pokročilé techniky zpracování dat, výkonnostní aspekty zpracování velkých dat. Příklady z praxe pro vše výše uvedené._ (PA036, PA220 || PA212)

> [!tip] Jak se to reálně zkouší (z reakcí komise) Komise (zejm. **Zezula, Nováček, Rebok, Batko, Dohnal**) typicky začne otevřeně: _„Databáze — popište co to je, k čemu to slouží, všechno okolo“_ nebo _„Zpracování dat / Spracovanie dát“_ a nechá vás mluvit. Pak se „chytí“ pojmu, který zmíníte, a jdou hlouběji. **Doporučení zkoušených: kreslete** (schéma hvězdy, B+ strom, kostku) — zabere to čas a ukáže pochopení. Časté pasti: relační algebra (Rebok do ní rád „vykoupe“), vyhodnocení dotazu + statistiky, složitost vyhledávání v B+ stromu/hashování, rozdíl OLTP/OLAP, proč DW místo file systému.

---
## 1. Základní pojmy: data, informace, znalosti, BI

### Business Intelligence (BI)

**BI = proces analýzy dat a prezentace výsledků manažerům pro informovaná rozhodnutí.** Zahrnuje nástroje a aplikace pro: sběr dat → přípravu pro uložení a analýzu → tvorbu a běh dotazů → reporty a dashboardy → vizualizaci.

- Vyvinulo se z **decision support systems (DSS)**; dnes přechází v _business analytics / (advanced) data analytics_ a _prescriptive analytics_.
- **BI vs. AI:** BI je „opak“ AI. AI systémy **rozhodují za uživatele**; BI **pomáhá uživateli rozhodnout se správně** na základě dostupných dat. (Mnoho BI technik má ale kořeny v AI.)

### BI pyramida (od dat ke znalostem)

Hierarchie hodnoty a abstrakce — odspodu nahoru:

|Vrstva|Co se děje|Příklad technologie|
|---|---|---|
|**Operational applications** (data)|zdroje dat, transakce|OLTP systémy|
|**OLAP analysis** (information)|analýza nad datovým skladem|data warehouse|
|**Information exploration**|statistické techniky|reporting, dashboardy|
|**Data mining** (knowledge)|učení modelů|predikce, ML|

> Data → Information → Knowledge. Čím výš, tím větší přidaná hodnota pro rozhodování.

### Klíčové problémy, které BI/DW řeší

1. **Složité a nepoužitelné modely** — DB modely jsou těžko srozumitelné, neslouží jednomu jasnému byznys účelu.
2. **Stejná data v mnoha systémech** — tentýž pojem (zákazník) je definován různě.
3. **Data uzpůsobená operativě** (účetnictví, fakturace) — nepodporují analýzy napříč funkcemi.
4. **Špatná kvalita dat** — chybějící, nepřesná, různé použití systémů.
5. **Data jsou „volatilní“** — mažou se / zastarávají, mění se v čase → bez historie.
---

## 2. Datový sklad (Data Warehouse) — definice a principy

### Tři klasické definice

- **Inmon** (Building the DW): _„Subject-oriented, integrated, time-varying, non-volatile collection of data used primarily in organizational decision making.“_ → **nejdůležitější definice, 4 vlastnosti.**
- **Kimball** (The DW Toolkit): _„Kopie transakčních dat specificky strukturovaná pro dotazování a analýzu.“_
- **Devlin (IBM):** jediné, kompletní a konzistentní úložiště dat z různých zdrojů, dostupné koncovým uživatelům srozumitelně v byznys kontextu.

### Čtyři vlastnosti DW (Inmon) — umět vyjmenovat a vysvětlit

1. **Subject-oriented (předmětově orientovaný)** — data organizovaná kolem byznys subjektů (Customer, Product, Order, Account…), ne kolem aplikací. Vše o jednom reálném objektu/události je propojeno.
2. **Integrated (integrovaný)** — data z většiny/všech operativních systémů, učiněna **konzistentními** (kódování pohlaví M/F, jednotky cm vs. inch, řešení konfliktních klíčů).
3. **Non-volatile (neměnný)** — data se po commitu **nepřepisují ani nemažou**; jsou _read-only_. Nahrávají se, ale neaktualizují; změna = nová verze/snapshot.
4. **Time-varying / time-variant (proměnný v čase)** — sledují se a zaznamenávají změny, aby reporty ukázaly vývoj v čase. Operativa má horizont 60–90 dní, **DW typicky 5–10 let**.

### Obecné požadavky na DW

Informace **snadno dostupné**, **konzistentní**, **včasné**, **chráněné**, **adaptabilní na změny** a **akceptované uživateli**.

> [!example] Příklad z praxe — proč vznikl DW (Walmart vs. Teradata) Walmart chtěl současně **zapisovat prodeje** a **dotazovat se** nad nimi (6-way JOIN přes sklady, města, výrobce…). Na OLTP systému dotaz trval 20 min a kvůli **pesimistickému zamykání** zamrzly pokladny. Řešení Teradaty: **kopírovat data z OLTP do samostatného DW** (loader ~100 GB/h) a analytiku spouštět tam. → Toto je důvod existence DW: oddělit analytickou zátěž od operativní.

---

## 3. OLTP vs. OLAP — must-know srovnání

> [!important] Nejčastější dílčí otázka komise: „Co je rozdíl mezi OLTP a OLAP?“

### OLTP (Online Transaction Processing)

- Každodenní operativa: mix insert/update/delete/read (zadávání objednávek, údržba zákazníků).
- Systém = **operational data store (ODS)**, drží **aktuální stav** dat.
- Z pohledu DB: **krátké operace**, většina dotazů **známa předem**, často **bodový přístup přes indexy**, **write access → ACID**.

### OLAP (Online Analytical Processing)

- Data pro **reporting a rozhodování**, **většinou read-only** (plánování zdrojů, marketing).
- Potřebuje archivní data; mírně zastaralá data jsou OK; reportuje **změny v čase**.
- Z pohledu DB: **dlouhé operace, většinou jen čtení**, dotazy **nejsou známy předem** a jsou **složité**, často **skeny velkých objemů**, data jsou (téměř) **append-only**.

### Tabulka rozdílů

|Kritérium|OLTP|OLAP / DW|
|---|---|---|
|Zaměření|operativní|taktické/strategické|
|Operace|insert/update/delete/read|převážně read|
|Délka dotazu|krátká|dlouhá|
|Dotazy|předem známé|ad-hoc, neznámé|
|Přístup k datům|bodový, přes indexy|full scany|
|Datové modelování|tradiční **ERD**, normalizace **3–5 NF**|**dimenzionální**, **0 NF** (denormalizováno)|
|Klíčový princip|ACID, zamykání|redundance, materializace|
|Velikost transakce|malá|velká|
|Velikost DB|10–400 GB|800 GB – 80 TB|

> **„Cubic“ technologie** je u čistého OLAP (MOLAP), zatímco DW/Data Mart bývá relační.

---

## 4. Architektura DW a životní cyklus

### BI architektura (datový tok)

```
Transaction data ──▶ Data Staging ──▶ Data Warehouse ──▶ Data Marts ──▶ Reporting
```

- **ODS (Operational Data Store):** aktuální integrovaná data, blízko zdrojům.
- **Data Warehouse:** centrální „single source of truth“; integruje více zdrojů, drží aktuální i historická data, odpovídá na multidimenzionální dotazy, tvoří agregované reporty.
- **Data Mart:** výřez DW pro jedno oddělení/skupinu (Marketing, Sales).
- V praxi se používají **database appliances** (Teradata, Oracle Exadata, IBM Netezza).

### Varianty architektury

1. **Independent data marts** (bez centrálního DW) — plněné přímo ze zdrojů; redundance, **žádný single source of truth**.
2. **Logical data marts** — data mart je jen **logický pohled** na celý DW; snazší integrovaný konzistentní pohled.

### Jak stavět DW: Top-down vs. Bottom-up

> [!important] Komise se ptá na top-down vs. bottom-up přístup.

- **Top-down (Inmon):** analyzovat globální potřeby, navrhnout a implementovat DW jako celek.
    - **+** globální obraz, konzistentní dobře integrovaný DW.
    - **−** vysoké náklady, dlouhá implementace, těžká analýza všech zdrojů najednou, těžko předvídat potřeby všech oddělení, dlouho není funkční systém → uživatelé ztrácí důvěru.
- **Bottom-up (Kimball):** DW se staví **inkrementálně**, iterativně vznikají jednotlivé **data marty** (každý kolem množiny faktů jednoho oddělení).
    - **+** konkrétní výsledky rychle, bez obrovských investic, lze zkoumat jednu oblast po druhé, rychlá zpětná vazba, udržuje zájem.
    - **−** může vést k částečnému/roztříštěnému pohledu na byznys.
- **První data mart** by měl hrát nejstrategičtější roli, být páteří celého DW a stát na dostupných konzistentních zdrojích.

### Query-driven vs. Warehouse-driven integrace

- **Query-driven (lazy):** data integrována na vyžádání. + nejaktuálnější data, bez duplikace; − pomalé, drahé pro časté dotazy, konkuruje lokálnímu zpracování. _V praxi se neujalo._
- **Warehouse-driven (eager):** data integrována dopředu a uložena v DW. + vysoký výkon dotazů, neruší zdroje; − duplikace dat, ne nejaktuálnější data. **Ujalo se v průmyslu.**

### Životní cyklus datového skladu (Kimball Lifecycle)

> [!important] Přímá dílčí otázka: „Životní cyklus datového skladu.“

Páteř (jedna větev) + paralelní větve:

```
Project Planning
   │
Business Requirements Definition
   ├──▶ Technical Architecture Design ──▶ Product Selection & Installation ─┐
   ├──▶ Dimensional Modeling ──▶ Physical Design ──▶ ETL Design & Development┤──▶ Deployment ──▶ Maintenance ──▶ Growth
   └──▶ BI Application Design ──▶ BI Application Development ───────────────┘
        (celé to zastřešuje Program/Project Management)
```

Fáze ve zkratce (3 hlavní iterativní bloky):

1. **Goal setting & planning** — cíle, hranice, velikost & priority; odhad nákladů/přínosů; analýza rizik a očekávání; zhodnocení dovedností týmu.
2. **Infrastructure design** — porovnat architektonická řešení, posoudit technologie/nástroje, vybrat přístup k návrhu a implementaci, vytvořit předběžný plán.
3. **Design & development of data marts** — každá iterace vytvoří nový data mart a nové aplikace, postupně se přidávají do DW.

Po nasazení: **Deployment → Maintenance → Growth** (růst spouští novou iteraci).

---

## 5. Dimenzionální modelování

> [!important] Jádro PA220. Komise: „popište kostku, dimenze, fakta“, „nakreslete schéma hvězdy“, „roll-up agregace / detailní pohled přes dimenze“.

### Návrh OLTP vs. reporting (DW)

- **OLTP design:** výkon = rychlé insert/update, minimalizace zamykání, malé výběry, kritická konzistence, zákony normalizace.
- **Reporting/DW design:** výkon = rychlé čtení velkých množin, zamykání není problém, rychlost insert/update nepodstatná → **denormalizace**.

### Dimenze, fakta, měřítka

- **Dimenze (dimension):** popisuje byznys událost (prodej produktu). To, podle čeho uživatel **třídí, seskupuje, filtruje** (datum, zákazník, prodejna). Často obsahuje **hierarchie** (city → region → state → country; date → day → month → quarter → year, weekday). Spodní úroveň hierarchie = nejjemnější granularita → určuje **atomic grain** faktové tabulky. Horní úroveň = typicky **„ALL“**.
- **Fakt (fact):** měřitelná metrika popsaná dimenzemi (sale amount, order quantity). Fakt = neprázdná buňka (v kontingenční tabulce); identifikován hodnotami dimenzí. Obvykle je **mnohem více dimenzí než faktů**. Fakt má být navázán právě na jednu hodnotu v každé dimenzi a na **spodní úrovně** dimenzí.
- **Měřítko (measure):** hodnota faktu, kterou chceme analyzovat. Má dvě složky: **numerickou hodnotu** + **agregační formuli** (typicky SUM).

### Typy faktů

- **Transaction (event) fact** — fakt pro každou byznys událost (prodej, objednávka).
- **Snapshot fact** — fakt pro každou kombinaci dimenzí v daném časovém intervalu; zachycuje aktuální stav (inventář). _Periodic snapshot._
- **Cumulative snapshot fact** — kumulativní stav až do teď (např. zpracování pojistné události); řádek faktu se **aktualizuje**.
- **Fact-less fact** — fakt bez numerických měr (kontakt se zákazníkem); jen „událost nastala“.
- **Aggregated fact** — předagregované součty pro zrychlení dotazů.
- **Consolidated fact** — fakta z více procesů na stejné granularitě (skutečné prodeje + forecast).

### Typy měřítek (aditivita) — častá past

- **Plně aditivní:** lze SUM přes **všechny** dimenze (sales price, gross profit). Časté u transaction faktů.
- **Semi-aditivní:** nelze agregovat přes **některé** dimenze (typicky čas) — např. **zůstatek na účtu** je aditivní přes vše kromě času. Časté u snapshotů.
- **Neaditivní:** nelze SUM přes žádnou dimenzi (jednotkové ceny, poměry/ratia). Místo SUM třeba AVG.
- **Aggregated / Derived measure** — předpočítané, resp. odvozené z jiných měřítek (gross profit z revenue − cost), nemusí být fyzicky uloženo.

### Grain (granularita)

**Grain = úroveň detailu jednoho řádku faktu** (co znamená jeden fakt). Dán kombinací spodních úrovní dimenzí ("total sales per store per day per product"). Důležitý pro škálovatelnost počtu faktů. **Atomic grain** = nejjemnější možná úroveň.

### Schéma hvězdy (Star schema)

- Centrální **faktová tabulka** (měřítka + cizí klíče do dimenzí) obklopená **dimenzionálními tabulkami**.
- Dimenze se vážou **přímo** na fakt, jsou **denormalizované** (žádné lookup tabulky jako v OLTP).
- **Primární klíč faktu** = kombinace FK do dimenzí; klíče dimenzí jsou **surrogate keys** generované při ETL.
![[star_schema.jpg]]

### Schéma vločky (Snowflake schema)

- Dimenze normalizovaná do dalších tabulek (dimenze odkazuje na další dimenzi).
- **+** hierarchie explicitně viditelné, flexibilní, dimenze zaberou méně místa (ale úspora je minimální — dimenze < 5 % místa DW).
- **−** těžší použití (mnoho JOINů), horší výkon (nelze efektivně použít bitmapové indexy).
- **Pravidlo:** OLTP vývojáři mají potlačit nutkání normalizovat na vločky — málokdy se to vyplatí.
![[Snowflake-schema.webp]]
### Klíčové pojmy

- **Surrogate keys (náhradní klíče):** umělé celočíselné klíče (1,2,3…), PK dimenzí, generované DW. Originální (přirozený) klíč se přidá jako běžný atribut.
    - **+** nezávislost DW na změnách operativních klíčů, řeší překryv klíčů při konsolidaci, klíče bez „inteligence“, řeší NULL (chybějící datum → nesmí být NULL ve FK faktu), lepší výkon (malý integer 2–4 B vs. dlouhý alfanumerický kód) → menší faktové tabulky (1 B v 1 mld. řádků = 1 GB), **nutné pro historii SCD**.
    - **−** nutné implementovat, více místa v dimenzích.
- **Conformed dimension (konformní dimenze):** konsolidovaný seznam hodnot dimenze podporující všechny zdrojové systémy; **kritická pro úspěšný DW**, sdílená napříč byznys procesy.

### 4 kroky dimenzionálního modelování (Kimball)

1. **Choose the business process** (zvol byznys proces) — co má DW pokrývat (popsat textem / BPMN / UML).
2. **Declare the grain** (deklaruj granularitu) — přesný popis jednou větou ("jednotlivá položka na účtence").
3. **Identify the dimensions** (identifikuj dimenze) — podstatná jména (date, store, inventory).
4. **Identify the facts** (identifikuj fakta) — numerická aditivní čísla (quantity, cost per unit).

> Pomůcka **„7 W“**: How, What, When, Where, Who, How many/much (= fakt), Why. Intuitivní pro byznys uživatele.

### DW Bus Matrix (Enterprise Bus Matrix)

Matice **byznys procesy (řádky) × společné dimenze (sloupce)**. Jeden sloupec = jedna dimenze, jeden řádek = jedna faktová tabulka. Hledá **konformní dimenze** referencovatelné z více procesů → dobrý DW design používá jen konformní dimenze.

---

## 6. Slowly Changing Dimensions (SCD)

> [!important] Velmi oblíbená dílčí otázka. Umět typy 1/2/3 a princip surrogate key + timestamp.

**Problém:** dosud jsme předpokládali, že dimenze jsou stabilní (jen se přidávají řádky). V praxi se hodnoty dimenzí mění (prodejna se přesune z regionu, produkt změní popis). Jak změnu zachytit závisí na tom, jakou hodnotu má pro byznys znát historii.

|Typ|Princip|+|−|
|---|---|---|---|
|**Type 1: Overwrite**|přepiš starou hodnotu|snadné; ideální při opravě chyb|**ztráta historie** — staré fakty se počítají pod novou hodnotu (today-for-yesterday)|
|**Type 2: Add new row**|nový řádek (nový surrogate key) pro novou verzi; fakty ukazují na verzi platnou v době události|**přesná historie** (historical truth)|nejsložitější; větší dimenze; bez časování nezachytí _kdy_ se změnilo|
|**Type 3: Timestamping**|Type 2 + sloupce `From`/`To` (validity interval) + `Master` (odkaz na původní řádek)|podporuje všechny scénáře (rollback „yesterday-for-today“)|větší dimenze|
|**Type 3b: Special facts**|Type 2 + speciální fakt s NULL měřítkem ukazující na nový řádek dimenze, časovaný přes Time dimenzi|zachytí vývoj subjektů v čase|složitější práce|

**Tři pohledy na čas:**

- **Today-for-yesterday** (Type 1) — vše interpretováno z pohledu _aktuálních_ hodnot dimenze.
- **Today-or-yesterday / historical truth** (Type 2) — událost se analyzuje podle hodnot platných _v době_ události.
- **Yesterday-for-today / rollback** (Type 3) — po zadání data najdeme řádky platné tehdy.

### Rapidly Changing Dimensions + Type 4 (Mini-dimension)

- Rozdíl slowly/rapidly je subjektivní; problém je **velikost** dimenze (Employee 100k, Customer 10M = „monster dimensions“).
- **Type 4 — Mini-dimension:** často se měnící atributy (demografie) vyčlení do malé **mini-dimenze**; spojité atributy se převedou na pásma (banding). S 6 atributy × 10 hodnot = 1 000 000 řádků kombinací. Faktová tabulka má pak FK i do mini-dimenze (**outrigger**).

---

## 7. ETL proces (Extract – Transform – Load)

> [!important] Komise (2025): „BI, OLTP/OLAP, proč DW, ETL“ — ETL stačí popsat principy.

**ETL = nejsložitější a nejvíc podceňovaná část DW — často ~80 % vývojového času.**

```
Sources ──Extract──▶ Staging area ──Transform/Clean──▶ Load ──▶ DW
```

### Data Staging Area

Tranzitní úložiště během ETL; provádí se zde transformace/čištění; **žádné uživatelské dotazy**; sekvenční operace nad velkými objemy; centrální ETL logika; snadno restartovatelné; bez zamykání/logování. Hotové dimenze se kopírují do data martů.

### Typy ETL procesu (kdy spouštět)

- **Initial Load** (prvotní naplnění)
- **Propagate Changes:** _Periodic Refresh_ (periodické přenahrání), _Periodic Delta_ (jen změny), _Online Propagation_ (stream, real-time).
- Trade-off: online propagace = stálá zátěž obou stran, bez bulk loadu; častější update = méně zastaralá data, ale větší režie.

### Extract — zachytávání změn (Change Data Capture)

- **Audit columns** — `last modified` timestamp / „new“ flag.
- **Full Diff** — diff starý snapshot vs. aktuální; důkladné, ale náročné (hashe/checksumy pro zrychlení).
- **Database Log Scraping** — čtení write-ahead logu (obsahuje všechny změny).
- **Message Queue Monitoring** — zdroj používá messaging framework → nízká režie.

### Transform — čištění (Data Cleansing)

Reálná data jsou „messy“. Úkoly: oprava překlepů, konverze typů, řešení NULL, **deduplikace** (record matching), řešení konfliktů, **konformace** dat.

**Primitiva čištění:** Parsing (rozdělení adresy na street/zip/city), **Similarity Join** (spojení podobných dat), **Clustering** (předzpracování pro deduplikaci), **Outlier detection**.

**Míry podobnosti pro Similarity Join:**

- **Edit distance** — min. počet operací (insert, delete, replace, transpose) pro transformaci s₁→s₂. **Levenshtein** = ins/del/replace; **LCS** (longest common subsequence) = jen ins/del.
- **Jaccard koeficient** — |průnik|/|sjednocení| množin (q-gramy: `2grams("Sweet")={Sw,we,ee,et}`).
- **SoundEx** — fonetický algoritmus (indexuje slova podle zvuku): `soundex("Robert")=soundex("Rupert")=R163`.

**Algoritmy párování:**

- **Naive** O(n²) — porovnej každého s každým.
- **Blocking** — rozděl do bloků (např. dle ZIP / první písmeno příjmení), porovnávej jen uvnitř bloku.
- **Sorted neighborhood** — přiřaď sort key, seřaď, klouzavé okno velikosti _w_.

### Data Quality (požadavky)

Data v DW musí být: **Precise** (přesná), **Complete** (úplná), **Consistent** (konzistentní — agregáty sedí s detaily), **Unique** (totéž má stejný klíč), **Timely** (dostatečně aktuální). _Data téměř nikdy nemají dobrou kvalitu._

### Error handling

- **Halting** — zastav a oprav ručně.
- **Suspense file (logging)** — loguj chyby stranou.
- **Tag the data and continue** — nejlepší: vadné fakty → **audit dimension**; vadná dimenze → unikátní error hodnoty. Lze vytvořit **error event schema** (grain = výskyt chyby).
- **Data stewards** — lidé zodpovědní za kvalitu dat konkrétních tabulek.

---

## 8. OLAP dotazy a operace

> [!important] Komise (2025): roll-up (hierarchický i dimenzionální), drill-down, kostka, agregace vs. detail přes dimenze.

### Datová kostka (Data Cube)

- Symbolická reprezentace agregovaných dat; může mít **mnoho dimenzí** (>3 = „hyper-cube“), typicky **4–12 dimenzí**.
- Kostka se skládá z **buněk (cells)**. **Sparse cube** = málo neprázdných buněk (kostka řídne s počtem/velikostí dimenzí), **dense cube** = mnoho neprázdných.
- Dimenzionalita se snižuje dotazy přes projekci/agregaci.

### Operace OLAP

- **Roll-up (drill-up):** vezme aktuální úroveň agregace a agreguje dál → větší zobecnění.
    - **Hierarchický roll-up** — výstup po hierarchii (Time.Week → Time.Year).
    - **Dimenzionální roll-up** — vypuštění jedné/více dimenzí (drop Client).
    - Roll-up nad vrchol hierarchie (atribut „ALL“) = konverze na dimenzionální roll-up.
- **Drill-down (roll-down):** opak roll-upu, **de-agregace** na jemnější detail; vyžaduje existenci materializovaných jemnějších dat („nelze drillovat, když data nejsou“).
- **Slice:** výběr podmnožiny pro **jednu hodnotu** jedné dimenze + projekce na ostatní (`WHERE article_id = laptop`). = equality select na jedné dimenzi.
- **Dice:** **rozsahová** podmínka na jedné dimenzi NEBO equality na **více** dimenzích (`WHERE article_id=laptop OR cellphone`).
- **Pivot (rotate / crosstab):** přeskupení pro zobrazení; vybere 2 dimenze k agregaci měřítka do mřížky (cross tabulation). Prostorově efektivní jen pro hustá data.
- **Drill across:** přechod z jedné faktové tabulky do jiné přes **sdílenou (konformní) dimenzi**.
- **Drill through:** zobrazení relačních transakcí tvořících bod v kostce.

![[olap_cube.png]]

### MOLAP / ROLAP / HOLAP

- **MOLAP (Multidimensional OLAP):** agregace v multidimenzionálních strukturách; inteligentní pre-agregace; dotazovací jazyk **MDX (MultiDimensional eXpressions)**; rychlé, ale méně flexibilní (kostku nutno přepočítat při změně designu).
- **ROLAP (Relational OLAP):** kostka nad relační DB; redundance je nutnost — **materializované pohledy, speciální indexy, denormalizovaná schémata**.
- **HOLAP:** hybridní (detail relačně, agregace multidimenzionálně).

### SQL & OLAP — jazyky pro analytiku

> [!note] Dílčí otázka: „jazyky pro realizaci analytických úloh.“

- **Žádný standardní OLAP jazyk** — SQL99 pro ROLAP, **SQL:2003 OLAP rozšíření**.
- Nedostatky SQL/92: těžko vyjádřitelné multiple aggregations (vyžadují UNION více GROUP BY), porovnání (self-join), reporting (RANK, NTILE, median, moving average), výkonové penalty.
- **SQL99 grouping operátory:**
    - `GROUP BY ROLLUP(cols)` — hierarchické subtotály (n+1 úrovní).
    - `GROUP BY CUBE(cols)` — všechny možné kombinace agregací.
    - `GROUP BY cols1, CUBE(cols2)` — částečný roll-up.
    - `GROUP BY GROUPING SETS(...)` — explicitní výčet roll-upů.
    - `GROUPING_ID(cols)` — identifikace úrovně roll-upu.
- Typický OLAP dotaz: `SELECT d1.x, d2.y, SUM(f.t1), AVG(f.t2) FROM Fact f, Dim1 d1, Dim2 d2 WHERE ... GROUP BY d1.x, d2.y;` (select+group dle atributů dimenzí, agreguj měřítka).
- **`ALL` = NULL v SQL** v crosstab reportech (dummy hodnota pro „vše/více hodnot“).

---

## 9. Indexování a optimalizace (výkonnostní aspekty)

> [!important] Komise miluje indexy: B+ stromy (složitost, arita = velikost bloku), hashování, bitmapy, JOINy (nested loop/merge/hash).

### Bitmapový index (Bitmap index)

- Pro atribut = kolekce **bit-vektorů**; počet vektorů = počet **distinct hodnot** atributu; délka vektoru = **kardinalita relace** (počet řádků). Bit signalizuje přítomnost hodnoty v řádku. Komprese **run-length encoding**.
- **Dotazy:** OR/AND hodnot = bitové operace na vektorech; lze kombinovat různé indexy téže tabulky.
- **Vhodný pro málo distinct hodnot** (gender, velikosti oblečení) → nízká selektivita; kombinace indexů → vysoce selektivní predikáty. Výkon jako B+ strom pro statická data.
- **Nevhodný:** mnoho distinct hodnot (mnoho bit polí) → řešení **Multi-component** (kódování v jiné číselné soustavě, např. <div,mod>, 100 hodnot → 20 vektorů místo 100) a **Range-encoded** (pro rozsahové dotazy — nastav bit ve všech následujících vektorech; rozsah = NOT previous AND current).

![[bitmap_index.webp|368]]

### Další indexy

- **B-stromy** — nevhodné pro multidimenzionální data.
- **UB-tree** — kombinuje B-strom s prostorovou křivkou (Z-order), vhodný pro vícerozměrná data a rozsahové dotazy; sousedící data clusterovaná.
- **R-tree** — nemusí škálovat do mnoha dimenzí.
- **Shrnutí:** bitmapy = zlomek velikosti dat, snižují odezvu pro velké třídy ad-hoc dotazů.
- **B+ strom (z DB otázek komise):** arita uzlu se volí **podle velikosti bloku na disku**; složitost vyhledávání **O(log n)**; data v listech.

### Data Partitioning (rozdělení dat)

Rozbití dat na **nepřekrývající se** části. Zlepšuje: výkon dotazů (méně dat ke skenu), dostupnost (backup/restore po particích), administraci (archivace, reindexace, loading).

- **Logické dělení** podle Date / Line of business / Geography / Org. unit / kombinace.
- **Fyzické dělení** — data na různé uzly → paralelizace na více discích/strojích.
- **Horizontální partitioning** — rozdělení **řádků** do více tabulek; partition function: **range / list / hash**. V DW: faktová tabulka dělená dle Time (týdny/měsíce) nebo stabilní dimenze (region).
- **Vertikální partitioning** (row splitting) — rozdělení **sloupců**; 1:1 vztah mezi particemi; málo používané/široké sloupce na jiné zařízení (souvisí s mini-dimenzí/outrigger).
- Doporučení: particionovat tabulky > 2 GB / > 100 mil. řádků.

### Optimalizace JOINů v DW

- Dotazy přes více dimenzí → mnoho JOINů (drahé). JOINy jsou **komutativní a asociativní** → libovolné pořadí → **join trees** (počet roste jako n!).
- Optimalizátor: statistiky pro minimalizaci výsledku, heuristiky (>6 relací → např. genetické algoritmy).
- **Heuristiky výběru JOIN:** spojuj relace s podmínkou (vyhni se cross joinům), minimalizuj velikost mezivýsledku `T(R)·T(S)/max(V(R,A),V(S,A))`, využij indexy a selektivitu.
- **V DW se OLTP heuristiky nehodí** — pokud selektivita predikátu je vysoká (velký podíl řádků), index nepomůže.
- **Doporučení DW:** **cross-join dimenzí** (10 obchodů × 20 dní × 50 produktů = 10 000 řádků) → jeden průchod faktem; pokud je cross-join příliš velký, **protni částečné (semi-)joiny** (když jsou všechny dimenzionální FK indexovány).

### Materializované pohledy (Materialized views)

- Pohledy, jejichž n-tice jsou **fyzicky uloženy** → rychlý přístup jako (vysokoúrovňová) cache.
- Nutno **udržovat obsah** při změně podkladových tabulek → ideálně **inkrementální view maintenance**.
- Klíčové pro ROLAP pre-agregace.

---

## 10. Analytika velkých dat (Big Data)

> [!important] Komise (2025, SWE-DEV): „big data charakteristika, MapReduce, Hadoop, co je data warehouse.“ NoSQL typy a kdy je použít.

### Charakteristiky Big Data — „V“

- **Volume** — objem dat roste 10× každých 5 let.
- **Variety** — různá struktura: text, multimédia, semi/nestrukturovaná.
- **Velocity** — kontinuální tok ze senzorů, sociálních sítí; real-time.
- **Veracity** — s různými zdroji těžší udržet jistotu/kvalitu dat.
- (**Value** — schopnost přeměnit data v byznys hodnotu.)

### Škálování

**Horizontální škálování** (přidávání uzlů) místo **vertikálního** (silnější HW). Horizontální je obecně náročnější navrhnout, ale pro big data nezbytné.

### Hadoop platforma

SW knihovna pro distribuované zpracování velkých dat napříč clustery; **vysoká dostupnost na aplikační vrstvě replikací** (běží na nespolehlivém HW).

- **HDFS** — distribuovaný file system s vysokou propustností:
    - Soubory děleny na **bloky/chunky** (typicky **64 MB**), replikovány na **3 různé stroje** (chytře — ne všechny na stejném racku). Blok a replikační faktor laditelné per soubor.
    - **Name node (master)** drží metadata (mapping soubor→chunky, umístění); **data nodes (chunk servers)** drží data. Klient se ptá masteru, pak kontaktuje data nody. Metadata masteru replikována.
    - Soubory jsou **write-once** (kromě append/truncate), právě jeden writer.

### MapReduce

Programovací model pro distribuované zpracování; uživatel definuje **jen map & reduce** funkce; tři fáze:

1. **Map** — transformace: vstup = 1 datová položka → výstup 0+ párů `(key, value)`. Klíče nemusí být unikátní. `Map(k1,v1) → list(k2,v2)`.
2. **Grouping / Shuffle** (automaticky framework) — páry seskupeny dle klíče; hodnoty stejného klíče → stejný reducer jako `(key, list)`.
3. **Reduce** — kombinace hodnot pro každý klíč → finální výsledek. `Reduce(k2, list(v2)) → list(k3,v3)`.

> [!example] Word Count (klasický příklad)
> 
> ```
> map(name, content):  for word w in content: emit(w, 1)
> reduce(word, counts): emit(word, sum(counts))
> ```

- **Combiner** — pre-agregace v mapperu (`combine(k, list(v1)) → v2`), obvykle stejný jako reduce; funguje jen pokud je reduce **komutativní a asociativní**; **šetří síťový přenos** (méně dat se shuffluje).
- **Partition function** — řídí, na který reducer klíč jde; default `hash(key) mod R`; lze přepsat (`hash(hostname(URL)) mod R`).
- **Stragglers (pomalí workeři)** — řešení: ke konci fáze spustit **backup kopie** úkolů, vyhraje rychlejší → dramaticky kratší dokončení.
- **Fault tolerance** — selhání úkolu → re-execute; master padne → abort.

### Cost model MapReduce (výkonnostní aspekty — PA212)

- **Communication cost** = celkové I/O všech procesů = vstupní soubor + 2× (velikost souborů Map→Reduce) + výstup reducerů.
- **Elapsed communication cost** = max I/O podél libovolné cesty (wall-clock při paralelizaci).
- Buď dominuje I/O (komunikace), nebo computation; _big-O_ zde není nejužitečnější (vždy lze přidat stroje). Total cost = co platíš cloudu; elapsed = reálný čas.
- **Reducer size / replication rate** — limit _s_ na vstup/výstup jednoho procesu (co se vejde do RAM/disku).

> [!example] Natural Join R(A,B) ⋈ S(B,C) v MapReduce Map: `R(a,b)→(b,(a,R))`, `S(b,c)→(b,(c,S))`. Reduce h(b) páruje všechny (a,R) s (c,S) → `(a,b,c)`.

### Apache Spark

Unified analytics engine pro velká data; vysoký výkon batch i streaming; **DAG scheduler**, query optimizer, fyzický execution engine; **~100× rychlejší než Hadoop** (in-memory).

### Stream processing

- **Storm** — real-time, škálovatelný, fault-tolerant; algoritmus jako **DAG**: _spouts_ (zdroj), _bolts_ (zpracování), hrany = streamy tuplů.
- **Spark Streaming**.

### NoSQL a distribuovaná úložiště (typy + kdy použít)

> [!note] Komise: „NoSQL typy, kde jsou vhodné — příklady z praxe.“

- **Key-value / wide-column stores (HBase):** strukturovaný/tabulkový model, **flexibilní schéma**, horizontální škálování, **bez ACID, bez JOINů**; key = řádek (s timestampem), value = multidimenzionální struktura. _Vhodné pro:_ obrovské objemy, vysoká propustnost, jednoduché přístupové vzory.
- **Column stores (C-store, Vertica):** relační model, hodnoty sloupce uloženy souvisle, **read-optimized**, vysoká dotazovací propustnost, **relaxovaná konzistence** čtení. _Vhodné pro:_ analytiku/agregace (čtení mála sloupců přes mnoho řádků).
- **Document stores:** JSON/dokumenty, flexibilní schéma. _Vhodné pro:_ obsah s proměnnou strukturou.
- **Graph DB:** vztahy/grafy. _Vhodné pro:_ sociální sítě, doporučování.
- **Real-time / NewSQL (VoltDB / H-Store):** škálovatelnost NoSQL + relační model + **ACID**, row-oriented na shared-nothing clusteru, in-memory, fault-tolerance replikací.

**ACID vs. BASE / CAP:** klasické DB ACID; mnoho NoSQL volí **BASE** (Basically Available, Soft state, Eventual consistency). **CAP teorém:** z {Consistency, Availability, Partition tolerance} lze při výpadku sítě garantovat jen 2 → eventual consistency (master-slave replikace).

### Distribuovaný DW / analytika nad big data

- **Hive** — DW pro velké datasety: nestrukturovaná data v HDFS, **schema-on-read**, dotazy **HiveQL** → převod na MapReduce joby, podpora indexace, UPDATE/DELETE na úrovni řádku.
- **Pig** — dataflow jazyk nad MapReduce.
- **Kylin** — OLAP pro big data: pre-počítané agregace (kostky) na Hadoop/Spark, sub-second dotazy, integrace s Tableau/Power BI.
- **Apache Mahout** — škálovatelná ML knihovna (doporučování, collaborative filtering, clustering, klasifikace).
- **R** — statistické výpočty a vizualizace.

### Power BI (nástroj, PA220 l.10)

Self-service BI nástroj: **Power Query (jazyk M)** pro ETL/import, **DAX (Data Analysis Expressions)** pro měřítka a kalkulované sloupce, datový model typu hvězda, reporty a dashboardy, režimy **Import** vs. **DirectQuery**.

---

## 11. Pokročilé techniky zpracování dat (PA212)

> [!info] PA212 = „Advanced Search Techniques for Large Scale Data Analytics“ (Zezula, Sedmidubský). Pokrývá výkonnostní a algoritmické aspekty big data.

### Bonferroniho princip (data mining caveat)

Při hledání vzorů v dostatečně velkých datech najdeme „zajímavé“ vzory i čistě **náhodou**. Bonferroni: počet hledaných jevů musí být malý vůči pravděpodobnosti náhodného výskytu, jinak jsou nálezy artefakty.

### Frequent itemsets & Association rules (Market-basket model)

- **Support** itemsetu I = počet košů obsahujících I; **frequent** = support ≥ práh _s_.
- **Asociační pravidlo** I→j: **Confidence** = `support(I∪{j}) / support(I)`. **Interest** = `conf(I→j) − Pr[j]` (zajímavá pravidla mají |interest| vysoký, typicky > 0,5). Ne každé high-confidence pravidlo je zajímavé (pravá strana může být prostě častá).
- **A-Priori algoritmus** — klíč je **monotonicita (downward closure):** je-li množina frequent, jsou frequent i všechny její podmnožiny; kontrapozice: pokud item není frequent, žádná dvojice s ním není frequent.
    - **Pass 1:** spočti výskyty jednotlivých itemů → frequent items (paměť ∝ #items).
    - **Pass 2:** počítej jen páry, kde **oba** itemy jsou frequent (paměť ∝ čtverec frequent items). Triangular matrix metoda.
    - Obecně: `C_k` (kandidáti) → `L_k` (frequent k-tice) iterativně.
- Varianty: **PCY** (hashování párů v pass 1), **SON**, **Toivonen** (random sampling).

### Finding Similar Items (LSH pipeline)

> [!important] Často: shingling → minhashing → LSH. Jaccard.

- **Jaccard similarity** sim(C₁,C₂) = |C₁∩C₂|/|C₁∪C₂|; **Jaccard distance** = 1 − sim.
- **1) Shingling** — převod dokumentu na množinu **k-shinglů** (k-gramů); pro 2-shingly `S("abca")={ab,bc,ca}`. Dlouhé shingly se hashují.
- **2) Min-Hashing** — převod velkých množin na krátké **signatury** zachovávající podobnost. Permutace řádků charakteristické matice; minhash = index prvního 1 v permutaci. **Pr[minhash(C₁)=minhash(C₂)] = Jaccard(C₁,C₂)**. Použij ~100 permutací (resp. hash funkcí — permutace jsou drahé).
- **3) LSH (Locality-Sensitive Hashing)** — soustřeď se jen na **kandidátní páry** pravděpodobně podobné. Signaturovou matici rozděl na **b pásem (bands) po r řádcích**; každé pásmo zahashuj do bucketů; **kandidát = stejný bucket v ≥1 pásmu**. Ladění **b a r** → S-křivka, práh ≈ `(1/b)^(1/r)`. Produkuje **false positives i false negatives**.

### Clustering

- **Cíl:** prvky v clusteru blízké/podobné, mezi clustery nepodobné. Podobnost přes vzdálenost (Euclidean, Cosine, Jaccard, edit distance). Clustering ve více dimenzích je těžký (curse of dimensionality).
- **Hierarchický (agglomerative, bottom-up):** každý bod = cluster, opakovaně spojuj 2 nejbližší. Otázky: jak reprezentovat cluster (centroid/clustroid), jak měřit blízkost, kdy přestat. (Divisive = top-down.)
- **Point assignment — k-means:** zvol _k_, inicializuj centroidy (po jednom bodu na cluster), přiřaď body k nejbližšímu centroidu, přepočítej centroidy, opakuj. Volba _k_ = **elbow method**.
- **BFR (Bradley-Fayyad-Reina)** — varianta k-means pro **obří data v hlavní paměti**; tři množiny: **Discard Set (DS)** (blízko centroidu, shrnuto statistikami N, SUM, SUMSQ), **Compressed Set (CS)** (shluky bodů daleko od centroidů), **Retained Set (RS)** (izolované body). Předpokládá normálně rozdělené clustery podél os.
- **CURE (Clustering Using REpresentatives)** — řeší problém BFR/k-means s neklasickými tvary clusterů; cluster reprezentován **více reprezentativními body** posunutými k centroidu.

### Mining Data Streams

> [!note] Stream = nekonečný tok tuplů; nelze uložit celý, jen _summary_ v omezené paměti.

- **Sampling fixed proportion** — vzorkuj 1/10 streamu; pozor: vzorkovat **dle klíče** (uživatele), ne náhodně dle tuplu, jinak zkreslení.
- **Reservoir Sampling** — udržuj reprezentativní vzorek pevné velikosti _s_ z proudu (i-tý prvek přijat s pravděpodobností s/i).
- **Bloom filter** — pravděpodobnostní filtr příslušnosti k množině; **bez false negatives**, ale s **false positives**; několik hash funkcí + bitové pole. Filtrování streamu.
- **Count-distinct — Flajolet-Martin** — odhad počtu různých prvků dle max. počtu koncových nul v hashích (R → ~2^R distinct).
- **DGIM** — počet jedniček v posledních N bitech v klouzavém okně bez uložení celého okna (exponenciálně rostoucí buckets).
- **Moments (AMS algoritmus)** — odhad momentů (surprise number).

### Link Analysis — PageRank

> [!important] PageRank = míra důležitosti stránky podle grafu odkazů.

- **Myšlenka:** stránka je důležitá, pokud na ni odkazují důležité stránky; „hlas“ stránky se dělí mezi její odchozí odkazy.
- **Stochastická matice M** (column-stochastic): `M[i][j] = 1/deg(j)` pokud j→i. Rank vektor **r** je **vlastní vektor**: `r = M·r` (hlavní eigenvektor s vlastním číslem 1).
- **Power iteration:** `r⁽⁰⁾ = [1/N,…]`, iteruj `r⁽ᵗ⁺¹⁾ = M·r⁽ᵗ⁾` do konvergence.
- **Problémy:**
    - **Dead ends** (stránka bez odchozích odkazů) — matice není column-stochastic, importance „leaks out“. Řešení: vždy teleportuj, když není kam jít.
    - **Spider traps** (skupina stránek odkazujících jen na sebe) — absorbuje veškerou importance. Řešení: teleport.
- **Teleporty (Google řešení):** s pravděpodobností **β** (typicky 0,8–0,9) následuj odkaz, s pravděpodobností **(1−β)** skoč na náhodnou stránku.
- **Google matrix:** `A = β·M + (1−β)·(1/N)·[1]ₙₓₙ`; iteruj `r = A·r`. Vzorec pro uzel: `r_j = Σ_{i→j} β·r_i/deg(i) + (1−β)/N`.
- **Topic-Specific (Personalized) PageRank** — teleport jen do množiny topic-relevantních stránek.
- **HITS (Hubs & Authorities)** — jiná míra: _authority_ (kvalitní obsah) a _hub_ (odkazuje na dobré authority); vzájemně se posilují.
- **Link spam & TrustRank** — umělé link farmy boostují PageRank; TrustRank teleportuje jen do důvěryhodných seed stránek.

### Retrieval Evaluation (vyhodnocení vyhledávání)

> [!note] „Jazyky/úlohy analytiky“ + efektivita vs. efektivnost vyhledávání.

- **Precision** = |relevantní ∩ vrácené| / |vrácené| (kolik z vrácených je relevantních).
- **Recall** = |relevantní ∩ vrácené| / |relevantní| (kolik relevantních se vrátilo).
- **Precision-Recall křivka** — interpolovaná na 11 úrovních recallu.
- **F-measure (F1)** = harmonický průměr precision a recall: `F = 2PR/(P+R)`. (E-measure pro b=1 → F-measure.) Harmonický průměr je vysoký, jen když jsou **obě** míry vysoké.
- **MAP (Mean Average Precision)** — průměr průměrných precision přes dotazy.
- **R-Precision** — precision na pozici R (= počet relevantních).
- **(n)DCG (Discounted Cumulated Gain)** — bere v úvahu **pozici** a **stupeň relevance** (gain dělený logaritmem pozice); nDCG = normalizovaný na ideální pořadí.
- **Reference collection** = dokumenty + dotazy + relevance judgments (uniformní → rychlé vyhodnocení; nejpoužívanější metoda v IR).

### Recommender systems (doporučovací systémy)

- **Utility matrix** U (uživatel × položka) je řídká; problém **cold start** (nový uživatel/položka bez dat).
- **Content-based** — doporuč položky podobné těm, co uživatel měl rád (stejný herec/žánr); profil položky + profil uživatele.
- **Collaborative filtering** — doporuč podle podobných uživatelů (user-user) nebo podobných položek (item-item); měří podobnost (cosine, Jaccard).
- **Hybridní** — kombinace.

---

## 12. Rychlý souhrn pojmů (cheat sheet)

|Pojem|Jedna věta|
|---|---|
|**DW (Inmon)**|subject-oriented, integrated, time-varying, non-volatile sbírka dat pro rozhodování|
|**OLTP vs OLAP**|OLTP = krátké zápisy/ACID/3NF; OLAP = dlouhé čtení/full scan/0NF/dimenzionální|
|**Star schema**|denormalizovaný fakt + dimenze, surrogate PK|
|**Snowflake**|normalizované dimenze, více JOINů, horší výkon|
|**Grain**|úroveň detailu jednoho řádku faktu|
|**Aditivita**|full / semi (ne přes čas) / non-additive|
|**SCD**|Type1 přepis (bez historie), Type2 nový řádek (historie), Type3 timestamp, Type4 mini-dim|
|**ETL**|extract → transform/clean → load; ~80 % vývoje|
|**Roll-up/Drill-down**|agregace (nahoru) / de-agregace (dolů, jemnější detail)|
|**Slice/Dice**|1 hodnota 1 dim / rozsah n. více dim|
|**Bitmap index**|bit-vektory, málo distinct hodnot, OR/AND|
|**Materializovaný pohled**|fyzicky uložený pohled = cache pro agregace|
|**Big Data 4V**|Volume, Variety, Velocity, Veracity (+Value)|
|**HDFS**|bloky 64 MB, replikace 3×, name node + data nodes, write-once|
|**MapReduce**|map → shuffle(group) → reduce; combiner šetří síť|
|**Spark**|in-memory DAG engine, ~100× rychlejší než Hadoop|
|**NoSQL**|key-value/column/document/graph; bez ACID, horizontální škálování|
|**LSH**|shingling → minhash → banding → kandidátní páry|
|**PageRank**|r = βM·r + (1−β)/N; teleport řeší dead-ends/spider traps|
|**Precision/Recall/F1**|přesnost / úplnost / harmonický průměr|

---

## 13. Q&A — reálné otázky komise + modelové odpovědi

### A) Datové sklady, BI, OLAP/OLTP

**Q (Nováček 2025): „Zpracování dat — základní pojmy a principy datových skladů.“** A: Začni od **BI** (analýza dat → rozhodování) a **proč DW**: oddělit analytickou zátěž od OLTP, aby dlouhé analytické dotazy „nezamrzaly“ operativní systémy (příklad Walmart). DW je dle **Inmona** _subject-oriented, integrated, time-varying, non-volatile_ sbírka pro rozhodování. Pak zmiň **OLTP vs OLAP** (krátké zápisy/ACID/3NF vs. dlouhé čtení/full scan/0NF/dimenzionální), **ETL** (extract–transform–load, ~80 % vývoje), a **analytiku na úrovni DB** (agregovaná data místo jednotlivých záznamů — roll-up hierarchický/dimenzionální, drill-down, kostka).

**Q (Nováček 2025): „Pokročilé techniky zpracování dat.“** A: BI, OLTP vs OLAP, prezentace vyššímu managementu, **multidimenzionální kostka** — popiš buňky/dimenze/měřítka. _Pozn. zkoušený:_ na otázku „jak kostku využiješ k interpretaci dat“ odpověz, že lze **agregovat** (roll-up) nebo se dívat **detailněji přes dimenze** (drill-down); praktický příklad: vykreslit prodej produktů v čase a ukázat manažerům **spike** = produkt se výborně prodával.

**Q (2024): „OLAP, OLTP — čím se liší? NoSQL — příklady, kde jsou vhodné (z praxe).“** A: Viz srovnávací tabulka (kapitola 3). NoSQL typy + use-case: **key-value/wide-column (HBase)** pro obří objemy a jednoduché přístupy; **column store (Vertica)** pro analytiku/agregace; **document** pro proměnnou strukturu; **graph** pro sociální sítě/doporučování. Vhodné tam, kde potřebuješ horizontální škálování a snese se eventual consistency místo ACID.

**Q (2024): „Datová analytika, BI, OLAP vs OLTP, ROLAP vs MOLAP, využití BI, typy NoSQL DB.“** A: BI = proces analýzy → rozhodování. **MOLAP** (data v multidim. strukturách, MDX, rychlé, méně flexibilní) vs **ROLAP** (kostka nad relační DB, redundance přes materializované pohledy/indexy) vs **HOLAP** (hybrid). NoSQL typy viz výše.

### B) Big data, MapReduce, Hadoop, distribuované zpracování

**Q (SWE-DEV 2025): „Spracovanie dát — OLTP vs OLAP, co je data warehouse, big data charakteristika, MapReduce, Hadoop.“** A: OLTP/OLAP/DW viz výše. **Big data 4V:** Volume, Variety, Velocity, Veracity (+Value). **Hadoop** = framework pro distribuované zpracování na clusteru; **HDFS** (bloky 64 MB, replikace 3×, name node + data nodes, write-once) + **MapReduce** (map → shuffle/group → reduce; uživatel píše jen map & reduce).

**Q (SWE-DEV 2024): „Distribuované systémy — SOAP vs REST, RPC, MapReduce, co se stane při výpadku HW při MapReduce, GRID vs Cloud.“** A: MapReduce: map produkuje `(k,v)` páry, framework je shuffluje dle klíče, reduce agreguje. **Při výpadku HW** (selhání úkolu) framework úkol **znovu spustí** (re-execute) na jiném uzlu díky replikaci dat a evidenci masteru; padne-li master → job se abortne. Pomalé workery (stragglers) se řeší **backup kopiemi** úkolů.

**Q (2017–2025, Dohnal/různí): „Distribuované systémy, škálovatelnost, sharding, RAID, cloud.“** A: **Horizontální** (přidávání uzlů) vs **vertikální** (silnější HW) škálování; horizontální je obecně náročnější, ale nutné pro big data. **Sharding** = horizontální rozdělení dat dle klíče (range/list/hash) na uzly. **Cloud:** IaaS/PaaS/SaaS. (RAID — mirroring pro dostupnost, striping pro výkon.) Eventual consistency, replikace master-slave řeší nedostupnost při výpadku instance.

### C) Relační databáze — model, algebra, SQL, vyhodnocení dotazu

**Q (Batko/Rebok/Nováček, opakovaně): „Relační databáze — relační model, relační algebra, SQL, vyhodnocení dotazu, transakce.“** A:

- **Relační model:** data v relacích (tabulkách); relace = množina n-tic; **klíče** — kandidátní, super-klíč, primární, cizí. Integritní omezení (entitní, referenční, doménová, + libovolné procedury).
- **Relační algebra** (proč: formální základ + **optimalizace** dotazu): selekce σ, projekce π, kartézský součin ×, join ⋈, sjednocení/průnik/rozdíl, přejmenování. Příklad notace: `σ_{cena>100}(Produkt)`.
- **SQL:** DDL (CREATE/ALTER/DROP) vs DML (SELECT/INSERT/UPDATE/DELETE). Pořadí klauzulí SELECTu při vyhodnocení: `FROM → WHERE → GROUP BY → HAVING → SELECT → ORDER BY`.
- **Vyhodnocení dotazu:** syntaktická → sémantická kontrola → **logický plán** (převod do relační algebry) → **fyzický plán** (výběr dle ceny). Plán se vybírá podle **statistik** (u klasické DB minimalizace **přístupů na disk**, u in-memory DB **času procesoru**). _Pozn.: Batko nemá rád „interní reprezentaci“ a tvrdí, že plán je „jeden“._
- **Transakce:** **ACID** (Atomicity, Consistency, Isolation, Durability) + úrovně izolace.

> [!warning] Past (2026): relační algebra Více zkoušených se v relační algebře „utopilo“ (Rebok/komise se do ní rádi zakousnou na 15 minut). Umět přesně pojmenovat a zapsat operace a vztah k SQL.

**Q (Batko 2024 / Nováček 2025): „Základy databáze — co to je, tabulky, záznamy, proč relační lepší než file system.“** A: DB vs **file system**: lepší dotazování/**vyhledávání**, integrita, konkurenční přístup, transakce, nezávislost dat, méně redundance — ale „záleží na situaci“. Schéma DB, operace přes SQL.

### D) Indexování, hashování, JOINy, ukládání dat

**Q (Ošlejšek 2017 / Batko / 2024–2026, velmi časté): „Indexování — hustý/řídký index, B+ stromy, hashování, bitmapy.“** A:

- **Hustý vs řídký index:** hustý má položku pro každý záznam; řídký jen pro některé bloky (záznamy seřazené).
- **B+ strom:** vyvážený, data v listech, vnitřní uzly jen klíče; **arita uzlu = velikost bloku na disku**; složitost vyhledávání **O(log n)**. Kreslit příklad doporučeno.
- **Hashování:** hash funkce → bucket; **open vs closed** (separate chaining vs open addressing); **statické vs dynamické** hashování pro řešení kolizí. Kryptografická hash funkce by byla overkill.
- **Bitmapový index:** bit-vektory pro distinct hodnoty, vhodný pro **málo distinct hodnot**, OR/AND = bitové operace; multi-component / range-encoded varianty.
- **Bloom filtr** u indexů (probabilistic membership).

> [!warning] Past: vlastnosti hashovací funkce Komise (2023): NE říkat „ideálně chceme bezkolizní hashovací funkci“ — předseda to považuje za nesmysl. U DB hashování stačí dobrá distribuce a determinismus.

**Q (Nováček 2025 / 2026): „Typy JOINů: Nested Loop vs Merge vs Hash.“** A:

- **Nested Loop Join** — pro každý řádek vnější tabulky projdi vnitřní; dobré pro malé tabulky / s indexem.
- **Sort-Merge Join** — obě tabulky seřaď podle join klíče, pak slévej; dobré pro velké seřazené vstupy.
- **Hash Join** — postav hash tabulku z menší relace, prober větší; dobré pro velké nesetříděné tabulky bez indexu (equi-join).
- V DW navíc: preferuj **cross-join dimenzí** + jeden průchod faktem, nebo protnutí **semi-joinů** přes indexované FK.

**Q (2022/2026): „Jak jsou data v relaci uložena / na disku, ukládání do bloků.“** A: Záznamy v **blocích/stránkách** na disku; tabulka = množina bloků; index ukazuje na bloky. Optimalizace: clusterování, partitioning (range/list/hash), komprese (column store).

**Q (Pitner 2020): „Optimalizace dotazů, table partitioning, indexing.“** A: Optimalizátor vybírá fyzický plán dle statistik; **partitioning** (horizontální/vertikální) snižuje objem skenovaných dat a umožňuje paralelizaci; **indexy** zrychlují bodové/rozsahové přístupy.

### E) Transakce, integritní omezení

**Q (2024/2026, opakovaně): „Integritní omezení, ACID, transakce, indexy, hašování, B+ stromy.“** A: **Integritní omezení** — entitní (PK), referenční (FK), doménová, + uživatelské (procedury). **Transakce** = ACID; izolační úrovně (read uncommitted → serializable) řeší dirty/non-repeatable read, phantom. Indexy/hash/B+ viz výše.

### F) Modelování dat, normalizace (přesah na analýzu/návrh)

**Q (Bühnová 2019 / 2019): „Datové modelování a normalizace, porovnání Class Diagram a E-R diagram.“** A: **Normalizace** (1NF–4NF) odstraňuje redundanci v OLTP; v **DW se naopak denormalizuje** (star schema, 0NF). **E-R vs Class diagram:** Class má navíc metody (ne funkce/procedury), jinak entity≈třídy, vztahy≈asociace.

---

## 14. Strategie u zkoušky (z reakcí komise)

> [!tip] Osvědčené taktiky
> 
> - **Začni obecně, pak jdi do hloubky** — komise nechá mluvit a „chytí se“ pojmu (mapReduce, Hadoop, kostka). Schválně zmiňuj pojmy, které umíš.
> - **Kresli** — schéma hvězdy, B+ strom, kostku, bitmapu, MapReduce flow. Zabere čas a ukáže pochopení.
> - **Měj příklad z praxe** — eshop (ukládání/změna ceny), prodej v čase (spike), Walmart (proč DW).
> - **Pozor na relační algebru** — nejčastější místo, kde komise „vykoupe“. Umět zapsat operace.
> - **Pozor na hash funkce** — neříkat „bezkolizní“ jako vlastnost; u DB hash ≠ kryptografický hash.
> - Zkoušející jsou většinou **vstřícní** (Nováček, Batko, Rebok, Dohnal, Ošlejšek) — když nevíš, navedou tě; nech se navést a stav na tom.

---