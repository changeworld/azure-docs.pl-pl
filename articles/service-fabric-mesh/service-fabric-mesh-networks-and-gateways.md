---
title: Wprowadzenie do sieci sieci szkieletowej usług Azure
description: Dowiedz się więcej o sieciach, bramkach i inteligentnym routingu ruchu w sieci szkieletowej usług.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459134"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Wprowadzenie do sieci w aplikacjach sieci szkieletowej usług
W tym artykule opisano różne typy modułów równoważenia obciążenia, sposób łączenia sieci z aplikacjami z aplikacjami oraz sposób kierowania ruchu między usługami w aplikacjach.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Moduły równoważenia obciążenia warstwy 4 a warstwy 7
Równoważenie obciążenia można wykonywać na różnych warstwach [modelu OSI](https://en.wikipedia.org/wiki/OSI_model) dla sieci, często w warstwie 4 (L4) i warstwie 7 (L7).  Zazwyczaj istnieją dwa typy modułów równoważenia obciążenia:

- Moduł równoważenia obciążenia L4 działa w warstwie transportu sieciowego, która zajmuje się dostarczaniem pakietów bez względu na zawartość pakietów. Moduł równoważenia sprawdzane są tylko nagłówki pakietów, więc kryteria równoważenia są ograniczone do adresów IP i portów. Na przykład klient nawiązuje połączenie TCP z modułem równoważenia obciążenia. Moduł równoważenia obciążenia kończy połączenie (odpowiadając bezpośrednio na SYN), wybiera wewnętrznej bazy danych i sprawia, że nowe połączenie TCP do wewnętrznej bazy danych (wysyła nowy SYN). Moduł równoważenia obciążenia L4 zazwyczaj działa tylko na poziomie połączenia lub sesji L4 TCP/UDP. W związku z tym waga obciążenia przekierowuje bajty wokół i upewnia się, że bajty z tej samej sesji zakończyć się w tym samym wewnętrznej bazy danych. Moduł równoważenia obciążenia L4 nie jest świadomy żadnych szczegółów aplikacji bajtów, które jest w ruchu. Bajty mogą być dowolnym protokołem aplikacji.

- Moduł równoważenia obciążenia L7 działa w warstwie aplikacji, która zajmuje się zawartością każdego pakietu. Sprawdza zawartość pakietu, ponieważ rozumie protokoły, takie jak HTTP, HTTPS lub WebSockets. Daje to modułowi równoważenia obciążenia możliwość wykonywania zaawansowanego routingu. Na przykład klient nawiązuje jedno połączenie TCP HTTP/2 z modułem równoważenia obciążenia. Następnie moduł równoważenia obciążenia przechodzi do nawiązywać dwa połączenia wewnętrznej bazy danych. Gdy klient wysyła dwa strumienie HTTP/2 do modułu równoważenia obciążenia, strumień jeden jest wysyłany do wewnętrznej bazy danych jeden i strumień dwa jest wysyłany do wewnętrznej bazy dwa. W związku z tym nawet multipleksowanie klientów, które mają znacznie różne obciążenia żądań będą skutecznie równoważone w wewnętrznej bazy danych. 

## <a name="networks-and-gateways"></a>Sieci i bramy
W [modelu zasobów sieci szkieletowej usług](service-fabric-mesh-service-fabric-resources.md)zasób sieciowy jest indywidualnie wdrażanym zasobem, niezależnie od zasobu aplikacji lub usługi, który może odnosić się do niego jako zależności. Służy do tworzenia sieci dla aplikacji, która jest otwarta dla Internetu. Wiele usług z różnych aplikacji może być częścią tej samej sieci. Ta sieć prywatna jest tworzona i zarządzana przez sieć szkieletową usług i nie jest siecią wirtualną platformy Azure (VNET). Aplikacje mogą być dynamicznie dodawane i usuwane z zasobu sieciowego, aby włączyć i wyłączyć łączność sieci wirtualnej. 

Brama służy do mostkować dwie sieci. Zasób bramy wdraża [serwer proxy Envoy,](https://www.envoyproxy.io/) który zapewnia routing L4 dla dowolnego protokołu i routingu L7 dla zaawansowanego routingu aplikacji HTTP(S). Brama kieruje ruch do sieci z sieci zewnętrznej i określa, do której usługi należy kierować ruch.  Sieć zewnętrzna może być siecią otwartą (zasadniczo publiczny internet) lub siecią wirtualną platformy Azure, co umożliwia łączenie się z innymi aplikacjami i zasobami platformy Azure. 

![Sieć i brama][Image1]

Podczas tworzenia zasobu `ingressConfig`sieciowego za pomocą publicznego adresu IP jest przypisywany do zasobu sieciowego. Publiczny adres IP będzie powiązany z okresem istnienia zasobu sieciowego.

Podczas tworzenia aplikacji mesh należy odwoływać się do istniejącego zasobu sieciowego. Można dodać nowe porty publiczne lub usunąć istniejące porty z konfiguracji transferu danych przychodzących. Usunięcie zasobu sieciowego zakończy się niepowodzeniem, jeśli zasób aplikacji odwołuje się do niego. Po usunięciu aplikacji zasób sieciowy jest usuwany.

## <a name="next-steps"></a>Następne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png