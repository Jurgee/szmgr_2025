# 5 Distribuované systémy

> [!abstract] Zadání státnicové otázky **Základní pojmy, principy. Rozdíl mezi centralizovanou a distribuovanou architekturou systému, nevýhody obojího a jejich překonávání. Replikace, sdílení dat. Architektura orientovaná na služby (SOA), webové služby. Příklady existujících technologií a jejich využití. Příklady z praxe pro vše výše uvedené.**

> [!tip] Na co se komise reálně ptá (souhrn z protokolů)
> 
> - **Dohnal / Bühnová / Čeleda / Lexa / Oujezský / Pastva / Walletzký** = hlavní zkoušející této otázky.
> - Skoro vždy: **porovnat centralizovaný vs distribuovaný systém**, **škálovatelnost (horizontální × vertikální)**, **replikace / sdílení (sharding) dat**, **SOA + ESB**, **SOAP vs REST**, **cloud (IaaS/PaaS/SaaS)**, **virtualizace**.
> - Oblíbené „nachytávky“: že **horizontální škálování je obecně těžší** (ne jednodušší); že nevýhoda SOA je **multiple points-of-failure**; vždy zmínit **loosely coupled**; **eventuální konzistence** a **master–slave** replikace při výpadku; u **MapReduce** co se stane při výpadku HW uzlu.
> - Klíčové: **vše umět vysvětlit na příkladu z praxe** a ideálně napojit na vlastní diplomku.

---

## 1. Základní pojmy a principy

### Co je distribuovaný systém

- Skládá se z **účastníků (computers / nodes)** a **propojení (network)**.
- Komunikace mezi účastníky slouží k:
    - **paralelnímu řešení problémů**,
    - **využití zdrojů** dostupných na konkrétních místech,
    - **využití služeb** poskytovaných účastníky.
- Vyžaduje **specifické programovací nástroje** = middleware.

### Co je middleware

- **Softwarová „abstrakční vrstva“** mezi distribuovanou aplikací a sítí/OS.
- Implementuje **společnou funkcionalitu**, kterou by jinak musel řešit každý vývojář znovu.
- Vznik v **60. letech** (skrýval HW/OS), zpřesněji formulováno v **80.–90. letech** v kontextu distribuovaných aplikací.
- Definice (k zapamatování jedna „hláška“): _middleware je „lepidlo“ mezi dvěma samostatnými programy_; je to **„lomítko v Client/Server“**.
- **Schéma:** Aplikace ⟷ API ⟷ Middleware ⟷ (síť) ⟷ Middleware ⟷ API ⟷ Aplikace.

### Co middleware řeší (služby middleware)

Distribuovaná aplikace musí řešit opakující se problémy, které middleware poskytuje jako nástroje:

- **Lokalizace služeb** – discovery, description (popis).
- **Komunikace a synchronizace.**
- **Spolehlivost, transakční zpracování.**
- **Škálovatelnost, rozšiřitelnost.**

Různé abstrakce middleware (různé pro/proti – aplikovatelnost, škálovatelnost, znovupoužitelnost):

- **RPC** (vzdálené volání procedur),
- **objektové metody** (OO middleware – CORBA),
- **zprávy** (message-oriented),
- **komponenty**,
- **webové služby / SOA**.

### Kdy a proč middleware nasadit (applicability)

Důvody pro distribuovaný systém:

- **Vyšší dostupnost** (fault tolerance),
- **vyšší výpočetní / úložná kapacita**,
- **rychlý vývoj** (využití hotových služeb),
- **interoperabilita** (nabídnout funkcionalitu externím aplikacím).

Volba middleware – pozor na:

- funkcionalitu a vlastnosti, cílové platformy (OS, HW – i mobily),
- propojení s ostatními systémy,
- **proprietární implementace → vendor lock-in**.

### [!] Fallacies of Distributed Computing (klasické „omyly“)

Komise i přednáška to ráda připomíná – co **NEPLATÍ**, ale lidé to mlčky předpokládají:

1. Síť je spolehlivá.
2. Latence je nulová.
3. Šířka pásma je nekonečná.
4. Síť je bezpečná.
5. Topologie se nemění.
6. Existuje jeden administrátor.
7. Náklady na přenos jsou nulové.
8. Síť je homogenní.

> [!example] Příklad z praxe Stejný kód jako lokálně **nelze** prostě „rozsekat“ na síť – musíte počítat s výpadky, latencí, různým kódováním dat (endianita, kódování stringů), bezpečností a tím, že volání „selže“ způsobem, který v monolitu nikdy nenastane.

---

## 2. Architektury: centralizovaná vs distribuovaná

> [!question] Otázka komise (Dohnal, Bühnová, Oujezský, Lexa, Čeleda…) _„Porovnejte centralizovanou a distribuovanou architekturu, výhody/nevýhody, jak se nevýhody překonávají.“_ Vždy chtějí příklady.

**Software architecture** = základní organizace systému (high-level abstrakce). Ovlivňuje výkon, škálovatelnost, modifikovatelnost, spolehlivost, bezpečnost, udržovatelnost. Skládá se z **komponent a jejich vztahů** (dekompozice složitého problému, paralelní vývoj). **Návrhové vzory** řeší opakující se problémy.

Postupný vývoj architektur (Batko se rád ptá „od monolitu po SOA“):

### 2.1 Monolitická architektura

- Soběstačná, nezávislá; **spojuje UI, logiku i data** v jednom celku.
- ❌ Žádná modularita, žádné znovupoužití, **žádná škálovatelnost**, těžké testování, sporná spolehlivost.

### 2.2 Vrstvená / „tiered“ architektura

- Aplikace rozdělena do více **tierů (vrstev)**, které lze **distribuovat** a paralelizovat na úrovni vrstvy, nezávisle implementovat.
- Vrstvy snadno vyměnitelné přes **API**.
- **Client/Server** paradigma: tenký/tlustý klient, aplikační logika na serveru, víceuživatelský přístup.

