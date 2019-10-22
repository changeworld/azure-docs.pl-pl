---
title: Interfejs wizualny
titleSuffix: Azure Machine Learning
description: Poznaj warunki, koncepcje i przepływ pracy, które tworzą interfejs wizualizacji (wersja zapoznawcza) dla Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.date: 9/23/2019
ms.openlocfilehash: a23f123c6ffadaad4f830e1f4eab3820e6ef56f6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692218"
---
# <a name="what-is-the-visual-interface-for-azure-machine-learning"></a>Co to jest interfejs wizualny dla Azure Machine Learning? 

Interfejs wizualny (wersja zapoznawcza) dla Azure Machine Learning umożliwia Przygotowywanie danych, uczenie, testowanie, wdrażanie, zarządzanie i śledzenie modeli uczenia maszynowego bez konieczności pisania kodu.

Nie ma potrzeby programowania, można wizualnie połączyć [zestawy danych](#datasets) i [moduły](#module) , aby utworzyć model.

Interfejs wizualny używa [obszaru roboczego](concept-workspace.md) Azure Machine Learning, aby:

+ Tworzenie, edytowanie i uruchamianie [potoków](#pipeline) w obszarze roboczym.
+ Dostęp do [zestawów danych](#datasets).
+ Użyj [zasobów obliczeniowych](#compute) w obszarze roboczym, aby uruchomić potok. 
+ Zarejestruj [modele](concept-azure-machine-learning-architecture.md#models).
+ [Publikuj](#publish) potoki jako punkty końcowe Rest.
+ [Wdrażaj](#deployment) modele jako punkty końcowe potoku (dla wnioskowania wsadowego) lub punkty końcowe w czasie rzeczywistym w zasobach obliczeniowych w obszarze roboczym.

![Przegląd interfejsu wizualizacji](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Przepływ pracy

Interfejs wizualny zapewnia interaktywną, wizualną kanwę umożliwiającą szybkie kompilowanie, testowanie i iterację modelu. 

+ Przeciąganie i upuszczanie [zestawów danych](#datasets) i [modułów](#module) na kanwę.
+ Połącz moduły ze sobą, aby utworzyć [potok](#pipeline).
+ Uruchom potok przy użyciu zasobu obliczeniowego obszaru roboczego usługi Machine Learning.
+ Wykonaj iterację projektu modelu, edytując potok i uruchamiając go ponownie.
+ Gdy wszystko będzie gotowe, przekonwertuj **potok szkoleniowy** na **potok wnioskowania**.
+ [Opublikuj](#publish) potok jako punkt końcowy REST, jeśli chcesz go ponownie przesłać bez kodu języka Python, który został skonstruowany.
+ [Wdróż](#deployment) potok wnioskowania jako punkt końcowy potoku lub punkt końcowy w czasie rzeczywistym, aby można było uzyskać dostęp do modelu przez inne osoby.

## <a name="pipeline"></a>Potok

Utwórz [potok](concept-azure-machine-learning-architecture.md#ml-pipelines) ml od podstaw lub Użyj istniejącego przykładowego potoku jako szablonu. Za każdym razem, gdy uruchamiasz potok, artefakty są przechowywane w obszarze roboczym. Uruchomienia potoku są pogrupowane w [eksperymenty](concept-azure-machine-learning-architecture.md#experiments).

Potok składa się z zestawów danych i modułów analitycznych połączonych ze sobą w celu skonstruowania modelu. W przypadku, prawidłowy potok ma następujące cechy:

* Zestawy danych mogą być połączone tylko z modułami.
* Moduły mogą być połączone z zestawami danych lub innymi modułami.
* Wszystkie porty wejściowe dla modułów muszą mieć połączenie z przepływem danych.
* Należy ustawić wszystkie wymagane parametry dla każdego modułu.


Aby dowiedzieć się, jak rozpocząć pracę z interfejsem wizualnym, zobacz [Samouczek: przewidywanie ceny za samochód dla urządzeń przenośnych za pomocą interfejsu wizualnego](ui-tutorial-automobile-price-train-score.md).

## <a name="datasets"></a>Zestawy danych

Zestaw danych uczenia maszynowego ułatwia uzyskiwanie dostępu do danych i korzystanie z nich. Wiele przykładowych zestawów danych znajduje się w interfejsie wizualizacji, dzięki któremu można eksperymentować z programem. W razie potrzeby możesz [zarejestrować](./how-to-create-register-datasets.md) więcej zestawów danych.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Interfejs wizualny ma wiele modułów niż funkcje transferu danych przychodzących do szkoleń, oceniania i procesów walidacji.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie Parametry modułu są wyświetlane w okienku właściwości po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

![Właściwości modułu](media/ui-concept-visual-interface/properties.png)

Aby uzyskać pomoc dotyczącą przechodzenia przez bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [algorytm & — informacje o module](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Zasoby obliczeniowe

Użyj zasobów obliczeniowych z obszaru roboczego, aby uruchomić potok i hostować wdrożone modele jako punkty końcowe w czasie rzeczywistym lub punkty końcowe potoku (na potrzeby wnioskowania wsadowego). Obsługiwane elementy docelowe obliczeń są następujące:

| Docelowy zasób obliczeniowy | Szkolenie | Wdrażanie |
| ---- |:----:|:----:|
| Azure Machine Learning obliczeń | ✓ | |
| Azure Kubernetes Service | | ✓ |

Elementy docelowe obliczeń są dołączone do [obszaru roboczego](concept-workspace.md)Machine Learning. Obiektami docelowymi obliczeń można zarządzać w obszarze roboczym w [Azure Portal](https://portal.azure.com) lub na [stronie docelowej obszaru roboczego (wersja zapoznawcza)](https://ml.azure.com).

## <a name="publish"></a>Publikuj

Po przygotowaniu potoku możesz go opublikować jako punkt końcowy REST. [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) można przesłać bez kodu języka Python, który go skonstruowano.

Ponadto PublishedPipeline może służyć do ponownego przesłania potoku z różnymi wartościami PipelineParameter i danymi wejściowymi.

## <a name="deployment"></a>Wdrażanie

Gdy model predykcyjny jest gotowy, wdróż go jako punkt końcowy potoku lub punkt końcowy w czasie rzeczywistym w interfejsie wizualizacji.

Punkt końcowy potoku to [PublishedPipeline, który można przesłać do uruchomienia potoku z różnymi wartościami PipelineParameter i danymi wejściowymi dotyczącymi wnioskowania wsadowego.

Punkt końcowy w czasie rzeczywistym zapewnia interfejs między aplikacją a modelem oceniania. Aplikacja zewnętrzna może komunikować się z modelem oceniania w czasie rzeczywistym. Wywołanie punktu końcowego w czasie rzeczywistym zwraca wyniki prognozowania do aplikacji zewnętrznej. Aby wykonać wywołanie do punktu końcowego w czasie rzeczywistym, należy przekazać klucz interfejsu API, który został utworzony podczas wdrażania punktu końcowego. Punkt końcowy jest oparty na pozostałej, popularnej architekturze dla projektów programowania w sieci Web.

Aby dowiedzieć się, jak wdrożyć model, zobacz [Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą interfejsu wizualnego](ui-tutorial-automobile-price-deploy.md).

## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem interfejsu wizualnego](ui-tutorial-automobile-price-train-score.md)
* Użyj jednego z przykładów i zmodyfikuj, aby wymusić Twoje potrzeby:

    * [Przykład 1-regresja: cena predykcyjna](how-to-ui-sample-regression-predict-automobile-price-basic.md)
    * [Przykład 2-Regresja: przewidywanie ceny i porównywanie algorytmów](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
    * [Przykład 3 — Klasyfikacja: przewidywanie ryzyka kredytowego](how-to-ui-sample-classification-predict-credit-risk-basic.md)
    * [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
    * [Przykład 5 — Klasyfikacja: przewidywanie zmian, pragnienie i kupowanie](how-to-ui-sample-classification-predict-churn.md)
    * [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-ui-sample-classification-predict-flight-delay.md)

