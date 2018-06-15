---
title: Określ ustawienia metryki i umieszczanie w Azure mikrousług | Dokumentacja firmy Microsoft
description: Opisujące usługi Service Fabric, określając metryki, ograniczenia umieszczania i inne zasady umieszczania.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 16e135c1-a00a-4c6f-9302-6651a090571a
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 16e19a02bf12e30e81eb1743fc637bce435df914
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210583"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurowanie ustawień Menedżera zasobów klastra dla usługi sieci szkieletowej usług
Menedżer zasobów klastra sieci szkieletowej usług umożliwia szczegółową kontrolę nad zasady każdego użytkownika, nazwę usługi. Każda usługa o nazwie można określić zasady jak powinna zostać przydzielona w klastrze. Każda usługa o nazwie również zdefiniować zestaw metryki, którą chce do raportu, w tym, jak ważne są do tej usługi. Konfigurowanie usług dzieli na trzy różne zadania:

1. Konfigurowanie ograniczeń umieszczania
2. Konfigurowanie metryki
3. Konfigurowanie zasad umieszczania zaawansowane i innymi regułami (mniej typowe)

## <a name="placement-constraints"></a>Ograniczenia dotyczące umieszczania
Ograniczenia dotyczące umieszczania są używane do węzłów w klastrze usługi faktycznie można uruchamiać na sterowanie. Zwykle określonego nazwane wystąpienie usługi lub wszystkich usług danego typu ograniczone do uruchamiania na określony typ węzła. Ograniczenia dotyczące umieszczania są rozszerzalne. Można określić dowolny zbiór właściwości dla węzła typu, a następnie wybierz dla nich z ograniczeniami podczas tworzenia usługi. Można również zmienić ograniczenia umieszczania usług jest uruchomiona. Dzięki temu można odpowiadanie na zmiany w klastrze lub wymagania dotyczące usługi. Właściwości danego węzła można również aktualizowany dynamicznie w klastrze. Więcej informacji na temat ograniczeń umieszczania i sposobach ich konfigurowania znajduje się w [w tym artykule](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metryki
Metryki są zestaw zasobów, które wymaga dana usługa o nazwie. Konfiguracja metryki usługi obejmuje ilość tego zasobu każdej repliki stanowego lub bezstanowego wystąpienie tej usługi wykorzystuje domyślnie. Metryki obejmują również wagi, która wskazuje, jak ważna równoważenia tego metryka jest do tej usługi, w przypadku wady i zalety są niezbędne.

## <a name="advanced-placement-rules"></a>Reguły umieszczania zaawansowane
Istnieją inne typy zasad umieszczania, które są przydatne w mniej typowych scenariuszy. Przykłady to:
- Ograniczenia, które pomagają w geograficznie rozproszonej klastrów
- Niektóre architektury aplikacji

Inne zasady umieszczania są skonfigurowane za pośrednictwem korelacji lub zasad.

## <a name="next-steps"></a>Kolejne kroki
- Metryki są zarządzaniu Menedżer zasobów klastra sieci szkieletowej usług konsumenckich i pojemności w klastrze. Aby dowiedzieć się więcej na temat metryki i sposobach ich konfigurowania, zapoznaj się [w tym artykule](service-fabric-cluster-resource-manager-metrics.md)
- Koligacja jest jeden tryb, które można skonfigurować dla usług. Nie jest często, ale jeśli potrzebne informacje na temat jego [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Istnieje wiele reguł różnych umieszczania skonfigurowane w usłudze do obsługi dodatkowych scenariuszach. Można znaleźć informacje dotyczące tych zasad umieszczania różnych [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Rozpocznij od początku i [wprowadzenie do usługi sieci szkieletowej klastra Menedżera zasobów](service-fabric-cluster-resource-manager-introduction.md)
- Aby dowiedzieć się o jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zobacz artykuł na [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Menedżer zasobów klastra ma wiele opcji opisujące klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się w tym artykule na [opisujące klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