> [!note] Layered × Tier (Pitner se na rozdíl rád ptá)
> 
> - **Layer (vrstva)** = logické rozdělení odpovědnosti (Presentation / Business / Data Access). Mohou běžet i na jednom stroji.
> - **Tier** = fyzické rozdělení / nasazení na samostatné stroje.
> - **Vrstvy komunikují jednosměrně** (shora dolů – prezentační → byznys → datová). To Batko 2026 vyloženě chtěl slyšet.

**One-tier (tenký klient):**

- ✅ Málo vývoje/údržby na klientu, snadné updaty.
- ❌ Drahá serverová strana, **single point-of-failure** (lze vylepšit – viz škálování/replikace).

**Two-tier (tlustý klient):**

- ✅ Lepší rozložení zátěže (část práce na klientu), prezentace přizpůsobitelná klientovi.
- ❌ Těsná vazba (tight coupling), klienta je třeba vyvíjet/nasazovat.

**Three (a více)-tier:**

- ✅ Serverové vrstvy lze rozdělit → lepší škálovatelnost a spolehlivost; prezentace může být na klientu.
- ❌ Složitost, problematické propojení vrstev.
- Typické vrstvy: **Presentation → (Application/Business) logic → Data access**.

### 2.3 Komponentová architektura

- **Komponenta** = softwarový balík; může vyžadovat jiné komponenty, poskytuje funkcionalitu.
- **Zapouzdřená**, interakce jen přes **API**; interní implementace skryta (data i algoritmy).
- **Vyměnitelná, znovupoužitelná, robustní** (testování, dokumentace).
- (Detail viz kap. 6 – komponentové middleware.)

### 2.4 Service-oriented Architecture (SOA)

> [!question] Otázka komise (Batko, Bühnová, Dohnal, Oujezský, Lexa, Walletzký…) _„Co je SOA, principy, výhody/nevýhody, co je ESB, příklad použití.“_

- Aplikace **složená z více nezávislých služeb**; služby propojeny přes **rozhraní**, potřeba **integrace a orchestrace**.
- Diagram z přednášky: služby (card billing, order processing, stock management, logging) komunikují přes **service bus (ESB)** pomocí **SOAP**; orchestrace přes **BPEL** (composed service).
- ✅ **Pros:** plně distribuovaná, snadná údržba.
- ❌ **Cons:** změny rozhraní rozbijí konzumenty; **nedostupnost služby** = **multiple points-of-failure** (to Batko vyloženě chce slyšet).

**Klíčové principy SOA (vždy zmínit „loosely coupled“):**

- **Loose coupling** – minimální závislosti, snadná údržba; konzument jen dodává data dle rozhraní.
- **Discovery** – registrace služeb, dostupná metadata.
- **Composition & reusability** – rychlá integrace, **otevřené standardy**.
- **Stub generátory** v různých jazycích → rychlý vývoj.
- **Service movability** – cloudové infrastruktury.
- **Statelessness** (bezstavovost).

> [!info] ESB (Enterprise Service Bus) Komunikační páteř SOA. Služby se nevolají přímo „bod–bod“, ale posílají zprávy na **sběrnici**, která řeší směrování, transformaci formátů, protokolovou konverzi a orchestraci. Umožňuje **loose coupling** mezi službami. (Příbuzné: **iPaaS** = cloudový ESB.)

### 2.5 Microservices

- Moderní „pod-typ“ SOA: aplikace složena z **malých nezávislých microservices** (UNIX filozofie _„do one thing and do it well“_).
- Není standardizováno, jen konsensuální; **vysoká koheze, nízká vazba**.
- **Lehká komunikace** – typicky **REST + JSON**.
- **Continuous delivery** – automatizované a nezávislé nasazení; změna ovlivní jen relevantní službu.

### 2.6 Peer-to-peer (P2P)

- Peer je zároveň klient i server.
- **Distribuované úložiště** (data u peerů), **replikace**, **směrovací (routing) protokoly** bez centrálního bodu, **self-organizing**.
- **Fault-tolerant, vysoce škálovatelné.**

### 2.7 Shared-Nothing architektura

> [!question] Souvisí s otázkou „sdílení / sharding dat“ (Dohnal 2018).

- **Nezávislé a soběstačné uzly (shards)** – nic se nesdílí → žádné soupeření o zdroje (contention).
- **„Nekonečně“ škálovatelné.**
- **Partitioning** – data rozdělena mezi shardy podle nějaké vlastnosti dat.
- Replikaci lze použít **jen pro read-only data**.

### Shrnutí: nevýhody a jejich překonávání

|Architektura|Hlavní nevýhoda|Jak se překonává|
|---|---|---|
|Monolit|Nelze škálovat, špatná údržba|Rozpad do vrstev / služeb|
|Centralizovaný (1-tier)|Single point-of-failure, drahý server|**Replikace, load balancing, vysoká dostupnost**|
|2-tier (tlustý klient)|Těsná vazba, deploy klienta|3-tier, tenký klient přes web/API|
|SOA|Multiple points-of-failure, závislost na rozhraních|Redundance/replikace služeb, verzování rozhraní, ESB|
|Distribuovaný obecně|Latence, výpadky, nekonzistence (viz _fallacies_)|Replikace, eventuální konzistence, ACK/retransmise, transakce|

---

## 3. Škálovatelnost a vysoká dostupnost (překonávání nevýhod)

> [!question] Otázka komise (Bühnová, Dohnal – velmi časté!) _„Škálovatelnost – horizontální vs vertikální. Vysoká dostupnost. Virtualizace.“_

### Vertikální vs horizontální škálování

- **Vertikální (scale up):** výkonnější stroj (víc CPU/RAM). Jednoduché, ale **má strop** a stroj je SPOF.
- **Horizontální (scale out):** přidání více strojů / virtuálních strojů.

> [!warning] Nachytávka (Bühnová 2020) Intuitivně se zdá horizontální škálování „jednodušší“ (přidám node do racku), ale **obecně je horizontální škálování NÁROČNĚJŠÍ** – musí se upravit architektura (rozdělení práce, konzistence dat). U centralizovaných systémů to stojí spoustu úsilí, pokud to vůbec jde efektivně (špatným rozdělením lze výkon i zhoršit).

### Nástroje vysoké dostupnosti (HA) v middleware

