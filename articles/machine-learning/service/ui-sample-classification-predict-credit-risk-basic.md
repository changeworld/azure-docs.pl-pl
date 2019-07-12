---
title: 'Klasyfikacja: Przewidywanie ryzyka kredytowego'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć usługi machine learning klasyfikatora bez napisania choćby jednego wiersza kodu za pomocą interfejsu wizualnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 6ec91ca83d7aa1bc5e6c290d35b573a60cc0ed19
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605770"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Próba 3 — klasyfikacji: Przewidywanie ryzyka kredytowego

Dowiedz się, jak tworzyć usługi machine learning klasyfikatora bez napisania choćby jednego wiersza kodu za pomocą interfejsu wizualnego. W tym przykładzie szkolenie modeli **dwuklasowych wzmocnione drzewo decyzyjnego** do prognozowania środki ryzyko oceniane (wysokiej lub niskiej) na podstawie informacji o aplikacji środków, takich jak Historia kredytów, wiek i liczba kart kredytowych.

Ponieważ którą próbujemy znaleźć odpowiedź na pytanie "Która opcja?" jest to problem klasyfikacji. Jednakże można zastosować ten sam proces podstawowych do dowolnego typu maszyny nauczanym problemem czy jest to regresji, klasyfikacji, klastrowania i tak dalej.

Oto wykres zakończone, w tym eksperymencie:

![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie przykład 3:

    ![Otwórz eksperyment](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Powiązane próbki

[Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt wielkość liter)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) zapewnia zaawansowane eksperymentu, która rozwiązuje ten sam problem co tego eksperymentu. Pokazuje, jak przeprowadzić _koszt poufnych_ Klasyfikacja za pomocą **wykonanie skryptu Python** modułu i Porównaj wydajność dwa algorytmy klasyfikacji binarnej. Jeśli chcesz dowiedzieć się więcej o tym, jak tworzyć eksperymenty klasyfikacji, odwołując się do niej.

## <a name="data"></a>Data

Używamy zestawu danych karty kredytowej niemiecki z repozytorium Irvine Unikatowości.
Zestaw danych zawiera 1000 próbek z funkcjami 20 i 1 etykiety. Poszczególne przykładowe aplikacje przedstawiają osoby. Funkcje obejmują funkcje numeryczne i podzielonych na kategorie. Zobacz [UCI witryny sieci Web](https://archive.ics.uci.edu/ml/datasets/Statlog+%28German+Credit+Data%29) znaczenie kategorii funkcji. Ostatnia kolumna jest etykiety, który wskazuje ryzyko kredytowe, a ma tylko dwa możliwe wartości: ryzyko kredytowe wysoki = 2 i niskie ryzyko kredytowe = 1.

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

Firma Microsoft, wykonaj następujące kroki, aby utworzyć eksperyment:

1. Przeciągnij Moduł niemiecki karty kredytowej na danych zestawu danych do obszaru roboczego eksperymentu.
1. Dodaj **edytować metadane** modułu, abyśmy mogli dodać nazw opisowych dla każdej kolumny.
1. Dodaj **podziału danych** moduł służący do tworzenia zestawów szkolenia i testowania. Ustaw ułamek wierszy w pierwszym zestawie danych wyjściowych do 0,7. To ustawienie określa, że 70% danych będą dane wyjściowe do lewy port modułu i rest z prawym portem. Po lewej stronie zestawu danych służy do szkolenia i właściwy do testowania.
1. Dodaj **Two-Class Boosted Decision drzewa** modułu, aby zainicjować klasyfikatora drzewa decyzyjnego.
1. Dodaj **Train Model** modułu. Klasyfikator z poprzedniego kroku połączyć się z lewym portem wejściowym **Train Model**. Dodaj zestaw treningowy (po lewej stronie danych wyjściowych port **podziału danych**) z prawym portem wejściowym z **Train Model**. **Uczenie modelu** będzie uczyć klasyfikatora.
1. Dodaj **Score Model** modułu i połącz **Train Model** modułu do niego. Następnie dodaj zestaw testów (prawy port **podziału danych**) do **Score Model**. **Score Model** będzie prognozowania. Możesz wybrać jego port wyjściowy prognoz i prawdopodobieństwa dodatnią klasy.
1. Dodaj **Evaluate Model** modułu i połącz się jego lewy port wejściowy z z ocenami zestawu danych. Aby wyświetlić wyniki oceny, wybierz port wyjściowy **Evaluate Model** modułu, a następnie wybierz pozycję **Visualize**.

Oto wykres eksperymentu ukończone:

![Wykres eksperymentu](media/ui-sample-classification-predict-credit-risk-basic/overall-graph.png)

## <a name="results"></a>Wyniki

![Ocena wyników](media/ui-sample-classification-predict-credit-risk-basic/evaluate-result.png)

Wyniki oceny widać to 0.776 AUC modelu. Na próg 0,5 dokładność jest 0.621 odwołania jest 0,456 i wynik F1 jest 0.526.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 1 - regresji. Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 - regresji. Porównaj algorytmy Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 - klasyfikacji: Przewidywanie zmienności](ui-sample-classification-predict-churn.md)
- [Przykład 6 - klasyfikacji: Prognozowanie opóźnień lotów](ui-sample-classification-predict-flight-delay.md)