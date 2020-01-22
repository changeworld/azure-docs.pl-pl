---
title: 'Model oceny: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu modelu Score w Azure Machine Learning do generowania prognoz przy użyciu przeszkolonego modelu klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 27838aae1d829598bed44a6c16f7a1d38a95b727
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76314100"
---
# <a name="score-model-module"></a>Moduł Score Model (Generowanie wyników przez model)

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do generowania prognoz przy użyciu przeszkolonego modelu klasyfikacji lub regresji.

## <a name="how-to-use"></a>Jak stosować

1. Dodaj moduł **modelu oceny** do potoku.

2. Dołącz model przeszkolony i zestaw danych zawierający nowe dane wejściowe. 

    Dane powinny mieć format zgodny z typem używanego modelu nauczonego. Schemat wejściowego zestawu danych powinien również zwykle odpowiadać schematowi danych używanych do uczenia modelu.

3. Uruchamianie potoku.

## <a name="results"></a>Wyniki

Po wygenerowaniu zestawu wyników przy użyciu [modelu oceny](./score-model.md):

+ Generowanie zestawu metryk używanych do oceny dokładności modelu (wydajność).  można połączyć zestaw danych oceny z [modelem](./evaluate-model.md), 
+ Kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizacja** , aby wyświetlić przykładowe wyniki.
+ Zapisz wyniki w zestawie danych.

Wynik lub przewidywana wartość może być w wielu różnych formatach, w zależności od modelu i danych wejściowych:

- W przypadku modeli klasyfikacji [model oceny](./score-model.md) generuje prognozowaną wartość klasy, a także prawdopodobieństwo wartości przewidywanej.
- W przypadku modeli regresji [model oceny](./score-model.md) generuje tylko przewidywaną wartość liczbową.
- W przypadku modeli klasyfikacji obrazów wynikiem może być Klasa obiektu w obrazie lub wartość logiczna wskazująca, czy dana funkcja została znaleziona.

## <a name="publish-scores-as-a-web-service"></a>Publikowanie wyników jako usługi sieci Web

Typowym użyciem oceniania jest zwrócenie danych wyjściowych w ramach predykcyjnej usługi sieci Web. Aby uzyskać więcej informacji, zobacz ten samouczek dotyczący tworzenia usługi sieci Web opartej na potoku w Azure Machine Learning:

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 