- **Name service / dynamic binding** – víc serverů registruje stejnou funkcionalitu → **load balancing** a **failover** (RPC kap. 6).
- **Replikace stavu** na více serverech (OO middleware, CORBA fault tolerance, redundance na úrovni objektů).
- **Container** komponentového middleware řeší balancing, high-availability, persistenci, transakce.
- U cloudu: automatické provisioning, redundance, **SLA**.

### Virtualizace (klíčová technologie cloudu i HA)

- **Odděluje OS a aplikace od hardwaru.**
- Více **OS instancí na jednom HW**; virtuální hosty lze **přesouvat** mezi fyzickými stroji.
- Základ **IaaS** a horizontálního škálování (alokace dalších VM).

> [!example] Příklad z praxe (Dohnal rád napojí na databáze a RAID) Vysoká dostupnost „nad databázemi“: read repliky (master–slave), failover na zálohu, **RAID** na úrovni disků:
> 
> - **RAID 1 (mirroring)** = zrcadlení dat → redundance/dostupnost (přežije výpadek disku).
> - **RAID 0 (striping)** = rozprostření dat přes disky → výkon, ale žádná redundance.
> - **Mirroring použiju**, když chci dostupnost/odolnost; **striping**, když chci propustnost.

---

## 4. Replikace a sdílení dat

> [!question] Otázka komise (Dohnal 2018 „data sharding“, Walletzký 2025 „replikace, sdílení dat“, Lexa 2025 „replikace, eventuální konzistence, master-slave“)

### Replikace

- **Stejná data uložena na více uzlech.**
- Účel: **dostupnost** (přežití výpadku uzlu), **výkon čtení** (víc replik = víc čtenářů), **odolnost proti chybám**.
- **Master–Slave (primary–replica):** zápisy jdou na master, čtení lze i z replik. Při výpadku master se povýší replika.
- ❗ Replikace přináší problém **konzistence**: po zápisu nemusí mít všechny repliky okamžitě stejná data.

> [!note] Eventuální konzistence (eventual consistency) V distribuovaném systému často nelze udržet silnou konzistenci a zároveň dostupnost (CAP – při výpadku sítě volíme mezi konzistencí a dostupností). **Eventuální konzistence** = repliky se _nakonec_ sjednotí, dočasně se ale mohou lišit. Lexa 2025 přesně tohle chtěl slyšet u řešení nedostupnosti dat při výpadku jedné instance (řešení = replikace, např. master–slave).

### Sdílení / rozdělení dat (sharding, partitioning)

- **Sharding / partitioning** = rozdělení dat mezi uzly podle vlastnosti dat (např. ID, region).
- **Shared-Nothing** (kap. 2.7): uzly nic nesdílí → bez soupeření → „nekonečná“ škálovatelnost; replikace pak jen pro read-only.
- Kompromis: **sharding** zvyšuje škálovatelnost zápisu, **replikace** zvyšuje dostupnost a škálovatelnost čtení; často se kombinují.

### Replikace v praxi – HDFS (Hadoop File System)

- Distribuovaný, vysoce škálovatelný, **fault-tolerant díky replikaci** – každý blok dat na **více data nodech**.
- Optimalizováno pro čtení; zápis **není konkurentní** (předpokládá se jeden zapisující na soubor).

### Ordering & synchronizace (Dohnal/komunikační detaily)

> Při sdílení dat napříč uzly je potřeba řešit **pořadí událostí** – nelze spoléhat na čas.

- **Logické hodiny – „happened before“**.
- **Lamport clock** – každý proces má čítač; při odeslání ++ a pošle se čas; při příjmu `max(time, msg)+1`. Dává **částečné (kauzální) uspořádání**: pokud a→b pak Time(a)<Time(b) (obráceně neplatí).
- **Vector clock** – vektor čítačů → **plné kauzální uspořádání** (a→b ⟺ VC(a)<VC(b)).
- **Centrální autorita (sequencer)** – **úplné uspořádání**, ale je to **single point of failure** (typické u databází – sekvence).
- **Source / Causal / Total ordering** – druhy garantovaného doručení.

---

## 5. Komunikace v distribuovaných systémech (kontext pro Dohnala)

> Dohnal otázku rád rozšíří na sítě a komunikaci.

- **Charakteristiky komunikace:** přenos dat mezi uzly, adresace, ošetření chyb a spolehlivost, řízení toku, QoS, uspořádané doručení, bezpečnost, **unicast / multicast / broadcast / anycast**.
- **Nespolehlivý unicast** (best effort, HW, typicky lokální segment; potřeba adresace, směrování, fragmentace – MTU).
- **IP:** adresace IPv4 (4 B) / IPv6 (16 B), ARP/NDP, směrování (statické/dynamické), fragmentace (MTU/PMTU).
- **Spolehlivý unicast** – řeší: poškození (checksum, parity, CRC, Hamming), ztrátu (ACK/NACK + retransmise dle timeoutu), duplicitu (unikátní ID), **flow control** (okno).
- **TCP spolehlivost:** kontrolní součty, sekvenční čísla, ACK (i bulk), dynamicky řízené okno (rychlost příjemce + congestion control).
- **Multicast** (jeden→více): šetří pásmo; spolehlivý multicast řeší ACK/NACK implozi (stromová/kruhová topologie, **RMP** s tokenem). Módy: nespolehlivý → spolehlivý neuspořádaný → source ordered → total ordered → K-resilient → safe delivery.
- **Bezpečnost komunikace:** důvěrnost, integrita, autenticita → kryptografie jako další vrstva.

---

## 6. Abstrakce middleware (technologie)

### 6.1 RPC – Remote Procedure Call

> [!question] Pastva 2025: _„Co je RPC, na co se používá.“_

