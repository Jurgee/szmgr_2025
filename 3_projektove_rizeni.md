# **Projektové řízení** (PA179)

> Plánování, řízení rizik, role modelů v projektovém řízení. Ganttovy diagramy, síťová analýza, metoda kritické cesty (CPM), Program Evaluation and Review Technique (PERT). Mezinárodní standardy a metodiky projektového řízení (PMI Project Management Body of Knowledge, PRINCE 2). Příklady z praxe pro vše výše uvedené. [PA179](https://is.muni.cz/auth/el/fi/jaro2022/PA179/um/)

1. [Plánování, řízení rizik, role modelů v projektovém řízení](#plánování-řízení-rizik-role-modelů-v-projektovém-řízení-14)
2. [Ganttovy diagramy, síťová analýza, metoda kritické cesty (CPM), Program Evaluation and Review Technique (PERT)](#ganttovy-diagramy-síťová-analýza-metoda-kritické-cesty-cpm-program-evaluation-and-review-technique-pert-24)
3. [Mezinárodní standardy a metodiky projektového řízení (PMI Project Management Body of Knowledge, PRINCE 2)](#mezinárodní-standardy-a-metodiky-projektového-řízení-pmi-project-management-body-of-knowledge-prince-2-34)
4. [Příklady z praxe pro vše výše uvedené](#příklady-z-praxe-pro-vše-výše-uvedené-44)

## Plánování, řízení rizik, role modelů v projektovém řízení (1/4)

### Plánování

Je třeba rozlišovat mezi:

- **Projekty**
    - dočasné - mají start i konec, typické fáze (příprava, provedení, uzavření, případně fáze sw životního cyklu)
    - přinášející změnu, dodávají hodnotu stakeholderům (všem zúčastněným; uživatelům, zákazníkům i dodavatelům produktu...)
    - unikátní - každý má svá specifika (požadavky, zákazníky, tým...), nejedná se o každodenní rutinnou práci firmy
    - plánované
    - opakovatelné prvky projektu (či celé projekty) jsou **procesy** - řízené událostmi, bývají dobře definované (jak postupovat), vizualizované flow chartem
      > proces je opakovatelná série aktivit s definovanými vstupy, výstupy, nástroji, technikami...
    - bývají spojené s rizikem - spojené s unikátností (nikdy jsme to nedělali), deadliny, děláme nějakou změnu
    - **řízení**
        - balanc mezi časem, cenou a rozsahem/kvalitou
        - začneme užíváním standardů (PRINCE2, PMBOK, IPMA), abychom efektivněji komunikovali, koordinovali, zvýšili důvěru stakeholderů, transparentnost, abychom znovu neobjevovali vymyšlené
- **Programy** - skupina dočasných, vzájemně provázaných projektů řízená jako skupina abychom dosáhli cílů, které projekty sdílí
    - **řízení**
        - správa rizik
        - odstraňování omezení a konfliktů z projektů
        - v projektech se přemýšlí a plánuje i s ohledem na jiné projekty programu
- **Portfolio** - skupina projektů a programů, řízená k dosažení dlouhodobých strategických cílů
    - a.k.a. co dlouhodobě nabízíme
    - **řízení**
        - monitoringem výkonu firmy
        - výběrem a prioritizací programů a projektů

Pro konkrétní projekt je potřeba si zvolit vhodný přístup **prediktivní** nebo **agilní** [viz otázka 2](2_softwarove_inzenyrstvi.md).

#### Agilní plánování

Agilní plánování je průběžné a adaptivní. Na začátku se stanoví vize produktu, cíle projektu a vytvoří se product backlog. Detailně se plánuje pouze nejbližší období (release, sprint), zatímco vzdálenější části projektu zůstávají na vyšší úrovni abstrakce a zpřesňují se postupně podle získaných informací.

#### Prediktivní plánování

Prediktivní plánování se snaží vytvořit detailní plán projektu již na začátku. Provádí se analýza požadavků, definice rozsahu, tvorba WBS, odhady pracnosti a nákladů, plánování aktivit a zdrojů. Výsledkem bývá harmonogram projektu (např. Ganttův diagram), rozpočet a plán řízení rizik. Konkrétní dokumentace závisí na použité metodice (PMBOK, PRINCE2 apod.).

Dělá se **projektový plán** (proč, co, kdo, kdy, jak, za kolik...) - **Work Breakdown Structure** tvořená ze specifikačního dokumentu, počítá se čas a cena jednotlivých **Work Packages** (součást WBS, nejnižší jednotka) třeba pomocí [PERT](#program-evaluation-and-review-technique-pert), jejich závislosti, tvoříme rozvrh (gantt/network diagram), přiřazujeme odpovědnosti (mělo by jít snadno najít aktivity člověka i všechny spojené s aktivitou). 

**Pozor na rozdíl Člověkoden (Man-Day / MD) vs. Kalendářní čas:** MD je jednotka **úsilí (pracnosti)**, nikoliv času. Úkol o velikosti 10 MD znamená, že zabere 10 dní *jednomu* člověku. Pokud na něj nasadíme 5 lidí, může teoreticky trvat jen 2 kalendářní dny. Do kritické cesty (CPM) se vždy dosazuje kalendářní *délka trvání*, ne absolutní úsilí. (Pozor na *Brooksův zákon*: Přidání lidí do zpožděného SW projektu ho obvykle ještě více zpozdí kvůli režii na komunikaci).

#### Chronologický postup prediktivního plánování (Státnicový chyták)
Zkoušející často nachytají studenty na otázce: „Co uděláte při plánování jako první? Gantt, nebo síťový graf?“ Správná odpověď je **WBS**. Postup musí jít striktně v tomto pořadí:
1.  **Dekompozice rozsahu (WBS):** Rozbití celého systému na nejmenší řiditelné celky (**Work Packages**). Bez toho nelze plánovat dál.
2.  **Odhad pracnosti a zdrojů:** Každému Work Package přiřadíme potřebné úsilí (např. pomocí PERT) a určíme, kdo ho bude dělat.
3.  **Sestavení síťového grafu:** Definujeme logické závislosti mezi úkoly (co na co navazuje).
4.  **Výpočet kritické cesty (CPM):** Zjistíme matematické trvání projektu a kritické úkoly.
5.  **Vykreslení do časové osy (Ganttův diagram):** Teprve na základě všech předchozích kroků vzniká Gantt jako vizuální kalendářní plán.

*WBS*

![](img/20230526000518.png)

### Řízení rizik

Postup:

1. **Identifikace rizik**
    - čerpáme z předchozích zkušeností, lessons learned... co a proč se stalo?
2. **Ohodnocení rizik**
    - každé riziko způsobí náklady, můžeme pro něj odhadnout cenu
    - každému přiřadíme pravděpodobnost a kritičnost dopadu, určíme následky
      ![](img/20230525214112.png)
3. **Určení odezev na rizika**, možnosti:
    - **akceptuj** - no tak se to stane, no, nevadí, náklady na prevenci by byly vyšší než samotné riziko
    - **vyhni se** - nastavení plánu, aby problém nemohl nastat (např. použití jiné technologie, která tento problém nemá)
    - **přesuň** (třeba na někoho jiného) - např. tento komponent outsourcujeme se solidním SLA, nebo se pojistíme
    - **zmenši** - sniž pravděpodobnost/míru dopadu rizika, třeba důkladnějším systémem reviews
4. **Stanovení monitoringu rizik**
    - stanovení odpovědnosti za monitoring rizik
    - určení, kde budou rizika definována, kdy budou revidována a upravována
5. **Vytvoření registru rizik**

Možné zdroje rizik (nejčastější tučně):

- **uživatel** - **nemožnost/neochota zapojit se**, odpor ke změnám
- **požadavky** - **špatně pochopené**, blbě definované, nejasné či neadekvátní, **přijdou změny** (mnohdy až v momentě, kdy mohou zásadně narušit vyvíjený systém)
- **složitost projektu** - komplexní doména, použití nové/nezavedené technologie
- **management** - **neefektivní řízení**, špatně zvolená/použitá metodika/standard, **špatný odhad nákladů/zdrojů/času**, špatně určená komunikace, **nezkušený manažer**
- **tým** - nezkušenost, **málo lidí**, osobní konflikty
- **firemní prostředí** - nestabilní, změna vedení...
- **subdodavatelé** - opoždění, nedostatečná kvalita, komunikace...

#### Specifika prevence u agilního řízení rizik

Prevence:

- **Transparence a zpětná vazba**, abychom předešli nedorozumění v týmu
- **Používání user stories** - jsou snadno pochopitelné pro zákazníka, dají se dobře ověřovat
- **Jasná definice, co znamená "hotovo"**
- **Krátké iterace** - brzo zjistíme, co je případně blbě

### Role modelů v projektovém řízení
V softwarovém projektovém řízení slouží modely především k **odhadování pracnosti (effort), času a ceny** projektu na základě historických dat a metrik. Zkoušející chtějí slyšet tyto dva hlavní přístupy:

* **Metoda funkčních bodů (Function Points - FP):** Odhaduje velikost SW z pohledu uživatele (počet vstupů, výstupů, dotazů, interních souborů). Je nezávislá na technologii.
* **Model COCOMO (Constructive Cost Model) a COCOMO II:** Algoritmetický model, který odhaduje pracnost v člověkoměsících (Person-Months) a kalendářní čas na základě velikosti kódu (KLOC - tisíce řádků kódu) a produktivních faktorů (zkušenost týmu, složitost platformy).

COCOMO rozlišuje **3 vývojové módy (kontexty projektu)**, na které se doc. Ráček velmi často ptá:
1.  **Organic (Organický):** Malé projekty, známé prostředí, malý a zkušený tým, flexibilní požadavky (např. interní firemní nástroj). Nízká režie řízení.
2.  **Semi-detached (Polorozdělený):** Střední projekty, smíšený tým (zkušení i nezkušení), část požadavků je striktní, část volnější. Vyšší nároky na koordinaci.
3.  **Embedded (Vestavěný / Integrovaný):** Komplexní projekty s extrémně přísnými omezeními (např. řízení letového provozu, bankovní jádro, embedded medicínský software). Požadavky jsou pevné, procesy rigidní, obrovská režie na testování a dokumentaci.

Dále sem spadají **matematické modely řízení** (síťové grafy CPM/PERT), které modelují projekt jako matematický graf a hledají v něm kritická místa (úzká hrdla).

## Ganttovy diagramy, síťová analýza, metoda kritické cesty (CPM), Program Evaluation and Review Technique (PERT) (2/4)

### Ganttovy diagramy

- nástroj pro plánování (nejen) projektů

V základu toto:

![](img/20230525192847.png)

ale lze rozšířit...

- y osa obsahuje úlohy (případně zdroje)
- x osa zobrazuje čas
- úloha jako uzel/obdélník (šířka udává časovou náročnost), případná hrana značí vztah
- je možné přidat:
    - milestones
    - progress
    - zdroje (kdo co dělá, dávají se místo událostí na osu y), pak řešíme problém plánování job-shopu
    - omezení, precedenční podmínky (úloha musí být započata až po dokončení jiné úlohy, něco je možné paralelizovat...)
- obvykle minimalizujeme makespan (čas dokončení poslední úlohy a tedy i celého projektu)

![](img/20230525195955.png)

### Síťová analýza

Je to způsob, jak si rozplánovat projekt jako **síť úkolů**, které na sebe navazují.

Metody pro modelování souboru činností vedoucích k dosažení nějakého cíle (tj. projektů).

Cílem je projekt naplánovat, minimalizovat prostoje a náklady, určit termíny, celkovou dobu trvání projektů, identifikovat kritické úlohy v projektu.

Používá se pro to síťový graf hranově/uzlově orientovaný - úlohy jsou na hranách/uzlech. Uzlově orientovaný umožňuje snadno modelovat precedenční podmínky, lze snadno použít pro metodu kritické cesty.

* **AON (Activity-on-Node / Uzlově orientovaný):** * **Uzly** představují samotné aktivity (činnosti, např. "Programování backendu").
    * **Hrany (šipky)** představují logické závislosti mezi nimi.
    * *Využití:* Mnohem častější v moderním softwaru (Jira, MS Project), protože se v něm snadno modelují složitější závislosti (např. SS, FF).
* **AOA (Activity-on-Arrow / Hranově orientovaný):**
    * **Hrany (šipky)** představují samotné aktivity, které spotřebovávají čas a zdroje.
    * **Uzly** představují **události / milníky (milestones)** – okamžik, kdy jedna činnost končí a druhá začíná (mají nulové trvání).
    * *Využití / Výhoda:* Zkoušející chtějí slyšet, že AOA se skvěle hodí pro přirozené zobrazení milníků přímo v grafech. Někdy vyžaduje zavedení "fiktivních hran" (dummy activities) s nulovým časem pro zachování logiky grafu.

### Metoda kritické cesty (CPM)

Metoda pro identifikaci vzájemně závislých aktivit, které mají vliv (jsou kritické) na dobu dokončení projektu a nemohou být opožděny bez prodloužení dokončení projektu.

#### 4 typy precedenčních závislostí (Chyták na tabuli)
Při výpočtu CPM na tabuli vám zkoušející nemusí dát jen klasickou následnost. Musíte znát všechny čtyři typy vazeb:
* **FS (Finish-to-Start / Konec-Start):** Nejběžnější. Úloha B může začít až poté, co úloha A skončí (např. Testování začne až po dokončení Implementace).
* **SS (Start-to-Start / Start-Start):** Úloha B může začít hned, jakmile začne úloha A. Mohou běžet paralelně (např. S programováním frontendů se může začít hned, jak se začne programovat backend).
* **FF (Finish-to-Finish / Konec-Konec):** Úloha B může skončit až tehdi, kdy skončí úloha A (např. Dokumentace celého systému může být hotová/skončená až v momentě, kdy skončí implementace poslední komponenty).
* **SF (Start-to-Finish / Start-Konec):** Velmi vzácná. Úloha B může skončit až poté, co úloha A začne.

[Postup](https://www.youtube.com/watch?v=4oDLMs11Exs):

- uděláme si graf závislostí, určíme si dobu trvání aktivit
- v prvním průchodu jdeme start => konec, řešíme earliest start/completion time. Když vedou 2 do 1, bereme maximum těch 2.
- v druhém průchodu jdeme konec => start, řešíme latest completion/start time. Když vedou 2 z 1, bereme minimum těch 2.
- kritická cesta obsahuje aktivity, které mají earliest & latest finish time identický
- slack/float udává, o kolik můžeme danou aktivitu opozdit, aniž by došlo ke zpoždění projektu (`latest completion time - earliest completion time`)

![](img/20230526101347.png)

### Program Evaluation and Review Technique (PERT)

Technika k odhadu času k dokončení tasku. Bereme **optimistický** odhad, **pesimistický** odhad a **nejpravděpodobnější** odhad:

`očekávaný = (optimistický + 4 * nejpravděpodobnější + pesimistický) / 6`

Pokud máme informace o úrovni platů implementátorů tasků, můžeme dopočítat odhadovanou cenu.

## Mezinárodní standardy a metodiky projektového řízení (PMI Project Management Body of Knowledge, PRINCE 2) (3/4)

- standardy projektového řízení PRINCE2, PMBOK, IPMA ICB popisují obecnější způsob řízení
- metodiky sw vývoje (RUP, SCRUM) řeší řízení v rámci vývojového týmu, jsou specifické pro vývoj SW

![](img/20230525184623.png)

### PMI Project Management Body of Knowledge (PMBOK)

- **procesně orientovaný** standard, podrobně popsaná sada good practices
- snadno se používá jako handbook pro vhodné znalostní oblasti a nástroje/techniky při životním cyklu projektu
- vhodný, když:
    - manažer potřebuje tipy na nástroje a techniky, jaké by měl použít, ale aspoň trochu tuší co a jak

49 procesů (série aktivit s definovanými vstupy, výstupy, nástroji a technikami) dělených do:

- **5 procesních skupin** - logické dělení procesů podle fází (inicializace, plánování, provedení, monitoring a řízení, uzavírání)
- **10 vědomostních oblastí/disciplín** projektového managementu, každá má vlastní procesy:
    - **Integrace**
        - tvorba **Project Charter**u:
            - **Business case (proč)** - cíle projektu, hrubá cena, rozpočet, rizika
            - **Project outcome (co)** - popis, hlavní cíle a požadavky
            - **Stakeholders (kdo)** - externí i interní, řešíme jejich role, potřeby, zapojení a odpovědnosti
            - **Management approach (jak)** - popis použitých standardů, nástrojů, metodik, životního cyklu projektu...
            - **Schedule (kdy)** - hrubý plán projektu, fáze, milestones, Ganttův diagram...
    - **Rozsah (scope)** - sesbírání požadavků, definice, validace a řízení rozsahu funkcionalit systému, tvorba Work Breakdown Structure
    - **Plán** - definice a určení pořadí aktivit, odhady časů aktivit, tvorba a řízení plánu
    - **Cena** - odhad cen a rozpočtu aktivit nebo jednotek práce pomocí Work Breakdown Structure, řízení ceny a rozpočtu
    - **Kvalita** - plánování, řízení a kontrola kvality
    - **Zdroje** - odhad nepeněžních a lidských zdrojů, jejich získávání a řízení, tvorba a správa týmů
    - **Komunikace** - plán, správa a kontrola komunikace a informací o projektu
    - **Riziko** - identifikace, kvalitativní (míra dopadu) a kvantitativní (pravděpodobnost) analýza rizik, jejich monitoring, plán a procesy reagující na rizika
    - **Dodavatelé** - produkty a služby pocházející mimo náš tým, kontrakty, objednávky, SLAčka, výběr dodavatelů, monitoring výkonu dodavatelů
    - **Stakeholdeři** - zúčastněné osoby; jejich identifikace, plánování a správa zapojení stakeholderů do projektu

### PRINCE 2 (PRojects IN Controlled Environment)

- standard pro řízení obecného projektu
- předepsaný postup, krok za krokem (spousta formulářů na vyplňování, checklisty)
- součástí není správa požadavků, rozpočtování
- vhodný pro:
    - nutnost velkého reportování
    - nutnost kompletní projektové dokumentace
    - tým vyžaduje řád a kontrolu
    - manažery s málo zkušenostmi, hodí se mu podrobný popis postupu

#### Fáze

(hrubě odpovídá UP inception, elaboration, construction a transition):

![img.png](img.png)

- **Starting up**
    - tvorba **Project brief**:
        - řešíme feasibilitu, zachycujeme klíčové požadavky, rizika
        - popis významných požadavků s dopadem na architekturu
        - identifikace actorů
        - identifikace dalších systémů, se kterými máme komunikovat
        - na konci známe cíle, hrubou architekturu
        - co se používá pro podobné systémy? s čím máme zkušenosti?
        - určení použitých technologií
        - určení orientační ceny, časového plánu a rizik
    - plán další fáze:
        - **Work Breakdown Structure**
        - identifikace aktivit, dependencí
        - odhad trvání aktivit, stanovení milestones
        - definice rolí a odpovědností
        - tvorba rozvrhu (Gantt/síťový diagram)
- **Initiation**
    - tvorba **Project Initiation Documentation** (dokument/více dokumentů):
        - obsahuje současný stav projektu, plány, Kdo, Co, Kdy, Jak, Proč, Za Kolik...
        - slouží k definici projektu, určení rámce...
        - schvaluje product board
        - detailní Business Case (důvody projektu, očekávání, cost-benefit analýzu, časovou škálu, ceny, rizika)
        - popis struktury managementu, rolí týmu
        - popis přístupu ke kvalitě, změnám, riziku, komunikaci
        - plán projektu
    - plán další fáze
- **Delivery**
    - obvykle má více částí (iterací), každá max 3 měsíce, každá má definované měřitelné a ověřitelné milestones
    - produktový manažer se stará o udržení ceny, termínů, rozsahu a kvality specifikované v PID
    - produktový manažer autorizuje, provádí reviews work packages, reportuje (pravidelně) status, změny, problémy a kvalitu výš, spravuje rizika a problémy
    - týmový manažer provádí týmové plánování (jednotlivých work packages), demonstruje kvalitu produktu, zajišťuje dodání work packages
    - mezi fázemi se hodnotí končící fáze a plánuje (zase WBS, gantt) další, aktualizuje se PID
- **Close**
    - předání produktu (samozřejmě opět spousta protokolů), nasazení, uzavření všech dokumentů, PID, dokumentace, tvorba end report a lessons learned
    - případné předání projektu ops a maintenance týmu
    - tvorba SLA

#### 7 principů

(vše máme nějak zdokumentované):

- **Kontinuální odůvodnění projektu** - proč to děláme?
- **Učení se ze zkušeností** - co (ne)fungovalo
- **Role a odpovědnosti** - přesně specifikovaná struktura týmu, vymezené práva a odpovědnosti
- **Řízení po fázích** - po každé fázi děláme review Project brief, provádíme reporting vyššímu managementu
- **Manage by exception** - řízení soustřeďujeme na části, které se nějak (negativně) vymykají. Nezasahujeme do toho, co funguje. Vytyčíme cíle a tolerovatelné odchylky v kvalitě, času, ceně a rozsahu, určíme zodpovědnosti za nepřekračování
- **Důraz na produkt** - primární cíl je produkt, ne práce
- **Přizpůsobení metodiky projektu** - není nutné PRINCE používat úplně doslovně, řádek po řádku. Ne všechny formuláře jsou vždy zcela relevantní

#### 7 témat

- **Business case** - obsahuje očekávané přínosy, rizika, časový a cenový rozsah, důvody projektu... Měl by být neustále aktualizován a držen validní po celou dobu projektu
- **Organizace** - definice rolí a odpovědností, typy stakeholderů (dodavatel, business/zákazník, uživatel), 3 úrovně managementu (project board pro směrování projektu (obsahuje exekutivu, senior suppliera, senior usera), project manager pro řízení projektu, team manager pro dodávání produktu), manage by exception
- **Kvalita** - monitoring, akceptační kritéria, určíme si strategii řízení kvality (nástroje, procesy), řešíme kvalitu produktu i manažerských výtvorů (plány, reporty)
- **Plány** - plánujeme projekt i jednotlivé fáze, Gantt diagram, Work Breakdown Structure je základem plánování
- **Rizika** - identifikace možných rizik, určujeme způsob reakce na dané riziko na základě ceny prevence, pravděpodobnosti a dopadu, uchováváme registr rizik
- **Změny** - u požadavků na změnu řešíme prioritu, dopad, kritičnost, zkoumáme možnosti řešení, dle změny upravujeme záznamy a plán
- **Postup projektu** - porovnáváme realitu s plány (čas, cena, kvalita, rozsah, rizika...), sledujeme zda stále projekt splňuje business case

#### 7 procesů

![](img/20230525115631.png)

- **Úplný začátek projektu** - nastínění business case, přiřazení klíčových vedoucích osob, studování "lessons learned" předchozích podobných projektů, získání autorizace product boardu
- **Inicializace projektu** - příprava strategií řízení (rizik, kvality, komunikace, konfigurace), projektového plánu, konkretizace business case, založení dokumentace
- **Řízení fáze** - řeší produktový manažer, monitoring, reportování významných událostí, řídíme exceptions, revidujeme a schvalujeme práci/nové části produktu
- **Řízení dodání produktu** - to samé co řízení fáze, ale řeší to týmový manažer
- **Směrování projektu** - vysokoúrovňová rozhodnutí, funguje po celou dobu projektu, plán nadcházející fáze, na konci projektu autorizujeme uzavření
- **Řízení mezi fázemi (managing a stage boundary)** - plán nadcházející fáze, řeší produktový manažer, aktualizace business case a projektového plánu, report předchozí fáze
- **Uzavření projektu** - řeší projektový manažer, evaluace, předání produktu, návrh board na ukončení

### IPMA ICB

*V otázce není, ale není na škodu znát*

- obecný standard pro vedení projektu
- na rozdíl od většiny ostatních obsahuje podrobnou sekci o soft skills
- vhodný, když:
    - projekt vyžaduje dobré soft-skills (komunikace, leadership, řešení konfliktů)
    - manažer je zkušený, zná procesy
    - není nutná spousta reportingu
- vhodné pro použití jako handbook pro různé manažerské kompetence
- kompetenční přístup, pro každou ICB popisuje požadované dovednosti a schopnosti, popis a metriky indikátorů kompetence
  > kompetence je aplikace znalostí (knowledge, informace & zkušenosti), dovedností (skill, schopnost aplikovat znalosti) a schopností (ability, použití dovedností efektivně, ve správný čas a na správném místě) k dosažení kýženého výsledku
    - kompetence perspektivy - metody a techniky pro interakci jedinců s prostředím
    - lidské kompetence - techniky pro jednání s jedinci/skupinami
    - praktické kompetence - metody a techniky pro úspěch projektu

### Metodiky

Popsány v [otázce 3](2_softwarove_inzenyrstvi.md) (metodiky sw vývoje jako RUP, SCRUM)

## Příklady z praxe pro vše výše uvedené (4/4)

### Specifika IT projektů

v porovnání s většinou průmyslových odvětví:

- nepřesné/neznámé, časté a měnící se požadavky
- větší nutnost přizpůsobení produktu
- velká složitost
- náročné testování
- neustálý a rapidní vývoj technologií
- možnost globální spolupráce
- projekty mohou v rámci portfolia ovlivnit ostatní projekty (zvlášť při selhání)
- nutnost řízení rizik
- dokončené projekty je často třeba servisovat/poskytovat podporu

### IT Infrastructure Library (ITIL)

Best practices pro **řízení IT služeb**:

**Fáze:**
- **Service strategy** - požadavky, strategie pro zajištění kýženého, finance, co vlastně budeme dělat
- **Service design** - Service Level Agreement, řešení rizik, security & business compliance
- **Service transition** - jak měníme stávající služby, řešení deploymentu, uložení získaných znalostí pro budoucí projekty
- **Service operation** - dokumentace pro uživatele/helpdesk, řešení incidentů/změnových požadavků/problémů, řešení identit a přístupu k systému
- **Continual service improvement** - monitoring, logování, aktualizace běžící služby

### Praktické příklady aplikace

#### Příklad použití CPM v praxi

**Situace:** Vývoj webové aplikace pro e-commerce

**Aktivity a závislosti:**
- A: Analýza požadavků (5 dní)
- B: Design databáze (3 dní, po A)
- C: Design UI/UX (4 dny, po A)
- D: Implementace backend (8 dní, po B)
- E: Implementace frontend (6 dní, po C)
- F: Integrace (3 dny, po D a E)
- G: Testování (4 dny, po F)

**Kritická cesta:** A → B → D → F → G (23 dní)
**Slack:** C a E mají 2 dny slack

#### Příklad PERT odhadu

**Task:** Implementace platebního systému

- **Optimistický odhad:** 8 dní (vše jde hladce)
- **Nejpravděpodobnější:** 12 dní (standardní průběh)
- **Pesimistický:** 20 dní (komplikace s API, bezpečnost)

**PERT odhad:** (8 + 4×12 + 20) / 6 = 76/6 ≈ 12,7 dní

[Go to the next question](./4_databaze.md)
