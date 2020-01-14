---
title: 'Projektant: klasyfikowanie, przykład przewidywania przychodów'
titleSuffix: Azure Machine Learning
description: Postępuj zgodnie z tym przykładem, kompilując klasyfikator bez kodu, aby przewidzieć przychody za pomocą programu Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 227bf490d1ac264bb54ff3d1ecf0ccc2b3ef2e0e
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75763556"
---
# <a name="build-a-classifier--use-feature-selection-to-predict-income-with-azure-machine-learning-designer"></a>Kompilowanie klasyfikatora & używanie wyboru funkcji do przewidywania przychodów za pomocą projektanta Azure Machine Learning

**Projektant (wersja zapoznawcza) — przykład 3**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dowiedz się, jak utworzyć klasyfikator uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu narzędzia Projektant (wersja zapoznawcza). Ten przykład pociąga **drzewa decyzyjne z dwoma klasami** do przewidywania dochodów z spisu dla dorosłych (> = 50 000 lub < = 50 000).

Ponieważ odpowiedź na pytanie dotyczy "? jest to nazywane problemem klasyfikacji. Można jednak zastosować ten sam podstawowy proces do rozwiązywania dowolnego typu problemu z uczeniem maszynowym, klasyfikacji, klastrowania i tak dalej.

Oto wykres końcowego potoku dla tego przykładu:

![Wykres potoku](./media/how-to-designer-sample-classification-predict-income/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknij przykład 3, aby go otworzyć.



## <a name="data"></a>Dane

Zestaw danych zawiera 14 funkcji i jedną kolumnę etykiety. Istnieje wiele typów funkcji, takich jak liczbowe i kategorii. Na poniższym diagramie przedstawiono fragment zestawu danych: ![dane](media/how-to-designer-sample-classification-predict-income/sample3-dataset-1225.png)



## <a name="pipeline-summary"></a>Podsumowanie potoku

Wykonaj następujące kroki, aby utworzyć potok:

1. Przeciągnij moduł danych binarnych dochodu spisu treści do kanwy potoku.
1. Dodaj moduł **Split Data (podział danych** ), aby utworzyć zestawy szkoleniowe i testowe. Ustaw ułamek wierszy w pierwszym zestawie danych wyjściowych na 0,7. To ustawienie określa, że 70% danych będzie wyprowadzane na lewy port modułu i reszta na właściwy port. Używamy lewego zestawu danych do szkolenia i po prawej stronie do testowania.
1. Dodaj moduł **wyboru funkcji na podstawie filtru** , aby wybrać 5 funkcji według PearsonCorreclation. 
1. Dodaj moduł **drzewa decyzyjnego z dwoma klasami** , aby zainicjować podwyższający klasyfikator drzewa decyzyjnego.
1. Dodaj moduł **uczenia modelu** . Połącz klasyfikatora z poprzedniego kroku z lewym portem wejściowym **modelu uczenia**. Połącz filtrowany zestaw danych z modułu wyboru funkcji opartego na filtrze jako zestawu danych szkoleniowych.  **Model uczenia** będzie szkolić klasyfikatora.
1. Dodaj przekształcenie wybierz kolumny i Zastosuj moduł transformacji, aby zastosować tę samą transformację (wybór funkcji na podstawie filtrowania) do testu zestawu danych.
![zastosowania-transformacja](./media/how-to-designer-sample-classification-predict-income/transformation.png)
1. Dodaj moduł **model oceny** i połącz go **z modułem** . Następnie Dodaj zestaw testów (dane wyjściowe modułu zastosowania przekształcenia, które zastosują wybór funkcji do zestawu testów) do **modelu wynikowego**. **Model wynikowy** wykona przewidywania. Możesz wybrać swój port wyjściowy, aby zobaczyć przewidywania i pozytywne wartości prawdopodobieństwa.


    Ten potok ma dwa moduły oceny, a po prawej stronie ma wykluczoną kolumnę etykiety przed wykonaniem przewidywania. Jest to przygotowana do wdrożenia punktu końcowego w czasie rzeczywistym, ponieważ dane wejściowe usługi sieci Web będą oczekiwać tylko na nieetykietę funkcji. 

1. Dodaj moduł **oceny modelu** i Połącz wynikowy zestaw danych z jego lewym portem wejściowym. Aby wyświetlić wyniki oceny, wybierz port wyjściowy modułu **oceny modelu** i wybierz opcję **Wizualizuj**.

## <a name="results"></a>Wyniki

![Ocena wyników](media/how-to-designer-sample-classification-predict-income/sample3-evaluate-1225.png)

W wynikach oceny można zobaczyć, że krzywe takie jak ROC, precyzja odwołania i niepomylene metryki. 

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla projektanta:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)
