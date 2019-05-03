---
title: 'Klasyfikacja: Przewidywanie ryzyka kredytowego'
titleSuffix: Azure Machine Learning service
description: Ten interfejs graficzny przykładowy eksperyment pokazuje sposób wykonania binarnej klasyfikacji przewidzieć ryzyko kredytowe, oparte na informacjach dostarczonych w kredytowym.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/02/2019
ms.openlocfilehash: 3d4ec3c71aaed6bddb012fb17ee5bb96da00cd76
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028533"
---
# <a name="sample-3---classification-predict-credit-risk"></a>Próba 3 — klasyfikacji: Przewidywanie ryzyka kredytowego

Ten interfejs graficzny przykładowy eksperyment pokazuje sposób wykonania binarnej klasyfikacji przewidzieć ryzyko kredytowe, oparte na informacjach dostarczonych w kredytowym. Pokazuje, jak można wykonywać podstawowe klasyfikacji, w tym operacji przetwarzania danych, Podziel zestaw danych w zestawach szkolenie i testowanie, uczenie modelu, ocena zestawu danych testowych i ocenić prognozy.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie przykład 3:

    ![Otwórz eksperyment](media/ui-sample-classification-predict-credit-risk-basic/open-sample3.png)

## <a name="related-sample"></a>Powiązane próbki

[Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt wielkość liter)](ui-sample-classification-predict-credit-risk-cost-sensitive.md) zapewnia zaawansowane eksperymentu, która rozwiązuje ten sam problem co tego eksperymentu. Pokazuje, jak przeprowadzić _koszt poufnych_ Klasyfikacja za pomocą **wykonanie skryptu Python** modułu i Porównaj wydajność dwa algorytmy klasyfikacji binarnej. Jeśli chcesz dowiedzieć się więcej o tym, jak tworzyć eksperymenty klasyfikacji, odwołując się do niej.

## <a name="data"></a>Dane

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

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 1 - regresji. Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 2 - regresji. Porównaj algorytmy Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 - klasyfikacji: Przewidywanie zmienności](ui-sample-classification-predict-churn.md)