---
title: Monitorowanie dryfowania danych (wersja zapoznawcza)
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak usługa Azure Machine Learning może monitorować na potrzeby dryfowania danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 13f73718fabd711e9c71a56ac4537b2ebef8a411
ms.sourcegitcommit: c71306fb197b433f7b7d23662d013eaae269dc9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371089"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Co to jest monitorowanie dryfowania danych (wersja zapoznawcza)?

Dryfowanie danych to zmiana dystrybucji danych. W kontekście uczenia maszynowego, przeszkolone modele uczenia maszynowego mogą powodować spadek wydajności przewidywania z powodu dryfu. Monitorowanie dryfu między danymi szkoleń i danymi używanymi do tworzenia prognoz może pomóc w wykrywaniu problemów z wydajnością.

Modele uczenia maszynowego są tak dobre, jak dane używane do uczenia się. Wdrażanie modeli w środowisku produkcyjnym bez monitorowania jego wydajności może prowadzić do wykrycia i niekorzystnego wpływu. Przy użyciu monitorowania dryfowania danych można wykrywać i dostosowywać do dryfowania danych. 

## <a name="when-to-monitor-for-data-drift"></a>Kiedy należy monitorować pod kątem dryfowania danych?

Możliwe do monitorowania metryki obejmują:

+ Wielkość dryfu (współczynnik dryfu)
+ Przyczyna dryfowania (dostęp z dryfem przez funkcję)
+ Metryki odległości (Wasserstein, energia itp.)

W przypadku takiego monitorowania można skonfigurować alerty lub akcje, gdy zostanie wykryte dryfowanie, a analityk danych może zbadać główną przyczynę problemu. 

Jeśli uważasz, że dane wejściowe dla wdrożonego modelu mogą ulec zmianie, należy rozważyć zastosowanie wykrywania dryfowania danych.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak jest monitorowane przedryfowanie danych w usłudze Azure Machine Learning

Przy użyciu **usługi Azure Machine Learning**, dryfowanie danych jest monitorowane za pośrednictwem zestawów danych lub wdrożeń. Aby monitorować do dryfowania danych, bazowy zestaw danych — zwykle jest to zestaw danych szkoleniowych dla modelu — jest określony. Drugi zestaw danych — zwykle dane wejściowe modelu zebrane z wdrożenia — są testowane względem bazowego zestawu danych. Oba zestawy danych są [profilowane i są](how-to-explore-prepare-data.md#explore-with-summary-statistics) danymi wejściowymi do usługi monitorowania dryfowania danych. Model uczenia maszynowego jest szkolony w celu wykrywania różnic między dwoma zestawami danych. Wydajność modelu jest konwertowana na współczynnik dryfu, który mierzy wielkość dryfu między dwoma zestawami danych. Przy użyciu [interpretacji modelu](machine-learning-interpretability-explainability.md) funkcje, które przyczyniają się do współczynnika dryfu, są obliczane. W profilu zestawu danych są śledzone informacje statystyczne dotyczące każdej funkcji. 

## <a name="data-drift-metric-output"></a>Dane wyjściowe metryki dotyczącej dryfowania danych

Istnieje wiele sposobów wyświetlania metryk dryfu:

* Użyj widżetu [Jupyter.](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py) `RunDetails`
* Użyj funkcji dla dowolnego `datadriftRun` obiektu. `get_metrics()`
* Wyświetlanie metryk w Azure Portal w modelu

Następujące metryki są zapisywane w każdej iteracji przebiegu dla zadania dryfowania danych:

|Metryka|Opis|
--|--|
wasserstein_distance|Odległość statystyczna zdefiniowana dla jednowymiarowej dystrybucji liczbowej.|
energy_distance|Odległość statystyczna zdefiniowana dla jednowymiarowej dystrybucji liczbowej.|
datadrift_coefficient|Współczynnik korelacji formalnie Matthews — liczba rzeczywista z zakresu od-1 do 1. W kontekście dryfu wartość 0 oznacza brak dryfu i 1 oznacza maksymalne dryfowanie.|
datadrift_contribution|Znaczenie funkcji dotyczącej dryfu.|

## <a name="next-steps"></a>Następne kroki

Zobacz przykłady i Dowiedz się, jak monitorować przedryfowanie danych:

+ [Dowiedz się, jak monitorować dryf danych dla modeli wdrożonych za pomocą usługi Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Wypróbuj [Jupyter Notebook przykłady](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)