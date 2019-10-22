---
title: 'Przykład interfejsu wizualizacji #5: Klasyfikacja do prognozowania zmian i pragnienie + do sprzedaży'
titleSuffix: Azure Machine Learning
description: Ten przykładowy potok interfejsu wizualizacji przedstawia przewidywane przedziały klasyfikatora w postaci binarnej, czyli typowe zadanie zarządzania relacjami z klientami (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 09/23/2019
ms.openlocfilehash: 82639779dde08bb1f71fb75dba62038dbf34d1b6
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693550"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Przykład 5 — Klasyfikacja: przewidywanie zmian, pragnienie i kupowanie 

Dowiedz się, jak utworzyć złożony potok uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji.

Ten potok pociąga za trzy, **dwuklasowe klasyfikatory drzewa decyzyjnego** do przewidywania typowych zadań dla systemów zarządzania relacjami z klientami (CRM): zmiany, pragnienie i sprzedaż. Wartości danych i etykiety są podzielone na wiele źródeł danych i szyfrowane w celu zachowywanie anonimowości informacji o klientach, jednak nadal możemy używać interfejsu wizualizacji do łączenia zestawów danych i uczenia modelu przy użyciu wartości zasłoniętych.

Ponieważ próbujesz odpowiedzieć na pytanie "? jest to nazywane problemem klasyfikacji, ale można zastosować tę samą logikę pokazaną w tym przykładzie, aby rozwiązać dowolny typ problemu uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Oto ukończony wykres dla tego potoku:

![Wykres potoku](./media/how-to-ui-sample-classification-predict-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla potoku przykładowego 5.

    ![Otwórz potok](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Dane

Dane dla tego potoku pochodzą z KDD filiżanki 2009. Zawiera on 50 000 wierszy i 230 kolumn funkcji. Zadaniem jest przewidywanie zmian, pragnienie i sprzedaży dla klientów korzystających z tych funkcji. Aby uzyskać więcej informacji na temat danych i zadania, zobacz [witrynę sieci Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Podsumowanie potoku

Ten przykładowy potok interfejsu wizualizacji przedstawia przewidywane zmiany, pragnienie i sprzedaż w klasyfikatorach binarnych, czyli wspólne zadanie zarządzania relacjami z klientami (CRM).

Najpierw kilka prostych operacji przetwarzania danych.

- Nieprzetworzony zestaw danych ma wiele wartości. Użyj **nieoczyszczonego modułu danych** , aby zastąpić brakujące wartości wartością 0.

    ![Wyczyść zestaw danych](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funkcje i odpowiadające im zmiany, pragnienie i etykiety do sprzedaży znajdują się w różnych zestawach danych. Użyj modułu **dodawania kolumn** , aby dołączyć kolumny etykiet do kolumn funkcji. Pierwsza kolumna, **Kol1**, jest kolumną etykiety. Pozostałe kolumny, **var1**, **Var2**i tak dalej, są kolumnami funkcji.

    ![Dodaj zestaw danych kolumny](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Użyj modułu **Split Data** , aby podzielić zestaw danych na zestawy pociągowe i testowe.

- Następnie użyj podwyższanego klasyfikatora drzewa decyzyjnego z domyślnymi parametrami w celu skompilowania modeli predykcyjnych. Kompiluj jeden model na zadanie, czyli jeden model każdy do przewidywania sprzedaży, pragnienie i zmian.

## <a name="results"></a>Wyniki

Wizualizuj dane wyjściowe modułu **oceny modelu** , aby zobaczyć wydajność modelu w zestawie testów. W przypadku zadania do sprzedaży w górę krzywa ROC pokazuje, że model jest lepszy niż model losowy. Obszar pod krzywą (AUC) to 0,857. Pod kątem progu 0,5 precyzja to 0,7, odwołanie jest 0,463, a wynik F1 to 0,545.

![Oceń wyniki](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Suwak **progu** można przenieść i zobaczyć zmianę metryk dla zadania klasyfikacji binarnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja: przewidywanie ryzyka kredytowego](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-ui-sample-classification-predict-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: Recenzje książek](how-to-ui-sample-text-classification.md)
