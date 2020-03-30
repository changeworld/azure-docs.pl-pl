---
title: Charakterystyka danych z monitoringu | Dokumenty firmy Microsoft
titleSuffix: Azure Machine Learning
description: Dowiedz się więcej o danych i zasobach zebranych dla usługi Azure Machine Learning i dostępnych w usłudze Azure Monitor. Usługa Azure Monitor zbiera i wyświetla dane dotyczące obszaru roboczego usługi Azure Machine Learning i umożliwia wyświetlanie metryk, ustawianie alertów i analizowanie zarejestrowanych danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927566"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Odwołanie do danych monitorowania uczenia maszynowego platformy Azure

Dowiedz się więcej o danych i zasobach zebranych przez usługę Azure Monitor z obszaru roboczego usługi Azure Machine Learning. Zobacz [monitorowanie usługi Azure Machine Learning, aby](monitor-azure-machine-learning.md) uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania.

## <a name="resource-logs"></a>Dzienniki zasobów

W poniższej tabeli wymieniono właściwości dzienników zasobów usługi Azure Machine Learning, gdy są one zbierane w dziennikach usługi Azure Monitor lub usłudze Azure Storage.

### <a name="amlcomputejobevents-table"></a>Tabela AmlComputeJobEvents

| Właściwość | Opis |
|:--- |:---|
| TimeGenerated | Czas wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| JobId | Identyfikator przesłanego zadania |
| Identyfikator eksperymentu | Identyfikator eksperymentu |
| Nazwa eksperymentu | Nazwa eksperymentu |
| Identyfikator klienta | Identyfikator subskrypcji, na którym przesłano eksperyment i zadanie |
| Nazwa obszaru roboczego | Nazwa obszaru roboczego uczenia maszynowego |
| NazwaKlastra | Nazwa klastra |
| Stan inicjowania obsługi administracyjnej | Stan składania ofert pracy |
| ResourceGroupName | Nazwa grupy zasobów |
| Jobname | Nazwa zadania |
| Ład klastra | Identyfikator klastra |
| Typ zdarzenia | Typ zdarzenia Job, np. |
| ExecutionState | Stan zadania (uruchom), np. |
| ErrorDetails | Szczegóły błędu zadania |
| KreacjaApiWersja | Wersja interfejsu API używana do tworzenia zadania |
| Nazwa grupy clusterresource | Nazwa grupy zasobów klastra |
| TFWorkerCount (Liczba pracowników) | Liczba pracowników TF |
| TFParameterServerCount | Liczba serwerów parametrów TF |
| Typ narzędzia | Rodzaj używanego narzędzia |
| RunInContainer (Niem. | Flaga opisująca, czy zadanie powinno być uruchamiane wewnątrz kontenera |
| JobErrorMessage | szczegółowy komunikat o błędzie zadania |
| Identyfikator węzła | Identyfikator węzła utworzonego w miejscu, w którym działa zadanie |

### <a name="amlcomputeclusterevents-table"></a>Tabela AmlComputeClusterEvents

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated | Czas wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| Stan inicjowania obsługi administracyjnej | Stan inicjowania obsługi administracyjnej klastra |
| NazwaKlastra | Nazwa klastra |
| Typ klastra | Typ klastra |
| Createdby | Użytkownik, który utworzył klaster |
| Liczba rdzeni | Liczba rdzeni w klastrze |
| Rozmiar maszyny Wirtualnej | Rozmiar maszyny wirtualnej klastra |
| VmPriority (VmPriority) | Priorytet węzłów utworzonych wewnątrz klastra Dedykowana/NiskaPriority |
| Typ skalowania | Typ ręcznego/automatycznego skalowania klastra |
| InitialNodeCount (InitialNodeCount) | Początkowa liczba węzłów klastra |
| Minimalny minimalny licznik | Minimalna liczba węzłów klastra |
| Maksymalna liczba | Maksymalna liczba węzłów klastra |
| WęzełDealocationOption | Jak węzeł powinien zostać cofnięty |
| Wydawca | Wydawca typu klastra |
| Oferta | Oferta, z którą jest tworzony klaster |
| SKU | Sku węzła/maszyny wirtualnej utworzonej wewnątrz klastra |
| Wersja | Wersja obrazu używanego podczas tworzenia węzła/maszyny wirtualnej |
| PodsiećId | Podsieć klastra |
| Stan alokacji | Stan alokacji klastra |
| CurrentNodeCount (CurrentNodeCount) | Bieżąca liczba węzłów klastra |
| Liczba docelowa | Liczba węzłów docelowych klastra podczas skalowania w górę/w dół |
| Typ zdarzenia | Typ zdarzenia podczas tworzenia klastra. |
| NodeIdleTimeSecondsBeforeScaleDown | Czas bezczynności w sekundach przed skalowaniem klastra w dół |
| WywłaszczoneNodeCount | Wywłaszczona liczba węzłów klastra |
| IsResizeGrow (IsResizeGrow) | Flaga wskazująca, że klaster jest skalowanie w górę |
| Nazwa VmFamilyname | Nazwa rodziny maszyn wirtualnych węzłów, które można utworzyć wewnątrz klastra |
| LeavingNodeCount | Opuszczanie liczby węzłów klastra |
| BezużytecznyNodeCount | Niezniszcząca liczba węzłów klastra |
| Liczba IdleNodeCount | Liczba węzłów bezczynności klastra |
| RunningNodeCount (RunningNodeCount) | Uruchamianie liczby węzłów klastra |
| PrzygotowanieNodeCount | Przygotowywanie liczby węzłów klastra |
| Przydział Przydziału Alokacja | Przydzielony przydział do klastra |
| PrzydziałUtilized | Wykorzystano przydział klastra |
| Czastransitionu alokacji | Czas przejścia z jednego stanu do drugiego |
| Kody clustererorcodes | Kod błędu odebrany podczas tworzenia lub skalowania klastra |
| KreacjaApiWersja | Wersja interfejsu API używana podczas tworzenia klastra |

### <a name="amlcomputeclusternodeevents-table"></a>Tabela AmlComputeClusterNodeEvents

| Właściwość | Opis |
|:--- |:--- |
| TimeGenerated | Czas wygenerowania wpisu dziennika |
| OperationName | Nazwa operacji skojarzonej ze zdarzeniem dziennika |
| Kategoria | Nazwa zdarzenia dziennika, AmlComputeClusterNodeEvent |
| NazwaKlastra | Nazwa klastra |
| Identyfikator węzła | Identyfikator utworzonego węzła klastra |
| Rozmiar maszyny Wirtualnej | Rozmiar maszyny wirtualnej węzła |
| Nazwa VmFamilyname | Rodzina maszyn wirtualnych, do której należy węzeł |
| VmPriority (VmPriority) | Priorytet utworzonego węzła Dedykowana/NiskaPriority |
| Wydawca | Wydawca obrazu maszyny wirtualnej, np. |
| Oferta | Oferta skojarzona z utworzeniem maszyny Wirtualnej |
| SKU | Utworzony sku węzła/maszyny wirtualnej |
| Wersja | Wersja obrazu używanego podczas tworzenia węzła/maszyny wirtualnej |
| Czas tworzenia klastra | Czas utworzenia klastra |
| ResizeStartTime | Godzina rozpoczęcia skalowania klastra w górę/w dół |
| ResizeEndTime | Czas zakończenia skalowania klastra w górę/w dół |
| Czas alokacji węzłów | Czas, w którym przydzielono węzeł |
| NodeBootTime (Czas na węzeł) | Czas, w którym węzeł został uruchomiony |
| StartTaskStartTime | Czas, kiedy zadanie zostało przypisane do węzła i rozpoczęte |
| Czas rozpoczęcia zadania | Czas zakończenia zadania przypisanego do węzła |
| TotalE2ETimeInSekundy | Węzeł całkowitego czasu był aktywny |

### <a name="metrics"></a>Metryki

W poniższych tabelach znajdują się metryki platformy zebrane dla usługi Azure Machine Learning Wszystkie metryki są przechowywane w obszarze nazw **Azure Machine Learning Workspace .**

**Model**

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Wdrożenie modelu nie powiodło się | Liczba | Liczba wdrożeń modelu, które nie powiodły się. |
| Rozpoczęto wdrażanie modelu | Liczba | Liczba rozpoczętych wdrożeń modelu. |
| Wdrożenie modelu powiodło się | Liczba | Liczba wdrożeń modelu, które powiodły się. |
| Rejestr modelu nie powiódł się | Liczba | Liczba rejestracji modelu, które nie powiodły się. |
| Rejestr modelu zakończył się pomyślnie | Liczba | Liczba rejestracji modelu, które powiodły się. |

**Limit przydziału**

Informacje o przydziałach są przeznaczone tylko dla obliczeń usługi Azure Machine Learning.

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Aktywne rdzenie | Liczba | Liczba aktywnych rdzeni obliczeniowych. |
| Aktywne węzły | Liczba | Liczba aktywnych węzłów. |
| Rdzenie bezczynne | Liczba | Liczba bezczynnych rdzeni obliczeniowych. |
| Węzły bezczynne | Liczba | Liczba bezczynnych węzłów obliczeniowych. |
| Opuszczanie rdzeni | Liczba | Liczba opuszczających rdzeni. |
| Opuszczanie węzłów | Liczba | Liczba węzłów opuszczających. |
| Wywłaszczone rdzenie | Liczba | Liczba wywłaszczonych rdzeni. |
| Wstępnie wywłaszczone węzły | Liczba | Liczba wywłaszczonych węzłów. |
| Procent wykorzystania przydziału | Wartość procentowa | Procent użytego przydziału. |
| Całkowita liczba rdzeni | Liczba | Całkowite rdzenie. |
| Łączna liczba węzłów | Liczba | Całkowita liczba węzłów. |
| Niezniszalne rdzenie | Liczba | Liczba bezużytecznych rdzeni. |
| Nieużywalne węzły | Liczba | Liczba węzłów bezużytecznych. |

Poniżej przedstawiono wymiary, za pomocą których można filtrować metryki przydziału:

| Wymiar | Metryki dostępne z | Opis |
| ---- | ---- | ---- |
| Nazwa klastra | Wszystkie metryki przydziału | Nazwa instancji obliczeniowej. |
| Nazwa rodziny VM | Procent wykorzystania przydziału | Nazwa rodziny maszyn wirtualnych używana przez klaster. |
| Priorytet maszyny wirtualnej | Procent wykorzystania przydziału | Priorytet maszyny Wirtualnej.

**Run**

Informacje na temat przebiegów szkoleniowych.

| Metryka | Jednostka | Opis |
| ----- | ----- | ----- |
| Ukończone przebiegi | Liczba | Liczba ukończonych przebiegów. |
| Nieudane przebiegi | Liczba | Liczba nieudanych przebiegów. |
| Rozpoczęte biegi | Liczba | Liczba rozpoczętych uruchomień. |

Poniżej przedstawiono wymiary, za pomocą których można filtrować metryki uruchamiania:

| Wymiar | Opis |
| ---- | ---- |
| Typ obliczeń | Typ obliczeń, który został użyty przez uruchomienie. |
| Typ kroku potoku | Typ [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) używane w uruchomieniu. |
| Opublikowany ChypiulineId | Identyfikator opublikowanego potoku używany w przebiegu. |
| Rodzaj uruchamiania | Typ uruchomienia. |

Prawidłowe wartości dla wymiaru RunType to:

| Wartość | Opis |
| ----- | ----- |
| Experiment | Nie potok działa. |
| PipelineRun (PipelineRun) | Uruchomić potok, który jest elementem nadrzędnym StepRun. |
| StepRun (StepRun) | Przebieg dla kroku potoku. |
| Ponowne uruchomienie | Uruchom dla kroku potoku, który ponownie używa poprzedniego uruchomienia. |

## <a name="see-also"></a>Zobacz też

- Opis monitorowania usługi Azure Machine Learning można znaleźć w części [Monitorowania usługi](monitor-azure-machine-learning.md) Azure Machine Learning.
- Zobacz [monitorowanie zasobów platformy Azure za pomocą usługi Azure Monitor, aby](/azure/azure-monitor/insights/monitor-azure-resource) uzyskać szczegółowe informacje na temat monitorowania zasobów platformy Azure.
