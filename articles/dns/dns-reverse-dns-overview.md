---
title: Omówienie odwrotnego systemu DNS na platformie Azure — usługa Azure DNS
description: W tej ścieżce szkoleniowej rozpocznij naukę, jak działa odwrotny dns i jak można go używać na platformie Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: bf3da62e989f0e029efdc8e9c70f5f45e0ddd765
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76932296"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure

W tym artykule przedstawiono omówienie działania odwrotnego systemu DNS i scenariuszy odwrotnego systemu DNS obsługiwanych na platformie Azure.

## <a name="what-is-reverse-dns"></a>Co to jest odwrócony DNS?

Konwencjonalne rekordy DNS umożliwiają mapowanie z nazwy DNS (takiej jak "www.contoso.com") na adres IP (na przykład 64.4.6.100).  Reverse DNS umożliwia translację adresu IP (64.4.6.100) z powrotem na nazwę ("www.contoso.com").

Reverse DNS rekordy są używane w różnych sytuacjach. Na przykład odwrotne rekordy DNS są szeroko stosowane w zwalczaniu spamu e-mail, weryfikując nadawcę wiadomości e-mail.  Odbierający serwer poczty pobiera odwrotny rekord DNS adresu IP serwera wysyłającego i sprawdza, czy ten host jest upoważniony do wysyłania wiadomości e-mail z domeny źródłowej. 

## <a name="how-reverse-dns-works"></a>Jak działa odwrócony DNS

Rekordy ODWROTNEGO DNS są hostowane w specjalnych strefach DNS, znanych jako strefy "ARPA".  Strefy te tworzą oddzielną hierarchię DNS równolegle z normalną hierarchią hostingową domen, takich jak "contoso.com".

Na przykład rekord DNS "www.contoso.com" jest implementowany przy użyciu rekordu DNS "A" o nazwie "www" w strefie "contoso.com".  Ten rekord A wskazuje na odpowiedni adres IP, w tym przypadku 64.4.6.100.  Wyszukiwanie wsteczne jest implementowane oddzielnie, przy użyciu rekordu "PTR" o nazwie "100" w strefie "6.4.64.in-addr.arpa" (należy pamiętać, że adresy IP są odwrócone w strefach ARPA).  Ten rekord PTR, jeśli został poprawnie skonfigurowany, wskazuje nazwę "www.contoso.com".

Gdy organizacji jest przypisany blok adresu IP, nabywa również prawo do zarządzania odpowiednią strefą ARPA. Strefy ARPA odpowiadające blokom adresów IP używanym przez platformę Azure są hostowane i zarządzane przez firmę Microsoft. Usługodawca sieciowy może obsługiwać strefę ARPA dla własnych adresów IP lub może zezwolić na hostowanie strefy ARPA w wybranej usłudze DNS, takiej jak Azure DNS.

> [!NOTE]
> Wyszukiwania DNS do przodu i wyszukiwania wstecznego DNS są implementowane w oddzielnych, równoległych hierarchiach DNS. Wyszukiwanie wsteczne dla "www.contoso.com" **nie** jest hostowane w strefie "contoso.com", a raczej jest hostowane w strefie ARPA dla odpowiedniego bloku adresów IP. Oddzielne strefy są używane dla bloków adresów IPv4 i IPv6.

### <a name="ipv4"></a>Protokół IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 powinna być w `<IPv4 network prefix in reverse order>.in-addr.arpa`następującym formacie: .

Na przykład podczas tworzenia strefy odwrotnej do hostowania rekordów dla hostów z adresami IP, które znajdują się w prefiksie 192.2.0/24, nazwa strefy zostanie utworzona przez izolowanie prefiksu sieci adresu (192.0.2), a następnie odwrócenie kolejności (2.0.192) i dodanie przyrostka `.in-addr.arpa`.

|Klasa podsieci|Prefiks sieci  |Odwrócony prefiks sieci  |Standardowy sufiks  |Nazwa strefy odwrotnej |
|-------|----------------|------------|-----------------|---------------------------|
|Klasa A|203.0.0.0/8     | 203        | .in-addr.arpa   | `203.in-addr.arpa`        |
|Klasa B|198.51.0.0/16   | 51.198     | .in-addr.arpa   | `51.198.in-addr.arpa`     |
|Klasa C|192.0.2.0/24    | 2.0.192    | .in-addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegacja IPv4 bez klasy

W niektórych przypadkach zakres ADRESów IP przydzielony organizacji jest mniejszy niż zakres klasy C (/24). W takim przypadku zakres IP nie mieści się na `.in-addr.arpa` granicy strefy w hierarchii strefy i dlatego nie można delegować jako strefy podrzędnej.

