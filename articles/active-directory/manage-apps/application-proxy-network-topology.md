---
title: Zagadnienia dotyczące topologii sieci, używając serwera Proxy usługi Azure Active Directory aplikacji | Dokumentacja firmy Microsoft
description: Obejmuje zagadnienia dotyczące topologii sieci, używając serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/28/2017
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: c3f07aa0ef4fea773e214e3e161a61e9337dc617
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/14/2018
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Zagadnienia dotyczące topologii sieci przy użyciu serwera Proxy usługi Azure Active Directory aplikacji

W tym artykule opisano zagadnienia dotyczące topologii sieci, podczas publikowania i zdalny dostęp do aplikacji przy użyciu serwera Proxy aplikacji usługi Azure Active Directory (Azure AD).

## <a name="traffic-flow"></a>Przepływ ruchu

Gdy aplikacja zostanie opublikowana przy użyciu serwera Proxy aplikacji usługi Azure AD, ruch z użytkowników do aplikacji przechodzi przez trzy połączenia:

1. Użytkownik nawiązuje połączenie z punktem końcowym publicznej usługi serwera Proxy aplikacji usługi Azure AD na platformie Azure
2. Serwer Proxy aplikacji usługi nawiązuje połączenie z łącznikiem serwera Proxy aplikacji
3. Łącznik serwera Proxy aplikacji łączy do aplikacji docelowej

