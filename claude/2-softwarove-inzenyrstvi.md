# 2 Softwarové inženýrství

> [!abstract] Životní cyklus SW, proces vývoje a řízení softwarového vývoje. Metodika (Rational) Unified Process (UP, RUP), agilní metodiky a principy agilního vývoje SW. Nasazení a provoz softwarových systémů. Údržba softwarových systémů, znovupoužitelnost. Příklady z praxe pro vše výše uvedené.

> [!tip] Jak na to u zkoušky Otázka má 5 logických bloků. Drž se osnovy: **(1) ŽC + proces + řízení → (2) UP/RUP → (3) agile → (4) nasazení/provoz → (5) údržba + znovupoužitelnost**, do každého bloku přihoď konkrétní příklad. Klíčová červená nit celé otázky: _čím později se chyba odhalí, tím dráž stojí oprava_ → proto iterace, validace a inspekce.

---

## 0. Základy: co je software a SW inženýrství

**Software** = souhrn počítačových programů, procedur, pravidel, dokumentace, školicích materiálů a dat (konfigurace, vzorky) relevantních pro provoz počítačového systému. **Není to jen kód.** SW produkt je komodita předávaná zákazníkovi.

**Vlastnosti SW:**

- Navrhuje a vyvíjí se inženýrskými technikami, _nevyrábí_ se v tradičním smyslu.
- Fyzicky se neopotřebovává (na rozdíl od HW).
- Většinou je „šitý na míru", menšina je „out-of-box".

**Dva typy produktů:**

- **Generické (krabicové)** – tvoří výrobce a prodává na volném trhu; specifikaci určuje interně (obchodní oddělení).
- **Smluvní (zakázkové)** – objedná konkrétní zákazník, vyvíjí dodavatel vázaný smlouvou; specifikace je součástí smlouvy, změny se schvalují a promítají do ceny.

**Softwarové inženýrství** = samostatná inženýrská disciplína, která aplikuje **systematický přístup k vývoji, provozu, údržbě a náhradě** software. Uznáno jako certifikovaný obor 1997 (USA). Chybí jednotná teorie, terminologie se liší — používá se kolekce technik, které fungují.

> [!note] Znaky dobře dodaného SW **Maintainability** (udržovatelnost) · **Reliability** (spolehlivost, bezpečnost, ochrana dat) · **Efficiency** (neplýtvá HW zdroji) · **Usability** (přívětivé GUI + dokumentace).

**Kritické faktory produktivity SW:** složitost · velikost (_programming-in-the-large_ vs. _-in-the-small_) · komunikace · čas a plán práce · **„neviditelnost" software**.

|Programming-in-the-small|Programming-in-the-large|
|---|---|
|Malé (one-man) projekty|Plánování, dělba práce, zdroje|
|Top-down, strukturované kódování, postupné zjemňování|Dokumentovaná specifikace|
|Inspekce logiky a kódu|Strukturované týmy|
|Nástroje: kompilátory, debuggery|Formalizované testy a inspekce|

---

## 1. Životní cyklus SW

### 1.1 Proces vývoje SW

Vývoj je proces transformace:

> uživatelské potřeby → **požadavky** → **návrh (design)** → **kód** → otestováno, zdokumentováno a certifikováno k provozu.

**Základní aktivity vývoje (Sommerville):**

1. **Specifikace** – definice funkcionality a provozních omezení.
2. **Vývoj (development)** – tvorba SW splňujícího specifikaci.
3. **Validace** – ověření (inspekce), že SW dělá přesně to, co se požaduje.
4. **Evoluce** – další vývoj podle měnících se požadavků zákazníka.

**Viditelné výstupy vývoje:** _artefakty_ (výpisy programů, dokumentace, data, zdrojový kód) a _procesy_ (pracovní postupy, rules-of-thumb, interakce členů týmu).

> [!note] Vlastnosti dobrého vývojového procesu **Comprehensiveness** (srozumitelnost def.) · **Visibility** (identifikovatelné výstupy) · **Reliability** (eliminuje/odhalí chyby dřív) · **Acceptability** (přijatelný pro inženýry) · **Robustness** (přežije neočekávané) · **Maintainability** (umí se vyvíjet) · **Speed** · **Supportability** (podpora CASE nástroji).

### 1.2 Modely životního cyklu

> [!important] Pro každý projekt se musí zvolit vhodný životní cyklus.

#### Vodopád (Waterfall)

Sekvenční fáze; integrace a testování celku najednou.

- **Výhody:** lightweight, nejnižší administrativní režie, vhodný pro malé/jednoduché projekty s nízkým rizikem, elegantní návrh (vzniká najednou), _manažeři ho mají rádi_.
- **Problémy:** reálné projekty nedodržují pevné pořadí kroků; uživatel zpočátku neumí přesně popsat požadavky; zákazník musí být trpělivý (žádné iterace); **pozdní odhalení problémů ohrozí celý projekt**.
- **Viditelnost = dokumenty:** každá aktivita produkuje dokument (Feasibility Study, Catalogue of Requirements, Functional Specification, Architecture Specification, Units Specification, Source Code, testovací protokoly, Acceptance Testing → finální systém + dokumentace).

#### Inkrementální (Incremental)

