---
title: Monitorowanie kontenerów w usłudze Azure Container Instances
description: Szczegółowe informacje na temat sposobu monitorowania użycia zasobów obliczeniowych, takich jak procesor i pamięć, przez kontenery w usłudze Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2018
ms.author: danlep
ms.openlocfilehash: 950d8b4b5ec1a55e2054039a01d6807915b5c714
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59784077"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorowanie zasobów kontenerów w usłudze Azure Container Instances

Usługa Azure Monitor zapewnia wgląd w szczegółowe dane o zasobach obliczeniowych używanych przez kontenery w usłudze Container Instances. Za pomocą usługi Azure Monitor można śledzić wykorzystanie procesora i pamięci przez grupy kontenerów i kontenery. Te dane użycia zasobów ułatwiają określenie najlepszych ustawień procesora i pamięci dla grup kontenerów.

W niniejszym dokumencie szczegółowo opisano gromadzenie danych użycia procesora i pamięci dla wystąpień kontenerów za pomocą witryny Azure Portal i interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Obecnie metryki użycia zasobów są dostępne tylko dla kontenerów systemu Linux.
>

## <a name="available-metrics"></a>Dostępne metryki

Usługa Azure Monitor udostępnia metryki dotyczące użycia **procesora** i **pamięci** dla usługi Azure Container Instances. Te metryki są dostępne dla grupy kontenerów i poszczególnych kontenerów.

Metryki procesora są wyrażane w **milirdzeniach**. Jeden milirdzeń to jedna tysięczna rdzenia procesora, zatem 500 milirdzeni (500 m) reprezentuje wykorzystanie 50% rdzenia procesora.

Metryki pamięci są wyrażane w **bajtach**.

## <a name="get-metrics---azure-portal"></a>Pobieranie metryk — Azure Portal

Po utworzeniu grupy kontenerów dane usługi Azure Monitor są dostępne w witrynie Azure Portal. Aby wyświetlić metryki dla grupy kontenerów, wybierz grupę zasobów, a następnie grupę kontenerów. Tutaj możesz zobaczyć wstępnie utworzone wykresy użycia procesora i pamięci.

![podwójny wykres][dual-chart]

W przypadku grupy kontenerów, która zawiera wiele kontenerów, użyj [wymiaru][monitor-dimension], aby przedstawić metryki dla poszczególnych kontenerów. Aby utworzyć wykres z metrykami pojedynczego kontenera, wykonaj następujące kroki:

1. Wybierz pozycję **Monitor** w menu nawigacji po lewej stronie.
2. Wybierz grupę kontenerów i metrykę (procesora lub pamięci).
3. Wybierz zielony przycisk wymiaru i wybierz **nazwę kontenera**.

![wymiar][dimension]

## <a name="get-metrics---azure-cli"></a>Pobieranie metryk — interfejs wiersza polecenia platformy Azure

Dane użycia procesora i pamięci usługi Container Instances można również zbierać przy użyciu interfejsu wiersza polecenia platformy Azure. Najpierw pobierz identyfikator grupy kontenerów za pomocą następującego polecenia. Zastąp element `<resource-group>` nazwą grupy zasobów, a element `<container-group>` zastąp nazwą grupy kontenerów.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Użyj następującego polecenia, aby pobrać metryki użycia **procesora**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:39:00  CPU Usage
2018-04-22 04:40:00  CPU Usage
2018-04-22 04:41:00  CPU Usage
2018-04-22 04:42:00  CPU Usage
2018-04-22 04:43:00  CPU Usage      0.375
2018-04-22 04:44:00  CPU Usage      0.875
2018-04-22 04:45:00  CPU Usage      1
2018-04-22 04:46:00  CPU Usage      3.625
2018-04-22 04:47:00  CPU Usage      1.5
2018-04-22 04:48:00  CPU Usage      2.75
2018-04-22 04:49:00  CPU Usage      1.625
2018-04-22 04:50:00  CPU Usage      0.625
2018-04-22 04:51:00  CPU Usage      0.5
2018-04-22 04:52:00  CPU Usage      0.5
2018-04-22 04:53:00  CPU Usage      0.5
```

Użyj następującego polecenia, aby pobrać metryki użycia **pamięci**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name              Average
-------------------  ------------  -----------
2018-04-22 04:38:00  Memory Usage
2018-04-22 04:39:00  Memory Usage
2018-04-22 04:40:00  Memory Usage
2018-04-22 04:41:00  Memory Usage
2018-04-22 04:42:00  Memory Usage  6.76915e+06
2018-04-22 04:43:00  Memory Usage  9.22061e+06
2018-04-22 04:44:00  Memory Usage  9.83552e+06
2018-04-22 04:45:00  Memory Usage  8.42906e+06
2018-04-22 04:46:00  Memory Usage  8.39526e+06
2018-04-22 04:47:00  Memory Usage  8.88013e+06
2018-04-22 04:48:00  Memory Usage  8.89293e+06
2018-04-22 04:49:00  Memory Usage  9.2073e+06
2018-04-22 04:50:00  Memory Usage  9.36243e+06
2018-04-22 04:51:00  Memory Usage  9.30509e+06
2018-04-22 04:52:00  Memory Usage  9.2416e+06
2018-04-22 04:53:00  Memory Usage  9.1008e+06
```

W przypadku grupy wielu kontenerów można dodać wymiar `containerName`, aby te dane były zwracane dla poszczególnych kontenerów.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2018-04-22 17:03:00  Memory Usage  aci-tutorial-app      1.95338e+07
2018-04-22 17:04:00  Memory Usage  aci-tutorial-app      1.93096e+07
2018-04-22 17:05:00  Memory Usage  aci-tutorial-app      1.91488e+07
2018-04-22 17:06:00  Memory Usage  aci-tutorial-app      1.94335e+07
2018-04-22 17:07:00  Memory Usage  aci-tutorial-app      1.97714e+07
2018-04-22 17:08:00  Memory Usage  aci-tutorial-app      1.96178e+07
2018-04-22 17:09:00  Memory Usage  aci-tutorial-app      1.93434e+07
2018-04-22 17:10:00  Memory Usage  aci-tutorial-app      1.92614e+07
2018-04-22 17:11:00  Memory Usage  aci-tutorial-app      1.90659e+07
2018-04-22 16:12:00  Memory Usage  aci-tutorial-sidecar  1.35373e+06
2018-04-22 16:13:00  Memory Usage  aci-tutorial-sidecar  1.28614e+06
2018-04-22 16:14:00  Memory Usage  aci-tutorial-sidecar  1.31379e+06
2018-04-22 16:15:00  Memory Usage  aci-tutorial-sidecar  1.29536e+06
2018-04-22 16:16:00  Memory Usage  aci-tutorial-sidecar  1.38138e+06
2018-04-22 16:17:00  Memory Usage  aci-tutorial-sidecar  1.41312e+06
2018-04-22 16:18:00  Memory Usage  aci-tutorial-sidecar  1.49914e+06
2018-04-22 16:19:00  Memory Usage  aci-tutorial-sidecar  1.43565e+06
2018-04-22 16:20:00  Memory Usage  aci-tutorial-sidecar  1.408e+06
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Omówienie monitorowania na platformie Azure][azure-monitoring], aby dowiedzieć się więcej na temat monitorowania na platformie Azure.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - Internal -->
[azure-monitoring]: ../monitoring-and-diagnostics/monitoring-overview.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
