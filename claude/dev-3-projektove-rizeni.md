# DEV 3 Projektové řízení

> [!abstract] Zadání otázky **Plánování, řízení rizik, role modelů v projektovém řízení. Ganttovy diagramy, síťová analýza, metoda kritické cesty (CPM), Program Evaluation and Review Technique (PERT). Mezinárodní standardy a metodiky projektového řízení (PMI Project Management Body of Knowledge, PRINCE 2). Příklady z praxe pro vše výše uvedené.**

> [!tip] Na co se komise reálně ptá (shrnutí z protokolů)
> 
> - **CPM a PERT** – jednoznačně nejčastější. Nakreslit síťový diagram (většinou AON), spočítat kritickou cestu (dopředný + zpětný průchod), určit rezervy (float). Umět to **na tabuli**.
> - **Typy vazeb FS / FF / SS / SF** + příklady a jak ovlivňují časy. Ráček rád zadá i jiné vazby než klasické Finish-to-Start.
> - **PERT vzorec** `te = (o + 4m + p)/6` a k čemu slouží (odhad doby trvání).
> - **WBS** – co to je, co je _work package_, že **MD je jednotka úsilí, ne času**.
> - **Gantt** – co je na osách, čím se dá obohatit, převod Gantt ↔ CPM.
> - **Řízení rizik** – 3 dimenze dopadu (čas/rozpočet/scope), identifikace, hodnocení (pravděpodobnost × dopad), 4 strategie reakce, risk register.
> - **Standardy** – rozdíly PRINCE2 vs PMBOK vs IPMA; rozdíl projekt/program/portfolio, projekt vs produkt.
> - **PPP** = Project, Programme, Portfolio.

---

## 1. Základy projektového řízení

### 1.1 Co je projekt

Definice se v jednotlivých standardech mírně liší, ale jádro je stejné:

- **PMI PMBOK:** _dočasné úsilí podniknuté za účelem vytvoření unikátního produktu, služby nebo výsledku._
- **PRINCE2:** _dočasná organizace vytvořená za účelem dodání jednoho či více produktů podle dohodnutého business case._
- **IPMA ICB:** _unikátní, dočasné, multidisciplinární a organizované úsilí realizovat dohodnuté výstupy v rámci předdefinovaných požadavků a omezení._

### 1.2 Čtyři hlavní charakteristiky projektu

|Charakteristika|Význam|
|---|---|
|**Temporary (dočasný)**|Má definovaný začátek a konec, má životní cyklus.|
|**Change-driving (přináší změnu)**|Dodává _hodnotu_ (value) ku prospěchu _stakeholderů_; opodstatnění je v _Business case_.|
|**Uncertain (nejistý)**|Nese rizika (deadliny, změna, něco děláme poprvé).|
|**Unique (unikátní)**|Není rutina; unikátní kombinace požadavků, výstupů, týmu, času…|

> [!note] Klíčové definice
> 
> - **Value (hodnota):** kvantifikovatelný přínos, který projekt dodává.
> - **Business case:** informace určující, zda se projekt vyplatí dělat.
> - **Stakeholder:** kdokoli, kdo může projekt ovlivnit nebo jím být ovlivněn (vedení, uživatelé, zákazníci, dodavatelé…).
> - **Project risk:** nejistá událost/podmínka, která může ovlivnit dosažení cílů.

### 1.3 Projekt vs. Proces

|Projekt|Proces|
|---|---|
|Unikátní, proběhne jen jednou|Opakovaný, mnoho instancí|
|Naplánovaný (scheduled)|Řízený událostmi (event-driven)|
|Řídit = držet věci „on track"|Řídit = dělat věci lépe a rychleji|
|Vizualizace: **Ganttův diagram**|Vizualizace: **Flow chart (vývojový diagram)**|

Projekt obsahuje opakovatelné prvky = procesy (např. _Define scope_, _Create WBS_, _Create project plan_). Projekt je v podstatě unikátní množina procesů.

### 1.4 PPP – Portfolio · Program · Projekt

> [!important] Často se ptají na rozdíl!
> 
> - **Projekt** – dočasné úsilí pro vytvoření unikátního výstupu.
> - **Program** – dočasná, **závislá** množina projektů řízená jako celek pro dosažení společných cílů (řeší konflikty a omezení mezi projekty, alokuje scope do projektů, řídí rizika programu).
> - **Portfolio** – **trvalá** (ongoing) množina projektů a programů řízená jako skupina pro **strategické cíle** (přidává hodnotu byznysu, monitoruje výkonnost, prioritizuje a vybírá projekty/programy).

**Příklad:** Portfolio „IT projekty a služby" → program „SW development" + program „IT maintenance" → projekty „Nový ERP", „Web pro HR", „Virtualizace serverů", „Výměna sítě".
### 1.5 Co je projektové řízení

_Aplikace znalostí, dovedností, nástrojů a technik na aktivity projektu za účelem splnění požadavků projektu_ (PMBOK). Zahrnuje plánování, delegování, monitoring a kontrolu všech aspektů + motivaci lidí.

