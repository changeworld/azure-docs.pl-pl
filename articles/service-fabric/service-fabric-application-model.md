---
title: Model aplikacji w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Jak model i dodaniu opisu aplikacji i usług w usłudze Service Fabric.
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 750970233cbcb14d901dbb5fa94f649f6ff8ae6c
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666277"
---
# <a name="model-an-application-in-service-fabric"></a>Modelowanie aplikacji w usłudze Service Fabric
Ten artykuł zawiera omówienie modelu aplikacji usługi Azure Service Fabric oraz sposób definiowania aplikacji i usługi za pomocą plików manifestu.

## <a name="understand-the-application-model"></a>Informacje o modelu aplikacji
Aplikacja to kolekcja usług składowych, które wykonują niektórych funkcji lub funkcje. Usługa wykonuje funkcję kompletne i autonomiczne i można uruchomić i Uruchom niezależnie od innych usług.  Usługa składa się z kodu, konfiguracji i danych. Dla każdej usługi kod zawiera wykonywalne pliki binarne, konfiguracja obejmuje ustawienia usługi, które mogą być ładowane w czasie wykonywania i danych składa się z dowolnych danych statycznych do użycia przez usługę. Każdy składnik w tym modelu aplikacja hierarchiczne może być niezależnie wersjonowana i uaktualniana.

![Model aplikacji usługi Service Fabric][appmodel-diagram]

Typ aplikacji Kategoryzacja aplikacji i składa się z pakietem typów usług. Typ usługi jest kategoryzacji usługi. Kategorii mogą mieć różne ustawienia i konfiguracje, ale najważniejsze funkcje pozostaje bez zmian. Wystąpień usługi są wariantów konfiguracji usługi innego typu usługi.  

Klasy (lub "typy") z aplikacji i usług są opisane za pośrednictwem plików XML (manifestów aplikacji i manifestów usługi).  Manifesty opisują, aplikacji i usług i szablonów, wobec których aplikacje mogą być utworzone z magazynu obrazów klastra.  Manifesty są tu szczegółowo [aplikacji i manifestów usługi](service-fabric-application-and-service-manifests.md). Definicja schematu dla pliku ServiceManifest.xml i ApplicationManifest.xml został zainstalowany przy użyciu zestawu Service Fabric SDK i narzędzi do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Opisano w nim schemat XML [dokumentacja schematu ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

Uruchom kod różne wystąpienia aplikacji jako oddzielne procesy, nawet wtedy, gdy pracujących na tym samym węźle usługi Service Fabric. Ponadto cyklu życia każdego wystąpienia aplikacji mogą być zarządzane (na przykład uaktualnić) niezależnie od siebie. Na poniższym diagramie przedstawiono, jak typy aplikacji składają się z typów usług, które z kolei składają się z kodu, konfiguracji i pakiety danych. Aby uprościć diagram, tylko kod/config/dane są pakuje dla `ServiceType4` są wyświetlane, chociaż każdego typu usług zawierałoby niektórych lub wszystkich tych typów pakietów.

![Typy aplikacji usługi Service Fabric i usługi][cluster-imagestore-apptypes]

Może istnieć co najmniej jedno wystąpienie typu usługi active w klastrze. Na przykład wystąpień usługi stanowej lub replik, osiągnięcia wysokiej niezawodności, replikując stanu między replikami znajduje się w różnych węzłach w klastrze. Replikacja zasadniczo nadmiarowości usługi, które mają być dostępne, nawet w przypadku awarii jednego węzła w klastrze. A [podzielona na partycje usługi](service-fabric-concepts-partitioning.md) dalsze dzieli jego stan (i wzorce dostępu do tego stanu) na węzłach w klastrze.

Na poniższym diagramie przedstawiono relację między aplikacjami i wystąpieniami usługi, partycji i replik.

![Partycje i repliki w ramach usługi][cluster-application-instances]

> [!TIP]
> Układ aplikacji można wyświetlić w klastrze przy użyciu narzędzia Service Fabric Explorer dostępnego pod http://&lt;yourclusteraddress&gt;: 19080/Explorer. Aby uzyskać więcej informacji, zobacz [wizualizowanie klastra przy użyciu narzędzia Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [skalowalności aplikacji](service-fabric-concepts-scalability.md).
- Dowiedz się więcej o usłudze [stanu](service-fabric-concepts-state.md), [partycjonowanie](service-fabric-concepts-partitioning.md), i [dostępności](service-fabric-availability-services.md).
- Przeczytaj, jak aplikacje i usługi są zdefiniowane w [aplikacji i manifestów usługi](service-fabric-application-and-service-manifests.md).
- [Modele hostowania aplikacji](service-fabric-hosting-model.md) opisano relację między replik lub wystąpień wdrożonej usługi, a proces hosta usługi.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


