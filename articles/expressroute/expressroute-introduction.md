---
title: 'Omówienie usługi ExpressRoute: rozszerzanie sieci lokalnej na platformę Azure za pośrednictwem połączenia prywatnego | Microsoft Docs'
description: W opisie technicznym usługi ExpressRoute omówiono sposób działania połączenia usługi ExpressRoute podczas rozszerzania sieci lokalnej na platformę Azure przez połączenie prywatne.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: overview
ms.date: 09/19/2018
ms.author: cherylmc
ms.openlocfilehash: f1bc447f73912ccf04f0c0cf7d6460d472bace4c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961913"
---
# <a name="expressroute-overview"></a>Omówienie usługi ExpressRoute
Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem połączenia prywatnego obsługiwanego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.

Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych przez dostawcę połączenia w ramach infrastruktury współlokacji. Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Informacje dotyczące łączenia sieci z firmą Microsoft przy użyciu usługi ExpressRoute można znaleźć w temacie [Modele połączeń usługi ExpressRoute](expressroute-connectivity-models.md).

![](./media/expressroute-introduction/expressroute-connection-overview.png)

## <a name="key-benefits"></a>Najważniejsze korzyści

* Łączność w warstwie 3 między siecią lokalną a usługą Microsoft Cloud za pośrednictwem dostawcy połączenia. Połączenie może być z sieci typu dowolna-dowolna (IP VPN), sieci Ethernet typu punkt-punkt lub przy użyciu łączności obejmującej wiele połączeń wirtualnych za pośrednictwem wymiany sieci Ethernet.
* Łączność z usługami w chmurze firmy Microsoft we wszystkich regionach w regionie geopolitycznym.
* Łączność globalna z usługami Microsoft we wszystkich regionach dzięki dodatkowi ExpressRoute Premium.
* Routing dynamiczny między siecią użytkownika i firmą Microsoft za pośrednictwem standardowych protokołów (BGP).
* Wbudowana nadmiarowość w każdej lokalizacji komunikacji równorzędnej w celu uzyskania większej niezawodności.
* Czas sprawnego działania połączenia zgodnie z umową [SLA](https://azure.microsoft.com/support/legal/sla/).
* Obsługa QoS dla programu Skype dla firm.

Więcej informacji znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

## <a name="features"></a>Funkcje

### <a name="layer-3-connectivity"></a>Łączność w warstwie 3
Firma Microsoft używa standardowego protokołu routingu dynamicznego (BGP) do wymiany tras między siecią lokalną, wystąpieniami na platformie Azure i publicznymi adresami Microsoft.  Ustanawiamy wiele sesji BGP z siecią dla różnych profilów ruchu. Więcej szczegółowych informacji można znaleźć w artykule [ExpressRoute circuit and routing domains](expressroute-circuit-peerings.md) (Obwód i domeny routingu usługi ExpressRoute).

### <a name="redundancy"></a>Nadmiarowość
Każdy obwód usługi ExpressRoute składa się z dwóch połączeń z dwoma routerami granicznymi Microsoft Enterprise (MSEE) od dostawcy połączenia/z granicy sieci. Firma Microsoft wymaga podwójnego połączenia BGP od dostawcy połączenia/ze strony użytkownika — po jednym do każdego MSEE. Można zdecydować o niewdrażaniu nadmiarowych urządzeń/obwodów sieci Ethernet po swojej stronie. Dostawcy połączenia używają jednak urządzeń nadmiarowych, aby zagwarantować, że połączenia będą przekazywane do firmy Microsoft w sposób nadmiarowy. Konfiguracja łączności nadmiarowej w warstwie 3 jest wymaganiem, które musi być spełnione, aby umowa [SLA](https://azure.microsoft.com/support/legal/sla/) była ważna.

### <a name="connectivity-to-microsoft-cloud-services"></a>Łączność z usługami w chmurze firmy Microsoft

Połączenia ExpressRoute umożliwiają dostęp do następujących usług:

* Usługi Microsoft Azure
* Usługi Microsoft Office 365
* Microsoft Dynamics 365

> [!NOTE]
> [!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]
> 

Szczegółowa lista usług obsługiwanych za pośrednictwem usługi ExpressRoute znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania). 

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Łączność z wszystkimi regionami w regionie geopolitycznym
Możesz połączyć się z firmą Microsoft w jednej z naszych [lokalizacji komunikacji równorzędnej](expressroute-locations.md) i mieć dostęp do wszystkich regionów w regionie geopolitycznym. 

Na przykład po połączeniu się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute masz dostęp do wszystkich usług w chmurze Microsoft obsługiwanych w Europie Północnej i Europie Zachodniej. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute), który zawiera omówienie regionów geopolitycznych, powiązanych regionów chmury Microsoft i odpowiadających im lokalizacji komunikacji równorzędnej usługi ExpressRoute.

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Globalna łączność dzięki dodatkowi ExpressRoute Premium
Możesz włączyć funkcję dodatku ExpressRoute Premium, aby rozszerzyć łączność w ramach granic geopolitycznych. Na przykład po połączeniu się z firmą Microsoft w Amsterdamie za pośrednictwem usługi ExpressRoute będziesz mieć dostęp do wszystkich usług w chmurze Microsoft obsługiwanych we wszystkich regionach na świecie (z wyjątkiem chmur krajowych). Możesz uzyskać dostęp do usług wdrożonych w Ameryce Południowej lub Australii w taki sam sposób, w jaki uzyskujesz dostęp do regionów Europy Północnej i Zachodniej.