- Umožňuje **volat funkce v jiném procesu** (typicky přes síť), **Client/Server**; sémantika volání je skryta frameworkem (vypadá to jako normální volání).
- Vznik v **80. letech**; pro OO → **remote method invocation**.
- **Princip:** klient volá `f(y)` ve **stubu** → **marshalling** argumentů do zprávy (network data representation) → odeslání serveru → server **unmarshalling**, zavolá funkci, výsledek marshalluje zpět → klient unmarshalluje a vrátí z volání.
- **Stub generation** přes **IDL** (interface definition language), compile/run time; různé jazyky a platformy.
- **Marshalling:** řeší rozdílné formáty (little/big endian, kódování stringů). „In“ parametry hodnotou, „out/inout“ se kopírují tam/zpět (callback).
- **Paralelismus:** threading modely – single-thread, thread per connection/request, thread pool (leader-follower).
- **Naming service:** server registruje funkce; klient získá handle (**dynamic binding**) → migrace serveru, **load balancing, vysoká dostupnost**.
- **Selhání:** stub musí klienta upozornit (výjimky/signály) – ztracený požadavek (resubmit, pokud nezměnil stav), ztracená odpověď (retransmise – server musí pamatovat), stavy → transakce.
- **Implementace:** Sun RPC (NFS, XDR), **Java RMI**, CORBA, DCOM, .NET Remoting, XML-RPC, **SOAP**, **gRPC**.

### 6.2 Objektově orientované middleware – CORBA, gRPC

- Přebírá OO techniky: zapouzdření, dědičnost, polymorfismus; data + operace.
- **Servant** = implementace objektu na serveru; **Proxy** = lokální zástupce na klientu (žádná data, jen přeposílá volání).
- ✅ Abstrahuje síť, znovupoužitelné komponenty, stav v servantech.
- ❌ Správa životního cyklu a stavu objektů, fault tolerance (replikace stavu na více serverech), složité nasazení (definice tříd se musí sdílet s klienty).

**CORBA (Common Object Request Broker Architecture):**

- Standard **OMG**, otevřený, vendor-independent; první verze **1991**, dnes v3.
- **IDL** – bindingy do mnoha jazyků (Ada, C, C++, Java, Python, Ruby…).
- **ORB (Object Request Broker)** – middleware pro komunikaci; standardní protokol **IIOP** (specializace GIOP nad TCP/IP).
- **Location transparency**, load balancing, fault tolerance, jazyková/platformní nezávislost, QoS, real-time.
- **POA (Portable Object Adapter)** – správa servantů na serveru (lifespan, retention, id assignment policy).

**Serializace dat:** XML (verbose), JSON (méně overhead, čitelné), binární (efektivní, externí struktura). **Protocol Buffers** – binární, jazykově/platformně neutrální, kompaktní, **silně typové schéma**, od Googlu (interně 2001, open-source 2008). **gRPC** – RPC framework nad Protobuf, nízká latence, request/response i **streaming**.

### 6.3 Message-oriented middleware (MOM)

> [!note] RPC × Messaging RPC = **těsná vazba**, hůře udržovatelné. Messaging = **loose coupling** (pošli požadavek / přijmi odpověď), payload strukturovaný i nestrukturovaný.

- **Adresace:** cílový proces nebo **fronta**; point-to-point, nepřímá (fronty), content addressing.
- **Doručení:** ztráta (ACK), duplicita (číslování), poškození (checksum).
- **Blokující × asynchronní** (callback / polling).
- **Enterprise messaging:** totální spolehlivost (nikdy se neztratí zpráva), multiplatformnost, bundling zpráv, load balancing, batch.

**JMS (Java Messaging Services):**

- **Broker** (middleware pro doručení), **Connection** (z factory), **Session** (ordering, listenery, transakce – vázané na jedno vlákno).
- **Destination = Queue nebo Topic:**
    - **Queue** – point-to-point, zprávu spotřebuje **právě jeden** příjemce.
    - **Topic** – one-to-many, **publish/subscribe** (i durable subscriber).
- **Message:** header (ID, timestamp, destination, type, delivery mode) + properties (+ message selectors) + body (text/map/bytes/stream/object).
- Příklad implementace: **ActiveMQ** (Apache, OpenWire/Stomp). Další: **IBM MQ Series**, **OpenSplice DDS**.

**MPI (Message Passing Interface) – HPC:**

- Stejný kód běží na více strojích, každý proces má **rank** (proces 0 = master), adresace dle ranku.
- Send varianty: send/ssend/bsend/rsend/isend; recv/mrecv/irecv.
- **Skupinová komunikace:** Bcast, Scatter, Gather, **Reduce**, Barrier. (Pozn.: MapReduce je podobný principu scatter & gather.)
- **Remote memory access** (put/get), implementace **OpenMPI** (mpicc, mpirun).

### 6.4 Komponentové middleware (CBSE)

> [!question] Batko 2022: _„Jak komponenta komunikuje s okolím (→ dependency injection), co je třeba zajistit (že existuje a je dostupná), signatura komponenty.“_

- **Component-based SW Engineering** – vyvíjet standardizované komponenty a skládat je do aplikací (reakce na selhání OO v znovupoužitelnosti – třídy bývají příliš specifické).
- **Definice (Szyperski):** _jednotka kompozice se smluvně specifikovanými rozhraními a explicitními kontextovými závislostmi; nasaditelná nezávisle, skládaná třetími stranami._
- **Charakteristiky:** standardized, independent, **composable** (jen přes veřejná rozhraní), deployable (typicky binární, bez kompilace), documented.
- **Rozhraní:**
    - **Provides interface** – funkcionalita, kterou komponenta poskytuje ostatním.
    - **Requires interface** – funkcionalita, kterou komponenta potřebuje od ostatních.
- **Component models:** EJB (Enterprise Java Beans), COM+ (.NET), CORBA Component Model.
- **Middleware/Container:** spouští komponenty, poskytuje platform & horizontal services, řeší lifecycle, persistenci, **bezpečnost, balancing, high-availability, transakce**.
- **Kompozice:** sekvenční, hierarchická, aditivní; **glue code** při nekompatibilitě rozhraní (parameter/operation incompatibility, incompleteness).
- **Vývoj pro znovupoužití:** generalizace algoritmů, extension points; trade-off **reusability × usability** (obecnější rozhraní = větší znovupoužitelnost, ale složitější/méně použitelné).
- **Příklady:** **OSGi** (Apache Felix; komponenty jako _bundles_, dynamické nasazení, automatické řešení „requires“ závislostí), **EJB 3.0** (anotace, **dependency injection**, convention over configuration; Glassfish, Oracle WebLogic).

