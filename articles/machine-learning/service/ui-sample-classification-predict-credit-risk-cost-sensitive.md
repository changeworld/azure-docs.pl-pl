---
title: 'Klasyfikacja: Prognozowanie ryzyka kredytowego (koszt poufnych)'
titleSuffix: Azure Machine Learning service
description: W tym artykule pokazano, jak tworzyć złożone eksperymentu uczenia maszynowego za pomocą interfejsu wizualnego. Dowiesz się, jak implementować niestandardowe skrypty języka Python oraz porównanie różnych modeli można wybrać najlepsze rozwiązanie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d714756c19b94eafc40cc0dbeffbc07704e8f94e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65787815"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)

W tym artykule pokazano, jak tworzyć złożone eksperymentu uczenia maszynowego za pomocą interfejsu wizualnego. Dowiesz się, jak implementować logikę niestandardową przy użyciu skryptów języka Python oraz porównanie różnych modeli można wybrać najlepsze rozwiązanie.

W tym przykładzie przygotowuje klasyfikatora przewidzieć ryzyko kredytowe, przy użyciu informacji o aplikacji środków, takich jak Historia kredytów, wiek i liczba kart kredytowych. Jednakże można zastosować koncepcji, w tym artykule do własnych uczenia maszynowego problemów.

Jeśli po prostu rozpoczniesz pracę z usługą machine learning, może zająć się [przykładowe klasyfikatora podstawowego](ui-sample-classification-predict-credit-risk-basic.md) pierwszy.

Oto wykres zakończone, w tym eksperymencie:

