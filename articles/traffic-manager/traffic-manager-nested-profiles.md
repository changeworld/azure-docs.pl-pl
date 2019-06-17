---
title: Zagnieżdżone profile usługi Traffic Manager na platformie Azure
titlesuffix: Azure Traffic Manager
description: W tym artykule opisano funkcję "Zagnieżdżone profile" z usługi Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: allensu
ms.openlocfilehash: 3c5459d0474ecd45501e634c4777fa178386183c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071150"
---
# <a name="nested-traffic-manager-profiles"></a>Zagnieżdżone profile usługi Traffic Manager

Usługa Traffic Manager udostępnia szereg metod routingu ruchu, które pozwalają kontrolować, jak usługa Traffic Manager wybierze, którym punktem końcowym powinny odbierać dane z każdego użytkownika końcowego. Aby uzyskać więcej informacji, zobacz [metody routingu ruchu w usłudze Traffic Manager](traffic-manager-routing-methods.md).

Każdy profil usługi Traffic Manager określa pojedynczą metodę routingu ruchu. Jednak istnieją scenariusze, które wymagają bardziej zaawansowanych routing ruchu niż routingu przez jeden profil usługi Traffic Manager. Można zagnieżdżać profile usługi Traffic Manager, aby połączyć korzyści wynikające z więcej niż jednej metody routingu ruchu. Zagnieżdżone Profile umożliwiają zastąpić domyślne zachowanie usługi Traffic Manager, obsługi większych i bardziej złożonych wdrożeń aplikacji.

Poniższe przykłady ilustrują sposób korzystania zagnieżdżone profile usługi Traffic Manager w różnych scenariuszach.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Przykład 1: Łączenie "Wydajność" i "Ważona" routing ruchu

Załóżmy, że wdrożono aplikację w następujących regionach platformy Azure: Zachodnie stany USA, Europa Zachodnia i Azja Wschodnia. Metody routingu ruchu "Wydajność" usługi Traffic Manager umożliwia kierowanie ruchu do region najbliżej użytkownika.

![Profil usługi Traffic Manager w jednym][4]

Teraz załóżmy, że chcesz przetestować aktualizacji usługi przed udostępnieniem jej więcej powszechnie. Chcesz użyć "ważona" metody routingu ruchu do kierowania niewielką część ruchu do wdrożenia testowego. Testowe wdrożenie wraz z istniejącego wdrożenia produkcyjnego należy skonfigurować w regionie Europa Zachodnia.

Nie można łączyć zarówno ważona i "wydajności routingu ruchu w jeden profil. Aby obsługiwać ten scenariusz, należy utworzyć profil usługi Traffic Manager przy użyciu dwa punkty końcowe w regionie Europa Zachodnia i metody routingu ruchu "Ważona". Następnie można dodać tego profilu "podrzędne" jako punkt końcowy do profilu "parent". Profil nadrzędnego nadal korzysta z metody routingu ruchu wydajności i zawiera inne wdrożeniami globalnymi jako punkty końcowe.

Na poniższym diagramie przedstawiono w tym przykładzie:

![Zagnieżdżone profile usługi Traffic Manager][2]

W tej konfiguracji ruch skierowany za pośrednictwem profilu nadrzędnego dystrybuuje ruch między regionami normalnie. W regionie Europa Zachodnia profilu zagnieżdżone dystrybuuje ruch do celów produkcyjnych i testowych punktów końcowych zgodnie z przypisane wagi.

