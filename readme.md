🌐 Enterprise Nätverksdesign: Tre-Lagers Modell med HSRP,OSPF & BGP
    En redundant och säker nätverksmiljö för högskolan, implementerad i Cisco Packet Tracer.

#Översikt:
    Detta projekt simulerar en tre-lagers nätverksarkitektur (Core-Distribution-Access) för en högskola med tre separata skolor:
        Teknisk högskola JTH (VLAN 10, 11, 100)
        International Bussiness skola JIBS (VLAN 20, 21, 101)
        Högskolan för lärande och kommunikation HLK (VLAN 30, 31, 102)

    ##Huvudfunktioner:

        -Redundans med HSRP för Distribution Layer.

        -Inter-VLAN routing via Layer 3-switchar.

        -Dual-homed Internet Edge med NAT/PAT och BGP.

        -OSPF för dynamisk routning med route summarization.

        -ACL både för Ingoing och Outgoing trafik för trafikfiltering (SSH, HTTP/HTTPS, ICMP).

🛠 Topologi & Enheter

    Core Layer:
        CS1, CS2 (Layer 3-switchar)

    Distribution Layer:
        DLS3-DLS8 (2x Layer 3-switchar per skola för redundans)

    Access Layer:
        6x Layer 2-switchar (2 per skola)

    Internet Edge:
        InternetEdge1, InternetEdge2 (Cisco ISR 4331 med BGP mot ISP)

#Konfigurationer

1. Dual-Homed Internet Edge
    Anslutning till ISP via fiber (BGP AS3301).
    Default routes mot ISP
    NAT Overloading (PAT) för VLAN 10-31.

2. Dynamisk Routning med OSPF
    Area 0: Kopplingar mellan Distribution-Core, Distribution-Distribution och Core-InternetEdge.
    Area 10: Vlan 10,11 och 100 (DLS3 och 4)
    Area 20: Vlan 20,21 och 101 (DLS5 och 6)
    Area 30: Vlan 30,31 och 102 (DLS7 och 8)
    Summering av areor
3. Redundans med HSRP
    Gemensam virtuell IP för Distribution-switchar.

4. Säkerhet (Internet Edge routrar)
    SSH Access endast från MGMT-VLAN (ACL).
    Extended ACL för Trafikfiltering:
        -Outgoing-Trafik
            Tillåt alla IP-adresser att skicka DNS-förfrågningar till högskolans DNS-server (193.10.208.22) om de använder en source port som är högre än 1023
            Klienter i VLAN 10 (Studenter på JTH) ska kunna nå webbservern på 193.10.31.167 endast via HTTP och HTTPS
            Klienter i VLAN 20 (Studenter på JIBS) ska kunna nå webbservern på 193.10.31.167 endast via HTTPS och ICMP
            Blockera all annan trafik.

        -Ingoing-Trafik
            Trafik från Internet mot alla lokala adresser ska endast tillåtas om det är servertrafik från HTTP, HTTPS, DNS eller ICMP
            Övrig trafik ska förbjudas. (Med undantag av viktiga nätverksfunktioner t.ex bgp,ospf osv...)

5. BGP mot ISP
    Annonsera publikt nätverk (1.2.3.4/32) via BGP:


#Testning & Verifiering:

    Klienter i student-VLAN ska kunna nå webbservern ute på Internet (193.10.31.167) enligt ACL specifikationer.
    Alla olika ACL-regler (SSH, Ingoing-Traffic, Outgoing-Traffic) ska fungera.
    BGP-peering ska ske mot ISP. Användaren på Internet (Internet User) ska kunna pinga den publika adressen 1.2.3.4 som annonseras ut via BGP

Lärdomar
    -Design av redundanta nätverk med HSRP och dual-homed ISP.
    -Route summarization i OSPF för optimerad routningstabell.
    -Trafiksegmentering med VLAN och ACL.