# Počítačové sítě

> [!abstract] **Koncepty, principy, architektury. ISO/OSI a TCP/IP model, IP protokol, transportní protokoly (TCP, UDP). Protokoly na síťových vrstvách, funkce IPv4, pokročilé funkce IPv6. Peer-to-peer (P2P) sítě, ad-hoc/senzorové sítě, vysokorychlostní sítě, počítačové sítě a multimédia. Příklady z praxe.** (PA159 Net-Centric Computing I., PA191 Advanced Computer Networking)

> [!tip] Jak komise reálně zkouší (shrnutí z dotazníku komise) Zkoušející: **Novák, Dohnal, Ošlejšek, Zlatuška, Čeleda, Krhovják, Švenda, Batko**. Z praxe se nejčastěji žádá:
> 
> - **Nakreslit a popsat ISO/OSI model** + co dělá každá vrstva, datové jednotky a protokoly na nich (PDU).
> - **Putování zprávy po vrstvách** (např. SMTP/e-mail) – enkapsulace.
> - **IP protokol** – účel, IPv4 vs IPv6 adresa, rozdělení IPv4 adresy na síťovou a hostitelskou část.
> - **TCP vs UDP** – rozdíly; klíčové slovo, které chtějí slyšet, je **congestion control** (řízení zahlcení) a spolehlivost.
> - **Směrování** – na jaké vrstvě běží (L3), **Distance Vector vs Link State** + příklad + metriky.
> - **Praktická otázka (oblíbená u Ošlejška!):** „Co všechno musím nastavit na PC kromě IP adresy, abych se připojil k síti (když nemám DHCP)?“ → **IP adresa, maska podsítě, výchozí brána (gateway)**, případně DNS. Vysvětlit k čemu každá věc je.
> - **Co dostanu, když se připojím k síti / „plug and play“** → **DHCP** (IP, maska, brána, DNS).
> - **Jak se řeší nedostatek IPv4 adres** → **NAT, CIDR/subnetting, privátní adresy, IPv6**.
> - **NAT** – k čemu a jak funguje.
> - **L1/L2 detailně** – protokoly přístupu k médiu: **CSMA/CD, CSMA/CA, Aloha, Token Ring**.
> - **DNS, DHCP, vyjmenovat well-known porty.**
> - **Jak funguje router.** Styl: většinou příjemná diskuse, navádějí. Stačí znát to z materiálů + logicky odvodit. Kreslení (OSI, adresy, diagramy) si pochvalují.

---

## 1. Základní koncepty, principy a architektury

### Co je počítačová síť

Skupina počítačů a zařízení propojených komunikačními kanály, které umožňují komunikaci mezi uživateli a sdílení prostředků (HW, soubory/data, SW).

**Tři základní charakteristiky doručování:**

- **Delivery** – data musí dorazit ke správnému příjemci.
- **Accuracy** – data musí dorazit nepoškozená.
- **Timeliness** – data musí dorazit včas.

**Ideální vs reálná síť:** ideální = transparentní, neomezená propustnost, žádné ztráty/zpoždění/jitter, zachované pořadí, nepoškozená data. Reálná síť má vnitřní strukturu → omezenou propustnost, občasné ztráty, proměnné zpoždění/jitter, může měnit pořadí, data se mohou poškodit.

**Požadované vlastnosti sítě:** efektivita (max. využití propustnosti), férovost (fairness), decentralizovaná správa, rychlá konvergence, multiplexing/demultiplexing, spolehlivost, řízení toku (flow control, ochrana před zahlcením).

### Dva základní přístupy k přepojování

| |**Spojované (circuit switching)**|**Nespojované (packet switching)**|
|---|---|---|
|Princip|sestaví se okruh (circuit) na celou dobu komunikace|data se sekají na **pakety**, posílané samostatně|
|Stav|síť udržuje stav okruhu|bezstavové (no state)|
|Cesta|pevná na celou dobu|pakety lze směrovat, slučovat, fragmentovat|
|QoS|snadná (skoro automatická)|velmi obtížná (**best-effort**)|
|Příklad|telefonní síť|**Internet**|

### Síťové (komunikační) protokoly

**Protokol** = sada pravidel definujících formát a pořadí zpráv vyměňovaných mezi komunikujícími entitami a akce při jejich odesílání/přijímání. Definuje:

- **Syntaxi** – struktura/formát dat (pořadí).
- **Sémantiku** – význam jednotlivých bitů/polí.
- **Časování (timing)** – kdy a jak rychle se data posílají.

Příklady: UDP, TCP, IP, IPv6, SSL/TLS, SNMP, HTTP, FTP, SSH, Aloha, CSMA/CD.

### Standardizace

Cíle: kvalita, bezpečnost, kompatibilita, interoperabilita, přenositelnost.

- **de facto** – standardy přijaté širokým používáním (často od výrobců).
- **de jure** – legislativně schválené oficiálním orgánem. Organizace: **ISO, ITU-T, ANSI, IEEE, IETF (RFC), IEC**.

---

## 2. Síťové modely: ISO/OSI a TCP/IP

> [!example] Tohle se ptají skoro vždy – umět nakreslit a popsat vrstvy.

### ISO/OSI model (7 vrstev)

Vrstvený model od organizace ISO pro zajištění kompatibility a interoperability. **Principy vrstvení:**

- Každá vrstva má vlastní funkcionalitu a přidává řídicí informace k datům.
- Vrstva komunikuje jen se sousedy: **využívá služby nižší vrstvy a poskytuje služby vyšší vrstvě**.
- Logicky komunikace probíhá mezi stejnými vrstvami (peer), fyzicky prochází všemi nižšími.
- Změna v jedné vrstvě → adaptují se jen sousední vrstvy.

|#|Vrstva (CZ / EN)|Funkce|PDU (datová jednotka)|Příklady|
|---|---|---|---|---|
|7|Aplikační / Application|služby uživateli/aplikacím|data / zpráva|HTTP, SMTP, DNS, FTP|
|6|Prezentační / Presentation|formát, kódování, šifrování, komprese|data|TLS, ASCII, JPEG|
|5|Relační / Session|správa relací (sessions)|data|RPC, NetBIOS|
|4|Transportní / Transport|proces-proces, spolehlivost, **porty**|**segment** (TCP) / datagram (UDP)|TCP, UDP|
|3|Síťová / Network|adresace (IP), **směrování**, fragmentace|**paket / datagram**|IP, ICMP, ARP|
|2|Linková / Data Link|rámce, MAC adresy, řízení přístupu k médiu|**rámec (frame)**|Ethernet, Wi-Fi (802.11), PPP|
|1|Fyzická / Physical|přenos bitů jako signálu po médiu|**bit**|RS-232, 802.3 fyzika|

> [!tip] Mnemotechnika (shora dolů): „**A**ll **P**eople **S**eem **T**o **N**eed **D**ata **P**rocessing".

### TCP/IP model a srovnání s ISO/OSI

TCP/IP má **4–5 vrstev**; 7vrstvý OSI nebyl široce přijat. Mapování:

|TCP/IP|≈ ISO/OSI|
|---|---|
|**Application**|7 + 6 + 5 (Aplikační, Prezentační, Relační)|
|**Transport**|4 (Transportní)|
|**Internet/Network**|3 (Síťová)|
|**Link / Network Access**|2 + 1 (Linková + Fyzická)|

**TCP/IP „hourglass" (přesýpací hodiny):** úzké hrdlo je vrstva **IP** – nad ní i pod ní je velká rozmanitost protokolů, ale všechno prochází přes jediný společný IP. „IP over everything, everything over IP."

![[iso_osi_tcp_ip.png|444]]

**Zapouzdření**
![[encapsulation.jpeg]]

---

## 3. Fyzická vrstva (L1)

**Hlavní cíl:** přenést bity (obsah rámců) mezi odesílatelem a příjemcem – převede proudy bitů na signály šířené médiem.

**Služby:** transformace bit→signál, řízení bitové rychlosti, bitová synchronizace (clocking), multiplexing, přepojování okruhů (circuit switching je funkce L1; packet switching řeší L2).

**Signály:** data (0/1) jsou digitální; signál v médiu může být analogový (vyžaduje **modulaci**) nebo digitální (vyžaduje **kódování**, kvůli synchronizaci).

**Přenosová média:**

- **Guided (drátová):** kroucená dvojlinka (twisted pair, LAN do 10 Gbps), koaxiál, **optické vlákno** (páteře, stovky Gbps).
- **Unguided (bezdrátová):** rádio, mikrovlny, infračervené – signál se šíří éterem.

**Multiplexing** (sdílení pásma souběžnými kanály): pro analog **FDM/WDM** (frekvenční/vlnové dělení), pro digitál **TDM** (časové dělení).

---

## 4. Linková vrstva (L2)

Přijímá pakety od L3, zabalí je do **rámců (frames)**; ve spolupráci s L1 zajišťuje **lokální doručení v rámci segmentu (LAN)**. Zajišťuje spolehlivost spoje, řízení toku a **řízení přístupu k médiu (MAC)**.

**Služby:**

- **Framing** – zapouzdření paketů do rámců.
- **Adresace** – fyzické **MAC adresy** (zdroj + cíl).
- **Error Control** – detekce/oprava chyb pomocí redundance:
    - **ARQ** (Automatic Repeat reQuest) – detekce + žádost o opakování (parita, **CRC**); vhodné pro málo ztrátová média.
    - **FEC** (Forward Error Correction) – detekce + oprava z redundantních dat (např. **Hammingův kód**); pro ztrátová média s velkým zpožděním.
- **Flow Control** – ochrana příjemce před zahlcením (stop-and-wait, sliding window).
- **MAC** – koordinace přístupu více zařízení ke sdílenému médiu.

> [!example] Ošlejšek se ptal detailně na L1/L2 protokoly: CSMA/CD, CSMA/CA, Aloha, Token Ring – uměj je rozlišit!

### Protokoly přístupu k médiu (MAC)

- **Random-access (náhodný přístup):**
    - **Aloha** – vysílej kdykoli; při kolizi čekej náhodnou dobu a opakuj. **Slotted Aloha** – vysílání jen na začátku časových slotů (vyšší propustnost).
    - **CSMA/CD** (Collision Detection) – „naslouchej před vysíláním", při vysílání **detekuj kolizi**, přeruš a opakuj po náhodné době (backoff). Použití: **klasický drátový Ethernet** (half-duplex). V drátu lze kolizi detekovat.
    - **CSMA/CA** (Collision Avoidance) – kolizím se **předchází** (např. RTS/CTS rezervace, náhodný backoff před vysíláním). Použití: **Wi-Fi (802.11)** – v bezdrátu nelze kolizi spolehlivě detekovat (skrytý uzel).
- **Controlled-access (řízený přístup):** rezervace, **polling**, **token** → **Token Ring / Token Bus** (vysílat smí jen držitel tokenu; bezkolizní, deterministické zpoždění).
- **Channelization (multiplexní):** FDMA, TDMA, CDMA.

### L2 sítě a zařízení

- **LAN topologie:** sběrnice (bus), kruh, hvězda, strom, mesh.
- **Bridge** – transparentní propojení, **odděluje kolizní domény** (kolize jej nepřejdou).
- **Switch** ≈ multiportový bridge; přepíná rámce podle **cílové MAC adresy**.
- **Backward Learning Algorithm** – switch/bridge se „učí" polohu stanic z **odesílacích MAC adres** příchozích rámců.
- **Spanning Tree (STP)** – distribuovaný algoritmus; zakáže některé porty, aby vznikl strom **bez smyček (loops)**. Fáze: výběr root bridge (nejnižší adresa) → výběr root portů (nejnižší cena cesty) → aktivace/deaktivace portů.

---

## 5. Síťová vrstva (L3) — IP

Přijímá segmenty od L4, transformuje na **pakety/datagramy**; logicky spojuje nezávislé LAN do iluze jedné WAN; zajišťuje jednoznačnou **adresaci** a **směrování**; ve spolupráci s L2 mapuje L3-adresy ↔ MAC adresy.

**Služby L3:** internetworking, packetizing, **fragmentace** (řeší různá MTU – datagram větší než MTU sítě se rozdělí na fragmenty), adresace, **address resolution (ARP/RARP)**, **routing**, řídicí zprávy (**ICMP**), multicast.

### IPv4 adresy

