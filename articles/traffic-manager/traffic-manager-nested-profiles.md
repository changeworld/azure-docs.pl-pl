---
title: Zagnieżdżone profilów usługi Traffic Manager | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcję "Zagnieżdżonych profilów" z usługi Azure Traffic Manager
services: traffic-manager
documentationcenter: ''
author: kumudd
manager: timlt
editor: ''
ms.assetid: f1b112c4-a3b1-496e-90eb-41e235a49609
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: kumud
ms.openlocfilehash: 1ac4ec2775ca9f690f5adf4f939908f8cee3f715
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876978"
---
# <a name="nested-traffic-manager-profiles"></a>Zagnieżdżonych profilów usługi Traffic Manager

Menedżer ruchu zawiera szereg metody routingu ruchu, które umożliwiają kontrolowanie sposobu Traffic Manager wybierze, którym punktem końcowym powinny odbierać dane z każdego użytkownika końcowego. Aby uzyskać więcej informacji, zobacz [metody routingu ruchu Menedżera ruchu](traffic-manager-routing-methods.md).

Każdy profil usługi Traffic Manager określa pojedynczej metody routingu ruchu. Istnieją jednak scenariusze, które wymagają bardziej złożone routingu ruchu niż routingu udostępniane przez jeden profil Menedżera ruchu. Można zagnieżdżać profilów usługi Traffic Manager do łączenia z zalet więcej niż jedna metoda routingu ruchu. Zagnieżdżone Profile umożliwiają zastąpienie zachowania domyślnego menedżera ruchu do obsługi większych i bardziej złożone wdrożenia aplikacji.

Poniższe przykłady przedstawiają sposób używania zagnieżdżonych profilów usługi Traffic Manager w różnych scenariuszach.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Przykład 1: Połączenie "Performance" i "Ważone" routingu ruchu

Załóżmy, że wdrożono aplikację w następujących regionach platformy Azure: zachodnie stany USA, Europa Zachodnia i Azja Wschodnia. Metoda routingu ruchu "Performance" Traffic Manager umożliwia dystrybucję ruchu do regionu najbliżej użytkownika.

![Profil Menedżera ruchu pojedynczej][4]

Teraz załóżmy, że chcesz przeprowadzić test przed udostępnieniem jej więcej powszechnie aktualizacji do usługi. Chcesz użyć "ważoną" metody routingu ruchu do kierowania niewielką część ruchu sieciowego dla danego wdrożenia testu. Testowe wdrożenie równolegle z istniejącego wdrożenia produkcyjnego skonfigurowane w Europa.

Nie można łączyć zarówno ważone i "wydajności-routingu ruchu w jednym profilu. Aby obsługiwać ten scenariusz, należy utworzyć profil Menedżera ruchu przy użyciu dwa punkty końcowe Europa i metody routingu ruchu "Ważone". Następnie można dodać tego profilu 'child' jako punktu końcowego do profilu 'parent'. Profil nadrzędnego nadal używa metody routingu ruchu wydajności i zawiera globalny wdrożenia jako punktów końcowych.

Na poniższym diagramie przedstawiono w tym przykładzie:

![Zagnieżdżonych profilów usługi Traffic Manager][2]

W tej konfiguracji ruch skierowany za pośrednictwem profilu nadrzędnego dystrybuuje ruch w regionach normalnie. W ramach Europa Zachodnia profilu zagnieżdżone dystrybuuje ruch do produkcyjnych i testowych punkty końcowe według wagi przypisane.