### 1.6 Trojimperativ (Triple constraint) – „PICK TWO"

Trojúhelník **Time – Cost – Scope** definuje hranice projektu. Vyvážení je odpovědnost projektového manažera. Zmenšení jedné strany zvyšuje tlak na zbývající dvě.

- **Fast + Cheap** → nebude to dobré
- **Good + Cheap** → nebude to rychlé
- **Good + Fast** → nebude to levné

> [!tip] Spurný se ptal přímo na "Price – Scope – Time". Kvalita (Quality) bývá uváděna jako střed/čtvrtý rozměr.

---

## 2. Plánování projektu

Plánování odpovídá na otázky **WHAT, WHEN, WHO a HOW MUCH** celého projektu. Postup tvorby plánu (Project Plan):

1. **Vytvořit WBS** (Work Breakdown Structure) a určit _work packages_.
2. **Spočítat odhady úsilí a nákladů** work packages (např. pomocí PERT).
3. **Definovat aktivity** – jejich závislosti, počáteční/koncová data, odpovědnosti.
4. **Vytvořit harmonogram** (Gantt nebo síťový diagram) s milníky a fázemi.

### 2.1 WBS – Work Breakdown Structure

> [!important] WBS = hierarchický rozklad veškeré práce, kterou je třeba v projektu udělat. Základ (foundation) pro harmonogram.

- Forma: diagram nebo hierarchický seznam výstupů a aktivit.
- Postup: vyjít z _Product Specification_ → určit hlavní výstupy (systémy, subsystémy, komponenty) → rozložit na work packages.
- **Pravidla WBS:**
    - Každá dekomponovaná úroveň musí tvořit **100 %** nadřazené úrovně (pravidlo 100 %).
    - Prvky na dané úrovni jsou **vzájemně výlučné** (mutually exclusive).
    - Každý work package by měl odpovídat reportovacímu období.

**Work Package** = úloha na nejnižší úrovni WBS:

- Lze ji odhadnout (čas, náklady).
- Nemá smysl ji dále dělit.
- Produkuje měřitelný výstup.
- Tvoří unikátní balíček práce.

> [!warning] MD = Man-Day = jednotka **úsilí (effort)**, NE času! Komise (Ráček 2026) to explicitně testuje. 10 MD neznamená 10 pracovních dní – záleží, kolik lidí a na jaký úvazek na úloze pracuje.

![[wbs.avif|524]]

### 2.2 Odhad úsilí a nákladů

- Z PERT odhadu (te) → přepočet na _Effort per wage level_ (analytik, designér, architekt…) → _Expected duration_ v pracovních dnech.
- **Estimated effort (MD) ≠ počet pracovních dní potřebných k dokončení** – musíme zohlednit počet členů týmu na úloze a jejich denní vytížení (full-time/half-time).
- Další techniky odhadu (z praxe / dalších přednášek): expertní odhad, analogie, odhad shora-dolů a zdola-nahoru, **COCOMO / COCOMO II**, **Function Points (FP)**, **Putnamova rovnice** (váže produktivitu, pracnost a délku projektu).

### 2.3 Definování aktivit a závislostí

- Závislosti pomocí **PDM (Precedence Diagramming Method)**.
- Počáteční a koncová data.

### 2.4 Přiřazení zdrojů a odpovědností – RACI / Resource Assignment Matrix

Matice propojuje work packages (aktivity) s členy týmu.

> [!note] RACI chart
> 
> - **R = Responsible** – kdo práci vykonává
> - **A = Accountable** – vlastník/schvalovatel, **vždy právě jedna osoba**
> - **C = Consulted** – poskytuje vstup
> - **I = Informed** – musí být informován

---

## 3. Ganttovy diagramy

> [!important] Ganttův diagram = harmonogramový model zobrazující plánované aktivity (úlohy) jako **pruhy (bary)** se začátkem a koncem.
> 
> - **Aktivity → svislá osa**
> - **Datum/čas → vodorovná osa**

Lze obohatit o:

- **Milníky (milestones)** – významné výstupy (zobrazované jako kosočtverce/body).
- **Zdroje** (kdo co dělá).
- **Postup (progress)** plnění.
- **PDM vazby** (šipky mezi pruhy).
- **Zvýraznění kritické cesty** (typicky červeně) a zobrazení **rezervy (slack/float)**.

Gantt je nejlepší nástroj pro vizualizaci **projektu** (oproti procesu = flow chart).

![[gantt.webp]]

---

## 4. Síťová analýza (Network analysis) a typy vazeb

Síťový diagram zobrazuje aktivity jako sekvenci uzlů propojených logickými vztahy. Slouží k výpočtu kritické cesty, rezerv a celkové doby trvání projektu.

### 4.1 Dvě notace: AON vs AOA

