---
title: Monitorowanie usługi Azure Machine Learning | Dokumenty firmy Microsoft
description: Dowiedz się, jak używać usługi Azure Monitor do wyświetlania, analizowania i tworzenia alertów dotyczących metryk z usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399109"
---
# <a name="monitoring-azure-machine-learning"></a>Monitorowanie usługi Azure Machine Learning

W tym artykule opisano dane monitorowania generowane przez usługę Azure Machine Learning. Opisano w nim również, jak można używać usługi Azure Monitor do analizowania danych i definiowania alertów.

> [!TIP]
> Informacje zawarte w tym dokumencie są przeznaczone głównie dla administratorów, ponieważ opisano monitorowanie usługi Azure Machine Learning. Jeśli jesteś analitykiem danych lub deweloperem i chcesz monitorować informacje specyficzne dla przebiegów szkolenia modelu, zobacz następujące dokumenty:
>
> * [Uruchamianie, monitorowanie i anulowanie przebiegów szkoleniowych](how-to-manage-runs.md)
> * [Rejestrowanie metryk dla przebiegów treningowych](how-to-track-experiments.md)
> * [Śledzenie eksperymentów za pomocą platformy MLflow](how-to-use-mlflow.md)
> * [Wizualizacja przebiegów za pomocą narzędzia TensorBoard](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Usługa Azure Machine Learning rejestruje dane monitorowania przy użyciu usługi Azure Monitor, która jest usługą monitorowania pełnego stosu na platformie Azure. Usługa Azure Monitor udostępnia kompletny zestaw funkcji do monitorowania zasobów platformy Azure. Może również monitorować zasoby w innych chmurach i lokalnie.

Zacznij od artykułu [Omówienie usługi Azure Monitor](/azure/azure-monitor/overview), który zawiera omówienie możliwości monitorowania. Poniższe sekcje opierają się na tych informacjach, podając szczegółowe informacje dotyczące korzystania z usługi Azure Monitor za pomocą usługi Azure Machine Learning.

Aby zrozumieć koszty związane z usługą Azure Monitor, zobacz [Użycie i szacowane koszty](/azure/azure-monitor/platform/usage-estimated-costs). Aby zrozumieć czas potrzebny na wyświetlenie danych w usłudze Azure Monitor, zobacz [Rejestrowanie czasu pozyskiwania danych.](/azure/azure-monitor/platform/data-ingestion-time)

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorowanie danych z usługi Azure Machine Learning

Usługa Azure Machine Learning zbiera te same rodzaje danych monitorowania, co inne zasoby platformy Azure, które są opisane w [obszarze Monitorowanie danych z zasobów platformy Azure.](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data) Zobacz [odwołanie do danych monitorowania usługi Azure Machine Learning, aby](monitor-resource-reference.md) uzyskać szczegółowe informacje na temat dzienników i metryk utworzonych przez usługę Azure Machine Learning.

## <a name="analyzing-metric-data"></a>Analizowanie danych metrycznych

Możesz analizować metryki usługi Azure Machine Learning, otwierając **metryki** z menu **Monitor platformy Azure.** Zobacz [Wprowadzenie do Eksploratora metryk platformy Azure, aby](/azure/azure-monitor/platform/metrics-getting-started) uzyskać szczegółowe informacje na temat korzystania z tego narzędzia.

Wszystkie metryki usługi Azure Machine Learning znajdują się w obszarze roboczym **usługi uczenia maszynowego.**

![Eksplorator metryk z wybraną przestrzenią roboczą usługi uczenia maszynowego](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrowanie i dzielenie

W przypadku metryk obsługujących wymiary można zastosować filtry przy użyciu wartości wymiaru. Na przykład filtrowanie **aktywnych rdzeni** dla `cpu-cluster`nazwy **klastra** . 

Można również podzielić metrykę według wymiaru, aby wizualizować porównanie różnych segmentów metryki ze sobą. Na przykład dzielenie **się typem kroku potoku,** aby wyświetlić liczbę typów kroków używanych w potoku.

Aby uzyskać więcej informacji na temat filtrowania i dzielenia, zobacz [Zaawansowane funkcje usługi Azure Monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Alerty

Dostęp do alertów dla usługi Azure Machine Learning można uzyskać, otwierając **alerty** z menu **Monitor platformy Azure.** Zobacz [Tworzenie, wyświetlanie i zarządzanie alertami metryk przy użyciu usługi Azure Monitor, aby](/azure/azure-monitor/platform/alerts-metric) uzyskać szczegółowe informacje na temat tworzenia alertów.

W poniższej tabeli wymieniono typowe i zalecane reguły alertów metryki dla usługi Azure Machine Learning:

| Typ alertu | Warunek | Opis |
|:---|:---|:---|
| Wdrażanie modelu nie powiodło się | Typ agregacji: Suma, Operator: Większa niż, Wartość progu: 0 | Gdy co najmniej jedno wdrożenie modelu nie powiodło się |
| Procent wykorzystania przydziału | Typ agregacji: Średnia, Operator: Większa niż, Wartość progowa: 90| Gdy procent wykorzystania przydziału jest większy niż 90% |
| Niezniszalne węzły | Typ agregacji: Suma, Operator: Większa niż, Wartość progu: 0 | Gdy istnieje jeden lub więcej węzłów bezużytecznych |

## <a name="configuration"></a>Konfigurowanie

> [!IMPORTANT]
> __Metryki usługi Azure Machine Learning nie muszą być skonfigurowane,__ są zbierane automatycznie i są dostępne w Eksploratorze metryk do monitorowania i alertów.

Można dodać ustawienie diagnostyczne, aby skonfigurować następujące funkcje:

* Archiwizuj informacje o dzienniku i metrykach na koncie magazynu platformy Azure.
* Przesyłanie strumieniowe informacji dziennika i metryki do usługi Azure Event Hub.
* Wysyłanie informacji o dziennikach i metrykach do usługi Azure Monitor Log Analytics.

Włączenie tych ustawień wymaga dodatkowych usług platformy Azure (konta magazynu, centrum zdarzeń lub usługi Log Analytics), co może zwiększyć koszt. Aby obliczyć szacowany koszt, odwiedź [kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator).

Aby uzyskać więcej informacji na temat tworzenia ustawienia diagnostycznego, zobacz [Tworzenie ustawień diagnostycznych do zbierania dzienników i metryk platformy](/azure/azure-monitor/platform/diagnostic-settings)na platformie Azure .

Można skonfigurować następujące dzienniki dla usługi Azure Machine Learning:

| Kategoria | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z klastrów obliczeniowych usługi Azure Machine Learning. |
| AmlComputeClusterNodeevent | Zdarzenia z węzłów w klastrze obliczeniowym usługi Azure Machine Learning. |
| AmlComputeJobevent | Zdarzenia z zadań uruchomionych na podstawie obliczeń usługi Azure Machine Learning. |

> [!NOTE]
> Po włączeniu metryki w ustawieniach diagnostycznych informacje o wymiarach nie są obecnie uwzględniane jako część informacji wysyłanych do konta magazynu, centrum zdarzeń lub analizy dzienników.

## <a name="analyzing-log-data"></a>Analizowanie danych dziennika

Korzystanie z usługi Azure Monitor Log Analytics wymaga utworzenia konfiguracji diagnostycznej i włączenia __wysyłania informacji do usługi Log Analytics.__ Aby uzyskać więcej informacji, zobacz [sekcję Konfiguracja.](#configuration)

Dane w dziennikach usługi Azure Monitor są przechowywane w tabelach, a każda tabela ma własny zestaw unikatowych właściwości. Usługa Azure Machine Learning przechowuje dane w następujących tabelach:

| Tabela | Opis |
|:---|:---|
| AmlComputeClusterEvent | Zdarzenia z klastrów obliczeniowych usługi Azure Machine Learning. |
| AmlComputeClusterNodeevent | Zdarzenia z węzłów w klastrze obliczeniowym usługi Azure Machine Learning. |
| AmlComputeJobevent | Zdarzenia z zadań uruchomionych na podstawie obliczeń usługi Azure Machine Learning. |

> [!IMPORTANT]
> Po wybraniu **dzienników** z menu azure machine learning usługa Log Analytics jest otwierana z ustawionym zakresem zapytania na bieżący obszar roboczy. Oznacza to, że kwerendy dziennika będzie zawierać tylko dane z tego zasobu. Jeśli chcesz uruchomić kwerendę, która zawiera dane z innych baz danych lub danych z innych usług platformy Azure, wybierz **dzienniki** z menu **Usługi Azure Monitor.** Zobacz [zakres zapytań dziennika i zakres czasu w usłudze Azure Monitor Log Analytics, aby](/azure/azure-monitor/log-query/scope/) uzyskać szczegółowe informacje.

Aby uzyskać szczegółowe informacje na temat dzienników i metryk, zobacz [odwołanie do danych monitorowania usługi Azure Machine Learning](monitor-resource-reference.md).

### <a name="sample-queries"></a>Przykładowe zapytania

Poniżej przedstawiono kwerendy, których można użyć, aby ułatwić monitorowanie zasobów usługi Azure Machine Learning: 

+ Uzyskaj zadania nie powiodło się w ciągu ostatnich pięciu dni:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Pobierz rekordy dla określonej nazwy zadania:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Pobierz zdarzenia klastra w ciągu ostatnich pięciu dni dla klastrów, w których rozmiar maszyny Wirtualnej jest Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Pobierz węzły przydzielone w ciągu ostatnich ośmiu dni:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać odwołanie do dzienników i metryk, zobacz [odwołanie do danych monitorowania usługi Azure Machine Learning](monitor-resource-reference.md).
- Aby uzyskać informacje dotyczące pracy z przydziałami powiązanymi z usługą Azure Machine Learning, zobacz [Zarządzanie przydziałami i żądaniawanie zasobów platformy Azure.](how-to-manage-quotas.md)
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [Monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor.](/azure/azure-monitor/insights/monitor-azure-resource)