![Diagram przedstawiający przepływ ruchu z użytkownika do aplikacji docelowej](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Lokalizacja dzierżawy i usługę serwera Proxy aplikacji

Po utworzeniu konta dla dzierżawy usługi Azure AD, region dzierżawy zależy od kraju, w którym można określić. Po włączeniu serwera Proxy aplikacji wystąpień usługi Serwer Proxy aplikacji dzierżawy są wybrane lub zostały utworzone w tym samym regionie co dzierżawy usługi Azure AD lub najbliższy region do niego.

Na przykład jeśli region dzierżawy usługi Azure AD jest Unii Europejskiej (UE), wszystkich łączników serwera Proxy aplikacji użyj wystąpień usługi w centrach danych platformy Azure w UE. Jeśli dostęp użytkowników opublikowane aplikacje, ruch przechodzi przez wystąpień usługi Serwer Proxy aplikacji w tej lokalizacji.

## <a name="considerations-for-reducing-latency"></a>Zagadnienia dotyczące zmniejszenia opóźnienia

Wszystkie rozwiązania proxy wprowadzić czas oczekiwania na połączenie sieciowe. Niezależnie od tego, który serwer proxy lub rozwiązanie sieci VPN wybierz jako rozwiązania dostępu zdalnego zawsze zawiera zestaw serwerów umożliwiające połączenie w sieci firmowej.

Organizacje zwykle zawierają punkty końcowe serwera w sieci obwodowej. Z serwera Proxy aplikacji usługi Azure AD natomiast ruch przechodzi przez usługę serwera proxy w chmurze podczas łączniki znajdują się w sieci firmowej. Brak sieci obwodowej jest wymagana.

Następne sekcje zawierają dodatkowe sugestie pomaga ograniczyć jeszcze bardziej opóźnienia. 

### <a name="connector-placement"></a>Umieszczanie łącznika

Serwer Proxy aplikacji wybiera lokalizację wystąpień automatycznie na podstawie lokalizacji użytkownika dzierżawcy. Jednak pojawi się zdecyduj, gdzie jest instalowany łącznik, co pozwala zdefiniowanie opóźnienia ruchu sieciowego.

Podczas konfigurowania serwera Proxy aplikacji usługi, na następujące pytania:

* Gdzie znajduje się aplikacja?
* Gdzie znajdują się większość użytkowników, którzy uzyskują dostęp do aplikacji
* Gdzie znajduje się wystąpienie serwera Proxy aplikacji
* Już masz połączenie sieciowe dedykowanych centrach danych platformy Azure, konfigurowanie, takich jak usługa Azure ExpressRoute lub podobne sieci VPN?

Łącznik ma do komunikowania się z system Azure, jak i aplikacje (kroki 2 i 3 w diagram przepływu ruchu), więc umieszczanie łącznika ma wpływ na opóźnienie te dwa połączenia. Podczas szacowania położenia łącznika, należy pamiętać następujące kwestie:

* Jeśli chcesz używać do logowania jednokrotnego ograniczone delegowanie protokołu Kerberos (KCD), łącznik musi wiersza procesów w centrum danych. Ponadto serwer łącznika musi być przyłączony do domeny.  
* W razie wątpliwości, należy zainstalować łącznik bliżej do aplikacji.

### <a name="general-approach-to-minimize-latency"></a>Ogólne podejście, aby zminimalizować opóźnienie

Aby zminimalizować czas oczekiwania na trasie ruchu, optymalizacja każdego połączenia sieciowego. Każde połączenie może zostać zoptymalizowana przez:

* Zmniejszenie odległość między dwa końce przeskoku.
* Wybieranie prawo sieci, aby przechodzić między nimi. Na przykład przechodzenie z sieci prywatnej, a nie publicznego Internetu może być szybsze, ze względu na dedykowany łącza.

Jeśli masz dedykowane łącza sieci VPN lub usługi platformy Azure i z siecią firmową, można użyć tego.

## <a name="focus-your-optimization-strategy"></a>Skoncentruj się strategii optymalizacji

Brak fragmentu, które można wykonać, aby kontrolować połączenie między użytkowników i usługę serwera Proxy aplikacji. Użytkownicy mogą uzyskiwać dostęp do aplikacji z siecią domową, kawiarni lub innego kraju. Zamiast tego można zoptymalizować połączeń z serwera Proxy aplikacji usługi z łączników serwera Proxy aplikacji w aplikacjach. Należy rozważyć zawierających następujące wzorce w danym środowisku.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Wzorzec 1: Umieszczanie łącznika bliski aplikacji

W sieci klienta, należy umieścić łącznika bliski aplikacji docelowej. Ta konfiguracja minimalizuje kroku 3 w diagramie topografii, ponieważ łączników i aplikacji są Zamknij. 

Jeśli Twojego łącznika wymaga wiersza z procesów z kontrolerem domeny, ten wzorzec jest korzystne. Większość klientów użycia tego wzorca, ponieważ działa dobrze w przypadku większości scenariuszy. Ten wzorzec mogą być również połączone z wzorcem 2, aby zoptymalizować ruch między usługą i łącznik.

### <a name="pattern-2-take-advantage-of-expressroute-with-public-peering"></a>Wzorzec 2: Korzystanie z usługi ExpressRoute z publicznej komunikacji równorzędnej

Jeśli masz usługi ExpressRoute z publicznej komunikacji równorzędnej umożliwia szybsze połączenia ExpressRoute ruch między serwerem Proxy aplikacji i łącznik. Łącznik jest nadal w sieci, bliski aplikacji.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Wzorzec 3: Korzystać z usługi ExpressRoute z prywatnej komunikacji równorzędnej

Jeśli masz dedykowane sieci VPN lub ustawienie usługi ExpressRoute z prywatnej komunikacji równorzędnej platformy Azure i z siecią firmową, masz inną opcję. W tej konfiguracji sieci wirtualnej platformy Azure jest zwykle traktowane jako rozszerzenie sieci firmowej. Można więc w centrum danych Azure jest instalowany łącznik i nadal spełniają wymagania małe opóźnienia połączenia łącznika do aplikacji.

Czas oczekiwania nie zostanie naruszony, ponieważ ruch jest przepływających przez dedykowane połączenie. Umożliwia również wyświetlenie ulepszone opóźnienia łącznika usługi Serwer Proxy aplikacji, ponieważ łącznik jest zainstalowany w centrum danych Azure bliski lokalizacja dzierżawy usługi Azure AD.

![Diagram przedstawiający łącznika instalowane w ramach centrum danych Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Inne podejścia

Chociaż ten artykuł koncentruje się umieszczanie łącznika, możesz również zmienić położenie aplikacji w celu uzyskania lepszej właściwości opóźnienia.

Organizacje są coraz, przenoszenie ich sieci w środowiskach hostowanych. Umożliwia to umieszczenie swoje aplikacje w środowisku hostowanej, która jest również częścią sieci firmowej i nadal mieścić się w domenie. W takim przypadku wzorców omówione w poprzednich sekcjach można zastosować do nowej lokalizacji aplikacji. Jeśli rozważasz tej opcji, zobacz [usług domenowych Azure AD](../../active-directory-domain-services/active-directory-ds-overview.md).

Ponadto należy wziąć pod uwagę organizowanie za pomocą łączników [grup łącznika](application-proxy-connector-groups.md) do docelowej aplikacji, które znajdują się w różnych lokalizacjach i sieci. 

## <a name="common-use-cases"></a>Typowe przypadki użycia

W tej sekcji możemy przeprowadzenie kilka typowych scenariuszy. Przyjęto założenie, że dzierżawy usługi Azure AD (i w związku z tym punkt końcowy usługi serwera proxy) znajduje się w Stanach Zjednoczonych (USA). Zagadnienia dotyczące omówione w za pomocą tych przypadkach stosuje się także do innych regionów na całym świecie.

W tych sytuacjach możemy wywołać każdego połączenia "przeskok" i numer je łatwiej omówienie:

- **Przeskoku 1**: użytkowników do usługi Serwer Proxy aplikacji
- **Przeskoku 2**: serwer Proxy aplikacji usługi łącznika serwera Proxy aplikacji
- **Przeskoku 3**: łącznika serwera Proxy aplikacji do aplikacji docelowej 

### <a name="use-case-1"></a>Przypadek użycia 1

**Scenariusz:** aplikacji znajduje się w sieci organizacji w Stanach Zjednoczonych, z użytkownikami w tym samym regionie. Nie ExpressRoute VPN istnieje lub między centrum danych Azure a siecią firmową.

**Zalecenie:** wzorzec wykonaj 1, wyjaśniono w poprzedniej sekcji. Dla opóźnienia lepsze Rozważ użycie usługi ExpressRoute, w razie potrzeby.

Jest to prosty wzorzec. Zoptymalizować przeskoku 3 przez umieszczenie łącznika obok aplikacji. To także wybór fizycznych, ponieważ łącznik jest zazwyczaj instalowany z wiersza z procesów do aplikacji i centrum danych w celu wykonania operacji KCD.

![Diagram przedstawiający, czy użytkownicy, serwer proxy, łącznika i aplikacji znajdują się w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Przypadek użycia 2

**Scenariusz:** aplikacji znajduje się w sieci organizacji w Stanach Zjednoczonych, globalnie rozłożenia użytkowników. Nie ExpressRoute VPN istnieje lub między centrum danych Azure a siecią firmową.

**Zalecenie:** wzorzec wykonaj 1, wyjaśniono w poprzedniej sekcji. 

Ponownie wspólnego wzorca w celu zoptymalizowania przeskoku 3, umieszcza się łącznika obok aplikacji. Przeskoku 3 nie jest zwykle kosztowne, jeśli jest w tym samym regionie. Jednak przeskoku 1 może być bardziej kosztowne, w zależności od tego, gdzie jest użytkownik, ponieważ użytkowników na całym świecie muszą uzyskać dostęp do wystąpienia serwera Proxy aplikacji w Stanach Zjednoczonych. Warto zauważyć, że żadne rozwiązanie do serwera proxy ma podobnej charakterystyce dotyczące użytkowników rozkłada się globalnie.

![Diagram pokazujący, że użytkownicy są rozkładane globalnie, ale serwer proxy, łącznika i aplikacji znajdują się w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Przypadek użycia 3

**Scenariusz:** aplikacji znajduje się w sieci organizacji w Stanach Zjednoczonych. Istnieje ExpressRoute z publicznej komunikacji równorzędnej platformy Azure i siecią firmową.

**Zalecenie:** wykonaj wzorce 1 i 2 wyjaśniono w poprzedniej sekcji.

Po pierwsze umieść łącznika, jak najbardziej zbliżone do aplikacji. Następnie system automatycznie używa ExpressRoute przeskoku 2. 

Jeśli ExpressRoute link używa publicznej komunikacji równorzędnej, ruch między serwerem proxy a łącznik przepływy za pośrednictwem tego łącza. Przeskok 2 optymalizacji opóźnień.

![Diagram przedstawiający między serwerem proxy i łącznik usługi ExpressRoute](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Przypadek użycia 4

**Scenariusz:** aplikacji znajduje się w sieci organizacji w Stanach Zjednoczonych. Istnieje ExpressRoute z prywatnej komunikacji równorzędnej platformy Azure i siecią firmową.

**Zalecenie:** wzorzec wykonaj 3, wyjaśniono w poprzedniej sekcji.

Łącznik należy umieścić w centrum danych Azure, która jest połączona z siecią firmową za pośrednictwem prywatnej komunikacji równorzędnej ExpressRoute. 

Łącznik można umieścić w centrum danych Azure. Ponieważ łącznik nadal ma procesów w wierszu, aplikacji i centrum danych za pośrednictwem sieci prywatnej, przeskoku 3 pozostaje zoptymalizowane. Ponadto przeskoku 2 jest zoptymalizowany dalej.

![Diagram przedstawiający łącznika w centrum danych Azure i usługi ExpressRoute, łączników i aplikacji](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Przypadek użycia 5

**Scenariusz:** aplikacji znajduje się w sieci organizacji w UE, z wystąpienia serwera Proxy aplikacji i większość użytkowników w Stanach Zjednoczonych.

**Zalecenie:** umieść łącznika obok aplikacji. Ponieważ USA użytkownicy uzyskują dostęp do wystąpienia serwera Proxy aplikacji wykonywanej w tym samym regionie, przeskoku 1 nie jest zbyt drogie. Przeskoku 3 jest zoptymalizowany. Należy rozważyć użycie usługi ExpressRoute w celu zoptymalizowania przeskoku 2. 

![Diagram przedstawiający użytkowników i serwera proxy w Stanach Zjednoczonych, przy użyciu łącznika i aplikacji w UE](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Można także rozważyć użycie jednego innego elementu variant w takiej sytuacji. W przypadku większości użytkowników w organizacji w Stanach Zjednoczonych, a następnie są istnieje prawdopodobieństwo, że sieci obejmuje również USA. Ustaw łącznika w Stanach Zjednoczonych, a w UE za pomocą wiersza dedykowanych wewnętrznej sieci firmowej do aplikacji. Są optymalizowane w ten sposób liczba przeskoków 2 i 3.

![Diagram przedstawiający użytkowników, proxy i łącznika w Stanach Zjednoczonych, aplikacji w UE](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Kolejne kroki

- [Włączanie serwera Proxy aplikacji](application-proxy-enable.md)
- [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
- [Włączanie dostępu warunkowego](application-proxy-integrate-with-sharepoint-server.md)
- [Rozwiązywanie problemów, które masz problem z serwerem Proxy aplikacji](../active-directory-application-proxy-troubleshoot.md)
