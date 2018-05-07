---
title: Omówienie wstecznego DNS na platformie Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wstecznego DNS działa i jak może służyć na platformie Azure
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: kumud
ms.openlocfilehash: 1ce14360d0f62a01172a8003e1d78a45885166f6
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Omówienie wstecznego DNS i pomocy technicznej na platformie Azure

Ten artykuł zawiera omówienie sposobu wstecznego DNS działa i odwrotnej scenariuszy DNS obsługiwane na platformie Azure.

## <a name="what-is-reverse-dns"></a>Co to jest wstecznego DNS?

Konwencjonalne rekordy DNS Włącz mapowania na podstawie nazwy DNS (np. www.contoso.com) na adres IP (na przykład 64.4.6.100).  Wstecznego DNS umożliwia tłumaczenie adresu IP (64.4.6.100) do nazwy (www.contoso.com).

Odwrotnej rekordy DNS są używane w różnych sytuacjach. Na przykład odwrotnej rekordy DNS są powszechnie używane w zapobieganiu spamu e-mail weryfikując nadawcy wiadomości e-mail.  Odbieranie serwera poczty pobiera wstecznego rekord DNS adres IP serwera wysyłania i sprawdza, czy ten host jest autoryzowany do wysyłania wiadomości e-mail z domeny pochodzenia. 

## <a name="how-reverse-dns-works"></a>Działa jak wstecznego DNS

Odwrotnej rekordy DNS są obsługiwane w specjalne stref DNS, znany jako strefy "ARPA".  Te strefy tworzą oddzielne hierarchii DNS równolegle z normalnym hierarchii hosting domeny, takie jak "contoso.com".

Na przykład rekordów DNS "www.contoso.com" jest implementowane przy użyciu rekordu DNS "A" o nazwie "www" w strefie "contoso.com".  Ten rekord A wskazuje odpowiedni adres IP, w tym przypadku 64.4.6.100.  Wyszukiwanie wsteczne jest implementowane oddzielnie, za pomocą rekordu "PTR" o nazwie "100" w strefie "6.4.64.in-addr.arpa" (Zauważ, że adresy IP są wycofywane w strefy ARPA.)  Ten rekord PTR, jeśli została skonfigurowana poprawnie, wskazuje nazwy www.contoso.com.

Jeśli organizacja jest przypisany blok adresów IP, również uzyskać prawo do zarządzania odpowiednie strefy ARPA. Strefy ARPA odpowiadający bloki adresów IP, które są używane przez usługę Azure są obsługiwane i zarządzany przez firmę Microsoft. Usługodawca Internetowy może obsługiwać strefy ARPA adresy IP dla Ciebie lub mogą umożliwić do hostowania strefy ARPA w usłudze DNS w wybranych przez użytkownika, takie jak usługi Azure DNS.

> [!NOTE]
> Wyszukiwania DNS do przodu i wyszukiwania wstecznego DNS są implementowane w oddzielnych, równoległe hierarchii DNS. Wyszukiwanie wsteczne "www.contoso.com" jest **nie** hostowana w strefie "contoso.com", a nie jest obsługiwany strefy ARPA na odpowiedni blok adresów IP. Bloki adresów IPv4 i IPv6 używane są osobne strefy.

### <a name="ipv4"></a>Protokół IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 powinny mieć następujący format: `<IPv4 network prefix in reverse order>.in-addr.arpa`.

Na przykład podczas tworzenia strefy wyszukiwania wstecznego do rekordów hosta dla hostów z adresów IP, które są w prefiksie 192.0.2.0/24, nazwę strefy zostałyby utworzone w przez izolowanie prefiksu sieci adresu (192.0.2), a następnie odwracanie kolejności (2.0.192) i dodawanie sufiks `.in-addr.arpa`.

|Klasa podsieci|Prefiks sieci  |Prefiks odwróconej sieci  |Standardowa sufiks  |Nazwa strefy wyszukiwania wstecznego |
|-------|----------------|------------|-----------------|---------------------------|
|Klasa A|203.0.0.0/8     | 203        | .w addr.arpa   | `203.in-addr.arpa`        |
|Klasa B|198.51.0.0/16   | 51.198     | .w addr.arpa   | `51.198.in-addr.arpa`     |
|Klasa C|192.0.2.0/24    | 2.0.192    | .w addr.arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Classless delegowania IPv4

W niektórych przypadkach zakres adresów IP przydzielone do organizacji jest mniejszy niż klasa C (/ 24) zakresu. W takim przypadku zakres adresów IP nie mieści się w granicach strefy w ramach `.in-addr.arpa` strefy hierarchię i dlatego nie może być delegowane jako strefy podrzędnej.

