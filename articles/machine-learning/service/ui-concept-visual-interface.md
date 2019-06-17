---
title: Interfejs wizualny
titleSuffix: Azure Machine Learning service
description: Więcej informacji na temat warunków, pojęcia i przepływu pracy, które tworzą interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 05/15/2019
ms.openlocfilehash: be07e0f3438ea93312d4eb440e7e63b8f98e11b8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67077373"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning-service"></a>Co to jest interfejs graficzny dla usługi Azure Machine Learning? 

Interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning umożliwia przygotowuj dane, uczyć, testowanie, wdrażanie, zarządzanie i śledzenia modeli uczenia maszynowego bez konieczności pisania kodu.

Nie jest wymagane żadne programowanie, wizualne łączenie [zestawów danych](#dataset) i [modułów](#module) do konstruowania modelu.

Interfejs graficzny korzysta z usługi Azure Machine Learning [obszaru roboczego](concept-workspace.md) do:

+ Zapis artefaktów [eksperymentować](#experiment) uruchamia do obszaru roboczego.
+ Dostęp do [zestawów danych](#dataset).
+ Użyj [zasoby obliczeniowe](#compute) w obszarze roboczym, aby uruchomić eksperyment. 
+ Zarejestruj [modeli](concept-azure-machine-learning-architecture.md#models).
+ [Wdrażanie](#deployment) modeli jako usług sieci web na obliczenia zasobów w obszarze roboczym.

![Omówienie interfejsu wizualnego](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Przepływ pracy

Interfejs graficzny zapewnia interaktywny, wizualny obszaru roboczego do szybkiego tworzenia, testowania i powtarzanie czynności w modelu. 

+ Możesz przeciągać i upuszczać [modułów](#module) na kanwę.
+ Łączenie modułów ze sobą do formularza [eksperymentować](#experiment).
+ Uruchom eksperyment, przy użyciu zasobów obliczeniowych, z obszaru roboczego usługi Machine Learning.
+ Wykonania iteracji projektu modelu, edytując eksperymentu i ponowne uruchomienie.
+ Gdy wszystko będzie gotowe, Konwertuj swoje **eksperymentu szkolenia** do **eksperyment predykcyjny**.
+ [Wdrażanie](#deployment) eksperyment predykcyjny jako sieci web usługi, aby Twoje model będzie dostępny przez innych użytkowników.

## <a name="experiment"></a>Eksperyment

Tworzenie eksperymentu od podstaw lub użyć przykładowego eksperymentu jako szablonu.  Każdorazowo, gdy uruchomienie eksperymentu, artefakty są przechowywane w obszarze roboczym.

Eksperyment składa się z zestawów danych i modułów analitycznych łączonych w celu utworzenia modelu. Prawidłowy eksperyment ma następujące cechy:

* Zestawy danych mogą być połączone tylko z modułami.
* Moduły mogą być połączone z zestawami danych lub innych modułów.
* Wszystkie porty wejściowe modułów muszą mieć jakieś połączenie z przepływem danych.
* Wszystkie wymagane, należy ustawić parametry dla każdego modułu.

Na przykład prosty eksperyment zobacz [Szybki Start: Przygotowanie i wizualizowanie danych bez konieczności pisania kodu w usłudze Azure Machine Learning](ui-quickstart-run-experiment.md).

Aby uzyskać bardziej szczegółowy przewodnik rozwiązania do analizy predykcyjnej, zobacz [samouczka: Prognozowanie cen samochodów za pomocą interfejsu wizualnego](ui-tutorial-automobile-price-train-score.md).

## <a name="dataset"></a>Zestaw danych

Zestaw danych to dane, która została przekazana do interfejs graficzny do użycia w procesie modelowania. Wiele przykładowych zestawów danych dołączono do eksperymentowania z, a następnie możesz przekazać jeden zestaw danych, gdy ich potrzebujesz.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Interfejs graficzny zawiera szereg modułów, począwszy od funkcji transferu danych przychodzących danych do szkolenia, ocenę i walidację.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie, parametry tego modułu są wyświetlane w okienku właściwości po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

![Właściwości modułu](media/ui-concept-visual-interface/properties.png)

Aby uzyskać pomoc w nawigowaniu Biblioteka algorytmów uczenia maszynowego, zobacz [algorytmów i modułów omówienie odwołań](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a> Zasoby obliczeniowe

Użyj obliczeń zasoby z obszaru roboczego na uruchamianie eksperymentu lub hosta wdrożonej modeli jako usług sieci web. Są obsługiwane obliczeniowych elementów docelowych:


| Obliczeniowego elementu docelowego | Szkolenia | Wdrożenie |
| ---- |:----:|:----:|
| Obliczeniowe platformy Azure Machine Learning | ✓ | |
| Azure Kubernetes Service | | ✓ |

Obliczeniowe elementy docelowe są dołączone do usługi Machine Learning [obszaru roboczego](concept-workspace.md). Zarządzanie obliczeniowych elementów docelowych w obszarze roboczym w [witryny Azure portal](https://portal.azure.com).

## <a name="deployment"></a>Wdrożenie

Gdy model analizy predykcyjnej jest gotowy, możesz go wdrożyć jako usługi sieci web bezpośrednio z interfejsu wizualnego.

Usługi sieci web zapewniają interfejs między aplikacją i oceniania modelu. Aplikacja zewnętrzna może komunikować się z modelem oceniania w czasie rzeczywistym. Wywołanie usługi sieci web zwraca wyniki prognozowania do aplikacji zewnętrznej. Wywołanie usługi sieci Web polega na przekazaniu klucza interfejsu API utworzonego podczas wdrażania tej usługi. Usługa sieci web jest oparta na interfejsu REST — popularnej architektury w projektach programistycznych w sieci web.

Aby dowiedzieć się, jak wdrożyć model, zobacz [samouczka: Wdrożyć model uczenia maszynowego za pomocą interfejsu wizualnego](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Kolejne kroki

* Naucz się podstaw analizy predykcyjnej i usługi machine learning za pomocą [Szybki Start: Przygotowanie i wizualizowanie danych bez konieczności pisania kodu w usłudze Azure Machine Learning](ui-quickstart-run-experiment.md).
* Użyj jednego z przykładów i modyfikowania pakietu potrzeb:
    * [Przykład 1 - regresji. Przewidzieć cenę](ui-sample-regression-predict-automobile-price-basic.md)
    * [Przykład 2 - regresji. Przewidzieć cenę i porównaj algorytmów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Próba 3 — klasyfikacji: Prognozowanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
    * [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Przykład 5 - klasyfikacji: Przewidywanie zmienności appetency i sprzedaży w górę](ui-sample-classification-predict-churn.md)
