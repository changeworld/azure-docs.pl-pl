---
title: Omówienie odwrotnego DNS na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak odwrotnego DNS działa i jak mogą być używane w Azure
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: victorh
ms.openlocfilehash: 9d3a62ec1c9ede1f25f2b53f800642a792b3aa28
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60192986"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Omówienie odwrotnego DNS i pomocy technicznej na platformie Azure

Ten artykuł zawiera omówienie sposobu odwrotnego DNS działa i odwrotnej scenariuszy DNS obsługiwanych na platformie Azure.

## <a name="what-is-reverse-dns"></a>Co to jest odwrotnym systemem DNS?

Konwencjonalne rekordy DNS Włącz mapowanie na podstawie nazwy DNS (takie jak "www.contoso.com"), aby adres IP (np. 64.4.6.100).  Odwrotnym systemem DNS umożliwia tłumaczenie adresu IP (64.4.6.100) do nazwy ("www.contoso.com").

Rekordami odwrotnego systemu DNS są używane w różnych sytuacjach. Na przykład rekordami odwrotnego systemu DNS są powszechnie używane w zapobieganiu spamu poczty e-mail, sprawdzając nadawcy wiadomości e-mail.  Odbieranie serwera poczty pobiera odwrotnej rekord DNS adresu IP serwera wysyłania i sprawdza, czy ten host jest autoryzowany do wysyłania wiadomości e-mail z domeny źródłowej. 

## <a name="how-reverse-dns-works"></a>Działa jak wstecznego DNS

Rekordami odwrotnego systemu DNS są hostowane w specjalnych stref DNS, znane jako "ARPA" strefy.  Te strefy tworzą hierarchię DNS oddzielne równolegle z normalnym hierarchii hosting domeny, taką jak "contoso.com".

Na przykład rekordów DNS "www.contoso.com" jest implementowany przy użyciu rekordu DNS "A" przy użyciu nazwy "www" w strefie "contoso.com".  Ten rekord A wskazuje na odpowiedni adres IP, w tym przypadku 64.4.6.100.  Wyszukiwanie wsteczne jest implementowane oddzielnie, przy użyciu rekordu "PTR" o nazwie "100" w strefie "6.4.64.in-addr.arpa" (Zauważ, że adresy IP zostały cofnięte w strefy ARPA.)  Ten rekord PTR, jeśli została skonfigurowana poprawnie, wskazuje nazwę "www.contoso.com".

Po przypisaniu blok adresów IP w organizacji, również uzyskać prawa do odpowiedniej strefy ARPA zarządzania. Strefy ARPA odpowiadający bloki adresów IP, które są używane przez platformę Azure są hostowane i zarządzany przez firmę Microsoft. Usługodawcy mogą być hostowane strefy ARPA własnych adresów IP dla siebie lub mogą zezwalać na hostowanie strefy ARPA w usłudze DNS w wybranych przez użytkownika, takich jak usługi Azure DNS.

> [!NOTE]
> Wyszukiwania do przodu DNS i wyszukiwania wstecznego DNS są implementowane w oddzielnych, równoległe hierarchii DNS. Wyszukiwanie wsteczne "www.contoso.com" jest **nie** hostowanych w strefie "contoso.com", zamiast jest ona hostowana na strefy ARPA odpowiedni blok adresów IP. Bloki adresów IPv4 i IPv6 są używane osobne strefy.

### <a name="ipv4"></a>IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 powinny być w następującym formacie: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Na przykład podczas tworzenia strefy wyszukiwania wstecznego rekordów hosta dla hostów z adresów IP, które znajdują się w prefiksie 192.0.2.0/24, nazwę strefy będzie tworzoną przez izolowanie prefiks sieci adresu (192.0.2), a następnie odwracanie kolejności (2.0.192) i dodanie sufiksu `.in-addr.arpa`.

|Klasa podsieci|Prefiks sieci  |Prefiksu odwróconej sieci  |Standardowa sufiks  |Nazwa strefy wyszukiwania wstecznego |
|-------|----------------|------------|-----------------|---------------------------|
|Klasa A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klasa B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klasa C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless delegowania IPv4

W niektórych przypadkach, zakres adresów IP przydzielone do organizacji jest mniejszy niż klasa C (/ 24) zakresu. W tym przypadku zakres adresów IP nie znajduje się w granicach strefy w ramach `.in-addr.arpa` strefa hierarchię i dlatego nie może być delegowane jako strefy podrzędnej.

