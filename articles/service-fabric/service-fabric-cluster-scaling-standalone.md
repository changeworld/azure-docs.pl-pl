---
title: Skalowanie klastra autonomicznego na platformie Azure Service Fabric | Microsoft Docs
description: Dowiedz się więcej o skalowaniu Service Fabric klastrów autonomicznych w programie lub w górę i w dół.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
ms.assetid: 5441e7e0-d842-4398-b060-8c9d34b07c48
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/13/2018
ms.author: dekapur
ms.openlocfilehash: eedf80ec82a748f5da8e51aed8b4d403dffe4169
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/29/2019
ms.locfileid: "68599869"
---
# <a name="scaling-service-fabric-standalone-clusters"></a>Skalowanie Service Fabric klastrów autonomicznych
Klaster Service Fabric jest połączonym z siecią zestawem maszyn wirtualnych lub fizycznych, w którym są wdrażane i zarządzane mikrousługi. Maszyna lub maszyna wirtualna będąca częścią klastra nazywa się węzłem. Klastry mogą zawierać potencjalnie tysiące węzłów. Po utworzeniu klastra Service Fabric można skalować klaster w poziomie (zmienić liczbę węzłów) lub w pionie (zmienić zasoby węzłów).  Klaster można skalować w dowolnym momencie, nawet w przypadku uruchamiania obciążeń w klastrze.  W miarę skalowania klastra aplikacje są automatycznie skalowane.

Dlaczego warto skalować klaster? Wymagania aplikacji zmieniają się w miarę upływu czasu.  Może być konieczne zwiększenie zasobów klastra w celu spełnienia zwiększonych obciążeń aplikacji lub ruchu sieciowego albo zmniejszenie zasobów klastra po spadku popytu.

## <a name="scaling-in-and-out-or-horizontal-scaling"></a>Skalowanie do wewnątrz i na zewnątrz lub skalowanie w poziomie
Zmienia liczbę węzłów w klastrze.  Po dołączeniu nowych węzłów do klastra [Menedżer zasobów](service-fabric-cluster-resource-manager-introduction.md) przenosi do nich usługi, co zmniejsza obciążenie istniejących węzłów.  Możesz również zmniejszyć liczbę węzłów, jeśli zasoby klastra nie są używane efektywnie.  Gdy węzły opuszczają klaster, usługi przechodzą z tych węzłów i obciążenia są zwiększane w pozostałych węzłach.  Zmniejszenie liczby węzłów w klastrze działającym na platformie Azure może zaoszczędzić pieniądze, ponieważ płacisz za liczbę używanych maszyn wirtualnych, a nie obciążenia na tych maszynach wirtualnych.  

- Udogodnienia Nieskończona skala w teorii.  Jeśli aplikacja jest przeznaczona do skalowalności, możesz włączyć nieograniczony wzrost, dodając więcej węzłów.  Narzędzia w środowiskach w chmurze ułatwiają dodawanie i usuwanie węzłów, dzięki czemu można łatwo dostosowywać pojemność i płacisz tylko za zasoby, których używasz.  
- Wada Aplikacje muszą być [zaprojektowane w celu](service-fabric-concepts-scalability.md)zapewnienia skalowalności.  Bazy danych i trwałość aplikacji mogą wymagać dodatkowej pracy w architekturze.  [Niezawodne kolekcje](service-fabric-reliable-services-reliable-collections.md) w Service Fabricych usługach stanowych, jednak znacznie ułatwiają skalowanie danych aplikacji.

Klastry autonomiczne umożliwiają wdrożenie klastra Service Fabric lokalnie lub w wybranym dostawcy chmury.  Typy węzłów składają się z maszyn fizycznych lub maszyn wirtualnych, w zależności od wdrożenia. W porównaniu z klastrami działającymi na platformie Azure proces skalowania klastra autonomicznego jest nieco bardziej zaliczane.  Należy ręcznie zmienić liczbę węzłów w klastrze, a następnie uruchomić uaktualnienie konfiguracji klastra.

Usunięcie węzłów może inicjować wiele uaktualnień. Niektóre węzły są oznaczone `IsSeedNode=”true”` tagami i mogą być identyfikowane przez zapytanie do manifestu klastra przy użyciu polecenia [Get-ServiceFabricClusterManifest](/powershell/module/servicefabric/get-servicefabricclustermanifest). Usunięcie takich węzłów może potrwać dłużej niż inne, ponieważ węzły inicjatora będą musiały zostać przeniesione w taki sposób. Klaster musi obsługiwać co najmniej trzy węzły podstawowego typu węzła.

> [!WARNING]
> Zaleca się, aby nie obniżyć liczby węzłów poniżej [rozmiaru klastra warstwy niezawodności](service-fabric-cluster-capacity.md#the-reliability-characteristics-of-the-cluster) dla klastra. Będzie to zakłócać możliwość replikowania Service Fabric usług systemowych w ramach klastra i zostanie ona utrwalona lub prawdopodobnie zniszczy klaster.
>

Podczas skalowania klastra autonomicznego należy pamiętać o następujących kwestiach:
- Zastąpienie węzłów podstawowych powinno odbywać się po jednym węźle, zamiast usuwać, a następnie dodawać w partiach.
- Przed usunięciem typu węzła Sprawdź, czy istnieją węzły odwołujące się do typu węzła. Usuń te węzły przed usunięciem odpowiedniego typu węzła. Po usunięciu odpowiednich węzłów można usunąć NodeType z konfiguracji klastra i rozpocząć uaktualnianie konfiguracji przy użyciu polecenia [Start-ServiceFabricClusterConfigurationUpgrade](/powershell/module/servicefabric/start-servicefabricclusterconfigurationupgrade).

Aby uzyskać więcej informacji, zobacz [skalowanie klastra autonomicznego](service-fabric-cluster-windows-server-add-remove-nodes.md).

## <a name="scaling-up-and-down-or-vertical-scaling"></a>Skalowanie w górę i w dół lub skalowanie w pionie 
Zmienia zasoby (procesor CPU, pamięć lub magazyn) węzłów w klastrze.
- Udogodnienia Architektura oprogramowania i aplikacji pozostaje taka sama.
- Wada Skończone skalowanie, ponieważ istnieje limit ilości zasobów, które można zwiększyć w poszczególnych węzłach. Przestój, ponieważ należy przełączyć maszyny fizyczne lub wirtualne w tryb offline, aby można było dodawać lub usuwać zasoby.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
* [Skalowanie klastra platformy Azure w poziomie lub na zewnątrz](service-fabric-tutorial-scale-cluster.md).
* [Programowo Skaluj klaster platformy Azure](service-fabric-cluster-programmatic-scaling.md) przy użyciu zestawu Azure COMPUTE SDK.
* [Skalowanie klastra autonomicznego w poziomie lub na zewnątrz](service-fabric-cluster-windows-server-add-remove-nodes.md).