| |**AON** (Activity-on-Node)|**AOA** (Activity-on-Arrow)|
|---|---|---|
|Aktivita|v **uzlu**|na **hraně/šipce**|
|Uzly|aktivity|události/milníky|
|Použití|dnes převažuje, používá PDM|starší, vyžaduje „dummy" aktivity|
|Výhoda|snadno čitelné, podporuje všechny typy vazeb|**dobře zobrazuje milníky přímo v uzlech** (zmiňoval Ráček)|

> [!tip] Komise (Ráček 2016): „AOA má výhodu, že se v něm dobře zobrazuje milestone právě v těch Node-ech."

### 4.2 Typy vazeb (PDM – Precedence Diagramming Method)

> [!important] Komise se ptá VELMI často – znát všechny 4 + příklad!
> 
> - **FS (Finish-to-Start)** – nástupce nemůže _začít_, dokud předchůdce neskončí. _Nejčastější._ Př.: ceremoniál nemůže začít, dokud neskončí závod.
> - **FF (Finish-to-Finish)** – nástupce nemůže _skončit_, dokud předchůdce neskončí. Př.: editaci dokumentu nelze dokončit dříve, než je dokončeno jeho psaní.
> - **SS (Start-to-Start)** – nástupce nemůže _začít_, dokud předchůdce nezačal. Př.: srovnávání betonu nemůže začít dříve, než začne lití základů.
> - **SF (Start-to-Finish)** – nástupce nemůže _skončit_, dokud předchůdce nezačal. **Velmi vzácné.**

Změna typu vazby mění výpočet časů v síti (Ráček rád zadá např. přepočet kritické cesty při změně FS → SS).

---

## 5. Metoda kritické cesty (CPM – Critical Path Method)

> [!important] Definice **CO:** metoda pro určení **nejdelšího řetězce závislých aktivit** v harmonogramu. **PROČ:** zjistit, které aktivity jsou **kritické** (musí být dokončeny včas) a které lze zpozdit bez prodloužení projektu. **Komponenty:** seznam aktivit (WP), závislosti (PDM), logický start a konec.

### 5.1 Co potřebujeme znát o aktivitě

- **Dobu trvání (DUR)**
- **Závislosti** (předchůdci/nástupci)
- Logické **start a end** body

### 5.2 Algoritmus – dopředný a zpětný průchod

Každý uzel obsahuje 4 hodnoty (+ trvání):

```
┌─────────────────────┐
│  ES  │  DUR  │  EF  │   ES = Earliest Start (nejdřívější začátek)
├──────┼───────┼──────┤   EF = Earliest Finish = ES + DUR
│  LS  │  TF   │  LF  │   LS = Latest Start = LF - DUR
└─────────────────────┘   LF = Latest Finish (nejpozdější konec)
```

**1) Dopředný průchod (forward pass)** – počítá ES a EF zleva doprava:

- ES první aktivity = 0 (nebo 1 dle konvence).
- `EF = ES + DUR`
- ES nástupce = **max** z EF všech předchůdců (musíme počkat na nejpozdějšího).

**2) Zpětný průchod (backward pass)** – počítá LF a LS zprava doleva:

- LF poslední aktivity = její EF (= celková doba projektu).
- `LS = LF − DUR`
- LF předchůdce = **min** z LS všech nástupců.

**3) Total Float (celková rezerva):** `TF = LS − ES = LF − EF`

- **Aktivity na kritické cestě mají TF = 0** → nesmí se zpozdit, jinak se prodlouží celý projekt.
- Kritická cesta = nejdelší cesta sítí = nejkratší možná doba realizace projektu.

> [!note] Doplňkové pojmy
> 
> - **Free float** – o kolik lze aktivitu zpozdit, aniž ovlivní ES nástupce.
> - **DRAG** (na obrázku) – „delay amount", o kolik daná kritická aktivita prodlužuje projekt (kolik by se ušetřilo jejím zkrácením).

### 5.3 Otázky komise na CPM

- Co se stane, když se zpozdí úloha **mimo** kritickou cestu? → Nic, dokud zpoždění ≤ její rezerva (float).
- O kolik se může taková úloha zpozdit? → o velikost jejího **total float**.
- Přepočítat kritickou cestu při změně typu vazby (FS → SS).

![[cpm_aon.png|697]]


---

## 6. PERT (Program Evaluation and Review Technique)

> [!important] PERT = **pravděpodobnostní technika pro odhad doby** potřebné k dokončení úlohy (aktivity, work package).

Pro úlohu se zadají **tři odhady času**:

- **o** = optimistický (minimální možný)
- **m** = nejpravděpodobnější (best estimate / most likely)
- **p** = pesimistický (maximální možný)

**Očekávaná doba (expected time):** $$ t_e = \frac{o + 4m + p}{6} $$

(vážený průměr s váhou 4 na nejpravděpodobnější odhad → tzv. beta rozdělení)

**Příklad z přednášky:**

|Work Package|o|m|p|te|
|---|---|---|---|---|
|Request Analysis|3|4|8|(3+16+8)/6 = **4,50**|
|Layout design|8|10|20|(8+40+20)/6 = **11,33**|