- **32 bitů**, zapisuje se dekadicky po bajtech (např. `192.168.1.10`).
- Adresa se dělí na **síťovou část (network)** a **hostitelskou část (host/device)** – hranici určuje **maska podsítě (subnet mask)** / prefix (CIDR, např. `/24`).
- Typy: **Unicast** (1 rozhraní), **Broadcast** (všem na LAN, „all-hosts"), **Multicast** (skupina příjemců – třída D `224.0.0.0–239.255.255.255`).
- **Privátní rozsahy** (RFC 1918): `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`.

> [!example] „Jak se rozdělí IPv4 adresa?" → na **network** část (kterou síť) a **host** část (které zařízení v síti). Maska určuje, kolik bitů je síťových.

![[ipv4_structure.png]]
### IPv4 datagram – hlavička (důležitá pole)

- **Version, HLEN** (délka hlavičky ve 4B slovech kvůli volitelnému poli Options).
- **DS/TOS** – třída pro **QoS** (odlišení důležitých/RT datagramů).
- **Total length** – max. 65535 B.
- **Identification, Flags, Offset** – **fragmentace**.
- **TTL (Time To Live)** – max. počet hopů; každý router **dekrementuje o 1**; při 0 se datagram **zahodí** (ochrana proti zacyklení) + odešle se ICMP.
- **Protocol** – vyšší protokol (multiplexing): **1 = ICMP, 2 = IGMP, 6 = TCP, 17 = UDP**.
- **Header checksum** – kontrolní součet **jen hlavičky** (přepočítává se na každém routeru kvůli TTL; data kontroluje L4).
- **Source/Destination IP**, **Options**, **Data**.

### Doprovodné protokoly L3

- **ICMP** (RFC 792) – IP je nespolehlivé (best-effort) bez zpětné vazby; ICMP hlásí chyby a stav sítě: _Destination unreachable, Time exceeded_ (vypršení TTL), _Echo request/reply_ (= **ping**).
- **ARP** – mapuje IP → MAC (v rámci LAN). **RARP** opačně.
- **IGMP** – správa členství v multicast skupinách.

### NAT (Network Address Translation)

> [!example] Ošlejšek: „K čemu je NAT a jak funguje? Jak se řeší nedostatek IPv4 adres?"

**Účel:** umožní více zařízením v privátní síti sdílet jednu (nebo málo) veřejnou IPv4 adresu → **šetří veřejné adresy** (jedno z hlavních řešení nedostatku IPv4). **Princip:** router přepisuje zdrojovou privátní IP (a port – PAT/„NAT overload") na veřejnou IP při odchozím provozu a zpět při příchozím; udržuje **NAT překladovou tabulku** (mapování privátní IP:port ↔ veřejná IP:port). **Vedlejší efekt:** poskytuje základní izolaci/„firewall" efekt, ale komplikuje příchozí spojení (potřeba port-forwarding).

**Řešení nedostatku IPv4 adres celkově:** **CIDR + subnetting** (efektivnější přidělování), **privátní adresy + NAT/PAT**, **DHCP** (dočasné přidělování), a definitivní řešení = **IPv6** (128 bitů).

---

## 6. Směrování (Routing)

> [!example] Časté: „Na jaké vrstvě probíhá směrování?" → **L3 (síťová)**. „Distance Vector vs Link State + příklad + metrika."

**Směrování** = nalezení cesty v síti mezi dvěma uzly, která splňuje jistá omezení (metriku). Faktory: **statické** (topologie) i **dynamické** (zatížení).

**Hop-by-hop princip:** router neřeší celou cestu, jen **jeden krok** – komu paket předat dál (někomu „blíž" k cíli). Další router rozhodne dál.

**Matematicky:** graf, kde uzly = routery (IP adresy), hrany = spoje, hodnota hrany = cena. Cíl = nejlevnější cesty. Algoritmy: **Bellman-Ford** (do jednoho cíle, O(LN)) a **Dijkstra** (do všech cílů, O(N²)).

**Globální problém:** globální znalost topologie je obtížně dosažitelná a rychle zastarává → lokální pohled = **směrovací tabulka**. Rozdíl lokální/globální znalosti vede ke smyčkám (black holes) a oscilacím.

**Statické vs dynamické směrování:** statické = ruční záznamy (malé/stabilní sítě); dynamické (adaptivní) = reaguje na změny (centralizované / izolované / **distribuované**).

### Distance Vector vs Link State

| |**Distance Vector (DV)**|**Link State (LS)**|
|---|---|---|
|Algoritmus|**Bellman-Ford**|**Dijkstra**|
|Co se vyměňuje|celé **směrovací tabulky** jen **sousedům**, periodicky|stav **vlastních spojů** **všem** v síti|
|Heslo|„vše o síti jen sousedům"|„o sousedech všem"|
|Znalost topologie|jen vzdálenosti (hop count)|**kompletní mapa sítě**|
|Konvergence|pomalejší, **count-to-infinity**, smyčky|rychlejší, odolné vůči oscilacím|
|Robustnost|chyba se šíří sítí (zmate ostatní)|chybný router šíří chybu jen o svých spojích|
|Složitost|změna se šíří jen k sousedům|O(nE) zpráv při změně|
|Použití|**menší sítě**|**velké sítě**|
|Protokol|**RIP**, IGRP, EIGRP|**OSPF**, IS-IS|

**Metriky:** DV obvykle **počet hopů (hop count)**; LS **cena** odvozená z **propustnosti** (případně latence, jitter, bandwidth). → „hop-by-hop metrika vs link-state metrika".

### Konkrétní protokoly

- **RIP** (Routing Information Protocol) – DV, metrika **hop count**, **∞ = 16** (max. 15 hopů), aktualizace každých **30 s** přes **UDP**, timeout 180 s, triggered updates. RIPv2 přidává masku podsítě (VLSM) a autentizaci, multicast 224.0.0.9. Vhodné pro malé sítě.
- **IGRP/EIGRP** (Cisco) – DV, **kompozitní metrika** (bandwidth, delay, reliability, load). EIGRP přidává loop-free routing (DUAL), VLSM, spolehlivé doručení.
- **OSPF** (Open Shortest Path First) – nejpoužívanější LS, metrika **cost = 10⁸/bandwidth**, **bez hop-count**, podpora oblastí (areas), autentizace, load-balancing po cestách se stejnou cenou, **Hello pakety** pro tvorbu sousedství.
- **BGP-4** (Border Gateway Protocol) – **Path Vector** (posílají se celé cesty, ne jen ceny) → detekce smyček a definice **politik (policies)**. Mezi autonomními systémy, běží **nad TCP (port 179)**, používá CIDR. Zprávy: OPEN, UPDATE, KEEPALIVE, NOTIFICATION.

### Autonomní systémy (AS)

Rozdělení Internetu na **autonomní systémy (domény)** kvůli škálovatelnosti (menší tabulky, méně výměn). Každý AS má **16bit ASN** (přiděluje ICANN). Typy: Stub, Multihomed, Transit.

- **Interior routing** (uvnitř AS) – **IGP**: RIP, OSPF, (E)IGRP, IS-IS.
- **Exterior routing** (mezi AS) – **EGP**: **BGP-4**.

### Jak funguje router (časté!)

Router plní dvě úlohy: **(1) routing** – buduje pohled na topologii a počítá nejlepší cesty (uloží do **forwarding table**), **(2) packet forwarding** – přesun paketu z příchozího (ingress) na odchozí (egress) rozhraní. Při forwardingu: validace IP hlavičky, **dekrement TTL** (a zahození při 0 + ICMP), **přepočet checksum**, **route lookup** (longest prefix matching s CIDR), případně fragmentace.

### MPLS (Multiprotocol Label Switching)

Forwarding podle **návěští (labelu)** přidaného před paket při vstupu do MPLS sítě; vnitřní routery nezkoumají IP adresu – rychlejší fixní vyhledávání návěští místo longest-prefix. Návěští odpovídají cílovým sítím; umožňuje traffic engineering.

---

## 7. IPv6 — pokročilé funkce

> [!example] Často: „IPv4 vs IPv6 adresy, jak vypadají, proč nový protokol."

### Proč nový protokol

Hlavní impuls: **vyčerpání adresního prostoru IPv4**. Dále: slabá podpora real-time, žádná bezpečnost, autokonfigurace, mobilita.

### Hlavní vlastnosti

- **Větší adresní prostor** – **128 bitů** (≈ 3×10³⁸ adres).
- **Jednodušší hlavička** – pevných **40 B**, jen nejnutnější informace; checksum, options a fragmentace **odstraněny** ze základní hlavičky.
- **Rozšiřitelnost** – přes **extension headers** (Hop-By-Hop, Routing, Fragment, ESP, AH).
- **Podpora real-time** – Flow Label, priority (Traffic Class).
- **Bezpečnost** – integrovaný **IPsec** (autentizace, šifrování, integrita).
- **Mobilita** – přes **home agents**.
- **Autokonfigurace** – stavová i bezstavová.

### Zápis IPv6 adres

Hexadecimálně, po dvojicích bajtů oddělených `:`. **Zkracování:** vedoucí nuly ve skupině lze vynechat (`0074`→`74`); souvislé skupiny nul lze nahradit `::` (**ale jen jednou** v adrese). Beztřídní (CIDR), např. `FDEC::BBFF:0:FFFF/60`.

![[ipv6_structure.png|452]]

### Typy IPv6 adres

- **Unicast** – jedno rozhraní.
- **Multicast** – skupina (všem členům), prefix `ff00::/8`.
- **Anycast** – novinka; skupina příjemců, ale data jdou jen **nejbližšímu** členovi.
- **Broadcast neexistuje** – nahrazen speciálními multicast skupinami (`FF02::1` = všechny uzly na LAN).
- **Link-local** – prefix `FE80::/10`, jen pro lokální spoj.

### Hlavička IPv6 (40 B)

Version, **Traffic Class** (priorita, DS/DSCP), **Flow Label** (20bit, identifikace toku), Payload length, **Next header** (typ následující hlavičky), **Hop limit** (≈ TTL), Source/Destination (128 bit).

### Neighbor Discovery Protocol (NDP)

Náhrada za ARP (z IPv4), součást **ICMPv6**. Zprávy: **RS** (Router Solicitation), **RA** (Router Advertisement), **NS** (Neighbor Solicitation), **NA** (Neighbor Advertisement), **Redirect**. Funkce: rozlišení L2 adres, **DAD** (Duplicate Address Detection), **NUD** (Neighbor Unreachability Detection), autokonfigurace.

### Autokonfigurace

- **Stavová (stateful)** – jako DHCP (**DHCPv6**).
- **Bezstavová (stateless)** – uzel si sám vytvoří **link-local adresu** (prefix FE80 + 64bit interface ID z MAC nebo náhodný), ověří unikátnost (DAD), pak z **RA** od routeru získá síťový prefix a sestaví globální adresu. Lze kombinovat.

### IPv6 — Path MTU Discovery

**Routery v IPv6 nesmí fragmentovat** – jen koncové uzly. Příliš velký datagram router zahodí a pošle ICMPv6 **Packet Too Big**; zdroj postupně zmenšuje velikost.

### IPv6 mobilita

**Home Address** (trvalá), **Care-of Address** (v cizí síti), **Home Agent (HA)** (router v domácí síti tuneluje pakety mobilnímu uzlu), **Correspondent Node**. Optimalizace cesty přes **Return Routability Procedure** (uzel prokáže, že vlastní obě adresy).

### IPv6 bezpečnost — IPsec

**CIA** (Confidentiality, Integrity, Availability) + **AAA** (Authentication, Authorization, Accounting) + nonrepudiation.

- **AH** (Authentication Header) – jen **autentizace + integrita** (ICV), nešifruje, autentizuje celý datagram.
- **ESP** (Encapsulating Security Payload) – **šifrování (důvěrnost)** + volitelně autentizace; neautentizuje vnější IP hlavičku.
- **Security Association (SA)** – jednosměrná dohoda (klíč + mechanismus); pro plně duplexní šifrovanou + autentizovanou komunikaci = **4 SA**. Identifikováno **SPI**.
- Režimy: **Transport** (chrání jen payload od L4) a **Tunnel** (chrání celý původní IP datagram, přidá novou IP hlavičku).
- Správa klíčů: **IKEv2**.

> [!warning] IPv6 NENÍ samo o sobě bezpečnější než IPv4 – v IPv6 je IPsec povinnou součástí stacku, v IPv4 se instaluje zvlášť, ale mechanismy jsou stejné.

### IPv6 QoS — IntServ vs DiffServ

- **IntServ (Integrated Services)** – rezervace prostředků **per-flow** end-to-end (protokol **RSVP**), Admission Control. Nevýhoda: stav ve vnitřních uzlech → špatná škálovatelnost.
- **DiffServ (Differentiated Services)** – pakety se **označí třídou (DSCP)** na vstupu do sítě, vnitřní uzly jen čtou značku. Bez rezervací, bez stavu → **dobře škáluje**. Značka v poli **TOS (IPv4) / Traffic Class (IPv6)**.

### Přechod IPv4 → IPv6

- **Dual Stack** – běží oba zásobníky současně (nejflexibilnější, ale dvojí spotřeba).
- **Tunneling** – IPv6 zapouzdřen do IPv4 (6to4, Teredo, ISATAP).
- **Translators (NAT-PT)** – překlad IPv6 ↔ IPv4 (IPv6-only ↔ IPv4-only uzly).

---

## 8. Transportní vrstva (L4) — TCP a UDP

> [!example] „TCP vs UDP, rozdíly" – klíčové slovo, které komise chce slyšet, je **congestion control**! Plus spolehlivost a spojovanost.

Poskytuje **proces-proces** (end-to-end) doručení; **porty** (16bit, 0–65535) identifikují aplikaci. Aplikace je v síti jednoznačně dána dvojicí **IP:port**. Služby: packetizing, connection control, adresace (porty), spolehlivost, flow control, error control, **congestion control + QoS**.

**Spojované vs nespojované:**

- Spojované (connection-oriented): nejprve se naváže spojení, pakety číslovány a potvrzovány.
- Nespojované: pakety bez navázání spojení, nečíslované, mohou se ztratit/přeházet.

### UDP (User Datagram Protocol)

- **Nespojovaný, nespolehlivý, best-effort**, minimální overhead.
- Přidává k IP jen **proces-proces komunikaci (porty)** a jednoduchou kontrolu chyb (checksum).
- Spolehlivost si musí zajistit aplikace.
- **Hlavička (8 B):** source port, destination port, length, checksum.
- **Použití:** DNS, DHCP, TFTP, **real-time přenosy (VoIP, video)**, **multicast**, jednoduché request-reply.

### TCP (Transmission Control Protocol)

- **Spojovaný, plně spolehlivý**, **byte-stream** orientovaný (UDP pracuje s bloky).
- Před komunikací **three-way handshake** (SYN → SYN/ACK → ACK).
- Spojení rozpoznatelné jen na koncových uzlech (routery o něm nevědí); **plně duplexní**; **piggybacking** (řídicí data v opačném směru); jen **point-to-point** (žádný multicast).
- **Hlavička:** source/destination port, **sequence number** (číslo prvního bajtu dat v segmentu), **acknowledgement number** (očekávaný další bajt), header length, **6 control bitů** (SYN, ACK, FIN, RST, PSH, URG), **window size** (flow control), checksum, urgent pointer.
- Spolehlivost zajištěna opakovaným vysíláním (**ARQ**).

> [!important] **Flow Control vs Congestion Control** (komise chce slyšet rozdíl!)
> 
> - **Flow Control** – chrání **příjemce** před zahlcením; řízeno **receiver window (rwnd)** – explicitní zpětná vazba od příjemce.
> - **Congestion Control** – chrání **síť** před zahlcením; řízeno **congestion window (cwnd)** na straně odesílatele (odhad dostupné propustnosti, **AIMD**).
> - Skutečně odeslané množství = **min(rwnd, cwnd)**.

### Porovnání TCP vs UDP

|             | **UDP**                            |  **TCP**                            |
| ------------------- | ---------------------------------- | --------------------------- |
| Spojení             | spojované (handshake)              | nespojované                 |
| Spolehlivost        | plně spolehlivé (ACK, retransmise) | nespolehlivé (best-effort)  |
| Pořadí              | zachované                          | nezaručené                  |
| Řízení toku         | ano (rwnd)                         | ne                          |
| **Řízení zahlcení** | **ano (cwnd, AIMD)**               | **ne**                      |
| Orientace           | byte-stream                        | zprávy/bloky                |
| Overhead/hlavička   | velký (≥20 B)                      | malý (8 B)                  |
| Vysílání            | jen point-to-point                 | i multicast/broadcast       |
| Použití             | web (HTTP), e-mail, FTP, SSH       | DNS, DHCP, VoIP, video, hry |

---

## 9. Aplikační vrstva (L7) a praktické protokoly

Poskytuje služby uživateli; aplikační protokoly (HTTP, SMTP) jsou **součástí** aplikací (web, e-mail), ne samostatné aplikace. Definují typy/syntaxi/sémantiku zpráv a pravidla výměny.

**Modely:** Client-Server (tenký/tlustý klient) vs **Peer-to-peer**; **pull** (iniciuje klient) vs **push** (iniciuje server).

### DNS (Domain Name System)

Překlad **doménových jmen ↔ IP adresy**. Hierarchický, distribuovaný. Běží převážně přes **UDP port 53** (větší odpovědi/zónové přenosy přes TCP). Typy záznamů: A (IPv4), AAAA (IPv6), MX (mail), CNAME, NS, PTR (reverzní).

### DHCP (Dynamic Host Configuration Protocol)

> [!example] „Co dostanu, když se připojím k síti / plug and play?" → **DHCP**.

Automaticky přiděluje stanici: **IP adresu, masku podsítě, výchozí bránu (gateway), adresy DNS serverů** (případně NTP, doménu). Proces **DORA**: Discover → Offer → Request → Ack (přes UDP, porty 67 server / 68 klient).

### Well-known porty (komise: „vyjmenuj")

|Port|Protokol| |Port|Protokol|
|---|---|---|---|---|
|20/21|FTP (data/řízení)||80|HTTP|
|22|SSH||110|POP3|
|23|Telnet||143|IMAP|
|25|SMTP||179|BGP|
|53|DNS||443|HTTPS|
|67/68|DHCP (server/klient)||993/995|IMAPS/POP3S|

### Putování zprávy po vrstvách (enkapsulace) — příklad e-mailu (SMTP)

1. **L7:** aplikace (e-mailový klient) vytvoří zprávu pomocí **SMTP**.
2. **L4:** TCP přidá hlavičku (port 25) → **segment**, naváže spojení, zajistí spolehlivost.
3. **L3:** IP přidá hlavičku (zdroj/cíl IP) → **paket/datagram**, určí směrování.
4. **L2:** přidá rámec s MAC adresami → **frame**, řeší lokální doručení.
5. **L1:** odešle jako **bity/signál** po médiu. Na každém routeru: rozbalí se po L3, rozhodne se o dalším hopu (TTL−1, přepočet checksum), znovu zabalí. U příjemce: opačná **dekapsulace** odspodu nahoru. → „peer vrstvy spolu logicky komunikují, fyzicky to jde přes všechny nižší".

---

## 10. Praktické scénáře z praxe (časté otázky komise)

> [!example] **OŠLEJŠKOVA OBLÍBENÁ:** „Co všechno musíš nastavit na PC kromě IP adresy, abys se připojil k síti (bez DHCP)?"

**Odpověď: IP adresa + maska podsítě + výchozí brána (default gateway)** (+ **DNS server** pro překlad jmen).

- **IP adresa** – jednoznačná identifikace zařízení v síti.
- **Maska podsítě** – určí, která část IP je síťová a která hostitelská → zařízení pozná, zda je cíl ve stejné podsíti (přímé doručení), nebo mimo (poslat na bránu).
- **Výchozí brána (gateway)** – IP routeru, kam se posílá provoz mimo lokální podsíť.
- **DNS server** – překlad doménových jmen na IP (jinak fungují jen číselné adresy).
- **S DHCP** se tohle vše získá automaticky („plug and play").

**Jak připojit zařízení k síti a zabezpečit ho (Švenda):** fyzické/bezdrátové připojení k L2 → získání IP konfigurace (DHCP / ruční IP+maska+brána+DNS) → adresování (L3) a směrování ven přes bránu → **zabezpečení:** šifrování spoje (**TLS/SSL** pro TCP aplikace, **IPsec** pro síťovou vrstvu), autentizace, firewall/NAT, u Wi-Fi WPA2/WPA3.

**Jak funguje router:** viz sekce 6 – buduje směrovací/forwarding tabulku (přes routing protokoly), pak forwarduje pakety podle cílové IP (longest prefix match), dekrementuje TTL, přepočítává checksum, případně fragmentuje, mezi sítěmi překládá adresy (NAT).

---

## 11. Peer-to-peer (P2P) sítě

**Definice:** distribuované systémy z propojených uzlů, schopné se **samoorganizovat** do topologií za účelem sdílení prostředků (obsah, CPU, úložiště, pásmo), odolné vůči výpadkům, **bez centrálního serveru/autority**.

### Client-Server vs P2P

| |**Client-Server**|**P2P**|
|---|---|---|
|Role|klient žádá, server poskytuje|každý peer je **klient i server**|
|Komunikace|klienti nekomunikují přímo|peeři komunikují přímo|
|Škálovatelnost|omezená serverem|**roste s počtem peerů**|
|Bezpečnost|centralizovaná (snazší)|distribuovaná (těžší)|
|Spolehlivost|redundantní servery (drahé)|„zdarma" – jiný peer zastoupí|
|Single point of failure|ano (server)|typicky ne|

**Vlastnosti P2P:** symetrické role, škálovatelnost (žádná all-to-all koordinace), heterogenita, decentralizace, dynamičnost, sdílení prostředků, samoorganizace.

### Overlay síť

P2P je **virtuální (overlay) síť** položená nad fyzickou (TCP/IP). „Vzdálenost" se měří v **overlay hopech**; sousedé v overlay mohou být fyzicky daleko. Metriky topologie: **Diameter** (nejdelší vzdálenost – minimalizovat) a **Average Degree** (počet spojení na peer). Topologie: Random Mesh, Tiered (super-peers), Ordered Lattice.

**Objevení peerů (peer discovery):** statická konfigurace, **centralizovaný adresář** (single point of failure), **member propagation** (od jednoho známého peera se dozvím o dalších).

### Taxonomie P2P

- **Centralizované P2P** – centrální server pomáhá lokalizovat zdroje, pak komunikace přímo. Příklad: **Napster, SETI@home, BOINC**. Nevýhody: SPOF, bottleneck, horší škálovatelnost.
- **Decentralizované (pure) P2P** – rovné role, jen částečný pohled. Příklad: **Gnutella, FreeNet**. Imunní vůči SPOF, dobře škáluje.
    - **Unstructured** – peer si drží svá data + sousedy; vyhledávání **floodingem** (žádná záruka úplnosti/času).
    - **Structured** – data umístěna podle pravidel (**DHT**), mapování data↔peer → záruka nalezení v **O(log N)**.
- **Hybridní P2P** – bez centrálního serveru, ale silnější uzly = **super-peers** plní serverové role (Kazaa, Gnutella ultrapeers).

### Routing/lookup v P2P

- **Centralized lookup (Napster)** – jednoduché, O(n) stavu na serveru, SPOF.
- **Flooded queries (Gnutella)** – robustní, ale O(n) zpráv; omezeno **TTL**; varianty BFS, Directed BFS, random walk.
- **Routed DHT queries (Chord, CAN, Pastry, Tapestry)** – O(log N).

**Chord (DHT):** konzistentní hashovací funkce mapuje uzly (z IP) i data (z klíče) na **m-bitový identifikátor** v kruhu `0…2ᵐ−1`. Klíč `k` patří prvnímu uzlu po směru hodinových ručiček (successor). **Finger table** zrychluje vyhledávání na O(log N). **CAN** = vícerozměrný grid, **Pastry/Tapestry** = mesh.

**Příklady z praxe:** sdílení souborů (BitTorrent, eMule), VoIP (Skype historicky), distribuované výpočty (SETI@home, Folding@home), blockchain/kryptoměny, distribuovaná úložiště.

---

## 12. Ad-hoc a senzorové sítě (MANETs, WSN)

### Motivace

Klasické bezdrátové sítě = **infrastrukturní** (buňky, base stations, páteř – GSM, Wi-Fi AP). Co když infrastruktura není / je drahá / není čas? (katastrofy, vojenské operace, odlehlá místa) → **bezdrátové ad-hoc sítě** (ad-hoc = „pro tento účel").

### Wireless Ad-hoc Network

Kolekce **autonomních uzlů** komunikujících přes **multihop rádiovou síť**, decentralizovaně. **Každý uzel je host i router.** Topologie je dynamická, řízení distribuované.

- **Výhody:** rychlá konstrukce, žádné dráty, odolnost (no SPOF jako base station), spektrálně efektivnější.
- **Problémy:** chybí centrální entita (nutná **samoorganizace**), omezený dosah (multihop), **mobilita** uzlů → **MANET (Mobile Ad-hoc Network)**. Klíčové výzvy: **MAC** (bez base station) a **směrování**.
- **VANETs** – vozidla jako uzly; pohyb organizovanější než u MANET → specializované protokoly.

### Wireless Sensor Networks (WSN)

M�sto interakce s člověkem **interakce s prostředím** – síť zapuštěná do prostředí; uzly se senzory/aktuátory měří/ovlivňují prostředí a bezdrátově komunikují. (WSAN = + aktuátory.)

- **Aplikace:** detekce požárů, monitoring zvěře (habitat monitoring), precizní zemědělství, inteligentní budovy/mosty (monitoring napětí po zemětřesení).
- **HW:** mikrokontrolér (např. ATmega128), rádiový transceiver, **baterie**, senzory. **SW:** event-driven OS (**TinyOS**).
- **Klíčové omezení: ENERGIE** (baterie) → protokoly musí být **energeticky efektivní** (cesty s nízkou spotřebou energie/bit, energy harvesting). Uzly často nemají globálně unikátní adresu.

### MANET vs WSN vs P2P

- **MANET vs WSN:** obojí řeší omezenou energii; WSN orientované na data/prostředí (data-centric, komunikace k sběrnému uzlu **sink**), často bezadresové; MANET orientované na klasickou komunikaci mezi uzly.
- **MANET vs P2P:** obojí samoorganizující, decentralizované, dynamické. MANET je spíš **platforma** (L2/L3) pro P2P aplikace; logické sousedství v P2P ≠ fyzické v MANET → P2P protokoly nutno přizpůsobit specifikům MANET.

### MAC v ad-hoc/senzorových sítích

Cíl: minimalizovat kolize + **energetická efektivnost**. Zdroje plýtvání energií: **kolize, overhearing** (zaslechnutí cizího provozu), **idle listening** (zbytečné naslouchání), protocol overhead.

> [!example] Dva zásadní problémy bezdrátového sdíleného média:
> 
> - **Hidden terminal (skrytý uzel)** – kolize, protože dva uzly mimo dosah jeden druhého, ale v dosahu příjemce, vysílají současně.
> - **Exposed terminal (odhalený uzel)** – uzel zbytečně nevysílá, protože slyší blízké vysílání, ačkoli by jeho přenos jinému cíli nevadil.

**Třídy MAC protokolů:**

- **Contention-based** – žádná rezervace, soupeření o kanál (kolize nutno řešit). Bez QoS záruk.
    - **BTMA (Busy Tone)** – oddělený řídicí kanál s „busy tone" (špatné využití pásma).
    - **MACA** (Multiple Access Collision Avoidance) – signalizační pakety **RTS** (Request to Send) / **CTS** (Clear to Send); RTS/CTS nesou dobu přenosu → okolní uzly si uloží **NAV** (Network Allocation Vector) a mlčí. (Princip CSMA/CA.)
    - **PCM** – řízení vysílacího výkonu kvůli úspoře energie.
- **Contention-based s rezervací** (MACA/PR) a **se scheduling** (LEACH, TRAMA) – QoS/priority, ohled na baterii.
- **S-MAC (Sensor-MAC)** – zavádí **listen/sleep cykly** (duty cycle) → šetří energii při nízké datové rychlosti; uzly synchronizují rozvrhy přes SYNC pakety.

### Směrování v ad-hoc sítích

- **Proaktivní (table-driven)** – periodická výměna topologie, trasy připravené dopředu.
    - **DSDV** (Destination-Sequenced Distance Vector) – periodická výměna celých tabulek, sekvenční čísla proti smyčkám.
    - **OLSR** (Optimized Link State Routing) – LS, ale flooding optimalizován přes **MPR (MultiPoint Relays)** místo plného floodingu.
    - (+) data lze poslat kdykoli; (−) overhead i bez provozu.
- **Reaktivní (on-demand)** – trasa se hledá až při potřebě.
    - **DSR** (Dynamic Source Routing) – zdroj flooduje **RREQ**, uzly připisují svou identitu, cíl pošle **RREP** zpět; source routing (celá cesta v paketu); route caching.
    - **AODV** (Ad-hoc On-Demand Distance Vector) – jako DSR, ale uzly si pamatují předchozí hop (next-hop, ne celá cesta) → menší per-packet overhead.
    - (+) malý overhead bez provozu; (−) zpoždění při hledání trasy.

---

## 13. Vysokorychlostní sítě (TCP na sítích s vysokou kapacitou a latencí)

> [!info] Téma PA159/PA191 lekce „Behind Traditional TCP" – protokoly pro **high-throughput, high-latency** sítě (long fat networks, LFN).

### Problém klasického TCP na LFN

Spoje s vysokou kapacitou **a** vysokou latencí (např. 10 Gb/s, RTT 100 ms). Velký **BDP (Bandwidth-Delay Product)** → potřeba obrovské okno. Klasické TCP (AIMD) roste pomalu a jediná ztráta drasticky propadne cwnd → **terribly slow**, nedosáhne max. propustnosti. Hodnocení protokolů: **aggressiveness** (využití pásma), **responsiveness** (zotavení ze ztráty), **fairness** (férový podíl).

### Klasické TCP — congestion control
- **AIMD** (Additive Increase Multiplicative Decrease) – základ.
- **Tahoe** – cwnd+1/RTT bez ztráty; při ztrátě cwnd=1, slow start.
- **Reno** – + **fast retransmit** (3 duplicitní ACK → okamžitá retransmise bez čekání na timeout) + **fast recovery** (sstresh=cwnd=0,5·cwnd).
- **NewReno** – více segmentů ve fast retransmit.
- **SACK** (Selective ACK) – retransmise jen ztracených paketů.
- **Vegas** – sleduje **RTT**; při růstu RTT (=blížící se zahlcení) lineárně snižuje okno (delay-based).

(TBD - pridat obrazky s grafama)

bw = (8 · MSS · ownd)/RTT, kde ownd = min(rwnd, cwnd).

### Řešení (přehled)

1. **Multi-stream TCP** – jeden tok přes víc TCP spojení (GridFTP, bbftp); pomáhá jen u izolovaných ztrát, startuje lineárně.
2. **Ladění implementace** – TCP checksum offloading, **zero-copy** (sendfile), **Web100** (instrumentace Linux TCP/IP stacku, auto-tuning).
3. **Konzervativní rozšíření TCP** (mění jen congestion control, většinou jen odesílatel):
    - **GridDT** – upravený AIMD.
    - **Scalable TCP** – **MIMD** (Multiplicative Increase Multiplicative Decrease); při malém okně/vyšší ztrátě přepne na AIMD.
    - **HighSpeed TCP (HSTCP)** – AIMD/MIMD, pro malá okna se chová jako klasické TCP.
    - **H-TCP, BIC-TCP** (binární vyhledávání cwnd), **CUBIC-TCP** (výchozí v Linuxu, kubická funkce času od poslední ztráty).
4. **TCP s podporou IP vrstvy** (potřeba změny v síti):
    - **QuickStart** – IP volba pro rychlý start (routery schvalují počáteční rychlost).
    - **E-TCP / ECN** (Explicit Congestion Notification) – router nastaví bit při blížícím se zahlcení místo zahození paketu.
    - **FAST TCP** – kombinuje delay (RTT) + ECN + ztráty; větší kroky daleko od rovnováhy, menší blízko (na rozdíl od Vegas s fixními kroky).
5. **Přístupy mimo TCP:**
    - **Tsunami, RBUDP** (Reliable Blast UDP) – **out-of-band TCP řídicí kanál** + **UDP datový kanál**, NACK místo ACK, vysílání pevnou rychlostí; pro disk-to-disk přenosy.
    - **XCP** (eXplicit Control Protocol) – **per-paket zpětná vazba od routerů** (kolik přidat/ubrat do okna).
    - **SCTP** – multi-stream, **multi-homing** (víc IP na uzel), message-oriented jako UDP, ale spolehlivé + congestion control jako TCP.
    - **DCCP** – nespolehlivé (jako UDP) + congestion control.

**Z praxe:** agresivní protokoly se používají na **privátních/dedikovaných okruzích** (Grid, CESNET, vědecké přenosy dat – iGrid, SC).

---

## 14. Počítačové sítě a multimédia

**Multimédia** = informace složená z více typů médií (text, obraz, audio, video, interakce) integrovaných dohromady. Po digitalizaci lze aplikovat **kompresi**: **lossy** (ztrátová – odstraní redundanci, např. JPEG, MPEG) vs **lossless** (bezztrátová).

### Charakteristiky médií

- **Text** – inherentně digitální, malé nároky na pásmo, **bez real-time omezení**; pro bezeztrátovost TCP, jinak UDP.
- **Audio** – vzorkování (sampling); toleruje **1–2 % ztrát** bez velké degradace; interaktivní (telefonie) = **RTI (Real-Time Intolerant)**, vyžaduje přísné meze zpoždění/jitteru; streaming = **RTT (Real-Time Tolerant)**.
- **Obraz (image)** – statické; velké → komprese (často progresivní); **bez real-time omezení**.
- **Video** – velmi vysoké nároky na pásmo; komprese zmenší velikost, ale **zvyšuje latenci** → komprimované video nevhodné pro přísně real-time; chybové/real-time nároky podobné audiu.

### Klasifikace médií (síťový pohled)

- **Real-time (RT)** vs **Non-real-time (NRT)** (text, obrázky – bez striktních mezí zpoždění).
- RT dále: **Discrete Media (DM)** vs **Continuous Media (CM)** (proud zpráv).
- RT-CM dále: **delay tolerant** vs **delay intolerant**.

### Požadavky multimédií na síť

- **Traffic requirements** (meze real-time parametrů): **delay, jitter, bandwidth, reliability**.
    - **Delay** = doba cesty paketu zdroj→cíl; **Jitter** = proměnlivost mezipaketového zpoždění u příjemce.
    - Složky zpoždění: **processing, transmission (vč. MAC access delay), propagation** (200 m ≈ 1 ms, 20 000 km ≈ 100 ms), **routing/queuing**.
    - Pozn.: **CSMA/CD Ethernet nedává záruky** na MAC access delay (nedeterminismus) → Isochronous Ethernet (802.9), QoS.
- **Functional requirements:** **multicast** (jedna kopie dat per spoj – native; nebo virtuální/aplikační multicast), **bezpečnost** (AH/ESP), **mobilita** (≈ IPv6), **session management**:
    - **Session Description** – popis relace (typy médií, kódování, čas) – **SDP**, **SIP**.
    - **Session Announcement** – **SAP**.
    - **Session Identification/Control** – relace z více proudů (audio+video), nutno udržet vztahy.
- **Přenosové protokoly:** **RTP/RTCP** (real-time přenos a kontrola) nad UDP.

**Z praxe (CESNET/FI MU):** SW i HW řešení pro přenos vysokorozlišovaného videa, vzdálená spolupráce, **CineGRID** (přenos digitálního filmu/4K přes vysokorychlostní sítě).

---

# 15. Q&A — reálné otázky komise a odpovědi

> [!note] Sestaveno z dotazníku komise (List „Summary"). Číslování otázek nemusí sedět – vybrány otázky z oblasti počítačových sítí (zkoušející Novák, Dohnal, Ošlejšek, Zlatuška, Čeleda, Krhovják, Švenda, Batko).

**Q1 (Novák): Popiš ISO/OSI model, jednotlivé vrstvy, IP protokol, TCP, UDP a jak funguje router.** A: Viz sekce 2 (7 vrstev + funkce + PDU), sekce 5 (IP – účel, adresace, datagram), sekce 8 (TCP spolehlivé/spojované vs UDP nespolehlivé/nespojované) a sekce 6 (router = routing + forwarding, TTL, longest prefix match). Nakresli OSI tabulku, ukaž enkapsulaci.

**Q2 (Novák): Nakresli ISO/OSI, popiš putování zprávy po vrstvách (např. SMTP). IP účel, IPv4/IPv6 adresy, rozdělení IPv4 adresy. TCP cíle, rozdíl TCP/UDP.** A: Enkapsulace shora dolů (sekce 9). IPv4 = 32 bitů, dělí se na **síťovou a hostitelskou část** (maskou); IPv6 = 128 bitů hex. **Pozor – komise (Matyska) chce u rozdílu TCP/UDP slyšet „congestion control"** (řízení zahlcení), které má jen TCP! (sekce 8.) Nepleť šifrování: TCP samo nešifruje – to dělá TLS/SSL nad ním.

**Q3 (Dohnal / Bühnová): Sítě obecně – co to je, popiš vrstvy OSI, směrování.** A: Síť = sdílení prostředků + komunikace (sekce 1). OSI vrstvy (sekce 2). Směrování na L3, hop-by-hop, DV vs LS (sekce 6).

**Q4 (Dohnal 2018): ISO OSI, všechny vrstvy. Co všechno dostanu, když se připojím k síti?** A: OSI (sekce 2). Po připojení (přes **DHCP**) dostanu **IP adresu, masku podsítě, výchozí bránu a DNS server** (sekce 9, 10).

**Q5 (Dohnal 2018): OSI vrstvy, směrování, IPv4, UDP, TCP.** A: Kombinace sekcí 2, 5, 6, 8. IPv4 datagram (TTL, fragmentace, protokol), směrovací protokoly.

**Q6 (Ošlejšek): Jak probíhá směrování v sítích a na jaké vrstvě? Vysvětli Distance Vector a Link State + příklad. Pak IP: co je potřeba zadat na PC kromě IP adresy, aby se správně načetla síť? K čemu NAT a jak funguje? Jak se řeší nedostatek IPv4 adres?** A: Směrování = **L3** (sekce 6). **DV** (Bellman-Ford, RIP, hop count, count-to-infinity) vs **LS** (Dijkstra, OSPF, kompletní mapa, cena). Kromě IP zadat **masku podsítě + výchozí bránu** (+ DNS) (sekce 10). **NAT** překládá privátní IP:port ↔ veřejnou (sekce 5). Nedostatek IPv4 řeší **NAT, CIDR/subnetting, privátní adresy, DHCP a IPv6**.

**Q7 (Ošlejšek): OSI model, detailně L1 a L2 vrstva. Protokoly obou vrstev. Jak funguje CSMA/CA, CSMA/CD, Aloha, Token Ring?** A: L1 (přenos bitů, média, multiplexing – sekce 3), L2 (rámce, MAC, error/flow control – sekce 4). **Aloha** (vysílej kdykoli, kolize→náhodné čekání), **CSMA/CD** (naslouchej + detekuj kolizi, drátový Ethernet), **CSMA/CA** (předcházení kolizím, Wi-Fi), **Token Ring** (vysílá držitel tokenu, bezkolizní) (sekce 4).

**Q8 (Ošlejšek 2021): Směrovací algoritmy (Link state vs Distance vector), jakou metrikou hodnotí cestu (hop-by-hop vs bandwidth/jitter/latency). Nakresli celý ISO-OSI model. Co potřebuji nastavit pro připojení PC k internetu bez DHCP?** A: DV vs LS + metriky (sekce 6: DV=hop count, OSPF cost=10⁸/bandwidth, lze i jitter/latence). OSI (sekce 2). Bez DHCP: **IP, Gateway, Mask** (+ DNS) a vysvětlit funkci každého (sekce 10).

**Q9 (Zlatuška 2020): Síťová zařízení, OSI, protokoly, médium, UDP/TCP, směrování, paket, IP, IP protokoly.** A: Zařízení (hub L1, switch/bridge L2, router L3 – sekce 4, 6), OSI (2), média (3), TCP/UDP (8), směrování (6), IP datagram a doprovodné protokoly ICMP/ARP/IGMP (5).

**Q10 (Čeleda 2021): DNS, DHCP, TCP, IP, vyjmenuj well-known porty. Komunikace přes vrstvy ISO/OSI.** A: DNS (překlad jmen, UDP/53), DHCP (DORA, IP+maska+brána+DNS), TCP/IP (sekce 8, 5), well-known porty (tabulka v sekci 9), enkapsulace přes vrstvy (sekce 9).

**Q11 (Krhovják 2021): Doplňující otázka k sítím – „plug and play".** A: **DHCP** – automatická konfigurace (IP, maska, brána, DNS) při připojení, bez ručního nastavení (sekce 9, 10). V IPv6 navíc **stateless autokonfigurace** přes NDP/RA (sekce 7).

**Q12 (Švenda 2021): Jak připojit zařízení k internetu a jak ho zabezpečit? (tcp, udp, ipsec, směrování, adresování)** A: Připojení a konfigurace (sekce 10), adresování (L3) a směrování přes bránu (6), zabezpečení: **TLS/SSL** nad TCP, **IPsec** (AH/ESP) na L3, WPA2/3 u Wi-Fi, firewall/NAT (sekce 7, 10).

**Q13 (Batko 2021): Co je počítačová síť? Popiš ISO/OSI model, jednotlivé vrstvy, jejich funkcionalitu, datové jednotky a protokoly.** A: Definice sítě (sekce 1), OSI tabulka s **PDU** (bit→rámec→paket→segment→data) a protokoly na vrstvách (sekce 2).

> [!tip] Závěrečné tipy ke zkoušce
> 
> - **Kresli** – OSI model, IPv4 adresu s maskou, enkapsulaci, DV/LS příklad, Chord kruh, RTS/CTS. Komise to oceňuje a získáš čas.
> - U **TCP vs UDP** vždy zmiň **congestion control** a spolehlivost.
> - U směrování řekni **„L3, hop-by-hop"** a rozliš **DV (Bellman-Ford, RIP) vs LS (Dijkstra, OSPF)**.
> - Měj připravenou **praktickou odpověď „IP + maska + brána + DNS"** a **NAT/IPv6** na nedostatek adres.
> - Když nevíš, mluv obecně a nech se navést – zkoušející většinou napovídají.