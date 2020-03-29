---
title: Zagnieżdżone profile menedżera ruchu na platformie Azure
titleSuffix: Azure Traffic Manager
description: W tym artykule opisano funkcję "Profile zagnieżdżone" usługi Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: rohink
ms.openlocfilehash: 282099cb274c1ea872a0df9c2753a939ef31421f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938574"
---
# <a name="nested-traffic-manager-profiles"></a>Zagnieżdżone profile usługi Traffic Manager

Usługa Traffic Manager zawiera szereg metod routingu ruchu, które umożliwiają kontrolowanie sposobu wybierania przez usługę Traffic Manager punktu końcowego, który powinien odbierać ruch od każdego użytkownika końcowego. Aby uzyskać więcej informacji, zobacz [Metody routingu ruchu usługi Traffic Manager](traffic-manager-routing-methods.md).

Każdy profil usługi Traffic Manager określa jedną metodę routingu ruchu. Istnieją jednak scenariusze, które wymagają bardziej zaawansowanego routingu ruchu niż routing dostarczony przez jeden profil usługi Traffic Manager. Profile usługi Traffic Manager można zagnieżdżać, aby połączyć zalety więcej niż jednej metody routingu ruchu. Profile zagnieżdżone umożliwiają zastąpienie domyślnego zachowania usługi Traffic Manager w celu obsługi większych i bardziej złożonych wdrożeń aplikacji.

Poniższe przykłady ilustrują sposób używania zagnieżdżonych profilów usługi Traffic Manager w różnych scenariuszach.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Przykład 1: Łączenie routingu ruchu "Wydajność" i "ważony"

Załóżmy, że wdrożono aplikację w następujących regionach platformy Azure: Zachodnie stany USA, Europa Zachodnia i Azja Wschodnia. Do dystrybucji ruchu do regionu najbliższego użytkownikowi używa się metody routingu ruchu "Wydajność" usługi Traffic Manager.

![Profil usługi Single Traffic Manager][4]

Teraz załóżmy, że chcesz przetestować aktualizację usługi przed wprowadzeniem go szerzej. Chcesz użyć metody routingu ruchu "ważona", aby skierować niewielki procent ruchu do wdrożenia testowego. Narzędzie testowe jest skonfigurowane wraz z istniejącym wdrożeniem produkcyjnym w Europie Zachodniej.

Nie można połączyć zarówno "Ważony" i "Wydajność ruchu routingu w jednym profilu. Aby obserwoć ten scenariusz, należy utworzyć profil usługi Traffic Manager przy użyciu dwóch punktów końcowych Europy Zachodniej i "Ważona" metoda routingu ruchu. Następnie należy dodać ten profil "podrzędny" jako punkt końcowy do profilu "nadrzędny". Profil nadrzędny nadal używa metody routingu ruchu wydajności i zawiera inne wdrożenia globalne jako punkty końcowe.

Na poniższym diagramie przedstawiono ten przykład:

![Zagnieżdżone profile usługi Traffic Manager][2]

W tej konfiguracji ruch kierowany za pośrednictwem profilu nadrzędnego normalnie rozprowadza ruch w różnych regionach. W Europie Zachodniej profil zagnieżdżony rozprowadza ruch do punktów końcowych produkcji i testów zgodnie z przypisanymi wagami.

