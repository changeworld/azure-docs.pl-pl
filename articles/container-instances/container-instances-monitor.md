---
title: Monitorowanie kontenerów w usłudze Azure Container Instances
description: Jak monitorować użycie zasobów obliczeniowych, takich jak procesor CPU i pamięci przez kontenerów w usłudze Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 04/24/2019
ms.author: danlep
ms.openlocfilehash: 7b46ea0518038eeb908591b8438acc2a9095242c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570892"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorowanie zasobów kontenerów w usłudze Azure Container Instances

[Usługa Azure Monitor] [ azure-monitoring] zapewnia wgląd w zasoby obliczeniowe używane przez kontenery wystąpień. Dane dotyczące użytkowania zasobów ułatwia określenie najlepsze ustawienia zasobu dla grupy kontenerów. Usługa Azure Monitor udostępnia metryki, które śledzą działanie sieci wystąpienia kontenera w.

Ten dokument wyszczególnia zbieranie metryk usługi Azure Monitor dla container instances za pomocą witryny Azure portal i interfejsu wiersza polecenia platformy.

> [!IMPORTANT]
> Metryki usługi Azure Monitor w usłudze Azure Container Instances są obecnie dostępne w wersji zapoznawczej, a niektóre [ograniczenia](#preview-limitations). Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W tej chwili metryk usługi Azure Monitor są dostępne tylko dla kontenerów systemu Linux.

## <a name="available-metrics"></a>Dostępne metryki

Usługa Azure Monitor udostępnia następujące [metryki dla usługi Azure Container Instances][supported-metrics]. Te metryki są dostępne dla grupy kontenerów i poszczególnych kontenerów.

* **Użycie procesora CPU** — pomiar **millicores**. Jeden millicore to 1/1000th rdzenia procesora CPU, więc 500 millicores (lub 500 mln) reprezentuje 50% użycia rdzenia procesora CPU. Agregowane jako **średnie użycie** ze wszystkich rdzeni.

* **Użycie pamięci** — zagregowane jako **średnie bajty**.

* **Sieci bajtów odebranych na sekundę** i **sieci bajtów wysłanych na sekundę** — zagregowane jako **średnia liczba bajtów na sekundę**. 

## <a name="get-metrics---azure-portal"></a>Pobieranie metryk — Azure Portal

Po utworzeniu grupy kontenerów dane usługi Azure Monitor są dostępne w witrynie Azure Portal. Aby wyświetlić metryki dla grupy kontenerów, przejdź do **Przegląd** stronie dla grupy kontenerów. Tutaj można zobaczyć wykresy utworzone wcześniej dla każdego z dostępnych metryk.

![podwójny wykres][dual-chart]

W grupie kontenerów, która zawiera wiele kontenerów, użyj [wymiaru] [ monitor-dimension] do przedstawienia metryk przez kontener. Aby utworzyć wykres z metrykami pojedynczego kontenera, wykonaj następujące kroki:

1. W **Przegląd** wybierz jeden z wykresów metryki, takie jak **Procesora**. 
1. Wybierz **zastosować podział** przycisk, a następnie wybierz pozycję **nazwa kontenera**.

![wymiar][dimension]

## <a name="get-metrics---azure-cli"></a>Pobieranie metryk — interfejs wiersza polecenia platformy Azure

Metryki dla wystąpienia kontenera można również gromadzić przy użyciu wiersza polecenia platformy Azure. Najpierw pobierz identyfikator grupy kontenerów za pomocą następującego polecenia. Zastąp element `<resource-group>` nazwą grupy zasobów, a element `<container-group>` zastąp nazwą grupy kontenerów.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Użyj następującego polecenia, aby pobrać metryki użycia **procesora**.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table

Timestamp            Name       Average
-------------------  ---------  ---------
2019-04-23 22:59:00  CPU Usage
2019-04-23 23:00:00  CPU Usage
2019-04-23 23:01:00  CPU Usage  0.0
2019-04-23 23:02:00  CPU Usage  0.0
2019-04-23 23:03:00  CPU Usage  0.5
2019-04-23 23:04:00  CPU Usage  0.5
2019-04-23 23:05:00  CPU Usage  0.5
2019-04-23 23:06:00  CPU Usage  1.0
2019-04-23 23:07:00  CPU Usage  0.5
2019-04-23 23:08:00  CPU Usage  0.5
2019-04-23 23:09:00  CPU Usage  1.0
2019-04-23 23:10:00  CPU Usage  0.5
```

Zmień wartość właściwości `--metric` parametr w poleceniu, aby uzyskać inne [metryki obsługiwane][supported-metrics]. Na przykład, użyj następującego polecenia, aby uzyskać **pamięci** metryk użycia. 

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table

Timestamp            Name          Average
-------------------  ------------  ----------
2019-04-23 22:59:00  Memory Usage
2019-04-23 23:00:00  Memory Usage
2019-04-23 23:01:00  Memory Usage  0.0
2019-04-23 23:02:00  Memory Usage  8859648.0
2019-04-23 23:03:00  Memory Usage  9181184.0
2019-04-23 23:04:00  Memory Usage  9580544.0
2019-04-23 23:05:00  Memory Usage  10280960.0
2019-04-23 23:06:00  Memory Usage  7815168.0
2019-04-23 23:07:00  Memory Usage  7739392.0
2019-04-23 23:08:00  Memory Usage  8212480.0
2019-04-23 23:09:00  Memory Usage  8159232.0
2019-04-23 23:10:00  Memory Usage  8093696.0
```

Dla grupy wielu kontenerów `containerName` wymiar można dodać do zwrócenia metryki dla kontenera.

```console
$ az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table

Timestamp            Name          Containername             Average
-------------------  ------------  --------------------  -----------
2019-04-23 22:59:00  Memory Usage  aci-tutorial-app
2019-04-23 23:00:00  Memory Usage  aci-tutorial-app
2019-04-23 23:01:00  Memory Usage  aci-tutorial-app      0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-app      16834560.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-app      17534976.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-app      18329600.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-app      19742720.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-app      14786560.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-app      14651392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-app      15470592.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-app      15450112.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-app      15339520.0
2019-04-23 22:59:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:00:00  Memory Usage  aci-tutorial-sidecar
2019-04-23 23:01:00  Memory Usage  aci-tutorial-sidecar  0.0
2019-04-23 23:02:00  Memory Usage  aci-tutorial-sidecar  884736.0
2019-04-23 23:03:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:04:00  Memory Usage  aci-tutorial-sidecar  831488.0
2019-04-23 23:05:00  Memory Usage  aci-tutorial-sidecar  819200.0
2019-04-23 23:06:00  Memory Usage  aci-tutorial-sidecar  843776.0
2019-04-23 23:07:00  Memory Usage  aci-tutorial-sidecar  827392.0
2019-04-23 23:08:00  Memory Usage  aci-tutorial-sidecar  954368.0
2019-04-23 23:09:00  Memory Usage  aci-tutorial-sidecar  868352.0
2019-04-23 23:10:00  Memory Usage  aci-tutorial-sidecar  847872.0
```

## <a name="next-steps"></a>Kolejne kroki

Zobacz [Omówienie monitorowania na platformie Azure][azure-monitoring], aby dowiedzieć się więcej na temat monitorowania na platformie Azure.

Dowiedz się, jak utworzyć [alertów dotyczących metryk] [ metric-alert] powiadomieniom Metryka dla usługi Azure Container Instances przekracza próg.

<!-- IMAGES -->
[cpu-chart]: ./media/container-instances-monitor/cpu-multi.png
[dimension]: ./media/container-instances-monitor/dimension.png
[dual-chart]: ./media/container-instances-monitor/metrics.png
[memory-chart]: ./media/container-instances-monitor/memory-multi.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-monitoring]: ../azure-monitor/overview.md
[metric-alert]: ..//azure-monitor/platform/alerts-metric.md
[monitor-dimension]: ../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics
[supported-metrics]: ../azure-monitor/platform/metrics-supported.md#microsoftcontainerinstancecontainergroups
