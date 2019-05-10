---
title: Zagadnienia dotyczące topologii sieci, korzystając z serwera Proxy usługi Azure Active Directory Application | Dokumentacja firmy Microsoft
description: Omówiono zagadnienia dotyczące topologii sieci, korzystając z serwera Proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2017
ms.author: celested
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5b87e452b0c79ae9cdc1d7f9f391a611dceda2f
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/07/2019
ms.locfileid: "65231516"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Zagadnienia dotyczące topologii sieci przy użyciu serwera Proxy usługi Azure Active Directory Application

W tym artykule opisano zagadnienia dotyczące topologii sieci, podczas publikowania i zdalny dostęp do aplikacji przy użyciu serwera Proxy aplikacji usługi Azure Active Directory (Azure AD).

## <a name="traffic-flow"></a>Przepływ ruchu

Po opublikowaniu aplikacji za pośrednictwem serwera Proxy aplikacji usługi Azure AD ruchu użytkowników do aplikacji przechodzi przez trzy połączenia:

1. Gdy użytkownik łączy się serwera Proxy aplikacji usługi Azure AD publiczny punkt końcowy usługi na platformie Azure
2. Usługa serwera Proxy aplikacji nawiązanie połączenia z łącznika serwera Proxy aplikacji
3. Łącznik serwera Proxy aplikacji łączy do aplikacji docelowej