[![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie przykład 4:

    ![Otwórz eksperyment](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Dane

Używamy zestawu danych karty kredytowej niemiecki z repozytorium Irvine Unikatowości. Ten zestaw danych zawiera 1000 próbek z funkcjami 20 i 1 etykiety. Poszczególne przykładowe aplikacje przedstawiają osoby. 20 funkcje obejmują funkcje numeryczne i podzielonych na kategorie. Zobacz [UCI witryny sieci Web](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) Aby uzyskać więcej informacji na temat zestawu danych. Ostatnia kolumna jest etykiety, który wskazuje ryzyko kredytowe, a ma tylko dwa możliwe wartości: ryzyko kredytowe wysoki = 2 i niskie ryzyko kredytowe = 1.

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

W tym eksperymencie porównamy dwa różne podejścia do generowania modeli, aby rozwiązać ten problem:

- Szkolenie przy użyciu oryginalnego zestawu danych.
- Szkolenie przy użyciu replikowanego zestawu danych.

Za pomocą obu metod Oceniamy modele przy użyciu zestawu danych testowych za pomocą replikacji, aby upewnić się, że wyniki są wyrównane przy użyciu funkcji cost. Przetestowanie dwoma klasyfikatorami za pomocą obu metod: **Obsługa Two-Class wektor maszyny** i **Two-Class wzmocnione drzewo decyzyjnego**.

Koszt misclassifying to przykład niskiego ryzyka jako wysokie wynosi 1, a misclassifying o wysokim ryzyku przykład jako niski koszt wynosi 5. Używamy **wykonanie skryptu Python** modułu dla tego błędu klasyfikacji kosztów.

Oto wykres eksperymentu:

[![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Przetwarzanie danych

Rozpoczniemy pracę przy użyciu **Edytor metadanych** modułu, można dodać nazwy kolumn, aby zastąpić domyślne nazwy kolumn bardziej zrozumiałej nazwy uzyskany z witryny UCI opis zestawu danych. Firma Microsoft zapewnia nowe nazwy kolumny jako wartości rozdzielanych przecinkami w **nową kolumnę** pole Nazwa **Edytor metadanych**.

Następnie mogliśmy wygenerować szkolenia i testować zestawy służące do tworzenia modelu prognozowania ryzyka. Firma Microsoft dzielenie oryginalnego zestawu danych w zestawach szkolenie i testowanie tej samej wielkości za pomocą **podziału danych** modułu. Aby utworzyć zestawy w taki sam rozmiar, ustawiliśmy **ułamek wierszy w pierwszym zestawie danych wyjściowych** możliwość 0,5.

### <a name="generate-the-new-dataset"></a>Generuj nowy zestaw danych

Ponieważ koszty niedoszacowania o podwyższonym ryzyku są wysokie, możemy ustawić koszt błędu klasyfikacji w następujący sposób:

- Dla przypadków o wysokim ryzyku źle zakwalifikowane jako niskiego ryzyka: 5
- Dla przypadków o niskim ryzyku źle zakwalifikowane jako wysokiego ryzyka: 1

Aby uwzględnić tę funkcję, kosztów, możemy wygenerować nowy zestaw danych. W nowym zestawie danych każdy przykład o wysokim ryzyku są replikowane pięć razy, ale nie zmienia się wiele przykładów o niskim ryzyku. Możemy podzielić dane na szkolenie i testowanie zestawów danych, przed włączeniem replikacji, aby uniemożliwić tym samym wierszu w obu zestawach.

Replikowanie danych o wysokim ryzyku, testujemy ten kod języka Python do **wykonanie skryptu Python** modułu:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

**Wykonanie skryptu Python** modułu replikuje zestawów danych szkoleniowych i testów.

### <a name="feature-engineering"></a>Inżynieria funkcji

**Two-Class pomocy technicznej Vector Machine** algorytm wymaga danych znormalizowana. Dlatego używamy **normalizacji danych** modułu do normalizacji zakresy wszystkie funkcje numeryczne z `tanh` transformacji. A `tanh` przekształcania konwertuje wszystkie funkcje liczbowe wartości do zakresu od 0 do 1 przy jednoczesnym zachowaniu ogólny rozkład wartości.

**Two-Class pomocy technicznej Vector Machine** modułu obsługuje funkcje ciągu, konwertowania go na potrzeby podzielonych na kategorie i następnie do binarnych funkcje o wartości 0 lub 1. Więc nie potrzebujemy do normalizacji tych funkcji.

## <a name="models"></a>Modele

Ponieważ możemy zastosować dwoma klasyfikatorami **Two-Class pomocy technicznej Vector Machine** (SVM) i **Two-Class Boosted Decision drzewa**, a także korzystają z dwóch zestawów danych, wygenerowanie łącznie z czterech modeli:

- SVM uczony przy użyciu oryginalnych danych.
- SVM uczony przy użyciu zreplikowanych danych.
- Wzmocnione drzewo decyzyjne uczony przy użyciu oryginalnych danych.
- Wzmocnione drzewo decyzyjne uczony przy użyciu zreplikowanych danych.

Standardowa eksperymentalne przepływu pracy firma Microsoft umożliwia tworzenie, szkolenie i testowanie modeli:

1. Inicjowanie algorytmów uczenia, za pomocą **Two-Class pomocy technicznej Vector Machine** i **Two-Class Boosted Decision drzewa**.
1. Użyj **Train Model** zastosowanie algorytmu do danych i utworzyć model rzeczywistych.
1. Użyj **Score Model** aby wygenerować wyniki przy użyciu przykładów testu.

Na poniższym diagramie przedstawiono część tego eksperymentu, w którym zestawy szkolenia oryginalnego i replikowane są używane do trenowania dwa różne modele SVM. **Uczenie modelu** jest podłączony do zestawu szkoleniowego i **Score Model** jest połączony z zestawu testowego.

![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

Na etapie oceny eksperymentu możemy obliczyć dokładność każdego z czterech modeli. W tym eksperymencie używamy **Evaluate Model** do przykładów, które mają ten sam błędu klasyfikacji porównania kosztów.

**Evaluate Model** modułu można obliczyć metryki wydajności dla maksymalnie dwa modele ocenami. Dlatego używamy jedno wystąpienie **Evaluate Model** ocena dwa modele SVM i inne wystąpienie **Evaluate Model** można obliczyć dwa modele wzmocnione drzewo decyzyjne.

Należy zauważyć, że zestaw danych replikowanych testu jest używany jako dane wejściowe dla **Score Model**. Innymi słowy wyniki końcowe dokładność obejmują kosztu w celu uzyskania etykiety jest nieprawidłowy.

## <a name="combine-multiple-results"></a>Łączenie wielu wyników

**Evaluate Model** modułu tworzy tabelę z jednego wiersza, który zawiera różne metryki. Aby utworzyć pojedynczy zestaw wyników, dokładności, najpierw używamy **Dodaj wiersze** do połączenia wyników w jedną tabelę. Następnie używamy poniższy skrypt języka Python w **wykonanie skryptu Python** modułu, aby dodać nazwę modelu i podejście szkolenia dla każdego wiersza w tabeli wyników:

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    new_cols = pd.DataFrame(
            columns=["Algorithm","Training"],
            data=[
                ["SVM", "weighted"],
                ["SVM", "unweighted"],
                ["Boosted Decision Tree","weighted"],
                ["Boosted Decision Tree","unweighted"]
            ])

    result = pd.concat([new_cols, dataframe1], axis=1)
    return result,
```

## <a name="results"></a>Wyniki

Aby wyświetlić wyniki doświadczenia, kliknąć prawym przyciskiem myszy wyjściem Visualize ostatniego **Select Columns in Dataset** modułu.

![Wizualizowanie danych wyjściowych](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

Pierwsza kolumna zawiera algorytm używany do generowania modelu uczenia maszynowego.
Druga kolumna wskazuje typ zestawu szkoleniowego.
Trzecia kolumna zawiera wartości kosztowej dokładności.

Z tymi wynikami, widać, że najlepszej dokładności jest świadczona przez model, który został utworzony za pomocą **Two-Class pomocy technicznej Vector Machine** i przeszkolony szkolenia replikowanego zestawu danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 1 - regresji. Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 - regresji. Porównaj algorytmy Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Próba 3 — klasyfikacji: Prognozowanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 5 - klasyfikacji: Przewidywanie zmienności](ui-sample-classification-predict-churn.md)