> [!note] Doplněk (obecná znalost)
> 
> - **Směrodatná odchylka** aktivity: `σ = (p − o)/6`, rozptyl `σ² = ((p−o)/6)²`.
> - PERT se používá pro **ohodnocení uzlů (dob trvání) v síťovém diagramu**, ze kterých se pak počítá CPM. → **Vztah PERT ↔ CPM:** PERT dodá odhady dob trvání, CPM z nich spočítá kritickou cestu. Na tuto souvislost se komise ptá (Ráček 2025).
> - Rozdíl od CPM: CPM pracuje s **deterministickými** dobami, PERT s **pravděpodobnostními** (tři odhady).

---

## 7. Řízení rizik (Risk management)

> [!important] **Project risk** = nejistá událost nebo podmínka, která může ovlivnit dosažení cílů projektu. Dopad rizika se projeví ve **3 dimenzích trojimperativu** (Ráček 2025 to chce slyšet):
> 
> - **Čas** – riziko může projekt zpozdit
> - **Rozpočet** – může projekt prodražit
> - **Scope/funkcionalita** – může změnit rozsah

IT projekty jsou rizikovější (unikátnost, časté změny požadavků, nestabilní zdroje). Řízení rizik bývá podceňováno.

### 7.1 Proces řízení rizik (4–5 kroků)

**1. Identifikace rizik**

- Hledat _lessons learned_, použít techniku **what/why** („Co se může stát?" „Proč by se to stalo?").
- Časté zdroje: nové technologie/procesy/dodavatelé, **závislosti**.
- ⚠️ **Častá chyba:** překročení rozpočtu/času/kvality NEJSOU rizika, ale **důsledky** rizik!

**2. Hodnocení rizik (Assess)**

- Ohodnotit **pravděpodobnost (probability)** a **dopad (impact)**, typicky stupnice Low/Medium/High (1–3).
- **Skóre = pravděpodobnost × dopad** (matice rizik).
- Definovat důsledky (consequences).

**3. Reakce na rizika (Risk responses) – 4 strategie**

|Strategie|Popis|
|---|---|
|**Accept (přijmout)**|Neudělat teď nic, řešit, až nastane (rizika s nízkým skóre).|
|**Avoid (vyhnout se)**|Změnit plány tak, aby situace vůbec nenastala.|
|**Transfer (přenést)**|Přenést dopad na třetí stranu (pojištění, smlouva s dodavatelem).|
|**Reduce (zmírnit)**|Snížit pravděpodobnost nebo dopad rizika.|

> [!tip] Praktický tip z protokolu: nízká pravděpodobnost + velká finanční ztráta → **pojištění** (transfer). Připravit i **kontingenční / fallback plán**, pokud zvolená reakce nezabere.

**4. Monitoring rizik**

- Kdo je odpovědný, kde jsou rizika definována (risk register), kdy se revidují (např. každý Sprint review).

**5. Risk Register** – záznam identifikovaných rizik, jejich stavu a historie. Obsahuje: identifikátor, popis, pravděpodobnost, dopad, skóre, reakci, opatření (measure), odpovědnou osobu, datum aktualizace, **reziduální riziko**.

> [!note] Role v řízení rizik (PRINCE2)
> 
> - **Risk owner** – odpovědný za dané riziko.
> - **Risk actionee** – přidělen k provedení reakce na riziko.

### 7.2 Dimenze SW rizik (zdroje rizik)

User · Requirements · Project complexity · Planning and control · Team · Organizational environment.

Nejčastější SW rizika: nedostatek personálu, nereálné odhady času/nákladů, nepochopení požadavků, neangažovanost uživatele, slabé PM, problémy s externími komponentami, pozdní změny požadavků.

![[risk_matrix.webp|423]]

---
## 8. Role modelů v projektovém řízení

V projektovém řízení používáme různé typy **modelů**, které slouží k plánování, komunikaci, analýze a kontrole. „Role modelů" lze chápat ve více rovinách:

### 8.1 Modely životního cyklu (lifecycle models)

- **Waterfall (vodopád):** vznikl ve stavebnictví; jednoduchý, lineární, **nepružný**, těžko se mění požadavky → **nevhodný pro SW**.
- **Spiral (spirálový):** **inkrementální** (postupně přidáváme) + **iterativní** (návrh v opakovaných cyklech) → vhodnější pro SW, většina SW frameworků je jeho variací.
- **Iterativní vs. inkrementální:** iterace = opakované cykly zlepšování; inkrement = přírůstek mezi dvěma iteracemi. (Komise tento rozdíl testuje.)

### 8.2 Modely pro plánování a harmonogram

- **WBS** (model rozkladu práce), **síťový diagram** (model závislostí), **Ganttův diagram** (model harmonogramu), **CPM/PERT** (modely času).

### 8.3 Modelovací (UML) diagramy – role v UP

Modely propojují požadavky s implementací:

|Workflow|Diagramy|
|---|---|
|Business modelling|Activity diagram|
|Requirements|**Use Case diagram** (primární kontrakt mezi vývojářem a klientem)|
|Analysis & Design|Class, Sequence, Collaboration diagram|
|Implementation|Class, Object, Component diagram|
|Test|Use Case, Class, Activity diagram|
|Deployment|Deployment diagram|

### 8.4 Modely odhadu nákladů/úsilí

COCOMO, COCOMO II, Function Points, Putnamova rovnice, PERT.

> [!tip] Pokud se zeptají obecně „role modelů", odpověz: modely **snižují nejistotu**, umožňují **komunikaci se stakeholdery**, jsou **základem pro plánování a kontrolu** (WBS→harmonogram), a v predikativním přístupu slouží i jako **dokumentace a kontrakt** (UML, Product Specification).

---

## 9. Mezinárodní standardy projektového řízení

Proč standardy? Lepší výsledky, efektivní koordinace, transparentnost, důvěra stakeholderů, „nevynalézat kolo".

Tři nejpoužívanější generické standardy: **PRINCE2**, **PMI PMBOK**, **IPMA ICB**.

> [!note] Analogie „rajská polévka" (tomato soup)
> 
> - **PMBOK** = kuchařská příručka (jak uvařit vodu, nakrájet cibuli, vypěstovat bazalku).
> - **PRINCE2** = recept na rajskou polévku (krok za krokem).
> - **IPMA** = průvodce kompetencemi kuchaře. Vzájemně se doplňují.

### 9.1 PRINCE2 (PRojects IN Controlled Environments)

- Vydavatel **Axelos**. **Preskriptivní** přístup – návod krok za krokem.
- Publikace „Managing Successful Projects with PRINCE2", ~400 stran, verze 2017.
- **Struktura 7-7-7:**

**7 principů** (vodítka; jejich nedodržení zvyšuje šanci na neúspěch):

1. Continued business justification (trvalé opodstatnění)
2. Learn from experience (uč se ze zkušeností)
3. Defined roles and responsibilities (definované role)
4. Manage by stages (řízení po etapách)
5. **Manage by exception** (řízení výjimkou – překročení tolerance se eskaluje výš)
6. Focus on products (zaměření na produkty)
7. Tailor to suit the project (přizpůsobení projektu)

**7 témat** (disciplíny řešené průběžně): Business case (WHY), Organization (WHO), Quality (WHAT), Plans (HOW/WHEN/HOW MUCH), Risk (WHAT IF), Change (impact), Progress (kde jsme).

**7 procesů** (postup životním cyklem): Starting up a Project (SU) → Directing a Project → Initiating a Project (IP) → Controlling a Stage → Managing Product Delivery → Managing a Stage Boundary (SB) → Closing a Project (CP).

**3 úrovně řízení / odpovědnost za procesy:**

- **Directing** – Project Board (Executive, Senior User, Senior Supplier)
- **Managing** – Project Manager
- **Delivering** – Team Manager

> [!warning] PRINCE2 **neřeší** management požadavků a rozpočtu → vhodné doplnit jiným standardem. Doslovné dodržování vytváří velkou dokumentační zátěž. **Vhodné když:** firma vyžaduje rozsáhlý reporting/dokumentaci, „command and control" řízení, velký diverzifikovaný tým, nezkušený PM.

### 9.2 PMI PMBOK (Project Management Body of Knowledge)

- Vydavatel **Project Management Institute (PMI)**. **Procesně orientovaný** – rozsáhlý průvodce dobrou praxí (nikoli návod krok za krokem).
- Publikace „A Guide to the PMBOK", ~530 stran, 6. edice (2017).
- **Struktura:** **49 procesů** → seskupené do **5 procesních skupin** a **10 znalostních oblastí**.
- Každý proces má definované **vstupy, výstupy, nástroje a techniky**.

**5 procesních skupin:** Initiating · Planning · Executing · Monitoring & Controlling · Closing.

**10 znalostních oblastí:** Integration · Scope · Schedule · Cost · Quality · Resource · Communication · **Risk** · Procurement · Stakeholder.

**Talent Triangle:** Technical PM + Leadership + Strategic & Business management.

> [!tip] **Vhodné když:** PM potřebuje detaily, jaké **nástroje a techniky** použít pro klíčové oblasti; je důraz na procesy. Použitelné kdykoli jako příručka.

### 9.3 IPMA ICB (Individual Competence Baseline)

- Vydavatel **International Project Management Association (IPMA)**. **Kompetenčně orientovaný** přístup.
- Verze ICB4 (2015), ~400 stran, rozděleno na Project/Programme/Portfolio.
- **Kompetence** = aplikace **znalostí (knowledge) + dovedností (skills) + schopností (abilities)**.
    - _Knowledge:_ rozumět konceptu Ganttova diagramu.
    - _Skills:_ umět Ganttův diagram sestavit.
    - _Abilities:_ úspěšně řídit harmonogram projektu.
