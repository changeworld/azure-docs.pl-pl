---
title: Określ ustawienia metryki i umieszczanie w usłudze Azure Service Fabric | Dokumentacja firmy Microsoft
description: Dowiedz się, jak opisano usługę Service Fabric, określając metryki, ograniczeniami dotyczącymi umieszczania i inne zasady umieszczania.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 21fcac62c9335652d0c682a6ac889be82e649464
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60844146"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurowanie ustawień Menedżer zasobów klastra usługi Service Fabric
Menedżer zasobów klastra usługi Service Fabric umożliwia szczegółową kontrolę nad tym reguły rządzące każda osoba, o nazwie usługi. Każda usługa o nazwie można określić zasady jak powinna zostać przydzielona w klastrze. Każda usługa o nazwie można również zdefiniować zestaw metryk, którą chce do raportu, w tym jak ważne są one do tej usługi. Konfigurowanie usług dzieli się na trzy różne zadania:

1. Konfigurowanie ograniczeń umieszczania
2. Konfigurowanie metryk
3. Konfigurowanie zasady umieszczania zaawansowane i innymi regułami (mniej typowe)

## <a name="placement-constraints"></a>Ograniczeniami dotyczącymi umieszczania
Ograniczeniami dotyczącymi umieszczania są używane do kontroli, które węzły w klastrze usługi faktycznie mogą być uruchamiane na. Zazwyczaj określonego nazwane wystąpienie usługi lub wszystkich usług danego typu ograniczone do uruchamiania na określonym typie węzła. Ograniczeniami dotyczącymi umieszczania są rozszerzalne. Można określić dowolny zbiór właściwości, według typu węzła, a następnie wybierz dla nich z ograniczeniami podczas tworzenia usługi. Można również zmienić ograniczeniami dotyczącymi umieszczania usługa jest uruchomiona. Pozwala na reagowanie na zmiany w klastrze lub wymagań usługi. Właściwości danego węzła można również aktualizowane dynamicznie w klastrze. Więcej informacji na temat ograniczeń umieszczania i sposobach ich konfigurowania można znaleźć w [w tym artykule](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metryki
Metryki są zestawu zasobów, których potrzebuje danego nazwaną usługę. Konfiguracja metryki usługi zawiera ilość zasobu każdego stanowych replik lub bezstanowych wystąpienie tej usługi używa domyślnie. Metryki zawierają również wagi, która wskazuje, jak ważne jest równoważenia tej metryki dla danej usługi, w przypadku stosowania kompromisów są niezbędne.

## <a name="advanced-placement-rules"></a>Reguły umieszczania zaawansowane
Istnieją inne typy reguły umieszczania, które są przydatne w mniej typowych scenariuszy. Przykłady to:
- Ograniczenia, które pomagają klastry rozproszone geograficznie
- Niektóre architektury aplikacji

Inne reguły umieszczania są konfigurowane za pośrednictwem korelacji lub zasady.

## <a name="next-steps"></a>Kolejne kroki
- Metryki są, jak Menedżer zasobów usługi Service Fabric klaster zarządza użycia i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryk i sposobach ich konfigurowania, zapoznaj się z [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)
- Koligacja jest jeden tryb, w którym można skonfigurować dla usługi. Nie jest powszechne, ale jeśli potrzebujesz jej możesz dowiedzieć się o nim [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Istnieje wiele reguł umieszczania różnych, które można skonfigurować na usługi w celu obsługi dodatkowych scenariuszy. Możesz dowiedzieć się o tych zasadach rozmieszczania różnych [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Zacznij od początku i [zapoznaj się z wprowadzeniem do usługi Service Fabric Menedżer zasobów klastra](service-fabric-cluster-resource-manager-introduction.md)
- Aby dowiedzieć się o zarządza Menedżer zasobów klastra i równoważy obciążenie w klastrze, zapoznaj się z artykułem na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Menedżer zasobów klastra ma wiele opcji do opisywania klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tego artykułu na [opisujące klaster usługi Service Fabric](service-fabric-cluster-resource-manager-cluster-description.md)
