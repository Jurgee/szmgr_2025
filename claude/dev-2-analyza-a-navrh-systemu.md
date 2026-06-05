# DEV 2 Analýza a návrh systémů

> [!abstract] Objektové metody návrhu informačních systémů. Specifikace a řízení požadavků. Softwarové architektury, komponentové systémy. Návrhové a architektonické vzory. Rozhraní komponent, kontrakty na úrovni rozhraní, OCL. Modely softwarových systémů, jazyk UML. Příklady z praxe pro vše výše uvedené. (PA103, PV167 || PV258 || PV293)

---

## 0. Jak se to reálně zkouší (insight z komisí)

Z reálných zápisů zkoušení vyplývá, na co se členové komise nejčastěji ptají. Stručně, ať víš, kam směřovat přípravu:

- **Ošlejšek** — nejčastěji **Use Case diagram** do hloubky (typy aktérů, include/extend/generalizace, extension points, _jak se specifikuje jeden use case_ — strukturovaným textem nebo sekvenčním diagramem, a **proč textem místo diagramu — protože je to kratší**). Dále **interakční diagramy** (sekvenční vs. komunikační, podmínky/cykly, vznik a zánik objektu za běhu), **stavový diagram**, **object diagram vs. ERD**, **komponenty / kontrakty / rozhraní**, funkční vs. nefunkční požadavky.
- **Bühnová** — UML obecně (nakreslit class diagram + popsat vazby), modely procesů a **vyvažování (balancing) modelů**, architektonické a návrhové vzory (vybere MVC, ať nakreslíš; SOA + jak fungují rozhraní), cloud/SOA/MVC obecně.
- **Batko** — **UML základní dělení (structural vs. behavioral, statické vs. dynamické)**, vazby v class diagramu (přesné názvy aggregation/composition + značení), **komponentové systémy** (jak komponenta komunikuje s okolím, dependency injection, signatura komponenty), **architektury od monolitu po layered po SOA** (chce slyšet, že SOA je distribuovaná), Clean Code + SOLID + refaktoring + testování.
- **Ráček** — UML diagramy mapované na **Unified Process** fáze, **agilní vs. prediktivní vývoj, SCRUM, iterativní/inkrementální**, procesní diagramy (activity, BPMN, use case), porovnání Class diagramu a E-R diagramu, normalizace.
- **Spurný** — typy požadavků (FP/NFP + příklady), use case (aktér, include/extends, dědičnost), **user stories**, životní cyklus SW a mapování fází na diplomku, BPM (a jaká je UML alternativa k BPM), uživatelská rozhraní (prototyp vs. wireframe).
- **Dohnal** — MVC, SOA (proč, praktická aplikace), softwarové architektury a návrhové vzory s příkladem na každý typ, BPM/BPMN.
- **Jakubíček / Rambousek / Macák / Plhák / Pastva / Lexa / Bártek** — návrhové vzory (rozdělení, vyber dva a popiš s příkladem), MVC vs. MVVM (data binding), 4-vrstvá architektura (DAL, Infrastructure, Business, Presentation), ORM a lazy loading, middleware, REST vs. SOAP, fáze vývoje, uživatelská rozhraní.

> [!tip] Univerzální rada Komise většinou navádí a nechce „nachytat". Chce **přesné názvy**, schopnost **nakreslit** diagram a **uvést příklad z praxe** (nejlépe z vlastní diplomky). Stačí znát věci z materiálů + logicky odvodit.

---

## 1. Objektové metody návrhu informačních systémů

### 1.1 Proč vůbec návrh a dekompozice?

Hlavní výzva SW vývoje **není** dodat software splňující funkční požadavky, ale:

- **Maintainability (udržovatelnost)** — schopnost upravovat kód kvůli opravám chyb a novým požadavkům.
- **Quality (kvalita)** — splnění kvalitativních aspektů: efektivita, bezpečnost, spolehlivost.

Proč je to těžké:

- **Complexity** — dnešní systémy jsou příliš velké na to, aby se řídily jako celek → řešením je **dekompozice** systému na menší soudržné části (lze je lépe udržovat a měřit jejich kvalitu).
- **Changing requirements** — požadavky se mění i během vývoje → dekompozice (malé izolované části s jasnou zodpovědností a komunikačními protokoly) lokalizuje změny; **process management** (řízení požadavků, vývoje, testů); **quality assurance**.

Tři pilíře řešení: **Process management**, **Quality assurance**, **System decomposition**.

### 1.2 Strukturovaný vs. objektový přístup

- **Strukturovaný program** — procedury alokované v paměti před spuštěním; čtou/zapisují data z datových segmentů a volají jiné procedury. Oddělené funkční a datové modely (kontextový diagram, DFD, ERD…), složité vztahy hlavně mezi funkčním a datovým modelem.
- **Objektový program** — objekty/komponenty spolupracují tím, že si posílají zprávy (volají metody a čekají na odpověď); objekty mohou vytvářet jiné objekty; data a zodpovědnosti jsou rozprostřeny mezi objekty, které dynamicky vznikají/zanikají v paměti. **Zapouzdření** dat a operací → těsná vazba data↔funkce je skrytá uvnitř, mezi částmi je volná vazba.

> [!note] Identita
> 
> - V **ERD**: identita = primární klíč.
> - V **class diagramu**: identita = adresa objektu v paměti (lze ji ale změnit). Na rozdíl od ER modelu nejsou M:N vztahy problém (lze pole odkazů na obou stranách), ale řešení se liší v udržovatelnosti a efektivitě.

### 1.3 Objektové fundamenty

- **Objekt** — nejmenší běhová entita ukládající data za vrstvou veřejných metod/rozhraní.
- **Třída** — statický pohled (design-time) na své objekty.
- **Stav** — konkrétní hodnoty atributů (polí).
- **Chování** — definováno metodami; může být ovlivněno stavem.
- **Identita** — daná adresou objektu (default).

### 1.4 Abstrakce

**Abstrakce** = klasifikace objektů a tříd (co reálně reprezentují v systému). Existuje mnoho možných (správných) abstrakcí; nejlepší závisí na požadavcích systému (aplikační doméně). Volba abstrakce je klíčová, protože ovlivňuje **rozdělení zodpovědností**.

Abstrakci jedné třídy ovlivňují: **atributy** (možné stavy), **metody** (chování), **okolní kontext** (sousední třídy/objekty).

> [!warning] Častá chyba Generická abstrakce kvůli znovupoužitelnosti třídy napříč systémy zavádí nepoužívanou funkcionalitu → snižuje udržovatelnost. **Reusability funguje pro komponenty, NE pro třídy/objekty** — třídy jsou těsně svázané se sousedy, komponenty jen volně přes kontrakty.

### 1.5 Dědičnost vs. asociace (agregace)

- **Dědičnost** — „každý savec je zvíře"; objekty **nikdy nemění** příslušnou třídu (typ) během života.
- **Asociace** — flexibilnější, vazby vznikají a mění se za běhu.
- Pravidlo: **Nikdy nepoužívej dědičnost, pokud se role objektu může měnit v čase.** Dědičnost lze vždy nahradit asociací. Reálná dekompozice obě principy mixuje.

### 1.6 SOLID principy

Návrhové principy pro srozumitelnější, flexibilnější a udržovatelnější software:

|Princip|Význam|
|---|---|
|**S** — Single Responsibility|Každý modul/třída/funkce má zodpovědnost za **jednu** část funkcionality a zapouzdřuje ji. → více menších tříd s úzce zaměřenými metodami.|
|**O** — Open/Closed|Entity jsou **otevřené pro rozšíření, uzavřené pro modifikaci**. Nová funkcionalita = nové metody/třídy, ne změny existujícího kódu.|
|**L** — Liskov Substitution|Kdekoli se očekává instance třídy, lze dosadit instanci její podtřídy bez narušení vlastností programu. (viz omezení na signatury a chování — kap. 7)|
|**I** — Interface Segregation|Rozděl velká rozhraní na menší a specifická, aby klienti znali jen metody, které je zajímají.|
|**D** — Dependency Inversion|Závisej na **abstrakcích, ne na konkrétních implementacích**.|

**Liskov — pravidla pro signatury:**

- Argumenty podtříd musí být **kontravariantní** (stejně nebo více generické než u bázové třídy).
- Návratový typ musí být **kovariantní** (stejně nebo méně generický, tj. více odvozený).
- Podtyp nesmí vyhazovat nové výjimky (jen podtypy výjimek nadtypu).

**Liskov — behaviorální podmínky (History constraint):** nové/upravené členy nesmí měnit stav objektu způsobem, který by bázová třída nedovolila (klasický příklad: `MechanicalDuck`, který nezakváká bez baterie, porušuje očekávání).