Gdy profil nadrzędnego, który używa metody routingu ruchu "Wydajność", każdy punkt końcowy musi być przypisany lokalizacji. Lokalizacja jest przypisany podczas konfigurowania punktu końcowego. Wybierz region platformy Azure, która jest najbliżej danego wdrożenia. Regiony platformy Azure są obsługiwane przez tabelę opóźnienie Internet wartości lokalizacji. Aby uzyskać więcej informacji, zobacz [usługi Traffic Manager "Wydajność" metody routingu ruchu](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Przykład 2: Monitorowanie punktu końcowego w profile zagnieżdżone

Traffic Manager aktywnie monitoruje kondycję każdego punktu końcowego usługi. Jeśli punkt końcowy jest w złej kondycji usługi Traffic Manager kieruje użytkowników do alternatywne punkty końcowe, aby zachować dostępność usługi. To zachowanie monitorowanie i trybu failover punktu końcowego ma zastosowanie do wszystkich metod routingu ruchu. Aby uzyskać więcej informacji, zobacz [monitorowanie punktu końcowego usługi Traffic Manager](traffic-manager-monitoring.md). Monitorowanie punktu końcowego działa inaczej w przypadku profilów zagnieżdżonych. Przy użyciu profilów zagnieżdżonych profilu nadrzędnego nie sprawdzać kondycję w elemencie podrzędnym bezpośrednio. Zamiast tego kondycji punktów końcowych profilu podrzędnych jest używana do obliczenia ogólnej kondycji profilu podrzędnych. Te informacje o kondycji jest propagowane w górę hierarchii profilów zagnieżdżonych. Profil nadrzędnego używa tego zagregowanej kondycji do ustalenia, czy należy kierować ruch do profilu podrzędnych. Zobacz [— często zadawane pytania](traffic-manager-FAQs.md#traffic-manager-nested-profiles) Aby uzyskać szczegółowe informacje na temat monitorowania kondycji, profilów zagnieżdżonych.

Wracając do poprzedniego przykładu, załóżmy, że wdrożenie produkcyjne w regionie Europa Zachodnia zakończy się niepowodzeniem. Domyślnie profilu "podrzędne" Określa, że cały ruch do wdrożenia testowego. Jeśli testowe wdrożenie nie powiedzie się także, profilu nadrzędnego Określa, czy profilu podrzędnych nie powinni odbierać ruch, ponieważ wszystkie podrzędne punkty końcowe są w złej kondycji. Następnie profilu nadrzędnego dystrybuuje ruch do innych regionów.

![Zagnieżdżone Profile pracy awaryjnej (zachowanie domyślne)][3]

Może się wszystkiego o taki układ. Lub mogą być zainteresowane, że cały ruch dla Europa Zachodnia posłuży do testowego wdrożenia na zamiast ruchu podzbioru ograniczone. Niezależnie od kondycji testowego wdrożenia chcesz do trybu failover do innych regionów, w przypadku niepowodzenia wdrożenia produkcyjnego w regionie Europa Zachodnia. Aby włączyć ten tryb failover, mogą określić parametr "MinChildEndpoints", podczas konfigurowania profilu podrzędnych jako punkt końcowy w profilu nadrzędnej. Parametr określa minimalną liczbę dostępnych punktów końcowych w profilu podrzędnych. Wartość domyślna to "1". W tym scenariuszu wartość elementu MinChildEndpoints równa 2. Poniżej tego progu profilu nadrzędnego uwzględnia profilu podrzędnych całego ona niedostępna i kieruje ruch do innych punktów końcowych.

Na poniższym rysunku przedstawiono tę konfigurację:

![Zagnieżdżone Profile trybu failover "MinChildEndpoints" = 2][4]

> [!NOTE]
> Metody routingu ruchu "Priority" rozsyła cały ruch do pojedynczego punktu końcowego. Związku z tym nieco cel w jest ustawienie MinChildEndpoints inne niż "1" dla profilu podrzędnych.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Przykład 3: Regiony priorytetów trybu failover w routingu ruchu "Wydajność"

Domyślne zachowanie dla metody routingu ruchu "Wydajność" jest w przypadku punktów końcowych w różnych lokalizacjach geograficznych, które użytkownicy końcowi są kierowane do punktu końcowego "najbliższy" pod kątem najniższych opóźnieniem sieci.

Jednak Załóżmy, że wolisz trybu failover ruchu Europa Zachodnia, zachodnie stany USA, a tylko kierować ruch do innych regionów, gdy oba punkty końcowe są niedostępne. Możesz utworzyć to rozwiązanie korzysta z profilu podrzędnych za pomocą metody routingu ruchu "Priority".

![Routingu preferencyjne przejścia w tryb failover ruchu "Wydajność"][6]

Ponieważ punkt końcowy Europa Zachodnia ma wyższy priorytet niż punkt końcowy zachodnie stany USA, cały ruch jest wysyłany do endpoint Europa Zachodnia, gdy oba punkty końcowe są w trybie online. W przypadku niepowodzenia Europa Zachodnia, jego ruch będzie kierowany do regionu zachodnie stany USA. Za pomocą profilu zagnieżdżone ruch będzie kierowany do Azja Wschodnia, tylko wtedy, gdy zarówno Europa Zachodnia i zachodnie stany USA się nie powieść.

Ten wzorzec można powtórzyć we wszystkich regionach. Zamień na wszystkich trzech punktów końcowych w profilu nadrzędnego trzy profile podrzędnych, każde z nich prezentuje kolejność priorytetów trybu failover.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Przykład 4: Kontrolowanie routing "Wydajność" ruchu między wieloma punktami końcowymi, w tym samym regionie

Załóżmy, że metoda routingu ruchu jest używana w profilu, który ma więcej niż jeden punkt końcowy w danym regionie "wydajność". Domyślnie ruch skierowany do tego regionu są rozkładane równomiernie pomiędzy wszystkie dostępne punkty końcowe w danym regionie.

!["Wydajność" ruchu routingu Dystrybucja ruchu w regionie (zachowanie domyślne)][7]

Zamiast opcji dodawania wielu punktów końcowych w regionie Europa Zachodnia, te punkty końcowe są ujęte w profilu oddzielne podrzędnych. Profil podrzędny zostanie dodany do elementu nadrzędnego jako punkt końcowy tylko w regionie Europa Zachodnia. Ustawienia w profilu podrzędnych umożliwia kontrolowanie dystrybucji ruchu z Europa Zachodnia, przez włączenie routingu ruchu na podstawie priorytetu równego lub ważonego w danym regionie.

![Ruch "Wydajność" routing za pomocą dystrybucji niestandardowe ruchu w regionie][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Przykład 5: Ustawienia monitorowania dla punktu końcowego

Załóżmy, że używasz usługi Traffic Manager można migrować bezproblemowym ruch ze starszej wersji lokalnej witryny sieci web do nowej wersji opartej na chmurze hostowanych na platformie Azure. Dla starszej wersji witryny chcesz użyć na stronie głównej identyfikatora URI do monitorowania kondycji lokacji. Ale z nową wersją oparte na chmurze, wdrażają niestandardową stronę monitorowania (ścieżka "/ monitor.aspx") zawierającej dodatkowe kontrole.

![Punkt końcowy usługi Traffic Manager monitoring (zachowanie domyślne)][9]

Ustawienia monitorowania w profilu usługi Traffic Manager dotyczą wszystkich punktów końcowych w jeden profil. Przy użyciu profilów zagnieżdżonych umożliwia podrzędny inny profil dla każdej witryny definiują różne ustawienia monitorowania.

![Punkt końcowy usługi Traffic Manager monitorowanie za pomocą ustawienia poszczególnych punktu końcowego][10]

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się więcej o [profile usługi Traffic Manager](traffic-manager-overview.md)

Dowiedz się, jak [Tworzenie profilu usługi Traffic Manager](traffic-manager-create-profile.md)

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
