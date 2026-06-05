
# DEV 6 Uživatelská rozhraní

> [!abstract] **Principy návrhu a vývoje uživatelského rozhraní v moderních softwarových systémech, vč. webových, mobilních. Proces vývoje uživatelského rozhraní a zásady kvality. User experience (UX), interaction design, prototypování, wireframování, uživatelský výzkum, testování použitelnosti. Technologie a nástroje. Příklady z praxe pro vše výše uvedené.** (PV252 || PV247 || PV278 || PV182)

> [!tip] Jak na to u komise (shrnutí ze zkušeností studentů)
> 
> - Komise (Ošlejšek, Batko, Bártek, Oujezský, Spurný) **nechce „bodový výčet z hlavy"** — chce, abys uměl/a o tématu **mluvit vlastními slovy**, propojit s vlastní diplomkou a uvést **příklady**.
> - Nejčastěji padá: **co je UX vs. UI**, **principy návrhu (CRAP)**, **fáze vývoje UI**, **prototyp vs. wireframe**, **user research**, **kvalitativní vs. kvantitativní testování**, **rozdíly desktop/mobil/web**, **SUS skóre**, **A/B testování**, **eye-tracking/heatmapy**.
> - Klíčové slovo „**iteративnost**" — návrh UI je cyklický proces, ne lineární.

---

## 1) Základy — UX vs UI

### User Experience (UX)

**UX = jak uživatel interaguje s produktem / systémem / službou (i firmou) a jak ji prožívá.** Zahrnuje vnímání _užitečnosti, snadnosti použití a efektivity_, ale i **všechny emoce a pocity před, během a po** kontaktu s produktem.

- **UX design** = proces tvorby _end-to-end_ zkušenosti mezi uživatelem a produktem.
- UX je širší než „obrazovka" — patří sem i obal, zákaznická podpora, čekání na doručení, pocit při reklamaci.
- Mantra: **„Don't make me think!"** — dobrý produkt nenutí uživatele přemýšlet, jak ho ovládat.
- _„Everything is designed. Few things are designed well."_ (Brian Reed) — všechno je navržené, ale málo věcí dobře.

### User Interface (UI)

**UI = prostor, kde dochází k interakci mezi člověkem a strojem.** Konkrétní vizuální a interaktivní vrstva: tlačítka, formuláře, ikony, barvy, typografie, layout.

### Rozdíl UX × UI (oblíbená otázka)

UI je **„špička ledovce"** — to viditelné. UX je **celá masa pod hladinou** — to, co rozhoduje o úspěchu.

| |**UI**|**UX**|
|---|---|---|
|Co řeší|vzhled a ovládací prvky|celkový prožitek a smysluplnost|
|Příklady činností|vizuální design, typografie, ikony, barvy|výzkum, IA, interaction design, testování|
|Otázka|„Vypadá to dobře a dá se to ovládat?"|„Vyřešilo to uživatelův problém?"|
|Pozn.|UI je **součást** UX|UX je nadřazené|

### UX Honeycomb (Peter Morville)

Sedm faset hodnotné zkušenosti. Středobod = **Valuable** (hodnotné).
- **Useful** — užitečné, plní reálnou potřebu
- **Usable** — snadno použitelné
- **Desirable** — žádoucí, emočně přitažlivé (estetika, branding)
- **Findable** — nalezitelné, dobrá navigace/IA
- **Accessible** — přístupné i lidem s hendikepem (a11y)
- **Credible** — důvěryhodné
- **Valuable** — hodnotné pro uživatele i byznys

### UX role / profese (pro kontext)

Doing: UX/UI Designer, UX Researcher, Interaction Designer, Visual Designer, UX Writer, Product Designer, Content Strategist, UX Engineer, Service Designer, Motion Designer. Leading: UX Manager, UX Strategist, UX Director, UX Product Manager.

---

## 2) Principy návrhu UI

### CRAP principy (komise se ptá explicitně!)

Čtyři základní principy vizuálního designu (Robin Williams). U komise stačí popsat **vlastními slovy**:

- **C — Contrast (kontrast):** odlišuj prvky, které se liší (barva, velikost, tučnost). Vytváří vizuální hierarchii a přitahuje pozornost k důležitému.
- **R — Repetition (opakování):** opakuj vizuální prvky (barvy, fonty, styly tlačítek) → konzistence a jednota napříč rozhraním.
- **A — Alignment (zarovnání):** nic neumísťuj „náhodně"; vše má mít vizuální spojení s něčím dalším → uspořádanost, čistota.
- **P — Proximity (blízkost):** související prvky umísti k sobě → vznikají logické skupiny, snižuje se kognitivní zátěž.

### „Dobrý design" — 10 principů (Dieter Rams)

Dobrý design je: **inovativní · činí produkt užitečným · estetický · srozumitelný · nevtíravý (unobtrusive) · poctivý (honest) · dlouhotrvající · důsledný do detailu · šetrný k prostředí · co nejméně designu** (_„as little design as possible"_).

### Další osvědčené zásady (obecná znalost — dobré zmínit)

- **Konzistence** (vnitřní i s konvencemi platformy).
- **Zpětná vazba** — systém vždy dá najevo, co se děje (loading, potvrzení).
- **Prevence chyb** > hlášení chyb.
- **Rozpoznání místo vzpomínání** (recognition over recall).
- **Affordance & signifiers** (Norman) — prvek napovídá, jak se používá.
- **Mapping** — logický vztah ovládání ↔ efekt.
- **Hickův zákon** — víc voleb = delší rozhodování; **Fittsův zákon** — větší/bližší cíl = rychlejší zásah.

---

## 3) Zásady kvality a použitelnosti

### 5 atributů použitelnosti (Jakob Nielsen)

> [!quote] Definice použitelnosti Použitelnost = jak snadno a příjemně se rozhraní používá. Měří se 5 atributy:

1. **Learnability (naučitelnost):** jak snadno zvládne uživatel základní úkoly **napoprvé**?
2. **Efficiency (efektivita):** jak rychle pak úkoly provádí, když se to naučil?
3. **Memorability (zapamatovatelnost):** jak snadno se vrátí k plné zdatnosti po pauze?
4. **Errors (chyby):** kolik chyb dělá, jak vážné jsou a jak snadno se z nich zotaví?
5. **Satisfaction (spokojenost):** jak příjemné je rozhraní používat?

> [!note] Pozn.: **Utility** (dělá to, co potřebuju) + **Usability** (dá se to dobře používat) = **Usefulness** (užitečnost).

### 10 Nielsenových heuristik (obecná znalost — bonus)

Viditelnost stavu systému · shoda systému s reálným světem · uživatelská kontrola a svoboda (undo) · konzistence a standardy · prevence chyb · rozpoznání místo vzpomínání · flexibilita a efektivita · estetický a minimalistický design · pomoc s rozpoznáním a zotavením z chyb · nápověda a dokumentace.

---

## 4) Proces vývoje UI — Design thinking & UCD

### Design Thinking

Přístup k **praktickému a kreativnímu řešení problémů**; lze aplikovat na jakýkoli obor (nejen design). Je **extrémně user-centric** — pochopit potřeby lidí a najít efektivní řešení.

**5 fází (Stanford d.school):**
1. **Empathize** — _Understanding people_: poznat uživatele (rozhovory, pozorování).
2. **Define** — _Figuring out the problem_: definovat skutečný problém / „point of view".
3. **Ideate** — _Generating ideas_: generovat co nejvíc nápadů (brainstorming „go crazy, go wild").
4. **Prototype** — _Creation & experimentation_: rychlé levné prototypy.
5. **Test** — _Refining the product_: testovat s uživateli, sbírat zpětnou vazbu, **iterovat**.

> [!important] Klíčové: proces je **iterativní a nelineární** — po testu se vracíme, předefinováváme problém, vylepšujeme prototyp.

### User-Centered Design (UCD) — průnik 3 hledisek (IDEO)

Dobré (a inovativní) řešení leží v **průniku** tří kruhů:
- **Desirability (People)** — chtějí to lidé? (UX hledisko)
- **Viability (Business)** — dává to byznysový smysl, je to udržitelné?
- **Feasibility (Technology)** — jsme to technicky schopni postavit?

### Double Diamond (obecná znalost — častý model procesu)

Dvě „diamantové" fáze divergence/konvergence:

1. **Discover → Define** (najít _správný problém_),
2. **Develop → Deliver** (najít _správné řešení_).

### Cyklus / fáze vývoje UI (komise: „postup vývoje UI")

Praktický zřetězený postup, který stačí umět popsat:

1. **Výzkum a analýza** — user research, definice cílové skupiny, požadavky, IA.
2. **Návrh konceptu** — user flows, sketche, **wireframy**.
3. **Prototypování** — interaktivní **prototyp** (low-fi → hi-fi).
4. **Vizuální design** — UI kit, barvy, typografie, komponenty.
5. **Testování použitelnosti** — s reálnými uživateli, iterace.
6. **Implementace** — vývoj front-endu.
7. **Nasazení, měření, údržba** — analytika, A/B testy, další iterace.

---

## 5) Uživatelský výzkum (user research)

> [!info] Proč Cílem je **pochopit uživatele, jeho potřeby, chování a kontext** dřív, než začneme navrhovat. Šetří náklady (chybu odhalíme dřív).

### Metody (komise zmiňuje interview, pozorování, „mluvení nahlas", prototypy)

- **Interview (rozhovor)** — strukturované / polostrukturované / nestrukturované.
- **Pozorování (observation)** — sledování uživatele v reálném kontextu; _contextual inquiry_.
- **Think-aloud („mluvení nahlas")** — uživatel během úkolu nahlas komentuje, co dělá a co čeká → odhaluje mentální model a třecí body.
- **Dotazníky / surveys** — škálovatelné, kvantitativní.
- **Card sorting** — pro návrh informační architektury.
- **Analýza dat / analytika** — chování reálných uživatelů (kliky, funnely).
- **Desk research** — existující reporty, předchozí výzkumy.

### Výstupy výzkumu

- **Persona** — fiktivní zástupce typického uživatele (cíle, potřeby, frustrace). Na začátku lze i _assumptive personas_.
- **User journey map** — cesta uživatele napříč dotykovými body, vč. emocí.
- **Empathy map** — co uživatel říká / myslí / dělá / cítí.

### Kvalitativní vs. kvantitativní výzkum (důležité!)

| |**Kvalitativní**|**Kvantitativní**|
|---|---|---|
|Otázka|**proč / jak**|**kolik / jak moc**|
|Vzorek|malý (≈ 5 uživatelů odhalí ~85 % problémů)|velký|
|Data|pozorování, citace, pocity|čísla, metriky|
|Příklady metod|interview, think-aloud, usability test 1v1|analytika, A/B test, dotazník (SUS)|

---

## 6) Wireframing a prototypování

### Wireframe vs. mockup vs. prototyp (komise: rozdíl wireframe × prototyp!)

- **Wireframe** — **statický „drátěný model"** rozložení obrazovky. Bez barev/grafiky, řeší **strukturu, hierarchii a rozmístění** prvků. Rychlý, levný, low-fidelity.
- **Mockup** — **statický, ale vizuálně věrný** návrh (barvy, typografie, obsah). Ukazuje, _jak to bude vypadat_. Není interaktivní.
- **Prototyp** — **interaktivní** model; lze v něm „proklikat" tok (flow). Simuluje chování. Může být **low-fi** (papír, klikací wireframy) i **hi-fi** (téměř k nerozeznání od finálního produktu).

![[sketch_wireframe_mockup_prototype.jpg]]

### Proč prototypovat dřív, než kódit (komise se ptá explicitně!)

- **Levné a rychlé ověření** nápadu — chybu opravím v prototypu za minuty, v kódu za dny.
- **Otestuji UX s uživateli** ještě před investicí do vývoje.
- **Komunikace** — sjednotí tým, klienta i stakeholdery na společné představě.
- **Iterace** — snadno zkouším varianty (i pro A/B test).
- Kód je drahý „závazek"; prototyp je **jednorázový experiment**.

### Nástroje

**Figma** (+ pluginy), Sketch, Adobe XD, Balsamiq (low-fi), InVision, Axure; pro papírové prototypy stačí tužka a papír.

---

## 7) Testování použitelnosti

> [!info] Cíl Ověřit s **reálnými uživateli**, jestli rozhraní zvládnou používat — odhalit třecí body, ne „obhájit" návrh.

### Kvalitativní testování (komise: „1v1, skupina, pocity, dotazník")

- **Moderované 1:1** — facilitátor zadává úkoly, uživatel **think-aloud**, pozorujeme chování a **pocity**.
- **Testování na skupině** účastníků.
- Na závěr **krátký dotazník / hodnocení** (vč. SUS, viz níže).
- Hledáme **proč** něco nefunguje.

### Kvantitativní testování (komise: „čas, jak dlouho komu co trvalo")

Měříme **metriky**:

- **Task success rate** (úspěšnost úkolu),
- **Time on task** (čas na úkol),
- **Error rate** (počet chyb),
- **Conversion / completion rate**.

### A/B testování (komise se ptá!)

Dvě (nebo více) **variant** stejné stránky/prvku se pustí současně různým skupinám uživatelů; měří se, **která dosahuje lepší metriky** (např. konverze). Statisticky podložené rozhodnutí místo dohadů. Vyžaduje dostatečný provoz (signifikance).

### Eye-tracking & heatmapy (komise: „pohyb očí → heatmap")

- **Eye-tracking** — sledování **pohybu očí**: kam se uživatel dívá, v jakém pořadí, kde se „zasekne".
- **Heatmapa** — agregovaná „teplotní" vizualizace pozornosti / kliků / scrollu (červená = nejvíc pozornosti). Lze získat z eye-trackingu i z klikových dat.

### SUS — System Usability Scale (komise: „co je SUS skóre?")

- **Standardizovaný dotazník o 10 otázkách**, 5bodová Likertova škála (souhlasím – nesouhlasím).
- Výsledkem je **skóre 0–100** (není to procento!).
- **≈ 68 = průměr**; nad 68 nadprůměrná použitelnost, ~80+ výborná.
- Rychlý, levný, srovnatelný nástroj pro **kvantitativní** hodnocení vnímané použitelnosti.

### Automatizované testování UI (komise: Ošlejšek se ptal!)

- Píší se **end-to-end / interaction testy**, které běží v **reálném prohlížeči** (např. **Playwright**, Cypress, Selenium).
- Lokátory ideálně podle **přístupnostních atributů** (`getByRole`, `getByText`) — ne křehké CSS selektory.
- **Nevýhody:** časově náročné na údržbu, hůř se definují (musí dodržet _login flow_, stav), křehké při změnách UI.
- **Pozn.:** automatizace ověří _funkčnost_ toku, ale **nenahradí** kvalitativní testování s lidmi (to odhalí _prožitek_ a porozumění).

---

## 8) Platformy — desktop vs mobil vs web

> [!important] Komise (Batko) chce hlavně slyšet rozdíly v interakci a možnostech HW.

- **Vstup:** mobil = **dotyk** (gesta, větší klikací cíle, palec), desktop = **myš + klávesnice** (hover, pravý klik, přesnost).
- **Senzory / HW:** mobil může přistupovat ke **kameře, GPS/poloze**, akcelerometru, notifikacím, biometrii → kontextové funkce.
- **Velikost a orientace obrazovky:** mobil malý/portrét, desktop velký/landscape → jiná informační hustota.
- **Web** musí být **responzivní** (jeden kód pro mnoho velikostí), řeší kompatibilitu prohlížečů, latenci sítě.
- **Kontext užití:** mobil „na cestách", krátké interakce, rušivé prostředí; desktop dlouhá soustředěná práce.
- **Konvence platformy** — iOS Human Interface Guidelines, Android Material Design, webové zvyklosti.

> [!example] Co odpovědět stručně „Hlavní rozdíl je **dotyk vs. myš** a u mobilu **přístup k HW (kamera, poloha)**; návrh proto musí počítat s většími cíli, gesty, responzivitou a kontextem použití."

---

## 9) Technická architektura UI

_(z přednášky — „jak UI funguje uvnitř"; dobré pro doplňující dotazy)_

### Element hierarchy (strom prvků)

- UI = **strom elementů** (widgets/views): okno → panely → tlačítka, texty, ikony…
- Každý element ví, **jak vykreslit** svůj kus informace (`draw(canvas)`).

### Události (events)

- Generovány vstupními zařízeními / externími podněty; platforma (browser/OS) je doručí elementu podle pozice, focusu…
- **Capturing** — událost putuje **shora dolů** k cíli.
- **Bubbling** — pokud ji element nespotřebuje, **probublává nahoru** stromem k rodičům.
- Element událost buď **spotřebuje (consume)**, nebo propustí dál.

### Stav aplikace (3 druhy)

|Typ|„Ztratíš, když…"|Příklad|Kde je uložen|
|---|---|---|---|
|**Element state**|…odstraníš element|pozice, scroll, hodnota inputu|jednotlivé UI elementy|
|**Session state**|…zavřeš okno|košík, přihlášení, navigace|browser / server (session cookie)|
|**Persistent state**|…selže disk|účet, nastavení, obsah|soubory/DB lokálně i na serveru|

Zjednodušeně: Element = _„jak UI vypadá teď"_, Session = _„co uživatel právě dělá"_, Persistent = _„co má uložené"_.

### Client-side vs. server-side rendering

- **Rendering** = _„kde se stav mění na HTML"_.
- **Server-side** (ASP.NET, Spring, Django, Rails, Laravel): server vrací hotové HTML. Centralizované, méně práce na klientu, víc kompute/bandwidth.
- **Client-side** (React, Vue, Svelte…): klient staví UI z lokálního stavu + dat z **API (REST, GraphQL)**. Bohatší interakce, offline, modulární; víc logiky v JS.
- Velké projekty obvykle **kombinují obojí**.

### Návrhové vzory pro oddělení stavu/logiky/UI

- **MVC (Model–View–Controller):** View vykresluje, Controller mění model dle událostí, Model = data + validace/persistence. View bývá „aktivní" (generuje celou hierarchii).
- **MVP (Model–View–Presenter):** View je **pasivní**, vše aktualizuje **Presenter** (imperativně). Odstraňuje nejednoznačnost „kdo aktualizuje View".
- **MVVM (Model–View–ViewModel):** vztah View↔ViewModel je **deklarativní přes data binding** (ne imperativní). Méně boilerplate, robustnější vůči změnám View. Sem patří většina moderních JS frameworků (React, Vue, Svelte).

### Komponentový návrh (component-based design)

- **Komponenta** zapouzdří složitou, ale samostatnou hierarchii elementů → **znovupoužitelná**, navenek vypadá jako jeden element.
- Převádí část „session-like" stavu na čistý **element state**.
- Popularizováno **Reactem**.

### WebComponents (webový standard)

- Standard prohlížeče pro komponenty; každá je **HTML element s vlastním tagem** (musí obsahovat `-`).
- **Shadow DOM** — vnitřní hierarchie skrytá před vnějším DOM i CSS (`attachShadow({mode:"open"})`).
- **`<template>` + `<slot>`** — deklarativní šablona; sloty určují, kam se vloží potomci.
- **Lifecycle:** `connectedCallback`, `disconnectedCallback`, `adoptedCallback`.

### Výkon: příliš mnoho elementů → Virtual/Lazy list

- V praxi už **800–1400 elementů** trápí výkon. Hodně dat (timeline, e-shop, Google Photos/Sheets, GitHub stromy) → tisíce elementů.
- Řešení:
    - **Stránkování / hierarchická navigace** — funguje, ale „immersion breaking" a problém u spojitých dat (tabulky).
    - **Virtual / Lazy list („windowing")** — v DOM je jen **fixní počet** viditelných položek; při scrollu se elementy **přidávají/odebírají**, data zůstávají v paměti.
- Úskalí: položky musí být +- stejného typu/výšky; rozbíjí browserové funkce (Ctrl+F, `::last-child`).

---

## 10) Technologie a nástroje

_(ze seminářů — front-end tooling; dobré jako „technologie a nástroje")_

> [!note] Proč tooling, když se JS/HTML/CSS „nekompilují"? **Kompatibilita** (transpile pro starší prohlížeče), **Performance** (minifikace), **Safety** (typy, lintery, testy).

|Nástroj|Role|
|---|---|
|**npm**|správce balíčků; `package.json`, `package-lock.json` (reprodukovatelnost), sémantické verzování, skripty|
|**webpack**|**bundler** — řeší importy a assety, balí do souborů; loaders, rules, plugins, entry/output, content-hash v názvu (cache)|
|**babel**|„kompilátor" — moderní JS → kompatibilní JS (`babel-loader`, presety)|
|**TypeScript (tsc)**|typové rozšíření JS; postupné typování; `tsconfig.json` (webpack defaultně typy nekontroluje!)|
|**LESS / SASS**|rozšíření CSS (proměnné, vnořování, mixiny) kompilované do CSS|
|**prettier**|automatické formátování kódu (čitelnost, konzistence)|
|**eslint**|linter — odhalí časté chyby v JS/TS|
|**jest**|unit testy (`expect` + matchery) — hlavně pro byznys logiku|
|**playwright**|E2E/interaction testy v reálném prohlížeči; lokátory `getByRole`/`getByText`|
|**istanbul (+nyc)**|code coverage|
|**CI**|spouští formátování, typy, testy automaticky na každý commit|

> [!tip] UI/UX nástroje Návrh & prototypování: **Figma** (+ pluginy), Sketch, Adobe XD, Balsamiq. AI v UX/UI: ChatGPT (brainstorming, UX copy, scénáře, plány testů), Notion AI (organizace, persony, journey mapy), generativní obrázky (Midjourney, Adobe Firefly, Leonardo), Looka (logo). Použití: sumarizace, analýza textu, generování nápadů, wireframy/prototypy, syntetický výzkum, kritika návrhů.

---

## 11) Příklady z praxe

> [!example] Dobré × špatné UX
> 
> - **Apple Magic Mouse** — nabíjecí port **na spodní straně** → při nabíjení nejde používat. Klasický příklad, kdy estetika porazila použitelnost.
> - **Tramvaj / MHD** — koupě jízdenky, orientace na zastávce: kde jsem, kde vystoupit, jak zaplatit — celé UX, nejen automat.
> - **Automat na kávu** — _„Kdy vyberu množství cukru? Jak poznám, kolik jsem vybral?"_ — chybějící zpětná vazba a nejasný mapping.
> - **Food-delivery app** (projekt z PV252) — discoverability jídel/restaurací (search/list/doporučení) a snadné vytvoření objednávky pro konkrétní cílovou skupinu.

> [!example] Vlastní diplomka Komise **velmi často** napojuje otázku na tvoji DP (Ošlejšek, Spurný, Batko). Připrav si: jaké UI jsi dělal/a, jak jsi ho navrhoval/a (wireframe/prototyp – low-fi?), jak jsi ho **testoval/a** a co z toho vzešlo.

---

## 12) Q&A — reálné otázky komise

_(Zdroj: tabulka SZZ — kód otázky **DEV4 / DEV5 = PV182·PV247·PV278**. Roky 2024–2026.)_

> [!question] Ošlejšek (2024): „Co je UX? Popiš CRAP. Testování UI — kvalitativní vs. kvantitativní. Automatizované testování UI." **UX** = celkový prožitek interakce uživatele s produktem (užitečnost, snadnost, emoce). **CRAP** = Contrast, Repetition, Alignment, Proximity (viz §2). **Kvalitativní testování** = 1v1 / na skupině, think-aloud, sledujeme pocity, závěrečný dotazník → odhalí _proč_. **Kvantitativní** = měříme čas na úkol, úspěšnost, počet chyb → _kolik_. **Automatizované UI testy** = E2E v reálném prohlížeči (Playwright/Cypress/Selenium), dodržují login flow, jsou časově náročné a křehké, neověří prožitek.

> [!question] Batko (2024): „Cyklus vývoje UI, principy tvorby UI, na co se zaměřit u desktop/mobil/web UI, techniky (interview, pozorování, think-aloud, prototypy)." **Cyklus:** výzkum → wireframy → prototyp → vizuální design → testování → implementace → měření/iterace (iterativní!). **Principy:** CRAP, konzistence, zpětná vazba, prevence chyb, „don't make me think". **Platformy:** dotyk vs. myš, u mobilu HW (kamera, poloha), responzivita. **Techniky:** interview, pozorování, think-aloud, prototypy.

> [!question] Batko (2024): „UI obecně — jak k němu přistupovat. Wireframe, prototypování, A/B testování, CRAP. Čím se liší UI pro telefon/web/desktop?" Postup návrhu (viz výše). **Wireframe** = statické rozložení; **prototyp** = interaktivní proklikatelný model. **A/B test** = dvě varianty → měřím lepší metriku. **CRAP** = 4 principy. **Rozdíl platforem:** hlavně **dotyk místo myši** a přístup k **kameře/poloze** u mobilu.

> [!question] Oujezský (2024): „Co by měla dobrá UI splňovat a jaký je postup vývoje UI (fáze)?" **Dobrá UI:** použitelná (5 Nielsenových atributů: learnability, efficiency, memorability, errors, satisfaction), konzistentní, srozumitelná, s dobrou zpětnou vazbou, přístupná, estetická (CRAP, Rams). **Fáze:** výzkum → návrh (wireframe) → prototyp → vizuál → testování → implementace → iterace.

> [!question] Bártek (2024): „Uživatelská rozhraní" (obecně) Mluv o UX vs. UI, principech návrhu (CRAP, použitelnost), procesu (design thinking, iterace), výzkumu, prototypování a testování. Nech se navést — Bártek upřesní, co chce slyšet; klidně odbočuj a uváděj příklady.

> [!question] Spurný (2025): „Rozdíl prototyp × wireframe. Proč dělat prototyp dřív než kódit? User research — jak a na co. Co sleduješ u interaktivního prototypu (eye-tracking → heatmap)? Co je SUS skóre?" **Wireframe** = statické low-fi rozložení; **prototyp** = interaktivní model (může být low-fi i hi-fi). **Proč prototyp:** levné/rychlé ověření, test s uživateli před investicí do kódu, komunikace v týmu, snadná iterace. **User research:** interview, pozorování, think-aloud, dotazníky → pochopit potřeby; děláme ho na začátku i průběžně. **U prototypu sleduji:** chování, kde se zasekne, **pohyb očí (eye-tracking)** → agregace do **heatmapy**. **SUS** = System Usability Scale, 10 otázek, 5bodová škála, skóre 0–100, průměr ≈ 68.

> [!question] Ráček (2025): „Unified Process — fáze, iterace, workflows, milníky, grafy. + Prototypování." **UP** = iterativní/inkrementální proces; fáze **Inception → Elaboration → Construction → Transition**, napříč nimi workflows (requirements, analysis & design, implementation, test…), každá iterace má milníky. **Prototypování** zapadá zejména do raných fází (ověření požadavků/architektury) — viz §6.

> [!question] Batko (2026): „UI/UX — návrh a principy se zaměřením na různé platformy (desktop vs mobil)." Kombinace předchozího: UX/UI rozdíl, principy návrhu a použitelnosti, proces, a hlavně **rozdíly platforem** (dotyk vs. myš, HW přístup, responzivita, kontext užití). Doptává se na detaily, ale pomáhá dojít k odpovědi.

> [!summary] TL;DR pro komisi Uměj **vlastními slovy**: UX×UI (ledovec), **CRAP**, **5 atributů použitelnosti**, **design thinking + iterace**, **wireframe×prototyp** a **proč prototypovat**, **user research** (interview/pozorování/think-aloud), **kval×kvant testování**, **A/B**, **eye-tracking/heatmapy**, **SUS**, **automatizované UI testy** (Playwright), **rozdíly desktop/mobil/web** (dotyk, kamera, poloha). Vše **propoj s vlastní diplomkou a příklady**.