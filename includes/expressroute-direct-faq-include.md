---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: jaredr80
ms.service: expressroute
ms.topic: include
ms.date: 10/07/2019
ms.author: jaredro
ms.custom: include file
ms.openlocfilehash: 9c53aac47e965a6eba681008d6b6476142190ef8
ms.sourcegitcommit: be344deef6b37661e2c496f75a6cf14f805d7381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2019
ms.locfileid: "72023087"
---
### <a name="what-is-expressroute-direct"></a>Co to jest usługa ExpressRoute Direct?

Usługa ExpressRoute Direct daje klientom możliwość łączenia się bezpośrednio z globalną siecią firmy Microsoft w skomunikowanych równorzędnie lokalizacjach strategicznie rozmieszczonych na całym świecie. Usługa ExpressRoute Direct oferuje łączność z dwoma procesorami (100 lub 10 GB/s), która obsługuje aktywne/aktywne połączenia na dużą skalę. 

### <a name="how-do-customers-connect-to-expressroute-direct"></a>Jak klienci nawiązują połączenie z usługą ExpressRoute Direct? 

Klienci będą musieli współpracować z lokalnymi przewoźnikami i dostawcami wspólnej lokalizacji w celu uzyskania łączności z routerami ExpressRoute, aby korzystać z funkcji ExpressRoute Direct.

### <a name="what-locations-currently-support-expressroute-direct"></a>Które lokalizacje są obecnie obsługiwane przez ExpressRoute bezpośrednio? 

Dostępne porty będą dynamiczne i będą dostępne dla programu PowerShell w celu wyświetlenia pojemności. Lokalizacje obejmują i *podlegają zmianom w zależności od dostępności*:

* Amsterdam
* Amsterdam2
* Auckland 
* Chicago
* Dallas
* Dublin
* Hongkong SAR
* Londyn
* Londyn2
* Melbourne
* New York City
* Perth
* Antonio San
* Seattle
* Seul
* Dolina Krzemowa
* Singapur2 
* Sydney
* Tajpej
* Tokio
* Toronto
* Waszyngton
* Waszyngton2

### <a name="what-is-the-sla-for-expressroute-direct"></a>Co to jest umowa SLA dotycząca usługi ExpressRoute Direct?

ExpressRoute Direct będzie korzystać z tej samej [klasy korporacyjnej ExpressRoute](https://azure.microsoft.com/support/legal/sla/expressroute/v1_3/). 

### <a name="what-scenarios-should-customers-consider-with-expressroute-direct"></a>Jakie scenariusze należy wziąć pod uwagę w przypadku ExpressRoute Direct?  

ExpressRoute Direct zapewnia klientom bezpośrednie pary portów 100 lub 10 GB/s do szkieletu globalnego firmy Microsoft. Scenariusze, które pozwolą klientom z największą korzyścią, obejmują: ogromne pozyskiwanie danych, izolację fizyczną dla rynków regulowanych i dedykowaną pojemność dla scenariusza z serii, takich jak renderowanie. 

### <a name="what-is-the-billing-model-for-expressroute-direct"></a>Jaki jest model rozliczeń dla usługi ExpressRoute Direct? 

Dla pary portów zostanie naliczona wartość ExpressRoute Direct. Standardowe obwody będą dołączane bez dodatkowych godzin, a warstwa Premium będzie miała niewielki dodatkowy koszt. Opłaty za ruch wychodzący są naliczane na podstawie obwodu na podstawie strefy lokalizacji komunikacji równorzędnej.

### <a name="when-does-billing-start-for-the-expressroute-direct-port-pairs"></a>Kiedy rozpoczyna się rozliczenia dla par portów ExpressRoute Direct?

Pary portów bezpośredniego ExpressRoute są rozliczane 45 dni do utworzenia zasobu bezpośredniego ExpressRoute lub po włączeniu 1 lub obu tych linków, w zależności od tego, co nastąpi wcześniej. 45-dniowy okres prolongaty zostanie udzielony, aby umożliwić klientom ukończenie procesu połączenia krzyżowego z dostawcą wspólnej lokalizacji.
