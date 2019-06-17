---
title: Jak działa usługa Azure Traffic Manager | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże Ci zrozumieć, jak usługi Traffic Manager kieruje ruch, aby uzyskać wysoką wydajność i dostępność aplikacji sieci web
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/05/2019
ms.author: allensu
ms.openlocfilehash: a74af002dfdad5df9640be4b5fdd7f657b183bd4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071185"
---
# <a name="how-traffic-manager-works"></a>Jak działa usługa Traffic Manager

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi usługi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Usługa Traffic Manager zapewnia dwie kluczowe korzyści:

- Dystrybucja ruchu sieciowym pogrupowane według jedną z kilku [metody routingu ruchu](traffic-manager-routing-methods.md)
- [Ciągłe monitorowanie kondycji punktu końcowego](traffic-manager-monitoring.md) i automatycznej pracy awaryjnej, gdy punkty końcowe się nie powieść.

Gdy klient próbuje nawiązać połączenie z usługą, jego rozpoznać nazwy DNS usługi na adres IP. Klient następnie łączy się z tego adresu IP, aby uzyskać dostęp do usługi.

**Najważniejsze punktem jest zrozumienie, usługa Traffic Manager działa na poziomie usługi DNS.**  Przy użyciu DNS usługi Traffic Manager kieruje klientów do punktów końcowych określonej usługi na podstawie reguł metody routingu ruchu. Klienci łączą się wybrany punkt końcowy z **bezpośrednio**. Usługa Traffic Manager nie jest serwer proxy lub bramą. Usługa Traffic Manager nie widzi ruch przekazywanie między klientem a usługą.

## <a name="traffic-manager-example"></a>Przykład usługi Traffic Manager

Firmy Contoso Corp opracowali nowego portalu dla partnerów. Adres URL dla tego portalu jest https://partners.contoso.com/login.aspx. Aplikacja jest obsługiwana w trzech regionach platformy Azure. Aby zwiększyć dostępność i zmaksymalizować wydajność globalnego, używają usługi Traffic Manager do rozproszenia ruchu klientów do najbliższego dostępnego punktu końcowego.

Aby uzyskać tę konfigurację, ich wykonaj następujące czynności:

1. Wdrażanie trzech wystąpień usługi. Nazwy DNS wdrożenia to "contoso-us.cloudapp .net", "contoso-eu.cloudapp .net" i ".net asia.cloudapp firmy contoso".
1. Utwórz profil usługi Traffic Manager o nazwie "contoso.trafficmanager.net" i skonfiguruj ją w trzech punktów końcowych przy użyciu metody routingu ruchu "Wydajność".
1. Konfigurowanie nazwy domeny znaczącej "partners.contoso.com", aby wskazywał "contoso.trafficmanager.net", przy użyciu rekordu CNAME systemu DNS.

![Konfiguracja DNS usługi Traffic Manager][1]

> [!NOTE]
> Korzystając z domeną znaczącą za pomocą usługi Azure Traffic Manager, należy użyć rekordu CNAME, aby wskazywała nazwę domeny znaczącej nazwy domeny usługi Traffic Manager. Standardy systemu DNS nie pozwalają utworzyć rekord CNAME w "wierzchołku" (lub głównym) domeny. Dlatego nie można utworzyć rekord CNAME dla domeny "contoso.com" (nazywane czasem "naked" domeny). Można utworzyć tylko rekord CNAME dla domeny w strefie "contoso.com", takich jak "www.contoso.com". Aby obejść to ograniczenie, zalecamy hosting domeny DNS na [system DNS Azure](../dns/dns-overview.md) i przy użyciu [rekordów aliasów](../dns/tutorial-alias-tm.md) wskaż profilowi usługi traffic manager. Alternatywnie można użyć prostego przekierowania HTTP na bezpośrednie żądania dla domeny "contoso.com" alternatywnej nazwy, takie jak "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Jak klienci łączą, za pomocą usługi Traffic Manager

Kontynuując poprzedni przykład, gdy klient żąda strony https://partners.contoso.com/login.aspx, klient wykonuje następujące kroki, aby rozpoznać nazwy DNS i nawiązania połączenia:

![Ustanawianie połączenia przy użyciu usługi Traffic Manager][2]

1. Klient wysyła zapytanie DNS do jego skonfigurowanych rekursywnych usług DNS można rozpoznać nazwy "partners.contoso.com". Cyklicznej usługi DNS, czasami nazywane usługą "DNS lokalnym" nie obsługuje bezpośrednio domen DNS. Przeciwnie klient off-loads pracy kontaktowania się z pomocą różne autorytatywne usługi DNS w Internecie, potrzebne do rozpoznawania nazw DNS.
2. Można rozpoznać nazwy DNS, rekursywnych usług DNS znajdzie serwery nazw dla domeny "contoso.com". Kontaktuje się następnie tych serwerów nazw, aby zażądać rekordu DNS "partners.contoso.com". Serwery DNS contoso.com zwraca rekord CNAME, który wskazuje na contoso.trafficmanager.net.
3. Następnie rekursywnych usług DNS znajdzie serwery nazw dla domeny "trafficmanager.net", które są udostępniane przez usługę Azure Traffic Manager. Wysyła następnie żądanie dotyczące rekordu DNS "contoso.trafficmanager.net" na tych serwerach DNS.
4. Serwery nazw usługi Traffic Manager otrzymają żądanie. Wybiera punkt końcowy na podstawie:

    - Skonfigurowany stan każdego punktu końcowego (wyłączone punkty końcowe nie są zwracane)
    - Bieżącą kondycję każdego punktu końcowego, zgodnie z ustaleniami kontrole kondycji usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md).
    - Wybranej metody routingu ruchu. Aby uzyskać więcej informacji, zobacz [metody routingu w usłudze Traffic Manager](traffic-manager-routing-methods.md).

5. Wybrany punkt końcowy jest zwracana jako innego rekordu CNAME systemu DNS. W tym przypadku Daj nam Załóżmy, że zwracany jest us.cloudapp.net firmy contoso.
6. Następnie rekursywnych usług DNS znajdzie serwery nazw dla domeny "cloudapp.net". Kontaktuje się te serwery nazw na żądanie dla .net contoso us.cloudapp rekordu DNS. Jest zwracany rekord DNS "A", zawierający adres IP punktu końcowego usługi Stanach Zjednoczonych.
7. Rekursywnych usług DNS konsoliduje wyniki i zwraca pojedynczą odpowiedź DNS do klienta.
8. Klient otrzymuje wyniki DNS i nawiązanie połączenia z danego adresu IP. Klient łączy się z punktem końcowym usługi aplikacji bezpośrednio, nie za pomocą usługi Traffic Manager. Ponieważ jest punkt końcowy HTTPS, wykonuje niezbędne Uzgadnianie SSL/TLS klienta, a następnie tworzy żądanie HTTP GET "/ login.aspx" strony.

Rekursywnych usług DNS buforuje odbieranych odpowiedzi DNS. Program rozpoznawania nazw DNS na urządzeniu klienckim buforuje wynik. Pamięć podręczna umożliwia kolejnych zapytań DNS na udzielenie odpowiedzi szybciej przy użyciu danych z pamięci podręcznej, zamiast kwerendy innych serwerów nazw. Czas trwania pamięci podręcznej jest określany przez właściwość "time-to-live" (TTL) w poszczególnych rekordów DNS. Krótszej wartości spowodować szybsze wygaśnięcia pamięci podręcznej i dlatego więcej połączeń do serwerów nazw usługi Traffic Manager. Wartości dłuższe oznacza, że może to trwać dłużej do kierowania ruchu od punktu końcowego nie powiodło się. Usługa Traffic Manager można skonfigurować czas wygaśnięcia używana w odpowiedzi DNS usługi Traffic Manager jako jedyne 0 sekund oraz możliwie jak 2 147 483 647 sekund (zgodne z maksymalną wielkość zakresu [standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), dzięki czemu można wybrać wartość najlepiej salda wymagań aplikacji.


## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o usłudze Traffic Manager [punktu końcowego monitorowaniu i automatycznemu trybu failover](traffic-manager-monitoring.md).

Dowiedz się więcej o usłudze Traffic Manager [metody routingu ruchu](traffic-manager-routing-methods.md).

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