> [!tip] Odpověď na Batkovu otázku Komponenta komunikuje s okolím **výhradně přes rozhraní** (provides/requires). Závislosti se dodávají přes **dependency injection** (kontejner „vstříkne“ požadované služby). Před použitím je třeba zajistit, že komponenta **existuje a je dostupná** (kontejner / OSGi resolver vyřeší requires). **Signatura** = specifikace rozhraní (jména operací, typy parametrů in/out, výjimky), generuje ji framework a poskytuje přes metadata/registry.

---

## 7. SOA a webové služby

> [!question] Otázka komise (Batko 2023 detailně, Lexa, Pastva, Oujezský) _„Definice webových služeb, SOAP vs REST, WSDL, rozdíly. SOAP není první pokus o standardizaci (→ předchůdce CORBA/RPC).“_

### Co jsou webové služby

- **XML-based distribuovaný systém služeb**: **SOAP, WSDL, UDDI**, WS-Interoperability.
- **Machine-to-machine** interakce přes síť pomocí zpráv.
- **Platformně a jazykově nezávislé**, velmi volně definované; lze generovat stuby, ale metody lze volat i přímo.
- ✅ Škálovatelné, jednoduché, distribuované. ❌ Méně efektivní (overhead), nutno tolerovat výpadky.

### WSDL (Web Service Description Language)

- W3C specifikace, popisuje **jak službu používat** (obdoba CORBA IDL), jazykově nezávisle, obvykle generováno.
- **Části WSDL:**
    - **Types** – vlastní typy zpráv (z XML Schema).
    - **Interfaces** (dříve _PortType_) – sada **operací** (in/out zprávy).
    - **Bindings** – navázání rozhraní na konkrétní protokol (např. SOAP over HTTP); jedno rozhraní lze navázat na víc protokolů.
    - **Services** – jedna či více **URL (Endpoints/Ports)** služby.
- **Message patterns:** One-way, Request-Response, Solicit-Response, Notification.

### SOAP

- Původně „Simple Object Access Protocol“; **XML formát pro výměnu zpráv** (přenos přes HTTP, TCP…), strukturu může definovat WSDL.
- **SOAP NENÍ transportní protokol** – je to zprávový formát; transport zajistí např. HTTP.
- Lze pro **RPC** (klient-server) i pro **zasílání XML dokumentů** i pro **messaging (pub/sub)**.
- **Struktura zprávy:** `Envelope` (kořen) → `Header` (0–1, volitelný) + `Body` (povinný, payload).
- **Headers:** rozšíření zprávy (jiné namespace), atribut **`mustUnderstand`**, **`role`**; zpracovatelné nezávisle na body (intermediaries).
- **Použití headerů:** **WS-Security / SAML** (digitální podpisy, klíče), **QoS** (spolehlivé doručení, transakce), **session state** (obdoba cookies).

### REST (Representational State Transfer)

- **Síťový architektonický styl** (Roy Fielding, PhD 2000). **Není standard** (žádná W3C spec).
- Stojí na: **HTTP** (přenos), **URL** (adresa zdroje), **XML/HTML/JSON/JPEG** (reprezentace), **MIME typy**.
- **Zdroje (resources)** jsou pojmenované URL; klient získá **reprezentaci**, ta ho uvede do stavu; přechod na jinou URL = jiný stav.
- **Charakteristiky:**
    - pull-based client-server, odkazy procházeny volbou klienta,
    - **stateless** (každý požadavek soběstačný),
    - cacheable / non-cacheable,
    - **uniform interface** – GET, POST, PUT, DELETE,
    - pojmenované zdroje (URL), propojené reprezentace (odkazy uvnitř objektů),
    - **vrstvená infrastruktura** (proxy, gateway, firewall).

### SOAP vs REST (oblíbené srovnání)

| |**REST**|**SOAP**|
|---|---|---|
|Struktura|Propojení (hypertext, uniform interface)|OO paradigma, zapouzdření dat|
|Přístup|GET/PUT/POST/DELETE|Specifické, mnoho metod|
|Rozšiřitelnost|Ano, přes odkazy|Nelze referencovat objekty mimo systém|
|Výkon|GET URI **cachovatelné**|Necachuje žádná existující technologie|
|Bezpečnost|Práva na úrovni datového objektu (GET/PUT/…)|Nutno implementovat vhodnou strategii (WS-Security)|
|Data|typicky **JSON**|**XML**|

> [!note] „SOAP není první pokus o standardizaci“ (Batko) Vzdálené volání se standardizovalo už dříve: **Sun RPC, CORBA (IIOP), DCOM** – webové služby (SOAP) jsou pozdější, jazykově/platformně neutrální XML přístup; REST je ještě pozdější, odlehčený styl nad HTTP.

### Příklady technologií WS

- **Apache CXF** (WS + WSDL autogenerátor, REST + WADL), **Jersey** (lehký REST), **Glassfish** (robustní Java kontejner).

---

## 8. Cloud computing

> [!question] Otázka komise (Bühnová, Čeleda, Pastva, Oujezský) _„Cloud, výhody/nevýhody, IaaS/PaaS/SaaS, virtualizace, příklady z praxe.“_

### Definice a charakteristiky

- **Výpočetní výkon „on-demand“** – přístup ke sdíleným zdrojům (servery, úložiště, aplikace, služby) → sdílené i náklady na údržbu.
- **Charakteristiky (NIST-like):**
    - **Elasticity** – self-service, automatické provisioning, alokace/uvolnění dle potřeby (škálovatelnost).
    - **Accessibility** – široký síťový přístup, různé platformy.
    - **Pooling** – sdílení zdrojů mezi nájemci (tenants), location transparency (úroveň „regionů“, pozor na legální omezení).
    - **Measured** – monitorované využití, **SLA**.

### Service modely (vždy umět porovnat!)