Zamiast tego inny mechanizm jest używany do przenoszenia kontroli nad poszczególnymi rekordami wyszukiwania wstecznego (PTR) do dedykowanej strefy DNS. Ten mechanizm deleguje strefę podrzędną dla każdego zakresu adresów IP, a następnie mapuje każdy adres IP w zakresie indywidualnie do tej strefy podrzędnej przy użyciu rekordów CNAME.

Załóżmy na przykład, że organizacji przyznano zakres IP 192.0.2.128/26 przez usługodawcę. Reprezentuje 64 adresy IP, od 192.0.2.128 do 192.0.2.191. Odwrócony DNS dla tego zakresu jest zaimplementowany w następujący sposób:
- Organizacja tworzy strefę wyszukiwania wstecznego o nazwie 128-26.2.0.192.in-addr.arpa. Prefiks "128-26" reprezentuje segment sieci przypisany do organizacji w zakresie klasy C (/24).
- Usługodawca isp tworzy rekordy NS w celu skonfigurowania delegowania DNS dla powyższej strefy ze strefy nadrzędnej klasy C. Tworzy również rekordy CNAME w nadrzędnej strefie wyszukiwania wstecznego (klasa C), mapując każdy adres IP w zakresie IP do nowej strefy utworzonej przez organizację:

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
- Następnie organizacja zarządza poszczególnymi rekordami PTR w strefie podrzędnej.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Wyszukiwanie wsteczne adresu IP "192.0.2.129" dla rekordu PTR o nazwie "129.2.0.192.in-addr.arpa". Ta kwerenda jest rozpoznawana za pośrednictwem CNAME w strefie nadrzędnej do rekordu PTR w strefie podrzędnej.

### <a name="ipv6"></a>Protokół IPv6

Nazwa strefy wyszukiwania wstecznego IPv6 powinna być w następującej formie:`<IPv6 network prefix in reverse order>.ip6.arpa`

Na przykład,. Podczas tworzenia strefy odwrotnej do hostowania rekordów dla hostów z adresami IP, które znajdują się w prefiksie 2001:db8:1000:abdc::/64, nazwa strefy zostanie utworzona przez izolowanie prefiksu sieci (2001:db8:abdc::). Następnie rozwiń prefiks sieciowy IPv6, aby usunąć [kompresję zerową](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), jeśli został użyty do skrócenia prefiksu adresu IPv6 (2001:0db8:abdc:0000::). Odwróć kolejność, używając kropki jako ogranicznika między każdą szesnastkową liczbą w prefiksie, aby zbudować odwrócony prefiks sieciowy (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) i dodać sufiks `.ip6.arpa`.


|Prefiks sieci  |Rozwinięty i odwrócony prefiks sieci |Standardowy sufiks |Nazwa strefy odwrotnej  |
|---------|---------|---------|---------|
|2001:db8:abdc::/64    | 0,0,0,0.c.d.b.a.8.b.d.0.1.0.0.2        | .ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102::/64    | 2,0,1,9,0,0,0,1,8 b.d.0,1,0,0,2        | .ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Obsługa platformy Azure dla systemu ODWRÓCONEGO DNS

Platforma Azure obsługuje dwa oddzielne scenariusze związane z odwrotnym systemem DNS:

**Hostowanie strefy wyszukiwania wstecznego odpowiadającej blokowi adresów IP.**
Usługa Azure DNS może służyć do [obsługi stref wyszukiwania wstecznego i zarządzania rekordami PTR dla każdego wyszukiwania DNS wstecznego](dns-reverse-dns-hosting.md), zarówno dla IPv4, jak i IPv6.  Proces tworzenia strefy wyszukiwania wstecznego (ARPA), konfigurowania delegowania i konfigurowania rekordów PTR jest taki sam jak w przypadku zwykłych stref DNS.  Jedyną różnicą jest to, że delegowanie musi być skonfigurowane za pośrednictwem usługodawcy isp, a nie rejestratora DNS i należy używać tylko typu rekordu PTR.

**Skonfiguruj rekord odwrotnego DNS dla adresu IP przypisanego do usługi platformy Azure.** Platforma Azure umożliwia [skonfigurowanie wyszukiwania wstecznego adresów IP przydzielonych do usługi platformy Azure.](dns-reverse-dns-for-azure-services.md)  To wyszukiwanie wsteczne jest skonfigurowane przez platformę Azure jako rekord PTR w odpowiedniej strefie ARPA.  Te strefy ARPA, odpowiadające wszystkim zakresom adresów IP używanym przez platformę Azure, są hostowane przez firmę Microsoft

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat odwrotnego DNS, zobacz [odwrotne wyszukiwanie DNS na Wikipedii](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [hostować strefę wyszukiwania wstecznego dla zakresu adresów IP przypisanych usługodawcy sieci isp w usłudze Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Dowiedz się, jak [zarządzać wsteczne rekordy DNS dla usług platformy Azure](dns-reverse-dns-for-azure-services.md).