Zamiast tego innego mechanizmu służy do przekazywania kontroli rekordów poszczególnych wyszukiwania wstecznego (PTR) do dedykowanych strefy DNS. Ten mechanizm deleguje strefy podrzędnej dla każdego zakresu adresów IP, a następnie mapuje każdy adres IP z zakresu indywidualnie do tej strefy podrzędnej przy użyciu rekordów CNAME.

Na przykład załóżmy, że organizacja otrzymuje 192.0.2.128/26 zakres adresów IP według jego usługodawcy internetowego. Reprezentuje 64 adresów IP, z 192.0.2.128 do 192.0.2.191. Odwrotnym systemem DNS dla tego zakresu jest wdrażany w następujący sposób:
- Organizacja tworzy strefę wyszukiwania wstecznego, o nazwie 128-26.2.0.192.in-addr.arpa. Prefiks "128-26' reprezentuje segmentu sieci przypisanym do organizacji w ramach klasy C (/ 24) zakresu.
- Usługodawca Internetowy tworzy rekordy NS, aby skonfigurować delegowanie DNS dla powyższych strefy w strefie nadrzędnej klasy C. Tworzy również rekordów CNAME w strefy wyszukiwania wstecznego nadrzędnego (klasy C), mapowanie każdego adresu IP w zakresie adresów IP do nowej strefy, utworzone przez organizację:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Organizacja zarządza poszczególnych rekordów PTR w ramach ich strefy podrzędnej.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Wyszukiwanie wsteczne dla zapytania "192.0.2.129" adres IP rekordu PTR systemu o nazwie "129.2.0.192.in-addr.arpa". To zapytanie jest rozpoznawany jako przy użyciu rekordu CNAME w strefie nadrzędnej, aby rekord PTR w strefie podrzędnej.

### <a name="ipv6"></a>IPv6

Nazwa strefy wyszukiwania wstecznego IPv6 powinien znajdować się w następującej postaci: `<IPv6 network prefix in reverse order>.ip6.arpa`

Na przykład. Podczas tworzenia strefy wyszukiwania wstecznego rekordów hosta dla hostów z adresów IP, znajdują się w 2001:db8:1000:abdc:: / 64 prefiks nazwy strefy zostałyby utworzone przez izolowanie prefiks sieci adresu (2001:db8:abdc::). Następnie rozwiń prefiks IPv6 sieci, aby usunąć [zero kompresji](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), jeśli został on użyty skrócenie czasu prefiks adresu IPv6 (2001:0db8:abdc:0000::). Odwracanie kolejności użycie kropki jako separator każdego liczbę szesnastkową w prefiksie, aby zbudować prefiksu odwróconej sieci (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) i Dodaj sufiks `.ip6.arpa`.


|Prefiks sieci  |Prefiks rozwinięte i odwróconej sieci |Standardowa sufiks |Nazwa strefy wyszukiwania wstecznego  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Pomoc techniczna platformy Azure dla odwrotnego systemu DNS

Platforma Azure obsługuje dwóch osobnych scenariuszy odnoszących się do odwrotnego systemu DNS:

**Hostowanie strefy wyszukiwania wstecznego, odpowiadający Twojej blok adresów IP.**
Usługa DNS platformy Azure można używać do [hostowanie stref wyszukiwania wstecznego i zarządzanie rekordów PTR dla każdego wyszukiwania wstecznego DNS](dns-reverse-dns-hosting.md), IPv4 i IPv6.  Proces tworzenia strefy wyszukiwania wstecznego (ARPA), konfigurowanie delegacji i skonfigurowanie rekordów PTR jest taka sama, jak w przypadku regularnego stref DNS.  Jedyne różnice są, delegowanie musi być skonfigurowany za pośrednictwem usługodawcy, a nie rejestratora DNS, i powinny być używane tylko typ rekordu PTR.

**Konfigurowanie zwrotnego rekordu DNS dla adresu IP przypisanego do usługi Azure.** System Azure umożliwia [Konfigurowanie wyszukiwania wstecznego dla adresów IP przydzielone do usługi Azure service](dns-reverse-dns-for-azure-services.md).  To wyszukiwanie wsteczne jest skonfigurowany przez platformę Azure jako rekord PTR w odpowiedniej strefy ARPA.  Te strefy ARPA, odpowiadające na wszystkie zakresy adresów IP używane przez platformę Azure, są obsługiwane przez firmę Microsoft

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji na temat odwrotnym systemem DNS, zobacz [wyszukiwania wstecznego DNS w witrynie Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [hostowanie strefy wyszukiwania wstecznego dla usługodawcy internetowego, przypisany zakresowi adresów IP w usłudze Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Dowiedz się, jak [Zarządzanie rekordami odwrotnego systemu DNS dla usług platformy Azure](dns-reverse-dns-for-azure-services.md).

