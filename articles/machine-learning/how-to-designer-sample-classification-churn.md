---
title: 'Projektant: przykład prognozowania zmian'
titleSuffix: Azure Machine Learning
description: Postępuj zgodnie z tym przykładem klasyfikacji, aby przewidzieć zmiany w programie Azure Machine Learning Designer & podwyższane drzewa decyzyjne.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: sgilley
ms.date: 12/25/2019
ms.openlocfilehash: 88f688608a0ae3d435699362f9326c7c02d494a4
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76311120"
---
# <a name="use-boosted-decision-tree-to-predict-churn-with-azure-machine-learning-designer"></a>Używanie podwyższania drzewa decyzyjnego do przewidywania zmian za pomocą narzędzia Azure Machine Learning Designer

**Przykład projektanta 5**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dowiedz się, jak utworzyć złożony potok uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu narzędzia Projektant.

Ten potok pociąga za **dwie klasy klasyfikatory drzewa decyzyjnego** , aby przewidzieć typowe zadania związane z zarządzaniem relacjami z klientami (CRM) dla klientów. Wartości i etykiety danych są podzielone na wiele źródeł danych i szyfrowane w celu zachowywanie anonimowości informacji o klientach, jednak nadal możemy używać projektanta do łączenia zestawów danych i uczenia modelu przy użyciu wartości zasłoniętych.

Ponieważ próbujesz odpowiedzieć na pytanie "? jest to nazywane problemem klasyfikacji, ale można zastosować tę samą logikę pokazaną w tym przykładzie, aby rozwiązać dowolny typ problemu uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Oto ukończony wykres dla tego potoku:

![Wykres potoku](./media/how-to-designer-sample-classification-churn/pipeline-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknij pozycję przykład 5, aby ją otworzyć. 

## <a name="data"></a>Dane

Dane dla tego potoku pochodzą z KDD filiżanki 2009. Zawiera on 50 000 wierszy i 230 kolumn funkcji. Zadaniem jest przewidywanie zmian, pragnienie i sprzedaży dla klientów korzystających z tych funkcji. Aby uzyskać więcej informacji na temat danych i zadania, zobacz [witrynę sieci Web KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="pipeline-summary"></a>Podsumowanie potoku

W tym przykładowym potoku w projektancie przedstawiono dane binarne klasyfikatora dotyczące zmian, pragnienie i sprzedaży, typowe zadanie zarządzania relacjami z klientami (CRM).

Najpierw kilka prostych operacji przetwarzania danych.

- Nieprzetworzony zestaw danych ma wiele wartości. Użyj **nieoczyszczonego modułu danych** , aby zastąpić brakujące wartości wartością 0.

    ![Wyczyść zestaw danych](media/how-to-designer-sample-classification-churn/sample5-dataset-1225.png)

- Funkcje i odpowiednie zmiany są w różnych zestawach danych. Użyj modułu **dodawania kolumn** , aby dołączyć kolumny etykiet do kolumn funkcji. Pierwsza kolumna, **Kol1**, jest kolumną etykiety. W wyniku wizualizacji można zobaczyć, że zestaw danych jest niezrównoważony. Istnieje bardziej negatywne (-1) przykłady niż pozytywne przykłady (+ 1). Użyjemy modułu **SMOTE** , aby później zwiększyć niereprezentowane przypadki.

    ![Dodaj zestaw danych kolumny](./media/how-to-designer-sample-classification-churn/sample5-addcol-1225.png)



- Użyj modułu **Split Data** , aby podzielić zestaw danych na zestawy pociągowe i testowe.

- Następnie użyj podwyższanego klasyfikatora drzewa decyzyjnego z domyślnymi parametrami w celu skompilowania modeli predykcyjnych. Kompiluj jeden model na zadanie, czyli jeden model każdy do przewidywania sprzedaży, pragnienie i zmian.

- W prawej części potoku używamy modułu **SMOTE** , aby zwiększyć procent pozytywnych przykładów. Wartość procentowa SMOTE jest ustawiana na 100, aby dwukrotnie uzyskać pozytywne przykłady. Dowiedz się więcej na temat sposobu działania modułu SMOTE przy użyciu [modułu SMOTE reference0](algorithm-module-reference/smote.md).

## <a name="results"></a>Wyniki

Wizualizuj dane wyjściowe modułu **oceny modelu** , aby zobaczyć wydajność modelu w zestawie testów. 

![Ocena wyników](./media/how-to-designer-sample-classification-churn/sample5-evaluate-1225.png)

 Suwak **progu** można przenieść i zobaczyć zmianę metryk dla zadania klasyfikacji binarnej. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla projektanta:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)
