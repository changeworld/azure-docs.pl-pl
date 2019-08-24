---
title: Monitorowanie, alerty i metryki — usługa Azure ExpressRoute | Dokumentacja firmy Microsoft
description: Ta strona zawiera informacje dotyczące monitorowania usługi ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 92ec03e20fb6e681a0afd14048449ad004ebca0c
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69991479"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorowanie, metryki i alerty usługi ExpressRoute

Ten artykuł zawiera omówienie usługi ExpressRoute, monitorowanie, alerty i metryki za pomocą usługi Azure Monitor. Usługa Azure Monitor jest jedno centralne miejsce dla wszystkich metryk, alerty i dzienniki diagnostyczne na platformie Azure.
 
>[!NOTE]
>Za pomocą **klasycznego metryki** nie jest zalecane.
>

## <a name="circuit-metrics"></a>Metryki obwodu

Aby przejść do **metryki**, kliknij stronę usługi ExpressRoute dla obwodu, który chcesz monitorować. W obszarze **monitorowanie**, można wyświetlić **metryki**. Wybierz spośród metryk wymienionych poniżej. Zostanie zastosowana domyślna agregacja. Opcjonalnie można zastosować podział, który będzie wyświetlać metryki z różnymi wymiarami.

### <a name="metrics-available"></a>Dostępne metryki: 
* **Dostępność** 
    * Dostępność protokołu ARP
      * Dostępne Wymiary:
        * Węzeł równorzędny (podstawowy/pomocniczy router ExpressRoute)
        * Typ komunikacji równorzędnej (prywatny/publiczny/Microsoft)
    * Dostępność protokołu BGP
      * Dostępne Wymiary:
        * Węzeł równorzędny (podstawowy/pomocniczy router ExpressRoute)
        * Typ komunikacji równorzędnej (prywatny/publiczny/Microsoft)
* **Komunikacyjne**
    * BitsInPerSecond
      * Dostępne Wymiary:
        * Typ komunikacji równorzędnej (prywatny/publiczny/Microsoft)
    * BitsOutPerSecond
      * Dostępne Wymiary:
        * Typ komunikacji równorzędnej (prywatny/publiczny/Microsoft)
    * GlobalReachBitsInPerSecond
      * Dostępne Wymiary:
        * Skey obwodu równorzędnego (klucz usługi)
    * GlobalReachBitsOutPerSecond
      * Dostępne Wymiary:
        * Skey obwodu równorzędnego (klucz usługi)

>[!NOTE]
>Użycie *GlobalGlobalReachBitsInPerSecond* i *GlobalGlobalReachBitsOutPerSecond* będzie widoczne tylko wtedy, gdy zostanie nawiązane co najmniej jedno połączenie Global REACH.
>

## <a name="bits-in-and-out---metrics-across-all-peerings"></a>Bity w i out-Metrics dla wszystkich komunikacji równorzędnych

Możesz wyświetlić metryki dla wszystkich komunikacji równorzędnych w danym obwodzie ExpressRoute.

![Metryki obwodu](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="bits-in-and-out---metrics-per-peering"></a>Bity w i out — metryki na komunikację równorzędną

Możesz wyświetlić metryki dla usługi prywatnej, publicznej i firmy Microsoft komunikacji równorzędnej w bitach na sekundę.

![Metryki dla komunikacji równorzędnej](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="bgp-availability---split-by-peer"></a>Dostępność protokołu BGP — dzielenie przez element równorzędny  

Możesz przeglądać w czasie rzeczywistym dostępność protokołu BGP w przypadku komunikacji równorzędnej i elementów równorzędnych (podstawowych i pomocniczych routerów ExpressRoute). Ten pulpit nawigacyjny pokazuje podstawową sesję protokołu BGP dla prywatnej komunikacji równorzędnej, a druga sesja BGP dla prywatnej komunikacji równorzędnej. 

![Dostępność protokołu BGP na element równorzędny](./media/expressroute-monitoring-metrics-alerts/erBgpAvailabilityMetrics.jpg) 

## <a name="arp-availability---split-by-peering"></a>Dostępność protokołu ARP — dzielenie według komunikacji równorzędnej  

Możesz przeglądać w czasie rzeczywistym dostępność [protokołu ARP](https://docs.microsoft.com/azure/expressroute/expressroute-troubleshooting-arp-resource-manager) między komunikacjami równorzędnymi i równorzędnymi (podstawowymi i pomocniczymi routerami ExpressRoute). Ten pulpit nawigacyjny pokazuje prywatną komunikację równorzędną ARP w ramach obu elementów równorzędnych, ale kończy się w przypadku komunikacji równorzędnej firmy Microsoft między różnymi elementami równorzędnymi. Agregacja domyślna (średnia) została wykorzystana w obu elementach równorzędnych.  

![Dostępność protokołu ARP na element równorzędny](./media/expressroute-monitoring-metrics-alerts/erArpAvailabilityMetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Połączenia bramy usługi ExpressRoute w bity/s

![połączenia bramy](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Alerty dla połączeń bramy usługi ExpressRoute

1. Aby skonfigurować alerty, przejdź do **usługi Azure Monitor**, następnie kliknij przycisk **alerty**.

   ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Kliknij przycisk **+ wybierz element docelowy** i wybierz zasób połączenia bramy usługi ExpressRoute.

   ![Docelowy]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
3. Zdefiniuj szczegóły alertu.

   ![grupy akcji](./media/expressroute-monitoring-metrics-alerts/alerthowto3.jpg)

4. Definiowanie i Dodaj grupę akcji.

   ![Dodaj grupę akcji](./media/expressroute-monitoring-metrics-alerts/actiongroup.png)

## <a name="alerts-based-on-each-peering"></a>Alerty oparte na każdego wystąpienia komunikacji równorzędnej

 ![Co to](./media/expressroute-monitoring-metrics-alerts/basedpeering.jpg)

## <a name="configure-alerts-for-activity-logs-on-circuits"></a>Konfigurowanie alertów dotyczących dzienników aktywności dla obwodów

W **kryteriów alertu**, możesz wybrać **dziennika aktywności** typu sygnału i wybierz sygnał.

  ![inny](./media/expressroute-monitoring-metrics-alerts/alertshowto6activitylog.jpg)
  
## <a name="next-steps"></a>Kolejne kroki

Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
  * [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
