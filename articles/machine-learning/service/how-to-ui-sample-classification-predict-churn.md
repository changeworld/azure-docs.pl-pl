---
title: 'Przykład #5 interfejsu wizualizacji: Klasyfikacja do przewidywania zmian i pragnienie + sprzedawanie'
titleSuffix: Azure Machine Learning
description: Ten przykładowy przykład interfejsu wizualizacji przedstawia przewidywane zmiany klasyfikatora w postaci binarnej, czyli typowe zadanie zarządzania relacjami z klientami (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 260d94ddf2572979e819ee89dfcbd315ef3c4769
ms.sourcegitcommit: 2ed6e731ffc614f1691f1578ed26a67de46ed9c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71131937"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Przykład 5 — Klasyfikacja: Przewidywanie zmian, pragnienie i kupowanie 

Dowiedz się, jak utworzyć złożony eksperyment uczenia maszynowego bez pisania jednego wiersza kodu przy użyciu interfejsu wizualizacji.

Ten eksperyment pociąga za trzy, **dwuklasowe klasyfikatory drzewa decyzyjnego** do przewidywania typowych zadań dla systemów zarządzania relacjami z klientami (CRM): zmiany, pragnienie i sprzedaż. Wartości danych i etykiety są podzielone na wiele źródeł danych i szyfrowane w celu zachowywanie anonimowości informacji o klientach, jednak nadal możemy używać interfejsu wizualizacji do łączenia zestawów danych i uczenia modelu przy użyciu wartości zaszyfrowanych.

Ponieważ próbujesz odpowiedzieć na pytanie "? jest to nazywane problemem klasyfikacji, ale można zastosować tę samą logikę w tym projekcie, aby rozwiązać dowolny typ problemu uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Oto ukończony wykres dla tego eksperymentu:

![Wykres eksperymentu](./media/how-to-ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla eksperymentu przykładowego 5.

    ![Otwieranie eksperymentu](media/how-to-ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Data

Dane dla tego eksperymentu pochodzą z KDD filiżanki 2009. Zawiera on 50 000 wierszy i 230 kolumn funkcji. Zadaniem jest przewidywanie zmian, pragnienie i sprzedaży dla klientów korzystających z tych funkcji. Aby uzyskać więcej informacji na temat danych i zadania, zobacz [witrynę sieci Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

Ten przykładowy przykład interfejsu wizualizacji przedstawia przewidywane zmiany, pragnienie i sprzedaż w klasyfikatorach binarnych, czyli wspólne zadanie zarządzania relacjami z klientami (CRM).

Najpierw wykonaj kilka prostych operacji przetwarzania danych.

- Pierwotny zestaw danych zawiera wiele brakujących wartości. Użyj **nieoczyszczonego modułu danych** , aby zastąpić brakujące wartości wartością 0.

    ![Wyczyść zestaw danych](./media/how-to-ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funkcje i odpowiadające im zmiany, pragnienie i etykiety do sprzedaży znajdują się w różnych zestawach danych. Użyj modułu **dodawania kolumn** , aby dołączyć kolumny etykiet do kolumn funkcji. Pierwsza kolumna, **Kol1**, jest kolumną etykiety. Pozostałe kolumny, **var1**, **Var2**i tak dalej, są kolumnami funkcji.

    ![Dodaj zestaw danych kolumny](./media/how-to-ui-sample-classification-predict-churn/added-column1.png)

- Użyj modułu **Split Data** , aby podzielić zestaw danych na zestawy pociągowe i testowe.

    Następnie użyj podwyższanego klasyfikatora drzewa decyzyjnego z domyślnymi parametrami w celu skompilowania modeli predykcyjnych. Kompiluj jeden model na zadanie, czyli jeden model każdy do przewidywania sprzedaży, pragnienie i zmian.

## <a name="results"></a>Wyniki

Wizualizuj dane wyjściowe modułu **oceny modelu** , aby zobaczyć wydajność modelu w zestawie testów. W przypadku zadania do sprzedaży w górę krzywa ROC pokazuje, że model jest lepszy niż model losowy. Obszar pod krzywą (AUC) to 0,857. Pod kątem progu 0,5 precyzja to 0,7, odwołanie jest 0,463, a wynik F1 to 0,545.

![Oceń wyniki](./media/how-to-ui-sample-classification-predict-churn/evaluate-result.png)

 Suwak **progu** można przenieść i zobaczyć zmianę metryk dla zadania klasyfikacji binarnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1 — regresja: Przewidywanie ceny samochodów](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 — Regresja: Porównanie algorytmów do prognozowania cen samochodów](how-to-ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 6 — Klasyfikacja: Przewidywanie opóźnień lotów](how-to-ui-sample-classification-predict-flight-delay.md)
