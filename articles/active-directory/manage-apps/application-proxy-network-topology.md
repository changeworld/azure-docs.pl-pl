---
title: Zagadnienia dotyczące topologii sieci dla serwera proxy aplikacji usługi Azure AD
description: Obejmuje zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure AD.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: eaceaf1f5e9b6e34ced5db39b61e607fffcb5953
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295132"
---
# <a name="network-topology-considerations-when-using-azure-active-directory-application-proxy"></a>Zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure Active Directory

W tym artykule opisano zagadnienia dotyczące topologii sieci podczas korzystania z serwera proxy aplikacji usługi Azure Active Directory (Azure AD) do zdalnego publikowania aplikacji i uzyskiwania do nich dostępu.

## <a name="traffic-flow"></a>Przepływ ruchu

Gdy aplikacja jest publikowana za pośrednictwem serwera proxy aplikacji usługi Azure AD, ruch od użytkowników do aplikacji przepływa przez trzy połączenia:

1. Użytkownik łączy się z publicznym punktem końcowym usługi azure ad proxy usługi na platformie Azure
1. Usługa proxy aplikacji łączy się ze złączem serwera proxy aplikacji
1. Łącznik serwera proxy aplikacji łączy się z aplikacją docelową

![Diagram przedstawiający przepływ ruchu z aplikacji użytkownika do docelowej](./media/application-proxy-network-topology/application-proxy-three-hops.png)

## <a name="tenant-location-and-application-proxy-service"></a>Lokalizacja dzierżawy i usługa serwera proxy aplikacji

Po zarejestrowaniu się w dzierżawie usługi Azure AD region dzierżawy jest określany przez określony kraj/region. Po włączeniu serwera proxy aplikacji wystąpienia usługi serwera proxy aplikacji dla dzierżawy są wybierane lub tworzone w tym samym regionie co dzierżawa usługi Azure AD lub w najbliższym regionie.

Jeśli na przykład krajem lub regionem dzierżawy usługi Azure AD jest Wielka Brytania, wszystkie łączniki serwera proxy aplikacji używają wystąpień usługi w europejskich centrach danych. When your users access published applications, their traffic goes through the Application Proxy service instances in this location.

## <a name="considerations-for-reducing-latency"></a>Zagadnienia dotyczące zmniejszenia opóźnień

Wszystkie rozwiązania proxy wprowadzają opóźnienia do połączenia sieciowego. Bez względu na to, które rozwiązanie proxy lub VPN wybierzesz jako rozwiązanie dostępu zdalnego, zawsze zawiera zestaw serwerów umożliwiających połączenie wewnątrz sieci firmowej.

Organizacje zazwyczaj obejmują punkty końcowe serwera w swojej sieci obwodowej. Jednak w usłudze Azure AD Application Proxy ruch przepływa przez usługę proxy w chmurze, podczas gdy łączniki znajdują się w sieci firmowej. Nie jest wymagana sieć obwodowa.

Następne sekcje zawierają dodatkowe sugestie, które pomogą Ci jeszcze bardziej zmniejszyć opóźnienia. 

### <a name="connector-placement"></a>Rozmieszczenie łącznika

Serwer proxy aplikacji wybiera lokalizację wystąpień na podstawie lokalizacji dzierżawy. Można jednak zdecydować, gdzie zainstalować łącznik, co daje możliwość zdefiniowania charakterystyki opóźnienia ruchu sieciowego.

Podczas konfigurowania usługi Proxy aplikacji należy zadać następujące pytania:

- Gdzie znajduje się aplikacja?
- Gdzie znajduje się większość użytkowników, którzy uzyskują dostęp do aplikacji?
- Gdzie znajduje się wystąpienie serwera proxy aplikacji?
- Czy masz już skonfigurowane dedykowane połączenie sieciowe z centrami danych platformy Azure, takim jak Azure ExpressRoute lub podobna sieć VPN?

Łącznik musi komunikować się z platformą Azure i aplikacjami (kroki 2 i 3 na diagramie przepływu ruchu), więc umieszczenie łącznika wpływa na opóźnienie tych dwóch połączeń. Oceniając położenie łącznika, należy pamiętać o następujących kwestiach:

- Jeśli chcesz użyć delegowania ograniczonego protokołu Kerberos (KCD) do logowania jednokrotnego, łącznik potrzebuje linii wzroku do centrum danych. Ponadto serwer łącznika musi być przyłączony do domeny.  
- W razie wątpliwości należy zainstalować złącze bliżej aplikacji.

### <a name="general-approach-to-minimize-latency"></a>Ogólne podejście do minimalizowanie opóźnień

Można zminimalizować opóźnienia ruchu end-to-end, optymalizując każde połączenie sieciowe. Każde połączenie można zoptymalizować poprzez:

- Zmniejszenie odległości między dwoma końcami przeskoku.
- Wybór odpowiedniej sieci do przechodzenia. Na przykład przechodzenie przez sieć prywatną, a nie publiczny Internet może być szybsze, ze względu na dedykowane łącza.

Jeśli masz dedykowane łącze VPN lub ExpressRoute między platformą Azure a siecią firmową, możesz chcieć tego użyć.

## <a name="focus-your-optimization-strategy"></a>Skoncentruj swoją strategię optymalizacji

Niewiele można zrobić, aby kontrolować połączenie między użytkownikami a usługą proxy aplikacji. Użytkownicy mogą uzyskiwać dostęp do aplikacji z sieci domowej, kawiarni lub innego kraju/regionu. Zamiast tego można zoptymalizować połączenia z usługi proxy aplikacji do łączników serwera proxy aplikacji do aplikacji. Należy rozważyć włączenie następujących wzorców w środowisku.

### <a name="pattern-1-put-the-connector-close-to-the-application"></a>Wzór 1: Umieść złącze blisko aplikacji

Umieść łącznik blisko aplikacji docelowej w sieci klienta. Ta konfiguracja minimalizuje krok 3 na diagramie topografii, ponieważ łącznik i aplikacja są blisko.

Jeśli łącznik potrzebuje linii wzroku do kontrolera domeny, a następnie ten wzorzec jest korzystne. Większość naszych klientów korzysta z tego wzorca, ponieważ działa dobrze w większości scenariuszy. Ten wzorzec można również połączyć ze wzorcem 2, aby zoptymalizować ruch między usługą a łącznikiem.

### <a name="pattern-2-take-advantage-of-expressroute-with-microsoft-peering"></a>Wzór 2: Skorzystaj z usługi ExpressRoute z komunikacją równorzędową firmy Microsoft

Jeśli masz skonfigurowany program ExpressRoute z komunikacją równorzędnościową firmy Microsoft, możesz użyć szybszego połączenia Usługi ExpressRoute dla ruchu między serwerem proxy aplikacji a łącznikiem. Łącznik jest nadal w sieci, blisko aplikacji.

### <a name="pattern-3-take-advantage-of-expressroute-with-private-peering"></a>Wzór 3: Skorzystaj z usługi ExpressRoute z prywatną komunikacją równorzędnościową

Jeśli masz dedykowaną sieć VPN lub usługę ExpressRoute skonfigurowaną z prywatną komunikacją równorzędną między platformą Azure a siecią firmową, masz inną opcję. W tej konfiguracji sieci wirtualnej na platformie Azure jest zazwyczaj uważany za rozszerzenie sieci firmowej. Dzięki temu można zainstalować łącznik w centrum danych platformy Azure i nadal spełniać wymagania dotyczące małych opóźnień połączenia łącznika z aplikacją.

Opóźnienie nie jest zagrożona, ponieważ ruch jest przepływa zawertne połączenie. Można również uzyskać ulepszone opóźnienie usługi usługi do łącznika serwera proxy aplikacji, ponieważ łącznik jest zainstalowany w centrum danych platformy Azure w pobliżu lokalizacji dzierżawy usługi Azure AD.

![Diagram przedstawiający łącznik zainstalowany w centrum danych platformy Azure](./media/application-proxy-network-topology/application-proxy-expressroute-private.png)

### <a name="other-approaches"></a>Inne podejścia

Chociaż głównym tematem tego artykułu jest umieszczenie łącznika, można również zmienić położenie aplikacji, aby uzyskać lepsze właściwości opóźnienia.

Coraz częściej organizacje przenoszą swoje sieci do środowisk hostowanych. Dzięki temu mogą umieszczać swoje aplikacje w środowisku hostowanym, które jest również częścią ich sieci firmowej i nadal znajduje się w domenie. W takim przypadku wzorce omówione w poprzednich sekcjach można zastosować do nowej lokalizacji aplikacji. Jeśli rozważasz tę opcję, zobacz [Usługi domenowe usługi Azure AD](../../active-directory-domain-services/overview.md).

Ponadto należy rozważyć zorganizowanie łączników przy użyciu [grup łączników](application-proxy-connector-groups.md) do kierowania aplikacji, które znajdują się w różnych lokalizacjach i sieciach.

## <a name="common-use-cases"></a>Typowe przypadki użycia

W tej sekcji przechodzimy przez kilka typowych scenariuszy. Załóżmy, że dzierżawa usługi Azure AD (i w związku z tym punkt końcowy usługi proxy) znajduje się w Stanach Zjednoczonych (USA). Zagadnienia omówione w tych przypadkach użycia mają również zastosowanie do innych regionów na całym świecie.

W tych scenariuszach nazywamy każde połączenie "przeskokiem" i numerujemy je w celu ułatwienia dyskusji:

- **Hop 1**: Użytkownik do usługi proxy aplikacji
- **Hop 2**: Usługa serwera proxy aplikacji do łącznika serwera proxy aplikacji
- **Hop 3**: Łącznik serwera proxy aplikacji do aplikacji docelowej 

### <a name="use-case-1"></a>Przypadek użycia 1

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych, z użytkownikami w tym samym regionie. Między centrum danych platformy Azure a siecią firmową nie istnieje usługa ExpressRoute ani VPN.

**Zalecenie:** Postępuj zgodnie z wzorem 1, wyjaśnionym w poprzedniej sekcji. Aby zwiększyć opóźnienie, należy rozważyć użycie usługi ExpressRoute, jeśli to konieczne.

Jest to prosty wzór. Optymalizujesz przeskok 3, umieszczając łącznik w pobliżu aplikacji. Jest to również naturalny wybór, ponieważ łącznik zazwyczaj jest zainstalowany z linii wzroku do aplikacji i centrum danych do wykonywania operacji KCD.

![Diagram przedstawiający użytkowników, serwer proxy, łącznik i aplikację znajdują się w Stanach Zjednoczonych](./media/application-proxy-network-topology/application-proxy-pattern1.png)

### <a name="use-case-2"></a>Przypadek użycia 2

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w STANACH Zjednoczonych, a użytkownicy są rozpowszechniani na całym świecie. Między centrum danych platformy Azure a siecią firmową nie istnieje usługa ExpressRoute ani VPN.

**Zalecenie:** Postępuj zgodnie z wzorem 1, wyjaśnionym w poprzedniej sekcji.

Ponownie typowy wzorzec jest optymalizacja przeskoku 3, gdzie można umieścić łącznika w pobliżu aplikacji. Hop 3 nie jest zazwyczaj drogie, jeśli wszystko znajduje się w tym samym regionie. Jednak przeskok 1 może być droższe w zależności od tego, gdzie jest użytkownik, ponieważ użytkownicy na całym świecie musi uzyskać dostęp do wystąpienia serwera proxy aplikacji w USA. Warto zauważyć, że każde rozwiązanie proxy ma podobne cechy dotyczące użytkowników rozłożonych na całym świecie.

![Użytkownicy są rozpowszechniane na całym świecie, ale wszystko inne jest w USA](./media/application-proxy-network-topology/application-proxy-pattern2.png)

### <a name="use-case-3"></a>Przypadek użycia 3

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych. Usługa ExpressRoute z komunikacją równorzędnościową firmy Microsoft istnieje między platformą Azure a siecią firmową.

**Zalecenie:** Postępuj zgodnie z wzorami 1 i 2, wyjaśnione w poprzedniej sekcji.

Najpierw umieść łącznik jak najbliżej aplikacji. Następnie system automatycznie używa usługi ExpressRoute dla przeskoku 2.

Jeśli łącze Usługi ExpressRoute korzysta z komunikacji równorzędnej firmy Microsoft, ruch między serwerem proxy a łącznikiem przepływa przez to łącze. Hop 2 ma zoptymalizowane opóźnienie.

![Diagram przedstawiający program ExpressRoute między serwerem proxy a łącznikiem](./media/application-proxy-network-topology/application-proxy-pattern3.png)

### <a name="use-case-4"></a>Przypadek użycia 4

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Stanach Zjednoczonych. Usługa ExpressRoute z prywatną komunikacją równorzędności istnieje między platformą Azure a siecią firmową.

**Zalecenie:** Postępuj zgodnie z wzorem 3, wyjaśnionym w poprzedniej sekcji.

Umieść łącznik w centrum danych platformy Azure, który jest połączony z siecią firmową za pośrednictwem prywatnej komunikacji równorzędnej usługi ExpressRoute.

Łącznik można umieścić w centrum danych platformy Azure. Ponieważ łącznik nadal ma linię wzroku do aplikacji i centrum danych za pośrednictwem sieci prywatnej, przeskok 3 pozostaje zoptymalizowany. Ponadto hop 2 jest dalej optymalizowany.

![Łącznik w centrum danych platformy Azure, usługa ExpressRoute między łącznikiem a aplikacją](./media/application-proxy-network-topology/application-proxy-pattern4.png)

### <a name="use-case-5"></a>Przypadek użycia 5

**Scenariusz:** Aplikacja znajduje się w sieci organizacji w Europie, z wystąpieniem serwera proxy aplikacji i większością użytkowników w STANACH Zjednoczonych.

**Zalecenie:** Umieść łącznik w pobliżu aplikacji. Ponieważ użytkownicy z USA uzyskują dostęp do wystąpienia serwera proxy aplikacji, które znajduje się w tym samym regionie, przeskok 1 nie jest zbyt kosztowny. Hop 3 jest zoptymalizowany. Należy rozważyć użycie usługi ExpressRoute do optymalizacji przeskoku 2.

![Diagram pokazuje użytkowników i serwer proxy w USA, łącznik i aplikacji w Europie](./media/application-proxy-network-topology/application-proxy-pattern5b.png)

Można również rozważyć użycie innego wariantu w tej sytuacji. Jeśli większość użytkowników w organizacji jest w Stanach Zjednoczonych, istnieje szansa, że twoja sieć zostanie rozszerzona również na Stany Zjednoczone. Umieść łącznik w Stanach Zjednoczonych i użyj dedykowanej wewnętrznej linii sieci firmowej do aplikacji w Europie. W ten sposób przeskoki 2 i 3 są zoptymalizowane.

![Diagram przedstawia użytkowników, serwer proxy i łącznik w USA, aplikację w Europie](./media/application-proxy-network-topology/application-proxy-pattern5c.png)

## <a name="next-steps"></a>Następne kroki

- [Włącz serwer proxy aplikacji](application-proxy-add-on-premises-application.md)
- [Włączanie logowania jednokrotnego](application-proxy-configure-single-sign-on-with-kcd.md)
- [Włączanie dostępu warunkowego](application-proxy-integrate-with-sharepoint-server.md)
- [Rozwiązywanie problemów z serwerem proxy aplikacji](application-proxy-troubleshoot.md)
