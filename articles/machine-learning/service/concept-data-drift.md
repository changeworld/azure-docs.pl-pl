---
title: Przesunięcie danych monitoring (wersja zapoznawcza)
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak monitorować usługę Azure Machine Learning dla danych odejściem od tego stanu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cody-dkdc
ms.author: copeters
ms.date: 06/20/2019
ms.openlocfilehash: 56761c32484d4f5b27800e56143c62d3731da852
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/22/2019
ms.locfileid: "67333220"
---
# <a name="what-is-data-drift-monitoring-preview"></a>Co to jest data odstępstw monitorowania (wersja zapoznawcza)?

Przesunięcie danych jest zmiana rozkład danych. W kontekście uczenia maszynowego wytrenowane modele uczenia maszynowego mogą występować prognozowania pogorszenie wydajności z powodu odejściem od tego stanu. Monitorowanie odchylenie między dane szkoleniowe i danych używane na potrzeby wykonywania prognoz może pomóc w wykryć problemy z wydajnością.

Modele uczenia maszynowego są tylko tak dobrze, jak dane używane do trenowania je. Wdrażanie modeli do środowiska produkcyjnego bez monitorowania jej wydajności może prowadzić do wykryte i niekorzystny wpływ na środowisko. Przy użyciu danych monitorowania odejściem od tego stanu, można wykryć i dostosowanie się do danych odejściem od tego stanu. 

## <a name="when-to-monitor-for-data-drift"></a>Kiedy do monitorowania dryfu danych?

Metryki, które możemy monitorować obejmują:

+ Wielkość dryfu (współczynnik odejściem od tego stanu)
+ Przyczyna dryfu (dryfu materiałów przekazywanych przez funkcję)
+ Metryki odległości (Wasserstein energii, itp.)

Z tego monitorowania, alertów lub akcji można skonfigurować pod kątem po odejściem od tego stanu są wykrywane i analityk danych można zbadać przyczynę problemu. 

Jeśli uważasz, że dane wejściowe dla modelu wdrożonej mogą ulec zmianie, należy rozważyć użycie wykrywanie dryfu danych.

## <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Jak dryfu danych jest monitorowany w usłudze Azure Machine Learning

Za pomocą **usługi Azure Machine Learning**, dryfu danych odbywa się za pośrednictwem zestawów danych lub wdrożeń. Aby monitorować dryfu danych, według planu bazowego zestawu danych — zwykle szkolenia zestawu danych dla modelu — jest określona. Drugi zestaw danych — zwykle modelu danych wejściowych zebrane z wdrożenia — są testowane w odniesieniu do bazowego zestawu danych. Oba zestawy danych są [profilowane](how-to-create-dataset-snapshots.md) i wprowadzania danych odstępstw usługi monitorowania. Model uczenia maszynowego jest uczony w celu wykrywania różnic między dwoma zestawami danych. Model wydajności jest konwertowana na współczynnik dryfu, czyli miary wielkości odchylenie między dwoma zestawami danych. Za pomocą [modelowania współdziałania](machine-learning-interpretability-explainability.md) są obliczane funkcje, które przyczyniły się do współczynnik odejściem od tego stanu. Z profilu zestaw danych jest śledzona informacje statystyczne na temat każdej funkcji. 

## <a name="data-drift-metric-output"></a>Dane odstępstw metryki danych wyjściowych

Istnieje wiele sposobów, aby wyświetlić metryki odejściem od tego stanu:

* Za pomocą widżetu Jupyter.
* Użyj `get_metrics()` funkcji na dowolnym `datadriftRun` obiektu.
* Wyświetlać metryki w witrynie Azure portal w modelu

Następujące metryki są zapisywane w każdej iteracji wykonywania zadania dryfu danych:

|Metryka|Opis|
--|--|
wasserstein_distance|Odległość statystyczne zdefiniowane dla jednowymiarowa dystrybucji wartości liczbowych.|
energy_distance|Odległość statystyczne zdefiniowane dla jednowymiarowa dystrybucji wartości liczbowych.|
datadrift_coefficient|Formalnie Matthews współczynnik korelacji, liczba rzeczywista z zakresu od -1 do 1. W kontekście dryfu 0 oznacza nie dryfu i wartość 1 oznacza maksymalną odejściem od tego stanu.|
datadrift_contribution|Funkcja ważność przyczyniające się do funkcji.|

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z przykładami i Dowiedz się, jak monitorować dryfu danych:

+ [Dowiedz się, jak monitorować dane odejściem od tego stanu w modelach wdrożone za pośrednictwem usługi Azure Kubernetes Service (AKS)](how-to-monitor-data-drift.md)
+ Wypróbuj [przykłady notesu programu Jupyter](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/data-drift/)