![Diagram przedstawiający przepływ ruchu z użytkownika do aplikacji docelowej](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Lokalizacja dzierżawy i usługę serwera Proxy aplikacji

Po zarejestrowaniu się dla dzierżawy usługi Azure AD z regionem danej dzierżawy zależy od kraju/regionu, które określisz. Po włączeniu serwera Proxy aplikacji, wystąpień usługi Serwer Proxy aplikacji dla Twojej dzierżawy są wybrane lub utworzone w tym samym regionie, co dzierżawy usługi Azure AD lub najbliższego regionu do niego.

Na przykład w przypadku dzierżawy usługi Azure AD kraj lub region Zjednoczone Królestwo, wszystkich łączników serwera Proxy aplikacji użyj wystąpień usług w centrach danych Europa. Podczas publikowania aplikacji dostępu użytkownikom ich ruch jest przesyłany za pośrednictwem wystąpień usługi Serwer Proxy aplikacji w tej lokalizacji.

## <a name="considerations-for-reducing-latency"></a>Zagadnienia dotyczące zredukowanie opóźnień

Wszystkie rozwiązania proxy wprowadzić opóźnienie do połączenia sieciowego. Niezależnie od tego, w którym serwer proxy lub rozwiązanie sieci VPN wybierz jako rozwiązania dostępu zdalnego go zawsze zawiera zestaw serwerów włączenie połączenia w sieci firmowej.

Organizacje zazwyczaj obejmują punkty końcowe serwera w sieci obwodowej. Za pomocą serwera Proxy aplikacji usługi Azure AD natomiast ruch odbywa się za pośrednictwem usługi Serwer proxy w chmurze podczas łączniki znajdują się w sieci firmowej. Brak sieci obwodowej jest wymagana.

Następne sekcje zawierają dodatkowe sugestie pozwala zmniejszyć opóźnienie jeszcze bardziej. 

### <a name="connector-placement"></a>Umieszczanie łącznika

Serwer Proxy aplikacji wybiera lokalizację wystąpień dla Ciebie na podstawie lokalizacji dzierżawy. Jednak można korzystać podjęcie decyzji zainstalować łącznik, dając do zdefiniowania parametrów opóźnienia w ruchu sieciowym.

Podczas konfigurowania usługi Serwer Proxy aplikacji, należy odpowiedzieć na następujące pytania:

* Gdzie znajduje się aplikacja
* Gdzie znajdują się większość użytkowników, którzy dostępu do aplikacji
* Gdzie znajduje się wystąpienie serwera Proxy aplikacji
* Czy masz już połączenie dedykowana sieć centrów danych platformy Azure, konfigurowanie, takich jak usługi Azure ExpressRoute lub podobne sieci VPN?

Łącznik musi komunikować się z platformy Azure i aplikacji (kroki 2 i 3 w diagram przepływu ruchu), więc umieszczanie łącznika ma wpływ na opóźnienie te dwa połączenia. Podczas szacowania położenia łącznika, należy pamiętać następujące kwestie:

* Jeśli chcesz użyć ograniczonego delegowania protokołu Kerberos (KCD) dla logowania jednokrotnego, łącznik musi linii wzroku do centrum danych. Ponadto serwer łącznika musi być przyłączony do domeny.  
* W razie wątpliwości, należy zainstalować łącznik bliżej do aplikacji.

### <a name="general-approach-to-minimize-latency"></a>Ogólne podejście, aby zminimalizować czas oczekiwania

Aby zminimalizować opóźnienie ruchu end-to-end, optymalizowanie każdego połączenia sieciowego. Każde połączenie można zoptymalizować poprzez:

* Zmniejszenie odległość między po obu stronach przeskoku.
* Wybieranie z odpowiednią siecią przechodzenia. Na przykład przechodzenie z sieci prywatnej, a nie z publicznego Internetu może być szybsze, ze względu na specjalne linki.

Jeśli masz dedykowanych VPN lub usługi ExpressRoute łącza między platformą Azure i siecią firmową, można używać go.

## <a name="focus-your-optimization-strategy"></a>Skoncentruj się strategii optymalizacji

Jest niewiele, które można zrobić, aby kontrolować połączenia między użytkownikami a usługa serwera Proxy aplikacji. Użytkownicy mogą uzyskiwać dostęp do aplikacji z siecią domową, kawiarni lub innego kraju/regionu. Zamiast tego można zoptymalizować połączeń z serwera Proxy aplikacji usługi do łączników serwera Proxy aplikacji do aplikacji. Należy rozważyć włączenie następujących wzorów w danym środowisku.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Wzorzec 1: Umieszczanie łącznika blisko aplikacji

W sieci klienta, należy umieścić łącznika blisko aplikacji docelowej. Ta konfiguracja minimalizuje kroku 3 na diagramie topografii, ponieważ Zamknij łączników i aplikacji. 

Jeśli Twój łącznik linii wzroku do kontrolera domeny, ten wzorzec jest korzystne. Większość naszych klientów Użyj tego wzorca, ponieważ działa dobrze w przypadku większości scenariuszy. Ten wzorzec można również łączyć z wzorcem 2, aby zoptymalizować ruch między usługą i łącznik.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Wzorzec 2: Skorzystaj z zalet usługi ExpressRoute za pomocą komunikacji równorzędnej firmy Microsoft

W przypadku usługi ExpressRoute skonfigurować za pomocą komunikacji równorzędnej firmy Microsoft umożliwia szybsze połączenia ExpressRoute dla ruchu między serwera Proxy aplikacji i łącznik. Łącznik jest nadal w sieci, blisko aplikacji.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Wzorzec 3: Skorzystaj z zalet usługi ExpressRoute za pomocą prywatnej komunikacji równorzędnej

Jeśli korzystasz z dedykowanego połączenia VPN lub usługi ExpressRoute skonfigurować za pomocą prywatnej komunikacji równorzędnej między platformą Azure i siecią firmową, masz inną opcję. W tej konfiguracji sieci wirtualnej na platformie Azure uznaje się zwykle jako rozszerzenia sieci firmowej. Można więc zainstalować łącznik w centrum danych platformy Azure i nadal spełniają wymagania małymi opóźnieniami połączeń aplikacji łącznika.

Opóźnienie nie są zagrożone, ponieważ ruch będą przepływać przez dedykowane połączenie. Możesz także uzyskać udoskonalone opóźnienie łącznika usługi serwera Proxy aplikacji, ponieważ łącznik jest instalowana w centrum danych platformy Azure blisko swojej lokalizacji dzierżawy usługi Azure AD.

![Diagram przedstawiający łącznika zainstalowane w ramach centrum danych platformy Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Inne metody

Chociaż ten artykuł koncentruje się umieszczania łącznika, można również zmienić umieszczania aplikacji w celu uzyskania lepsze charakterystyki opóźnienia.

Coraz częściej organizacje są przenoszone ich sieci w środowiskach hostowanych. Umożliwia to umieścić swoje aplikacje w środowisku hostowanej, która jest również częścią sieci firmowej, a w dalszym ciągu w domenie. W tym przypadku wzorców omówione w poprzednich sekcjach można zastosować do nowej lokalizacji aplikacji. Jeśli rozważasz tej opcji, zobacz [usług domenowych Azure AD](../../active-directory-domain-services/active-directory-ds-overview.md).

Ponadto należy wziąć pod uwagę organizowanie za pomocą łączników [grupy łączników](application-proxy-connector-groups.md) do aplikacji docelowej, które znajdują się w różnych lokalizacjach i sieci. 

## <a name="common-use-cases"></a>Typowe przypadki użycia

W tej sekcji części omówimy kilka typowych scenariuszy. Przyjęto założenie, że dzierżawa usługi Azure AD (i w związku z tym punktu końcowego usługi serwera proxy) znajduje się w Stanach Zjednoczonych (US). Zagadnienia omówione w tych przypadkach mają zastosowanie również do innych regionów na całym świecie używają.

Dla tych scenariuszy możemy wywołać każde połączenie "przeskok" i liczbę je łatwiej omówienie:

- **Przeskoku 1**: Użytkownika dla usługi serwera Proxy aplikacji
- **Przeskoku 2**: Usługa serwera Proxy aplikacji do łącznika serwera Proxy aplikacji
- **Przeskoku 3**: Łącznik serwera Proxy aplikacji do aplikacji docelowej 

### <a name="use-case-1"></a>Przypadek użycia 1

**Scenariusz:** Aplikacja jest w sieci organizacji w Stanach Zjednoczonych, z użytkownikami, w tym samym regionie. Nie usługi ExpressRoute lub sieci VPN istnieje między centrum danych platformy Azure i siecią firmową.

**Zalecenie:** Postępuj zgodnie z wzorcem 1, opisane w poprzedniej sekcji. Ulepszone opóźnienia należy wziąć pod uwagę przy użyciu usługi ExpressRoute, jeśli to konieczne.

Jest to prosty wzorzec. Optymalizuj się przeskoku 3, umieszczając łącznika obok aplikacji. Jest to również naturalny wybór, ponieważ łącznik jest zazwyczaj instalowany z linii wzroku do aplikacji i centrum danych do wykonywania operacji ograniczonego delegowania protokołu Kerberos.

![Diagram przedstawiający, że użytkownicy, serwer proxy, łącznika i aplikacji są w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Przypadek użycia 2

**Scenariusz:** Aplikacja jest w sieci organizacji w Stanach Zjednoczonych, użytkownikom rozłożyć globalnie. Nie usługi ExpressRoute lub sieci VPN istnieje między centrum danych platformy Azure i siecią firmową.

**Zalecenie:** Postępuj zgodnie z wzorcem 1, opisane w poprzedniej sekcji. 

Ponownie wspólny wzorzec do optymalizacji przeskoku 3, umieszcza się łącznika obok aplikacji. Przeskoku 3 nie jest zazwyczaj kosztowne, a jeśli w tym samym regionie. Jednak przeskok 1 może być bardziej kosztowne w zależności od tego, gdzie użytkownik jest, ponieważ użytkownicy na całym świecie muszą uzyskiwać dostęp do wystąpienia serwera Proxy aplikacji w Stanach Zjednoczonych. Warto zauważyć, że żadne rozwiązanie do serwera proxy ma podobne charakterystyki dotyczące użytkowników, które są rozmieszczone globalnie.

![Diagram przedstawiający, że użytkownicy są dystrybuowane globalnie, ale serwer proxy, łączników i aplikacji znajdują się w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Przypadek użycia 3

**Scenariusz:** Aplikacja jest w sieci organizacji w Stanach Zjednoczonych. Usługi ExpressRoute za pomocą komunikacji równorzędnej firmy Microsoft istnieje między platformą Azure i siecią firmową.

**Zalecenie:** Postępuj zgodnie z wzorców 1 i 2, opisane w poprzedniej sekcji.

Po pierwsze umieść łącznika możliwie blisko aplikacji. Następnie system automatycznie korzysta z usługi ExpressRoute dla przeskoku 2. 

Jeśli link usługi ExpressRoute używa komunikacji równorzędnej firmy Microsoft, ruch między serwera proxy i łącznik przepływy za pośrednictwem tego łącza. Przeskok 2 ma zoptymalizowane pod kątem opóźnień.

![Diagram przedstawiający usługi ExpressRoute między serwera proxy i łącznik](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Przypadek użycia 4

**Scenariusz:** Aplikacja jest w sieci organizacji w Stanach Zjednoczonych. Istnieje usługi ExpressRoute za pomocą prywatnej komunikacji równorzędnej między platformą Azure i siecią firmową.

**Zalecenie:** Postępuj zgodnie z wzorcem 3, opisane w poprzedniej sekcji.

Łącznik należy umieścić w centrum danych platformy Azure, która jest połączona z siecią firmową za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute. 

Łącznik można umieścić w centrum danych platformy Azure. Ponieważ łącznik nadal ma linii wzroku do aplikacji i centrum danych za pośrednictwem sieci prywatnej, przeskoku 3 pozostaje zoptymalizowane. Ponadto przeskoku 2 jest dodatkowo zoptymalizowany.

![Diagram przedstawiający łącznika w centrum danych platformy Azure i usługa ExpressRoute między łączników i aplikacji](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Przypadek użycia 5

**Scenariusz:** Aplikacja jest w sieci organizacji w Unii Europejskiej, z wystąpieniem serwera Proxy aplikacji, a większość użytkowników w Stanach Zjednoczonych.

**Zalecenie:** Umieść łącznika obok aplikacji. Ponieważ użytkownicy w Stanach Zjednoczonych, uzyskujesz dostęp do wystąpienia serwera Proxy aplikacji, który ma miejsce w tym samym regionie, przeskok 1 nie jest za drogi. Przeskoku 3 jest zoptymalizowany. Należy wziąć pod uwagę przy użyciu usługi ExpressRoute w celu zoptymalizowania przeskoku 2. 

![Diagram przedstawiający użytkowników i serwera proxy w Stanach Zjednoczonych, przy użyciu łącznika i aplikacji na terenie UE](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Można również rozważyć użycie jednego innych wariant w takiej sytuacji. W przypadku większości użytkowników w organizacji w Stanach Zjednoczonych, a następnie jest szansa, że sieci rozszerza także Stanów Zjednoczonych. Umieść łącznika w Stanach Zjednoczonych, a za pomocą wiersza dedykowanych wewnętrznej sieci firmowej do aplikacji na terenie UE. Przeskoki w ten sposób 2 i 3 są zoptymalizowane.

![Diagram przedstawiający użytkowników, serwer proxy i łącznika w aplikacji na terenie UE w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Kolejne kroki

- [Włącz serwer Proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
- [Włączanie dostępu warunkowego](application-proxy-integrate-with-sharepoint-server.md)
- [Rozwiązywanie problemów z serwerem Proxy aplikacji](application-proxy-troubleshoot.md)