Gdy profil nadrzędnego używa metody routingu ruchu "Performance", każdego punktu końcowego musi być przypisany lokalizacji. Lokalizacja jest przypisany podczas konfigurowania punktu końcowego. Wybierz region platformy Azure najbardziej zbliżony do wdrożenia. Regiony platformy Azure są obsługiwane przez tabelę opóźnienia Internet wartości lokalizacji. Aby uzyskać więcej informacji, zobacz [Menedżera ruchu "Performance" metody routingu ruchu](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Przykład 2: Monitorowania punktów końcowych w profilach zagnieżdżone

Menedżer ruchu aktywnie monitoruje kondycję każdego punktu końcowego usługi. Jeśli punkt końcowy jest zła, Traffic Manager kieruje użytkowników do alternatywnego punktów końcowych, aby zachować dostępności usługi. To zachowanie monitorowania i trybu failover punktu końcowego ma zastosowanie do wszystkich metod routingu ruchu. Aby uzyskać więcej informacji, zobacz [monitorowanie punktu końcowego Menedżera ruchu](traffic-manager-monitoring.md). Punkt końcowy monitorowania działa inaczej w przypadku zagnieżdżonych profilów. Przy użyciu profilów zagnieżdżonych profilu nadrzędnego nie sprawdzania kondycji w elemencie podrzędnym bezpośrednio. Zamiast tego kondycji profilu podrzędnych punktów końcowych służy do obliczenia ogólnej kondycji profilu podrzędnych. Te informacje o kondycji są propagowane w górę hierarchii profilu zagnieżdżone. Profil nadrzędnego używa tego zagregowane kondycji do ustalenia, czy umożliwiających kierowanie ruchem do profilu podrzędnych. Zobacz [— często zadawane pytania](traffic-manager-FAQs.md#traffic-manager-nested-profiles) szczegółowe informacje o monitorowanie kondycji zagnieżdżonych profilów.

Wracając do poprzedni przykład, załóżmy, że wdrożenia produkcyjnego w Europie zachodnie nie powiedzie się. Domyślnie profilu 'child' Określa, że cały ruch do środowiska testowego. Jeśli testowe wdrożenie nie powiedzie się także, profil nadrzędnego Określa, czy profilu podrzędnych nie powinni odbierać ruchu, ponieważ wszystkie podrzędne punkty końcowe są w złej kondycji. Następnie profilu nadrzędnego dystrybuuje ruch do innych regionów.

![Zagnieżdżone profilu trybu failover (domyślnie)][3]

Może być to rozmieszczenie modyfikowania. Lub może nie zawierać dane, że cały ruch Europa Zachodnia to teraz będzie wdrożenia testowego zamiast ruchu ograniczonym podzbiorem. Niezależnie od kondycji środowiska testowego ma do trybu failover do innych regionów, w przypadku niepowodzenia wdrożenia produkcyjnego w Europa Zachodnia. Aby włączyć ten tryb failover, można określić parametru "MinChildEndpoints" podczas konfigurowania profilu podrzędnych jako punktu końcowego w profilu nadrzędnej. Parametr określa minimalną liczbę dostępnych punktów końcowych w profilu podrzędnych. Wartość domyślna to "1". W tym scenariuszu należy ustawić wartość MinChildEndpoints na 2. Poniżej tego progu profil nadrzędnego uwzględnia profilu całego podrzędnych jako niedostępny i kieruje ruch do innych punktów końcowych.

Na poniższym rysunku przedstawiono tę konfigurację:

![Zagnieżdżone trybu failover profil z "MinChildEndpoints" = 2][4]

> [!NOTE]
> Metody routingu ruchu 'Priority' rozsyła cały ruch do jednego punktu końcowego. W związku z tym jest niewiele cel ustawienie MinChildEndpoints innych niż "1" dla profilu podrzędnych.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Przykład 3: Priorytetów trybu failover regiony routingu ruchu "Performance"

Domyślne zachowanie dla metody routingu ruchu "Performance" pozwala uniknąć nadmiernego ładowania dalej najbliższego punktu końcowego i powoduje kaskadowych dużej liczby błędów. Gdy punkt końcowy nie powiedzie się, cały ruch, który będzie przekierowywany do określonego punktu końcowego jest równomiernie rozłożona na innych punktów końcowych wszystkich regionach.

![Routing domyślny tryb failover ruchu "Performance"][5]

Jednak Załóżmy wolą trybu failover ruchu Europa zachodnie stany USA, a tylko kierować ruch do innych regionów, gdy oba punkty końcowe są niedostępne. Można utworzyć tego rozwiązania przy użyciu profilu podrzędnych za pomocą metody routingu ruchu 'Priority'.

![Routingu z preferencyjne trybu failover ruchu "wydajność.][6]

Ponieważ punkt końcowy Europa ma wyższy priorytet niż punkt końcowy zachodnie stany USA, cały ruch wysyłany do punktu końcowego Europa Zachodnia, gdy oba punkty końcowe są w trybie online. W przypadku niepowodzenia Europa ruch jest kierowany do zachodnie stany USA. Z profilu zagnieżdżone ruch jest kierowany do Azja Wschodnia, tylko w przypadku, gdy nie powiedzie się zarówno Europa Zachodnia, jak i zachodnie stany USA.

Ten wzorzec można powtórzyć dla wszystkich regionów. Zamień wszystkich trzech punktów końcowych w profilu nadrzędnego trzy profile podrzędnych, każdy dostarczanie kolejność priorytetów trybu failover.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Przykład 4: Kontrolowanie "Performance" routingu ruchu między wieloma punktami końcowymi w tym samym regionie

Załóżmy, że metoda routingu ruchu jest używana w profilu, który ma więcej niż jeden punkt końcowy w określonym regionie "wydajności'. Domyślnie ruch skierowany do tego regionu jest równomiernie rozłożona dostępnych punktów końcowych w tym regionie.

![Ruch "Performance" routingu ruchu w regionie dystrybucji (domyślnie)][7]

Zamiast opcji dodawania wiele punktów końcowych w Europa Zachodnia, te punkty końcowe są ujęte w profilu oddzielne podrzędnych. Profil dziecka zostanie dodany do elementu nadrzędnego jako punktu końcowego tylko w Europa Zachodnia. Ustawienia w profilu podrzędne można kontrolować dystrybucję ruchu z Europa Zachodnia przez włączenie routingu ruchu na podstawie priorytetu lub ważoną w tym regionie.

![Ruch "Performance" routingu z Dystrybucja ruchu w regionie niestandardowych][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Przykład 5: Ustawienia monitorowania dla punktu końcowego

Załóżmy, że używasz usługi Traffic Manager sprawnie migracji ruch z starszych lokalnej witryny sieci web do nowej wersji opartej na chmurze hostowana na platformie Azure. Dla starszych lokacji chcesz użyć strony głównej identyfikator URI do monitorowania kondycji lokacji. Ale dla nowej wersji opartej na chmurze, w przypadku wdrażania monitorowania niestandardowej strony (ścieżka "/ monitor.aspx") zawiera dodatkowe kontrole.

![Punkt końcowy Menedżera ruchu monitorowania (domyślnie)][9]

Ustawienia monitorowania w profilu usługi Traffic Manager dotyczą wszystkich punktów końcowych w jednym profilu. Zagnieżdżonych profilów możesz za pomocą profilu inny podrzędny dla każdej witryny do definiowania różnych ustawień monitorowania.

![Monitorowanie za pomocą ustawień-endpoint punktu końcowego Menedżera ruchu][10]

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [profilów usługi Traffic Manager](traffic-manager-overview.md)

Dowiedz się, jak [Tworzenie profilu Menedżera ruchu](traffic-manager-create-profile.md)

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
