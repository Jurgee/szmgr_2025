# 1 Kvalita kódu

> [!abstract] Kvalita ve vývoji softwarových systémů, atributy kvality a softwarové metriky. Taktiky pro zajištění kvality na úrovni jednotlivých atributů kvality. Principy Clean Code a SOLID, refaktoring kódu. Testování kódu, jednotkové testy, integrační testy, uživatelské a akceptační testy. Ladění a testování výkonu. Proces řízení kvality ve vývoji softwarových systémů. Příklady z praxe.

---

## 1 Kvalita ve vývoji SW, atributy kvality a softwarové metriky

### Co je kvalita softwaru

- **IEEE Std. 610.12-1990:** stupeň, do jaké míry systém/komponenta/proces splňuje (a) specifikované požadavky a (b) potřeby nebo očekávání zákazníka/uživatele.
- **ISO/IEC 9001 (pohled PV260):** schopnost SW produktu vyhovět požadavkům / potřebám zákazníka.
- **Klasický pohled:** každý program _něco_ dělá správně, ale nemusí dělat to, co chceme. Kvalita = dodržení explicitních funkčních požadavků + dokumentovaných standardů + implicitních charakteristik očekávaných od profesionálního SW.
- **Aspekty (nedostatky) kvality:** odchylky od požadavků, nedodržení standardů, odchylky od běžných (implicitních) zvyklostí.

### Iceberg kvality (PV260) — externí vs. interní