- **IaaS (Infrastructure as a Service)** – IT infrastruktura (servery, úložiště); **klíčová je virtualizace**. Např. **Amazon EC2/S3, Azure**.
- **PaaS (Platform as a Service)** – platformy a běhová prostředí, rychlý vývoj. Např. **Google App Engine, Azure App Service**.
- **SaaS (Software as a Service)** – aplikační služby pro koncové uživatele (typicky web). Např. **Office 365, Gmail**.
- Rozdíl = **co spravuje provider vs zákazník** (od IaaS, kde zákazník spravuje nejvíc, po SaaS, kde téměř vše spravuje provider).
- **Další modely:** iPaaS (cloudový ESB/SOA), BaaS/MBaaS, DaaS (desktop), DBaaS (DB), STaaS (storage).

### Deployment modely

- **Private** (jedna organizace), **Community** (sdílí komunita – např. **MetaCentrum/CESNET**), **Public** (placené, kdokoli, velký rozsah), **Hybrid** (kombinace – privátní + špičky do veřejného).

### Technologie umožňující cloud

- **Virtualizace** (decoupling OS/aplikace od HW, více OS na 1 HW, přesun VM),
- **broadband síť**, **webové služby** (interoperabilita), **prohlížeče/HTML5** (bohaté UI na tenkém klientu), **kryptografie** (auth, privacy).

### Škálovatelnost v cloudu

- **Vertikální** – výkonnější stroje; **horizontální** – alokace dalších VM (viz kap. 3).

### Pro a proti

- ✅ Lineárně škálující náklady, žádná počáteční investice, sdílená údržba a bezpečnostní tým, lepší izolace mezi tenanty, redundance, SLA.
- ❌ Důvěra a vlastnictví dat, ztráta fyzické kontroly, off-shore/legal issues, bezpečnost systémů (patchování), dostupnost (výpadky, konektivita).
- **Standardy:** OVF (přenositelnost VM), SAML/XACML (přístup), OCCI (API pro IaaS), CDMI (data), CIM (management).
- **Příklady toolkitů:** **jclouds** (ComputeService = VM abstrakce, BlobStore = storage), **OpenNebula/OpenStack**.

---

## 9. Distribuované zpracování dat / BigData

> [!question] Pastva 2024/2025: _„MapReduce, Hadoop, GRID vs Cloud, co se stane při výpadku HW uzlu při MapReduce, OLTP vs OLAP, data warehouse.“_

### BigData – motivace (3 V)

- **Volume** (objem), **Variety** (různorodost typů), **Velocity** (rychlost generování).

### Batch vs Stream processing

| |**Batch**|**Stream**|
|---|---|---|
|Data|obrovská, statická, **bounded**|jednotlivé položky, **unbounded**|
|Operace|procedurální|funkcionální|
|Stav|udržován|bezstavové (min. stav)|
|Čas|časově náročné|real-time|
|Tech.|Hadoop, **GRID**|Apache Storm, Samza|

- **Hybridní:** **Kappa** (batch jako stream – **Apache Flink**), **micro-batch** (**Apache Spark**).

### MapReduce

- Dvě uživatelské operace:
    - **Map** – aplikuje se na každý kus dat, produkuje **klíč** (data-centric, distribuováno v clusteru).
    - **Reduce** – sumarizace skupiny dat se stejným klíčem.
- Mezi tím **Shuffle**. Podobné MPI scatter & gather.
- **Middleware poskytuje:** správu dat (vč. redundance), messaging pro distribuované spuštění, **fault-tolerance**.

> [!tip] „Co se stane při výpadku HW uzlu“ (Pastva) Díky **fault-tolerance a replikaci** (data v HDFS na více uzlech) framework **úlohu/task přeplánuje na jiný uzel** – Job tracker monitoruje a řeší selhání, task se spustí znovu jinde. Výpočet tedy pokračuje bez ztráty dat.

### Hadoop

- Open-source MapReduce framework (inspirace Google, v Javě, Apache).
- **Struktura:** **HDFS** (data node = data, **name node** = adresářová služba) + M-R vrstva (**Job tracker** rozděluje tasky, monitoruje, řeší selhání; **Task tracker** spouští task v samostatné JVM).
- **HDFS:** distribuovaný, škálovatelný, **fault-tolerant (replikace bloků ~64 MB na více data nodů)**, optimalizováno pro čtení, zápis nekonkurentní.
- **Rozšíření:** Hive (SQL/HQL), Pig (analýza), Mahout (ML), **Cassandra** (NoSQL key-value), **HBase** (column-oriented).

### GRID computing

- Model využívající výkon **mnoha síťových zdrojů** (nejen CPU – i úložiště, speciální zařízení), pro výpočetně náročné problémy, **sdílení drahých zdrojů mezi institucemi**.
- **Batch (jobs), job queues**, stejný příkaz na více strojích (rozdělení dat ručně).
- **GRAM** (alokace/management), **scheduler** (fronty, matching, priority, meta-schedulery), **GSI** (certificate-based PKI, single sign-on, delegace).

> [!note] GRID vs Cloud (Pastva) **GRID** = primárně batch výpočty, sdílení zdrojů mezi institucemi, uživatel ručně řeší rozdělení/joby. **Cloud** = on-demand elastické zdroje, virtualizace, samoobslužné provisioning, placené dle využití (IaaS/PaaS/SaaS). Cloud cílí na obecné služby a elasticitu, GRID na náročné vědecké výpočty.

### Stream frameworky

- **Apache Storm** – real-time, topologie (DAG): **Spouts** (zdroje streamu) → **Bolts** (zpracování po jedné n-tici); at-least-once (Trident → exactly-once přes micro-batche).
- **Apache Samza** – nad **Kafkou** (topic = stream), brokeři, partitions (pořadí v partition garantováno), používá YARN + HDFS.
- **Apache Flink** – kappa, batch = ohraničený stream; sources/operators/sinks, sessions, snapshoty pro recovery.
- **Apache Spark** – batch s podporou streamu, **in-memory** (zrychlení vs MapReduce), **RDD** (immutable kolekce, transformace tvoří nové RDD), automatická optimalizace.

---

## 10. Senzorové sítě (doplněk – občas jako podotázka, Oujezský)

