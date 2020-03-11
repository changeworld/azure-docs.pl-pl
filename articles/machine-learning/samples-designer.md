---
title: Przykładowe potoki projektanta
titleSuffix: Azure Machine Learning
description: Użyj przykładów w programie Azure Machine Learning Designer, aby przeskakuje i uruchamiać potoki uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/10/2020
ms.openlocfilehash: 82476b9cc8d92c815df602496ed3dcb33014a4fd
ms.sourcegitcommit: b8d0d72dfe8e26eecc42e0f2dbff9a7dd69d3116
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79037402"
---
# <a name="designer-sample-pipelines"></a>Przykładowe potoki projektanta

Skorzystaj z wbudowanych przykładów w programie Azure Machine Learning Designer, aby szybko rozpocząć tworzenie własnych potoków uczenia maszynowego. [Repozytorium usługi GitHub](https://github.com/Azure/MachineLearningDesigner) Azure Machine Learning Designer zawiera szczegółową dokumentację ułatwiającą zrozumienie niektórych typowych scenariuszy uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Azure Machine Learning obszar roboczy z jednostką SKU przedsiębiorstwa.


## <a name="how-to-use-sample-pipelines"></a>Jak używać potoków przykładowych

Projektant zapisuje kopię przykładowych potoków w obszarze roboczym programu Studio. Możesz edytować potok, aby dostosować go do swoich potrzeb i zapisać jako własny. Użyj ich jako punktu początkowego, aby szybko Rozpocznij pracę projekty.

1. Zaloguj się do <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.Azure.com</a>i wybierz obszar roboczy, z którym chcesz współpracować.

1. Wybierz pozycję **Projektant**.

1. Wybierz przykładowy potok w sekcji **nowe potoku** .

    Wybierz pozycję **Pokaż więcej próbek** , aby uzyskać pełną listę przykładów.

## <a name="regression-samples"></a>Próbki regresji

Dowiedz się więcej na temat wbudowanych przykładów regresji.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 1: regresja — Prognoza cen dla samochodów (podstawowa)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Przewidywanie cen samochodów przy użyciu regresji liniowej. |
| [Przykład 2: regresja — Prognoza cen dla samochodów (Zaawansowane)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Przewidywanie cen samochodów przy użyciu lasu decyzyjnego i podwyższanie drzewa decyzyjnego regresorów. Porównaj modele, aby znaleźć najlepszy algorytm.

## <a name="classification-samples"></a>Przykłady klasyfikacji

Dowiedz się więcej o wbudowanych przykładach klasyfikacji. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 3: klasyfikacja binarna z wyborem funkcji — prognozowanie dochodu](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Przewidywanie przychodów jako wysokie lub niskie przy użyciu dwuklasowego drzewa decyzyjnego. Użyj korelacji Pearsona, aby wybrać funkcje.
| [Przykład 4: klasyfikacja binarna z niestandardowym skryptem języka Python — przewidywanie ryzyka kredytowego](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klasyfikowanie aplikacji kredytowych jako wysokiego lub niskiego ryzyka. Użyj modułu skryptu języka Python do ważenia danych.
| [Przykład 5: klasyfikacja binarna — Prognoza relacji klienta](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Przewidywanie zmian klientów przy użyciu dwuklasowych drzew decyzyjnych. Użyj SMOTE do próbkowania danych.
| [Przykład 7: Klasyfikacja tekstu — zestaw danych witryny Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klasyfikowanie typów firmy z artykułów witryny Wikipedia przy użyciu wieloklasowej regresji logistycznej. |
| Przykład 12: Rozpoznawanie litery w klasyfikacji wieloklasowej | Utwórz kompletną liczbę klasyfikatorów binarnych do klasyfikowania pisanych liter. |

## <a name="recommender-samples"></a>Przykłady rekomendacji

Dowiedz się więcej na temat wbudowanych przykładów zalecenia. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| Przykład 10: rekomendacje — tweety klasyfikacji filmów | Kompiluj aparat polecanego filmu z tytułów i klasyfikacji filmów. |

## <a name="utility-samples"></a>Przykłady narzędzi

Dowiedz się więcej o przykładach demonstrujących narzędzia i funkcje uczenia maszynowego. Aby dowiedzieć się więcej o przykładach bez linków do dokumentacji, należy otworzyć przykłady i wyświetlić komentarze do modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 6: Używanie niestandardowego skryptu języka R — prognozowanie opóźnień lotów](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Przykład 8: krzyżowe sprawdzanie poprawności dla klasyfikacji binarnej — przewidywanie dochodu dla dorosłych | Użyj operacji krzyżowego sprawdzania poprawności, aby utworzyć klasyfikator binarny dla dorosłych dochodów.
| Przykład 9: ważność funkcji permutacji | Użyj ważności funkcji permutacji, aby obliczyć wyniki ważności dla zestawu danych testowych. 
| Przykład 11: dostrajanie parametrów dla klasyfikacji binarnej — przewidywanie dochodu dla dorosłych | Użyj dopasowywania parametrów modelu, aby znaleźć optymalne parametry do kompilowania klasyfikatora binarnego. |

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak tworzyć i wdrażać modele uczenia maszynowego za pomocą [samouczka: przewidywanie ceny za samochód z użyciem projektanta](tutorial-designer-automobile-price-train-score.md)
