---
title: Wprowadzenie do usługi Azure Service Fabric sieci | Dokumentacja firmy Microsoft
description: Więcej informacji na temat sieci, bramy i routing ruchu inteligentne w usługi Service Fabric siatki.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: b0e1047c5bbd7d8caaf2afd8b002be1c46837852
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60811034"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Wprowadzenie do sieci w aplikacji usługi Service Fabric siatki
W tym artykule opisano różnego rodzaju moduły równoważenia obciążenia, jak połączyć sieć bramy za pomocą aplikacji do innych sieci i jak ruch jest przekierowywany między usługami w swoich aplikacjach.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Warstwy 4 równoważenia obciążenia warstwy 7 programu vs
Równoważenie obciążenia mogą być wykonywane w różnych warstwach w [OSI model](https://en.wikipedia.org/wiki/OSI_model) sieci, często w warstwie 4 (L4) i warstwy 7 (L7).  Zazwyczaj istnieją dwa typy usługi równoważenia obciążenia:

- Moduł równoważenia obciążenia P4 działa w warstwie transportowej sieci obsługuje dostarczanie pakietów nie w odniesieniu do zawartości pakietów. Tylko nagłówki pakietów są kontrolowane przez moduły równoważenia obciążenia, dzięki czemu równoważenia kryteriów jest ograniczony do adresów IP i portów. Na przykład klient nawiązuje połączenie TCP do modułu równoważenia obciążenia. Moduł równoważenia obciążenia przerywa połączenie (według odpowiada bezpośrednio na SYN), wybiera wewnętrznej bazy danych i tworzy nowe połączenie TCP do wewnętrznej bazy danych (wysyła nowe SYN). Moduł równoważenia obciążenia P4 zazwyczaj działa tylko na poziomie połączenia P4 TCP/UDP lub sesji. Dlatego Równoważenie obciążenia przekierowuje bajtów wokół i upewnia się, bajty z tej samej sesji likwidacji w tej samej wewnętrznej bazy danych. Moduł równoważenia obciążenia P4 nie rozpoznaje wszelkie szczegóły aplikacji bajtów, które jest przenoszona. Bajtów może być dowolnego protokołu aplikacji.

- Moduł równoważenia obciążenia L7 działa w warstwie aplikacji, która zajmuje się zawartość każdego pakietu. Sprawdza ono zawartość pakietu, ponieważ sam protokołów, takich jak HTTP, HTTPS i WebSockets. Moduł równoważenia obciążenia daje możliwość wykonywania zaawansowanego routingu. Na przykład klient nawiązuje połączenie TCP protokołu HTTP/2 do modułu równoważenia obciążenia. Moduł równoważenia obciążenia, która jest następnie przechodzi nawiązać dwa połączenia wewnętrznej bazy danych. Gdy klient wysyła dwóch strumieni HTTP/2 do modułu równoważenia obciążenia, strumienia, jeden są wysyłane do wewnętrznej bazy danych jedną i strumienia dwa są wysyłane do zaplecza dwa. W związku z tym nawet Multipleksowanie klientów, którzy mają bardzo różne żądania obciążenia będzie być równoważone wydajnie w zaplecza. 

## <a name="networks-and-gateways"></a>Sieci i bramy
W [Model zasobów sieci szkieletowej usług](service-fabric-mesh-service-fabric-resources.md), zasobu sieciowego jest zasobem indywidualnie do wdrożenia, niezależnie od zasób aplikacji lub usługi, który może odwoływać się do niego jako ich zależności. Służy do tworzenia sieci dla aplikacji, która jest otwarta dla Internetu. Wiele usług z różnych aplikacji może być częścią tej samej sieci. Tej sieci prywatnej jest tworzony i zarządzany przez usługę Service Fabric i nie jest siecią wirtualną platformy Azure (VNET). Aplikacje mogą być dynamicznie dodawane i usuwane z zasobu sieciowego, który pozwala włączać i wyłączać połączenie między SIECIAMI. 

Brama jest używane do zestawiania dwie sieci. Wdraża zasobu bramy [proxy usługa Envoy](https://www.envoyproxy.io/) zapewniający P4 dla wszystkich protokołów i L7 routingu dla zaawansowanych routing aplikacji protokołu HTTP (S). Brama kieruje ruchem w sieci z poziomu zewnętrznej sieci i określa, które usługi na potrzeby kierowania ruchu do.  Zewnętrznej sieci może być siecią open (zasadniczo publicznej sieci internet) lub siecią wirtualną platformy Azure, która umożliwia łączenie się z innymi aplikacjami platformy Azure i zasobów. 

![Sieci i bramy][Image1]

Po utworzeniu zasobu sieciowego przy użyciu `ingressConfig`, publiczny adres IP jest przypisany do zasobu sieciowego. Publiczny adres IP zostaną powiązane z okresem istnienia zasobu sieciowego.

Po utworzeniu aplikacji siatki go odwoływać się istniejącego zasobu sieciowego. Można dodawać nowych portów publicznego lub istniejących portów mogą być usunięte z konfiguracji transferu danych przychodzących. Usuwania zasobu sieciowego zakończy się niepowodzeniem, jeśli zasób aplikacji jest do niego odwoływać. Aplikacja zostanie usunięta, usunięcie zasobu sieciowego.

## <a name="next-steps"></a>Kolejne kroki 
Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, zapoznaj się z omówieniem:
- [Omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png