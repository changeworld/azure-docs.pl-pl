---
title: Typowe metryki automatycznego skalowania
description: Dowiedz się, metryk, które są często używane do skalowania automatycznego usługi w chmurze, maszyn wirtualnych i aplikacji sieci Web.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 7b6f454a8d4c8794b8c56494fd9ed573f8b79852
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35262243"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Azure metryki wspólnej Skalowanie automatyczne monitora
Azure Monitor Skalowanie automatyczne umożliwia skalowanie liczby wystąpień uruchomione, na podstawie danych telemetrycznych (metryk). W tym dokumencie opisano typowe metryki, które możesz chcieć użyć. W portalu Azure dla farmy serwerów i usług w chmurze można wybrać metryki zasobu skalowanie przez. Jednak możesz również wszystkie metryki z skalowanie przez inny zasób.

Azure Monitor skalowania automatycznego ma zastosowanie tylko do [zestawy skalowania maszyny wirtualnej](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usługi w chmurze](https://azure.microsoft.com/services/cloud-services/), i [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/). Innymi usługami Azure, użyj metod skalowania.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Obliczanie metryki dla maszyn wirtualnych w Menedżerze zasobów
Domyślnie maszyn wirtualnych z systemem Resource Manager i zestawy skalowania maszyny wirtualnej Emituj podstawowe metryki (poziomie hosta). Ponadto podczas konfigurowania funkcji zbierania danych diagnostycznych dla maszyny Wirtualnej platformy Azure i VMSS rozszerzenie diagnostyki Azure również emituje liczników wydajności systemu operacyjnego gościa (często nazywana "Systemu operacyjnego gościa metryki").  Za pomocą tych metryk w reguł skalowania automatycznego.

Można użyć `Get MetricDefinitions` interfejsu API/PoSH/CLI, aby wyświetlić dostępne dla zasobu VMSS metryki.

Jeśli używasz zestawy skalowania maszyny Wirtualnej i nie widzisz określonej metryki na liście, a następnie prawdopodobnie *wyłączone* Twojego rozszerzenia diagnostyki.

Jeśli nie jest określonej metryki próbkowany lub przenoszony z częstotliwością chcesz umożliwić aktualizację konfiguracji diagnostyki.

W przypadku obu powyższych przypadkach ma wartość true, następnie przejrzyj [Użyj programu PowerShell, aby włączyć na maszynie wirtualnej z systemem Windows Azure Diagnostics](../virtual-machines/windows/ps-extensions-diagnostics.md) dotyczące programu PowerShell do konfigurowania i aktualizowania Twoje rozszerzenie diagnostyki maszyny Wirtualnej Azure, aby włączyć metryki. Ten artykuł zawiera także przykładowy plik konfiguracyjny diagnostyki.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metryki hosta dla Menedżera zasobów systemu Windows i maszyn wirtualnych systemu Linux
Następujące metryki poziomu hosta są emitowane domyślnie dla maszyny Wirtualnej platformy Azure i VMSS w systemach Windows i Linux wystąpień. Te metryki pokazują maszyny Wirtualnej Azure, ale są zbierane z hosta maszyny Wirtualnej Azure, a nie za pośrednictwem agent został zainstalowany na maszynie Wirtualnej gościa. Możesz użyć tych metryk w regułach Skalowanie automatyczne.

- [Metryki hosta dla Menedżera zasobów systemu Windows i maszyn wirtualnych systemu Linux](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Metryki hosta dla Menedżera zasobów systemu Windows i zestawy skalowania maszyny Wirtualnej systemu Linux](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Metryki systemu operacyjnego gościa maszyn wirtualnych Menedżera zasobów systemu Windows
Po utworzeniu maszyny Wirtualnej na platformie Azure diagnostics jest włączona przy użyciu rozszerzenia diagnostyki. Rozszerzenia diagnostyki emituje zestawu metryki pobranych z wewnątrz maszyny Wirtualnej. Oznacza to, że skalowanie automatyczne wylogowuje metryki, które nie są emitowane przez domyślny.

Za pomocą następującego polecenia programu PowerShell, można wygenerować listy metryki.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Można utworzyć alertu dotyczącego następujące metryki:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procent |
| \Processor(_Total)\% czas uprzywilejowany |Procent |
| \Processor(_Total)\% czas użytkownika |Procent |
| Częstotliwość \Processor informacji (_Total) \Processor |Licznik |
| \System\Processes |Licznik |
| Liczba \Thread \Process (_Total) |Licznik |
| Liczba \Handle \Process (_Total) |Licznik |
| \Memory\% Zadeklarowane bajty w użyciu |Procent |
| \Memory\Available Bytes |Bajty |
| \Memory\Committed bajtów |Bajty |
| \Memory\Commit limit |Bajty |
| \Memory\Pool stronicowanej |Bajty |
| \Memory\Pool puli niestronicowanej |Bajty |
| \PhysicalDisk(_Total)\% czas na dysku |Procent |
| \PhysicalDisk(_Total)\% czas odczytu z dysku |Procent |
| \PhysicalDisk(_Total)\% czas zapisu na dysku |Procent |
| Transfery \Disk \PhysicalDisk (_Total) na sekundę |CountPerSecond |
| Odczyty \Disk \PhysicalDisk (_Total) na sekundę |CountPerSecond |
| Zapisy \Disk \PhysicalDisk (_Total) na sekundę |CountPerSecond |
| \Disk \PhysicalDisk (_Total) bajty/s |Bajty na sekundę |
| Bajty odczytane/s \Disk \PhysicalDisk (_Total) |Bajty na sekundę |
| Bajty zapisu \Disk \PhysicalDisk (_Total) / s |Bajty na sekundę |
| \Avg \PhysicalDisk (_Total) Długość kolejki dysku |Licznik |
| \Avg \PhysicalDisk (_Total) Długość kolejki odczytu dysku |Licznik |
| \Avg \PhysicalDisk (_Total) Długość kolejki dysku zapisu |Licznik |
| \LogicalDisk(_Total)\% wolnego miejsca |Procent |
| Megabajtów \Free \LogicalDisk (_Total) |Licznik |

### <a name="guest-os-metrics-linux-vms"></a>Metryki systemu operacyjnego gościa maszyn wirtualnych systemu Linux
Po utworzeniu maszyny Wirtualnej na platformie Azure diagnostics jest domyślnie włączona przy użyciu rozszerzenia diagnostyki.

Za pomocą następującego polecenia programu PowerShell, można wygenerować listy metryki.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Można utworzyć alertu dotyczącego następujące metryki:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Memory\AvailableMemory |Bajty |
| \Memory\PercentAvailableMemory |Procent |
| \Memory\UsedMemory |Bajty |
| \Memory\PercentUsedMemory |Procent |
| \Memory\PercentUsedByCache |Procent |
| \Memory\PagesPerSec |CountPerSecond |
| \Memory\PagesReadPerSec |CountPerSecond |
| \Memory\PagesWrittenPerSec |CountPerSecond |
| \Memory\AvailableSwap |Bajty |
| \Memory\PercentAvailableSwap |Procent |
| \Memory\UsedSwap |Bajty |
| \Memory\PercentUsedSwap |Procent |
| \Processor\PercentIdleTime |Procent |
| \Processor\PercentUserTime |Procent |
| \Processor\PercentNiceTime |Procent |
| \Processor\PercentPrivilegedTime |Procent |
| \Processor\PercentInterruptTime |Procent |
| \Processor\PercentDPCTime |Procent |
| \Processor\PercentProcessorTime |Procent |
| \Processor\PercentIOWaitTime |Procent |
| \PhysicalDisk\BytesPerSecond |Bajty na sekundę |
| \PhysicalDisk\ReadBytesPerSecond |Bajty na sekundę |
| \PhysicalDisk\WriteBytesPerSecond |Bajty na sekundę |
| \PhysicalDisk\TransfersPerSecond |CountPerSecond |
| \PhysicalDisk\ReadsPerSecond |CountPerSecond |
| \PhysicalDisk\WritesPerSecond |CountPerSecond |
| \PhysicalDisk\AverageReadTime |Sekundy |
| \PhysicalDisk\AverageWriteTime |Sekundy |
| \PhysicalDisk\AverageTransferTime |Sekundy |
| \PhysicalDisk\AverageDiskQueueLength |Licznik |
| \NetworkInterface\BytesTransmitted |Bajty |
| \NetworkInterface\BytesReceived |Bajty |
| \NetworkInterface\PacketsTransmitted |Licznik |
| \NetworkInterface\PacketsReceived |Licznik |
| \NetworkInterface\BytesTotal |Bajty |
| \NetworkInterface\TotalRxErrors |Licznik |
| \NetworkInterface\TotalTxErrors |Licznik |
| \NetworkInterface\TotalCollisions |Licznik |

## <a name="commonly-used-web-server-farm-metrics"></a>Często używane metryki sieci Web (farmy serwerów)
Można także przeprowadzić automatycznego skalowania oparte na wspólnej metryki serwera sieci web, takie jak długość kolejki Http. Nazwa metryki jego jest **HttpQueueLength**.  W poniższej sekcji przedstawiono metryki farmy (aplikacje sieci Web) dostępnego serwera.

### <a name="web-apps-metrics"></a>Metryki aplikacji sieci Web
Można wygenerować listy metryki aplikacji sieci Web za pomocą następującego polecenia programu PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Możesz alert po wystąpieniu lub skalowania przez te metryki.

| Nazwa metryki | Jednostka |
| --- | --- |
| CpuPercentage |Procent |
| MemoryPercentage |Procent |
| DiskQueueLength |Licznik |
| HttpQueueLength |Licznik |
| BytesReceived |Bajty |
| Żądania |Bajty |

## <a name="commonly-used-storage-metrics"></a>Często używane metryki magazynu
Możesz skalować długości kolejki magazynu, który jest liczba komunikatów w kolejce magazynu. Długość kolejki magazynu to specjalne metryki i próg jest liczba komunikatów dla każdego wystąpienia. Na przykład jeśli istnieją dwa wystąpienia, a próg wynosi 100, skalowanie występuje, gdy łączna liczba wiadomości w kolejce to 200. Który może być 100 komunikaty dla każdego wystąpienia, 120 i 80 lub wszelkie inne kombinacje dodające maksymalnie 200 lub więcej.

Skonfiguruj to ustawienie w portalu Azure w **ustawienia** bloku. W przypadku zestawy skalowania maszyny Wirtualnej, można aktualizować w szablonie usługi Resource Manager do używania w ustawieniu skalowania automatycznego *metricName* jako *ApproximateMessageCount* i podaj identyfikator kolejki magazynu jako *metricResourceUri*.

Na przykład z kontem magazynu Classic metricTrigger Ustawienia skalowania automatycznego zadania obejmują:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Konto magazynu (z systemem innym niż klasyczne) metricTrigger zadania obejmują:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Często używane metryki usługi Service Bus
Możesz skalować według długość kolejki usługi Service Bus, która jest liczba komunikatów w kolejki usługi Service Bus. Długość kolejki usługi Service Bus jest specjalne metryki i próg jest liczba komunikatów dla każdego wystąpienia. Na przykład jeśli istnieją dwa wystąpienia, a próg wynosi 100, skalowanie występuje, gdy łączna liczba wiadomości w kolejce to 200. Który może być 100 komunikaty dla każdego wystąpienia, 120 i 80 lub wszelkie inne kombinacje dodające maksymalnie 200 lub więcej.

W przypadku zestawy skalowania maszyny Wirtualnej, można aktualizować w szablonie usługi Resource Manager do używania w ustawieniu skalowania automatycznego *metricName* jako *ApproximateMessageCount* i podaj identyfikator kolejki magazynu jako *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Dla usługi Service Bus koncepcji grupy zasobów nie istnieje, ale usługi Azure Resource Manager tworzy domyślną grupę zasobów dla regionu. Grupa zasobów jest zwykle w formacie "Default - magistrali usług — [region]". Na przykład "Domyślne-magistrali usług-EastUS", "Domyślne-magistrali usług-WestUS", "Domyślne-magistrali usług-AustraliaEast" itp.
>
>
