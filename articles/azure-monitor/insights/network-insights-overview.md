---
title: Usługa Azure Monitor dla sieci (wersja zapoznawcza)
description: Szybkie omówienie usługi Azure Monitor dla sieci, który zapewnia kompleksowy widok kondycji i metryki dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/07/2019
ms.openlocfilehash: 0f5b2fbd13cb9658e255fde727e115df748aaed9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654872"
---
# <a name="azure-monitor-for-networks-preview"></a>Usługa Azure Monitor dla sieci (wersja zapoznawcza)
Usługa Azure Monitor for Network zapewnia kompleksowy widok kondycji i metryk dla wszystkich wdrożonych zasobów sieciowych bez żadnej konfiguracji. Zaawansowane funkcje wyszukiwania pomagają identyfikować zależności zasobów, umożliwiając scenariusze, takie jak identyfikowanie zasobów, które hostują witrynę, po prostu wyszukując nazwę hostowanej witryny sieci Web.

Strona **Omówienie** usługi Azure Monitor for Networks zapewnia łatwy sposób wizualizacji spisu zasobów sieciowych wraz ze zdrowiem zasobów i alertami. Jest on podzielony na cztery kluczowe obszary funkcjonalne:

- Wyszukiwanie i filtrowanie
- Kondycja zasobów i metryki
- Alerty 
- Widok zależności

![Strona przeglądowa](media/network-insights-overview/overview.png)

## <a name="search-and-filtering"></a>Wyszukiwanie i filtrowanie
Widok kondycji i alertów zasobów można dostosować za pomocą filtrów, takich jak **subskrypcja,** **grupa zasobów** i **typ zasobu.** Pole wyszukiwania umożliwia przeszukiwanie właściwości zasobów.

W polu wyszukiwania można wyszukiwać zasoby i skojarzone zasoby. Na przykład publiczny adres IP jest skojarzony z bramą aplikacji. Wyszukiwanie nazwy DNS publicznych adresów IP spowoduje zidentyfikowanie zarówno publicznego adresu IP, jak i skojarzonej bramy aplikacji.

![Wyszukiwanie](media/network-insights-overview/search.png)


## <a name="resource-health-and-metric"></a>Kondycja i metryka zasobów
Każdy kafelek reprezentuje typ zasobu, z liczbą wystąpień wdrożonych we wszystkich wybranych subskrypcjach wraz ze stanem kondycji zasobów. W poniższym przykładzie jest 105 ER i połączenia SIECI VPN wdrożone, 103 są w dobrej kondycji i 2 niedostępne.

![Kondycja zasobów](media/network-insights-overview/resource-health.png)

Kliknięcie dwóch niedostępnych połączeń ER i VPN uruchamia widok metryki. 

![Widok metryczny](media/network-insights-overview/metric-view.png)

Możesz kliknąć każdy element w widoku siatki. Kliknij ikonę Kondycja, aby przekierować do kondycji zasobów dla tego połączenia. Kliknij alerty, aby przekierować do strony alertów i metryk odpowiednio dla tego połączenia. 

## <a name="alerts"></a>Alerty
Siatka **Alerty** po prawej stronie udostępnia widok wszystkich alertów generowanych dla wybranych zasobów we wszystkich subskrypcjach. Kliknij liczbę alertów, aby przejść do strony alertów szczegółowych.

## <a name="dependency-view"></a>Widok zależności
Widok **zależności** pomaga wizualizować sposób konfigurowania zasobu. Obecnie widok zależności jest obsługiwany tylko dla bramy aplikacji. Dostęp do widoku zależności można uzyskać, klikając nazwę zasobu bramy aplikacji z widoku siatki metryki.

![Widok bramy aplikacji](media/network-insights-overview/application-gateway.png)

Widok zależności dla bramy aplikacji zapewnia uproszczony widok sposobu, w jaki frontonowe **serwery** IP są połączone z odbiornikami, regułami i pulą wewnętrznej bazy danych. Krawędzie łączące są oznaczone kolorami i zawierają dodatkowe szczegóły na podstawie kondycji puli wewnętrznej bazy danych. Widok zawiera również szczegółowy widok metryk bramy aplikacji i metryk dla wszystkich powiązanych pul zaplecza, takich jak VMSS i wystąpienia maszyn wirtualnych.

![Widok zależności](media/network-insights-overview/dependency-view.png)

Wykres zależności umożliwia łatwą nawigację do ustawień konfiguracji. Kliknij prawym przyciskiem myszy pulę zaplecza, aby uzyskać dostęp do innych funkcji. Na przykład jeśli pula wewnętrznej bazy danych jest maszyną wirtualną, można bezpośrednio uzyskać dostęp do aplikacji VM Insights i rozwiązywania problemów z połączeniem z usługą Network Watcher w celu zidentyfikowania problemów z łącznością.

![Menu widoku zależności](media/network-insights-overview/dependency-view-menu.png)

Pasek wyszukiwania i filtru w widoku zależności zapewnia łatwy sposób wyszukiwania na wykresie. Na przykład wyszukiwanie *AppGWTestRule* w poniższym przykładzie zawęzi widok graficzny do wszystkich węzłów połączonych za pośrednictwem *AppGWTestRule*. 

![Przykład wyszukiwania](media/network-insights-overview/search-example.png)

Różne filtry zapewniają pomoc zawęzić do określonej ścieżki i stanu. Na przykład wybierz tylko *złej kondycji* z listy rozwijanej **Stan kondycji,** aby wyświetlić wszystkie krawędzie, gdzie stan jest *złej kondycji*.

Kliknij **widok szczegółowe metryki,** aby uruchomić wstępnie skonfigurowany skoroszyt ze szczegółowymi metrykami bramy aplikacji, wszystkich zasobów puli wewnętrznej bazy danych i interfejsów IP front-end. 

## <a name="next-steps"></a>Następne kroki 

- Dowiedz się więcej o monitorowaniu sieci na co to [Azure Network Watcher?](/azure/network-watcher/network-watcher-monitoring-overview).
