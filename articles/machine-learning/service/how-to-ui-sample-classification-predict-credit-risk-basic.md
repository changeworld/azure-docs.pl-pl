---
title: 'Przykład interfejsu wizualizacji #3: Klasyfikacja do prognozowania ryzyka kredytowego'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć klasyfikator uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 4649303b8ee643130b8e254f01bfffbe8ad9eb2b
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693498"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Przykład 3 — Klasyfikacja: przewidywanie ryzyka kredytowego

Dowiedz się, jak utworzyć klasyfikator uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji. Ten przykład potoku **dwuklasowego drzewa decyzyjnego** do przewidywania ryzyka kredytowego (wysokiej lub niskiej) w oparciu o informacje o aplikacji kredytowej, takie jak historia kredytów, wiek i liczba kart kredytowych.

Ponieważ odpowiedź na pytanie dotyczy "? jest to nazywane problemem klasyfikacji. Można jednak zastosować ten sam podstawowy proces do rozwiązywania dowolnego typu problemu uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Oto wykres końcowego potoku dla tego przykładu:

![Wykres potoku](media/how-to-ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla potoku przykładowego 3:

    ![Otwórz potok](media/how-to-ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Powiązana przykład

[Przykład 4 — Klasyfikacja: Prognoza ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md) oferuje zaawansowany potok, który rozwiązuje ten sam problem co ten przykład. Pokazuje, jak przeprowadzić klasyfikację z *uwzględnieniem kosztów* przy użyciu modułu **skryptu języka Python** i porównać wydajność dwóch binarnych algorytmów klasyfikacji. Jeśli chcesz dowiedzieć się więcej o tworzeniu potoków klasyfikacji, zapoznaj się z tym tematem.


## <a name="data"></a>Dane

Przykład używa niemieckiego zestawu danych karty kredytowej z repozytorium UC Irvine. Zawiera on 1 000 próbek z 20 funkcjami i 1 etykietą. Każdy przykład reprezentuje osobę. Funkcje obejmują funkcje liczbowe i kategorii. Zapoznaj się z [witryną sieci Web programu UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) , aby poznać znaczenie funkcji kategorii. Ostatnia kolumna to etykieta, która oznacza ryzyko kredytowe i ma tylko dwie możliwe wartości: wysokie ryzyko kredytowe = 2 i niskie ryzyko kredytowe = 1.

## <a name="pipeline-summary"></a>Podsumowanie potoku

Wykonaj następujące kroki, aby utworzyć potok:

1. Przeciągnij moduł zestawu danych Data Card karty kredytowej w języku niemieckim do kanwy potoku.
1. Dodaj moduł **edytowania metadanych** , aby można było dodać znaczące nazwy dla każdej kolumny.
1. Dodaj moduł **Split Data (podział danych** ), aby utworzyć zestawy szkoleniowe i testowe. Ustaw ułamek wierszy w pierwszym zestawie danych wyjściowych na 0,7. To ustawienie określa, że 70% danych będzie wyprowadzane na lewy port modułu i reszta na właściwy port. Używamy lewego zestawu danych do szkolenia i po prawej stronie do testowania.
1. Dodaj moduł **drzewa decyzyjnego z dwoma klasami** , aby zainicjować podwyższający klasyfikator drzewa decyzyjnego.
1. Dodaj moduł **uczenia modelu** . Połącz klasyfikatora z poprzedniego kroku z lewym portem wejściowym **modelu uczenia**. Dodaj zestaw szkoleniowy (lewy port wyjściowy **danych podziału**) do właściwego portu wejściowego **modelu uczenia**. **Model uczenia** będzie szkolić klasyfikatora.
1. Dodaj moduł **modelu oceny** i połącz go z **modułem** . Następnie Dodaj zestaw testów (prawidłowy port **danych podziału**) do **modelu wynikowego**. **Model wynikowy** wykona przewidywania. Możesz wybrać swój port wyjściowy, aby zobaczyć przewidywania i pozytywne wartości prawdopodobieństwa.
1. Dodaj moduł **oceny modelu** i Połącz wynikowy zestaw danych z jego lewym portem wejściowym. Aby wyświetlić wyniki oceny, wybierz port wyjściowy modułu **oceny modelu** i wybierz opcję **Wizualizuj**.

## <a name="results"></a>Wyniki

![Oceń wyniki](media/how-to-ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

W wynikach oceny można zobaczyć, że AUC modelu to 0,776. Pod kątem progu 0,5 precyzja to 0,621, odwołanie jest 0,456, a wynik F1 to 0,526.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-ui-sample-classification-predict-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-ui-sample-classification-predict-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: Recenzje książek](how-to-ui-sample-text-classification.md)