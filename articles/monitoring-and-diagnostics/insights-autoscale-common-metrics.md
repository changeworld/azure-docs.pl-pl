---
title: Typowe metryki automatycznego skalowania
description: Dowiedz się, jaką metrykę są często używane do automatycznego skalowania usługi w chmurze, maszyny wirtualne i aplikacje sieci Web.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 12/6/2016
ms.author: ancav
ms.component: autoscale
ms.openlocfilehash: 48c53b1b0c037e6bcfea3be49fdd2110e1e694b3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46970706"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Usługa Azure Monitor autoscaling często używane metryki
Usługa Azure Monitor autoscaling umożliwia skalowanie liczby wystąpień uruchomionych w górę lub w dół, na podstawie danych telemetrycznych (metryki). W tym dokumencie opisano typowe metryki, które prawdopodobnie chcą używać. W witrynie Azure portal można wybrać metryki zasobu, aby przeskalować. Jednak możesz również dowolnej metryce z skalowanie przez inny zasób.

Skalowanie automatyczne platformy Azure Monitor ma zastosowanie tylko do [Virtual Machine Scale Sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [usług w chmurze](https://azure.microsoft.com/services/cloud-services/), [App Service — Web Apps](https://azure.microsoft.com/services/app-service/web/), i [usługi API Management](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Inne usługi platformy Azure, użyj metod skalowania.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Obliczenia metryk dla maszyn wirtualnych opartych na programie Resource Manager
Domyślnie maszyn wirtualnych opartych na usłudze Resource Manager i Virtual Machine Scale Sets emituje metryki podstawowe (host poziomie). Ponadto podczas konfigurowania funkcji zbierania danych diagnostyki dla maszyny Wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych, rozszerzenie diagnostyki platformy Azure również emituje liczników wydajności systemu operacyjnego gościa (powszechnie znane jako "Systemu operacyjnego gościa metryki").  Te metryki są używane w reguł skalowania automatycznego.

Możesz użyć `Get MetricDefinitions` interfejsu API/PoSH/CLI, aby wyświetlić metryki dostępności zasobu zestawu skalowania maszyn wirtualnych.

Jeśli używasz usługi VM scale sets i nie widzisz określonej metryki na liście, a następnie prawdopodobnie *wyłączone* w rozszerzenie diagnostyki.

Jeśli w określonej metryki nie jest próbkowany lub przenoszony z częstotliwością, chcesz, aby umożliwić aktualizację konfiguracji diagnostyki.

Jeśli albo poprzednim przypadku ma wartość true, następnie przejrzyj [Diagnostyka Azure maszyny wirtualnej z systemem Windows w programie PowerShell](../virtual-machines/windows/ps-extensions-diagnostics.md) dotyczące programu PowerShell do konfigurowania i aktualizacji Twojego rozszerzenia diagnostyki maszyny Wirtualnej platformy Azure, aby włączyć metryki. Ten artykuł zawiera również przykładowy plik konfiguracji diagnostyki.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metryki hosta Windows opartych na usłudze Resource Manager i maszyn wirtualnych systemu Linux
Następujące metryki na poziomie hosta są emitowane przez domyślną dla maszyny Wirtualnej platformy Azure i zestawu skalowania maszyn wirtualnych w wystąpieniach systemów Windows i Linux. Te metryki pokazują maszynę Wirtualną platformy Azure, ale są zbierane z hosta maszyny Wirtualnej platformy Azure, a nie za pośrednictwem agenta zainstalowanego na maszynie Wirtualnej gościa. Te metryki może używać reguł skalowania automatycznego.

- [Metryki hosta Windows opartych na usłudze Resource Manager i maszyn wirtualnych systemu Linux](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Metryki hosta opartego na usłudze Resource Manager Windows i Linux VM Scale Sets](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Metryki systemu operacyjnego gościa opartego na usłudze Resource Manager VMs Windows
Po utworzeniu maszyny Wirtualnej na platformie Azure diagnostics jest włączone za pomocą rozszerzenia diagnostyki. Rozszerzenie diagnostyki emituje zestaw metryk pobranych z wewnątrz maszyny Wirtualnej. Oznacza to, że można automatycznego skalowania poza metryki, które nie są generowane domyślnie.

Aby wygenerować listę metryk, należy za pomocą następującego polecenia programu PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Można utworzyć alertu dla następujących metryk:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Procent |
| \Processor(_Total)\% czas uprzywilejowany |Procent |
| \Processor(_Total)\% czas użytkownika |Procent |
| Częstotliwość \Processor informacji (%) (_łącznie) \Processor |Licznik |
| \System\Processes |Licznik |
| Liczba \Thread \Process (%) (_łącznie) |Licznik |
| Liczba \Handle \Process (%) (_łącznie) |Licznik |
| \Memory\% przydzielonych bajtów w użyciu |Procent |
| \Memory\Available Bytes |Bajty |
| \Memory\Committed bajtów |Bajty |
| \Memory\Commit limit |Bajty |
| Bajty stronicowanej \Memory\Pool |Bajty |
| \Memory\Pool niestronicowanej |Bajty |
| \PhysicalDisk(_Total)\% dysku w czasie |Procent |
| \PhysicalDisk(_Total)\% czas odczytu z dysku |Procent |
| \PhysicalDisk(_Total)\% czas zapisu na dysku |Procent |
| Transfery \Disk \PhysicalDisk (%) (_łącznie) na sekundę |CountPerSecond |
| Odczyty \Disk \PhysicalDisk (%) (_łącznie) na sekundę |CountPerSecond |
| Zapisy \Disk \PhysicalDisk (%) (_łącznie) na sekundę |CountPerSecond |
| \Disk \PhysicalDisk (%) (_łącznie) bajty/s |Bajty na sekundę |
| \Disk \PhysicalDisk (%) (_łącznie) Odczytane bajty/s |Bajty na sekundę |
| Bajty zapisu \Disk \PhysicalDisk (%) (_łącznie) / s |Bajty na sekundę |
| \Avg \PhysicalDisk (%) (_łącznie). Długość kolejki dysku |Licznik |
| \Avg \PhysicalDisk (%) (_łącznie). Długość kolejki odczytu dysku |Licznik |
| \Avg \PhysicalDisk (%) (_łącznie). Długość kolejki zapisu dysku |Licznik |
| \LogicalDisk(_Total)\% wolnego miejsca |Procent |
| Megabajtów \Free \LogicalDisk (%) (_łącznie) |Licznik |

### <a name="guest-os-metrics-linux-vms"></a>Metryki systemu operacyjnego gościa, maszyny wirtualne systemu Linux
Po utworzeniu maszyny Wirtualnej na platformie Azure diagnostics jest domyślnie włączona, przy użyciu rozszerzenia diagnostyki.

Aby wygenerować listę metryk, należy za pomocą następującego polecenia programu PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Można utworzyć alertu dla następujących metryk:

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

## <a name="commonly-used-web-server-farm-metrics"></a>Często używane metryki sieci Web (farma serwerów)
Można również wykonać automatyczne skalowanie, oparte na typowych metryk serwera sieci web, takie jak długość kolejki Http. Nazwa metryki jego jest **HttpQueueLength**.  W poniższej sekcji przedstawiono metryki farmy (aplikacje sieci Web) dostępnego serwera.

### <a name="web-apps-metrics"></a>Metryki aplikacji sieci Web
Można generować listę metryk aplikacji sieci Web za pomocą następującego polecenia programu PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Można alert po wystąpieniu lub przez te metryki.

| Nazwa metryki | Jednostka |
| --- | --- |
| CpuPercentage |Procent |
| MemoryPercentage |Procent |
| DiskQueueLength |Licznik |
| HttpQueueLength |Licznik |
| BytesReceived |Bajty |
| Żądania |Bajty |

## <a name="commonly-used-storage-metrics"></a>Często używane metryki magazynu
Możesz skalować, długość kolejki magazynu, który jest liczba komunikatów w kolejce magazynu. Długość kolejki magazynu to specjalne metryki i próg jest liczba komunikatów na wystąpienie. Na przykład jeśli istnieją dwa wystąpienia i próg jest ustawiony na 100, skalowanie występuje, gdy łączna liczba komunikatów w kolejce wynosi 200. Który może być 100 wiadomości dla każdego wystąpienia, 120 i 80 lub wszelkie inne kombinacje dodającego maksymalnie 200 lub więcej.

To ustawienie jest skonfigurowane w witrynie Azure portal w **ustawienia** bloku. Dla zestawów skalowania maszyn wirtualnych, możesz zaktualizować ustawienie automatycznego skalowania w szablonie usługi Resource Manager, aby użyć *metricName* jako *ApproximateMessageCount* i przekaż identyfikator kolejki magazynu jako  *metricResourceUri*.

Na przykład przy użyciu klasycznego konta magazynu metricTrigger ustawienie skalowania automatycznego obejmuje:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

W przypadku konta magazynu (inne niż wersja klasyczna) metricTrigger zadania obejmują:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Często używane metryki usługi Service Bus
Możesz skalować, długość kolejki usługi Service Bus, która jest liczba komunikatów w kolejce usługi Service Bus. Długość kolejki usługi Service Bus to specjalne metryki i próg jest liczba komunikatów na wystąpienie. Na przykład jeśli istnieją dwa wystąpienia i próg jest ustawiony na 100, skalowanie występuje, gdy łączna liczba komunikatów w kolejce wynosi 200. Który może być 100 wiadomości dla każdego wystąpienia, 120 i 80 lub wszelkie inne kombinacje dodającego maksymalnie 200 lub więcej.

Dla zestawów skalowania maszyn wirtualnych, możesz zaktualizować ustawienie automatycznego skalowania w szablonie usługi Resource Manager, aby użyć *metricName* jako *ApproximateMessageCount* i przekaż identyfikator kolejki magazynu jako  *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Dla usługi Service Bus nie istnieje pojęcie grup zasobów, ale usługi Azure Resource Manager tworzy domyślną grupę zasobów, na region. Grupa zasobów ma zazwyczaj format "Default - ServiceBus-[region]". Na przykład "Domyślne-ServiceBus-EastUS", "Domyślna-ServiceBus-WestUS", "Domyślne-ServiceBus-AustraliaEast" itp.
>
>