- **Struktura tří oblastí kompetencí:**
    - **5 Perspective** (strategie, governance, compliance, power & interest, kultura)
    - **10 People** (soft skills – komunikace, leadership, teamwork, vyjednávání, řešení konfliktů…)
    - **13 Practice** (scope, time, quality, finance, resources, risk & opportunity, stakeholders…)

> [!tip] **Vhodné když:** klíčové jsou **soft skills**, zkušený PM. Jediný ze standardů s důkladnou sekcí o soft skills.

### 9.4 Srovnání standardů (cheat sheet)

| |PRINCE2|PMBOK|IPMA ICB|
|---|---|---|---|
|Vydavatel|Axelos|PMI|IPMA|
|Přístup|preskriptivní (metoda)|procesní (příručka)|kompetenční|
|Struktura|7 principů/témat/procesů|5 skupin, 10 oblastí, 49 procesů|5+10+13 kompetencí|
|Hlavní síla|návod krok za krokem|nástroje a techniky|soft skills|
|Certifikace|Foundation / Practitioner|PMP|level A–D|

> [!note] IPMA je 4. nejmenovaný v zadání, ale komise (Ráček) ho zmiňuje při srovnání. Pozor: **IPMA** ≠ PMI ≠ PRINCE2.

![[pm_standards.png]]

---

## 10. Přístupy k vývoji SW – příklady z praxe

> [!important] **Predictive vs. Agile** – komise (Ošlejšek) se ptá, co je u agilního _fixní_ a co _proměnlivé_: **fixní = deadline a peníze (čas+náklady), proměnlivá = funkcionalita (scope).** U predikativního je naopak fixní scope.

| |**Predictive (prediktivní)**|**Agile (agilní)**|
|---|---|---|
|Charakter|rigidnější, zaměřen na **procesy**|flexibilní, zaměřen na **lidi**|
|Požadavky|fixní, předem dané|pravidelně aktualizované|
|Plánování|důkladné předem (upfront)|minimální předem|
|Příklad|**Unified Process**|**SCRUM**|
|Kontrakt|Fixed time, fixed price|Time and means|

### 10.1 Unified Process (UP) – prediktivní framework

- **Iterativní a inkrementální**, **risk-driven**, **architecture-centric**, **use-case (requirements) driven**.
- Iterace = mini-projekt (max ~3 měsíce); rozdíl mezi dvěma iteracemi = **increment**.
- 6 workflows v iteraci: Business Modelling, Requirements, Analysis & Design, Implementation, Test, Deployment.
- **4 fáze (komise chce přesné názvy!):**
    1. **Inception** – feasibility, business case, esenciální požadavky, kritická rizika → _milník: Objectives_
    2. **Elaboration** – architektonický baseline, sběr požadavků, UML modely → _milník: Architecture_
    3. **Construction** – dokončení návrhu, build & test → _milník: Operational Capability_
    4. **Transition** – oprava defektů, uživatelské manuály, **školení uživatelů**, předání → _milník: Product Release_

> [!tip] Spurný (2025): v **Transition** odevzdáváme i jinou než projektovou dokumentaci (administrátorskou) a důležité je **školení zaměstnanců**. Pozor (Ošlejšek 2026): Inception/Elaboration/Construction/Transition je **celý projekt**, jednotlivá iterace probíhá uvnitř fáze.

### 10.2 SCRUM – agilní framework