- **Aplikace:** vojenské, environmentální (požáry, povodně), zdravotní (monitoring fyziologie).
- **Architektura:** **sensor nodes** (snímají, zpracují, pošlou přes rádio) → **base station/sink** → **operator/user** (task manager, dotazy).
- **HW omezení:** baterie (den–10 let), slabé CPU (MHz), krátký dosah (metry), nízké datové toky; přenos: WiFi, GSM, Sigfox, LoRaWAN, Bluetooth, RF, **ZigBee** (přímo pro sensor networks).
- **Směrování:** **reaktivní** (route se hledá až když jsou data) – **AODV** (každý uzel pamatuje next hop, znalost route distribuovaná), **DSR** (kompletní route v hlavičce), **Rumor Routing** (agenti, náhodná cesta, TTL).
- **Proč middleware pro SN:** „lepidlo“ mezi HW/OS/sítí a aplikací; standardizované služby, runtime, efektivní využití zdrojů.
- **Výzvy:** omezený výkon/energie (sleep mód, minimalizace přenosů), škálovatelnost/mobilita/dynamická topologie (fault tolerance, self-config), heterogenita, **data aggregation** (přenos 1 bitu ~ 1000 instrukcí → agregovat a minimalizovat přenosy), real-time, bezpečnost.
- **Tradiční middleware (RMI, EJB, CORBA) je pro SN příliš „heavyweight“.** Existující SN middleware: Impala (ZebraNet), Maté (VM nad TinyOS), TinyDB (dotazy), Agilla (mobilní agenti).

---

## 11. Příklady technologií a využití (rychlá tabulka k zapamatování)

|Oblast|Technologie|Využití v praxi|
|---|---|---|
|RPC|Sun RPC (NFS), Java RMI, **gRPC** + Protobuf|Vzdálené volání, mikroservisní komunikace|
|OO middleware|**CORBA** (ORB/IIOP/IDL), DCOM, .NET Remoting|Heterogenní podnikové systémy|
|Messaging|**JMS/ActiveMQ**, IBM MQ, Kafka, DDS|Asynchronní integrace, fronty/topiky|
|HPC|**MPI / OpenMPI**|Vědecké paralelní výpočty|
|Komponenty|**EJB**, OSGi (Felix), COM+|Business logika, modulární aplikace|
|Web services|**SOAP + WSDL**, **REST/JSON**, Apache CXF, Jersey|M2M integrace, veřejná API|
|SOA / integrace|**ESB**, BPEL (orchestrace), iPaaS|Podniková integrace služeb|
|Cloud|**AWS (EC2/S3), Azure, GCP**, OpenStack, MetaCentrum|IaaS/PaaS/SaaS, elastické škálování|
|BigData batch|**Hadoop/HDFS** (MapReduce), GRID, Spark|Offline analytika, data mining|
|BigData stream|**Storm, Samza/Kafka, Flink**|Real-time analytika, detekce událostí|
|NoSQL|Cassandra, HBase|Škálovatelné úložiště (sharding/replikace)|
|Sensor net|ZigBee, TinyDB, AODV/DSR|IoT, monitoring prostředí|

---

# Q&A – otázky komise a odpovědi

> [!note] Pozn. Otázky vybrané z protokolů komise k tématu **Distribuované systémy / SOA / WS / cloud / architektury / komponenty / BigData**. Číslování otázek v Excelu nesedí (mění se v čase), proto řazeno tematicky. Odpovědi shrnují, co zkoušející chtěl slyšet.

### Distribuované vs centralizované, škálovatelnost

**Q (Dohnal 2016): Distribuované systémy vs centralizované – jak se řeší rozdělení práce?** A: Centralizovaný = vše na jednom místě (SPOF, strop výkonu, jednoduchá konzistence). Distribuovaný = více uzlů + síť, řeší paralelismus, využití vzdálených zdrojů/služeb. **Rozdělení práce** = dekompozice úlohy (tiers/služby), **partitioning/sharding dat**, u výpočtů **scatter–gather / MapReduce**, load balancing přes name service. (Podotázka byly Petriho sítě – modelování souběžnosti.)

**Q (Dohnal 2017): Distribuované systémy → škálovatelnost na vysokou dostupnost nad databázemi + RAID.** A: Škálovatelnost horiz./vert.; HA nad DB = repliky (master–slave), failover. **RAID 1 = mirroring** (redundance/dostupnost), **RAID 0 = striping** (výkon). Mirroring pro dostupnost, striping pro propustnost.

**Q (Bühnová 2018): Distribuované systémy – základy, výhody/nevýhody (spolehlivost, kvalita, bezpečnost), škálovatelnost (horiz./vert.).** A: Viz kap. 1–3. Výhody: dostupnost, kapacita, rychlý vývoj, interoperabilita. Nevýhody: latence/výpadky (fallacies), nekonzistence, bezpečnost. Škálování: vertikální (silnější stroj, strop) × horizontální (více strojů, ale náročnější).

**Q (Dohnal 2018): Distribuované systémy – charakteristiky, porovnat centralizovaný a distribuovaný, data sharding, architektury, škálovatelnost.** A: Charakteristiky účastníci+síť; sharding = rozdělení dat dle vlastnosti (shared-nothing → škálovatelnost); architektury monolit→tiered→komponenty→SOA→microservices→P2P.

**Q (Bühnová 2020): Distribuované systémy, porovnání s centralizovanými, škálovatelnost, virtualizace.** A: ⚠️ Pozor: **horizontální škálování je obecně NÁROČNĚJŠÍ** než vertikální (nutná úprava architektury, konzistence). Virtualizace = více OS na HW, přesun VM – základ cloudu/IaaS.

**Q (Čeleda 2022 / Bühnová 2022 / Dohnal 2023): Distribuované systémy + cloud, škálovatelnost, virtualizace, IaaS-PaaS-SaaS.** A: Viz kap. 2, 3, 8.

**Q (Lexa 2025): Distribuované systémy – základní pojmy, principy, middleware, centralizovaná vs distribuovaná, škálování, replikace, eventuální konzistence, nedostupnost dat při výpadku.** A: Middleware = abstrakční vrstva. Řešení výpadku jedné instance = **replikace (master–slave)**; dočasná nekonzistence → **eventuální konzistence** (repliky se nakonec sjednotí).

### SOA, ESB, webové služby