> [!note] Polymorfismus Produkt dědičnosti a dynamického vázání. Podtřída dědí jméno metody, ale může mít jinou implementaci. (Klasická otázka: jaký je výstup `ClassA o = new ClassB(); o.print();` → použije se implementace z `ClassB`.)

---

## 2. Modely softwarových systémů, jazyk UML

### 2.1 Přehled UML diagramů

UML diagramy se dělí na dvě hlavní skupiny (oblíbená otázka **Batka**):

**Strukturální (statické):**

- **Class diagram** (hlavní model), Object diagram, Component diagram, Deployment diagram, Package diagram, Composite structure, Profile.

**Behaviorální (dynamické):**

- **Use Case diagram**, **Sequence diagram**, **Communication diagram**, **State machine diagram**, **Activity diagram**, Timing, Interaction overview.

> [!tip] Statický × dynamický pohled Class diagram je statický model, ale **v analytické fázi se používá hlavně k vyjasnění chování** (funkčních požadavků). Sequence/Activity/State zachycují dynamiku.

### 2.2 Class diagram (vazby — komisní favorit)

Vztahy mezi třídami (komise chce **přesné názvy a grafické značení**):

|Vztah|Význam|Notace|
|---|---|---|
|**Asociace**|strukturální vztah, „zná/používá"|čára|
|**Agregace**|„má" / celek-část, slabá (sdílení částí)|prázdný kosočtverec ◇|
|**Kompozice**|silné vlastnictví, část nepřežije celek|plný kosočtverec ◆|
|**Generalizace / dědičnost**|„je"|prázdná trojúhelníková šipka ▷|
|**Závislost**|dočasná vazba (parametr, lokální proměnná)|čárkovaná šipka|
|**Realizace**|implementace rozhraní|čárkovaná trojúhelníková šipka|

Dále: **multiplicita** (0..1, 1, 0..*, 1..*), **navigovatelnost**, **role names**, atributy `viditelnost name : Type`, metody (v class diagramu jsou to **metody**, ne funkce/procedury — pozn. Ráček).

![[uml_notation.png|322]]

### 2.3 Object diagram vs. ERD (Ráček)

- **Object diagram** = snímek konkrétních instancí v určitém okamžiku (objekty + linky + hodnoty atributů).
- **Class diagram vs. ERD**: Class diagram má navíc **metody** (chování), zatímco ERD popisuje jen data; identita v ERD = primární klíč, v OO = adresa v paměti. Class umožňuje M:N přímo (pole na obou stranách), ERD potřebuje vazební entitu. Souvisí i **normalizace** (1NF–4NF) u ERD/datového modelu.

### 2.4 Use Case diagram (Ošlejšek — do hloubky!)

**Prvky:**

- **Systém** (rámeček = hranice systému, _system boundary_).
- **Aktéři** — **3 typy**: (1) osoby/uživatelé (uživatelské role), (2) externí systémy, (3) **čas / časované akce** (časovač). Mezi aktéry může být **generalizace**.
- **Případy užití (use cases)** — elipsy.

**Vztahy:**

- **`<<include>>`** — UC vždy zahrnuje jiný UC (povinné, společné chování vytknuté ven).
- **`<<extend>>`** — rozšiřuje funkcionalitu **podmíněně**; jejíž použití je „na uvážení" — ale POZOR, samotná odpověď „rozšiřuje funkčnost" Ošlejškovi **nestačí**: extend se používá ve specifických případech, má **extension points** (body rozšíření) a může mít dodatečné atributy/podmínky.
- **Generalizace** — mezi UC i mezi aktéry.

**Jak se specifikuje jeden use case** (klíčová podotázka):

- **Strukturovaným textem** (scénář: hlavní tok, alternativní toky, předpoklady, výsledek) — **NEBO**
- **sekvenčním diagramem**.
- **Proč spíš text než diagram?** Není potřeba být úplně formální vzhledem k účelu UC diagramu a **textový popis je kratší** (to chtěl Ošlejšek slyšet).

![[use_case_diagram.webp|425]]

### 2.5 Interakční diagramy (Sequence / Communication)

- **Sequence diagram** — časová osa, lifelines, zprávy (synchronní/asynchronní/návratové), aktivace.
    - **Vznik objektu za běhu**: `<<create>>`; **zánik**: `<<destroy>>` (křížek na konci lifeline).
    - **Podmínky a cykly** — pomocí **combined fragments**: `alt` (podmínka/větvení), `opt` (volitelné), `loop` (cyklus), `par`, `ref`.
- **Communication (collaboration) diagram** — stejná informace jako sequence, ale zdůrazňuje **strukturu vazeb** mezi objekty; zprávy číslované (1, 1.1, 1.2…). **Sekvenční a komunikační se dají vzájemně mapovat** a moc se neliší (jen důraz: čas vs. struktura).

![[sequence_diagram.webp|494]]

### 2.6 State machine diagram (stavový diagram)

- **Stavy** (states), **přechody** (transitions), **události** (events) spouštějící přechody, **akce** (actions), **strážní podmínky** (guards).
- **Determinismus** — z daného stavu při dané události vede nanejvýš jeden přechod.
- **Podstavy (substates) a dědičnost** — z libovolného podstavu se lze dostat mimo rodičovský stav.
- Vhodný pro zachycení **vnitřních stavů komponent**.

![[uml_state_diagram.webp]]

### 2.7 Activity diagram

- Tok aktivit/akcí, větvení (decision/merge), souběh (fork/join), swimlanes (oddíly zodpovědnosti). Vhodný pro popis chování poskytovaných služeb a procesů. Lze ho mapovat na sekvenční diagram.

![[uml_activity_diagram.webp|327]]

### 2.8 Konceptuální (analytický) class diagram

Tzv. **problem domain model** — používá termíny/koncepty aplikační domény (ne třídy programovacího jazyka). Popisuje doménu pomocí termínů (tříd), jejich vlastností (atributů) a kontextu (asociace s multiplicitou). Tři úrovně detailu: **Conceptual Information Model → Information Design Model → Class Design Model** (konkrétní jazyk).

### 2.9 BPMN / BPM (Spurný, Dohnal, Ráček)

**BPMN** (Business Process Model and Notation) — modelování byznys procesů. Základní kategorie prvků:

- **Flow Objects**: Events (start/intermediate/end), Activities (task, sub-process), Gateways (rozhodovací — XOR, AND, OR).
- **Connecting Objects**: Sequence Flow (řídí pořadí — POZOR, **nepřenáší data**!), Message Flow, Association.
- **Swimlanes**: Pools a Lanes (kdo co dělá).
- **Artifacts**: Data Objects, Groups, Annotations.

**UML alternativa k BPM** = **Activity diagram**. K čemu BPM: srozumitelná komunikace procesů mezi byznysem a vývojem; konzistence s ostatními diagramy.

![[uml_bpmn.png]]

### 2.10 Konzistence a vyvažování modelů (Bühnová)

- **Konzistence uvnitř modelu** a **konzistence mezi modely** (např. use case ↔ class ↔ sequence).
- **Vyvažování modelů (balancing)** — průběžné dopracovávání a synchronizace; iterativní modelování pomáhá řešit složitý životní cyklus.

---

## 3. Specifikace a řízení požadavků

### 3.1 Typy požadavků (Spurný, Ošlejšek)

|Typ|Popis|Plán implementace v…|
|---|---|---|
|**Funkční (FP)**|co systém dělá; vztaženo k requirements analysis a use case modelingu. V komponentových systémech i vnitřní funkčnost komponent a inter-komponentová spolupráce (kontrakty).|system **design**|
|**Nefunkční (NFP)**|omezení na použité technologie (HW/SW kompatibilita, middleware).|(technologie)|
|**Extra-funkční**|kvalitativní omezení: response time v X % volání (performance), dostupnost X % měsíčně (reliability).|system **architecture**|

> Příklady FP: „Po naskenování čárového kódu se přidá položka do účtenky." NFP: „Musí běžet na PostgreSQL i Oracle." Extra-funkční: „99,9 % volání odpoví do 10 ms."

### 3.2 User stories (Spurný)

Krátký formát z pohledu uživatele: **„Jako `<role>` chci `<cíl>`, abych `<přínos>`."** Doplněno **akceptačními kritérii** (často Given–When–Then). Vhodné pro agilní vývoj; hrubší než plný textový use case, podporují diskusi.

### 3.3 Use cases vs. user stories