### <a name="across-on-premises-connectivity-with-expressroute-global-reach"></a>Łączność między sieciami lokalnymi za pomocą rozwiązania ExpressRoute Global Reach

Możesz umożliwić rozwiązaniu ExpressRoute Global Reach wymianę danych między lokacjami zdalnymi, łącząc wiele obwodów usługi ExpressRoute. Jeśli na przykład masz prywatne centrum danych w Kalifornii połączone z usługą ExpressRoute w Dolinie Krzemowej i kolejne prywatne centrum danych w Teksasie połączone z usługą ExpressRoute w Dallas, dzięki rozwiązaniu ExpressRoute Global Reach możesz połączyć te prywatne centra danych za pośrednictwem dwóch obwodów usługi ExpressRoute. Ruch sieciowy między centrami danych będzie odbywał się za pośrednictwem sieci firmy Microsoft.

Aby uzyskać więcej informacji, zobacz [ExpressRoute Global Reach](expressroute-global-reach.md).

### <a name="rich-connectivity-partner-ecosystem"></a>Bogaty ekosystem partnerów łączności
Usługa ExpressRoute ma rosnący ekosystem dostawców połączenia i partnerów SI. Najnowsze informacje znajdują się w artykule [ExpressRoute providers and locations](expressroute-locations.md) (Dostawcy i lokalizacje usługi ExpressRoute).

### <a name="connectivity-to-national-clouds"></a>Łączność z chmurami krajowymi
Firma Microsoft obsługuje izolowane środowiska w chmurze dla określonych regionów geopolitycznych i segmentów klientów. Lista krajowych chmur i dostawców znajduje się w artykule [ExpressRoute providers and locations](expressroute-locations.md) (Dostawcy i lokalizacje usługi ExpressRoute).

### <a name="expressroute-direct"></a>Usługa ExpressRoute Direct

Usługa ExpressRoute Direct daje klientom możliwość łączenia się bezpośrednio z globalną siecią firmy Microsoft w skomunikowanych równorzędnie lokalizacjach strategicznie rozmieszczonych na całym świecie. Usługa ExpressRoute Direct zapewnia dwustronną łączność z przepustowością 100 Gb/s, co umożliwia obsługę łączności w trybie aktywny/aktywny na dużą skalę. 

Oto kilka najważniejszych funkcji, które zapewnia usługa ExpressRoute Direct:

* Masywne pozyskiwanie danych do usług, takich jak Storage i Cosmos DB 
* Fizyczne oddzielenie w przypadku tych branż, które są regulowane i w których łączność musi być dedykowana i oddzielona, na przykład w przypadku usług bankowych, rządowych i handlowych 
* Pełna kontrola nad rozmieszczeniem obwodów na podstawie jednostek biznesowych

