---
title: Skalowanie automatyczne wspólnych metryk
description: Dowiedz się, które metryki są często używane do automatycznego skalowania usług w chmurze, maszyn wirtualnych i aplikacji sieci Web.
ms.topic: conceptual
ms.date: 12/6/2016
ms.subservice: autoscale
ms.openlocfilehash: 2c335168683212337876c963a7cfdb441d0ac69a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845564"
---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Skalowanie automatyczne w monitorze platformy Azure — typowe metryki

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Skalowanie automatyczne usługi Azure Monitor umożliwia skalowanie liczby uruchomionych wystąpień w górę lub w dół, na podstawie danych telemetrycznych (metryki). W tym dokumencie opisano typowe metryki, których można użyć. W witrynie Azure portal można wybrać metrykę zasobu do skalowania według. Można jednak również wybrać dowolną metrykę z innego zasobu, aby skalować według.

Skalowanie automatyczne usługi Azure Monitor dotyczy tylko [zestawów skalowania maszyn wirtualnych,](https://azure.microsoft.com/services/virtual-machine-scale-sets/) [usług w chmurze,](https://azure.microsoft.com/services/cloud-services/) [usługi aplikacji — aplikacje sieci Web](https://azure.microsoft.com/services/app-service/web/)i usług [zarządzania interfejsami API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts). Inne usługi platformy Azure używają różnych metod skalowania.

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Metryki obliczeń dla maszyn wirtualnych opartych na Menedżerze zasobów
Domyślnie maszyny wirtualne oparte na Menedżerze zasobów i zestawy skalowania maszyn wirtualnych emitują podstawowe metryki (na poziomie hosta). Ponadto podczas konfigurowania zbierania danych diagnostycznych dla maszyny wirtualnej platformy Azure i VMSS rozszerzenie diagnostyczne platformy Azure emituje również liczniki wydajności systemu gościa-systemu operacyjnego (powszechnie znane jako "metryki gościa systemu operacyjnego").  Wszystkie te metryki są używane w regułach skalowania automatycznego.

Za pomocą `Get MetricDefinitions` interfejsu API/PoSH/CLI można wyświetlić metryki dostępne dla zasobu VMSS.

Jeśli używasz zestawów skalowania maszyny Wirtualnej i nie widzisz określonej metryki na liście, prawdopodobnie jest *wyłączona* w rozszerzeniu diagnostyki.

Jeśli określona metryka nie jest próbkowana lub przesyłana z odpowiednią częstotliwością, można zaktualizować konfigurację diagnostyki.

Jeśli albo poprzedni przypadek jest spełniony, a następnie przejrzyj [użyj programu PowerShell, aby włączyć diagnostykę platformy Azure na maszynie wirtualnej](../../virtual-machines/extensions/diagnostics-windows.md) z systemem Windows o programie PowerShell, aby skonfigurować i zaktualizować rozszerzenie diagnostyki maszyn wirtualnych platformy Azure, aby włączyć metrykę. Ten artykuł zawiera również przykładowy plik konfiguracji diagnostyki.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Metryki hosta dla maszyn wirtualnych opartych na Menedżerze zasobów i Windows
Następujące metryki na poziomie hosta są emitowane domyślnie dla platformy Azure VM i VMSS w wystąpieniach systemu Windows i Linux. Te metryki opisują maszynę wirtualną platformy Azure, ale są zbierane z hosta maszyny Wirtualnej platformy Azure, a nie za pośrednictwem agenta zainstalowanego na maszynie wirtualnej gościa. Te metryki można używać w regułach skalowania automatycznego.

- [Metryki hosta dla maszyn wirtualnych opartych na Menedżerze zasobów i Windows](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachines)
- [Metryki hosta dla zestawów skalowania maszyn wirtualnych opartych na Menedżerze zasobów](../../azure-monitor/platform/metrics-supported.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-for-resource-manager-based-windows-vms"></a>Metryki systemu operacyjnego gościa dla maszyn wirtualnych systemu Windows opartych na Menedżerze zasobów
Podczas tworzenia maszyny Wirtualnej na platformie Azure diagnostyka jest włączona przy użyciu rozszerzenia diagnostyki. Rozszerzenie diagnostyki emituje zestaw metryk pobranych z wnętrza maszyny Wirtualnej. Oznacza to, że można automatycznie skalować metryki, które nie są emitowane domyślnie.

Listę metryk można wygenerować za pomocą następującego polecenia w programie PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Alert można utworzyć dla następujących metryk:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Processor(_Total)\% Processor Time |Wartość procentowa |
| \Czas uprzywilejowany procesora(_Total)\% |Wartość procentowa |
| \Procesor(_Total)\% Czas użytkownika |Wartość procentowa |
| \Informacje o procesorze(_Total)\Częstotliwość procesora |Liczba |
| \System\Procesy |Liczba |
| \Proces(_Total)\Liczba wątków |Liczba |
| \Proces(_Total)\Liczba uchwytów |Liczba |
| \Używane\% bajty zatwierdzone pamięci |Wartość procentowa |
| \Memory\Available Bytes |Bajty |
| \Pamięć\Zatwierdzone bajty |Bajty |
| \Pamięć\Limit zatwierdzania |Bajty |
| \Pamięć\Bajty stronicowane w puli |Bajty |
| \Pamięć\Pula bajtów niestronicowa |Bajty |
| \PhysicalDisk(_Total)\% Czas dysku |Wartość procentowa |
| \PhysicalDisk(_Total)\% Czas odczytu dysku |Wartość procentowa |
| \PhysicalDisk(_Total)\% Czas zapisu dysku |Wartość procentowa |
| \PhysicalDisk(_Total)\Transfery dysków/s |Połów połówek |
| \PhysicalDisk(_Total)\Odczyty dysku/s |Połów połówek |
| \PhysicalDisk(_Total)\Zapisy na dysku/s |Połów połówek |
| \PhysicalDisk(_Total)\Bajty dysku/s |PrzeztówPerSekunda |
| \PhysicalDisk(_Total)\Bajty odczytu dysku/s |PrzeztówPerSekunda |
| \PhysicalDisk(_Total)\Bajty zapisu dysku/s |PrzeztówPerSekunda |
| \PhysicalDisk(_Total)\Średnia długość kolejki dysku |Liczba |
| \PhysicalDisk(_Total)\Średnia długość kolejki odczytu dysku |Liczba |
| \PhysicalDisk(_Total)\Średnia długość kolejki zapisu dysku |Liczba |
| \LogicalDisk(_Total)\% Wolne miejsce |Wartość procentowa |
| \LogicalDisk(_Total)\Darmowe megabajty |Liczba |

### <a name="guest-os-metrics-linux-vms"></a>Metryki systemu operacyjnego gościa Linux VMs
Podczas tworzenia maszyny Wirtualnej na platformie Azure diagnostyka jest domyślnie włączona przy użyciu rozszerzenia diagnostyki.

Listę metryk można wygenerować za pomocą następującego polecenia w programie PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Alert można utworzyć dla następujących metryk:

| Nazwa metryki | Jednostka |
| --- | --- |
| \Pamięć\DostępnaMoja |Bajty |
| \Pamięć\ProcentDostępne Memory |Wartość procentowa |
| \Pamięć\UżywaneJemory |Bajty |
| \Pamięć\ProcentUsedMemory |Wartość procentowa |
| \Pamięć\PercentUsedByCache |Wartość procentowa |
| \Pamięć\PagesPerSec |Połów połówek |
| \Pamięć\PagesReadPerSec |Połów połówek |
| \Pamięć\PagesWrittenPerSec |Połów połówek |
| \Pamięć\DostępneSwap |Bajty |
| \Pamięć\ProcentDostępnySwap |Wartość procentowa |
| \Pamięć\UsedSwap |Bajty |
| \Pamięć\ProcentUsedSwap |Wartość procentowa |
| \Procesor\PercentIdleTime |Wartość procentowa |
| \Procesor\PercentUserTime |Wartość procentowa |
| \Procesor\PercentNiceTime |Wartość procentowa |
| \Procesor\PercentPrivilegedTime |Wartość procentowa |
| \Procesor\PercentInterruptTime |Wartość procentowa |
| \Procesor\PercentDPCTime |Wartość procentowa |
| \Procesor\PercentProcesorTime |Wartość procentowa |
| \Procesor\PercentIOWadatczas |Wartość procentowa |
| \PhysicalDisk\BytesPerSekund |PrzeztówPerSekunda |
| \PhysicalDisk\ReadBytesPerSecond |PrzeztówPerSekunda |
| \PhysicalDisk\WriteBytesPerSecond |PrzeztówPerSekunda |
| \PhysicalDisk\TransfersPerSecond |Połów połówek |
| \PhysicalDisk\OdczytyNachund |Połów połówek |
| \PhysicalDisk\WritesPerSecond |Połów połówek |
| \Dysk fizyczny\Średnia Czas czytania |Sekundy |
| \PhysicalDisk\AverageWriteTime |Sekundy |
| \PhysicalDisk\ŚredniaCzastransferu |Sekundy |
| \PhysicalDisk\ŚredniaUskuskowakość wyd. |Liczba |
| \NetworkInterface\BytesPrzełączone |Bajty |
| \NetworkInterface\BytesOdnastawiona |Bajty |
| \NetworkInterface\PakietyPrzełączone |Liczba |
| \NetworkInterface\PacketsReceived \NetworkInterface\PacketsReceived \NetworkInterface\PacketsReceived \Network |Liczba |
| \NetworkInterface\BytesTotal |Bajty |
| \NetworkInterface\TotalRxErrors |Liczba |
| \NetworkInterface\TotalTxErrors |Liczba |
| \NetworkInterface\TotalCollisions |Liczba |

## <a name="commonly-used-app-service-server-farm-metrics"></a>Często używane dane usługi app service (farma serwerów)
Można również wykonać skalowanie automatyczne na podstawie typowych metryk serwera sieci web, takich jak długość kolejki http. Jego nazwa metryczna to **HttpQueueLength**.  W poniższej sekcji wymieniono dostępne metryki farmy serwerów (App Service).

### <a name="web-apps-metrics"></a>Metryki aplikacji sieci Web
Listę metryk aplikacji sieci Web można wygenerować za pomocą następującego polecenia w programie PowerShell.

```
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Możesz alertować na lub skalować przez te metryki.

| Nazwa metryki | Jednostka |
| --- | --- |
| CpuPercentage (CpuPercentage) |Wartość procentowa |
| MemoryPercentage (PamięćPrzestńczliwienie) |Wartość procentowa |
| Funkcja DiskQueueLength |Liczba |
| Funkcja HttpQueueLength |Liczba |
| Bytesreceived |Bajty |
| Bajtów |Bajty |

## <a name="commonly-used-storage-metrics"></a>Często używane dane magazynu
Można skalować według długości kolejki magazynu, czyli liczby komunikatów w kolejce magazynu. Długość kolejki magazynu jest metryką specjalną, a próg jest liczbą komunikatów na wystąpienie. Na przykład jeśli istnieją dwa wystąpienia i jeśli próg jest ustawiony na 100, skalowanie występuje, gdy całkowita liczba wiadomości w kolejce wynosi 200. Może to być 100 komunikatów na wystąpienie, 120 i 80 lub dowolną inną kombinację, która dodaje się do 200 lub więcej.

Skonfiguruj to ustawienie w witrynie Azure portal w bloku **Ustawienia.** W przypadku zestawów skalowania maszyn wirtualnych można zaktualizować ustawienie Skalowanie automatyczne w szablonie Menedżera zasobów, aby używać *metrykiName* jako *ApproximateMessageCount* i przekazać identyfikator kolejki magazynu jako *metrykęResourceUri*.

Na przykład za pomocą klasycznego konta magazynu metryka ustawienia skalowania automatycznegoWyłącznik będzie zawierać:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

W przypadku (niekszyckowego) konta magazynu metricTrigger będzie zawierać:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Często używane dane usługi Service Bus
Można skalować według długości kolejki usługi Service Bus, która jest liczbą komunikatów w kolejce usługi Service Bus. Długość kolejki usługi Service Bus jest specjalną metryką, a próg jest liczbą komunikatów na wystąpienie. Na przykład jeśli istnieją dwa wystąpienia i jeśli próg jest ustawiony na 100, skalowanie występuje, gdy całkowita liczba wiadomości w kolejce wynosi 200. Może to być 100 komunikatów na wystąpienie, 120 i 80 lub dowolną inną kombinację, która dodaje się do 200 lub więcej.

W przypadku zestawów skalowania maszyn wirtualnych można zaktualizować ustawienie Skalowanie automatyczne w szablonie Menedżera zasobów, aby używać *metrykiName* jako *ApproximateMessageCount* i przekazać identyfikator kolejki magazynu jako *metrykęResourceUri*.

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> W przypadku usługi Service Bus koncepcja grupy zasobów nie istnieje, ale usługa Azure Resource Manager tworzy domyślną grupę zasobów na region. Grupa zasobów jest zwykle w formacie "Default-ServiceBus-[region]". Na przykład "Default-ServiceBus-EastUS", "Default-ServiceBus-WestUS", "Default-ServiceBus-AustraliaEast" itp.
>
>

