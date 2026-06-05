# DEV 1 Programování a softwarový vývoj

> [!abstract] Zadání otázky **Nástroje a prostředí pro softwarový vývoj rozsáhlých systémů. Základní koncepty softwarových architektur z pohledu implementace. Vícevrstvá architektura moderních informačních systémů, architektura model-view-controller. Technologie a jazyky vhodné pro frontend a backend vývoj. Persistence, ORM. Příklady z praxe pro vše výše uvedené.** _(PA165 || PV179, volba Programování, SA200)_

> [!tip] Jak na to u komise (shrnutí z reálných zkoušení)
> 
> - Komise chce **základy + porozumění + příklady z praxe**, ne hloubku do nejmenších detailů.
> - Velmi často: _„vyber si jednu architekturu / jeden vzor a popiš ho"_. Stačí umět **mluvit obecně, výhody/nevýhody a jak to spolu komunikuje**.
> - U MVC chtějí **co s čím komunikuje**, u vrstvené architektury **že vrstvy komunikují jednosměrně** a **specializaci týmů**.
> - U ORM/perzistence: **princip, výhody, mapování vztahů (1:1, 1:N, M:N), lazy loading**.
> - Nebojte se nakreslit obrázek (vrstvy, MVC trojúhelník). Když se zaseknete, navádějí vás.

## 1. Nástroje a prostředí pro vývoj rozsáhlých systémů

Cílem nástrojů je zvládnout **složitost, velikost týmu a tempo změn** velkého systému. Patří sem:

### 1.1 IDE (Integrated Development Environment)

> [!quote] Reálná otázka (Bühnová, SWE1) Chtěla slyšet **nějaké nástroje pro vývoj (IDE, Git, …)**, víc se doptávala k IDE — _z praxe, s čím programátorovi pomáhá, jak využíváme jeho schopnosti_.

IDE = integrované vývojové prostředí, které sdružuje editor, kompilátor/build, debugger a další nástroje. Co programátorovi reálně usnadňuje:

- **Syntax highlighting** a **IntelliSense / autocomplete** (našeptávání API, parametrů).
- **Navigace v kódu** – go to definition, find usages, refaktoring (přejmenování, extrakce metody).
- **Statická analýza a linting** – upozornění na chyby ještě před spuštěním.
- **Integrovaný debugger** – breakpointy, krokování, watch proměnných.
- **Integrace s VCS, testy, package managerem, kontejnery**.
- **Generování kódu** (scaffolding) – např. v ASP.NET vygeneruje controllery/views.