Vývoj podle obecných („general") požadavků, zákazník dostává **přírůstky** během projektu.

- Problémy: general requirements vs. realita, větší režie a vyšší cena, dokumentace může zastarat, údržba zvyšuje entropii.

#### Implementace podle obecných požadavků

Potřeba velmi talentovaných jednotlivců, průměrný tým nestačí; úspěšné produkty dělaly malé špičkové týmy. Systémy bývají špatně strukturované (opakované změny ničí strukturu → evoluce je drahá). Proces je neviditelný.

#### Prototypování (Prototyping)

Vhodné pro menší projekty s nejasnými požadavky. „Lidé rádi kritizují" → prototyp slouží ke sběru znalostí. Doporučeny 1–2 iterace prototypu; prototypy se po specifikaci většinou zahodí (důraz na nízkou cenu). Specifikace vzniká ze zpětné vazby na prototyp.

#### Výzkumnický (Researcher)

Trial-and-error, neexistující/neplatná dokumentace, členy nelze nahradit, experimentování s nepredikovatelným výstupem. → **Nevhodný pro kritickou infrastrukturu.**

#### Spirálový (Spiral, Boehm 1988)

Iterativní model řízený rizikem (každá smyčka = analýza rizik, vývoj, plánování další iterace).

> [!tip] Volba životního cyklu (shrnutí)
> 
> - **Iterativní** – velký projekt s relativně jasnými požadavky
> - **Prototyp** – menší projekt s nejasnými požadavky
> - **Vodopád** – malý projekt s velmi jasnými požadavky
> - **Researcher** – specifický projekt s byznys cílem, ale velmi obecnými požadavky

---

## 2. Řízení softwarového vývoje

### 2.1 IT projekt a project management

IT projekt = jako každý projekt: **dočasný · řízený změnou · nejistý · unikátní**. Rozdíl: výstupy se tvoří a provozují pomocí IT.

- **Závislost projektů v portfoliu** – selhání jednoho má kaskádový efekt; sleduj kritický projekt.
- **Význam risk managementu** – unikátnost, časté změny požadavků a nestabilní zdroje = vyšší riziko; řízení rizik bývá podceňováno.
- **Trojimperativ (triple constraint):** rozsah × čas × náklady (kvalita).

**Typy IT projektů:** SW development (nejčastější) · IT procurement · IT network & infrastructure · system integration.

### 2.2 Dva hlavní přístupy

|Predikativní (plánovaný)|Agilní|
|---|---|
|Rigidnější, **focus na procesy**|Flexibilní, **focus na lidi**|
|Fixní rozsah / požadavky|Průběžně aktualizované požadavky|
|Důkladné plánování předem|Minimální plánování předem|
|Příklad: **Unified Process**|Příklad: **SCRUM**|

### 2.3 Komunikace v týmu (model SW fyziky)

- `PP = LOC / E` (produktivita programátora; řádky/měsíc)
- N programátorů → `N(N−1)/2 ≈ N²` interakcí; `λN²` = režie na komunikaci.
- `GPP = LOC / (E + λN²)` (skupinová produktivita) → `GPP/PP = E / (E + λN²)`.

> [!warning] Brooksův zákon Přidání člena do zpožděného projektu může zpoždění **ještě zvětšit** — náklady na zaučení nového člena bývají vyšší než jeho přínos.

### 2.4 Softwarová fyzika (Putnamova rovnice)

- S rostoucí délkou programu **klesá** produktivita programátorů.
- `N = c · T^(1/3) · D^(4/3)` (N = délka v SLOC, T = pracnost v člověkoměsících, D = doba realizace).
- **Důsledek:** programy psané ve spěchu jsou delší. Při zkrácení termínu na 83 % je **pracnost dvojnásobná**.
- Existuje **„nedosažitelná oblast"** (nelze zkrátit dobu pod určitou mez) a **oblast stability**.
- Rozložení řešitelské kapacity v čase ≈ **Rayleighova vlna**; ve vrcholu počtu pracovníků je hotovo ~40 % práce.

### 2.5 Odhadování ceny: COCOMO

**COCOMO** (Constructive Cost Model, B. Boehm 1981) — cena vývoje přímo závisí na velikosti (#SLOC). Přesnost odhadu velikosti roste s etapou (v raných fázích až 4:1 oběma směry).

- **3 úrovně modelu:** Základní (hrubý z KSLOC) · Střední (vliv dalších faktorů) · Pokročilý (vliv vývoj. etap).
- **3 vývojové módy:**
    - **Organický** – malé projekty (<50 KSLOC), plné porozumění požadavkům (vědecké/jednoduché obchodní aplikace).
    - **Bezprostřední (semidetached)** – střední (<300 KSLOC) (transakční zpracování, OS/překladač).
    - **Vázaný (embedded)** – velké, těsná omezení, RT systémy, povelové a řídicí systémy.
- **Vzorce:** `E = a·(KSLOC)^b`, `T = c·E^d` (a,b,c,d dle úrovně a módu; např. základní/semidetached: a=3.0, b=1.12, c=2.5, d=0.3).
- **Korekční faktor Fc** = součin **15 cost drivers** (atributy produktu RELY/DATA/CPLX, HW atributy TIME/STOR/VIRT/TURN, atributy týmu ACAP/PCAP/AEXP/VEXP/LEXP, atributy projektu MODP/TOOL/SCED) na škále _velmi nízký … extrémně velký_. `E = Fc · En`.
- **Modifikace existujícího SW:** `ESLOC = ASLOC·(0,4·DM + 0,3·CM + 0,3·IM)` (DM = % modifikace návrhu, CM = % modifikace kódu, IM = integrační úsilí).

### 2.6 COCOMO II (1995)

Důvody změny: nové procesy, nové měření velikosti, **znovupoužití SW**, rozhodování při neúplné informaci.

- **3 modely podle etapy:** ACM (Application Composition, moderní nástroje/GUI) · EDM (Early Design, hrubé odhady) · PAM (Post-Architecture, po specifikaci architektury).
- Velikost: KSLOC · UFP (neupravené funkční body) · EKSLOC.
- **Měřítkový exponent** (1.01–1.26) řízen 5 drivery: návaznost na předchozí výsledky, flexibilita vývoje, řešení architektury/rizik, koheze týmu, **vyspělost procesu (SEI CMM)**.
- Nové atributy EM: **RUSE** (požadovaný stupeň znovupoužitelnosti), DOCU, RCPX, PVOL, PERS, PREX, PCON, SECU, **SITE** (vývoj na více místech)…
- Modifikace: `ESLOC = ASLOC·(AA + SU + 0,4·DM + 0,3·CM + 0,3·IM)` (AA = posouzení použitelnosti modulu, SU = pochopení/čitelnost SW).

### 2.7 Funkční body (FPA)

Normalizovaná metrika domény: **měří aplikační funkce a data, ne kód** (IFPUG).

- **Transakční funkce:** Externí vstupy (EI), Externí výstupy (EO), Externí dotazy (EQ).
- **Datové funkce:** Vnitřní logické soubory (ILF), Soubory vnějšího rozhraní (EIF).
- Každá položka se klasifikuje (nízká/průměrná/vysoká) podle matice **DET × FTR/RET**, vynásobí vahou a sečte → **neupravené FP**.
- **Faktor technické složitosti:** 14 obecných charakteristik (0–5), `FP = [0,65 + 0,01·Σ] × neupravené FP`.
- **Praktické přepočty (Capers Jones):** 1 FP ≈ 128 příkazů v C, 107 COBOL, 13 SQL; `FP^1.2` ≈ počet test. případů; `FP/150` ≈ vývojáři, `FP/750` ≈ udržovatelé. Velikosti: vstup objednávky ~1 250 FP, rezervace letenek ~25 000 FP, Windows 95 ~85 000 FP.

> [!example] Příklad z praxe (FI MU, 2005) Změřeno 7 studentských projektů (C/C++/Java/Delphi). Z FP přepočtena velikost kódu (porovnáno se skutečností) a odhadnuta doba řešení přes COCOMO i FPA → výsledky porovnány. Ukázalo to, jak se odhady liší podle zvoleného jazyka.

### 2.8 Kvalita SW

**Kvalita** (IEEE 610.12): stupeň, do jaké míry systém splňuje specifikované požadavky a potřeby/očekávání uživatele. Aspekty: odchylky od požadavků, nedodržení standardů, odchylky od implicitních zvyklostí.

**Faktory kvality – McCall (1977):** korektnost, spolehlivost, efektivita, integrita, použitelnost, udržovatelnost, flexibilita, testovatelnost, přenositelnost, **znovupoužitelnost**, schopnost spolupráce. (Přímo měřitelné: #chyb/KLOC/čas; nepřímo: použitelnost, udržovatelnost.)

**Globální hodnocení vyspělosti výroby:**

- **CMM (Capability Maturity Model, SEI):** 1 Výchozí (chaotický) → 2 Opakovatelný (řízené požadavky, plánování, QA, konfigurace) → 3 Definovaný (org. proces, školení, inženýrské metody) → 4 Řízený (kvantitativní, statisticky řízená kvalita) → 5 Optimalizující (prevence chyb, inovace, řízené změny procesu).
- **ISO 9001** (systémy kvality – 20 prvků: odpovědnost vedení, řízení návrhu/dokumentace, inspekce a testování…), **ISO 9000-3** (jak aplikovat na vývoj SW).
- **MBNQA** (cena kvality).

**Jak začít SQA:** hypotéza → výběr metrik → sběr dat → interpretace → akce ke zlepšení → iterace.

### 2.9 Testování

> [!quote] Definice Testování je proces spuštění programu **s cílem najít chybu**. Úspěšný test je ten, který odhalí dosud neodhalenou chybu (Myers). Testování _nemůže prokázat nepřítomnost_ defektů, jen jejich přítomnost.

- **Verifikace** = „děláme věci správně" (test proti vnitřní činnosti) × **Validace** = „děláme správné věci" (test proti specifikaci).
- **Black-box** (funkční, dle SRS, zajímá co dělá) × **White-box** (struktura, pokrytí příkazů/cest; **cyklomatická složitost** = rozhodnutí + 1 = hrany − uzly + 2 → počet nezávislých cest).
- Úplné testování není realizovatelné (kombinatorická exploze cest) → selektivní/vzorkové testování.
- **Testování je destruktivní činnost** → programátor není dobrý tester vlastního výtvoru; nutné nezávislé týmy.
- **Úrovně:** unit (vývojáři, „test drivery") → integrace → systémové → akceptační. **V-model** páruje fáze návrhu s odpovídajícími úrovněmi testů.
- Zdroje defektů: ~56 % požadavky, ~27 % návrh, ~7 % kód → většina chyb vzniká _před_ kódováním.
![[v-model.webp]]

### 2.10 Inspekce a recenze

Formálnost roste: konverzace → prohlídka (walkthrough) → recenze → **inspekce** (nejformálnější, nejúčinnější). Efektivita odhalení defektů roste s formálností (Pressman).

> [!important] Relativní cena nalezení a opravy defektu (normováno k návrhu) Požadavky 0,75 · **Návrh 1,0** · Kódování 1,5 · Testování 3,0 · Systémové testy 10,0 · **Provoz 60–100** → inspekce v raných fázích = obrovská úspora.

- **Tým inspekce:** moderátor (vedoucí) · zapisovatel · autor · recenzenti/inspektoři.
- **Pravidla:** přezkušuj **produkt, ne tvůrce**; klidný tón, otázky místo obvinění; uváděj problémy, neřeš je; vyhni se diskusi o stylu; naplánuj jako úlohu projektu; zaznamenej výsledky.
- IBM **ODC** (ortogonální klasifikace defektů) — typ defektu se asociuje s vývojovou etapou.

### 2.11 Chyby a neúspěchy projektů

- **Výsledek projektu:** úspěšný (včas, v rozpočtu, všechny rysy) · s výhradami (překročen rozpočet/termín, méně funkcí) · neúspěšný (zastaven/zrušen). _(Standish CHAOS / Johnson 2001.)_
- **Porucha (failure)** = neschopnost plnit funkci × **chyba/defekt (fault)** v programu × **omyl (error)** člověka.
- **Prevence neúspěchu:** zapojení vrcholového řízení a uživatelů, efektivní project a requirements management, inkrementální vývoj, „všechny smysluplné inženýrské kroky" (dokumentace, měření, plánování, QA).

---

## 3. (Rational) Unified Process — UP / RUP

### 3.1 Co to je

Predikativní framework pro vývoj SW. **4 charakteristiky / hesla:**

- **Iterativní a inkrementální**
- **Řízený riziky** (risk-driven)
- **Architecture-centric** (zaměřený na architekturu)
- **Use-case driven** (řízený případy užití / požadavky)

**Historie:** Objectory (1987–95, Ivar Jacobson) → Rational Objectory Process 4.1 (1996) → **Rational Unified Process 5.0 (1998)**; UP = sjednocení s UML (Booch, Rumbaugh, Jacobson — „tři amigos"). RUP je komerční, detailně rozpracovaná konkretizace obecného UP.

> [!note] UP je process framework Není to jeden pevný proces — je navržen pro flexibilitu a rozšiřitelnost (různé strategie ŽC, různé artefakty, aktivity, role).

### 3.2 Iterace a workflows

Každá **iterace = mini-projekt** (neměla by trvat >3 měsíce); rozdíl mezi dvěma iteracemi = **inkrement**. Každá iterace prochází **6 workflows (disciplín):**

1. Business Modelling
2. Requirements
3. Analysis & Design
4. Implementation
5. Test
6. Deployment (interní/externí)

Iterace se seskupují do **fází**. Workflows jsou aktivní napříč všemi fázemi, jen s různou intenzitou.

![[up_hump_chart.png]]

### 3.3 Čtyři fáze a milníky

|Fáze|Cíl|Milník (na konci)|
|---|---|---|
|**Inception** (Zahájení)|vymezit rozsah projektu, business case|Lifecycle Objectives|
|**Elaboration** (Rozpracování)|plán, specifikace rysů, **baseline architektury**|Lifecycle Architecture (baseline)|
|**Construction** (Konstrukce)|postavit produkt|Initial Operational Capability|
|**Transition** (Předání)|předat produkt uživatelům|Product Release|

### 3.4 Modely a UML

Každý workflow je asociován s jedním či více **modely**: Use Case Model, Analysis Model, Design Model, Implementation Model, Deployment Model, Test Model. **UML diagramy poskytují pohledy (views) do modelů** (use case, class, sequence, collaboration, statechart, activity, component, deployment, object diagrams).

- **Use-case driven:** případy užití řídí celý řetězec workflow (analýza → návrh → implementace → test), vážou je dohromady.
- **Architecture-centric:** architektura ztělesňuje kolekci pohledů na modely; UP předepisuje postupné vytváření _spustitelné_ architektury. „Use cases = funkce, architektura = forma" — musí být v rovnováze.
- Základní pojmy: **Artefakt** (informace produkovaná/měněná/užívaná procesem), **Aktivita** (jednotka práce), **Role/Worker**.

### 3.5 Kdy použít UP

- Většina požadavků musí být specifikována předem.
- Potřebuješ plnou kontrolu nad procesem a týmem.
- Vývoj vyžaduje důkladnou dokumentaci (UML diagramy).

> [!example] Kontraktace u predikativního vývoje — Fixed Time, Fixed Price Dohoda zaplatit za jasně předem definovaný výstup. **+** zákazník dostane definovaný produkt, není nutný jeho dohled během projektu. **−** striktní termíny a rozpočet, obtížné plnit change requesty, víc času na plánování, smlouva musí obsahovat hodně specifik (akceptační kritéria, milníky, penále).

---

## 4. Agilní metodiky a principy agilního vývoje

### 4.1 Manifest agilního vývoje (Utah, únor 2001)

> [!quote] 4 hodnoty
> 
> - **Jednotlivci a interakce** před procesy a nástroji
> - **Fungující software** před obsáhlou dokumentací
> - **Spolupráce se zákazníkem** před vyjednáváním o smlouvě
> - **Reakce na změnu** před plněním plánu
> 
> Umožnit změnu je efektivnější než se jí bránit — je třeba být připraven reagovat na nepředvídatelné.

### 4.2 Společné rysy všech agilních metodik

1. **Iterativní a inkrementální vývoj s krátkými iteracemi** — funkcionalita se dodává po částech; zákazník průběžně sleduje vývoj a má jistotu, že nedostane něco nechtěného.
2. **Komunikace mezi zákazníkem a vývojovým týmem** — ideálně je zákazník přímo součástí týmu, účastní se návrhu, testů a dává zpětnou vazbu.
3. **Průběžné automatizované testování** — testy předem připravené (ideálně před implementací), při každé změně se pouští kompletní sada (regrese, ochrana integrace).

### 4.3 Konkrétní metodiky

#### Extreme Programming (XP) — Beck, Cunningham, Jeffries

Pro menší projekty/týmy, nejasné nebo rychle se měnící požadavky. Jediný exaktní, nezpochybnitelný zdroj informací = **zdrojový kód**. Osvědčené postupy dotahuje do extrému: revize kódu → **párové programování**; testování → nepřetržité (unit/funkční/akceptační); návrh → každodenní činnost.

- **Principy XP:** rychlá zpětná vazba · předpoklad jednoduchosti (přidej složitost až když je potřeba) · přírůstková změna (malé inkrementy) · využití (vítání) změny · kvalitní práce (kvalita je fixní proměnná).

#### Feature-Driven Development (FDD) — De Luca, Coad

Vývoj po malých **vlastnostech (features)** — elementární funkcionalita s hodnotou pro uživatele. **5 fází** (první 3 sekvenční, pak iterativní), iterace ~2 týdny. Začíná tvorbou modelu → seznam vlastností → implementace po vlastnostech. Detailně plánuje a kontroluje proces, měří pokrok.

#### SCRUM — Schwaber, Beedle → [[#5. SCRUM detailně]]

Iterativní vývoj, sprinty ~měsíc. Nedefinuje konkrétní procesy, jen zavádí každodenní schůzky (scrum meeting). Každý sprint končí předvedením funkčního dema zákazníkovi.

#### Test-Driven Development (TDD) — Beck

Neřeší tvorbu specifikací/plánů/dokumentace (každý tým si zvolí sám). Testy = hlavní fáze vývoje. **Základní pravidlo: psát testy dřív než kód**, implementovat jen ty části, které testem projdou (red → green → refactor).

---

## 5. SCRUM detailně

> [!abstract] Přehled Nejrozšířenější agilní framework. „Simple to understand, difficult to master." Iterativní a inkrementální. Teorie: **Scrum Guide** (~19 stran) — krátká, ale striktní pravidla. _„Bez dodržení pravidel nemáš Scrum."_ Očekává se doplnění o další metody/nástroje.

### 5.1 Role

- **Product Owner** — vlastní a prioritizuje Product Backlog, maximalizuje hodnotu produktu, zastupuje zákazníka/byznys.
- **Scrum Master** — zajišťuje dodržování Scrumu, odstraňuje překážky (impediments), slouží týmu (servant leader).
- **Development Team** — samoorganizující se, cross-funkční, dodává inkrement.

### 5.2 Artefakty

- **Product Backlog** — uspořádaný seznam všeho, co se může do produktu udělat (user stories), spravuje PO.
- **Sprint Backlog** — položky vybrané do aktuálního sprintu + plán jejich dodání.
- **Product Increment** — funkční přírůstek na konci sprintu (splňuje Definition of Done).

### 5.3 Události (events)

- **Sprint** — časově ohraničená iterace (typicky ~měsíc / 2–4 týdny), kontejner pro ostatní události.
- **Sprint Planning** — co se dodá a jak (vznik Sprint Backlogu).
- **Daily Scrum** — krátká denní schůzka: co jsem udělal, co budu dělat, jaké překážky.
- **Sprint Review** — předvedení inkrementu zákazníkovi, zpětná vazba, úprava backlogu.
- **Sprint Retrospective** — zlepšení **procesu** (co fungovalo, co změnit).

### 5.4 Odhadování — Planning Poker

Odhad pracnosti položky backlogu popsané jako **user story**. Každý člen ukáže kartu (např. 5 **story points**), karty se odhalí naráz, diskutuje se do konsenzu. **Story point nemá objektivní hodnotu** — každý tým si interně definuje, kolik úsilí představuje.

### 5.5 Kdy použít Scrum

- Přesné požadavky nejsou předem k dispozici.
- Tým je silný v komunikaci a spolupráci.
- Zákazník chce použít nějakou formu produktu co nejdříve.

> [!example] Kontraktace u agilního vývoje — Time & Material Platba za odpracovaný čas a zdroje (kolik lidí, jaká hodinová sazba). **+** flexibilní, rozsah není znám předem, snadné přidávat funkce / change requesty, častý dohled zákazníka buduje důvěru. **−** stálé zapojení zákazníka stojí čas, těžko se předvídá finální rozpočet a termín, řízení trojimperativu je nepřetržitá výzva.

---

## 6. Nasazení a provoz softwarových systémů

### 6.1 Nasazení (Deployment)

- V **UP** je **Deployment** samostatný workflow (interní/externí nasazení) a vyúsťuje ve fázi **Transition** s milníkem **Product Release**; v UP existuje i **Deployment Model** a deployment diagram (UML) popisující rozmístění komponent na uzly.
- Akceptační testování (Acceptance Testing) → finální systém + dokumentace předané zákazníkovi.
- FP charakteristiky systému řeší i otázky nasazení: _„Jsou konverze a instalace zahrnuty v návrhu? Je systém navrhován pro násobné instalace u různých organizací?"_

### 6.2 Provoz a služby — ITIL

> [!note] Service (definice) Prostředek dodávání hodnoty zákazníkům usnadněním výstupů, kterých chtějí dosáhnout (např. posílání e-mailu), **bez vlastnictví konkrétních nákladů** (mailserver) **a rizik** (stárnutí technologie).

**ITIL** (Information Technology Infrastructure Library) = best practices pro **správu IT služeb** — odpovídá na otázku _„co se děje před a po IT projektu?"_. Výstup projektu (deliverable) se stává součástí služeb a produktů, které vyžadují další správu.

**5 fází životního cyklu služby:**

1. **Service Strategy**
2. **Service Design**
3. **Service Transition** (sem zapadá nasazení výstupu projektu)
4. **Service Operation** (běžný provoz, řešení incidentů)
5. **Continual Service Improvement**

Každá fáze = procesy s jasně definovanými vstupy a výstupy.

### 6.3 Chyby a opotřebení v provozu

- **HW** — zpočátku výkon stačí, chyby se postupně odstraňují; nakonec HW „zaostává" za okolím → výměna.
- **SW** — naivní představa, že časem odstraníš všechny chyby a problémy zmizí. Realita: zákazník během provozu opakovaně žádá modifikace → SW je složitější a vznikají nové chyby → jednou se vyplatí systém přepsat od nuly (**Lehmanův zákon**, viz [[#7. Údržba softwarových systémů]]).

---

## 7. Údržba softwarových systémů

> [!quote] Definice **Údržba** = modifikace SW produktu **po předání zákazníkovi** s cílem odstranit chyby, zlepšit výkon nebo se přizpůsobit měnícímu se prostředí.

**Typy údržby (klasické dělení):**

- **Korektivní** — oprava chyb.
- **Adaptivní** — přizpůsobení změnám prostředí (OS, HW, legislativa, třetí strany).
- **Perfektivní** — zlepšení výkonu/funkcí na žádost uživatele.
- _(Preventivní — úpravy snižující budoucí náklady na údržbu.)_

**Problémy údržby (z praxe):** zvýšená podpora hned po předání, požadavky na rozvoj, změny v navázaných systémech třetích stran, **změny zákonů**, zastaralá dokumentace / knowledge base.

> [!important] Relativní cena údržby Když budoucí strukturu dobře promyslíš a navrhneš, implementace je o trochu dražší, ale náklad se vrátí ve **fázi provozu**, kdy se poskytuje údržba a on-demand úpravy. _(Údržba je obvykle nejdražší fáze ŽC.)_

### 7.1 Lehmanovy zákony evoluce SW

**1974:**

- **Zákon trvalé změny** — systém v reálném prostředí se neustále mění, dokud není levnější ho restrukturalizovat nebo nahradit novou verzí.
- **Zákon rostoucí složitosti** — během evolučních změn se program stává méně strukturovaným a roste vnitřní složitost (entropie); její odstranění vyžaduje další úsilí.
- **Zákon samoregulace** — tempo změn globálních atributů se z dlouhodobého hlediska chová jako samoregulovaný, statisticky popsatelný a předpověditelný proces.

**1978:**

- **Zákon invariantní pracovní rychlosti** — celkový postup vývoje je statisticky invariantní; tempo nekoreluje s vloženými zdroji.
- **Zákon zachování známosti** — uživatelé i tým musí udržovat znalost systému; rychlý růst brání jejímu zvládnutí → průměrný přírůstek zůstává invariantní.

---

## 8. Znovupoužitelnost (reuse)

**Znovupoužitelnost** (McCall): rozsah, v jakém lze program nebo jeho části znovu použít v jiné aplikaci (funkce a balení). Jeden ze základních faktorů kvality.

**Kde se v PA017 objevuje:**

- **Typy produktů:** většina SW je „tailor-made", menšina je **out-of-box** (krabicový SW = forma znovupoužití hotového řešení).
- **COCOMO** – odhad pro **modifikaci existujícího SW**: `ESLOC = ASLOC·(0,4·DM + 0,3·CM + 0,3·IM)`.
- **COCOMO II** – znovupoužití byl jeden z hlavních důvodů revize; faktory **RUSE** (požadovaný stupeň znovupoužitelnosti — vyšší RUSE = dražší vývoj, protože komponenta musí být obecnější), **AA** a **SU** při asimilaci cizích modulů; nelineární model znovupoužití (přizpůsobení modulu může být dražší než lineární předpoklad).
- **Funkční body** – jedna z 14 obecných charakteristik: _„Je kód navrhován s cílem znovupoužití?"_

**Praktické formy znovupoužití:** knihovny a komponenty, frameworky, návrhové vzory, COTS/krabicový SW, generické produkty, znovupoužití architektury (v UP architecture-centric).

> [!warning] Znovupoužitelnost není zadarmo Návrh komponenty pro opakované použití je dražší (obecnost, dokumentace, testování) — proto COCOMO II zavádí faktor RUSE. Vyplatí se tam, kde se komponenta skutečně použije vícekrát.

---

## 9. Příklady z praxe (shrnutí pro otázku)

> [!example] Co můžeš zmínit jako „příklad z praxe"
> 
> - **Volba ŽC:** kritická infrastruktura → ne Researcher; malá utilita s jasným zadáním → vodopád; produkt s nejasným zadáním a požadavkem na rychlé dodání → Scrum.
> - **UP fáze:** vývoj informačního systému přes Inception → Elaboration (baseline architektury) → Construction → Transition (release).
> - **Scrum:** dvoutýdenní sprinty, Planning Poker pro odhad user stories, demo zákazníkovi na konci sprintu.
> - **Odhady:** FI MU 2005 — 7 studentských projektů (C/C++/Java/Delphi), porovnání FPA vs. COCOMO; vliv volby jazyka na odhad.
> - **Velikosti aplikací:** rezervace letenek ~25 000 FP, Windows 95 ~85 000 FP.
> - **Cena chyb:** „kdyby 99,9 % funkcí bylo správně" → tisíce chybných lékařských předpisů/hodinu, havárie letů — ilustrace, proč 99,9 % nestačí.
> - **Údržba:** změna zákona (DPH, GDPR) vynutí adaptivní údržbu IS; opakované change requesty zvyšují entropii (Lehman) → časem přepis.
> - **Provoz:** přechod projektového výstupu do ITIL Service Operation (incident management, SLA).
> - **Neúspěšné projekty:** Standish CHAOS report — velká část IT projektů „s výhradami" nebo neúspěšných.

---

## 10. Rychlé opáčko před zkouškou (cheat-sheet)

- **4 aktivity vývoje:** specifikace · vývoj · validace · evoluce.
- **Modely ŽC:** vodopád · inkrementální · prototyp · researcher · spirála (Boehm) · iterativní.
- **Predikativní vs. agilní:** procesy/fixní rozsah/plán předem (UP) × lidé/měnící se rozsah/min. plánování (Scrum).
- **UP 4 hesla:** iterativní & inkrementální · risk-driven · architecture-centric · use-case driven.
- **UP fáze:** Inception → Elaboration → Construction → Transition (milníky: LCO, LCA, IOC, Release).
- **UP 6 workflows:** Business Modelling · Requirements · Analysis & Design · Implementation · Test · Deployment.
- **Agile manifest:** lidé > procesy · funkční SW > dokumentace · spolupráce > smlouva · změna > plán.
- **Agile rysy:** krátké iterace · komunikace se zákazníkem · průběžné automatizované testy.
- **Metodiky:** XP · FDD · Scrum · TDD.
- **Scrum 3-3-5:** 3 role (PO, SM, Dev) · 3 artefakty (Product/Sprint Backlog, Increment) · 5 událostí (Planning, Sprint, Daily, Review, Retrospective).
- **Provoz:** ITIL 5 fází (Strategy, Design, Transition, Operation, CSI).
- **Údržba:** korektivní/adaptivní/perfektivní; Lehmanovy zákony; nejdražší fáze.
- **Reuse:** McCall faktor · COCOMO II RUSE · out-of-box produkty.
- **Odhady:** SW fyzika (Putnam) · COCOMO (módy + 15 driverů) · COCOMO II · FPA (EI/EO/EQ/ILF/EIF).
- **Kvalita:** McCall · CMM (5 úrovní) · ISO 9001.
- **Chyby:** cena opravy roste s fází (návrh 1 → provoz 60–100); inspekce = nejlevnější prevence; testování dokazuje přítomnost, ne nepřítomnost chyb.
- **Brooksův zákon:** přidání lidí do zpožděného projektu zvyšuje zpoždění.

---

## 11. Q&A — reálné otázky komise (PA017)

> [!info] Zdroj Sestaveno z protokolů obhajob **2016–2026** (soubor _SSZ/SSME — okruhy členů komise_). Níže jsou jen otázky, které reálně padly k softwarovému inženýrství, seskupené podle podotázek + modelové odpovědi opřené o tvoje poznámky a materiály PA017. Tato sekce shrnuje, co reálně padá u komise, a odkazuje na detaily výše.

### Kdo zkouší a na co se fixuje

> [!tip] Profily zkoušejících (PA017)
> 
> - **Ráček** — hlavní zkoušející okruhu. Skoro každý rok chce **metriky** (klasifikace procesní/produktové/zdrojové), **cyklomatickou složitost**, **funkční body** (co konkrétně jsou EI/EO/EQ/ILF/EIF — _nesnáší abstraktní „něco, nějaká data"_), **COCOMO/COCOMO II** do hloubky (módy, modely, vzorce), **kvalitu SW** a **testování**. Říká: _„podstatou není memorování, ale schopnost vysvětlit podstatu a kontext a navázat na praktickou zkušenost."_
> - **Ošlejšek** — chce **ITIL**: 5 fází, co se v každé děje, klíčové procesy, příklad z každé „knihy". Jde dost do hloubky.
> - **Plhák / Spurný / Oujezský / Vojnar (2024–2026)** — spíš základy a **praktické mapování na diplomku**: prediktivní vs. agilní, fáze ŽC, UP, Scrum, druhy testů. Milí, navádějí.
> - **Ročkai (2026)** — formální metody/verifikace: co je verifikace × testování, _můžeme zaručit bezchybný program?_, jak jinak verifikovat než testováním, coverage.

> [!warning] Známé chytáky (padly opakovaně)
> 
> - **UP fáze ≠ iterace:** Inception/Elaboration/Construction/Transition pokrývají **celý projekt**, každá se skládá z 1+ iterací. _(Ošlejšek 2026, Ráček 2020)_ — viz sekce „3. Unified Process".
> - **Agile — co je pevné a co proměnlivé:** pevný je **deadline a peníze**, proměnlivá je **funkcionalita**. _(Ošlejšek 2026)_
> - **Backlog ve Scrumu** spravuje **Product Owner**. _(Ráček 2022)_
> - **FP komponenty** popisuj konkrétně (EI = formulář pro zadání objednávky…), ne „nějaká data". _(Ráček 2019)_
> - **Cyklomatická složitost** — uměj **vzorec** i hranici, odkdy je to „špatné" (klasicky **V(G) > 10**). _(Spurný 2024)_

---

### 1. Životní cyklus a fáze vývoje

**Otázka (Ráček 2017, Plhák 2024/2026, Spurný 2026): Typy modelů životního cyklu — kdy který zvolíš a proč? Popiš jednotlivé fáze vývoje.**

- Fáze (společné všem modelům): **analýza/požadavky → návrh → implementace → testování → nasazení/provoz**. Modely se liší tím, jestli a jak projekt dělí na menší části → jiný způsob řízení.
- Volba podle typu projektu:
    - **Vodopád** — malý projekt, **velmi jasné a stabilní požadavky**.
    - **Iterativní** — velký projekt s relativně jasnými požadavky.
    - **Inkrementální** — chceme dodávat po částech, brzká zpětná vazba, distribuce nákladů.
    - **Prototyp** — menší projekt s **nejasnými požadavky** (prototyp se po analýze zahodí).
    - **Spirála** — důraz na **analýzu rizik**, práce v cyklech.
    - **Researcher** — obecné/byznys zadání, experiment; _nevhodné pro kritickou infrastrukturu_ (nelze řídit, není dokumentace, řešitelé nenahraditelní).
- Praktická pointa pro Spurného: umět **namapovat fáze na vlastní diplomku** a uvést **typy požadavků** (funkční × nefunkční) s příklady.

**Otázka (Spurný 2026): Typy požadavků.**

- **Funkční** (co systém dělá) × **nefunkční** (výkon, bezpečnost, použitelnost, spolehlivost). Zachycení: zajímá nás **co** a **proč**, ne **jak**; rozlišuj co klient _říká_ a co _potřebuje_.

---

### 2. Prediktivní vs. agilní + metodiky

**Otázka (mnoho zkoušejících — Plhák, Oujezský „Waterfall a agile", Ošlejšek 2026): Co je prediktivní a co agilní vývoj, jak se liší?**

- **Prediktivní (RUP):** rigidní, **důraz na procesy**, fixní rozsah/požadavky, plánování předem, plná kontrola; pevné jsou požadavky, proměnné čas/zdroje.
- **Agilní (Scrum):** flexibilní, **důraz na lidi**, požadavky se mění, minimální plánování předem; **pevný deadline + peníze, proměnlivá funkcionalita**.
- Příklady agilních metodik: **XP, Scrum** (+ FDD, TDD — viz doplnění níže).

**Otázka (Ráček 2022): Základní atributy agilního vývoje.**

- Manifest (chce slyšet hlavně): **working software over comprehensive documentation** a **individuals & interactions / communication over processes**; + reakce na změnu > plán, spolupráce se zákazníkem > smlouva.
- Společné rysy: krátké iterace, komunikace se zákazníkem, průběžné automatizované testování.

> [!example] Chyták (Ráček 2022): Jak nacenit SW při agilním vývoji? Žádaná odpověď: **jádro systému** naceň klasicky (fixed-price podle přesné dokumentace a kalkulace předem — ať zákazník ví, do čeho jde), a na **průběžně agilně dodávanou funkcionalitu** přidej **tarif podle velikosti týmu, který sprintuje** (Time & Material).

---

### 3. UP / RUP

**Otázka (Ráček 2020, Ošlejšek 2026): Popiš UP diagram (sloupce/řádky), fáze, UML diagramy, napasuj iterativní a inkrementální přístup.**

- **Sloupce** = časová osa = 4 fáze (Inception, Elaboration, Construction, Transition), každá z 1+ iterací.
- **Řádky** = disciplíny/workflows (Business Modeling, Requirements, Analysis & Design, Implementation, Test, Deployment + podpůrné: Config & Change Mgmt, Project Mgmt, Environment).
- **Vlnky (humps)** = intenzita úsilí dané disciplíny v čase (Requirements vysoko v Inception, Implementation vysoko v Construction).
- **Iterativní** = každá iterace prochází všemi disciplínami, produkt se evolučně zpřesňuje (malíř skicuje celé plátno). **Inkrementální** = výstup iterace je spustitelný, otestovaný přírůstek (dům po pokojích). UP je oboje zároveň.
- UML per workflow: Business → activity; Requirements → use case; Analysis & Design → sequence + class; Implementation → class + component; Deployment → deployment diagram.

**Otázka (Ráček 2017): Přesné názvy etap + milníky.**

- Inception → **Lifecycle Objectives** · Elaboration → **Lifecycle Architecture** (baseline) · Construction → **Initial Operational Capability** · Transition → **Product Release**.
- **RUP** = komerční konkretizace obecného rámce **UP** (přidává role, postupy).

---

### 4. SCRUM

**Otázka (Ráček 2022, Ošlejšek 2026, Spurný 2026, Bühnová): Popiš Scrum — role, artefakty, události. Kdo má backlog?**

- **Role:** Product Owner (spravuje **Product Backlog**, priority, zastupuje stakeholdery), Scrum Master (dodržování procesu, odstraňuje překážky), tým vývojářů (3–9, samoorganizující, soběstačný).
- **Artefakty:** Product Backlog (user stories) · Sprint Backlog (výběr do sprintu, rozpad na tasky) · Product Increment (použitelný přírůstek dle Definition of Done).
- **Události:** Sprint (≤ 1 měsíc, stejně dlouhé) · Sprint Planning · Daily Scrum (15 min) · Sprint Review (demo + úprava backlogu) · Sprint Retrospective (zlepšení procesu).
- **Backlog spravuje Product Owner.** Odhady přes **Planning Poker** (story points, Fibonacci), tempo = **team velocity**, sledováno v **burndown chartu**.
- Iterativní vs. iterační/inkrementální: umět to říct stejně jako u UP (Ošlejšek se ptá i tady).

---

### 5. Kvalita SW

**Otázka (Ráček 2018: trojúhelník kvality, jak měřit, metrika udržovatelnosti; Bühnová 2023).**

> [!note] Detaily k tématu výše v sekci „2. Řízení softwarového vývoje → Kvalita SW".

- **Kvalita** (IEEE): míra, do jaké systém splňuje specifikované požadavky a očekávání uživatele.
- **Trojúhelník kvality** = trojimperativ **čas × cena × rozsah** (kvalita je uvnitř — je to to, co obětuješ, když utáhneš ostatní vrcholy).
- **Faktory kvality (McCall):** korektnost, spolehlivost, efektivita, použitelnost, **udržovatelnost**, flexibilita, testovatelnost, přenositelnost, **znovupoužitelnost**…
- **Jak měřit:** přímo měřitelné (#chyb/KLOC, MTBF) × nepřímo (udržovatelnost, použitelnost).
- **Příklad metriky udržovatelnosti:** **MTTC** (mean time to change), počet modulů zasažených změnou, nebo **cyklomatická složitost** jako proxy (vyšší složitost → hůř se udržuje).
- Vyspělost výroby: **CMM** (5 úrovní: Výchozí → Opakovatelný → Definovaný → Řízený → Optimalizující), **ISO 9001**.

**Otázka (Ráček 2020): Jak klasifikujeme chyby?**

- **Podle závažnosti (severity):** kritická / velká / malá (blokuje × omezuje × kosmetická).
- **Ortogonální klasifikace defektů — ODC (IBM):** typ defektu se asociuje s vývojovou etapou (umožní zpětně poznat, ve které fázi vznikla většina chyb).
- Pojmy: **omyl (error, člověk) → defekt/chyba (fault, v programu) → porucha (failure, projev za běhu)**.

---

### 6. Testování

**Otázka (Ráček 2018/2021, Spurný 2024/2026, Vojnar 2026, Plhák 2026, Ročkai 2026).**

- **V-model:** Požadavky/Use Case ↔ **akceptační** testy · Architektura ↔ **systémové** · Návrh komponent ↔ **integrační** · Implementace ↔ **jednotkové**.
- **Kdo dělá co:** unit = vývojáři · integrační = vývojáři/test tým · systémové = test tým (i nefunkční požadavky) · **akceptační = zákazník** (validace).
- **Verifikace × validace:** verifikace = „děláme věci správně" (proti specifikaci/struktuře) × validace = „děláme správné věci" (proti potřebám uživatele).
- **White-box** (struktura, pokrytí cest) × **black-box** (funkce dle specifikace).
- **Integrační postupy:** **shora-dolů** (stuby) × **zdola-nahoru** (drivery) × sandwich/big-bang.
- **Kompletní × selektivní testování:** úplné testy nejsou realizovatelné (kombinatorická exploze) → testuje se vzorek.
- **Další druhy** (Vojnar 2026): **regresní** (po změně pustit celou sadu), **smoke** (základní funkčnost po buildu), **sanity**, **fuzzing** (náhodné/neočekávané vstupy).
- **Code/flow coverage** (Lexa 2024, Ročkai 2026): % pokrytí příkazů/větví/cest testy.
- _„Testování dokazuje přítomnost chyb, ne jejich nepřítomnost"_ → **bezchybný program nelze zaručit**; jinak než testováním verifikujeme **statickou analýzou, inspekcí/recenzí, formálními metodami** (Ročkai).

---

### 7. Metriky

**Otázka (Ráček 2019/2020/2021 — opakovaně!): Klasifikace metrik + příklady, LOC, cyklomatická složitost.**

> [!note] Detaily výše v sekci „2. Řízení softwarového vývoje". Ráček chce metriky skoro pokaždé.

- **Klasifikace (chce slyšet přesně tyto 3 + příklady):**
    - **Procesní** — o procesu vývoje: pracnost, doba, počet nalezených defektů na fázi, produktivita.
    - **Produktové** — o produktu: **LOC**, **cyklomatická složitost**, **funkční body**, počet defektů.
    - **Zdrojové** — o zdrojích: zkušenost týmu, cena, výkon HW.
- **LOC — klady/zápory:** + jednoduché, snadno automaticky spočítatelné; − **závislé na jazyce a programátorovi**, neměří složitost, nejde změřit brzy, motivuje psát delší kód.
- **Cyklomatická složitost V(G):** `V(G) = hrany − uzly + 2` (= počet rozhodnutí + 1 = počet nezávislých cest = počet oblastí grafu). K čemu: white-box testy (kolik nezávislých cest = min. počet test. případů) a jako míra složitosti/udržovatelnosti. **Hranice:** klasicky **V(G) > 10 = příliš složité.**
- **Jiná metrika založená na složitosti** (Ráček 2021): **Halstead** (z počtu operátorů/operandů → objem, obtížnost, pracnost).

---

### 8. Funkční body (FP)

**Otázka (Ráček 2019/2021): K čemu jsou FP, jak se počítají, jejich typy. Co konkrétně reprezentují EI/EO/EQ/ILF/EIF?**

> [!note] Detaily výše v sekci „2. Řízení softwarového vývoje → Funkční body". Ráček je miluje — mluv konkrétně.

- **K čemu:** metrika velikosti **z pohledu funkcí a dat aplikace, nezávislá na jazyku** (na rozdíl od LOC) → odhad velikosti/pracnosti už z návrhu.
- **5 typů (s konkrétním příkladem!):**
    - **EI** (External Input) — vstup měnící data, např. _formulář „přidat objednávku"_.
    - **EO** (External Output) — výstup s výpočtem, např. _měsíční report tržeb_.
    - **EQ** (External Inquiry) — dotaz bez výpočtu (vstup+výstup), např. _vyhledání zákazníka podle ID_.
    - **ILF** (Internal Logical File) — interně udržovaná data, např. _tabulka objednávek_.
    - **EIF** (External Interface File) — data čtená z cizího systému, např. _číselník měn z externí služby_.
- **Výpočet:** každou položku klasifikuj (nízká/průměrná/vysoká) podle matice DET×FTR/RET → vynásob vahou → součet = neupravené FP; pak `FP = [0,65 + 0,01·Σ(14 charakteristik)] × neupravené FP`.
- **Příklady ze 14 charakteristik:** distribuované zpracování, výkon, znovupoužitelnost, snadnost instalace/změn…

---

### 9. Odhadování úsilí — COCOMO

**Otázka (Ráček 2021/2023): Jaké máme způsoby odhadování a kdy je použít? COCOMO/COCOMO II do hloubky.**

> [!note] Detaily výše v sekci „2. Řízení softwarového vývoje → COCOMO". Ráček chce vzorce a módy.

- **Způsoby a kdy:** **expertní posudek** (zkušenost, rychlé), **analogie** (podobný hotový projekt), **dekompozice/WBS + PERT**, **algoritmické modely (COCOMO)**, **funkční body** (brzy, z návrhu).
- **COCOMO** (Boehm 1981): cena přímo závisí na velikosti (KSLOC). `E = a·KSLOC^b`, `T = c·E^d`.
    - **3 úrovně detailu:** základní / střední / pokročilá.
    - **3 vývojové módy:** **organický** (malé, <50 KSLOC, jasné požadavky), **semidetached** (střední, <300 KSLOC), **embedded** (velké, RT, těsná omezení).
    - **15 cost drivers** (Fc) — atributy produktu/HW/týmu/projektu (RELY, CPLX, ACAP, TOOL…), `E = Fc·En`.
- **COCOMO II** (1995): **3 modely podle etapy** — Application Composition / Early Design / Post-Architecture; velikost v KSLOC/UFP; **scale factors** řízené i vyspělostí procesu (CMM); nový faktor **RUSE** (znovupoužitelnost).
- _Kdy který:_ čím ranější fáze a méně informací, tím hrubší model (Early Design); po specifikaci architektury Post-Architecture.

---

### 10. Inspekce / kontrola během vývoje

**Otázka (Ráček 2018): Jak probíhá komisionální kontrola během vývoje, kdo se účastní, na co se zaměřuje?**

> [!note] = **inspekce/recenze**. Detaily výše v sekci „2. Řízení softwarového vývoje".

- Žebříček formálnosti: prohlídka (walkthrough) → recenze → **inspekce** (nejformálnější, nejúčinnější).
- **Tým:** moderátor · zapisovatel · autor · recenzenti/inspektoři.
- **Zaměření:** hledá se **defekt, ne řeší se** (neopravuje na místě); **přezkušuje se produkt, ne tvůrce**; klidný tón, otázky místo obvinění; výsledky se zaznamenají.
- **Proč brzy:** relativní cena opravy defektu roste s fází — **návrh 1 → kódování 1,5 → testy 3 → systémové testy 10 → provoz 60–100**. Inspekce v raných fázích = největší úspora.

---

### 11. Nasazení, provoz a ITIL

**Otázka (Ošlejšek skoro každý rok, Bühnová, Pitner, Batko, Dohnal): Co je IT služba? Popiš ITIL — 5 fází, co se v každé děje, klíčové procesy a příklad. Kdo je zodpovědný?**

> [!note] Detaily k ITIL výše v sekci „6. Nasazení a provoz". Je to nejčastější otázka „provozní" části napříč lety.

- **Služba:** prostředek dodávání hodnoty zákazníkovi (usnadní výstup, který chce) **bez toho, aby vlastnil náklady a rizika**.
- **ITIL** = best practices pro **správu IT služeb (ITSM)**; řeší _„co je před a po IT projektu"_ — výstup projektu se stává službou, kterou je nutné dál spravovat.
- **5 fází životního cyklu služby** (+ příklad procesu z každé):
    1. **Service Strategy** — strategie a hodnota služby (proces: _Financial / Demand management_).
    2. **Service Design** — návrh služby a **SLA** (proces: **Service Level Management** — kvalitativní i kvantitativní požadavky; + Capacity, Availability).
    3. **Service Transition** — uvedení do provozu = **sem patří nasazení** (proces: _Change management, Release & Deployment, Configuration/CMDB_).
    4. **Service Operation** — běžný provoz (proces: **Incident management**, Problem, Event, Access).
    5. **Continual Service Improvement** — průběžné zlepšování (7-step improvement).
- **Zodpovědnost** (Bühnová): za jednotlivé fáze odpovídají různé role/útvary — strategii vedení/service owner, provoz service desk/operations tým atd.

**Otázka (praktická část nasazení):** otestovat v prostředí blízkém produkčnímu, **logování + monitoring + zpětná vazba**, příprava prostředí (lze automatizovat PaaS/Kubernetes), kontrola (často zastaralé) dokumentace, **školení uživatelů**, případně customizace (drž jako „příklad z praxe").

---

### 12. Údržba a znovupoužitelnost

**Otázka (Plhák 2024: „udržitelnost + znovupoužitelnost").**

- **Údržba** = modifikace po předání zákazníkovi. **Typy:** **korektivní** (chyby), **adaptivní** (změna prostředí/OS/HW, **změna zákonů**, třetí strany), **perfektivní** (vylepšení na žádost), preventivní.
- **Lehmanovy zákony** (doplň do poznámek): _trvalá změna_ · _rostoucí složitost (entropie)_ — opakované změny ničí strukturu, časem se vyplatí přepsat · _samoregulace_ · _invariantní pracovní rychlost_ · _zachování známosti_.
- **Cena:** dobře promyšlená struktura → dražší implementace, ale návratnost v provozu; **údržba je obvykle nejdražší fáze ŽC**.
- **Znovupoužitelnost:** faktor kvality (McCall); formy = knihovny/komponenty, frameworky, **COTS / krabicový (out-of-box) SW**, znovupoužití architektury; v odhadech **RUSE (COCOMO II)** + asimilace cizího modulu (AA, SU). Pointa: **reuse není zadarmo** — obecná komponenta je dražší na návrh, vyplatí se při více použitích.

---

### Mini-checklist před vstupem ke komisi

- [ ] **Metriky:** procesní / produktové / zdrojové + příklad ke každé _(Ráček)_
- [ ] **Cyklomatická složitost:** vzorec `E−N+2`, k čemu, hranice >10 _(Ráček, Spurný)_
- [ ] **FP:** EI/EO/EQ/ILF/EIF + konkrétní příklad ke každému _(Ráček)_
- [ ] **COCOMO:** 3 módy, vzorec, COCOMO II 3 modely _(Ráček)_
- [ ] **Kvalita:** trojúhelník, McCall, metrika udržovatelnosti, klasifikace chyb (severity + ODC) _(Ráček)_
- [ ] **Testování:** V-model, kdo dělá co, verifikace×validace, white/black box, integrace shora/zdola, regresní/smoke/fuzzing _(Ráček, Vojnar, Ročkai)_
- [ ] **Inspekce:** tým, „produkt ne tvůrce", cena chyb po fázích _(Ráček)_
- [ ] **ITIL:** 5 fází + proces + příklad z každé _(Ošlejšek)_
- [ ] **Údržba:** typy + Lehman; **Reuse:** RUSE, COTS, „není zadarmo" _(Plhák)_
- [ ] **Chytáky:** UP fáze = celý projekt; agile pevné=čas+peníze; backlog=PO; agile nacenění=fixed core + T&M tarif