- **Use case** = detailní scénář interakce (hlavní + alternativní toky), formálnější.
- **User story** = stručná, zaměřená na hodnotu, rozpracovávaná v konverzaci s product ownerem.

---

## 4. Návrhové vzory (Design Patterns) a analytické vzory

### 4.1 Co je vzor a historie

- **Christopher Alexander (1977–79)** — pojem „pattern" pro architekturu budov/měst; _pattern language_. Definice: „Každý vzor popisuje problém, který se opakuje, a jádro jeho řešení tak, že ho lze použít milionkrát, nikdy ne stejně."
- **GoF (Gamma, Helm, Johnson, Vlissides), 1995** — _Design Patterns: Elements of Reusable Object-Oriented Software_.
- **Martin Fowler, 1997** — _Analysis Patterns_.

**Úrovně vzorů:** kódové (coding) → návrhové (design) + analytické/byznys → architektonické. Společný cíl: zlepšit kvalitu návrhu (specifickou nebo udržovatelnost).

### 4.2 Vlastnosti a rizika vzorů

- Vzor **není hotový návrh**, je to meta-model, který je nutné **instanciovat**, zvážit kompromisy a kombinovat s jinými. Použití je **kreativní proces**, ne mechanické.
- Společný slovník („tady použijeme Observer") → lepší komunikace, rychlejší návrh.
- **Rizika**: aplikace co nejvíce vzorů ≠ dobře; řešení špatného problému; podobná vizuální struktura různých vzorů (State vs. Strategy).

### 4.3 Elementy popisu vzoru (GoF)

Essential: **Pattern name, Problem (kdy aplikovat), Solution (prvky a vztahy), Consequences (výsledky a kompromisy)**. Plný GoF formát navíc: Intent, Motivation, Applicability, Structure, Participants, Collaborations, Implementation, Sample Code, Known Uses, Related Patterns.

### 4.4 Klasifikace GoF vzorů

|Účel ↓ / Scope →|**Class**|**Object**|
|---|---|---|
|**Creational**|Factory Method|Abstract Factory, Builder, Prototype, Singleton|
|**Structural**|Adapter (class)|Adapter (object), Bridge, Composite, Decorator, Facade, Flyweight, Proxy|
|**Behavioral**|Interpreter, Template Method|Chain of Responsibility, Command, Iterator, Mediator, Memento, Observer, State, Strategy, Visitor|

- **Creational** — inicializace a konfigurace objektů; skrývají proces tvorby.
- **Structural** — oddělují rozhraní od implementace; skládají třídy/objekty.
- **Behavioral** — dynamické interakce; rozdělení behaviorální zodpovědnosti.

### 4.5 Creational vzory

**Singleton** — jediná instance s globálním přístupovým bodem.

- _Použití_: musí existovat právě jedna instance dostupná z dobře známého bodu.
- _Důsledky_: kontrolovaný přístup, redukce globálních proměnných, lze podtřídit.
- _Kritika (anti-pattern)_: globální stav (code smell), porušuje SRP, špatně se testuje, problémy s konkurencí. Řešení: **dependency injection + ORM** (Spring).
![[dp_singleton.png]]

**Factory Method** — deleguje vytvoření produktu na podtřídy (`createDocument()`). Použij, když třída nezná dopředu třídu objektů, které tvoří.
![[dp_factory_method.png]]

**Builder** — postupně (per partes) vytváří složitý produkt; odděluje konstrukci od reprezentace. **Director** řídí, **ConcreteBuilder** staví. Příklad: `StringBuilder`, `DocumentBuilder`.
![[dp_builder.png]]

**Abstract Factory** — vytváří **rodiny kompatibilních produktů** (např. iOS vs. Android widgety). Konkrétní factory bývá Singleton.
![[dp_abstract_factory.png]]

**Prototype** — nové instance klonováním vzorového objektu (`clone()`).
![[dp_prototype.png]]

### 4.6 Structural vzory

**Adapter (Wrapper)** — převádí rozhraní existující třídy na to, které potřebujeme.
![[dp_object_adapter.png]]

- **Class adapter** (vícenásobná dědičnost) vs. **Object adapter** (delegace na adaptee — funguje i pro podtřídy adaptee).
![[dp_class_adapter.png]]

**Bridge** — odděluje **abstrakci od implementace**, aby se daly měnit nezávisle (např. TV × ovladač). Rozdíl od Adaptera: Bridge se navrhuje předem pro nezávislou variabilitu, Adapter mění rozhraní existujícího objektu (ex post).
![[dp_bridge.png]]

**Composite** — strom celek-část; klient zachází s listy i kompozity **jednotně** (`operation()`, `add`, `remove`). Příklad: `java.awt.Container#add`.

![[dp_composite.png]]

**Decorator** — dynamicky přidává zodpovědnosti objektu (alternativa k explozi podtříd). Příklad: `BufferedWriter(new FileWriter(...))`. Rozdíl od Strategy: decorator mění „slupku" (rozšiřuje chování), strategy mění „vnitřek" (vyměňuje chování).

![[dp_decorator.png]]

**Facade** — jednoduché rozhraní ke složitému subsystému; snižuje vazby klient↔subsystém.
![[dp_facade.png]]

**Flyweight** — sdílení mnoha jemných objektů (úspora paměti). **Intrinsic state** (sdílený, nezávislý na kontextu) vs. **extrinsic state** (závisí na kontextu, předává klient). Příklad: `Integer.valueOf()` cachuje −128..127.
![[dp_flyweight.png]]

**Proxy** — zástupce reálného objektu (remote / virtual / protection / smart reference). Příklad: `Collections.unmodifiableList()`.
![[dp_proxy.png]]

> [!note] Podobné vzory zavádějící mezičlánek **Decorator** (řetězí chování) × **Object Adapter** (mění rozhraní) × **Proxy** (vloží kód před voláním — bezpečnost, vzdálené připojení).

### 4.7 Behavioral vzory

**Iterator** — jednotný průchod kolekcí bez odhalení vnitřní reprezentace (`first/next/hasNext/current`). Bez iterátoru by v Javě nebyl for-each.
![[dp_iterator.png]]

**Strategy** — rodina zaměnitelných algoritmů; eliminuje switch-case; alternativa k podtřídění. Context drží strategii jako parametr.
![[dp_strategy.png]]

**State** — objekt mění chování při změně vnitřního stavu; dělá přechody stavů explicitní.

> [!tip] State vs. Strategy (oblíbené srovnání)
> 
> - State drží odkaz na **context**, Strategy ne.
> - State se může **sám nahradit** (změnit stav contextu), Strategy ne.
> - Strategy se předává jako **parametr**, State si vytváří context sám.
> - Strategy řeší **jednu konkrétní úlohu**, State poskytuje implementaci pro (skoro) vše, co context dělá.

![[dp_state.png]]

**Observer** (publish-subscribe) — závislé objekty se aktualizují při změně subjektu (`attach/detach/notify` → `update`). „Hollywood principle: Don't call me, I'll call you." Příklad: `java.util.Observer/Observable`, `EventListener`.

![[db_observer.png]]

**Memento** — uloží a obnoví stav objektu bez porušení zapouzdření (Originator, Memento, Caretaker). Příklad: serializace, `Date#getTime/setTime`.
![[dp_memento.png]]

**Visitor** — přidává nové operace jako externí třídy nad strukturou objektů (double dispatch: `accept(visitor)` → `visitElementX(this)`). Snadné přidávat operace, těžké přidávat nové prvky.
![[db_visitor.png]]

**Command** — požadavek jako objekt (lze frontovat, logovat, undo). **Chain of Responsibility** — požadavek delegován po řetězci zodpovědných. **Mediator** — koordinuje interakce mezi objekty. **Template Method** — kostra algoritmu s kroky doplněnými podtřídou. **Interpreter** — interpret malé gramatiky.
![[db_command.png]]

### 4.8 Analytické vzory (Fowler)

Používají se v **analytické fázi** na konceptuálním (problem domain) modelu; umožňují zachytit funkční požadavky „efektivně" (bez stálého překreslování) a **řídí kladení otázek** analytikem. Založené na data modelingu (ERD / class diagram). Klíčové UML modely pro analýzu: **Use Case** (uživatelské požadavky) + **konceptuální class diagram**.

**9 kolekcí (65 vzorů + 21 podpůrných):**

1. **Accountability** — organizační struktura a zodpovědnosti.
2. **Observations and Measurements** — měřené hodnoty (základ datových struktur).
3. **Observations for Corporate Finance** — finanční analýzy.
4. **Referring to Objects** — identifikace objektů.
5. **Inventory and Accounting** — pohyb zdrojů mezi „kapsami".
6. **Planning, Trading, Derivative Contracts, Trading Packages** — plány, obchod, finance.

**Vybrané vzory:**

- **Party** — sjednocený nadtyp osoby a organizace (subjektů ve vztahu zodpovědnosti).
- **Organization Hierarchies** — rekurzivní vztah parent–children (flexibilní hierarchie); přidání nové úrovně = nová podtřída + OCL pravidlo, ne překreslení.
- **Organization Structure** — více paralelních hierarchií / měnících se v čase (důraz na pravidla, TimePeriod).
- **Accountability** = kombinace Party + Organization Structure; `AccountabilityType` určuje povahu vztahu.
- **Accountability Knowledge Level** — connection rules omezující platné páry parent/child (knowledge level konfiguruje operational level).
- **Quantity / Conversion Ratio / Compound Units** — hodnoty + jednotky + převody (motivace: Mars Climate Orbiter — záměna jednotek za 327 M$).
- **Measurement** (kvantitativní) vs. **Observation** (kvalitativní — PhenomenonType + Phenomenon).
- **Name / Identification Scheme** — identifikace objektů (více schémat: jméno, RČ, čárový kód…).
- **Account / Transaction** (Inventory & Accounting) — princip zachování: položku nelze vytvořit/zničit, jen přesunout; `balance = sum(entries.amount)`, u transakce `sum(entries.amount)=0`.

> **Analytické vs. design vzory**: analytické zachycují funkční požadavky na konceptuálním modelu (termíny domény); design vzory řeší udržovatelnou dekompozici tříd na úrovni atributů, metod a přesných vztahů.

---

## 5. Softwarové architektury a architektonické vzory

### 5.1 Co je SW architektura

> „Software architecture are those decisions that are hard to change." — M. Fowler

Architektura = množina **základních návrhových rozhodnutí** o systému; struktura složená ze SW prvků, jejich navenek viditelných vlastností a vzájemných vztahů (Bass et al.), + principy návrhu a vývoje. **Robert C. Martin**: dobrý architekt **maximalizuje počet rozhodnutí, která nemusí udělat** (odložit rozhodnutí o UI, DB, frameworku).

### 5.2 Stavební prvky

- **Modules (moduly)** — kusy SW realizující funkcionalitu (komponenty, mikroslužby, procesy, vlákna, subsystémy). Často běží paralelně → modelujeme **distribuované systémy**.
- **Connectors (konektory)** — komunikační kanály a rozhraní modulů (volání metod, kanály zpráv, publish-subscribe, protokoly); mají vlastní vnitřní logiku; v komponentových systémech je realizuje **framework**.
- **Deployment (nasazení)** — mapování modulů a konektorů na HW/SW zdroje (servery, OS, cloud).

### 5.3 Styly vs. vzory

- **Architektonický styl** — souhrnné architektonické principy ovlivňující kód (mělčí znalost domény, obecná pravidla).
- **Architektonický vzor** — generické řešení návrhu architektury pro opakující se problém, parametrizovatelné kontextem (hlubší znalost domény).

### 5.4 Client-Server a P2P

- **Server** — reaktivní entita (jen odpovídá), **Client** — aktivní (iniciuje). Důvody: různé HW/SW nároky, různí dodavatelé, horizontální (více klientů) a vertikální (silnější server) škálovatelnost.
- **P2P (peer-to-peer)** — peers jsou rovnocenní (poskytují i žádají služby); zobecnění client-server. Příklady: BitTorrent, Skype.
- **Tiers**: 2-tier → 3-tier → N-tier (distributed presentation / presentation / application / data management).

### 5.5 Vrstvená architektura (Layered) — komisní favorit

Strukturuje aplikaci do **vrstev na různých úrovních abstrakce**.

**Pravidla:**

- Vyšší vrstva = vyšší abstrakce.
- Vrstva N poskytuje služby vrstvě N+1, deleguje podúlohy na N−1.
- Služby vrstvy N používá **jen** vrstva N+1 (jednosměrně — to chce slyšet Batko!).

**Dynamika:** top-down (klient → dolů), bottom-up (driver → nahoru), přes podmnožinu vrstev (cache).

**Důsledky:** + znovupoužitelnost, standardizace, lokální závislosti (testovatelnost), zaměnitelnost vrstev (Proxy pro remote, Adapter pro adaptaci rozhraní, Bridge pro dynamickou výměnu). − kaskády změn, nižší efektivita (overhead), obtížná granularita vrstev.

**Varianty:** Relaxed Layered (vrstva smí použít všechny nižší — open/closed layers, fast-lane reader), Layering Through Inheritance.

**Klasické vrstvy:** Presentation → Business → Persistence → Database.

**4-vrstvá / iDesign Method (Macák):**

- **Presentation** — komunikace s klientem, žádná byznys logika (kdo žádá).
- **Service** — orchestruje use cases, workflow (co je třeba udělat).
- **Domain model** — vykonává byznys logiku napříč use cases (jak to implementovat).
- **Resource Access** — přístup ke zdrojům (DB, REST endpointy — odkud data).
- **Utilities** — cross-cutting concerns (security, logging, reporting).

### 5.6 Repository

Interakce s daty přes **centrální repository**, který zprostředkovává přístup mezi datovou vrstvou a byznys vrstvami. Izoluje doménové objekty od kódu přístupu k DB; mapuje slabě typovaná data (DB) na silně typované doménové entity; funguje i jako cache.

### 5.7 MVC, MVP, MVVM (Bühnová, Dohnal, Macák)

**MVC (Model-View-Controller):**

- **Model** — funkční jádro + data; registruje a notifikuje závislé View/Controller (Observer!).
- **View** — prezentace; každý View si vytváří **Controller** (1:1).
- **Controller** — přijímá vstupní události, překládá je na požadavky pro Model nebo View.
- Striktně odděluje vstup a výstup. Použití: webové aplikace (Spring MVC, ASP.NET MVC). + více pohledů na jeden model, synchronizace, vyměnitelnost. − složitost, těsná vazba View↔Controller a na Model.

**MVP (Model-View-Presenter):** varianta pro „widget" a moderní web; **Presenter** manipuluje model i aktualizuje view.

**MVVM (Model-View-ViewModel):** View **nepřistupuje k modelu přímo**; **ViewModel** vystavuje properties/commands a zajišťuje **data binding** (to chce Macák slyšet). Známé z WPF/Angular/Vue.

![[mvc_mvp_mvvm.png]]

> [!tip] MVC vs. tiers/layers (Ošlejšek) MVC je vzor pro **interakci/prezentaci** (typicky v presentation layer), zatímco layered/tiered architektura dělí celý systém na **vrstvy abstrakce**. Lze je kombinovat (MVC klient + vrstvený server). **Layered** = logické vrstvy; **tiered** = fyzické rozdělení (deployment).

### 5.8 Broker

Strukturuje **distribuované** systémy s oddělenými komponentami komunikujícími přes vzdálené volání. **Broker** koordinuje komunikaci (najde server, předá zprávu). Prvky: Clients, Servers, Broker, **Client-side / Server-side proxy**, Bridges (propojení dvou brokerů). Příklady: **CORBA, ESB (Enterprise Service Bus)**, Apache Camel, Mule. + lokační transparentnost, interoperabilita. − efektivita, fault tolerance, ladění.

![[broker_architecture.png]]

### 5.9 Microkernel (Plug-in)

Jádro = **minimální funkcionalita** + **plug-in komponenty** (samostatné, ideálně bez vzájemných závislostí, lze přidávat/odebírat za běhu). Registry plug-inů (jméno, kontrakt, protokol). Příklady: Eclipse, VS Code, webové prohlížeče, IDE. + portabilita, rozšiřitelnost, oddělení mechanismů (jádro) a politik (plug-iny). − více meziprocesní komunikace.

![[microkernel_architecture.png]]

### 5.10 Pipes & Filters (Pipeline)

Zpracování **proudu dat**; každý krok = **filter**, propojené **pipes**. Filtry: **Producer (source)**, **Transformer (map)**, **Tester (reduce)**, **Consumer (sink)**. Filtry jsou samostatné, většinou bezstavové, dělají jednu úlohu. Aktivní vs. pasivní filter; push-only / pull-only / pull-push pipeline. Příklady: UNIX shell roury, OpenGL pipeline, Java streams, MapReduce, CEP. + flexibilita, znovupoužití, paralelizace. − sdílení stavu, overhead transformace dat.

![[pipes_and_filters_architecture.jpg]]

### 5.11 Blackboard

Pro problémy bez deterministické strategie. **Blackboard** (centrální úložiště + slovník), **Knowledge sources** (nezávislé subsystémy komunikující jen přes blackboard), **Control** (monitoruje a plánuje). Příklady: AI systémy, Bayesovské sítě. + experimentování, znovupoužití. − testování, nezaručené řešení, efektivita.

### 5.12 Monolit → SOA → Microservices (Batko, Dohnal)

**Monolitická architektura** — celá aplikace jako jeden nasaditelný celek. Zahrnuje styly: **layered, pipeline, microkernel**.

- nízké náklady, jednoduchost, dobrá volba pro malé apps / startovní bod / nejasné požadavky.
- pád jedné části shodí celek, dlouhý startup, špatná škálovatelnost, horší modularita/elasticita.

**SOA (Service-Oriented Architecture)** — systém složený ze **služeb** s definovanými rozhraními; **distribuovaná** (to chce Batko slyšet — rozdíl od layered). Přístupy budování: **top-down** vs. **bottom-up**. Komunikace SOAP/REST, často přes **ESB**.

**Microservices** — jemně granulované, nezávisle nasaditelné a škálovatelné služby, vlastní data store; vhodné s **DDD bounded contexts**.

**Vrstvená vs. SOA:** vrstvená je typicky monolitická a komunikuje jednosměrně mezi vrstvami; SOA je **distribuovaná**, služby komunikují přes síťová rozhraní.

![[monolith_soa_microservices.png]]

### 5.13 Domain-Driven Design — strategický návrh (PV293)

**DDD** = přístup k návrhu, který modeluje software podle domény na základě vstupu **doménových expertů**; iterativní spolupráce inženýrů a expertů.

**Klíčové pojmy:**

- **Ubiquitous Language** — společný rigorózní jazyk vývojářů a uživatelů; doménový model na něm stojí; vyvíjí se v čase.
- **Bounded Context** — explicitně vymezený rozsah, ve kterém platí **jeden** model (např. „product" ve skladu vs. v košíku). Musí být co nejvíce jednotný. **Bounded Context ≠ Module** (moduly jen vytvářejí jmenné prostory).
- **Context Map** — diagram bounded contextů a jejich propojení.
- **Subdomains**: **Core domain** (kde musí firma excelovat — strategická investice), **Supporting subdomain** (důležitá, ale ne core — custom/outsourcing), **Generic subdomain** (off-the-shelf).
- **Big Ball of Mud** — antipattern, kterému se vyhýbáme.

**Context Mapping patterns (vztahy týmů/kontextů):**

- **Partnership** — dva týmy uspějí/selžou společně, synchronizace.
- **Shared Kernel** — sdílený malý společný model (těžko se udržuje).
- **Customer-Supplier** — upstream (U) dodavatel, downstream (D) zákazník.
- **Conformist** — downstream se přizpůsobí upstream modelu (upstream nemá motivaci pomáhat).
- **Anticorruption Layer (ACL)** — nejvíce defenzivní; překladová vrstva izolující downstream model od cizích konceptů.
- **Open Host Service** — otevřený protokol/API ke kontextu jako sada služeb.
- **Published Language** — dobře dokumentovaný jazyk výměny (JSON Schema, Swagger, Protobuf, Avro).
- **Separate Ways** — integrace by nepřinesla užitek.

### 5.14 DDD — taktický návrh (PV293)

- **Entity** — modeluje individuální věc s **unikátní identitou (ID)**; obvykle **mutable** (stav se mění v čase); rovnost dle identity; delší životní cyklus (persistence v DB).
- **Value Object** — **immutable** koncepční celek **bez identity**; rovnost dle hodnot atributů; popisuje/kvantifikuje entitu; kratší životní cyklus.
- **Aggregate** — shluk doménových objektů jako **jeden celek**; jedna entita je **Aggregate Root**; může obsahovat Value Objects. Aggregate je základní jednotka načítání/ukládání; **transakce nesmí překračovat hranice agregátu**.
- **Domain Event** — něco, co se stalo v minulosti (sloveso v minulém čase), o čem chtějí vědět jiné části domény; implementuje side-effects napříč agregáty; pro škálovatelnost **eventual consistency**.
- **Command** — instrukce k provedení operace mezi agregáty (může být odmítnut validací).
- **Event Sourcing** — místo ukládání stavu agregátu se ukládají **všechny domain events** (event store). **Projection** = read-only denormalizovaný pohled odvozený z eventů (často cachovaný); může jich být víc. **Snapshot** pro výkon.

**Čtyři pravidla agregátů:**

1. Chraň byznys invarianty uvnitř hranic agregátu.
2. Navrhuj malé agregáty (rychle se načítají, méně paměti).
3. Odkazuj na jiné agregáty **jen přes identitu (ID)**.
4. Aktualizuj jiné agregáty přes **eventual consistency**.

**Event Storming** — rychlá technika návrhu zapojující doménové experty i vývojáře; lepící lístky podle barev (oranžová = domain event/minulý čas, modrá = command, žlutá = aktér, fialová = policy/process, růžová = bounded context). Modeluje se zleva doprava v čase. **CQRS** (Command Query Responsibility Segregation) a SWOT analýza souvisí.

---

## 6. Komponentové systémy (Batko)

### 6.1 Komponenty vs. objekty/třídy

| |Objekty / třídy|Komponenty|
|---|---|---|
|Povaha|design units|„executable" jednotka běžící v runtime prostředí (middleware)|
|Přístup|přímý (rozhraní volitelné), white-box|nepřímý přes rozhraní/konektory, **black-box / gray-box**|
|Rozhraní|volitelné, více rozhraní|dobře definované komplexní kontrakty (IDL, WSDL)|
|Zdroj|viditelný kód, kompilován s kontextem|bez viditelného kódu, vyvíjen nezávisle|
|Životní cyklus|jednoduchý (kód→kompilace→instance)|složitý (specifikace→implementace→assembly→deployment→instance)|

- **Black-box** = jen dokumentovaná rozhraní; **Gray-box** = + přehled vnitřní architektury.

### 6.2 Proč komponenty

- **Reusability (znovupoužitelnost)** — návrh jednou, použití mnohokrát (ambiciózní „Holy Grail"); záležitost jednotlivé komponenty a jejích rozhraní.
- **Replaceability (zaměnitelnost)** — dnes primární cíl; věci se mění → komponenta musí jít snadno vyměnit; záležitost **architektury celého systému**.

### 6.3 Komponentové modely a frameworky

- **Modely/standardy**: komerční (CCM/CORBA, EJB/J2EE, COM+/.NET), akademické (Fractal, KobrA, PCM, SOFA, Darwin, Koala…). Liší se v: run-time vs. design-time, dynamické vs. statické, stateful vs. stateless, definice služeb (signatury / komunikační protokoly / pre-post podmínky / behaviorální popis), assembly (synchronní vs. asynchronní, flat vs. hierarchický, simple vs. multiple propojení).
- **Component framework (middleware/runtime)** — prostředí podporující komponenty daného standardu, řídí jejich interakci. Příklady: JBoss, GlassFish, Liferay.

### 6.4 UML komponentové diagramy

- **Komponenta** — zaměnitelná část systému poskytující realizaci sady operací.
- **Rozhraní** — kolekce operací poskytovaných (provided) nebo požadovaných (required) komponentou. Notace: **class-style** vs. **lollipop** (lízátko: kolečko = provided, půlkolečko = required).
- **Port** — má **identitu**; jiná komponenta komunikuje přes konkrétní port; může mít multiplicitu; připojeno provided i required rozhraní.
- **Part** — jednotka implementace komponenty (jméno + typ + multiplicita).
- **Konektory**: by interfaces (přes kompatibilní provided/required), direct (přímé propojení), **delegation** (propojení vnitřních a vnějších portů — šipka).

![[uml_component.png]]

### 6.5 Kontrakty a úrovně specifikace

Dva typy kontraktů (oba podporují **replaceability**, čím formálnější tím lépe → **OCL**):

- **Usage** — mezi rozhraním komponenty a jejími klienty (runtime kontrakt).
- **Realization** — mezi specifikací komponenty a její implementací (design-time kontrakt).

**Úrovně:** Component Specification → Component Interface (1..*) → Component Implementation → Installed Component → Component Object (instance).

**Component Interface** — runtime kontrakt s klientem; operace často se **pre/post podmínkami**; více rozhraní snižuje dopad změn (klient závisí jen na části funkcionality).

### 6.6 Od tříd ke komponentám (bottom-up)

- **Vertikální** modularizace (vrstvy abstrakce) × **horizontální** (v rámci jedné úrovně, NE hierarchická).
- **Balíčky (packages)** — logické seskupení dle funkcionality (use cases), vysoká vnitřní soudržnost, nízká vnější vazba.
- **„Data" asociace** (asociuji, protože objekt spravuji — často agregace) → řeš referenci na nižší datové vrstvě (např. `OrderItemLine` drží jen ID a instanci dodá DB / framework přes **dependency injection**).
- **„Funkční" asociace** (asociuji, protože potřebuji volat) → přesuň službu do **provided interface** komponenty; objekty identifikovány přes ID.
- **Dědičnost** → reorganizuj, nebo aplikuj **Object Adapter** (lze chápat i jako Proxy).

**Generická pravidla dekompozice:** vyhni se cyklům závislostí, drž rozumný počet komponent na úrovni (**5–10, ideálně 7** — kognitivní limit), použij vertikální strukturování / hierarchické komponenty.

> [!note] Co chtěl Batko slyšet Jak komponenta komunikuje s okolím (přes rozhraní/konektory), jak ji používám (přes **dependency injection**), co je třeba zajistit (že existuje a je dostupná), a **signatura komponenty** (co musí obsahovat, k čemu je, jak se generuje a kde se ukládá/poskytuje).

---

## 7. Rozhraní, kontrakty na úrovni rozhraní, OCL

### 7.1 Rozhraní

Přetížený pojem — různé úrovně: GUI (člověk↔počítač), API knihoven, **rozhraní komponent**, **nízkoúrovňové rozhraní objektů/tříd**.

Vlastnosti rozhraní objektů a komponent: „cedule" se seznamem služeb (signatura = operace + vstupní/výstupní parametry), kontakt s okolím, **veřejná viditelnost**, mohou specifikovat **kontrakty** (omezení použití), musí být dobře dokumentovaná.

**V UML:** třída se stereotypem `<<interface>>`; **realizace** (vs. specializace/dědičnost); class-style vs. lollipop. **Provided** (kolečko) vs. **required** (půlkolečko) rozhraní.

### 7.2 Kontrakt (Design by Contract)

**Kontrakt** = právní dohoda dvou stran, kde obě přijímají závazky a zakládají na nich práva. OO kontrakt umožňuje volajícímu a poskytovateli sdílet stejné předpoklady o třídě/komponentě.

**Kontrakt = dokumentace operací + parametry + omezení.** Při formulaci služby se zaměř na: podmínky, za nichž je služba poskytnuta, a výsledek.

### 7.3 Typy podmínek

|Podmínka|Kdy platí|Význam|
|---|---|---|
|**Invariant**|vždy, po celou dobu života instance|vlastnost, která se nikdy nemění|
|**Precondition**|**před** voláním operace|„práva" kontraktu — když platí, klient může službu použít|
|**Postcondition**|**po** vykonání operace|„závazky" kontraktu — poskytovatel garantuje splnění|

Pokud precondition není splněna, chování (výsledek) je nepředvídatelné.

### 7.4 Dědičnost kontraktů (Liskov)

- **Invariant** je vždy zděděn; podtřída ho může **zesílit (strengthen)**, ne zeslabit.
    - `Flight inv: duration < 4` → `JetFlight inv: duration < 2` OK; `< 5` ŠPATNĚ.
- **Precondition** může být **zeslabena (weakened) — kontravariance** v podtřídě.
    - `Super::foo pre: i > 1000` → `Sub::foo pre: i > 0` OK (zvládne stejné vstupy).
- **Postcondition** může být **zesílena (strengthened) — kovariance** v podtřídě.
    - `Super::foo post: result > 0` → `Sub::foo post: result > 1000` OK (volající dostane vždy > 0).

### 7.5 OCL (Object Constraint Language)

**OCL** = jazyk pro formulaci omezení s formální silou matematické notace a srozumitelností přirozeného jazyka. Doplňuje UML modely o informace; přesná specifikace kontraktů; **silně typovaný, deklarativní** jazyk. Komentář začíná `--`.

- **Historie**: 1995 (IBM, IBEL, pojišťovnictví); OCL definuje samotné UML od 1.2.
- **Účel**: doplnit informace k modelu, přesná specifikace kontraktů, testovací požadavky, zpřesnění sémantiky UML.

**Struktura výrazu:** **context** propojuje omezení s prvkem modelu (třída, association class, interface). Tři druhy: invariant, pre-condition, post-condition. Formulováno na úrovni tříd, sémantika na úrovni objektů.

**Syntaxe:**

```ocl
-- Invariant
context Flight inv: self.duration < 4
context Flight inv flightDuration: duration < 4   -- pojmenovaný

-- Pre/post
context Flight::shiftDeparture(t:Integer) pre: t > 0
context Flight::shiftDeparture(t:Integer):Time
   post: result = departTime@pre + t
```

- `self` — objekt, z něhož se omezení vyhodnocuje.
- `result` — výsledek operace (v post-podmínce).
- `@pre` — hodnota **před** vykonáním operace (jen v post-podmínkách).

**Základní typy a operace:** Boolean (`and, or, xor, not, implies, if-then-else-endif`), Integer (`*,+,-,/,abs`), Real (`floor`), String (`toUpper, concat`). Plus klasifikátory z modelu (atributy, query operace) a asociace (role names).

**Navigace přes asociace:** `context Flight inv: origin <> destination`. Chybí-li role name, použij jméno třídy na druhém konci malým písmenem. (Asociační třídy nemají role names → použij jméno třídy.)

**Kolekce** (navigace často vrací kolekce):

- **Set** (neuspořádaná, bez duplicit), **Bag** (neuspořádaná, s duplicitami), **Sequence** (uspořádaná, s duplicitami).
- Operace přes `->`:
    - `collect(expr)` — vrací **bag** hodnot výrazu (≈ **projekce** v relační algebře). Zkratka: `self.flights.airline`.
    - `select(expr)` / `reject(expr)` — pod/množina splňující/nesplňující podmínku (≈ **selekce**).
    - `forAll(expr)` — true, pokud platí pro všechny.
    - `exists(expr)` — true, pokud platí aspoň pro jeden.
    - `iterate(elem; acc = init | …)` — obecná iterace (zobecnění předchozích).
    - `isEmpty, notEmpty, size, sum, count(e), includes/excludes, includesAll, asSet, union, intersection, including/excluding, first`.

```ocl
context Airport inv:
   self.departingFlights->select(duration < 4)->notEmpty
context Airline inv:
   flights->select(maxNrPassengers > 150)->notEmpty
```

**Reference na stav:** `oclInState(s)`. **Type checking:** `oclIsTypeOf(t)`, `oclIsKindOf(t)`, `oclType`. **Lokální proměnné:** `let var : Type = expr in …`.

---

## 8. Quality Assurance (častá podotázka — Bühnová/Batko)

### 8.1 Proč QA

Cíl: garantovat kvalitu SW/služeb. Důvody:

- **SLA (Service-Level Agreement)** — zákazníci platí a chtějí záruky (dostupnost, výkon, …); SLA bývá spojeno s pokutami za porušení.
- **SaaS / cloud** — dlouhodobý kontinuální vývoj; klíčové škálovatelnost a udržovatelnost.

### 8.2 Jak měřit a garantovat kvalitu

|Metoda|Popis|
|---|---|
|**Monitoring a testování**|verifikace kvality existujícího systému; levné, populární, nepřesné výsledky|
|**Predikce z modelu**|odhad kvality systému ve vývoji; vyžaduje (zjednodušený) model; použitelné během celého návrhu|
|**Formální verifikace**|nejpřesnější, nejdražší; ověření předpokladů na dobře vypracovaném modelu|

**Komponentové systémy** se hodí pro predikci (jasné kontrakty, modely různých úrovní, vysokoúrovňová dekompozice dle architektury).

### 8.3 Atributy kvality

Performance (propustnost, response time), Reliability (bezporuchovost, dostupnost, robustnost, recovery), Security (confidentiality, integrity, availability), Scalability (zátěž, souběh, škálování dat), Maintainability (modifiability, adaptability).

> [!warning] Kvalitativní atributy jsou často protichůdné Nelze optimalizovat vše najednou (security × usability, performance × maintainability/reliability…).

### 8.4 Role ve vývoji komponent (časté UML diagramy)

|Role|Cíl|Diagramy|
|---|---|---|
|**Component Developer**|implementace a dokumentace komponent|class/object, component, interaction, activity, **state**|
|**Software Architect**|sestavení systému dle rozhraní, dokumentace konektorů|component, sequence|
|**Deployer**|výběr fyzických zdrojů, fyzická architektura|**deployment**|
|**Domain Expert**|analýza požadavků, hranice systému|**use case** (+ dokumentace), sequence|
|**QoS Analyst**|výběr atributů, analýza, interpretace|QoS evaluation model (automaty, Markov chains, layered queuing networks — Palladio)|

### 8.5 Specifikace QoS

- **Naivní**: anotace služby konkrétními čísly (response time max. 10 ms v 99,9 %). Problém: nelze garantovat čísla bez znalosti kvality externích služeb a prostředí.
- **Assume-Guarantee**: „pokud kontext (předpoklady) → garantujeme něco". Příklad: reliability CPU 94,33 % + Service2 93,77 % → reliability Service1 > 88,81 %.
- **Parametric**: neznámý kontext nahrazen volnými parametry; sensitivity analysis odhalí závislosti výsledků na předpokladech.

### 8.6 Tuning tactics (ladění architektury)

> Optimalizace vybraného atributu úpravou architektury. Není zaručena; hrozí zhoršení jiných atributů.

- **Performance**: zjednodušit konektory (méně adapterů/wrapperů), nabídnout více rozhraní pro různé kontexty, oddělit data od výpočtu, omezit broadcast, **synchronní → asynchronní** komunikace, alokovat blízce komunikující části k sobě.
- **Reliability**: kontrolovat externí závislosti, vystavit stav modulů + invarianty, error reporting (výjimky), kontrolovat spolehlivost v konektorech, vyhnout se single point of failure (replikace), auto-backup/recovery, health monitoring.
- **Scalability**: integrální moduly s jasným účelem, distribuovat datové zdroje, replikovat data, přímé závislosti → nepřímé (zprávy/broadcast), paralelní zpracování, eliminovat bottlenecky.
- **Maintainability**: rozdělit/sloučit zodpovědnosti, malé kompaktní moduly, izolovat data od výpočtu, interakční logiku do konektorů (funkčnost v modulech), eliminovat zbytečné závislosti, hierarchická architektura.

### 8.7 Clean Code, SOLID, refaktoring, testování (Batko)

- **Clean Code** — čitelný, srozumitelný kód (smysluplné názvy, malé funkce, žádná duplicita).
- **SOLID** — viz kap. 1.6.
- **Refaktoring** — změna struktury kódu beze změny chování (zlepšení udržovatelnosti).
- **Testování**: **jednotkové** (unit), **integrační**, **uživatelské/akceptační** testy; ladění a testování výkonu.

---

## 9. Příklady z praxe

### 9.1 GoF vzory v Java Core API

|Vzor|Příklad|
|---|---|
|Singleton|`Runtime.getRuntime()`, `Desktop.getDesktop()`|
|Builder|`StringBuilder`, `DocumentBuilder`|
|Abstract Factory|`DocumentBuilderFactory`|
|Adapter|`Arrays.asList()`|
|Bridge|`InputStreamReader(InputStream)`|
|Composite|`java.awt.Container#add(Component)`|
|Decorator|`BufferedWriter`, `PrintWriter`|
|Proxy|`Collections.unmodifiableList()`|
|Iterator|`Collection#iterator()` (umožňuje for-each)|
|Strategy|`Comparator` v `TreeSet`|
|Flyweight|`Integer.valueOf()` (cache −128..127)|
|Memento|`Serializable`, `Date#getTime/setTime`|
|Observer|`java.util.Observer/Observable`, `EventListener`|

### 9.2 Demo příklad: Public Transport Company

Průběžný příklad z PA103 — dopravní podnik (Driver, Dispatcher, Technician, Depot, Line, Stop, Vehicle: Tram/Bus/TrolleyBus). Ukazuje analytické vzory (Accountability pro přidělování práce, Observation pro záznam dat o řidičích, Account/Transaction pro počty vozidel v depu, Enterprise Segment pro analýzu zpoždění) i design vzory (Composite pro mapu linek, Strategy/State pro plánování směn, Iterator pro průchod zastávek).

### 9.3 Případová studie: Chemické struktury

ChemStructure → Molecule → Residue → Atom. Postupná aplikace vzorů: **Singleton** (Model jako přístupový bod), **Composite** (strom ModelElement), **Iterator** (průchod), **Strategy/Decorator/Flyweight** (pozice „třesoucích se" atomů, sdílení atomů stejného typu). Ukazuje, že **vzory se kombinují** a že existuje mnoho možných dekompozic lišících se vlastnostmi.

---

---

# Q&A — Otázky komise a odpovědi

> Níže jsou reálné otázky z komisí (téma analýza a návrh systémů) přepsané + stručné modelové odpovědi. Zkoušející v závorce.

### UML obecně a diagramy

**Q (Batko): UML — základní dělení (structural vs. behavioral), statické vs. dynamické, vazby v class diagramu, nakreslit sekvenční a use case.** A: Structural (statické): class, object, component, deployment, package. Behavioral (dynamické): use case, sequence, communication, state machine, activity. Vazby v class diagramu: asociace, agregace (◇), kompozice (◆), generalizace (▷), závislost (čárkovaná šipka), realizace. Sekvenční = lifelines + zprávy v čase; use case = systém + aktéři + UC + include/extend/generalizace.

**Q (Bühnová): UML obecně, nakreslit class diagram + popsat vazby, modely procesů, vyvažování modelů.** A: Viz vazby výše. Modely procesů = activity/BPMN. Vyvažování (balancing) = průběžná synchronizace a kontrola konzistence uvnitř i mezi modely (use case ↔ class ↔ sequence).

**Q (Bühnová): BPM obecně + nakreslit jednoduchý proces, vyjmenovat UML diagramy.** A: BPM modeluje byznys procesy; BPMN prvky: events, activities, gateways, sequence/message flow, swimlanes, data. UML diagramy viz dělení výše.

**Q (Ošlejšek): Use Case diagram — aktéři, typy vazeb, jak se dokumentuje, druhy vazeb.** A: Aktéři 3 typy (osoby, systémy, čas). Vazby: include (povinné), extend (podmíněné, extension points), generalizace (UC i aktéři). Specifikace UC: strukturovaný text (scénář) nebo sekvenční diagram. Pozor na extend — neříkat jen „rozšiřuje funkčnost".

**Q (Ošlejšek): Proč se u UC detailu používá spíš slovní popis místo diagramu?** A: Protože je to **kratší** a netřeba být formální vzhledem k účelu UC.

**Q (Ošlejšek): Stavový diagram — kdy se přechází ze stavu do stavu, události, akce, determinismus, dědičnost (podstavy). Activity diagram.** A: Přechody spouští události (s guardy), provádějí akce. Determinismus = nanejvýš jeden přechod při dané události. Podstavy: z libovolného podstavu lze ven mimo rodičovský stav. Activity = tok akcí, větvení, fork/join, swimlanes.

**Q (Ošlejšek): Interakční diagramy — sekvenční, podmínka/cyklus, vznik/zánik objektu za běhu, komunikační diagram a v čem se liší.** A: Sekvenční: combined fragments alt/opt/loop pro podmínky/cykly; `<<create>>`/`<<destroy>>` pro vznik/zánik. Komunikační: stejná informace, důraz na strukturu vazeb (číslované zprávy); od sekvenčního se moc neliší.

**Q (Ošlejšek): Object diagram a rozdíl mezi object a ERD; dynamické a statické modely.** A: Object diagram = snímek konkrétních instancí. ERD popisuje jen data (identita = PK), class/object má i metody (identita = adresa). Statické = class/object, dynamické = sequence/state/activity (Ošlejšek tím myslel UML).

**Q (Ráček): Data model, porovnání Class diagramu a E-R diagramu, normalizace (až 4NF).** A: Class má navíc **metody** (ne funkce/procedury). ERD = entity, atributy, vztahy, klíče. Normalizace 1NF–4NF odstraňuje redundance a anomálie.

**Q (Bartek): Modelování IS, konkrétně UML (v návaznosti na diplomku).** A: Popsat, které UML diagramy jsi použil a proč (use case pro požadavky, class pro doménu, sequence pro interakce, deployment pro nasazení).

**Q (Ráček): RSSS — UML, procesní diagramy: activity diagram, BPMN, use case.** A: Activity = tok aktivit; BPMN = byznys proces (events/activities/gateways/lanes); use case = funkční požadavky.

### Návrhové a architektonické vzory

**Q (Jakubíček / Rambousek / Macák): Návrhové vzory — co to je, proč je používat, rozdělení, vyber dva a popiš s příklady z praxe. Analytické vs. návrhové.** A: Vzor = osvědčené řešení opakujícího se problému (meta-model k instanciaci). Proč: udržovatelnost, společný slovník, rychlejší návrh, design confidence. Rozdělení: creational / structural / behavioral (+ class/object scope). Příklady: **Observer** (publish-subscribe, `EventListener`), **Strategy** (zaměnitelné algoritmy, `Comparator`), **Singleton** (`Runtime`), **Decorator** (`BufferedWriter`). Analytické vzory (Fowler) — funkční požadavky na konceptuálním modelu; návrhové (GoF) — dekompozice tříd.

**Q (Macák): Co jsou analytické a návrhové vzory, ke každému 2 příklady a co dělají.** A: Analytické (Fowler): **Party** (sjednocení osoba/organizace), **Accountability** (vztahy zodpovědnosti), **Observation/Measurement** (záznam hodnot). Návrhové (GoF): viz výše (Observer, Strategy, Adapter, Composite…).

**Q (Bühnová): Architektonické a návrhové vzory — vybrat MVC a nakreslit + vysvětlit, nakreslit SOA a vysvětlit rozhraní + příklad rozhraní, vyjmenovat návrhové vzory a jak je dělíme.** A: MVC = Model/View/Controller (Model notifikuje View přes Observer, View↔Controller 1:1). SOA = distribuované služby s rozhraními (SOAP/REST), příklad rozhraní = WSDL/REST endpoint. Dělení vzorů: creational/structural/behavioral.

**Q (Dohnal): MVC, SOA — popsat, proč, praktická aplikace. Softwarové architektury a návrhové vzory, příklad na každý typ.** A: MVC odděluje vstup/výstup, web frameworky (Spring MVC). SOA distribuované služby, top-down/bottom-up, ESB. Architektury: monolit/layered/SOA/microservices/MVC/broker/pipes&filters. Vzory — viz klasifikace s příklady.

**Q (Ošlejšek): Architektury — MVC, SOA, porovnání MVC a layered, porovnání MVC s vrstvami DAL/Business/Presentation.** A: MVC = vzor interakce (v presentation vrstvě), layered = dělení celého systému na vrstvy abstrakce. MVC ≠ DAL/Business/Presentation, ale dají se kombinovat (MVC klient + vrstvený server).

**Q (Pitner): Layered architectures (rozdíl vs. tier), MVC.** A: **Layered** = logické vrstvy (jednosměrná komunikace N→N−1), **tiered** = fyzické rozdělení (deployment). MVC viz výše.

**Q (Batko): SW architektury — od monolitu po layered po SOA. Vrstvená architektura, výhody (specializace týmů), jak komunikují vrstvy (jednosměrně), rozdíl layered vs. SOA (distribuovaná).** A: Monolit (jeden celek) → layered (vrstvy abstrakce, specializace týmů, jednosměrná komunikace) → SOA (distribuované služby). Rozdíl: SOA je distribuovaná.

**Q (Bühnová): Cloud, SOA a MVC obecně.** A: Cloud/SaaS = dlouhodobý vývoj, škálovatelnost. SOA = služby. MVC = oddělení prezentace.

**Q (Macák): MVC architektura — co s čím komunikuje, role controlleru (autorizace, routing); porovnání s MVVM (data binding); 4-vrstvá architektura (DAL, Infrastructure, Business, Presentation); perzistence a ORM, mapování M:N a 1:1, lazy loading.** A: V MVC controller přijímá vstup (routing, autorizace), mění model, model notifikuje view. MVVM: view nepřistupuje k modelu přímo, ViewModel + **data binding**. 4 vrstvy: Presentation → Business → Infrastructure → DAL. ORM mapuje objekty na tabulky (M:N přes spojovací tabulku, 1:1 přes sdílený/cizí klíč); **lazy loading** = data se načtou až při prvním přístupu.

### Komponenty, rozhraní, kontrakty

**Q (Batko): Komponentové systémy — jak komponenta komunikuje s okolím, jak ji používám, dependency injection, co zajistit (existence a dostupnost), signatura komponenty (co obsahuje, k čemu, jak generujeme, kde se ukládá).** A: Komunikace přes provided/required rozhraní a konektory (realizuje framework). Použití přes **dependency injection** (framework dodá instanci). Zajistit, že komponenta existuje a je dostupná. Signatura = popis rozhraní (operace + parametry + návratové typy), generuje se z IDL/specifikace, poskytuje ji framework/registry.

**Q (Ošlejšek): Komponenty, kontrakty, rozhraní — vysvětlit a jak se to používá.** A: Komponenta = zaměnitelná black-box jednotka v middleware. Rozhraní = provided/required sada operací (lollipop notace). Kontrakt = operace + parametry + omezení (invariant/pre/post), nejlépe v OCL; usage (klient↔rozhraní) a realization (specifikace↔implementace).

### Požadavky a vývoj

**Q (Spurný): Specifikace a řízení požadavků — typy požadavků, příklady FP a NFP. Use case (aktéři, include/extends, dědičnost). Jak definujeme jeden use case. User stories.** A: FP (co systém dělá) vs. NFP (omezení technologií) + extra-funkční (kvalitativní). UC viz výše. Definice UC = strukturovaný text/sekvenční diagram. User story = „Jako role chci cíl, abych přínos" + akceptační kritéria.

**Q (Ošlejšek): Modelování a řízení SW vývoje. Funkční a nefunkční požadavky. Class diagram s vazbami, use case.** A: FP/NFP viz výše. Class diagram s vazbami (asociace/agregace/kompozice/generalizace). Use case s aktéry a vazbami.

**Q (Ráček): Unified Process + agilní vývoj — fáze UP a příslušné UML diagramy, inkrementální a iterativní přístup, agilní vývoj obecně, SCRUM podrobněji.** A: UP fáze: Inception, Elaboration, Construction, Transition; iterativní + inkrementální. Diagramy: use case (požadavky), class/sequence (analýza/design), deployment (nasazení). Agilní = iterace + reakce na změnu; SCRUM: role (PO, SM, Team), artefakty (backlogy, increment), události (sprint, daily, review, retrospektiva).

**Q (Plhák): Jednotlivé fáze vývoje (Požadavky, Návrh…) podrobně, agilní vs. prediktivní metodiky.** A: Fáze: požadavky → návrh → implementace → testování → nasazení → údržba. Prediktivní (vodopád) = plán dopředu; agilní = iterativní, adaptivní.

**Q (Spurný): Životní cyklus SW a fáze, mapování na diplomku, typy požadavků, UML (Use Case, Class), testování (rozdělení testů).** A: Životní cyklus viz výše. Testy: unit / integrační / akceptační (+ výkonové).

### Kvalita

**Q (Batko): Kvalita kódu — principy Clean Code a SOLID, refaktoring. Testování (unit, integrační, uživatelské/akceptační), ladění a testování výkonu.** A: Clean Code = čitelnost, smysluplné názvy, malé funkce, DRY. SOLID viz kap. 1.6. Refaktoring = změna struktury beze změny chování. Testy: unit (jednotka) → integrační (spolupráce) → akceptační (uživatel). Výkon = stress/peak testing.

### Uživatelská rozhraní

**Q (Spurný): Uživatelská rozhraní — rozdíl mezi prototypem a wireframem, přínos dělat prototyp, User research.** A: Wireframe = statické rozložení (struktura); prototyp = interaktivní (low/hi-fi). Prototyp dřív než kód = levné ověření + zpětná vazba. User research = testování s uživateli (např. sledování pohybu očí).

**Q (Bártek): Uživatelská rozhraní.** A: Naladit se na očekávání — typy UI, prototypování, použitelnost, user research.

### Distribuované / middleware (přesahová témata)

**Q (Pastva): Distribuované systémy — SOAP vs. REST, HTTP metody v REST, RPC, MapReduce, GRID vs. Cloud, SOA.** A: SOAP = protokol s XML obálkou (WSDL); REST = architektonický styl nad HTTP (GET/POST/PUT/DELETE, stateless, zdroje přes URI). RPC = vzdálené volání procedury. MapReduce = map + reduce nad rozdělenými daty (selhání HW → přepočítá se úloha). GRID vs. Cloud (sdílení výpočtů vs. on-demand zdroje). SOA = distribuované služby.

**Q (Lexa): Middleware, SOA/microservices, rozdíl REST vs. SOAP.** A: Middleware = vrstva mezi aplikacemi/komponentami (komunikace, integrace). SOA = hrubozrnné služby; microservices = jemné, nezávisle nasaditelné. REST (styl, HTTP, JSON) vs. SOAP (protokol, XML, WSDL).

---

> [!success] Hotovo Tyto poznámky pokrývají všechny podotázky okruhu „Analýza a návrh systémů" včetně toho, na co se reálně ptají členové komise. Hodně štěstí u státnic!