Příklady: **Visual Studio / VS Code / Rider** (.NET, C#), **IntelliJ IDEA / Eclipse** (Java), **PyCharm** (Python).

### 1.2 Verzování (VCS) – Git

- Distribuovaný systém správy verzí; sleduje historii, umožňuje větvení (branch), slučování (merge), spolupráci v týmu.
- **Code review** přes pull/merge requesty (GitHub, GitLab, Bitbucket).
- Branching strategie: Git Flow, trunk-based development.

### 1.3 Build systémy a správa závislostí

- **NuGet** (.NET), **Maven/Gradle** (Java), **npm/yarn/pnpm** (JS), **pip/poetry** (Python).
- Sestavení, řešení tranzitivních závislostí, verzování knihoven.

### 1.4 CI/CD (Continuous Integration / Delivery / Deployment)

- Automatický build + testy při každém commitu (GitHub Actions, GitLab CI, Jenkins).
- Automatizované nasazení do prostředí (dev/staging/prod).
- Snižuje riziko integrace u velkého týmu.

### 1.5 Testování

- Frameworky: **xUnit / NUnit / MSTest** (.NET), **JUnit** (Java), **Jest** (JS).
- Mockovací knihovny: **NSubstitute / Moq** (.NET), **Mockito** (Java) – izolace závislostí v unit testech.
- Druhy: jednotkové, integrační, end-to-end, akceptační.

### 1.6 Ladění výkonu a monitoring

> [!quote] Reálná otázka (Bühnová, SWE1) Premostila k **performance** – jak se _monitoruje, testuje, jak řešíme performance issues z hľadiska softvéru_.

- **Profilery** (CPU, paměť) – najdou „hot path", úniky paměti.
- **Logování** (Serilog, log4j), **APM / monitoring** (Application Insights, Prometheus + Grafana, OpenTelemetry).
- **Zátěžové / výkonnostní testy** (k6, JMeter).
- Typické řešení výkonu: caching, optimalizace DB dotazů a indexů, asynchronní zpracování, škálování (horizontální/vertikální).

### 1.7 Infrastruktura pro velké systémy

- **Kontejnerizace** (Docker) – izolace, reprodukovatelnost prostředí.
- **Orchestrace** (Kubernetes) – škálování, self-healing, rolling updates.
- **Cloud** (Azure, AWS, GCP), **IaC** (Terraform).
- **Issue tracking / project management** (Jira, Azure DevOps).

---

## 2. Základní koncepty softwarových architektur (z pohledu implementace)

**Softwarová architektura** = množina zásadních rozhodnutí o struktuře systému: jaké jsou komponenty, jaké jsou jejich odpovědnosti, jak spolu komunikují a jaká pravidla/omezení platí. Mění se těžko, proto se rozhoduje brzy.

### 2.1 Klíčové principy

- **Separation of Concerns (SoC)** – každá část řeší jednu věc.
- **High Cohesion & Low Coupling** – uvnitř komponenty silně souvisí, mezi komponentami slabá závislost. (Explicitně zmiňováno v PV179 u návrhu služeb.)
- **Modularita, zapouzdření, abstrakce** – komunikace přes rozhraní, ne přes detaily.
- **DRY** (Don't Repeat Yourself), **KISS**, **YAGNI**.

### 2.2 SOLID

|Princip|Význam|
|---|---|
|**S** – Single Responsibility|Třída má jediný důvod ke změně.|
|**O** – Open/Closed|Otevřená rozšíření, uzavřená pro modifikaci.|
|**L** – Liskov Substitution|Potomek lze použít místo předka bez porušení chování.|
|**I** – Interface Segregation|Raději více malých rozhraní než jedno velké.|
|**D** – Dependency Inversion|Vyšší moduly nezávisí na nižších, oba závisí na **abstrakcích**.|

> [!note] Dependency Inversion (PV179) High-level třída nemá volat `new` na low-level objekty a starat se o jejich životnost – **má je dostat zvenku**.

### 2.3 IoC, DI a DI kontejner

- **Inversion of Control (IoC)** – „pattern", jak implementovat Dependency Inversion; obrací řízení toku/vytváření objektů. Varianty: Flow Inversion, **Creation Inversion** (Factory, Service Locator, Dependency Injection…).
- **Dependency Injection (DI)** – vezmu všechny závislosti modulu, vytvořím konkrétní objekty a předám je instanci modulu **zvenku**.
    - **Constructor Injection** (preferované) a **Setter Injection**.
- **DI Container** – jednoduchá realizace DI; nakonfiguruji závislosti a kontejner je (rekurzivně) rozřeší. V .NET vestavěný `IServiceCollection` (lifetimes: Singleton, Scoped, Transient).

### 2.4 Architektonické styly (přehled)

|Styl|Charakteristika|Výhody|Nevýhody|
|---|---|---|---|
|**Monolit**|Vše v jednom nasazeném celku|Jednoduchý vývoj/nasazení na začátku|Špatně škáluje, těžká údržba ve velkém|
|**Vrstvená (layered)**|Horizontální rozdělení podle odpovědností|Specializace týmů, testovatelnost, výměna vrstev|Režie, „prostupování" vrstvami|
|**Klient–server**|Klient žádá, server poskytuje|Centralizovaná logika/data|Server = úzké hrdlo / SPOF|
|**SOA**|Systém složený z **volně vázaných služeb** s rozhraními|Znovupoužitelnost, integrace heterogenních systémů|Komplexita, latence|
|**Mikroslužby**|Mnoho malých, nezávisle nasaditelných služeb|Nezávislé škálování a nasazení, polyglot|Distribuovaná složitost, eventuální konzistence|
|**Event-driven**|Komunikace přes události/zprávy|Volné vázání, škálovatelnost|Obtížné ladění, sledování toku|

> [!quote] Reálné otázky (DEV1/DEV2 — architektury)
> 
> - _Batko:_ monolit → vrstvená architektura (chce **specializaci týmů** + komunikaci **jednosměrně**) → **SOA** (chce slyšet, že je **distribuovaná**), rozdíl vrstvená vs SOA.
> - _Lexa:_ modely SW systémů – **klient–server, vícevrstvá, mikroservisní**; stačilo obecně popsat výhody/nevýhody.
> - _Dohnal/Ošlejšek:_ **softwarové architektury a návrhové vzory, příklad na každý typ**.

### 2.5 Návrhové vzory (design patterns)

Znovupoužitelná řešení opakujících se problémů. Dělení (GoF):

- **Tvořivé (creational):** Factory Method, Abstract Factory, Builder, **Singleton**, Prototype.
- **Strukturální (structural):** Adapter, **Facade**, Decorator, Proxy, Composite, Bridge.
- **Chování (behavioral):** **Observer**, **Strategy**, Command, Iterator, State, Template Method.

Vzory **z PV179** (umět vyjmenovat a vysvětlit):

- **Repository** – abstrakce nad přístupem k datům (CRUD na jednom místě, nezávislost na ORM).
- **Unit of Work** – sleduje změny entit v rámci transakce, na konci `Commit`.
- **Query Object** – reprezentuje dotaz (filtr, řazení, stránkování) jako objekt.
- **DTO (Data Transfer Object)** – přenos dat mezi vrstvami.
- **Facade** – jednoduché rozhraní nad více službami.
- **Identity Map** – jeden řádek DB = jedna instance entity v paměti.
- **Interceptor / Filter** – logika před/po akci (logování, autorizace).

> [!quote] Reálná otázka (Jakubíček/Rambousek/Macák, DEV1/DEV2) _„Co jsou návrhové vzory, proč je používat, jak je dělíme, vyber si dva a popiš + příklady použití v praxi."_ + UML: **strukturální vs behaviorální** diagramy.

---

## 3. Vícevrstvá architektura moderních informačních systémů

Nejčastější architektura podnikových IS. Pozor na rozdíl:

- **Tier (vrstva fyzická / nasazovací)** – běží na samostatném zařízení (klient / aplikační server / DB server). → **3-tier**.
- **Layer (vrstva logická)** – logické rozdělení kódu. Více layers může běžet v jednom tier.

### 3.1 Klasické 3 (–4) vrstvy
```
┌─────────────────────────────┐
│  Prezentační vrstva          │  UI: MVC/Web API, Razor/React/Flutter
├─────────────────────────────┤
│  Vrstva business logiky      │  Services, Façades, DTO, Query objects
├─────────────────────────────┤
│  Infrastructure / DAL        │  Repository, Unit of Work
├─────────────────────────────┤
│  Datová vrstva / Databáze    │  ORM → SQL
└─────────────────────────────┘
        závislosti směřují JEN DOLŮ (jednosměrně)
```

> [!quote] Reálná odpověď, kterou komise ocenila (Macák, SWE1/DEV1) 4-vrstvá architektura: **DAL → Infrastructure → Business → Presentation**. Vrstvy komunikují jednosměrně.

> [!important] Co komise chce slyšet
> 
> - **Vrstvy komunikují jen jedním směrem** (vyšší volá nižší, nikdy obráceně). PV292 (Flutter) to formuluje jako _„Never reverse usage (imports) direction"_.
> - **Výhody:** specializace týmů (každý tým jedna vrstva), oddělení zájmů, testovatelnost, **vyměnitelnost vrstvy** (např. změna UI nebo DB bez zásahu do zbytku).
> - **Nevýhody:** výkonová režie, riziko „anemického" modelu, prostupování změn přes všechny vrstvy.

### 3.2 Co která vrstva dělá (dle PV179 referenční architektury)

**Datová vrstva (DAL):**

- Vlastní implementace DAL: třída zapouzdřující přístup k DB (Select/Insert/Update/Delete přes `SqlConnection`/`SqlCommand`) → plná kontrola, ale hodně práce.
- Typicky místo toho **ORM** (EF Core) + vzory **Repository**, **Unit of Work**, **Query**.

**Business vrstva:**

- **Services** – aplikační logika, dekomponovaná podle Single Responsibility (High Cohesion, Low Coupling).
- **Façades** – jednoduché rozhraní pro prezentační vrstvu, kombinují více služeb; fasád je výrazně méně než služeb.
- **DTO + mapování** – aby vrstva nad business nebyla závislá na entitách (typicky 1 DTO = 1 use case; jedna entita se mapuje na více DTO). Mapovací knihovny: **Mapster**, AutoMapper.

**Prezentační vrstva:**

- **Web API** (HTTP služby, REST) nebo **MVC** (viz dále). Vrací data jako JSON/XML + HTTP status code.

---

## 4. Architektura Model–View–Controller (MVC)

MVC odděluje prezentační vrstvu na tři odpovědnosti:

- **Model** – data + (často) doménová/aplikační logika; to, co se zobrazuje.
- **View** – prezentace (HTML), vykreslí model uživateli.
- **Controller** – přijme vstup uživatele, rozhodne, co se stane, naplní model a vybere view.

![[mvc.png|650]]

### 4.1 Tok v ASP.NET Core MVC (dle PV179)

```
Uživatel → (interakce s UI)
   → Routing k příslušné akci controlleru
       → ověří se práva a validita vstupních dat
       → akce se vykoná (zavolá business vrstvu)
       → vybere View a předá mu Model
   → Uživatel vidí novou stránku
```

> [!quote] Reálná otázka (Bühnová, DEV1) _„Vyber MVC, nakresli a vysvětli, co s čím komunikuje."_ — chce **trojúhelník Controller ↔ Model ↔ View** a roli každé části. _(Macák, SWE1):_ detailněji **role controlleru** (autorizace, routing), porovnání s **MVVM** (hlavně **data binding**).

### 4.3 Příbuzné vzory

- **MVVM (Model–View–ViewModel):** mezi View a Model stojí ViewModel; klíč je **data binding** (View se automaticky aktualizuje při změně ViewModelu). Typické pro WPF, Blazor, DotVVM, mobilní UI.
- **MVP (Model–View–Presenter):** Presenter řídí View přes rozhraní; časté u desktop/Android.

### 4.4 Web API a REST

- Web API = jednoúčelové HTTP služby, jednoduchá konfigurace, **REST**.
- **Richardsonův model zralosti REST:**
    - Level 0 – 1 HTTP metoda, 1 URI;
    - Level 1 – 1 metoda, n URI (resources);
    - Level 2 – více HTTP metod, n URI;
    - Level 3 – + content negotiation a **HATEOAS**.
- **HTTP metody:** GET (read), POST (create), PUT (replace), PATCH (update), DELETE.
- **HTTP status kódy:** 200 OK, 201 Created, 400 Bad Request, 403 Forbidden, 404 Not Found…

---

## 5. Technologie a jazyky pro frontend a backend

### 5.1 Backend

|Platforma|Jazyk|Hlavní framework|ORM|
|---|---|---|---|
|**.NET** (PV179)|C#|ASP.NET Core (MVC, Web API, Blazor)|Entity Framework Core|
|**JVM** (PA165)|Java|Spring Boot / Jakarta EE|JPA / Hibernate|
|**Node.js**|JS/TS|Express, NestJS|Prisma, TypeORM|
|**Python**|Python|Django, FastAPI, Flask|Django ORM, SQLAlchemy|
|Další|PHP, Go, Ruby|Laravel, Gin, Rails|Eloquent, GORM, ActiveRecord|

Backend řeší: business logiku, persistenci, API (REST/GraphQL/gRPC), autentizaci a autorizaci, integrace.

**Autentizace / autorizace (PV179 – Identity):**

- **Authentication** = ověření identity; **Authorization** = určení přístupových práv.
- **ASP.NET Identity** – správa účtů, RBAC, claims.
- **OAuth 2.0** – standard pro **delegovanou autorizaci** (uživatel nesdílí heslo s aplikací); grant types/flows (Authorization Code, Client Credentials, Resource Owner Password…).
- **OpenID Connect (OIDC)** – identitní vrstva nad OAuth 2.0, umožňuje **SSO**; discovery endpoint `/.well-known/openid-configuration`.
- **JWT (JSON Web Token)** – self-contained, digitálně podepsaný; 3 části (header.payload.signature); claims `iss`, `aud`, `sub`, `exp`, `iat`, `scope`…
- **Roles vs Claims vs Policies:** role = skupina oprávnění; claims = vlastnosti uživatele (flexibilnější); policies = pravidla pro povolení/zamítnutí.
- **Ukládání hesel:** nikdy ne v kódu/plaintextu → **hashování** (jednosměrné, ověření porovnáním hashů), 2FA. _„Don't roll your own crypto."_ Rozdíl **encoding (Base64 – bez bezpečnosti) vs encryption (vratná s klíčem) vs hashing (nevratné)**.

### 5.2 Frontend

- **Základ webu:** HTML (struktura), CSS (vzhled), JavaScript (chování). _(PV179 přímo odkazuje na w3schools jako self-study.)_
- **SPA frameworky:** React, Angular, Vue (komponenty, virtuální DOM, reaktivita).
- **Fullstack frameworky:** Next.js, Nuxt.js, SvelteKit
- **Cross-platform / mobil:** **Flutter** (PV292), React Native, .NET MAUI.
- **Nativní mobil:** Swift (iOS), Kotlin (Android)

> [!quote] Reálná otázka (Macák, SWE1) Začalo se **platformami a jejich specifiky** → konkrétní **frameworky a jazyky** → MVC → MVVM (data binding) → 4-vrstvá architektura → persistence + ORM. _(Batko, DEV4):_ čím se liší UI pro **telefon / web / desktop** – telefon má **dotyk místo myši** a přístup ke **kameře/poloze**.

---

## 6. Persistence a ORM

### 6.1 Persistence

**Persistence** = trvalé uchování dat (přežijí běh aplikace), typicky v databázi. Datová vrstva ji zapouzdřuje, aby zbytek aplikace nezávisel na konkrétním úložišti.

### 6.2 ORM (Object-Relational Mapping)

Technika převodu dat mezi **objektovým** světem (třídy, dědičnost, reference) a **relačním** světem (tabulky, řádky, cizí klíče) – řeší tzv. **impedance mismatch**.

|Výhody|Nevýhody|
|---|---|
|Výrazně méně kódu v DAL|Pozor na cenu/výkon dotazů|
|Práce s objekty místo SQL|Těžší manuální optimalizace|
|Změna tracking, migrace|Omezená funkcionalita oproti raw SQL|

Příklady ORM: **Entity Framework Core**, NHibernate (.NET); **Hibernate / JPA** (Java); SQLAlchemy (Python). **Micro ORM** (Dapper, PetaPoco) – rychlejší, jednodušší, omezená funkcionalita, bližší raw SQL.


**Migrace** – aktualizace schématu DB **bez ztráty dat**; každá migrace ví, jak se dostat na novou i zpět na předchozí verzi (`add-migration`, `update-database`). **Seedování** dat přes `OnModelCreating`.

### 6.4 Dotazování

- **SQL** – databázově závislé, bez kontroly kompilátorem.
- **LINQ to Entities** – IntelliSense, kontrola kompilátorem (preferované).
- **Vyhodnocení dotazu:**
    - **na serveru** – filtrování v DB, `IQueryable` (data nejsou v paměti),
    - **na klientovi** – filtrování v paměti, `IEnumerable`.
- Operace: `ToList/First/Find` (select), `Add/AddRange` (insert), `Update` (update), `Remove` (delete).

### 6.5 Vztahy a načítání (committee favorit)

> [!quote] Reálná otázka (Macák, SWE1) Persistence a ORM – **výhody, princip**, detaily k **mapování M:N a 1:1**, snažil se vytáhnout i **lazy loading**.

**Mapování vztahů:**

- **1:1** – jedna entita má referenci na druhou; FK na jedné straně (často sdílený PK).
- **1:N** – jedna entita má kolekci (`List<B>`), druhá referenci + FK.
- **M:N** – přes **spojovací (join) tabulku**; EF Core ji umí i implicitně, klasicky se modeluje explicitní spojovací entitou se dvěma FK.

**Načítání navigation properties:**

- **Eager Loading** – vrátí `A` rovnou i s `B` (`Include`).
- **Explicit Loading** – `B` se dotáhne explicitně (`Load`).
- **Lazy Loading** – `B` se načte automaticky při prvním přístupu (`virtual` + proxy); pozor na **N+1 problém**.

### 6.6 Transakce a ACID (rychle)

- **Transakce** = atomická skupina operací; **ACID**: Atomicity, Consistency, Isolation, Durability.
- EF Core: `SaveChanges` běží v jedné transakci; lze i explicitně.

### 6.7 Java varianta (PA165 – obecná znalost)

- **JPA** = specifikace; **Hibernate** = nejčastější implementace.
- `EntityManager` ≈ `DbContext`; anotace `@Entity`, `@Id`, `@OneToMany`, `@ManyToMany`, `@JoinColumn`.
- Dotazy: JPQL / Criteria API; Spring Data repozitáře (`JpaRepository`) ≈ Repository pattern.

---

## 7. Příklad z praxe – propojení všeho

E-shop jako vícevrstvá webová aplikace:

1. **Frontend (prezentační vrstva):** React/Flutter/MVC View zobrazí katalog. Uživatel klikne „Koupit".
2. **MVC Controller / Web API** přijme HTTP POST, ověří autorizaci (`[Authorize]`, JWT/Identity), zvaliduje vstup (`ModelState`), namapuje request na **DTO**.
3. **Business vrstva (Service / Façade)** spočítá cenu, ověří sklad, zaznamená objednávku – volá DAL.
4. **DAL (Repository + Unit of Work)** přes **EF Core** vytvoří entitu objednávky, EF ji přeloží do SQL a v jedné **transakci** uloží do DB.
5. **Persistence:** data trvale v relační DB; vztah Objednávka–Položky (1:N), Produkt–Kategorie (M:N).
6. **Nástroje kolem:** kód v Gitu, IDE s debuggerem, CI/CD nasadí kontejner do Kubernetes, monitoring sleduje výkon, profiler najde pomalý dotaz → přidá se index.

---

## Q&A – co se reálně ptala komise (a odpovědi)

> [!note] Pozn. Otázky jsou z reálných zápisů (okruh **SWE / DEV – Programování a softwarový vývoj** a sousední architektonické dotazy). Číslování okruhů se mění mezi roky, soustřeď se na obsah.

**Q1 (Bühnová): Programování a SW vývoj – jaké znáš nástroje pro vývoj? IDE – s čím pomáhá programátorovi? Jak se monitoruje/testuje výkon a řeší performance issues?** A: Nástroje = IDE, Git, build/package manager (NuGet/Maven/npm), CI/CD, testovací a mockovací frameworky, profilery, monitoring, Docker/K8s. IDE pomáhá: IntelliSense, navigace, refaktoring, debugger, statická analýza, scaffolding. Výkon: profiling (CPU/paměť), logování + APM (App Insights, Prometheus), zátěžové testy (k6/JMeter); řešení = caching, optimalizace dotazů/indexů, async, škálování.

**Q2 (Macák): Platformy a jejich specifika → frameworky a jazyky → MVC (role controlleru) → MVVM (data binding) → 4-vrstvá architektura → persistence + ORM (M:N, 1:1, lazy loading).** A: Platformy (web, desktop, mobil) mají různá UI a omezení; FE jazyky/frameworky (HTML/CSS/JS, React/Angular/Flutter), BE (.NET/C#, Java/Spring). MVC: Controller řeší routing, autorizaci, validaci, naplní Model a vybere View. MVVM přidává ViewModel + **data binding**. 4 vrstvy: DAL → Infrastructure → Business → Presentation, jednosměrné závislosti. ORM mapuje objekty na tabulky; 1:1 (sdílený/jeden FK), M:N (join tabulka), **lazy loading** = navigation property se načte až při přístupu (pozor na N+1).

**Q3 (Bühnová): Architektonické a návrhové vzory – vyber MVC, nakresli a vysvětli; nakresli SOA a vysvětli rozhraní; vyjmenuj návrhové vzory a jak je dělíme.** A: MVC trojúhelník (Controller přijme vstup → Model → View vykreslí). SOA = volně vázané služby komunikující přes definovaná rozhraní (např. REST/SOAP API, WSDL kontrakt). Vzory: tvořivé (Factory, Singleton, Builder), strukturální (Facade, Adapter, Decorator), behaviorální (Observer, Strategy, Command).

**Q4 (Dohnal / Macák): Softwarové architektury a návrhové vzory – příklad na každý typ.** A: Architektury: monolit, vrstvená, klient-server, SOA, mikroslužby, event-driven (+ výhody/nevýhody). Vzory po kategoriích – ke každé jeden popsat: Factory (vytváření), Facade (zjednodušené rozhraní), Observer (notifikace o změně).

**Q5 (Ošlejšek): Návrh architektury – statické vs dynamické modely (UML), use case, object diagram vs ERD.** A: Statické modely = struktura (class diagram, object diagram), dynamické = chování (sequence, state, activity). Use case = aktéři + jejich cíle. Object diagram = instance tříd v čase; ERD = entity a vztahy na úrovni dat/DB.

**Q6 (Lexa): Modely softwarových systémů – popiš jednu architekturu (klient-server / vícevrstvá / mikroslužby).** A: Vícevrstvá: prezentační/business/datová vrstva, jednosměrné závislosti; nemusíš znát přesné názvy vrstev, ale **co se na vrstvě děje**. Výhody: SoC, testovatelnost, výměna vrstev. Nevýhody: režie.

**Q7 (Batko): Co je softwarový vývoj? Fáze? Architektury – monolit → vrstvená (výhody, jak komunikují vrstvy) → SOA (rozdíl vůči vrstvené).** A: SW vývoj = proces od požadavků přes návrh, implementaci, testování po nasazení a údržbu. Architektury: monolit (vše pohromadě), vrstvená (specializace týmů, vrstvy komunikují **jednosměrně**), SOA = je **distribuovaná**, služby přes síťová rozhraní.

**Q8 (Jakubíček / Rambousek): Návrhové vzory – co to je, proč, dělení, vyber dva a popiš + příklady; UML strukturální vs behaviorální diagramy.** A: Viz Q3/Q4. UML strukturální: class, component, deployment, object; behaviorální: use case, sequence, activity, state.

**Q9 (Macák/PV179): Persistence a ORM – princip, výhody, mapování 1:1 a M:N, lazy loading.** A: Persistence = trvalé uložení dat. ORM převádí objekty ↔ tabulky (řeší impedance mismatch), méně kódu, ale opatrně s výkonem dotazů. 1:1 / 1:N / M:N (join tabulka). Lazy/eager/explicit loading; lazy = automaticky při přístupu.

**Q10 (obecné k DAL/EF): Proč vlastní Repository/Unit of Work, když mám DbSet/DbContext?** A: Kvůli **abstrakci nad konkrétním ORM** (lze ho vyměnit kvůli výkonu), modifikace dat/dotazů na jednom místě, přehlednější transakce a lepší testovatelnost (mockování).

**Q11 (Identity/PV179): Authentication vs authorization; jak bezpečně ukládat uživatele/hesla?** A: Authentication = ověření identity, authorization = určení práv. Hesla se **hashují** (jednosměrně, při loginu se porovná hash), 2FA, žádné vlastní crypto. OAuth2 = delegovaná autorizace, OIDC = identita nad OAuth2 (SSO), JWT = podepsaný token s claims.

---

> [!success] Minimální „must-know" pro odpověď
> 
> 1. Umět nakreslit **vrstvy** a **MVC trojúhelník** a říct **kdo s kým komunikuje** (jednosměrně).
> 2. Vyjmenovat **architektonické styly** a **3 kategorie návrhových vzorů** + příklad.
> 3. **DI/IoC** jednou větou (závislosti dostává modul zvenku).
> 4. **ORM**: co to je, výhody/nevýhody, 1:1 / 1:N / M:N, lazy vs eager loading.
> 5. **FE vs BE** technologie + jeden příklad z praxe.