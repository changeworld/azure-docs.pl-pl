---
title: Co to jest Traffic Manager | Dokumentacja firmy Microsoft
description: Ten artykuł pomoże poznać Menedżera ruchu jest i czy jest wybór routingu ruchu prawo aplikacji
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: 75d5ff9a-f4b9-4b05-af32-700e7bdfea5a
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: kumud
ms.openlocfilehash: 50d7f14d0d4234ee98d8a46e903b5f916cb02fab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23877055"
---
# <a name="overview-of-traffic-manager"></a>Omówienie usługi Traffic Manager

Microsoft Azure Traffic Manager pozwala kontrolować dystrybucję ruchu użytkowników dla punktów końcowych usług w różnych centrach danych. Punkty końcowe usługi obsługiwane przez usługę Traffic Manager obejmują maszynach wirtualnych platformy Azure, aplikacji sieci Web i usług w chmurze. Usługi Traffic Manager można również używać z zewnętrznymi punktami końcowymi poza platformą Azure.

Menedżer ruchu używa systemu nazw domen (DNS) do klienta żądania kierowane do najbardziej odpowiedniego punktu końcowego na podstawie metody routingu ruchu i kondycji punktów końcowych. Menedżer ruchu udostępnia szereg [metody routingu ruchu](traffic-manager-routing-methods.md) i [punktu końcowego opcje monitorowania](traffic-manager-monitoring.md) do własnych potrzeb różnych aplikacji i modele automatycznej pracy awaryjnej. Menedżer ruchu jest odporność na uszkodzenia, łącznie z awarią całej region platformy Azure.

## <a name="traffic-manager-benefits"></a>Zalety usługi Traffic Manager

Menedżer ruchu może ułatwić:

* **Zwiększanie dostępności ważnych aplikacji**

    Menedżer ruchu zapewnia wysoką dostępność dla aplikacji dla monitorowania punktów końcowych i podając automatycznej pracy awaryjnej, gdy punkt końcowy przestanie działać.

* **Zwiększyć czas odpowiedzi dla aplikacji wysokiej wydajności**

    Azure umożliwia uruchamianie usługi w chmurze lub witryny sieci Web w centrach danych na całym świecie. Menedżer ruchu skraca czas odpowiedzi aplikacji, kierując ruchu do punktu końcowego najniższe opóźnienia sieci dla klienta.

* **Przeprowadź obsługę usługi bez przestoju**

    Można wykonać operacji zaplanowanej konserwacji na aplikacje bez przestoju. Menedżer ruchu kieruje ruch do alternatywnego punkty końcowe, w trakcie konserwacji.

* **Łączenie lokalnych i aplikacji działających w chmurze**

    Traffic Manager obsługuje zewnętrzne, innych niż Azure punkty końcowe umożliwiające ma być używany z hybrydowego w chmurze i lokalnych wdrożeń, w tym "serii chmurą," "migracji do chmury," i "trybu failover z chmurą" scenariuszy.

* **Dystrybuuj ruch we wdrożeniach dużych, złożonych**

    Przy użyciu [zagnieżdżonych profilów usługi Traffic Manager](traffic-manager-nested-profiles.md), można utworzyć reguły zaawansowane i elastyczne do obsługi na potrzeby wdrożeń większych i bardziej skomplikowanych można łączyć metody routingu ruchu.

## <a name="how-traffic-manager-works"></a>Jak działa usługa Traffic Manager

Usługi Azure Traffic Manager pozwala kontrolować dystrybucję ruchu między punktami końcowymi aplikacji. Punkt końcowy jest internetowy usługi hostowanej wewnątrz lub na zewnątrz Azure.

Menedżer ruchu zapewnia dwie kluczowe korzyści:

1. Rozkład ruchu zgodnie z jedną z kilku [metody routingu ruchu](traffic-manager-routing-methods.md)
2. [Ciągłego monitorowania kondycji punktu końcowego](traffic-manager-monitoring.md) i automatycznej pracy awaryjnej, gdy punkty końcowe nie powiedzie się.

