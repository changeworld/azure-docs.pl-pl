---
title: Monitorowanie i Diagnostyka w aplikacjach usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Więcej informacji na temat monitorowania i diagnozowania aplikacji usługi Service Fabric siatki na platformie Azure.
services: service-fabric-mesh
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 36c9a5d75c4a72365638619ab85d451df647feb3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939821"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Monitorowanie i diagnostyka usługi Service Fabric siatki dzieli się na trzy główne typy danych diagnostycznych:

- Dzienniki aplikacji — są one zdefiniowane jako dzienniki z konteneryzowanych aplikacji, w jaki sposób ma instrumentacji aplikacji (np. dzienniki platformy docker)
- Zdarzenia platformy — zdarzeń z odpowiednich operacji usługi kontenera platformy siatki aktualnie w tym kontenerze aktywacji, dezaktywacji i kończenie działania.
- Metryki kontenera — metryki użycia i wydajności zasobów dla kontenerów (statystyk platformy docker)

W tym artykule opisano opcje monitorowania i diagnostyki dla najnowszej wersji zapoznawczej dostępne.

## <a name="application-logs"></a>Dzienniki aplikacji

Możesz wyświetlić dzienniki platformy docker z od wdrożonych kontenerów na podstawie poszczególnych kontenerów. W modelu aplikacji usługi Service Fabric siatki każdy kontener jest pakietu kodu w aplikacji. Aby wyświetlić skojarzone dzienniki przy użyciu pakietu kodu, użyj następującego polecenia:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Można uzyskać nazwy replik, można użyć polecenia "az siatki replik usługi". Nazwy repliki jest zwiększenie liczby całkowite z zakresu od 0.

Poniżej przedstawiono, jak to wygląda przy przeglądaniu dzienniki z kontenerów VotingWeb.Code z aplikacji do głosowania:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metryki kontenera 

Środowisko siatki udostępnia kilka metryk wskazujący, jak działają kontenery. Następujące metryki są dostępne za pośrednictwem platformy Azure portal i platformy Azure monitoruje interfejsu wiersza polecenia:

| Metryka | Opis | Jednostki|
|----|----|----|
| CpuUtilization | ActualCpu/AllocatedCpu jako wartość procentowa | % |
| MemoryUtilization | ActualMem/AllocatedMem jako wartość procentowa | % |
| AllocatedCpu | Procesor CPU przydzielonych zgodnie z szablonu usługi Azure Resource Manager | Millicores |
| AllocatedMemory | Pamięć przydzielona zgodnie z szablonu usługi Azure Resource Manager | MB |
| ActualCpu | Użycie procesora CPU | Millicores |
| ActualMemory | Użycie pamięci | MB |
| ContainerStatus | 0 - nieprawidłowa: Stan kontenera jest nieznany <br> 1 — oczekuje: Kontener zostało zaplanowane do uruchomienia <br> 2 — początkowa: Trwa uruchamianie kontenera <br> 3 - pracę: Kontener został pomyślnie uruchomiony. <br> 4 — zatrzymywanie: Kontener jest zatrzymywana <br> 5 - zatrzymane: Kontener została pomyślnie zatrzymana | ND |
| ApplicationStatus | 0 — nieznany: Stan nie jest możliwy do pobrania <br> 1 — gotowe do: Aplikacja została pomyślnie uruchomiona. <br> 2 — uaktualnienie: Brak uaktualnienia w toku <br> 3 — tworzenie: Trwa tworzenie aplikacji <br> 4 — usuwanie: Trwa usuwanie aplikacji <br> 5 — nie powiodło się: Nie można wdrożyć aplikację | ND |
| Bajty | 0 - nieprawidłowa: Usługa nie ma obecnie stan kondycji <br> 1 — ok: Usługa jest w dobrej kondycji  <br> 2 — Ostrzeżenie: Może istnieć problem wymaga analizy <br> 3 — błąd: Coś jest tak, który wymaga zbadania <br> 4 - nieznane: Stan nie jest możliwy do pobrania | ND |
| ServiceReplicaStatus | 0 - nieprawidłowa: Replika nie ma obecnie stan kondycji <br> 1 — ok: Usługa jest w dobrej kondycji  <br> 2 — Ostrzeżenie: Może istnieć problem wymaga analizy <br> 3 — błąd: Coś jest tak, który wymaga zbadania <br> 4 - nieznane: Stan nie jest możliwy do pobrania | ND | 
| RestartCount | Liczby ponownych uruchomień kontenera | ND |

> [!NOTE]
> Bajty i ServiceReplicaStatus wartości są takie same jak [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) w usłudze Service Fabric. 

Wszystkie metryki jest dostępny w różnych wymiarach, tak aby było widać wartości zagregowane na różnych poziomach. Bieżącą listę wymiary są następujące:

* ApplicationName
* ServiceName
* ServiceReplicaName
* CodePackageName

> [!NOTE]
> Wymiar CodePackageName nie jest dostępna dla aplikacji systemu Linux. 

Każdy wymiar odnosi się do różnych składników [modelu aplikacji usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Usługa Azure Monitor interfejsu wiersza polecenia

Pełną listę poleceń jest dostępna w [dokumentacja wiersza polecenia platformy Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) , ale wprowadzono kilka pomocne poniższe przykłady 

W każdym przykładzie identyfikator zasobu ze wzorcem to

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Użycie procesora CPU kontenerów w aplikacji

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Wykorzystanie pamięci dla każdej repliki usługi
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Ponowne uruchomienie dla każdego kontenera, w oknie 1 godzina 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Średnie wykorzystanie Procesora na usługi o nazwie "VotingWeb" w oknie 1 godzina
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Eksplorator metryk

Eksplorator metryk jest blok w portalu, w którym możesz wizualizować wszystkie metryki aplikacji siatki. Ten blok jest dostępna na stronie aplikacji w portalu i usługi Azure monitor bloku, ten ostatni z nich służy do wyświetlania metryk dla wszystkich zasobów platformy Azure, które obsługują usługi Azure Monitor. 

![Eksplorator metryk](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Kolejne kroki
* Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
* Aby dowiedzieć się więcej o poleceniach metryk usługi Azure Monitor, zapoznaj się z [dokumentacja wiersza polecenia platformy Azure Monitor](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list).
