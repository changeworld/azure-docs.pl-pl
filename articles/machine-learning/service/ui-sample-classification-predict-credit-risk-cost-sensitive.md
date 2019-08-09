---
title: Zmianę Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)
titleSuffix: Azure Machine Learning service
description: W tym artykule przedstawiono sposób tworzenia złożonego eksperymentu uczenia maszynowego za pomocą interfejsu wizualnego. Dowiesz się, jak zaimplementować niestandardowe skrypty języka Python i porównać wiele modeli, aby wybrać najlepszą opcję.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 942d6fa6db7ee2fc07fd11d3448ac7ec96c3bd43
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845964"
---
# <a name="sample-4---classification-predict-credit-risk-cost-sensitive"></a>Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)

W tym artykule przedstawiono sposób tworzenia złożonego eksperymentu uczenia maszynowego za pomocą interfejsu wizualnego. Dowiesz się, jak wdrożyć logikę niestandardową przy użyciu skryptów języka Python i porównać wiele modeli, aby wybrać najlepszą opcję.

Ten przykład pociąga za niego klasyfikatora do przewidywania ryzyka kredytowego przy użyciu informacji o aplikacji kredytowej, takich jak historia kredytów, wiek i liczba kart kredytowych. Można jednak zastosować Koncepcje opisane w tym artykule, aby poznać własne problemy z uczeniem maszynowym.

Jeśli dopiero zaczynasz pracę z uczeniem maszynowym, możesz najpierw zacząć korzystać z [przykładu klasyfikatora Basic](ui-sample-classification-predict-credit-risk-basic.md) .

Oto ukończony wykres dla tego eksperymentu:

[![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla przykładowego eksperymentu 4:

    ![Otwieranie eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/open-sample4.png)

## <a name="data"></a>Data

Korzystamy z niemieckiego zestawu danych kart kredytowych z repozytorium UC Irvine. Ten zestaw danych zawiera 1 000 próbek z 20 funkcjami i 1 etykietą. Każdy przykład reprezentuje osobę. 20 funkcji obejmuje funkcje liczbowe i kategorii. Aby uzyskać więcej informacji na temat zestawu danych, zobacz [witrynę sieci Web UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) . Ostatnia kolumna to etykieta, która oznacza ryzyko kredytowe i ma tylko dwie możliwe wartości: wysokie ryzyko kredytowe = 2 i niskie ryzyko kredytowe = 1.

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

W tym doświadczeniu porównamy dwa różne podejścia do generowania modeli w celu rozwiązania tego problemu:

- Szkolenie z oryginalnym zestawem danych.
- Szkolenie z replikowanym zestawem danych.

W obu podejściach szacujemy modele przy użyciu zestawu danych testowych z replikacją, aby upewnić się, że wyniki są wyrównane przy użyciu funkcji koszt. Przetestujemy dwa klasyfikatory z obu metod: **Dwie klasy obsługują maszyny wektorowe** i **dwuklasowe drzewo decyzyjne**.

Koszt nieznacznego klasyfikowania przykładu niskiego ryzyka jest równy 1, a koszt nieznaczącego klasyfikowania przykładu wysokiego ryzyka wynosi 5. Używamy modułu **skryptu języka Python** w celu uwzględnienia tego kosztu nieprawidłowej klasyfikacji.

Oto wykres eksperymentu:

[![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="data-processing"></a>Przetwarzanie danych

Zaczynamy od używania modułu **edytora metadanych** do dodawania nazw kolumn w celu zastąpienia domyślnych nazw kolumn nazwami z bardziej zrozumiałymi nazwami uzyskanymi z opisu zestawu danych w witrynie UCI. W **nowej kolumnie Nazwa kolumny** w **edytorze metadanych**udostępniamy nowe nazwy kolumn jako wartości rozdzielane przecinkami.

Następnie generujemy zestawy szkoleniowe i testowe używane do tworzenia modelu przewidywania ryzyka. Ten sam zestaw danych został podzielony na zestawy szkoleniowe i testowe o takim samym rozmiarze przy użyciu modułu **Split Data** . Aby utworzyć zestawy o równym rozmiarze, ustawiamy **ułamek wierszy w pierwszej opcji wyjściowego zestawu danych** na 0,5.

### <a name="generate-the-new-dataset"></a>Generuj nowy zestaw danych

Ze względu na to, że koszt podnoszenia ryzyka jest wysoki, ustawiamy koszt nieodpowiedniej klasyfikacji w następujący sposób:

- W przypadku przypadków o wysokim ryzyku sklasyfikowanych jako niskie ryzyko: 5
- W przypadku przypadków o niskim ryzyku sklasyfikowanych jako wysokie ryzyko: 1

Aby odzwierciedlić tę funkcję kosztu, generujemy nowy zestaw danych. W nowym zestawie danych każdy przykład wysokiego ryzyka jest replikowany pięć razy, ale liczba przykładów z niskim ryzykiem nie ulega zmianie. Dane są dzielone na szkolenia i testowe zestawy danych przed replikacją, aby zapobiec występowaniu tego samego wiersza w obu zestawach.

Aby replikować dane o wysokim ryzyku, ten kod w języku Python zostanie umieszczony w module **wykonywania skryptu języka Python** :

```Python
import pandas as pd

def azureml_main(dataframe1 = None, dataframe2 = None):

    df_label_1 = dataframe1[dataframe1.iloc[:, 20] == 1]
    df_label_2 = dataframe1[dataframe1.iloc[:, 20] == 2]

    result = df_label_1.append([df_label_2] * 5, ignore_index=True)
    return result,
```

Moduł **wykonywania skryptu języka Python** replikuje zarówno zestaw danych szkoleniowych, jak i testowych.

### <a name="feature-engineering"></a>Inżynieria funkcji

Algorytm **maszyny wektorowej obsługujący dwie klasy** wymaga znormalizowanych danych. Dlatego używamy modułu **normalizowanie danych** do normalizacji zakresów wszystkich funkcji liczbowych z `tanh` przekształceniem. `tanh` Transformacja konwertuje wszystkie funkcje liczbowe na wartości z zakresu od 0 do 1, zachowując ogólną dystrybucję wartości.

Dwuklasowy moduł **maszyny wektorowej** obsługuje funkcje ciągów, konwertując je do funkcji kategorii, a następnie do funkcji binarnych o wartości 0 lub 1. Dlatego nie trzeba znormalizować tych funkcji.

## <a name="models"></a>Modele

Ze względu na to, że stosujemy dwa klasyfikatory, **dwie klasy obsługują maszyny wektorowe** (SVM) i dwuklasowe **drzewo decyzyjne**, a także dwa zestawy danych generują łącznie cztery modele:

- SVM z oryginalnymi danymi.
- SVM z replikowanymi danymi.
- Wzmocnione drzewo decyzyjne przeszkolone z oryginalnymi danymi.
- Wzmocnione drzewo decyzyjne przeszkolone z replikowanymi danymi.

Używamy standardowego eksperymentalnego przepływu pracy do tworzenia, uczenia i testowania modeli:

1. Zainicjuj algorytmy uczenia przy użyciu dwuklasowej **maszyny wektorowej** i dwuklasowego **drzewa decyzyjnego**.
1. Użyj **modelu uczenia** , aby zastosować algorytm do danych i utworzyć rzeczywisty model.
1. Użyj **modelu oceny** , aby wygenerować wyniki przy użyciu przykładów testowych.

Na poniższym diagramie przedstawiono część tego eksperymentu, w której oryginalne i zreplikowane zestawy szkoleniowe są używane do uczenia dwóch różnych modeli SVM. **Model uczenia** jest połączony z zestawem szkoleniowym, a **model oceny** jest połączony z zestawem testów.

![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-cost-sensitive/score-part.png)

W fazie oceny eksperymentu obliczamy dokładność każdego z czterech modeli. Na potrzeby tego eksperymentu używamy do **szacowania modelu** , aby porównać przykłady, które mają ten sam koszt niesklasyfikowany.

Moduł **oceny modelu** może obliczać metryki wydajności dla maksymalnie dwóch modeli ocenionych. W związku z tym korzystamy z jednego wystąpienia **Oceń model** do oszacowania dwóch modeli SVM i innego wystąpienia **Oceń model** , aby oszacować dwa podwyższane modele drzewa decyzyjnego.

Zwróć uwagę, że replikowany zestaw danych testu jest używany jako dane wejściowe dla **modelu oceny**. Innymi słowy, końcowe wyniki dokładności obejmują koszt uzyskania nieprawidłowych etykiet.

## <a name="combine-multiple-results"></a>Łączenie wielu wyników

Moduł **Oceń model** tworzy tabelę z pojedynczym wierszem zawierającym różne metryki. Aby utworzyć pojedynczy zestaw wyników dokładności, najpierw użyjemy **Dodaj wiersze** , aby połączyć wyniki w jedną tabelę. Następnie użyjemy następującego skryptu języka Python w module **Wykonaj skrypt języka Python** , aby dodać nazwę modelu i metodę uczenia dla każdego wiersza w tabeli wyników:

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

Aby wyświetlić wyniki eksperymentu, można kliknąć prawym przyciskiem myszy wizualizację danych wyjściowych ostatnio **wybranych kolumn w module DataSet** .

![Wizualizacja danych wyjściowych](media/ui-sample-classification-predict-credit-risk-cost-sensitive/result.png)

W pierwszej kolumnie jest wyświetlany algorytm uczenia maszynowego używany do generowania modelu.
Druga kolumna wskazuje typ zestawu szkoleniowego.
Trzecia kolumna zawiera wartość dokładności z uwzględnieniem kosztów.

Z tych wyników można zobaczyć, że Najlepsza dokładność jest zapewniana przez model, który został utworzony z dwuklasową **maszyną wektorową** , i przeszkolony na zestawie danych szkolenia zreplikowane.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1 — regresja: Przewidywanie ceny samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 — Regresja: Porównanie algorytmów do prognozowania cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 5 — Klasyfikacja: Prognozowanie zmian](ui-sample-classification-predict-churn.md)
- [Przykład 6 — Klasyfikacja: Przewidywanie opóźnień lotów](ui-sample-classification-predict-flight-delay.md)