Gdy klient próbuje połączyć się z usługą, jego rozpoznać nazwę DNS usługi na adres IP. Klient następnie łączy się z tego adresu IP do uzyskania dostępu do usługi.

**Zrozumienie najważniejszych punkt jest czy Traffic Manager działa na poziomie DNS.**  Menedżer ruchu używa DNS do kierować klientów do punktów końcowych określonej usługi na podstawie reguł metody routingu ruchu. Klienci nawiązują połączenie z wybranego punktu końcowego **bezpośrednio**. Menedżer ruchu nie jest serwer proxy lub bramy. Menedżer ruchu nie widzi ruch przekazywanie między klientem a usługą.

### <a name="traffic-manager-example"></a>Przykład Menedżera ruchu

Firmy Contoso Corp zostały opracowane nowego portalu dla partnerów. Adres URL dla tego portalu jest https://partners.contoso.com/login.aspx. Aplikacja znajduje się w trzech regionów platformy Azure. Aby zwiększyć dostępność i zmaksymalizować wydajność globalnych, używają Menedżera ruchu do rozproszenia ruchu klientów do najbliższej dostępnej punktu końcowego.

Aby uzyskać tę konfigurację, ich wykonaj następujące kroki:

1. Wdrażanie trzech wystąpień usługi. Nazwy DNS te wdrożenia są ".net us.cloudapp firmy contoso", "contoso-eu.cloudapp .net" i "contoso-asia.cloudapp .net".
2. Utwórz profil usługi Traffic Manager o nazwie "contoso.trafficmanager.net" i skonfiguruj go przy użyciu metody routingu ruchu "Performance" obrębie trzech punktów końcowych.
* Konfigurowanie nazwy domeny niestandardowych "partners.contoso.com", aby wskazywał "contoso.trafficmanager.net", przy użyciu rekordu CNAME systemu DNS.

![Konfiguracja DNS Menedżera ruchu][1]

> [!NOTE]
> Podczas korzystania z usługi Azure Traffic Manager domen niestandardowych, należy użyć rekord CNAME, aby wskazywała nazwę domeny niestandardowych nazwy domeny usługi Traffic Manager. Standardy usługi DNS nie pozwalają utworzyć rekord CNAME w "wierzchołku" (lub głównego) do domeny. W związku z tym nie można utworzyć rekord CNAME dla domeny "contoso.com" (nazywane czasem "naked" domeny). Można utworzyć tylko rekord CNAME dla domeny, w obszarze "contoso.com", takie jak "www.contoso.com". Aby obejść to ograniczenie, zaleca się przy użyciu prostego przekierowanie bezpośrednie żądania HTTP dla domeny "contoso.com" do alternatywnej nazwy, takie jak "www.contoso.com".

### <a name="how-clients-connect-using-traffic-manager"></a>Jak klienci łączą przy użyciu Menedżera ruchu

Kontynuowanie z poprzedniego przykładu, gdy klient żąda https://partners.contoso.com/login.aspx strony, klient wykonuje następujące czynności, aby rozpoznać nazwy DNS i nawiązania połączenia:

![Ustanawianie połączenia za pomocą Menedżera ruchu][2]

