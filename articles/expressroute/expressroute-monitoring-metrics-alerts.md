---
title: Monitorowanie usługi Azure ExpressRoute, metryki i alerty | Dokumentacja firmy Microsoft
description: Ta strona zawiera informacje dotyczące monitorowania usługi ExpressRoute
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: cherylmc
ms.openlocfilehash: 73bacebe6edc5b1d1273a10a0db7397766c45b0c
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46971647"
---
# <a name="expressroute-monitoring-metrics-and-alerts"></a>Monitorowanie metryk i alertów usługi ExpressRoute

 Ten artykuł zawiera informacje o monitorowaniu usługi ExpressRoute, metryki i alerty. Usługa Azure Monitor jest jedno centralne miejsce dla wszystkich metryk, alerty i dzienniki diagnostyczne na platformie Azure.

## <a name="circuit-metrics"></a>Metryki obwodu

Aby przejść do **metryki**, kliknij stronę usługi ExpressRoute dla obwodu, który chcesz monitorować. W obszarze **monitorowanie** może można wyświetlić **metryki**.

![metryki obwodu](./media/expressroute-monitoring-metrics-alerts/ermetricspeering.jpg)

## <a name="metrics-per-peering"></a>Metryki dla komunikacji równorzędnej

Możesz wyświetlić metryki dla usługi prywatnej, publicznej i firmy Microsoft komunikacji równorzędnej w bitach na sekundę.

![metryki dla komunikacji równorzędnej](./media/expressroute-monitoring-metrics-alerts/erpeeringmetrics.jpg) 

## <a name="expressroute-gateway-connections-in-bitsseconds"></a>Połączenia bramy usługi ExpressRoute w bity/s

![połączenia bramy](./media/expressroute-monitoring-metrics-alerts/erconnections.jpg ) 

## <a name="alerts-for-expressroute-gateway-connections"></a>Alerty dla połączeń bramy usługi ExpressRoute

1. Aby skonfigurować alerty, przejdź do **usługi Azure Monitor**, następnie kliknij przycisk **alerty**.

  ![alerts](./media/expressroute-monitoring-metrics-alerts/eralertshowto.jpg)

2. Kliknij przycisk **+ wybierz element docelowy** i wybierz zasób połączenia bramy usługi ExpressRoute.

  ![cel]( ./media/expressroute-monitoring-metrics-alerts/alerthowto2.jpg)
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
* Skonfiguruj połączenie usługi ExpressRoute.
  
  * [Tworzenie i modyfikowanie obwodu](expressroute-howto-circuit-arm.md)
  * [Tworzenie i modyfikowanie konfiguracji komunikacji równorzędnej](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