Gdy profil nadrzędny używa metody routingu ruchu "Wydajność", każdemu punktowi końcowemu musi być przypisana lokalizacja. Lokalizacja jest przypisywana podczas konfigurowania punktu końcowego. Wybierz region platformy Azure najbliżej wdrożenia. Regiony platformy Azure są wartości lokalizacji obsługiwane przez tabelę opóźnienia internet. Aby uzyskać więcej informacji, zobacz [Traffic Manager 'Performance' traffic-routing metody](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Przykład 2: Monitorowanie punktów końcowych w profilach zagnieżdżonych

Usługa Traffic Manager aktywnie monitoruje kondycję każdego punktu końcowego usługi. Jeśli punkt końcowy jest w złej kondycji, usługa Traffic Manager kieruje użytkowników do alternatywnych punktów końcowych w celu zachowania dostępności usługi. To zachowanie monitorowania punktu końcowego i pracy awaryjnej dotyczy wszystkich metod routingu ruchu. Aby uzyskać więcej informacji, zobacz [Monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md). Monitorowanie punktu końcowego działa inaczej dla profilów zagnieżdżonych. W profilach zagnieżdżonych profil nadrzędny nie wykonuje bezpośrednio kontroli kondycji dziecka. Zamiast tego kondycja punktów końcowych profilu podrzędnego jest używana do obliczania ogólnej kondycji profilu podrzędnego. Te informacje o kondycji są propagowane w hierarchii profilu zagnieżdżonego. Profil nadrzędny używa tego zagregowanego zdrowia, aby określić, czy ruch ma być kierowany do profilu podrzędnego. Zobacz często zadawane [pytania, aby](traffic-manager-FAQs.md#traffic-manager-nested-profiles) uzyskać szczegółowe informacje na temat monitorowania kondycji profili zagnieżdżonych.

Wracając do poprzedniego przykładu, załóżmy, że wdrożenie produkcyjne w Europie Zachodniej nie powiedzie się. Domyślnie profil "podrzędny" kieruje cały ruch do wdrożenia testowego. Jeśli wdrożenie testowe również zakończy się niepowodzeniem, profil nadrzędny określa, że profil podrzędny nie powinien odbierać ruchu, ponieważ wszystkie podrzędne punkty końcowe są w złej kondycji. Następnie profil nadrzędny dystrybuuje ruch do innych regionów.

![Tryb failover profilu zagnieżdżonego (zachowanie domyślne)][3]

Możesz być zadowolony z tego układu. Lub może być zaniepokojony, że cały ruch w Europie Zachodniej jest teraz będzie do wdrożenia testowego zamiast ograniczonego ruchu podzbioru. Niezależnie od kondycji wdrożenia testu, chcesz przejść w stan fail over do innych regionów, gdy wdrożenie produkcyjne w Europie Zachodniej kończy się niepowodzeniem. Aby włączyć tę funkcję failover, można określić parametr "MinChildEndpoints" podczas konfigurowania profilu podrzędnego jako punktu końcowego w profilu nadrzędnym. Parametr określa minimalną liczbę dostępnych punktów końcowych w profilu podrzędnym. Wartością domyślną jest "1". W tym scenariuszu należy ustawić Wartość MinChildEndpoints na 2. Poniżej tego progu profil nadrzędny uważa, że profil całego dziecka jest niedostępny i kieruje ruch do innych punktów końcowych.

Poniższy rysunek ilustruje tę konfigurację:

![Zagnieżdżona przewijanie awaryjne profilu z "MinChildEndpoints" = 2][4]

> [!NOTE]
> Metoda routingu ruchu "Priorytet" dystrybuuje cały ruch do jednego punktu końcowego. W związku z tym istnieje niewielki cel w MinChildEndpoints ustawienie inne niż "1" dla profilu podrzędnego.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Przykład 3: Priorytetowe regiony trybu failover w routingu ruchu "Wydajność"

Domyślne zachowanie dla metody routingu ruchu "Wydajność" jest, gdy masz punkty końcowe w różnych lokalizacjach geograficznych użytkownicy końcowi są kierowane do "najbliższego" punktu końcowego pod względem najniższego opóźnienia sieci.

Załóżmy jednak, że ruch w Europie Zachodniej jest przewijany w trybie failover do zachodnich stanów USA i tylko ruch bezpośredni do innych regionów, gdy oba punkty końcowe są niedostępne. To rozwiązanie można utworzyć przy użyciu profilu podrzędnego przy użyciu metody routingu ruchu "Priorytet".

![Routing ruchu "Performance" z preferencyjnym przejściem awaryjnym][6]

Ponieważ punkt końcowy Europy Zachodniej ma wyższy priorytet niż punkt końcowy zachodnie stany USA, cały ruch jest wysyłany do punktu końcowego Europy Zachodniej, gdy oba punkty końcowe są w trybie online. Jeśli Europa Zachodnia zawiedzie, jej ruch jest kierowany do zachodnich Stanów Zjednoczonych. W profilu zagnieżdżonego ruch jest kierowany do Azji Wschodniej tylko wtedy, gdy zarówno Europa Zachodnia, jak i Zachodnie stany USA zawiodą.

Można powtórzyć ten wzorzec dla wszystkich regionów. Zastąp wszystkie trzy punkty końcowe w profilu nadrzędnym trzema profilami podrzędnymi, z których każdy zapewnia priorytetową sekwencję trybu failover.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Przykład 4: Kontrolowanie routingu ruchu "Wydajność" między wieloma punktami końcowymi w tym samym regionie

Załóżmy, że metoda routingu ruchu "Wydajność" jest używana w profilu, który ma więcej niż jeden punkt końcowy w określonym regionie. Domyślnie ruch kierowany do tego regionu jest rozłożony równomiernie na wszystkie dostępne punkty końcowe w tym regionie.

![Routing ruchu "Wydajność" w dystrybucji ruchu w regionie (zachowanie domyślne)][7]

Zamiast dodawać wiele punktów końcowych w Europie Zachodniej, te punkty końcowe są ujęte w oddzielny profil podrzędny. Profil podrzędny jest dodawany do nadrzędnego jako jedyny punkt końcowy w Europie Zachodniej. Ustawienia profilu podrzędnego mogą kontrolować dystrybucję ruchu w Europie Zachodniej, włączając routing ruchu opartego na priorytecie lub ważony w tym regionie.

![Routing ruchu "Wydajność" z niestandardową dystrybucją ruchu w regionie][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Przykład 5: Ustawienia monitorowania punktu końcowego

Załóżmy, że używasz usługi Traffic Manager do płynnej migracji ruchu ze starszej lokalnej witryny sieci Web do nowej wersji opartej na chmurze hostowanej na platformie Azure. W przypadku starszej witryny chcesz użyć identyfikatora URI strony głównej do monitorowania kondycji witryny. Ale dla nowej wersji opartej na chmurze implementujesz niestandardową stronę monitorowania (ścieżka "/monitor.aspx"), która zawiera dodatkowe kontrole.

![Monitorowanie punktu końcowego usługi Traffic Manager (zachowanie domyślne)][9]

Ustawienia monitorowania w profilu usługi Traffic Manager dotyczą wszystkich punktów końcowych w ramach jednego profilu. W przypadku profilów zagnieżdżonych można użyć innego profilu podrzędnego na witrynę do zdefiniowania różnych ustawień monitorowania.

![Monitorowanie punktu końcowego usługi Traffic Manager z ustawieniami punktu końcowego][10]

## <a name="faqs"></a>Często zadawane pytania

* [Jak skonfigurować profile zagnieżdżone?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#traffic-manager-endpoint-monitoring)

* [Ile warstw zagnieżdżania obsługuje usługa Traffic Manger?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-many-layers-of-nesting-does-traffic-manger-support)

* [Czy można mieszać inne typy punktów końcowych z zagnieżdżonych profilów podrzędnych, w tym samym profilu usługi Traffic Manager?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [W jaki sposób model rozliczeń ma zastosowanie do profilów zagnieżdżonych?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-the-billing-model-apply-for-nested-profiles)

* [Czy wpływ na wydajność profilów zagnieżdżonych?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#is-there-a-performance-impact-for-nested-profiles)

* [W jaki sposób usługa Traffic Manager oblicza kondycję zagnieżdżonego punktu końcowego w profilu nadrzędnym?](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [profilach usługi Traffic Manager](traffic-manager-overview.md)

Dowiedz się, jak [utworzyć profil usługi Traffic Manager](traffic-manager-create-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png
