---
title: Informacje o monitorowaniu danych | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej na temat danych i zasobów zebranych dla Azure Machine Learning i dostępnych w Azure Monitor. Azure Monitor zbiera i przedstawia dane dotyczące obszaru roboczego Azure Machine Learning, a następnie pozwala wyświetlać metryki, ustawiać alerty i analizować zarejestrowane dane.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/06/2019
ms.openlocfilehash: 178c60d82c8337987e9858a409fcdef7063c9559
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684915"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Informacje o monitorowaniu usługi Azure Machine Learning

Dowiedz się więcej o danych i zasobach zbieranych przez Azure Monitor z obszaru roboczego Azure Machine Learning. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .

## <a name="resource-logs"></a>Dzienniki zasobów

W poniższej tabeli wymieniono właściwości dzienników zasobów Azure Machine Learning, które są zbierane w Azure Monitor dziennikach lub usłudze Azure Storage.

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Właściwość | Opis |
|:--- |:---|
| TimeGenerated | Godzina wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| JobId | Identyfikator przesłanego zadania |
| ExperimentId | Identyfikator eksperymentu |
| eksperymentname | Nazwa eksperymentu |
| customerSubscriptionId | Identyfikator subskrypcji, w której eksperyment i zadanie zostały przesłane |
| workspaceName | Nazwa obszaru roboczego uczenia maszynowego |
| clusterName | Nazwa klastra |
| ProvisioningState | Stan przesłania zadania |
| ResourceGroupName | Nazwa grupy zasobów |
| JobName | Nazwa zadania |
| ClusterId | Identyfikator klastra |
| Klasę | Typ zdarzenia zadania, np., JobSubmitted, JobRunning, JobFailed, JobSucceeded itd. |
| ExecutionState | Stan zadania (przebieg), np., w kolejce, uruchomione, zakończone powodzeniem, zakończone niepowodzeniem |
| ErrorDetails | Szczegóły błędu zadania |
| CreationApiVersion | Wersja interfejsu API użyta do utworzenia zadania |
| ClusterResourceGroupName | Nazwa grupy zasobów klastra |
| TFWorkerCount | Liczba procesów roboczych TF |
| TFParameterServerCount | Liczba serwerów parametrów TF |
| — Narzędzie | Typ używanego narzędzia |
| RunInContainer | Flaga opisująca, czy zadanie powinno być uruchamiane wewnątrz kontenera |
| JobErrorMessage | szczegółowy komunikat o błędzie zadania |
| NodeId | IDENTYFIKATOR węzła utworzonego w przypadku, gdy zadanie jest uruchomione |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated | Godzina wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| ProvisioningState | Stan aprowizacji klastra |
| clusterName | Nazwa klastra |
| clustertype | Typ klastra |
| CreatedBy | Użytkownik, który utworzył klaster |
| CoreCount | Liczba rdzeni w klastrze |
| vmSize | Rozmiar maszyny wirtualnej klastra |
| VmPriority | Priorytet węzłów utworzonych w klastrze dedykowanym/LowPriority |
| Skalowanie | Typ ręcznego skalowania klastra/automatycznego |
| InitialNodeCount | Początkowa liczba węzłów klastra |
| MinimumNodeCount | Minimalna liczba węzłów klastra |
| MaximumNodeCount | Maksymalna liczba węzłów klastra |
| NodeDeallocationOption | Jak ma zostać cofnięty przydział węzła |
| Publisher | Wydawca typu klastra |
| Oferta | Oferta, w której tworzony jest klaster |
| Jednostka SKU | Jednostka SKU utworzonego węzła/maszyny wirtualnej w klastrze |
| Wersja | Wersja obrazu używana podczas tworzenia węzła/maszyny wirtualnej |
| SubnetId | SubnetId klastra |
| AllocationState | Stan alokacji klastra |
| CurrentNodeCount | Bieżąca liczba węzłów klastra |
| TargetNodeCount | Liczba węzłów docelowych klastra podczas skalowania w górę/w dół |
| Klasę | Typ zdarzenia podczas tworzenia klastra. |
| NodeIdleTimeSecondsBeforeScaleDown | Czas bezczynności (w sekundach), po którym klaster będzie skalowany w dół |
| PreemptedNodeCount | Liczba przeniesiona węzłów klastra |
| IsResizeGrow | Flaga oznaczająca, że klaster jest skalowany w górę |
| VmFamilyName | Nazwa rodziny maszyn wirtualnych węzłów, które mogą zostać utworzone w klastrze |
| LeavingNodeCount | Pozostawienie liczby węzłów klastra |
| UnusableNodeCount | Niezdatna do użycia liczba węzłów klastra |
| IdleNodeCount | Liczba węzłów bezczynności klastra |
| RunningNodeCount | Liczba uruchomionych węzłów klastra |
| PreparingNodeCount | Przygotowywanie liczby węzłów klastra |
| QuotaAllocated | Przydzielono przydział do klastra |
| QuotaUtilized | Wykorzystany limit przydziału klastra |
| AllocationStateTransitionTime | Czas przejścia z jednego stanu do innego |
| ClusterErrorCodes | Kod błędu otrzymany podczas tworzenia lub skalowania klastra |
| CreationApiVersion | Wersja interfejsu API użyta podczas tworzenia klastra |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated | Godzina wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| clusterName | Nazwa klastra |
| NodeId | Identyfikator utworzonego węzła klastra |
| vmSize | Rozmiar maszyny wirtualnej węzła |
| VmFamilyName | Rodzina maszyn wirtualnych, do której należy węzeł |
| VmPriority | Priorytet utworzonego węzła/LowPriority |
| Publisher | Wydawca obrazu maszyny wirtualnej, np. Microsoft-dsvm |
| Oferta | Oferta skojarzona z tworzeniem maszyny wirtualnej |
| Jednostka SKU | Utworzono jednostkę SKU węzła/maszyny wirtualnej |
| Wersja | Wersja obrazu używana podczas tworzenia węzła/maszyny wirtualnej |
| ClusterCreationTime | Czas utworzenia klastra |
| ResizeStartTime | Czas rozpoczęcia skalowania klastra w górę/w dół |
| ResizeEndTime | Czas zakończenia skalowania klastra w górę/w dół |
| NodeAllocationTime | Czas przydzielenia węzła |
| NodeBootTime | Czas uruchomienia węzła |
| StartTaskStartTime | Godzina, o której zadanie zostało przypisane do węzła i uruchomione |
| StartTaskEndTime | Czas zakończenia zadania przypisanego do węzła |
| TotalE2ETimeInSeconds | Łączny węzeł czasu był aktywny |