1. Klient wysyła zapytanie DNS do jej usługi DNS skonfigurowanego cykliczne można rozpoznać nazwy "partners.contoso.com". Cykliczne usługi DNS, czasem nazywane usługą "DNS lokalnych" nie obsługuje bezpośrednio domen DNS. Zamiast klienta off-loads pracy kontaktowania się z różnych autorytatywne usług DNS w Internecie potrzebne do rozpoznania nazwy DNS.
2. Do rozpoznania nazwy DNS, usługi DNS cykliczne wyszukuje serwery nazw dla domeny "contoso.com". Go następnie kontaktuje się te serwery nazw do żądania rekord DNS "partners.contoso.com". Serwery DNS contoso.com zwracają rekord CNAME, który wskazuje contoso.trafficmanager.net.
3. Następnie usługa DNS cykliczne wyszukuje serwery nazw dla domeny "trafficmanager.net", które są dostarczane przez usługę Azure Traffic Manager. Następnie wysyła żądanie "contoso.trafficmanager.net" rekordu DNS do tych serwerów DNS.
4. Serwery nazw Menedżera ruchu otrzymają żądanie. Wybiera on punkt końcowy na podstawie:

    - Stan każdego punktu końcowego skonfigurowanego (wyłączone punkty końcowe nie są zwracane)
    - Bieżąca kondycja każdego punktu końcowego, zgodnie z ustaleniami kontroli kondycji usługi Traffic Manager. Aby uzyskać więcej informacji, zobacz [monitorowanie punktu końcowego Menedżera ruchu](traffic-manager-monitoring.md).
    - Wybranej metody routingu ruchu. Aby uzyskać więcej informacji, zobacz [metody routingu ruchu Menedżera](traffic-manager-routing-methods.md).

5. Wybrany punkt końcowy jest zwracana jako innego rekordu CNAME systemu DNS. W takim przypadku Daj nam Załóżmy, że jest zwracana contoso us.cloudapp.net.
6. Następnie usługa DNS cykliczne wyszukuje serwery nazw dla domeny "cloudapp.net". Kontaktuje się te serwery nazw programu .net contoso us.cloudapp żądania rekordu DNS. Rekord DNS "A", zawierający adres IP punktu końcowego usługi amerykańskiej jest zwracana.
7. Usługa DNS cykliczne konsoliduje wyniki i zwraca pojedynczą odpowiedź DNS do klienta.
8. Klient odbiera wyniki DNS i łączy do danego adresu IP. Klient łączy się z punktem końcowym usługi aplikacji bezpośrednio, nie za pomocą Menedżera ruchu. Ponieważ chodzi o punkt końcowy HTTPS, klient wykonuje niezbędne Uzgadnianie SSL/TLS, a następnie zgłasza żądanie HTTP GET "/ login.aspx" strony.

Usługa DNS cykliczne buforuje odbieranych odpowiedzi DNS. Program rozpoznawania nazw DNS na urządzeniu klienckim buforuje wynik. Buforowanie umożliwia późniejsze zapytań DNS do szybciej udziela odpowiedzi przy użyciu danych z pamięci podręcznej, a nie inne serwery nazw zapytań. Czas trwania pamięci podręcznej jest określany przez właściwość "time-to-live" (TTL) każdego rekordu DNS. Krótszej wartości powoduje szybsze wygaśnięcia pamięci podręcznej i w związku z tym więcej przechodzenia do serwerów nazw usługi Traffic Manager. Dłużej wartości oznaczają, że może to trwać dłużej bezpośredniego ruch od punktu końcowego nie powiodło się. Traffic Manager można skonfigurować czas wygaśnięcia używane w odpowiedzi DNS Menedżera ruchu być możliwie jak 0 sekund i wysokości 2 147 483 647 sekund (zgodne z maksymalną wielkość zakresu [ze standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt)), dzięki któremu można wybrać wartość, która najlepiej równoważy wymagań aplikacji.

## <a name="pricing"></a>Cennik

Aby uzyskać informacje o cenach, zobacz [cennik usługi Traffic Manager](https://azure.microsoft.com/pricing/details/traffic-manager/).

## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczące usługi Traffic Manager, zobacz [Traffic Manager — często zadawane pytania](traffic-manager-FAQs.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Menedżerze ruchu [punkt końcowy monitorowania i automatycznej pracy awaryjnej](traffic-manager-monitoring.md).

Dowiedz się więcej o Menedżerze ruchu [metody routingu ruchu](traffic-manager-routing-methods.md).

Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.

<!--Image references-->
[1]: ./media/traffic-manager-how-traffic-manager-works/dns-configuration.png
[2]: ./media/traffic-manager-how-traffic-manager-works/flow.png