Aby uzyskać więcej informacji, zobacz [About ExpressRoute Direct (Usługa ExpressRoute Direct)](https://go.microsoft.com/fwlink/?linkid=2022973).

### <a name="bandwidth-options"></a>Opcje przepustowości
Możesz zakupić obwody usługi ExpressRoute do szerokiego zakresu przepustowości. Lista obsługiwanych przepustowości znajduje się poniżej. Koniecznie sprawdź u dostawcy połączenia listę obsługiwanych przepustowości, które oferuje.

* 50 Mb/s
* 100 Mb/s
* 200 Mb/s
* 500 Mb/s
* 1 Gb/s
* 2 Gb/s
* 5 Gb/s
* 10 Gb/s

### <a name="dynamic-scaling-of-bandwidth"></a>Dynamiczne skalowanie przepustowości
Możesz zwiększać przepustowość obwodu usługi ExpressRoute (w oparciu o optymalne rozwiązanie) bez konieczności zatrzymywania połączeń. 

### <a name="flexible-billing-models"></a>Elastyczne modele rozliczeń
Możesz wybrać najlepszy dla siebie model rozliczeń. Wybierz z modeli rozliczeń wymienionych poniżej. Więcej informacji znajduje się w artykule [ExpressRoute FAQ](expressroute-faqs.md) (Usługa ExpressRoute — często zadawane pytania).

* **Nieograniczona ilość danych**. Obwód usługi ExpressRoute jest obciążany opłatą co miesiąc. Cały transfer danych przychodzących i wychodzących jest bezpłatny. 
* **Mierzone dane**. Obwód usługi ExpressRoute jest obciążany opłatą co miesiąc. Cały transfer danych przychodzących jest bezpłatny. Transfer danych wychodzących jest obciążany opłatą za GB transferu danych. Stawki transferu danych różnią się zależnie od regionu.
* **Dodatek ExpressRoute Premium**. ExpressRoute Premium jest dodatkiem w obwodzie usługi ExpressRoute. Dodatek ExpressRoute Premium zapewnia następujące możliwości: 
  * Zwiększone limity tras dla publicznej i prywatnej komunikacji równorzędnej Azure z 4000 do 10 000 tras.
  * Globalna łączność dla usług. Obwód usługi ExpressRoute utworzony w dowolnym regionie (z wyłączeniem chmur krajowych) będzie miał dostęp do zasobów we wszystkich innych regionach na świecie. Na przykład dostęp do sieci wirtualnej utworzonej w Europie Zachodniej można uzyskać za pośrednictwem obwodu usługi ExpressRoute zainicjowanego w Dolinie Krzemowej.
  * Zwiększona liczba łączy sieci wirtualnej na obwód usługi ExpressRoute z 10 do większego limitu, w zależności od przepustowości obwodu.

## <a name="faq"></a>Często zadawane pytania

Często zadawane pytania dotyczące usługi ExpressRoute można znaleźć na stronie [Usługa ExpressRoute — często zadawane pytania](expressroute-faqs.md).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [modelach połączeń usługi ExpressRoute](expressroute-connectivity-models.md).
* Więcej informacji na temat połączeń ExpressRoute i domen routingu. Zobacz artykuł [ExpressRoute circuits and routing domains](expressroute-circuit-peerings.md) (Obwody i domeny routingu usługi ExpressRoute).
* Znajdź dostawcę usługi. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute).
* Upewnij się, że zostały spełnione wszystkie wymagania wstępne. Zobacz artykuł [ExpressRoute prerequisites](expressroute-prerequisites.md) (Wymagania wstępne usługi ExpressRoute).
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md), [NAT](expressroute-nat.md) i [QoS](expressroute-qos.md).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Tworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
  * [Konfigurowanie komunikacji równorzędnej na potrzeby obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
  * [Łączenie sieci wirtualnej z obwodem usługi ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
* Poznaj inne kluczowe [możliwości sieciowe](../networking/networking-overview.md) platformy Azure.