**Q (Batko 2018): SOA – základní principy, příklad použití, co je ESB a na co je dobré.** A: SOA = aplikace z nezávislých služeb, **loose coupling, discovery, composition, stateless**. **ESB** = komunikační sběrnice (směrování, transformace, orchestrace přes BPEL), umožňuje volné propojení služeb.

**Q (Dohnal 2018): MVC, SOA – popsat, proč, praktická aplikace.** A: MVC = Model/View/Controller (oddělení dat, prezentace, řízení); SOA = distribuovaná architektura služeb. MVC oddělení odpovědností a testovatelnost; SOA znovupoužití a integrace.

**Q (Batko 2022/2024): SW architektury obecně, SOA.** A: Od monolitu po vrstvenou a SOA. ⚠️ Nevýhoda SOA = **multiple points-of-failure**; vždy zmínit **loosely coupled**. SOA se od vrstvené liší tím, že je **distribuovaná**.

**Q (Batko 2023): SOAP, REST, definice webových služeb (do detailu), rozdíly služeb.** A: WS = XML-based M2M služby (SOAP/WSDL/UDDI). **SOAP** = XML zprávový formát (Envelope/Header/Body), není transport. **REST** = styl nad HTTP (GET/POST/PUT/DELETE), stateless, cacheable, zdroje = URL. SOAP není první standardizace (předchůdci CORBA/RPC).

**Q (Lexa 2024/2025): Middleware, SOA/microservices, REST vs SOAP; klient-server, vícevrstvá, mikroservisní.** A: Viz kap. 2 (architektury) a 7 (REST vs SOAP). Vrstvy komunikují jednosměrně; mikroservisy = malé nezávislé služby, REST+JSON.

**Q (Oujezský 2024): SOA, distribuované vs centralizované, web services, výhody/nevýhody, příklady z praxe.** A: Vše povrchově, ale **na příkladech z praxe** (např. e-shop: katalog, platby, sklad jako oddělené služby přes ESB).

**Q (Pastva 2024/2025): Distribuované systémy – SOAP vs REST, HTTP metody v REST, RPC, MapReduce (+ výpadek HW), GRID vs Cloud, cloud systémy, SOA, replikace/sdílení dat.** A: Viz kap. 6 (RPC), 7 (SOAP/REST), 8 (cloud, GRID vs cloud), 9 (MapReduce + přeplánování tasku při výpadku). HTTP metody REST = GET/POST/PUT/DELETE.

**Q (Walletzký 2025): Distributed systems – SOA, replikace/sdílení dat.** A: SOA = služby přes rozhraní; replikace = stejná data na více uzlech (dostupnost/výkon čtení), sdílení dat = sharding/partitioning.

### Komponenty, MVC, vrstvy

**Q (Pitner 2020 / Ošlejšek 2020): Layered vs tier architektury, MVC, porovnání MVC s vrstvami DAL/Business/Presentation.** A: Layer = logická vrstva (může být na 1 stroji), tier = fyzické nasazení. MVC odděluje Model/View/Controller; vrstvená architektura = Presentation→Business→Data Access (jednosměrná komunikace).

**Q (Batko 2022): Komponentové systémy – komunikace komponenty s okolím, dependency injection, signatura komponenty.** A: Komunikace jen přes **provides/requires rozhraní**; závislosti přes **dependency injection**; před použitím zajistit, že komponenta existuje a je dostupná (resolver/container); **signatura** = specifikace rozhraní (operace, typy in/out, výjimky) v metadatech.

**Q (Bühnová 2024 / Dohnal 2024 / Ošlejšek 2024): Architektonické a návrhové vzory, MVC, SOA, rozhraní, komponenty, kontrakty.** A: Nakreslit MVC a SOA, vysvětlit rozhraní; komponenta = kontrakt (provides/requires); příklad na každý typ architektury.

### Cloud, BigData

**Q (Bühnová 2021/2022): Cloudové architektury, SOA, výhody SOA, porovnat IaaS/PaaS/SaaS.** A: IaaS (infrastruktura, virtualizace – EC2/S3) → PaaS (platforma – App Engine) → SaaS (aplikace – Gmail/Office365). Rozdíl = co spravuje provider vs zákazník.

**Q (Pastva 2025 – Zpracování dat): OLTP vs OLAP, data warehouse, BigData (3V), MapReduce, Hadoop.** A: OLTP = transakční (provoz), OLAP = analytické (agregace, multidimenzionální kostka, roll-up/drill-down); **data warehouse** odděluje analytiku, aby nezatěžovala OLTP; ETL plní DWH; BigData = Volume/Variety/Velocity; MapReduce = Map (klíče) + Reduce (agregace) + Shuffle; Hadoop/HDFS s replikací bloků.

### Senzorové sítě (podotázka)

**Q (Oujezský 2025): Senzorové sítě – HW omezení, protokoly, síťování.** A: HW omezení (baterie, slabé CPU, krátký dosah); směrování reaktivní (AODV/DSR/Rumor); ZigBee; middleware musí být lightweight, řešit agregaci dat a úsporu energie.

---

> [!success] Checklist před zkouškou
> 
> - [ ] Umím nakreslit a porovnat **1/2/3-tier, komponentovou, SOA, microservices** architekturu.
> - [ ] Vysvětlím **centralizovaný vs distribuovaný** + nevýhody a jejich překonání (replikace, HA, load balancing).
> - [ ] **Horizontální × vertikální** škálování (a že horizontální je obecně těžší).
> - [ ] **Replikace** (master–slave, eventuální konzistence) a **sharding/shared-nothing**.
> - [ ] **SOA principy** + **ESB** + **loosely coupled** + nevýhoda **multiple points-of-failure**.
> - [ ] **SOAP vs REST**, **WSDL** části, message patterns.
> - [ ] **Cloud**: charakteristiky, **IaaS/PaaS/SaaS**, deployment modely, **virtualizace**.
> - [ ] **RPC** princip (marshalling, stuby, naming service, failure handling).
> - [ ] **MapReduce/Hadoop/HDFS** + chování při výpadku uzlu; **GRID vs Cloud**.
> - [ ] Ke všemu **příklad z praxe** (ideálně z vlastní diplomky).