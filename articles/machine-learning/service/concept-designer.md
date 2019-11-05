---
title: Tworzenie modeli ML przy użyciu narzędzia Projektant
titleSuffix: Azure Machine Learning
description: Poznaj warunki, koncepcje i przepływ pracy, które tworzą projektanta dla Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 11/04/2019
ms.openlocfilehash: ee97322e58fe7ab3a1474f55c6294822b8ce90da
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517867"
---
# <a name="what-is-azure-machine-learning-designer-preview"></a>Co to jest Azure Machine Learning Designer (wersja zapoznawcza)? 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Projektant dla Azure Machine Learning umożliwia Przygotowywanie danych, uczenie, testowanie, wdrażanie, zarządzanie i śledzenie modeli uczenia maszynowego bez konieczności pisania kodu.

Nie ma potrzeby programowania, można wizualnie połączyć [zestawy danych](#datasets) i [moduły](#module) , aby utworzyć model.

Projektant używa [obszaru roboczego](concept-workspace.md) Azure Machine Learning, aby:

+ Tworzenie, edytowanie i uruchamianie [potoków](#pipeline) w obszarze roboczym.
+ Dostęp do [zestawów danych](#datasets).
+ Użyj [zasobów obliczeniowych](#compute) w obszarze roboczym, aby uruchomić potok. 
+ Zarejestruj [modele](concept-azure-machine-learning-architecture.md#models).
+ [Publikuj](#publish) potoki jako punkty końcowe Rest.
+ [Wdrażaj](#deployment) modele jako punkty końcowe potoku (dla wnioskowania wsadowego) lub punkty końcowe w czasie rzeczywistym w zasobach obliczeniowych w obszarze roboczym.

![Omówienie projektanta](media/ui-concept-visual-interface/overview.png)

## <a name="workflow"></a>Przepływ pracy

Projektant oferuje interaktywną, wizualną kanwę umożliwiającą szybkie kompilowanie, testowanie i iterację w modelu. 

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


Aby dowiedzieć się, jak rozpocząć pracę z projektantem, zobacz [Samouczek: przewidywanie ceny za samochód dla urządzeń przenośnych za pomocą projektanta](tutorial-designer-automobile-price-train-score.md).

## <a name="datasets"></a>Zestawy danych

Zestaw danych uczenia maszynowego ułatwia uzyskiwanie dostępu do danych i korzystanie z nich. W projektancie zawarto kilka przykładowych zestawów danych, za pomocą których można eksperymentować. W razie potrzeby możesz [zarejestrować](./how-to-create-register-datasets.md) więcej zestawów danych.

## <a name="module"></a>Moduł

Moduł jest algorytmem, który można wykonać na danych. Projektant ma wiele modułów niż funkcje transferu danych przychodzących do szkoleń, oceniania i procesów walidacji.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie Parametry modułu są wyświetlane w okienku właściwości po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

![Właściwości modułu](media/ui-concept-visual-interface/properties.png)

Aby uzyskać pomoc dotyczącą przechodzenia przez bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [algorytm & — informacje o module](../algorithm-module-reference/module-reference.md)

## <a name="compute"></a>Zasoby obliczeniowe

Użyj zasobów obliczeniowych z obszaru roboczego, aby uruchomić potok i hostować wdrożone modele jako punkty końcowe w czasie rzeczywistym lub punkty końcowe potoku (na potrzeby wnioskowania wsadowego). Obsługiwane elementy docelowe obliczeń są następujące:

| Docelowy zasób obliczeniowy | Szkolenia | Wdrożenie |
| ---- |:----:|:----:|
| Azure Machine Learning obliczeń | ✓ | |
| Azure Kubernetes Service | | ✓ |

Elementy docelowe obliczeń są dołączone do [obszaru roboczego](concept-workspace.md)Machine Learning. Obiektami docelowymi obliczeń można zarządzać w obszarze roboczym programu [Azure Machine Learning Studio](https://ml.azure.com).

## <a name="publish"></a>Publikowanie

Po przygotowaniu potoku możesz go opublikować jako punkt końcowy REST. [PublishedPipeline](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.graph.publishedpipeline?view=azure-ml-py) można przesłać bez kodu języka Python, który go skonstruowano.

Ponadto PublishedPipeline może służyć do ponownego przesłania potoku z różnymi wartościami PipelineParameter i danymi wejściowymi.

## <a name="deployment"></a>Wdrożenie

Gdy model predykcyjny jest gotowy, wdróż go jako punkt końcowy potoku lub punkt końcowy w czasie rzeczywistym z projektanta.

Punkt końcowy potoku to PublishedPipeline, w którym można przesłać uruchomienie potoku z różnymi wartościami PipelineParameter i danymi wejściowymi do wnioskowania o partie.

Punkt końcowy w czasie rzeczywistym zapewnia interfejs między aplikacją a modelem oceniania. Aplikacja zewnętrzna może komunikować się z modelem oceniania w czasie rzeczywistym. Wywołanie punktu końcowego w czasie rzeczywistym zwraca wyniki prognozowania do aplikacji zewnętrznej. Aby wykonać wywołanie do punktu końcowego w czasie rzeczywistym, należy przekazać klucz interfejsu API, który został utworzony podczas wdrażania punktu końcowego. Punkt końcowy jest oparty na pozostałej, popularnej architekturze dla projektów programowania w sieci Web.

Aby dowiedzieć się, jak wdrożyć model, zobacz [Samouczek: Wdrażanie modelu uczenia maszynowego za pomocą projektanta](tutorial-designer-automobile-price-deploy.md).

## <a name="moving-from-the-visual-interface-to-the-designer"></a>Przechodzenie z interfejsu wizualizacji do projektanta

Interfejs wizualny (wersja zapoznawcza) został zaktualizowany i jest teraz Azure Machine Learning projektanta (wersja zapoznawcza). Projektant został przeprojektowany tak, aby korzystał z zaplecza opartego na potoku, który jest w pełni zintegrowany z innymi funkcjami Azure Machine Learning. 

W wyniku tych aktualizacji niektóre koncepcje i terminy dla interfejsu wizualizacji zostały zmienione lub zmieniono ich nazwy. Zapoznaj się z poniższą tabelą, aby poznać najważniejsze zmiany pojęciowe. 

| Koncepcja projektanta | Wcześniej w interfejsie wizualizacji |
| ---- |:----:|
| Wersja robocza potoku | Doświadczenia |
| Punkt końcowy w czasie rzeczywistym | Usługa sieci Web |

### <a name="migrating-to-the-designer"></a>Migrowanie do projektanta

Istniejące eksperymenty interfejsu wizualizacji i usługi sieci Web można przekonwertować na potoki i punkty końcowe w czasie rzeczywistym w projektancie. Wykonaj następujące kroki, aby przeprowadzić migrację zasobów interfejsu wizualizacji:

[!INCLUDE [migrate from the visual interface](../../../includes/aml-vi-designer-migration.md)]


## <a name="next-steps"></a>Następne kroki

* Poznaj podstawy analizy predykcyjnej i uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)
* Użyj jednego z przykładów i zmodyfikuj, aby wymusić Twoje potrzeby:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)

