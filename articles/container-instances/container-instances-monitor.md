---
title: Monitorowanie wystąpień kontenerów
description: Jak monitorować zużycie zasobów obliczeniowych, takich jak procesor i pamięć przez kontenery w wystąpieniach kontenerów platformy Azure.
ms.topic: article
ms.date: 04/24/2019
ms.openlocfilehash: b4a66254c18d7e01b6d56e64e6b62721b620d499
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250036"
---
# <a name="monitor-container-resources-in-azure-container-instances"></a>Monitorowanie zasobów kontenerów w usłudze Azure Container Instances

[Usługa Azure Monitor][azure-monitoring] zapewnia wgląd w zasoby obliczeniowe używane przez wystąpienia kontenerów. Te dane użycia zasobów pomaga określić najlepsze ustawienia zasobów dla grup kontenerów. Usługa Azure Monitor udostępnia również metryki, które śledzą aktywność sieciową w wystąpieniach kontenera.

Ten dokument zawiera szczegółowe informacje o zbieraniu metryk usługi Azure Monitor dla wystąpień kontenerów przy użyciu witryny Azure portal i interfejsu wiersza polecenia platformy Azure.

> [!IMPORTANT]
> Metryki usługi Azure Monitor w wystąpieniach kontenerów platformy Azure są obecnie w wersji zapoznawczej i obowiązują pewne [ograniczenia.](#preview-limitations) Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania][terms-of-use]. Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna.

## <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

W tej chwili metryki usługi Azure Monitor są dostępne tylko dla kontenerów systemu Linux.

## <a name="available-metrics"></a>Dostępne metryki

Usługa Azure Monitor udostępnia następujące [metryki dla wystąpień kontenerów platformy Azure.][supported-metrics] Te metryki są dostępne dla grupy kontenerów i poszczególnych kontenerów.

* **Użycie procesora** - mierzone w **milikores**. Jeden milicyr jest 1/1000th rdzenia procesora, więc 500 milikores (lub 500 m) stanowi 50% wykorzystania rdzenia procesora. Zagregowane jako **średnie użycie** we wszystkich rdzeniach.

* **Użycie pamięci** — agregowane jako **średnie bajty**.

* **Bajty sieciowe odebrane na sekundę** i **bajty sieciowe przesyłane na sekundę** — agregowane jako średnie **bajty na sekundę**. 

## <a name="get-metrics---azure-portal"></a>Pobieranie metryk — Azure Portal

Po utworzeniu grupy kontenerów dane usługi Azure Monitor są dostępne w witrynie Azure Portal. Aby wyświetlić metryki dla grupy kontenerów, przejdź do strony **Przegląd** dla grupy kontenerów. Tutaj możesz zobaczyć wstępnie utworzone wykresy dla każdego z dostępnych danych.

![podwójny wykres][dual-chart]

W grupie kontenerów, która zawiera wiele kontenerów, należy użyć [wymiaru][monitor-dimension] do przedstawienia metryk według kontenera. Aby utworzyć wykres z metrykami pojedynczego kontenera, wykonaj następujące kroki:

1. Na stronie **Przegląd** wybierz jeden z wykresów metrycznych, takich jak **PROCESOR .** 
1. Wybierz przycisk **Zastosuj podział** i wybierz pozycję **Nazwa kontenera**.

![wymiar][dimension]

## <a name="get-metrics---azure-cli"></a>Pobieranie metryk — interfejs wiersza polecenia platformy Azure

Metryki dla wystąpień kontenerów można również zbierać przy użyciu interfejsu wiersza polecenia platformy Azure. Najpierw pobierz identyfikator grupy kontenerów za pomocą następującego polecenia. Zastąp element `<resource-group>` nazwą grupy zasobów, a element `<container-group>` zastąp nazwą grupy kontenerów.


```console
CONTAINER_GROUP=$(az container show --resource-group <resource-group> --name <container-group> --query id --output tsv)
```

Użyj następującego polecenia, aby pobrać metryki użycia **procesora**.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric CPUUsage --output table
```

```output
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

Zmień wartość parametru `--metric` w poleceniu, aby uzyskać inne obsługiwane [metryki][supported-metrics]. Na przykład użyj następującego polecenia, aby uzyskać metryki użycia **pamięci.** 

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --output table
```

```output
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

Dla grupy wielu kontenerów `containerName` wymiar można dodać do zwracania metryk na kontener.

```azurecli
az monitor metrics list --resource $CONTAINER_GROUP --metric MemoryUsage --dimension containerName --output table
```

```output
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

## <a name="next-steps"></a>Następne kroki

Zobacz [Omówienie monitorowania na platformie Azure][azure-monitoring], aby dowiedzieć się więcej na temat monitorowania na platformie Azure.

Dowiedz się, jak tworzyć [alerty metryki,][metric-alert] aby otrzymywać powiadomienia, gdy metryka dla wystąpień kontenerów platformy Azure przekroczy próg.

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
