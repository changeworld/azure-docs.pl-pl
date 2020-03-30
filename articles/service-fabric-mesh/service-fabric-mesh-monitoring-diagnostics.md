---
title: Monitorowanie i diagnostyka w aplikacjach usługi Azure Service Fabric Mesh
description: Dowiedz się więcej o monitorowaniu i diagnozowaniu aplikacji w sieci szkieletowej usług na platformie Azure.
author: srrengar
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: srrengar
ms.custom: mvc, devcenter
ms.openlocfilehash: 247a1de4d00668371337295616d31caf101f0cc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75498147"
---
# <a name="monitoring-and-diagnostics"></a>Monitorowanie i diagnostyka
Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią. Monitorowanie i diagnostyka sieci szkieletowej usług mesh jest podzielone na trzy główne typy danych diagnostycznych:

- Dzienniki aplikacji — są one definiowane jako dzienniki z konteneryzowanych aplikacji, na podstawie sposobu instrumentowania aplikacji (np. dzienników docker)
- Zdarzenia platformy — zdarzenia z platformy Mesh istotne dla operacji kontenera, obecnie w tym aktywacji kontenera, dezaktywacji i zakończenia.
- Metryki kontenerów — metryki wykorzystania zasobów i wydajności kontenerów (statystyki docker)

W tym artykule omówiono opcje monitorowania i diagnostyki dla najnowszej wersji zapoznawczej dostępne.

## <a name="application-logs"></a>Dzienniki aplikacji

Dzienniki docker można wyświetlić z wdrożonych kontenerów, na podstawie kontenera. W modelu aplikacji sieci szkieletowej usług każdy kontener jest pakietem kodu w aplikacji. Aby wyświetlić skojarzone dzienniki z pakietem kodu, użyj następującego polecenia:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --app-name <nameOfApp> --service-name <nameOfService> --replica-name <nameOfReplica> --code-package-name <nameOfCodePackage>
```

> [!NOTE]
> Aby uzyskać nazwę repliki, można użyć polecenia "az mesh service-replica". Nazwy replik są zwiększaniem liczby całkowitych od 0.

Oto, jak to wygląda, aby zobaczyć dzienniki z kontenera VotingWeb.Code z aplikacji do głosowania:

```cli
az mesh code-package-log get --resource-group <nameOfRG> --application-name SbzVoting --service-name VotingWeb --replica-name 0 --code-package-name VotingWeb.Code
```

## <a name="container-metrics"></a>Metryki kontenerów 

Środowisko siatki udostępnia kilka metryk wskazujących, jak kontenery działają. Następujące metryki są dostępne za pośrednictwem witryny Azure portal i interfejsu wiersza polecenia monitora Platformy Azure:

| Metryka | Opis | Jednostki|
|----|----|----|
| Utilizacja procesora | Rzeczywista protokółCpu/allocatedCpu jako wartość procentowa | % |
| MemoryUtilization (Funkcja MemoryUtilization) | ActualMem/AllocatedMem jako wartość procentowa | % |
| AllocatedCpu (Protokół zaalokowany) | Procesor przydzielony zgodnie z szablonem usługi Azure Resource Manager | Millicores ( Millicores ) |
| AllocatedMemory (Memory) | Pamięć przydzielona zgodnie z szablonem usługi Azure Resource Manager | MB |
| Rzeczywista protokółCpu | Użycie procesora | Millicores ( Millicores ) |
| Wartość rzeczywista | Użycie pamięci | MB |
| Stan kontenera | 0 - Nieprawidłowy: Stan kontenera jest nieznany <br> 1 - Oczekujące: Kontener ma zaplanowane uruchomienie <br> 2 - Uruchamianie: Pojemnik jest w trakcie uruchamiania <br> 3 - Rozpoczęto: Kontener został pomyślnie uruchomiony <br> 4 - Zatrzymanie: Kontener jest zatrzymany <br> 5 - Zatrzymano: Kontener został pomyślnie zatrzymany | Nie dotyczy |
| Stan aplikacji | 0 - Nieznany: Stan nie jest można odzyskać <br> 1 - Gotowy: Aplikacja działa pomyślnie <br> 2 - Modernizacja: Trwa uaktualnienie <br> 3 - Tworzenie: Aplikacja jest tworzona <br> 4 - Usuwanie: Aplikacja jest usuwana <br> 5 - Nie można wdrożyć aplikacji: nie można wdrożyć aplikacji | Nie dotyczy |
| Stan serwisu | 0 - Nieprawidłowe: usługa nie ma obecnie stanu kondycji <br> 1 - Ok: Usługa jest zdrowa  <br> 2 - Ostrzeżenie: Może być coś złego wymagającego dochodzenia <br> 3 - Błąd: Jest coś nie tak, że wymaga dochodzenia <br> 4 - Nieznany: Stan nie jest można odzyskać | Nie dotyczy |
| ServiceReplicaStatus (Stan ServiceReplica) | 0 - Nieprawidłowe: replika nie ma obecnie stanu kondycji <br> 1 - Ok: Usługa jest zdrowa  <br> 2 - Ostrzeżenie: Może być coś złego wymagającego dochodzenia <br> 3 - Błąd: Jest coś nie tak, że wymaga dochodzenia <br> 4 - Nieznany: Stan nie jest można odzyskać | Nie dotyczy | 
| Liczba restartów | Liczba ponownych uruchomień kontenera | Nie dotyczy |

> [!NOTE]
> ServiceStatus i ServiceReplicaStatus wartości są takie same jak [HealthState](https://docs.microsoft.com/dotnet/api/system.fabric.health.healthstate?view=azure-dotnet) w sieci szkieletowej usług. 

Każda metryka jest dostępna w różnych wymiarach, dzięki czemu można zobaczyć agregaty na różnych poziomach. Bieżąca lista wymiarów jest następująca:

* ApplicationName
* ServiceName
* Nazwa serviceReplicaname
* Nazwa pakietu kodu

> [!NOTE]
> Wymiar CodePackageName nie jest dostępny dla aplikacji systemu Linux. 

Każdy wymiar odpowiada różnym składnikom [modelu aplikacji sieci szkieletowej usług](service-fabric-mesh-service-fabric-resources.md#applications-and-services)

### <a name="azure-monitor-cli"></a>Interfejsu wiersza polecenia monitora platformy Azure

Pełna lista poleceń jest dostępna w [dokumentów interfejsu wiersza polecenia usługi Azure Monitor,](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list) ale poniżej uwzględniliśmy kilka przydatnych przykładów 

W każdym przykładzie identyfikator zasobu jest zgodny z tym wzorcem

`"/subscriptions/<your sub ID>/resourcegroups/<your RG>/providers/Microsoft.ServiceFabricMesh/applications/<your App name>"`


* Wykorzystanie kontenerów w aplikacji

```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization"
```
* Wykorzystanie pamięci dla każdej repliki usługi
```cli
    az monitor metrics list --resource <resourceId> --metric "MemoryUtilization" --dimension "ServiceReplicaName"
``` 

* Ponowne uruchomienie każdego kontenera w oknie 1 godziny 
```cli
    az monitor metrics list --resource <resourceId> --metric "RestartCount" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z
``` 

* Średnie wykorzystanie procesora CPU w usługach o nazwie "VotingWeb" w oknie 1 godziny
```cli
    az monitor metrics list --resource <resourceId> --metric "CpuUtilization" --start-time 2019-02-01T00:00:00Z --end-time 2019-02-01T01:00:00Z --aggregation "Average" --filter "ServiceName eq 'VotingWeb'"
``` 

### <a name="metrics-explorer"></a>Eksplorator metryk

Eksplorator metryk to blok w portalu, w którym można wizualizować wszystkie metryki aplikacji Mesh. Ten blok jest dostępny na stronie aplikacji w portalu i bloku monitora platformy Azure, z których ten ostatni służy do wyświetlania metryk dla wszystkich zasobów platformy Azure, które obsługują usługę Azure Monitor. 

![Eksplorator metryk](./media/service-fabric-mesh-monitoring-diagnostics/metricsexplorer.png)


<!--
### Container Insights

In addition to the metrics explorer, we also have a dashboard available out of the box that shows sample metrics over time under the Insights blade in the application's page in the portal. 

![Container Insights](./media/service-fabric-mesh-monitoring-diagnostics/containerinsights.png)
-->

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat usługi Service Fabric Mesh, przeczytaj [omówienie usługi Service Fabric Mesh](service-fabric-mesh-overview.md).
* Aby dowiedzieć się więcej o poleceniach metryk usługi Azure Monitor, zapoznaj się z [dokumentów interfejsu wiersza polecenia interfejsu wiersza polecenia usługi Azure Monitor.](https://docs.microsoft.com/cli/azure/monitor/metrics?view=azure-cli-latest#az-monitor-metrics-list)