### <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla Azure Machine Learning wszystkie metryki są przechowywane w **obszarze nazw Azure Machine Learning obszarze roboczym usługi**.

**Wzorów**

| Modelowanie | Jednostka | Opis |
| ----- | ----- | ----- |
| Nie można wdrożyć modelu | Licznik | Liczba wdrożeń modelu zakończonych niepowodzeniem. |
| Rozpoczęto Wdrażanie modelu | Licznik | Liczba uruchomionych wdrożeń modelu. |
| Wdrażanie modelu powiodło się | Licznik | Liczba wdrożeń modelu, które zakończyły się powodzeniem. |
| Nie można zarejestrować modelu | Licznik | Liczba rejestracji modelu zakończonych niepowodzeniem. |
| Rejestrowanie modelu powiodło się | Licznik | Liczba rejestracji modelu zakończonych powodzeniem. |

**Limit przydziału**

Informacje o limicie przydziału są przeznaczone tylko dla Azure Machine Learning obliczeń.

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Aktywne rdzenie | Licznik | Liczba aktywnych rdzeni obliczeniowych. |
| Aktywne węzły | Licznik | Liczba aktywnych węzłów. |
| Rdzenie bezczynne | Licznik | Liczba rdzeni obliczeniowych w bezczynnym stanie. |
| Węzły bezczynne | Licznik | Liczba bezczynnych węzłów obliczeniowych. |
| Opuszczanie rdzeni | Licznik | Liczba rdzeni opuszczających. |
| Opuszczanie węzłów | Licznik | Liczba pozostałych węzłów. |
| Występujące rdzenie | Licznik | Liczba przeniesiona rdzeni. |
| Zastępujące węzły | Licznik | Liczba przeniesiona węzłów. |
| Procent wykorzystania przydziałów | Procent | Procent użytego przydziału. |
| Całkowita liczba rdzeni | Licznik | Łączna liczba rdzeni. |
| Łączna liczba węzłów | Licznik | Łączna liczba węzłów. |
| Rdzenie, których nie można używać | Licznik | Liczba rdzeni, których nie można używać. |
| Węzły niezdatne do użytku | Licznik | Liczba węzłów, które nie są używane. |

Poniżej znajdują się wymiary, których można użyć do filtrowania metryk przydziału:

| Wymiar | Metryki dostępne z | Opis |
| ---- | ---- | ---- |
| Nazwa klastra | Wszystkie metryki przydziału | Nazwa wystąpienia obliczeniowego. |
| Nazwa rodziny maszyn wirtualnych | Procent wykorzystania przydziałów | Nazwa rodziny maszyn wirtualnych używanej przez klaster. |
| Priorytet maszyny wirtualnej | Procent wykorzystania przydziałów | Priorytet maszyny wirtualnej.

**Wykonane**

Informacje o przebiegach szkoleniowych.

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Ukończone uruchomienia | Licznik | Liczba ukończonych uruchomień. |
| Nieudane uruchomienia | Licznik | Liczba nieudanych uruchomień. |
| Uruchomione uruchomienia | Licznik | Liczba rozpoczętych uruchomień. |

Poniżej znajdują się wymiary, których można użyć do filtrowania metryk przebiegu:

| Wymiar | Opis |
| ---- | ---- |
| Computetype | Typ obliczeń używany do uruchomienia. |
| PipelineStepType | Typ [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) używany w przebiegu. |
| PublishedPipelineId | Identyfikator opublikowanego potoku używanego w przebiegu. |
| RunType | Typ uruchomienia. |

Prawidłowe wartości dla wymiaru RunType są następujące:

| Wartość | Opis |
| ----- | ----- |
| Doświadczenia | Uruchomienia bez potoku. |
| PipelineRun | Uruchomienie potoku, które jest elementem nadrzędnym elementu StepRun. |
| StepRun | Przebieg kroku potoku. |
| ReusedStepRun | Przebieg kroku potoku, który ponownie używa poprzedniego przebiegu. |

## <a name="see-also"></a>Zobacz też

- Aby uzyskać opis Azure Machine Learning monitorowania, zobacz [Azure Machine Learning monitorowania](monitor-azure-machine-learning.md) .
- Aby uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure, zobacz [monitorowanie zasobów platformy Azure za pomocą Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .
