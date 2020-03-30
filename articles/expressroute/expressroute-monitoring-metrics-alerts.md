---
title: 'Usługa Azure ExpressRoute: monitorowanie, metryki i alerty'
description: Ta strona zawiera informacje o monitorowaniu usługi ExpressRoute
services: expressroute
author: mialdrid
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.openlocfilehash: 268a7e7c94285d3c4fdcb0c5fb91b685c09b58c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436912"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorowanie, metryki i alerty usługi ExpressRoute

Ten artykuł ułatwia zrozumienie monitorowania usługi ExpressRoute, metryk i alertów przy użyciu usługi Azure Monitor. Usługa Azure Monitor to jeden sklep kompleksowej obsługi dla wszystkich metryk, alertów, dzienników diagnostycznych na całej platformie Azure.
 
>[!NOTE]
>Nie zaleca się **używania metryk klasycznych.**
>

## <a name="expressroute-metrics"></a>Metryki usługi ExpressRoute

Aby wyświetlić **metryki,** przejdź do strony *Monitor platformy Azure* i kliknij pozycję *Metryki*. Aby wyświetlić metryki **usługi ExpressRoute,** filtruj według *obwodów usługi ExpressRoute*typu zasobu . Aby wyświetlić metryki **globalnego zasięgu,** filtruj według *obwodów usługi ExpressRoute* typu zasobu i wybierz zasób obwodu usługi ExpressRoute z włączonym zasięgiem globalnym. Aby wyświetlić metryki **Direct usługi ExpressRoute,** przefiltruj typ zasobu według *portów usługi ExpressRoute*. 

Po wybraniu metryki zostanie zastosowana domyślna agregacja. Opcjonalnie można zastosować dzielenie, które pokaże metrykę o różnych wymiarach.

### <a name="available-metrics"></a>Dostępne metryki
|**Metryka**|**Kategoria**|**Wymiary(-a)**|**Funkcje(-a)**|
| --- | --- | --- | --- |
|Dostępność ARP|Dostępność|<ui><li>Peer (podstawowy/pomocniczy router usługi ExpressRoute)</ui></li><ui><li> Typ komunikacji równorzędnej (prywatny/publiczny/Microsoft)</ui></li>|ExpressRoute|
|Dostępność BGP|Dostępność|<ui><li> Peer (podstawowy/pomocniczy router usługi ExpressRoute)</ui></li><ui><li> Typ komunikacji równorzędnej</ui></li>|ExpressRoute|
|BitsInPerSekund|Ruch|<ui><li> Typ komunikacji równorzędnej (usługa ExpressRoute)</ui></li><ui><li>Łącze (ExpressRoute Direct)</ui></li>| <li> ExpressRoute</li><li>Usługa ExpressRoute Direct|
|BitsOutPerSekunda|Ruch| <ui><li>Typ komunikacji równorzędnej (usługa ExpressRoute)</ui></li><ui><li> Łącze (ExpressRoute Direct) | <ui><li>Expressroute<ui><li>Usługa ExpressRoute Direct</ui></li> |
|GlobalReachBitsInPerSecond|Ruch|<ui><li>Klucz Skey obwodu równorzędnego (klucz usługi)</ui></li>|Globalny zasięg|
|GlobalReachBitsOutPerSekunda|Ruch|<ui><li>Klucz Skey obwodu równorzędnego (klucz usługi)</ui></li>|Globalny zasięg|
|Stan administracyjny|Łączność fizyczna|Link|Usługa ExpressRoute Direct|
|LiniaProtocol|Łączność fizyczna|Link|Usługa ExpressRoute Direct|
|RxLightPoziom|Łączność fizyczna|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|Usługa ExpressRoute Direct|
|Poziom TxLight|Łączność fizyczna|<ui><li>Link</ui></li><ui><li>Lane</ui></li>|Usługa ExpressRoute Direct|
>[!NOTE]
>Za pomocą *GlobalGlobalReachBitsInPerSecond* i *GlobalGlobalReachBitsOutPerSecond* będzie widoczny tylko wtedy, gdy zostanie nawiązane co najmniej jedno połączenie globalnego zasięgu.
>

## <a name="circuits-metrics"></a>Metryki obwodów

### <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bity w i na zewnątrz - metryki we wszystkich komunikacji równorzędnej

Metryki można wyświetlać we wszystkich komunikacji równorzędnej w danym obwodzie usługi ExpressRoute.

![metryki obwodu](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

### <a name="bits-in-and-out---metrics-per-peering"></a>Bity w i na zewnątrz - metryki na komunikację równorzędną

Można wyświetlać metryki dla prywatnych, publicznych i komunikacji równorzędnej firmy Microsoft w bitach/sekundę.

![metryki na komunikację równorzędnej](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

### <a name="bgp-availability---split-by-peer"></a>Dostępność BGP — podział według elementu równorzędnego  

Dostępność protokołu BGP w czasie zbliżonym do rzeczywistego można wyświetlać w komunikacji równorzędnej i równorzędnej (routery podstawowe i pomocnicze usługi ExpressRoute). Ten pulpit nawigacyjny pokazuje podstawową sesję BGP do prywatnej komunikacji równorzędnej i drugą sesję BGP w dół dla prywatnej komunikacji równorzędnej. 

![Dostępność protokołu BGP na element równorzędny](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

### <a name="arp-availability---split-by-peering"></a>Dostępność ARP — dzielenie według komunikacji równorzędnej  

Dostępność [usługi ARP w](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) czasie rzeczywistym można wyświetlać w trybie zbliżonym do dostępności WER w trybie komunikacji równorzędnej i równorzędnej (routery podstawowe i pomocnicze usługi ExpressRoute). Ten pulpit nawigacyjny pokazuje private peering sesji ARP w górę w obu elementów równorzędnych, ale zakończyć w dół dla komunikacji równorzędnej firmy Microsoft między komunikacją równorzędną. Domyślna agregacja (średnia) została wykorzystana w obu elementach równorzędnych.  

![Dostępność ARP na element równorzędny](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-direct-metrics"></a>Bezpośrednie dane usługi ExpressRoute

### <a name="admin-state---split-by-link"></a>Stan administratora — podziel według linku
Stan administratora dla każdego łącza pary portów Direct usługi ExpressRoute.

![er bezpośredni stan administratora](./media/expressroute-monitoring-metrics-alerts/adminstate-per-link.jpg)

### <a name="bits-in-per-second---split-by-link"></a>Bity w sekundzie — dzielenie według łącza
Bity w sekundzie można wyświetlać w obu łączach pary portów Direct usługi ExpressRoute. 

![er bezpośrednie bity w na sekundę](./media/expressroute-monitoring-metrics-alerts/bits-in-per-second-per-link.jpg)

### <a name="bits-out-per-second---split-by-link"></a>Bity na sekundę - Podziel według łącza
Bity na sekundę można również wyświetlać w obu łączach pary portów Direct usługi ExpressRoute Direct. 

![er bezpośrednie bity na sekundę](./media/expressroute-monitoring-metrics-alerts/bits-out-per-second-per-link.jpg)

### <a name="line-protocol---split-by-link"></a>Protokół liniowy — dzielenie według łącza
Protokół wiersza można wyświetlić na każdym łączu pary portów Direct usługi ExpressRoute.

![protokół linii bezpośredniej](./media/expressroute-monitoring-metrics-alerts/line-protocol-per-link.jpg)

### <a name="rx-light-level---split-by-link"></a>Rx Light Level - Podziel przez link
Dla każdego portu można wyświetlić poziom światła Rx (poziom światła **odbierany**przez port Direct usługi ExpressRoute). Zdrowy poziom światła Rx zazwyczaj mieści się w zakresie od -10 do 0 dBm

![er linia bezpośrednia Rx Poziom światła](./media/expressroute-monitoring-metrics-alerts/rxlight-level-per-link.jpg)

### <a name="tx-light-level---split-by-link"></a>Poziom światła Tx - Podziel przez link
Dla każdego portu można wyświetlić poziom oświetlenia Tx (poziom światła **przesyłany przez**port Direct usługi ExpressRoute) dla każdego portu. Zdrowy poziom światła Tx zazwyczaj mieści się w zakresie od -10 do 0 dBm

![er linia bezpośrednia Rx Poziom światła](./media/expressroute-monitoring-metrics-alerts/txlight-level-per-link.jpg)

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Połączenia bramy usługi ExpressRoute w bitach/sekundach

![połączenia bramy](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg )

## <a name="alerts-for-expressroute-gateway-connections"></a>Alerty dotyczące połączeń bramy usługi ExpressRoute

1. Aby skonfigurować alerty, przejdź do **usługi Azure Monitor**, a następnie kliknij pozycję **Alerty**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Kliknij **pozycję +Wybierz miejsce docelowe** i wybierz zasób połączenia bramy usługi ExpressRoute.

   ![Docelowego]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Zdefiniuj szczegóły alertu.

   ![grupa akcji](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Zdefiniuj i dodaj grupę akcji.

   ![dodawanie grupy akcji](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alerty oparte na poszczególnych elementach komunikacji równorzędnej

 ![Co](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurowanie alertów dla dzienników aktywności w obwodach

W **kryteriach alertu**można wybrać **dziennik aktywności** dla typu sygnału i wybrać sygnał.

  ![Innego](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Następne kroki

Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
  * [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