- Nejběžnější agilní framework. „The Scrum Guide" (~19 stran), striktní pravidla.
- **3 role:** Product Owner (reprezentuje stakeholdery, spravuje Product Backlog, hlavní odpovědnost: komunikace) · Scrum Master (řídí proces, odstraňuje překážky) · Team of Developers (3–9, cross-funkční, samoorganizovaný, dodává produkt).
- **3 artefakty:** Product Backlog (scope, User Stories, Story Points) · Sprint Backlog (podmnožina pro sprint, úkoly v hodinách) · Product Increment (musí být v použitelném stavu, splnit definici „Done").
- **5 událostí:** Sprint Planning (8h) · Sprint (iterace ≤ 1 měsíc, stejná délka) · Daily Scrum (15 min) · Sprint Review (4h, kontrola inkrementu) · Sprint Retrospective (3h, zlepšení procesu).
- **Planning Poker** – odhad pracnosti User Story ve Story Points.

### 10.3 ITIL (rámec pro IT služby – kontext kolem projektu)

Best practices pro správu IT služeb, řeší „co je před a po IT projektu". 5 fází životního cyklu služby: Service Strategy · Service Design · Service Transition · Service Operation · Continual Service Improvement.

---

## 11. Příklady z praxe (z přednášek PA179)

Kurz staví na **dvou kontrastních projektech** se stejným PM:

- **Projekt 1 – „IS pro hendikepované studenty":** malý co-located tým, zákazník = Centrum pro hendikepované, časté zapojení, jen nezbytná dokumentace → **Agile: SCRUM + IPMA ICB**.
- **Projekt 2 – „SW pro městské informační kiosky":** velký diverzifikovaný tým, zákazník = úřad s rigidní politikou, striktní deadliny a rozpočet, detailní specifikace předem → **Predictive: PRINCE2 + UP**.

> [!note] Hlavní lessons learned
> 
> - **Neexistuje jediný správný přístup.** „Karty v ruce" (firma, tým, typ projektu) určují strategii (životní cyklus, plánování, zapojení zákazníka).
> - Špatná volba přístupu je častou příčinou selhání projektu.
> - **Faktory úspěchu:** silný business case, podpora vedení, kompetentní PM, schopný tým, efektivní řízení změn/rizik/kvality, silný a aktuální plán, realistický harmonogram, dobře alokované zdroje, dobrá komunikace, správná volba nástrojů.

Kombinování standardů: PM standardy (PRINCE2/PMBOK/IPMA) řeší netechnické aspekty (stakeholdeři, rizika), SW standardy (UP/SCRUM) řeší požadavky, vývoj, testování. Reálný SW projekt obvykle **kombinuje více standardů**.

---

## 12. Q&A – otázky komise a odpovědi

> [!question] Ráček: Co je to PPP? (zkratka + definice) **PPP = Project, Programme, Portfolio.** Projekt = dočasné úsilí pro unikátní výstup; Program = závislá množina projektů pro společné cíle; Portfolio = trvalá množina projektů/programů pro strategické cíle. _(Často následuje project planning a jeho části.)_

> [!question] Ráček: Srovnání standardů IPMA, PMI, PRINCE2. Rozdíly program / portfolio / proces / projekt. Viz [[#9.4 Srovnání standardů cheat sheet]] a [[#1.4 PPP – Portfolio · Program · Projekt]]. PRINCE2 = preskriptivní metoda (7-7-7), PMBOK = procesní příručka (5/10/49), IPMA = kompetenční (soft skills).

> [!question] Ráček/Bühnová: Project management – WBS, Gantt, síťové diagramy, PERT, CPM, rizika. Projeď celý workflow plánování: WBS → odhady (PERT) → závislosti (PDM) → harmonogram (Gantt/síť) → kritická cesta (CPM). Rizika: 3 dimenze (čas/rozpočet/scope), úbytek zdrojů (lidé, fyzické zdroje). V Ganttu nemusí všechny aktivity navazovat těsně právě kvůli řízení rizik (rezerva na nejasný konec předchozí aktivity).

> [!question] Ráček: Nakresli síťový diagram a spočítej kritickou cestu (i s jinými vazbami než F→S). Nakresli **AON** diagram, doplň DUR, proveď **dopředný průchod** (ES/EF = max z předchůdců), **zpětný průchod** (LF/LS = min z nástupců), spočítej **TF = LS−ES**. Kritická cesta = uzly s TF = 0. Při změně vazby (FS→SS) přepočítej časy. Viz [[#5. Metoda kritické cesty CPM – Critical Path Method]].

> [!question] Ráček: Typy vazeb v síťovém diagramu + příklady, jak ovlivňují časy. **FS, FF, SS, SF** – viz [[#4.2 Typy vazeb PDM – Precedence Diagramming Method]]. FS nejčastější, SF vzácná. Změna vazby mění ES/EF/LS/LF nástupce.

> [!question] Ráček/Bühnová/Spurný: Co je PERT, na co je, vzorec. Pravděpodobnostní technika odhadu doby trvání úlohy. `te = (o + 4m + p)/6`. Dodává odhady dob trvání pro uzly síťového diagramu → vstup pro CPM. Vztah PERT↔CPM: PERT odhadne časy, CPM z nich spočítá kritickou cestu.

> [!question] Spurný/Ráček: CPM a PERT detailně – nejen JAK, ale i PROČ. **CPM proč:** zjistit kritické aktivity (musí být včas) a rezervy u nekritických; určit nejkratší možnou dobu projektu. **PERT proč:** zvládnout nejistotu odhadu (tři scénáře místo jednoho čísla). Spolu: realistický harmonogram + identifikace, kde se nesmí zpozdit.

> [!question] Šimková: Gantt, milníky (nakreslit na tabuli), CPM, PERT, PPP. Gantt = pruhy aktivit (osa Y = aktivity, osa X = čas), milníky jako kosočtverce, kritická cesta červeně. Viz sekce 3, 5, 6 a [[#1.4 PPP – Portfolio · Program · Projekt]].

> [!question] Batko: Procesy a životní cyklus projektu (úvodní fáze, plánování, závěrečná fáze). Fáze: **Pre-project → Initiation → Execution → Close**. Úvodní fáze: smlouva, business case, project brief. Plánování: WBS, odhady, PERT, CPM. Závěrečná fáze: dokumentace, reklamace, hlášení chyb, předání, lessons learned (PRINCE2: Closing a Project, End project report).

> [!question] Ráček: Typy modelů životního cyklu; SCRUM a RUP (přesné názvy etap); iterativní vs. inkrementální. Waterfall (lineární, nevhodný pro SW) vs Spiral (iterativní+inkrementální). UP/RUP fáze: **Inception, Elaboration, Construction, Transition**. SCRUM = agilní, sprinty. _Iterativní_ = opakované cykly zlepšování; _inkrementální_ = přidávání přírůstků. Viz sekce 8 a 10.

> [!question] Ráček: PPP, příklady agilních metodik, jaký standard pro SCRUM, rozdíl PMBOK vs PRINCE2, projekt vs produkt. Agilní: **SCRUM, XP**. Pro řízení projektu se SCRUMem lze zvolit **PMBOK**. PMBOK = procesní příručka (znalostní oblasti, nástroje), PRINCE2 = preskriptivní metoda krok za krokem. **Projekt** (dočasný, unikátní výstup) vs **produkt** (dlouhodobý výsledek/služba, kterou projekt vytváří a který se dál spravuje). Program vs portfolio viz sekce 1.4.

> [!question] Ráček: Co je nejdřív při plánování? Jak „nacenit" části? Co je Gantt a co na jeho osách? Převod Gantt → CPM. Nejdřív **WBS**. Nacenění přes **PERT** (`te = (o+4m+p)/6`). Gantt: osa Y = aktivity, osa X = čas. Převod na CPM: z pruhů a jejich vazeb sestavit síťový diagram a spočítat kritickou cestu. Pozor na zápis šipek u jednotlivých vazeb a že **MD je jednotka úsilí, ne času**.

> [!question] Ráček (2025): Řízení rizik – co jsou rizika, jak je řešit, PERT, kritická cesta, vztah PERT–CPM. Riziko = nejistá událost ovlivňující cíle, projeví se ve **3 složkách: čas, rozpočet, scope**. Reakce: **Accept / Avoid / Transfer / Reduce** (+ příklady). PERT odhadne doby, CPM určí kritickou cestu z těchto dob. Viz sekce 6 a 7.

> [!question] Spurný (2024/2026): Síťová analýza, PERT, CPM (nakreslit a spočítat), vazby F-S/F-F/S-S/S-F, Gantt. Kompletně sekce 4–6. Umět nakreslit AON, spočítat CP na tabuli, vyjmenovat a vysvětlit všechny 4 vazby s příklady.

> [!question] Spurný (2025): Price–Scope–Time, Unified Process, dokumentace v Transition. Trojimperativ [[#1.6 Trojimperativ Triple constraint – „PICK TWO"]]. V **Transition** se kromě projektové odevzdává i **administrátorská dokumentace** a probíhá **školení zaměstnanců**.

> [!question] Ošlejšek (2026): Agilní vývoj a čím se liší; SCRUM; iterativní vs iterace; UP fáze; co je u agilního fixní/proměnlivé. Agile = flexibilní, zaměřený na lidi, časté změny (vs predictive = procesy, fixní požadavky). SCRUM = role/události/artefakty. UP řádky/sloupce = workflows × fáze; **Inception/Elaboration/Construction/Transition = celý projekt** (ne jedna iterace). U agilního je **fixní deadline + peníze, proměnlivá funkcionalita.**

> [!question] Ráček (2023): Odhadování úsilí na projektu – metody, COCOMO, COCOMO2, FP, WBS, PERT. Metody odhadu: expertní, analogie, shora-dolů / zdola-nahoru, **COCOMO (módy, modely, vzorce), COCOMO II, Function Points, PERT, WBS**. Volba dle fáze a dostupných dat. _(Detaily COCOMO přesahují PA179 přednášky – patří spíš k SW inženýrství.)_

---

## 13. Rychlé opakování před zkouškou (flashcards)

- **4 charakteristiky projektu?** Temporary, Change-driving, Uncertain, Unique.
- **Trojimperativ?** Time – Cost – Scope (PICK TWO).
- **PPP?** Project, Programme, Portfolio.
- **WBS work package?** Nejnižší úloha, lze odhadnout, měřitelný výstup, nedělit dál.
- **PERT vzorec?** `te = (o + 4m + p)/6`.
- **CPM kritická cesta?** Nejdelší cesta sítí; aktivity s **Total Float = 0**.
- **TF?** `LS − ES = LF − EF`.
- **Forward pass?** ES = max(EF předchůdců), EF = ES + DUR.
- **Backward pass?** LF = min(LS nástupců), LS = LF − DUR.
- **4 typy vazeb?** FS, FF, SS, SF.
- **4 reakce na riziko?** Accept, Avoid, Transfer, Reduce.
- **PRINCE2?** 7 principů / 7 témat / 7 procesů; preskriptivní; Axelos.
- **PMBOK?** 5 skupin / 10 oblastí / 49 procesů; procesní; PMI.
- **IPMA ICB?** 5 perspective / 10 people / 13 practice; kompetenční.
- **UP fáze?** Inception, Elaboration, Construction, Transition.
- **SCRUM 3-3-5?** 3 role, 3 artefakty, 5 událostí.
- **MD?** Man-Day = jednotka **úsilí**, ne času!