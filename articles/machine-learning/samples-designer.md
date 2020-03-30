---
title: Przykładowe potoki projektanta
titleSuffix: Azure Machine Learning
description: Użyj przykładów w projektancie usługi Azure Machine Learning, aby szybko uruchomić potoki uczenia maszynowego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389345"
---
# <a name="designer-sample-pipelines"></a>Przykładowe potoki projektanta

Skorzystaj z wbudowanych przykładów w projektancie usługi Azure Machine Learning, aby szybko rozpocząć tworzenie własnych potoków uczenia maszynowego. Projektant usługi Azure Machine Learning [Repozytorium GitHub](https://github.com/Azure/MachineLearningDesigner) zawiera szczegółową dokumentację, która ułatwia zrozumienie niektórych typowych scenariuszy uczenia maszynowego.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://aka.ms/AMLFree).
* Obszar roboczy usługi Azure Machine Learning z jednostką SKU przedsiębiorstwa.


## <a name="how-to-use-sample-pipelines"></a>Jak korzystać z przykładowych potoków

Projektant zapisuje kopię przykładowych potoków do obszaru roboczego studio. Możesz edytować potok, aby dostosować go do swoich potrzeb i zapisać go jako swój własny. Użyj ich jako punktu wyjścia do szybkiego rozpoczęcia projektów.

### <a name="open-a-sample-pipeline"></a>Otwieranie przykładowego potoku

1. Zaloguj się, aby <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>i wybierz obszar roboczy, z którego chcesz pracować.

1. Wybierz **pozycję Projektant**.

1. Wybierz przykładowy potok w sekcji **Nowy potok.**

    Wybierz **pozycję Pokaż więcej próbek,** aby uzyskać pełną listę próbek.

### <a name="submit-a-pipeline-run"></a>Przesyłanie przebiegu potoku

Aby uruchomić potok, należy najpierw ustawić domyślny obiekt docelowy obliczeń, aby uruchomić potok.

1. W okienku **Ustawienia** po prawej stronie kanwy wybierz **pozycję Wybierz cel obliczeniowy**.

1. W wyświetlonym oknie dialogowym wybierz istniejący obiekt docelowy obliczeń lub utwórz nowy. Wybierz **pozycję Zapisz**.

1. Wybierz **prześlij** w górnej części kanwy, aby przesłać przebieg potoku.

W zależności od przykładowego potoku i ustawień obliczeniowych wykonanie przebiegów może zająć trochę czasu. Domyślne ustawienia obliczeniowe mają minimalny rozmiar węzła 0, co oznacza, że projektant musi przydzielić zasoby po bezczynności. Powtarzające się przebiegi potoku zajmie mniej czasu, ponieważ zasoby obliczeniowe są już przydzielone. Ponadto projektant używa buforowanych wyników dla każdego modułu, aby jeszcze bardziej zwiększyć wydajność.


### <a name="review-the-results"></a>Sprawdzanie wyników

Po zakończeniu działania potoku można przejrzeć potok i wyświetlić dane wyjściowe dla każdego modułu, aby dowiedzieć się więcej.

Aby wyświetlić wyjścia modułu, należy wykonać następujące czynności:

1. Wybierz moduł na kanwie.

1. W okienku szczegółów modułu po prawej stronie obszaru roboczego wybierz pozycję **Wyjścia + dzienniki**. Wybierz ikonę ![wykresu](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png) wizualizuj ikonę, aby zobaczyć wyniki każdego modułu. 

Użyj przykładów jako punktów wyjścia dla niektórych z najbardziej typowych scenariuszy uczenia maszynowego.

## <a name="regression-samples"></a>Próbki regresji

Dowiedz się więcej o wbudowanych przykładach regresji.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 1: Regresja - Przewidywanie cen samochodów (podstawowe)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md) | Przewiduj ceny samochodów za pomocą regresji liniowej. |
| [Przykład 2: Regresja - Przewidywanie cen samochodów (zaawansowane)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md) | Przewiduj ceny samochodów za pomocą lasu decyzyjnego i zwiększył regresorów drzewa decyzyjnego. Porównaj modele, aby znaleźć najlepszy algorytm.

## <a name="classification-samples"></a>Próbki klasyfikacji

Dowiedz się więcej o wbudowanych przykładach klasyfikacji. Możesz dowiedzieć się więcej o przykładach bez łączy dokumentacji, otwierając przykłady i zamiast tego wyświetlając komentarze modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 3: Klasyfikacja binarna z wyborem funkcji - Przewidywanie dochodów](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | Przewiduj dochód jako wysoki lub niski, używając dwuklasowego drzewa decyzyjnego. Użyj korelacji Pearsona, aby wybrać operacje.
| [Przykład 4: Klasyfikacja binarna z niestandardowym skryptem Pythona - Przewidywanie ryzyka kredytowego](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | Klasyfikuj wnioski kredytowe jako wysokie lub niskie ryzyko. Użyj modułu Wykonywanie skryptu Pythona, aby wciążyć dane.
| [Przykład 5: Klasyfikacja binarna - Przewidywanie relacji z klientem](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | Przewiduj rezygnację klientów przy użyciu dwuklasowych drzew decyzyjnych wzmocnionych. Użyj SMOTE do próbkowania tendencyjnych danych.
| [Przykład 7: Klasyfikacja tekstu - Zestaw danych Wikipedia SP 500](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | Klasyfikowanie typów firm z artykułów Wikipedii z wieloklasową regresją logistyczną. |
| Przykład 12: Klasyfikacja wieloklasowa - Rozpoznawanie liter | Utwórz zespół klasyfikatorów binarnych, aby sklasyfikować litery pisane. |

## <a name="recommender-samples"></a>Próbki polecający

Dowiedz się więcej o wbudowanych przykładach polecania. Możesz dowiedzieć się więcej o przykładach bez łączy dokumentacji, otwierając przykłady i zamiast tego wyświetlając komentarze modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| Przykład 10: Zalecenie - Tweety z oceną filmu | Zbuduj silnik rekomendacji filmów z tytułów i ocen filmów. |

## <a name="utility-samples"></a>Próbki narzędziowe

Dowiedz się więcej o przykładach, które pokazują narzędzia i funkcje uczenia maszynowego. Możesz dowiedzieć się więcej o przykładach bez łączy dokumentacji, otwierając przykłady i zamiast tego wyświetlając komentarze modułu.

| Przykładowy tytuł | Opis | 
| --- | --- |
| [Przykład 6: Użyj niestandardowego skryptu R - Przewidywanie opóźnienia lotu](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| Przykład 8: Krzyżowa walidacja klasyfikacji binarnej - Przewidywanie dochodów dorosłych | Użyj sprawdzania poprawności krzyżowej, aby utworzyć klasyfikatora binarnego dla dochodu osoby dorosłej.
| Przykład 9: Znaczenie funkcji permutacji | Użyj znaczenia funkcji permutacji do obliczania wyników ważności dla zestawu danych testowych. 
| Przykład 11: Dostrajanie parametrów klasyfikacji binarnej - Przewidywanie dochodów dorosłych | Użyj Tune Model Hyperparameters, aby znaleźć optymalne hiperparametry do utworzenia klasyfikatora binarnego. |

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak tworzyć i wdrażać modele uczenia maszynowego za pomocą [samouczka: Przewidywanie ceny samochodu z projektantem](tutorial-designer-automobile-price-train-score.md)
