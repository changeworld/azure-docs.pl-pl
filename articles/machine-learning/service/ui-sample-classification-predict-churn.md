---
title: 'Klasyfikacja: Przewidywanie zmienności appetency i sprzedaży w górę '
titleSuffix: Azure Machine Learning service
description: Ten interfejs graficzny przykładowy eksperyment pokazuje Klasyfikator binarny przewidywanie zmienności, typowe zadanie zarządzania relacjami z klientami (CRM).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 42724f5fcb3101015cef0d218a3d548f349646be
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65785822"
---
# <a name="sample-5---classification-predict-churn-appetency-and-up-selling"></a>Przykład 5 - klasyfikacji: Przewidywanie zmienności appetency i sprzedaży w górę 

Informacje o sposobie tworzenia eksperymentu uczenia maszynowego złożonych bez napisania choćby jednego wiersza kodu za pomocą interfejsu wizualnego.

Ten eksperyment szkolenie modeli trzy, **dwuklasowych wzmocnione drzewo decyzyjnego dla** klasyfikatorów do prognozowania typowe zadania związane z systemami zarządzania (CRM) relacji klientów: zmian appetency i sprzedaży w górę. Wartości danych i etykiet są podzielone między wiele źródeł danych i zaszyfrowane do anonimizuj informacje o klientach, jednak firma Microsoft może nadal używać interfejs graficzny łączenie zestawów danych do nauczenia modelu, używając zaszyfrowane wartości.

Ponieważ którą próbujemy znaleźć odpowiedź na pytanie "Która opcja?" jest to problem klasyfikacji. Jednakże można zastosować te same czynności w tym eksperymencie rozwiązania dowolnego typu maszyny nauczanym problemem, czy jest to regresji, klasyfikacji, klastrowania i tak dalej.

Oto wykres zakończone, w tym eksperymencie:

![Wykres eksperymentu](./media/ui-sample-classification-predict-churn/experiment-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie 5 próbki.

    ![Otwórz eksperyment](media/ui-sample-classification-predict-churn/open-sample5.png)

## <a name="data"></a>Dane

Dane, które służy do tego eksperymentu jest z zawodów KDD Cup 2009. Zestaw danych zawiera 50 000 wierszy i 230 kolumn funkcji. Zadanie jest przewidywanie zmienności appetency i proponując dla klientów, którzy korzystają z tych funkcji. Aby uzyskać więcej informacji na temat danych i zadania, zobacz [witryny sieci Web z zawodów KDD](https://www.kdd.org/kdd-cup/view/kdd-cup-2009).

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

Ten interfejs graficzny przykładowy eksperyment pokazuje Klasyfikator binarny przewidywanie zmienności appetency i proponując, typowe zadanie zarządzania relacjami z klientami (CRM).

Po pierwsze robimy pewne proste przetwarzanie danych.

- Nieprzetworzone zestaw danych zawiera wiele brakujące wartości. Używamy **Clean Missing Data** modułu, aby zastąpić brakujący wartości od 0.

    ![Wyczyść zestawu danych](./media/ui-sample-classification-predict-churn/cleaned-dataset.png)

- Funkcje i odpowiedni postęp dokonany w, appetency, oraz etykiety proponując znajdują się w różnych zestawów danych. Używamy **Dodaj kolumny** modułu do dołączenia kolumn etykiety kolumn funkcji. Pierwsza kolumna **Col1**, jest to kolumna etykiety. Pozostałe kolumny, **Var1**, **Var2**i tak dalej są kolumnami funkcji.

    ![Dodaj zestaw danych kolumny](./media/ui-sample-classification-predict-churn/added-column1.png)

- Używamy **podziału danych** modułu, aby podzielić zestawu danych na szkolenie i testowanie zestawów.

    Następnie używamy Klasyfikator binarny wzmocnione drzewo decyzyjne z parametrami domyślnymi tworzyć modele predykcyjne. Firma Microsoft tworzy jeden model na zadanie, oznacza to, że jeden model każdego z nich do przewidywania proponując appetency i współczynnika zmian.

## <a name="results"></a>Wyniki

Wizualizowanie danych wyjściowych **Evaluate Model** modułu, aby wyświetlić wydajności modelu w zestawie testów. Zadania proponując WIELOKLASOWA krzywa pokazuje, czy model jest lepiej niż model losowy. W obszarze pod krzywą (AUC) ma 0.857. Na próg 0,5 dokładność jest 0,7 odwołania jest 0.463 i wynik F1 jest 0.545.

![Ocena wyników](./media/ui-sample-classification-predict-churn/evaluate-result.png)

 Możesz przenieść **próg** suwaka i zobacz metryki Zmień zadanie klasyfikacji binarnej.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 1 - regresji. Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 - regresji. Porównaj algorytmy Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Próba 3 — klasyfikacji: Prognozowanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