Zamiast tego inny mechanizm służy do przekazywania kontroli rekordów poszczególnych wyszukiwania wstecznego (PTR) do dedykowanych strefy DNS. Ten mechanizm deleguje strefy podrzędnej dla każdego zakres adresów IP, a następnie mapuje każdego adresu IP w zakresie indywidualnie do tej strefy podrzędnej przy użyciu rekordów CNAME.

Na przykład załóżmy, że organizacja otrzymuje 192.0.2.128/26 zakres IP przez jego usługodawcy internetowego. Reprezentuje 64 adresy IP z 192.0.2.128 do 192.0.2.191. Wstecznego DNS dla tego zakresu jest zaimplementowana w następujący sposób:
- Organizacja tworzy strefę wyszukiwania wstecznego o nazwie 128-26.2.0.192.in-addr.arpa. Prefiks "128-26' reprezentuje segment sieci przypisanym do organizacji w ramach klasy C (/ 24) zakresu.
- Usługodawca Internetowy tworzy rekordy NS, aby skonfigurować delegowanie DNS dla strefy powyżej ze strefy nadrzędnej klasy C. Tworzy również rekordy CNAME w strefie nadrzędnej (Klasa C) wyszukiwania wstecznego mapowanie poszczególne adresy IP na zakres adresów IP do nowej strefy utworzonej przez organizację:

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
- Organizacja zarządza poszczególnych rekordów PTR w ich strefy podrzędnej.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Wyszukiwanie wsteczne dla zapytania "192.0.2.129" adres IP dla rekordu PTR o nazwie "129.2.0.192.in-addr.arpa". To zapytanie rozpoznaje za pośrednictwem CNAME w strefie nadrzędnej rekordu PTR w strefie podrzędnej.

### <a name="ipv6"></a>Protokół IPv6

Nazwa strefy wyszukiwania wstecznego IPv6, powinna być w następującym formacie: `<IPv6 network prefix in reverse order>.ip6.arpa`

Na przykład. Podczas tworzenia strefy wyszukiwania wstecznego do rekordów hosta dla hostów z adresów IP które znajdują się w 2001:db8:1000:abdc:: / 64 prefiks, nazwę strefy zostałyby utworzone przez izolowanie prefiksu sieci adresu (2001:db8:abdc::). Następnie rozwiń węzeł prefiksu sieci IPv6, aby usunąć [zero kompresji](https://technet.microsoft.com/library/cc781672(v=ws.10).aspx), jeśli użyto w celu skrócenia prefiks adresu IPv6 (2001:0db8:abdc:0000::). Odwracanie kolejności kropką jako separator każdą liczbę szesnastkową prefiksu, tworzenie prefiks odwróconej sieci (`0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2`) i Dodaj sufiks `.ip6.arpa`.


|Prefiks sieci  |Prefiks rozwinięte i odwróconej sieci |Standardowa sufiks |Nazwa strefy wyszukiwania wstecznego  |
|---------|---------|---------|---------|
|2001:db8:abdc:: / 64    | 0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2        | . ip6.arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001:db8:1000:9102:: / 64    | 2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2        | . ip6.arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Obsługa platformy Azure wstecznego DNS

Azure obsługuje dwa oddzielne scenariusze odnoszących się do wstecznego DNS:

**Hosting strefy wyszukiwania wstecznego odpowiadający bloku adresu IP.**
Usługa DNS platformy Azure można używać do [przechowywania stref wyszukiwania wstecznego i zarządzanie nimi rekordów PTR dla każdego wyszukiwania wstecznego DNS](dns-reverse-dns-hosting.md)dla protokołów IPv4 i IPv6.  Proces tworzenia strefy wyszukiwania wstecznego (ARPA), konfigurowanie delegacji i konfigurowania rekordów PTR są takie same jak regularne strefy DNS.  Jedyne różnice są delegowanie musi być skonfigurowany za pomocą usługodawca Internetowy, a nie rejestratora DNS, czy tylko typ rekordu PTR powinien być używany.

**Skonfiguruj odwrotnej rekordu DNS dla adresu IP przypisanego do usługi Azure.** Platforma Azure umożliwia [skonfigurować wyszukiwanie wsteczne adresy IP przydzielone do usługi Azure](dns-reverse-dns-for-azure-services.md).  To wyszukiwanie wsteczne jest skonfigurowana przez platformę Azure jako rekordu PTR w odpowiedniej strefy ARPA.  Te strefy ARPA, odpowiadający wszystkich zakresów IP używanych przez platformę Azure, są obsługiwane przez firmę Microsoft

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać więcej informacji dotyczących wstecznego DNS, zobacz [istnienia wstecznego wyszukiwania DNS dla Wikipedia](http://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się, jak [hostowanie strefy wyszukiwania wstecznego zakres IP przypisany usługodawcy internetowego w usłudze Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Dowiedz się, jak [Zarządzanie odwrotnej rekordy DNS dla usług Azure](dns-reverse-dns-for-azure-services.md).

