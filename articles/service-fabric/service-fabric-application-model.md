---
title: Model aplikacji sieci szkieletowej usług Azure | Dokumentacja firmy Microsoft
description: Informacje o modelu i opisano aplikacji i usług w sieci szkieletowej usług.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: ryanwi
ms.openlocfilehash: dd453f393620923041eb8fa07b551f4945f48235
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204638"
---
# <a name="model-an-application-in-service-fabric"></a>Model aplikacji w sieci szkieletowej usług
Ten artykuł zawiera omówienie modelu aplikacji sieci szkieletowej usług Azure i sposobu definiowania aplikacji i usług za pośrednictwem plików manifestu.

## <a name="understand-the-application-model"></a>Zrozumienie model aplikacji
Aplikacja jest kolekcja składników usług, które wykonywać niektórych funkcji lub funkcji. Usługa działa pełny i autonomiczne i można uruchomić i uruchomić niezależnie od innych usług.  Usługa składa się z kodu, konfiguracji i danych. Dla każdej usługi kod składa się z pliku wykonywalnego plików binarnych, konfiguracja obejmuje ustawienia usługi, które mogą zostać załadowane w czasie wykonywania i danych składa się z dowolnego statycznych danych do użycia przez usługę. Każdego składnika w tym modelu hierarchiczne aplikacji może być niezależnie określonej wersji, jak i uaktualnionych.

![Model aplikacji sieci szkieletowej usług][appmodel-diagram]

Typ aplikacji kategoryzacji aplikacji i składa się z pakietem typu usługi. Typ usługi jest kategoryzacji usługi. Klasyfikacja może mieć różne ustawienia i konfiguracje, ale do podstawowych funkcji jest taka sama. Wystąpień usługi są zmian konfiguracji innej usługi typu usługi.  

Klasy (lub "typów") aplikacji i usług opisanych za pomocą plików XML (manifestów aplikacji i manifestów usługi).  Manifesty opisano aplikacji i usług oraz szablonów, które można wdrożyć aplikacji z magazynu obrazu klastra.  Manifesty są tu szczegółowo [aplikacji i manifestów usługi](service-fabric-application-and-service-manifests.md). Definicja schematu dla pliku ServiceManifest.xml i ApplicationManifest.xml jest instalowany z zestawu SDK sieci szkieletowej usług i narzędzi do *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Schemat XML jest udokumentowany w [ServiceFabricServiceModel.xsd schematu dokumentacji](service-fabric-service-model-schema.md).

Kod dla różnych aplikacji wystąpień Uruchom jako osobne procesy nawet wtedy, gdy pracujących na tym samym węźle sieci szkieletowej usług. Ponadto można zarządzać cyklem życia każdego wystąpienia aplikacji (na przykład uaktualnić) niezależnie. Na poniższym diagramie przedstawiono, jak typy aplikacji składają się z typów usług, które z kolei składają się z kodu, konfiguracji i danych. Aby uprościć na diagramie, pakietami tylko/config/danych kodu `ServiceType4` są wyświetlane, że każdy typ usługi to niektórych lub wszystkich tych typów pakietów.

![Typy aplikacji usługi Service Fabric i usługi][cluster-imagestore-apptypes]

Może istnieć co najmniej jedno wystąpienie typu usługi active w klastrze. Na przykład wystąpienie usługi stanowej lub replik, osiągnąć wysoką niezawodnością poprzez replikację stanu między replikami znajdujących się w różnych węzłach w klastrze. Replikacja zasadniczo zapewnia nadmiarowość dla usługi, które mają być dostępne, nawet w przypadku awarii jednego węzła w klastrze. A [podzielona na partycje usługi](service-fabric-concepts-partitioning.md) dalsze dzieli jego stan (i wzorce dostępu do tego stanu) w węzłach w klastrze.

Na poniższym diagramie przedstawiono związek między aplikacjami i wystąpień usług, partycji i replik.

![Partycji i replik w ramach usługi][cluster-application-instances]

> [!TIP]
> Układ aplikacji można wyświetlić w klastrze za pomocą narzędzia Service Fabric Explorer dostępne w http://&lt;yourclusteraddress&gt;: 19080/Explorer. Aby uzyskać więcej informacji, zobacz [wizualizacja klastra za pomocą Eksploratora usługi sieć szkieletowa](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [skalowalność aplikacji](service-fabric-concepts-scalability.md).
- Dowiedz się więcej o usłudze [stanu](service-fabric-concepts-state.md), [partycjonowania](service-fabric-concepts-partitioning.md), i [dostępności](service-fabric-availability-services.md).
- Przeczytaj informacje o sposobie aplikacji i usług są zdefiniowane w [aplikacji i manifestów usługi](service-fabric-application-and-service-manifests.md).
- [Hosting modeli aplikacji](service-fabric-hosting-model.md) opis relacji między repliki (lub wystąpień) wdrożonej usługi i procesu hosta usługi.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


