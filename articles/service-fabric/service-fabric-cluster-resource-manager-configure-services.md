---
title: Określanie danych i ustawień miejsc docelowych
description: Dowiedz się, jak opisać usługę sieci szkieletowej usług, określając metryki, ograniczenia miejsc docelowych i inne zasady umieszczania.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75610101"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>Konfigurowanie ustawień Menedżera zasobów klastra dla usług sieci szkieletowej usług
Menedżer zasobów klastra sieci szkieletowej usług umożliwia precyzyjną kontrolę nad regułami regulującymi każdą indywidualną usługę o nazwie. Każda nazwana usługa może określić reguły, w jaki sposób powinny być przydzielane w klastrze. Każda nazwana usługa może również zdefiniować zestaw metryk, które chce zgłosić, w tym, jak ważne są one dla tej usługi. Konfigurowanie usług dzieli się na trzy różne zadania:

1. Konfigurowanie ograniczeń umieszczania
2. Konfigurowanie metryk
3. Konfigurowanie zaawansowanych zasad umieszczania i innych reguł (rzadziej)

## <a name="placement-constraints"></a>Ograniczenia umieszczania
Ograniczenia umieszczania są używane do kontrolowania, które węzły w klastrze usługa może faktycznie działać na. Zazwyczaj określone wystąpienie usługi o nazwie lub wszystkie usługi danego typu ograniczone do uruchomienia w określonym typie węzła. Ograniczenia umieszczania są rozszerzalne. Można zdefiniować dowolny zestaw właściwości na typ węzła, a następnie wybrać dla nich z ograniczeniami podczas tworzenia usług. Można również zmienić ograniczenia umieszczania usługi, gdy jest uruchomiona. Dzięki temu można reagować na zmiany w klastrze lub wymagania usługi. Właściwości danego węzła można również dynamicznie aktualizować w klastrze. Więcej informacji na temat ograniczeń umieszczania i sposobu ich konfigurowania można znaleźć w [tym artykule](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Metryki
Metryki to zestaw zasobów, których potrzebuje dana nazwana usługa. Konfiguracja metryki usługi zawiera, ile tego zasobu każda replika stanowa lub bezstanowe wystąpienie tej usługi zużywa domyślnie. Metryki obejmują również wagę, która wskazuje, jak ważne jest równoważenie tej metryki dla tej usługi, w przypadku, gdy konieczne są kompromisy.

## <a name="advanced-placement-rules"></a>Zaawansowane reguły umieszczania
Istnieją inne typy reguł umieszczania, które są przydatne w mniej typowych scenariuszach. Przykłady to:
- Ograniczenia, które pomagają w klastrach rozproszonych geograficznie
- Niektóre architektury aplikacji

Inne reguły umieszczania są konfigurowane za pomocą korelacji lub zasad.

## <a name="next-steps"></a>Następne kroki
- Metryki są jak menedżer zasobów klastra sieci szkieletowej usług zarządza zużyciem i pojemnością w klastrze. Aby dowiedzieć się więcej o danych i sposobie ich konfigurowania, zapoznaj się z [tym artykułem](service-fabric-cluster-resource-manager-metrics.md)
- Koligacja jest jednym trybem, który można skonfigurować dla swoich usług. Nie jest to powszechne, ale jeśli tego potrzebujesz, możesz dowiedzieć się o tym [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
- Istnieje wiele różnych reguł umieszczania, które można skonfigurować w usłudze do obsługi dodatkowych scenariuszy. Możesz dowiedzieć się o tych różnych zasadach umieszczania [tutaj](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)
- Zacznij od początku i [uzyskaj wprowadzenie do Menedżera zasobów klastra sieci szkieletowej usług](service-fabric-cluster-resource-manager-introduction.md)
- Aby dowiedzieć się, jak Menedżer zasobów klastra zarządza i równoważy obciążenie w klastrze, zapoznaj się z artykułem na temat [równoważenia obciążenia](service-fabric-cluster-resource-manager-balancing.md)
- Menedżer zasobów klastra ma wiele opcji opisu klastra. Aby dowiedzieć się więcej na ich temat, zapoznaj się z tym [artykułem opisującym klaster sieci szkieletowej usług](service-fabric-cluster-resource-manager-cluster-description.md)