- **Externí kvalita (viditelná, „symptomy", pohled uživatele):** usability, accuracy, performance, reliability, security, cost.
- **Interní kvalita (neviditelná, „kořen", pohled vývojáře/manažera):** coding practices, program structure, complexity, testability, flexibility, maintainability, understandability, reusability.
- Klíč: interní vlastnosti (snadno měřitelné, ovlivnitelné vývojářem) určují externí vlastnosti, ale **mapování není přímočaré** (např. reliabilita).
- **Tři pohledy stakeholderů (PA103):**
    - _User Experience (zákazník)_ → Feature: usability, accuracy, reliability, performance, security.
    - _Code Quality (vývojář)_ → Engineering: modularity, complexity, resilience, understandability, testability.
    - _Long-term View (manažer)_ → Adjustability: adaptability, portability, reusability, maintainability, scalability.

### Faktory kvality — McCall et al. (1977)

|Faktor|Význam|
|---|---|
|**Korektnost**|jak program splňuje specifikaci a záměry uživatele|
|**Spolehlivost**|jak přesně plní zamýšlené funkce|
|**Efektivita**|množství potřebných výpočetních prostředků|
|**Integrita**|ochrana před neoprávněným přístupem|
|**Použitelnost**|úsilí na naučení, ovládání, vstup/výstup|
|**Udržovatelnost**|úsilí na nalezení a opravu chyby|
|**Flexibilita**|úsilí na modifikaci|
|**Testovatelnost**|úsilí na ověření funkce|
|**Přenositelnost**|úsilí na přemístění na jiné HW/SW|
|**Znovupoužitelnost**|rozsah opětovného použití částí jinde|
|**Schopnost spolupráce**|úsilí na propojení s jiným systémem|

Kategorie faktorů: **operační charakteristiky** / **schopnost akceptovat změny** / **adaptibilita na nové prostředí**.

### Modely kvality

- **ISO/IEC 9126 → ISO/IEC 25010** — standardní modely kvality produktu.
- **Quality Model (PA017):** product → {operation: reliability, efficiency, usability; revision: maintainability, testability; transition: portability, reusability} → měřeno **metrikami**.
- **Měřitelnost faktorů:** přímo měřitelné (např. #chyb/KLOC/čas) vs. nepřímo měřitelné (použitelnost, udržovatelnost).

### Klasifikace atributů kvality (PA103)- **Performance:** throughput, response time, efektivita využití zdrojů.

- **Reliability:** bezchybný běh, availability, robustness, schopnost zotavení.
- **Security:** confidentiality, integrity, availability (CIA).
- **Scalability:** load, souběžná komunikace, škálování dat.
- **Maintainability:** modifiability, adaptability.

### Funkční vs. nefunkční / extra-funkční požadavky (PA103)

- **Nefunkční:** omezení na technologie (HW/SW kompatibilita, middleware).
- **Extra-funkční:** kvalitativní omezení na implementaci (response time v X % volání, availability X % měsíčně) → plán jejich naplnění je v **architektuře**.
- **QoS (Quality of Service):** anotované rozhraní s informací o kvalitě jednotlivých služeb; problém = těžko garantovat konkrétní čísla, pokud nejsou jasná čísla externích služeb / HW.

### Trojúhelník kvality a odhad úsilí (často zmiňováno komisí)

- **Trojúhelník kvality / „železný trojúhelník" projektu:** **cena (cost) – čas (time) – rozsah (scope)**; kvalita je uprostřed a je výsledkem jejich vyvážení — utáhnu-li jeden vrchol, ovlivním ostatní (např. v agilním vývoji je **pevný čas a peníze**, **proměnlivá funkcionalita**).
- **Odhad úsilí (PA017, zmiňováno spolu s kvalitou):**
    - **COCOMO** (Boehm, 1981) — `#SLOC` jako hlavní indikátor; empiricky `E = a·KSLOC^b` (E = úsilí v člověko-měsících), doba `T = c·E^d`; postup: nominální úsilí Eₙ → korekční faktor F_c → aktuální úsilí E → doba T. **COCOMO II** = novější varianta s více modely.
    - **Funkční body (FP):** předběžný odhad z omezené informace; měří **vstupy (EI), výstupy (EO), dotazy (EQ), vnitřní (ILF) a vnější (EIF) paměti**; princip: `velikost × složitost × rizikové faktory`.
    - **Pozn.:** **MD (člověko-den)** je jednotka **úsilí, ne času**.

### Software metriky

**Měření (Fenton & Pfleeger):** přiřazení čísel/symbolů atributům entit reálného světa podle jasných pravidel.

- _„You cannot manage what you cannot measure"_ (DeMarco). Ale: musíte vědět **co a jak** měřit. Měření musí být **goal-driven**.

**Metriky velikosti**

- **LOC / KLOC** — lineární, ale jazykově a programátorsky závislé; **nikdy** ne jako míra produktivity. Vhodné spíš pro **normalizaci** (např. _comment density_ = CLOC/LOC).

**Metriky složitosti**

- **McCabe Cyclomatic Complexity (CC):** `v(G) = |E| − |N| + 2P` (E=hrany, N=uzly, P=počet nesouvislých částí grafu). Prakticky = počet větvení v kódu + 1 na úrovni metody.
    - Typické rozsahy: **1–4 nízká, 5–7 střední, 8–10 vysoká, 11+ velmi vysoká** (>50 = „untestable").
    - Pozor: měří **syntaktickou**, ne sémantickou složitost (dva fragmenty se stejnou CC mohou být různě srozumitelné).
    - **Vazba na V-model / testování:** CC udává počet **nezávislých cest** → minimální počet testů pro **pokrytí cest**; uplatní se na úrovni **detailního návrhu a unit (white-box) testování** a je mírou **testovatelnosti**.
- **Halstead Software Science:** operátory n1/N1, operandy n2/N2 → objem `V = N · log2 n`.
- **McClure:** `Complexity = C + V` (C = počet porovnání, V = počet řídicích proměnných).
- **Card & Glass — strukturální složitost modulu:** `S(i) = fan_out²(i)`.

**OO metriky — Chidamber & Kemerer (C&K)**

- **WMC** Weighted Methods per Class, **DIT** Depth of Inheritance Tree, **NOC** Number of Children, **CBO** Coupling Between Objects (vysoké = špatné), **RFC** Response For a Class, **LCOM** Lack of Cohesion of Methods (negativní míra soudržnosti).
- **Fan-in / Fan-out** — toky do/z modulu.

**Maintainability Index (MI)**

- `MI = 171 − 5.2·ln(V) − 0.23·CC − 16.2·ln(LOC)` (V = Halstead volume, CC = cyklomatická složitost, LOC).

**Měřicí modely (ISO/IEC 15939):** Attribute → Base Measure → (Measurement Function) → Derived Measure → (Model) → Indicator → interpretace → informace pro rozhodování.

**Pasti měření:** sběr bez významu, neanalyzování čísel, nereálné cíle, _paralysis by analysis_.

> _„Count what is countable. Measure what is measurable. And what is not measurable, make measurable."_ — Galileo

---

## 2 Taktiky pro zajištění kvality na úrovni atributů

> **Roadmap k QE metodám (PV260) — velký obraz:**
> 
> - **Define** quality issues — requirements engineering, atributy kvality, metriky.
> - **Prevent** — clean code, SOLID, design patterns, pair programming, code conventions, QA procesy, standardy (CMMI, ISO 9000, ISO/IEC 25010).
> - **Detect** — (ne)funkční testování, design inspections, code reviews, statická analýza kódu.
> - **Repair** — reliability/performance/security/maintainability **taktiky**.
> - **Keep track** — issue tracking, technical debt management, configuration management.

**Tuning architektury (PA103):** optimalizace zvoleného atributu úpravou architektury.

- Výhody: prověřené techniky pro zvýšení kvality.
- Pozor: optimalizace **není zaručena** (často jen drobná) a hrozí **zhoršení jiných atributů** → taktiky aplikovat s rozumem a znát vzájemné vazby.

> **Bad code smells** existují i pro atributy kvality (jen se hůř „vygooglí"). **Taktiky** jsou definovány na **vyšší úrovni abstrakce** než smells. **Každá taktika pro jeden atribut může být anti-pattern pro jiný** → odtud konflikty.

### Performance

**Smells:** ① Redundant Work (časově náročná metoda se počítá opakovaně → volej jednou, výsledek cachuj) · ② One by One Processing (lineární vyhledávání → chytřejší algoritmy/datové struktury) · ③ Long Critical Section (zbytečný kód v kritické sekci → vytáhnout ven, případně partial locking) · ④ Busy Waiting (opakované dotazování → Hollywood principle „Don't call us, we'll call you", **Observer**).

**Taktiky:** ① Vezmi **profiler** — neháde​j, kde je problém · ② Zkoumej **complexity a frequency** výpočtů (efektivněji / méně často) · ③ **Concurrency** (jen když rozumíš všem důsledkům) · ④ **Řiď zdroje** — load balancing, řízení přístupu, cache, replikace.

### Scalability

**Definice (PA103):** schopnost SW přizpůsobit se novým požadavkům na velikost a rozsah. **Smells (mylné víry):** ① „Distribuce zlepší výkon" (ne nutně — síťové I/O, koherence) · ② „Jen výkon" (přibývá problémů s reliabilitou, security…) · ③ „Framework to vyřeší".

> **Fowlerův první zákon distribuce objektů: „Don't distribute your objects."** Když to jde, zůstaň lokální.

**Taktiky (PA103):** moduly s jasným účelem a srozumitelným rozhraním (přidání/replikace má min. dopad) · **distribuuj datové zdroje** (vyhýbej se bottlenecku) · **identifikuj data vhodná k replikaci**.

- **Horizontal scaling (scale-out)** — přidávat uzly/„servery"; **Vertical scaling (scale-up)** — přidávat výkon stávajícímu HW.

### Reliability

**Smells:** ① Input Kludge (kontroluj všechny vstupy) · ② Blind Faith (nedůvěřuj cizímu kódu ani sobě) · ③ Poorly Handled Exceptions · ④ Unguarded Sequential Coupling (předpoklady o pořadí volání bez kontroly) · ⑤ Fashionable Coding (technologie, kterým nerozumíš).

**Taktiky (PV260):** ① **Monitor** dění (acceptance checking, logging) · ② **Pečlivé zpracování výjimek** · ③ **Fault tolerant** systém (redundance, self-healing, rebinding) · ④ **Restart/recovery** schopnosti.

> Řešíme jen **SW** spolehlivost; nepředpokládáme ultra-spolehlivý systém (jinak n-version programming).

**Taktiky (PA103):** moduly nechť **exponují svůj stav** + state invariants · vhodné **error reporting** (např. výjimky pro post-mortem) · **kontroluj spolehlivost v konektorech** (omezení propagace selhání) · **vyhni se single points of failure** (replikace, dekompozice) · **auto-backup a recovery** kritické funkcionality · **„health state" monitoring**.

### Testability

**Smells:** ① **Global State** (objekty nesmí komunikovat tajně) · ② Lack of **Dependency Injection** · ③ **Law of Demeter** violation (mluv jen s nejbližšími přáteli) · ④ Misplaced & Hard Coded **new** operator (nemíchej factory a service kód).

> V **>90 %** případů je problémem **Global State**. Projevy: flaky testy, záleží na pořadí testů, nelze paralelizovat, skryté stavy (`System.currentTime()`, DB). _Když je kód těžké testovat, neptej se jak ho „obejít", ale co je s ním špatně._

**Taktiky:** ① Piš **CLEAN** kód · ② Vyhni se **global state** (i skrytému) · ③ **Odděl rozhraní od implementace** (záměna při testu) · ④ Udělej **závislosti explicitní** (pomůže i kompilátor) · ⑤ **Odděl factories od business logiky**.

### Maintainability

**Smells:** ① **Early Tuning** (nikdy neobětuj čitelnost předčasné optimalizaci) · ② **Super-Flexibility** („flexibility breeds complexity" — začni jednoduše, flexibilitu přidávej) · ③ **Simple = Stupid, Complex = Smart** (i chytré systémy stojí na jednoduchých strukturách).

**Taktiky (PV260):** ① Piš **CLEAN** kód („premature optimization is the root of all evil") · ② **Připrav se na změnu** (interfaces, dědičnost, polymorfismus, design patterns) · ③ Navrhni **architekturu** pečlivě (modularizace) · ④ Hlídej **závislosti** (Law of Demeter, High Cohesion, Low Coupling).

**Taktiky (PA103):** odděl interakci od funkcionality (interakce do konektorů — Broker, iDesign) · eliminuj zbytečné závislosti (MVC + DI) · hierarchická architektura (více pohledů na různých úrovních abstrakce).

### Security

- Atributy: **confidentiality, integrity, availability**.
- Repair: identifikuj a odstraň zranitelnosti / single points of failure. **Security by Design** — bezpečnost zabudovaná od začátku.

### Konflikty mezi atributy (PV260 / PA103)

> **Bad news: nelze optimalizovat všechno najednou.** Požadavky bývají _contradictory_ i _coincident_. Příklady vazeb: Usability × Security, Performance × Reliability, Performance × Maintainability, Understandability × Maintainability × Portability. → Nutné **trade-offy**.

---

## 3 Clean Code a SOLID, refaktoring

### Clean Code

> _„Clean code is simple and direct. Clean code reads like well-written prose… full of crisp abstractions and straightforward lines of control."_ — Grady Booch _„Clean code always looks like it was written by someone who cares."_ — Michael Feathers (Zdroj: Robert C. Martin, _Clean Code_; _Clean Architecture_.)

- **Naming:** používej doménový jazyk, buď konzistentní (Trial vs. Evaluation), víc business slovníku, míň tech žargonu.
- **Funkce:** obvykle začínají slovesem, odhalují záměr, dělají **jen jednu věc**.
- **DRY (Don't Repeat Yourself):** duplikace = noční můra údržby; odstraň ji (ale ne za každou cenu).
    - **True duplication:** změna v jedné instanci vyžaduje změnu ve všech → odstranit.
    - **False duplication:** kód vypadá stejně, ale vyvíjí se nezávisle z jiného důvodu → nechat.
- Související: **GRASP** (High Cohesion, Low Coupling, Polymorphism), úrovně abstrakce, naming conventions; _„nežij s rozbitými okny" (broken windows)_.

### SOLID

|       | Princip               | Význam                                                                                                                                                          |
| ----- | --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **S** | Single Responsibility | každý modul/třída/funkce má **jedinou odpovědnost** a tu zapouzdřuje → víc menších tříd s úzce zaměřenými metodami                                              |
| **O** | Open/Closed           | artefakt **otevřený rozšíření, uzavřený modifikaci**; řeší se abstrakcemi (interfaces, abstraktní třídy); pozor na předčasné abstrakce                          |
| **L** | Liskov Substitution   | místo instance třídy lze dosadit instanci podtřídy bez narušení vlastností programu; podtřídy nesmí porušit invarianty/omezení báze (Square ≠ podtyp Rectangle) |
| **I** | Interface Segregation | klienti nemají být nuceni záviset na metodách, které nepoužívají → malá, soudržná rozhraní místo „tlustých"                                                     |
| **D** | Dependency Inversion  | **závis na abstrakcích, ne na konkrétnostech**; high-level moduly nemají záviset na low-level, oba na abstrakcích; detaily závisí na abstrakcích, ne naopak     |

> SOLID dává **flexibilní design**, lze aplikovat na libovolné úrovni (třída, modul, služba). Vol správné řešení, ne to snadné.

### Refaktoring (PV260, Lect 3 — Fowler)

- **Definice:** disciplinovaná změna **tvaru** kódu pomocí **malých kroků zachovávajících chování**; velká změna = řetězec malých.
    
    > _„Refactoring changes programs in small steps, so if you make a mistake, it is easy to find the bug."_ — Fowler
    
- **Co NENÍ refaktoring:** ne každý úklid kódu je refaktoring. Fowler: obecně „restructuring"; přepisování = „rewriting".
- **Kdy začít:** když musíš stejně měnit kód (feature/bugfix), ale kód „bojuje" s tebou.
    
    > _„When you have to add a feature but the code is not structured conveniently, first refactor … then add the feature."_ — Fowler _„For each desired change, make the change easy (this may be hard), then make the easy change."_ — Kent Beck
    
- **Kdy přestat:** když kód snadno přijme požadovanou změnu. _„The true test of good code is how easy it is to change it."_
- **Medicínská metafora:** code smells = nemoci, automatizované testy = monitory životních funkcí; programátor diagnostikuje, refaktoruje a sleduje testy.
- **Prominentní refaktoringy:** Extract / Inline / Move Function, Change Function Declaration, Rename Variable/Field, Replace Conditional with Polymorphism, Introduce Parameter Object.

### Code smells (Fowler)

- **Rozpoznání:** některé vidět v jednom souboru/metodě, jiné jen v kontextu více tříd, některé až při pokusu o změnu.
- **Prominentní smells:** Large Class, Long Function, Long Parameter List, Primitive Obsession, Mysterious Name, Repeated Switches, Shotgun Surgery, Comments.
- (Z výzkumu, příklady detekce prahem: God Class, Data Class, Brain Method, Shotgun Surgery, Dispersed Coupling, Message Chains.)

---

## 4 Testování kódu (jednotkové, integrační, uživatelské, akceptační)

### Základní pojmy

- _„Program testing can show the presence of bugs, but never their absence."_ — Dijkstra.
- **Testování:** proces ověření, že systém splňuje specifikované požadavky (IEEE). Ukazuje chyby, funkce, výkon a je **indikátorem kvality**.
- **Verifikace vs. validace (PA017):**
    - **Verifikace** = test proti vnitřní činnosti = _„dělat věci správně"_ (building the product right).
    - **Validace** = test proti specifikovaným funkcím = _„dělat správné věci"_ (building the right product).
- **Pojmy (IEEE 1044-2009):**
    - **Error** = lidská akce produkující nesprávný výsledek.
    - **Fault / Defect / Bug** = imperfekce v artefaktu, která nesplňuje požadavky.
    - **Failure** = ukončení schopnosti produktu plnit funkci / vybočení z mezí.
- **Test Oracle Problem:** jak mechanicky určit, zda je výstup pro daný vstup správný.

### V-model (PA017)

Levá větev (vývoj) ↔ pravá větev (testování), spojené horizontálně:

- Project Requirements ↔ Production/Operations & Maintenance
- Product Requirements & Spec Analysis ↔ System & Acceptance Testing
- (Non-functional Requirements ↔ Load & Performance Test)
- High-Level Design ↔ Integration & Testing
- (User Interface Design ↔ Usability Test)
- Detailed Design ↔ Unit Testing
- ↓ Coding ↑

![[v-model.webp]]

### Úrovně testování (Roadmap PV260 + PA017)

- **Unit / Component** → funkční test
- **System & Integration** → funkční, nefunkční, regresní
- **Release test** → použitelnost, learnability
- **Acceptance (User) test** → akceptační kritéria, požadavky
- Průřezově: **performance** (response time, stress), **security** (penetration, red team).

> **Kdo dělá které testy (časté na komisi):** **unit + integrační** testy píší **vývojáři**; **systémové / výkonnostní / security** testy dělají **testeři / QA**; **akceptační (uživatelské)** testy provádí / schvaluje **zákazník / uživatel**.

#### Unit testing

- Třída/jednotka testovaná **izolovaně**. Testy mají být: **Fast, Simple, Readable, Deterministic, bez duplikace logiky, součást buildu, konzistentní pojmenování, používat test doubles**.
- **AAA pattern:** **Arrange** (nastav podmínky) → **Act** (spusť testovaný kód) → **Assert** (ověř chování).
- **Kdo a kdy (PA017):** testují **vývojáři**, testy se programují ve **stejném jazyce** jako moduly (alt. název „testovací drivery"), seskupují se do **kolekcí testů (test suites)**, probíhají **postupně během vývoje** po dokončení modulů.
- Typicky **white box** („bílá skříňka"), někdy ale i black box.

#### Test Doubles (Meszaros, _xUnit Test Patterns_)

|Typ|Popis|
|---|---|
|**Dummy**|předávané, ale nepoužité (výplň parametrů)|
|**Fake**|funkční implementace nevhodná do produkce (in-memory DB)|
|**Stub**|dává předpřipravené odpovědi na volání v testu|
|**Spy**|stub, který navíc zaznamenává, jak byl volán|
|**Mock**|předprogramovaný očekáváními = specifikace očekávaných volání|

#### Integration testing

- Cíl: ověřit, že **samostatně vyvinuté moduly fungují spolu**.
- Na rozdíl od unit testů používá **externí závislosti**, ověřuje více modulů najednou → **pomalejší a složitější**.
- **Integrační postupy (PA017):**
    - **Shora dolů (top-down):** nejprve se implementuje **jádro/kostra** systému (minimální „skořápka"), neúplné části se nahradí **stuby (pahýly/protézy)** — náhražkové objekty se shodným rozhraním, postupně nahrazované skutečnými moduly. Odhaluje **chyby analýzy a návrhu**, je slučitelné s prototypováním.
    - **Zdola nahoru (bottom-up):** začíná **jednotlivými moduly** (po unit testech), kombinuje je do **subsystémů** a ty do celku; používá **drivery** — nadřazené řídicí objekty volající testovaný modul.

#### System testing (validace celého integrovaného systému)

- **Usability**, **Load/Stress**, **Performance**, **Functional**, **Security**. Dále: installation/deployment, documentation, migration testing.

#### Acceptance (User) testing

- Validace proti **akceptačním kritériím** a požadavkům uživatele; rozhoduje o přijetí systému. Na pravém vrcholu V-modelu spolu se system testem.

#### Regression / Smoke / Sanity / Exploratory

- **Regression** — žádná změna nezpůsobila nové (ani znovuobjevené) defekty; **průřezový** koncept napříč úrovněmi.
- **Smoke** — kontrola kritických funkcí nové **nestabilní** buildy; selže → build zamítnut.
- **Sanity** — ověření nové **stabilní** buildy pro pokračování dalším testováním; cíl chytit problémy co nejdřív.
- **Exploratory** — semi-manuální učení + návrh + provádění; freestyle / strategy-based / scenario-based. _„Discovering the unexpected is more important than confirming the known."_

### Techniky návrhu testů

- **Black box** (proti specifikaci): equivalence partitioning, **boundary value analysis**.
- **White box / „bílá skříňka"** (zohledňuje strukturu programu): pokrytí příkazů, cest průchodu kódem.

### Code coverage

- **Druhy:** statement, function, branch, condition coverage.
- 100 % vyžaduje TDD, ale **není feasible** kvůli nákladům (vývoj s testy 2–4× dražší). **100 % ≠ bezchybný SW** (pokryta jen malá část stavů).
- **Pareto:** ~80 % defektů v ~20 % kódu; právě těch 20 % je hůř pokrytelné.
    
    > Fowler: pokrytí je užitečné pro nalezení **nepokrytých** částí, ale je špatné jako **číselné** vyjádření kvality testů.
    
- Lepší míra kvality testů: **Mutation testing** — vkládají se umělé defekty (mutanty); kolik jich testy „zabijí".

### Pokročilé techniky (PV260)

- **Fuzzing** — injektování nevalidních/malformovaných vstupů k odhalení pádů a zranitelností (buffer overflow).
- **Metamorphic testing** — testy z **relací mezi vstupy a výstupy** (řeší oracle problem). Příklad: nejkratší cesta `|P(G,a,b)| = |P(G,b,a)|`.

### Test Driven Development (TDD)

- Cyklus: **① napiš failující test → ② napiš kód, aby prošel → ③ refaktoruj** (red-green-refactor).
- Vlastnosti testů (FIRST): **Fast, Independent, Repeatable, Self-checking, Timely** (test dřív než implementace).

---

## 5 Ladění a testování výkonu

### Profiling vs. benchmarking vs. microbenchmarking (PV260, Lect 7)

- **Profiling:** _co_ je pomalé/rychlé (dotTrace, dotMemory, ANTS, PerfView, VS Profiling Tools).
- **Benchmarking:** _jak dlouho_ to trvá — metriky; porovnání stejný SW / různý HW, různé verze SW.
- **Microbenchmarking:** měření malého konkrétního kusu kódu.
- **Nástroje:** raději existující — BenchmarkDotNet, NBench, MeasureIt, MS Xunit Performance, Etimo.Benchmarks.

### Proč je (micro)benchmarking těžký

- Přesnost a latence měření; **warmup/cleanup**; (ne)optimalizovaný build; (ne)připojený debugger.
- Optimalizace měřeného kódu (inlining, hoisting, lowering, unrolling), **JIT**, **GC**, tiered compilation, PGO.
- **Izolace prostředí** (power plan), sběr a vyhodnocení dat (multimodální rozdělení, outliers).
- _PerfLab (MS):_ 35 TB dat, reporty/dashboardy, auto-filing, triage, validace výkonu v PR.

### Klíčové výkonnostní metriky (PV260, cloud — Lect 8)

- **Response Time** = latence při zpracování business transakce = komunikační latence + doba zpracování požadavku (sčítá se přes vrstvy: web server → DB → blob storage).
- **Throughput** = množství práce za jednotku času (transakce/operace/requesty za s).
    - Silně závisí na propustnosti zapojených komponent → **včas identifikuj bottleneck**.
    - Rozlišuj **průměrný vs. peak** throughput.
    - Pozor na **násobení požadavků** (1 request → N dotazů do DB) a limity (Azure SQL ~1000 req/s).

### Výkonnostní testy (System testing)

- **Load testing** — chování při očekávané zátěži.
- **Stress testing** — chování při těžké/extrémní zátěži (hledání mezí).

### Vztah k taktikám/smells pro výkon

- Viz [[#Performance]] v sekci 2 — profiler první, complexity/frequency, concurrency, řízení zdrojů; smells Redundant Work / One-by-One / Long Critical Section / Busy Waiting.

---

## 6 Proces řízení kvality ve vývoji SW

### SQA (Software Quality Assurance) — principy (PA017)

- Definovaná a dokumentovaná **politika kvality**.
- Definice **zodpovědností, autorit a vztahů** všech, kdo ovlivňují kvalitu.
- Dokumentované **postupy a instrukce** pro kvalitu.
- **Efektivní implementace** systému na všech úrovních organizace.
- **Záznam všech SQA aktivit**.

### CMM — Capability Maturity Model (SEI, Carnegie-Mellon) (PA017)

|Úroveň|Název|Charakteristika|
|---|---|---|
|1|Výchozí|chaotický proces, nepředvídatelná cena/plán/kvalita|
|2|Opakovatelný|intuitivní; řízené požadavky, plánování projektu, řízené subkontrakty, SQA, konfigurace|
|3|Definovaný|orientace na kvalitu; def. org. procesu, školení, inženýrské metody, prověrky/oponentury|
|4|Řízený|kvantitativní, statisticky řízená kvalita; měření a kvantitativní řízení procesu|
|5|Optimalizující|prevence chyb, inovace technologií, řízené změny výrobních procesů|

- Globální hodnocení výroby: vyspělost = **CMM**, systémy kvality = **ISO 9001**, ocenění kvality = **MBNQA**.

### Standardy

- **ISO 9001 / 9000-3 / 9004** — systémy kvality (vývoj/dodávka SW).
- **ISO/IEC 25010 (resp. 9126)** — model kvality produktu.
- **IEEE:** 730 (SQA Plans), 983 (SQA), 1028 (reviews & audits), 1012 (verifikace a validace), 1074 (life cycle processes).
- **CMMI a ITIL** — referenční modely pro zlepšování procesu (PV260).

### Detekce: inspekce, recenze, prohlídky (PA017)

- **Review techniky** dle formálnosti/náročnosti:
    - **Inspekce** — vysoce formální, cílem účinné zjištění a odstranění **všech** defektů.
    - **Recenze (odborné)** — formální, představení/zjištění požadavků, rozlišení nejednoznačností, školení.
    - **Prohlídky (walkthrough)** — neformální, žádné měření, minimální náročnost, krátká doba, školení vývojářů.
- **Cíle formálního přezkoušení:** odhalit chyby ve funkci/logice/implementaci; ověřit splnění požadavků; dodržení standardů; jednotný vývoj; zvýšit řiditelnost projektů.
- **Tým:** vedoucí týmu (**moderátor**), **zapisovatel**, **autor**, **recenzenti/inspektoři**.
- **Příprava inspektora:** rozumět kontextu, projít materiály, dělat poznámky, formulovat **otázky**, vyhnout se hodnocení stylu.
- **Provedení:** přezkušuj **produkt, ne tvůrce**; klidný tón; uváděj problémy, **neřeš je**; nediskutuj styl, jen tech. správnost; plánuj jako úlohy projektu; zaznamenej výsledky.
- **Formulář:** filtruje nepodstatné problémy předem, šetří čas na schůzce, umožňuje statistiky a měřitelnost efektivity.

### Závažnost defektů (PA017)

- **Kritické** — pád systému / narušení dat, není známá cesta k vyhnutí.
- **Vážné** — chybné výstupy, je známá cesta k vyhnutí, zasažena významná část.
- **Středně závažné** — omezená funkcionalita, lze se vyhnout/ignorovat.
- **Málo závažné** — lze opominout bez narušení funkčnosti.

### Klasifikace defektů — IBM ODC (Orthogonal Defect Classification) (PA017)

Typy: **Funkce, Rozhraní, Ověřování, Přiřazení, Časování/serializace, Sestavení/balení/spojování, Dokumentace, Algoritmus.** (Programátor volí typ z toho, jak defekt nakonec opravil; vždy se rozlišuje „něco chybí" vs. „něco je špatně".)

### Vývojový proces revisited (PV260 / Mews, Lect 5)

- **Analyze:** žádné automatizované testy, kvalita přes review dokumentů.
- **Develop:** všechny druhy automatizovaných testů, code review, statická analýza, CI/CD.
- **Test:** integrační/akceptační, nefunkční, výkonnostní.
- **Deploy:** ověření omezení (legal approval), ověření úspěchu nasazení.
- Historie testování: žádné testy → switche pro testovatelnost → automatizované unit/integrační → TDD → continuous testing.

### Continuous Integration / Delivery (PV260, Lect 5)

- Kompletní vývojová a verifikační **pipeline jedním nástrojem** (uspořádaný seznam akcí), měření zdraví projektu, **„always green"** (něco je špatně → stop), integrace externích nástrojů (jazyky, source control, build engines, package repos, issue trackery, notifikace).
- **Kroky pipeline:** Build/Compile → Static analysis (coverage, coding rules, issues, duplicities) → Test → Package (download/create/publish) → Deploy.
- **CI metriky:** **Success rate** (% failed builds), **Time to fix test**, **Average time to market**; trendy zdraví: coverage ↑, duplicities ↓, issues ↓, rule violations ↓.

---

## 7 Shrnutí a příklady z praxe

### Provázání předmětů

- **PV260** — code-level kvalita: clean code, smells, refaktoring, testování a metriky, automatizace/CI, performance engineering, taktiky a konflikty atributů.
- **PA017** — procesní/manažerská kvalita: definice kvality, McCall, CMM, ISO/IEEE, inspekce a recenze, klasifikace chyb, metriky, V-model.
- **PA103** — OO design pro kvalitu: SOLID, atributy kvality, QoS, tuning architektury, taktiky (reliability, scalability, maintainability), komponenty (black/grey/white-box).

### Příklady z praxe (z přednášek)

- **Apple SSL „goto fail" (2012–2014):** chybějící/zdvojený řádek v kontrole certifikátu — defekt typu **kontrola toku** nalezitelný **code review**; nedetekován ~1,5 roku.
- **Apache memory leak (v2.0.48):** chybějící `SSL_free` → pomalý leak (v běžném provozu) / rychlý při DoS; bez zjevné chyby.
- **Toyota Camry (unintended acceleration):** „control-flow spaghetti", **CC > 50** = untestable, throttle funkce **CC > 100** = unmaintainable — ukázka **metrik složitosti** v praxi.
- **Cloud (Mews/Azure):** násobení požadavků (1 request → 10 DB volání) naráží na limit Azure SQL (~1000 req/s) → nutné měřit throughput a hledat bottleneck.
- **Global State / Singletons (Hevery):** příčina flaky a neparalelizovatelných testů — praktický důvod pro **DI** a testovatelný design.
- **Square vs. Rectangle:** klasický příklad porušení **LSP**.
- **Observer pattern** jako řešení smellu **Busy Waiting** („Hollywood principle").

### Vzorové zkratky (rychlá tahák tabulka)

|Zkratka|Význam|
|---|---|
|CC / MCC|McCabe Cyclomatic Complexity|
|LOC / KLOC|Lines / kilo-lines of code|
|WMC, DIT, NOC, CBO, RFC, LCOM|C&K OO metriky|
|AAA|Arrange-Act-Assert|
|TDD|Test Driven Development|
|CI/CD|Continuous Integration / Delivery|
|SQA|Software Quality Assurance|
|CMM(I)|Capability Maturity Model (Integration)|
|ODC|Orthogonal Defect Classification|
|QoS|Quality of Service|
|CIA|Confidentiality, Integrity, Availability|
|MI|Maintainability Index|

### Klíčové myšlenky na ústní zkoušku

1. Kvalita = interní vlastnosti (měřitelné, ovlivnitelné) → externí vlastnosti (viditelné uživateli); atributy se **navzájem ovlivňují a jsou v konfliktu**.
2. **Smell × Taktika:** smell = lokální zápach kódu; taktika = vyšší úroveň abstrakce; taktika pro jeden atribut může být anti-pattern pro jiný.
3. **Clean Code + SOLID + refaktoring** = prevence problémů s udržovatelností a testovatelností.
4. **Pyramida/úrovně testů** (unit → integration → system → acceptance) + verifikace vs. validace + V-model.
5. **Coverage ≠ kvalita testů**; mutation testing je lepší míra.
6. **Profiluj, neháde​j**; měření výkonu je statisticky náročné (warmup, JIT, GC, outliers).
7. **Proces:** Define → Prevent → Detect (review/inspekce/statická analýza/testy) → Repair (taktiky) → Keep track; podpořeno CMM/ISO a CI/CD.

---

## 8 Q&A — otázky reálné komise s odpověďmi

> Sestaveno z databáze otázek, které u okruhu **Kvalita kódu** (SWE/DEV) reálně padaly (Lexa, Batko, Ráček, Bühnová, Vojnar, Švenda, Plhák, Macák…). Čísla otázek nesedí napříč roky, témata ano.

### Q1. Co je kvalita SW a jak bys ji měřil? (Ráček, Bühnová)

Kvalita = míra, do jaké systém splňuje **specifikované požadavky** a **potřeby/očekávání uživatele** (IEEE 610.12). Rozlišujeme **interní** kvalitu (struktura, složitost, testovatelnost — měřitelná, ovlivnitelná vývojářem) a **externí** (usability, reliability, performance — viditelná uživateli); interní určuje externí. Měřit lze přímo (#chyb/KLOC) i nepřímo (udržovatelnost). Měření musí být **goal-driven** („You cannot manage what you cannot measure"). → viz [[#1 Kvalita ve vývoji SW, atributy kvality a softwarové metriky]].

### Q2. Trojúhelník kvality (Ráček)

Projektový „**železný trojúhelník**": **cena – čas – rozsah**, kvalita je výsledkem jejich vyvážení; změna jednoho vrcholu ovlivní ostatní. V agilním vývoji bývá **pevný čas a rozpočet**, **proměnlivá funkcionalita**.

### Q3. Faktory kvality + příklad metriky udržovatelnosti (Ráček)

Faktory McCall (korektnost, spolehlivost, efektivita, integrita, použitelnost, udržovatelnost, flexibilita, testovatelnost, přenositelnost, znovupoužitelnost, spolupráce). Příklad metriky udržovatelnosti: **Maintainability Index** `MI = 171 − 5.2·ln(V) − 0.23·CC − 16.2·ln(LOC)` (V = Halstead volume, CC = cyklomatická složitost). Souvisí i LCOM, CBO (nízká soudržnost / vysoké provázání = horší údržba).

### Q4. Cyklomatická složitost — co to je, vzorec, k čemu, kdy/kde? (Ráček, Batko — velmi časté)

**McCabe CC:** `v(G) = |E| − |N| + 2P` = počet větvení + 1 na úrovni metody. Udává počet **nezávislých cest** → **minimální počet testů pro pokrytí cest**; je mírou **testovatelnosti** a uplatní se u **detailního návrhu a unit (white-box) testování**. Rozsahy: 1–4 nízká … 11+ velmi vysoká, >50 prakticky neotestovatelná. Pozor: měří **syntaktickou**, ne sémantickou složitost. (Komise často nadiktuje kus kódu a chce CC spočítat.)

### Q5. Jiná metrika založená na složitosti (Ráček, Batko)

**Halstead** (objem `V = N·log2 n` z operátorů/operandů), **McClure** (`C + V` = porovnání + řídicí proměnné), **Card & Glass** strukturální složitost `S(i) = fan_out²`. OO: WMC, DIT, NOC, CBO, RFC, LCOM.

### Q6. V-model a validace vs. verifikace (Ráček, Batko, Spurný)

**V-model:** levá větev (požadavky → high-level → detailní návrh → kód) zrcadlí pravou (unit → integrace → systémový → akceptační test). **Verifikace** = „děláme věci správně" (test proti vnitřní činnosti/návrhu). **Validace** = „děláme správné věci" (test proti specifikaci/potřebám uživatele). CC se aplikuje na úrovni **unit testů** (white-box). → viz [[#V-model (PA017)]].

### Q7. Jaké znáš typy testů a kdo je dělá? (Vojnar, Švenda)

**Unit** (jednotka izolovaně, white-box, píší **vývojáři**) → **integrační** (spolupráce modulů s externími závislostmi, **vývojáři**) → **systémové** (validace celku — usability, load/stress, performance, security; **testeři/QA**) → **akceptační/uživatelské** (proti akceptačním kritériím, schvaluje **zákazník/uživatel**). Plus **regresní, smoke, sanity, exploratory, fuzzing**. → viz [[#Úrovně testování (Roadmap PV260 + PA017)]].

### Q8. White box vs. black box (Vojnar, Švenda)

**White box** („bílá skříňka") zohledňuje **strukturu** programu (pokrytí příkazů/cest) — typicky unit testy. **Black box** testuje proti **specifikaci** bez znalosti vnitřku (equivalence partitioning, boundary value analysis).

### Q9. Popiš unit test (Švenda, Batko)

Testuje jednotku **izolovaně**; vlastnosti FIRST (Fast, Independent, Repeatable, Self-checking, Timely). Vzor **AAA** (Arrange–Act–Assert). Píší vývojáři ve stejném jazyce, seskupují do test suites. Závislosti se nahrazují **test doubles** (dummy, fake, **stub**, spy, **mock**).

### Q10. Integrační test — cíl a jak bys ho implementoval? (Ráček, Švenda)

Cíl: ověřit, že **samostatně vyvinuté moduly fungují spolu**; používá **reálné externí závislosti** nebo **mock objekty**, je pomalejší než unit. **Postupy:** **top-down** (nejdřív jádro, neúplné části = **stuby**; odhaluje chyby návrhu, jde s prototypováním) a **bottom-up** (od modulů přes subsystémy k celku, řízeno **drivery**).

### Q11. Co obnáší systémový test? (Ráček)

Validace **kompletního integrovaného systému** proti požadavkům: usability, **load/stress**, performance, functional, security testing (+ instalační, dokumentační, migrační). Dělají testeři/QA.

### Q12. Akceptační (uživatelský) test (Vojnar)

Validace proti **akceptačním kritériím** a požadavkům uživatele; rozhoduje o **přijetí** systému. Provádí/schvaluje **zákazník**. Na pravém vrcholu V-modelu.

### Q13. Jiné způsoby testování (Vojnar)

**Regresní** (nové změny nezpůsobily nové/staré defekty — průřezové), **smoke** (kritické funkce nestabilní buildy, selže → zamítnuto), **sanity** (ověření stabilní buildy), **exploratory** (semi-manuální učení+návrh+běh), **fuzzing** (nevalidní/náhodné vstupy → pády, zranitelnosti), **mutation testing** (vkládání umělých defektů — míra kvality testů).

### Q14. Code coverage — co to je, je to míra kvality testů? (Lexa)

Procento kódu „pokrytého" testy; druhy: **statement, branch, condition, function**. 100 % vyžaduje TDD, ale není feasible (2–4× dražší) a **100 % ≠ bezchybný SW**. Dle Fowlera je dobré pro hledání **nepokrytých** míst, ale **špatné jako číselná míra kvality testů** (Pareto: 80 % chyb ve 20 % kódu). Lepší míra = **mutation testing**.

### Q15. Clean Code a SOLID (Batko)

Clean code = jednoduchý, čte se jako próza, odhaluje záměr; naming v doménovém jazyce, funkce dělají jednu věc, **DRY**. **SOLID** = Single Responsibility, Open/Closed, Liskov Substitution, Interface Segregation, Dependency Inversion (závis na abstrakcích). → viz [[#3 Clean Code a SOLID, refaktoring]].

### Q16. Refaktoring — co to je a kdy ho děláš? (Batko)

**Disciplinovaná změna tvaru kódu malými kroky zachovávajícími chování.** Začínáš, když musíš stejně měnit kód (feature/bugfix) a struktura ti brání („first refactor, then add the feature" — Fowler), přestáváš, když kód změnu snadno přijme. Hlídají to **automatizované testy**. Příklady: Extract Function, Rename, Replace Conditional with Polymorphism.

### Q17. Jak klasifikujeme chyby? (Ráček)

**Pojmy:** error (lidský omyl) → fault/defect/bug (vada v kódu) → failure (selhání běhu). **Podle závažnosti:** kritické / vážné / středně závažné / málo závažné. **IBM ODC (orthogonal defect classification):** Funkce, Rozhraní, Ověřování, Přiřazení, Časování/serializace, Sestavení/balení, Dokumentace, Algoritmus. → viz [[#Klasifikace defektů — IBM ODC (Orthogonal Defect Classification) (PA017)]].

### Q18. Jak probíhá komisionální kontrola během vývoje, kdo a na co? (Ráček)

**Inspekce/recenze/prohlídky.** Tým: **moderátor (vedoucí), zapisovatel, autor, recenzenti/inspektoři**. Zaměřují se na **odhalení defektů** ve funkci/logice/implementaci, dodržení požadavků a standardů; přezkušuje se **produkt, ne autor**, problémy se **uvádějí, neřeší**, využívá se **formulář**. Inspekce = nejformálnější, prohlídka = neformální. → viz [[#Detekce: inspekce, recenze, prohlídky (PA017)]].

### Q19. Ladění a testování výkonu — jak monitorovat, testovat a řešit perf issues? (Bühnová, Batko)

**Profiling** (co je pomalé — dotTrace, PerfView) vs. **benchmarking** (jak dlouho — BenchmarkDotNet) vs. **microbenchmarking**. Metriky: **response time** (latence), **throughput** (práce/čas), hledání **bottlenecku**. Výkonnostní testy: **load** a **stress**. Měření je statisticky náročné (warmup, JIT, GC, outliers). **Řešení issues:** napřed **profiluj, nehádej**; pak sniž **complexity/frequency** výpočtů, použij **concurrency**, **řiď zdroje** (cache, load balancing, replikace); pozor na perf smells (Redundant Work, One-by-One, Long Critical Section, Busy Waiting). → viz [[#5 Ladění a testování výkonu]].

### Q20. Odhad úsilí (občas k „kvalitě SW") (Bühnová, Ráček)

**COCOMO** (Boehm): `E = a·KSLOC^b`, doba `T = c·E^d`, postup Eₙ → F_c → E → T; **COCOMO II** novější. **Funkční body (FP):** měří EI/EO/EQ/ILF/EIF, `velikost × složitost × rizikové faktory`. **MD = jednotka úsilí, ne času.** **LOC** se na produktivitu **nepoužívá**.