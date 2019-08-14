---
title: 'Przykład #3 interfejsu wizualizacji: Klasyfikacja do prognozowania ryzyka kredytowego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć klasyfikator uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 7565f94910d0e926682a72af42b02059fe7295ea
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68990050"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego

Dowiedz się, jak utworzyć klasyfikator uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji. Ten przykład pociąga za pomocą dwuklasowego **drzewa decyzyjnego** , aby przewidzieć ryzyko kredytowe (wysokie lub niskie) w oparciu o informacje o aplikacji kredytowej, takie jak historia kredytów, wiek i liczba kart kredytowych.

Ponieważ próbujemy odpowiedzieć na pytanie "? jest to nazywane problemem klasyfikacji. Można jednak zastosować ten sam podstawowy proces do rozwiązywania dowolnego typu problemu uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Oto ukończony wykres dla tego eksperymentu:

![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla przykładowego eksperymentu 3:

    ![Otwieranie eksperymentu](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Powiązana przykład

[Przykład 4 — Klasyfikacja: Prognoza ryzyka kredytowego (zależnie](ui-sample-classification-predict-credit-risk-cost-sensitive.md) od kosztów) oferuje zaawansowany eksperyment, który rozwiązuje ten sam problem co ten eksperyment. Pokazuje, jak przeprowadzić klasyfikację z _uwzględnieniem kosztów_ przy użyciu modułu **skryptu języka Python** i porównać wydajność dwóch binarnych algorytmów klasyfikacji. Jeśli chcesz dowiedzieć się więcej na temat sposobu tworzenia eksperymentów klasyfikacji, zapoznaj się z tym tematem.

## <a name="data"></a>Data

Korzystamy z niemieckiego zestawu danych kart kredytowych z repozytorium UC Irvine.
Zestaw danych zawiera 1 000 próbek z 20 funkcjami i 1 etykietą. Każdy przykład reprezentuje osobę. Funkcje obejmują funkcje liczbowe i kategorii. Zapoznaj się z [witryną sieci Web programu UCI](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) , aby poznać znaczenie funkcji kategorii. Ostatnia kolumna to etykieta, która oznacza ryzyko kredytowe i ma tylko dwie możliwe wartości: wysokie ryzyko kredytowe = 2 i niskie ryzyko kredytowe = 1.

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

Wykonaj następujące kroki, aby utworzyć eksperyment:

1. Przeciągnij moduł zestawu danych z karty kredytowej w języku niemieckim do kanwy eksperymentu.
1. Dodaj moduł **edytowania metadanych** , aby można było dodać znaczące nazwy dla każdej kolumny.
1. Dodaj moduł **Split Data (podział danych** ), aby utworzyć zestawy szkoleniowe i testowe. Ustaw ułamek wierszy w pierwszym zestawie danych wyjściowych na 0,7. To ustawienie określa, że 70% danych będzie wyprowadzane na lewy port modułu i reszta na właściwy port. Używamy lewego zestawu danych do szkolenia i po prawej stronie do testowania.
1. Dodaj moduł **drzewa decyzyjnego z dwoma klasami** , aby zainicjować podwyższający klasyfikator drzewa decyzyjnego.
1. Dodaj moduł **uczenia modelu** . Połącz klasyfikatora z poprzedniego kroku z lewym portem wejściowym **modelu uczenia**. Dodaj zestaw szkoleniowy (lewy port wyjściowy **danych podziału**) do właściwego portu wejściowego **modelu uczenia**. **Model uczenia** będzie szkolić klasyfikatora.
1. Dodaj moduł **modelu oceny** i połącz go z modułem. Następnie Dodaj zestaw testów (prawidłowy port **danych podziału**) do **modelu wynikowego**. **Model wynikowy** wykona przewidywania. Możesz wybrać swój port wyjściowy, aby zobaczyć przewidywania i pozytywne wartości prawdopodobieństwa.
1. Dodaj moduł **oceny modelu** i Połącz wynikowy zestaw danych z jego lewym portem wejściowym. Aby wyświetlić wyniki oceny, wybierz port wyjściowy modułu **oceny modelu** i wybierz opcję **Wizualizuj**.

Oto kompletny wykres eksperymentu:

![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Wyniki

![Oceń wyniki](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

W wynikach oceny można zobaczyć, że AUC modelu to 0,776. Pod kątem progu 0,5 precyzja to 0,621, odwołanie jest 0,456, a wynik F1 to 0,526.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1 — regresja: Przewidywanie ceny samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 — Regresja: Porównanie algorytmów do prognozowania cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: Prognozowanie zmian](ui-sample-classification-predict-churn.md)
- [Przykład 6 — Klasyfikacja: Przewidywanie opóźnień lotów](ui-sample-classification-predict-flight-delay.md)
