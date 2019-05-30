---
title: 'Regresja: Przewidzieć cenę i porównaj algorytmów'
titleSuffix: Azure Machine Learning service
description: W tym artykule przedstawiono sposób tworzenia eksperymentu uczenia maszynowego złożonych bez napisania choćby jednego wiersza kodu za pomocą interfejsu wizualnego. Dowiedz się, jak do trenowania i porównywania wielu modele regresji, aby przewidzieć cenę samochodu na podstawie funkcji Technical Preview
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: c8c813a2304797e71499a916e29c18f8bec2b389
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787793"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Przykład 2 - regresji. Przewidzieć cenę i porównaj algorytmów

Informacje o sposobie tworzenia eksperymentu uczenia maszynowego złożonych bez napisania choćby jednego wiersza kodu za pomocą interfejsu wizualnego. W tym przykładzie szkolenie modeli i porównuje wielu modele regresji, aby przewidzieć cenę samochodu na podstawie jego techniczne funkcji. Firma Microsoft udostępni uzasadnienie wyboru tych elementów wprowadzone w tym eksperymencie, dzięki czemu można czoła własne problemów uczenia maszynowego.

Jeśli po prostu rozpoczniesz pracę z usługą machine learning, może zająć się [wersję podstawową](ui-sample-regression-predict-automobile-price-basic.md) tego doświadczenia, aby wyświetlić podstawowe regresji, eksperymentowanie.

Oto wykres zakończone, w tym eksperymencie:

[![Wykres eksperymentu](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie przykład 2:

    ![Otwórz eksperyment](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

Do tworzenia eksperymentu, firma Microsoft wykonaj następujące kroki:

1. Pobierz dane.
1. Wstępne przetwarzanie danych.
1. Uczenie modelu.
1. Testowanie, oceny i Porównaj modele.

## <a name="get-the-data"></a>Pobieranie danych

W tym eksperymencie używamy **danych dotyczących cen samochodów (Raw)** zestaw danych, który pochodzi z repozytorium Learning maszyny UCI. Ten zestaw danych zawiera 26 kolumn, które zawierają informacje dotyczące samochodów, w tym marka, model, cena, funkcje pojazdu (np. liczba cylindrów), MPG i ubezpieczenia ryzyka. Celem tego eksperymentu jest przewidzieć cenę samochodu.

## <a name="pre-process-the-data"></a>Wstępne przetwarzanie danych

Zadania podrzędne przygotowania danych głównego obejmują czyszczenia danych, integracji, przekształcania, redukcji oraz dyskretyzacji lub zaokrąglania. W interfejsie visual można znaleźć modułów do wykonywania tych operacji i innych danych, przetwarzanie wstępne zadań w **przekształcania danych** grupy w panelu po lewej stronie.

W tym eksperymencie używamy **Select Columns in Dataset** modułu, aby wykluczyć kolumny znormalizowane straty, które mają wiele wartości. Następnie używamy **Clean Missing Data** można usunąć wierszy z brakującymi wartościami. Pozwala to utworzyć czystego zestaw danych szkoleniowych.

![Przetwarzanie wstępne danych](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Uczenie modelu

Machine learning problemy różnią się. Typowe zadania uczenia maszynowego obejmują klasyfikacji i klastrowania, regresji i polecania systemów, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań przypadków użycia. Po pobraniu algorytmu, należy dostosować jego parametry to w opracowywaniu dokładniejszych modeli. Następnie należy ocenić wszystkie modele, w oparciu o metryki, takie jak wydajność, dokładność i zrozumiałość informacji.

Ponieważ celem tego eksperymentu jest do prognozowania cen samochodów, a kolumna etykiety (cena) zawiera liczb rzeczywistych, model regresji jest dobrym wyborem. Biorąc pod uwagę, że wiele funkcji jest stosunkowo mały (mniej niż 100), a te funkcje nie są rozrzedzony, granic decyzji prawdopodobnie będzie nieliniowych.

Aby porównać wydajność różnych algorytmów, użyjemy dwóch algorytmów nieliniowych, **wzmocnione regresji drzewa decyzyjnego** i **regresji lasu decyzji**, tworzenie modeli. Oba algorytmy mają parametry, które mogą być zmieniane, ale możemy użyć wartości domyślnych w tym eksperymencie.

Używamy **podziału danych** modułu, aby losowo podzielić dane wejściowe, aby zestaw danych szkoleniowych zawiera 70% oryginalne dane i testowania zestaw danych zawiera 30% oryginalne dane.

## <a name="test-evaluate-and-compare-the-models"></a>Testowanie, oceny i Porównaj modele

Używamy dwa różne zestawy danych losowo wybranym szkolenie, a następnie przetestować model, zgodnie z opisem w poprzedniej sekcji. Możemy podzielić zestawu danych i używać różnych zestawów danych do nauczenia i przetestowania modelu dokonanie oceny modelu więcej cel.

Po model jest uczony, używamy **Score Model** i **Evaluate Model** modułów do generowania wyników i Ewaluacja modeli. **Ocenianie modelu** generuje prognoz dla zestawu danych testowych za pomocą uczonego modelu. Firma Microsoft następnie przekazać wyniki do **Evaluate Model** do generowania metryk oceny.

W tym eksperymencie użyjemy dwóch wystąpień **Evaluate Model** do porównywania dwóch par modeli.

Po pierwsze firma Microsoft porównanie dwóch algorytmów na zestawie danych szkoleniowych.
Po drugie możemy porównać dwa algorytmy testowanie zestawu danych.
Poniżej przedstawiono wyniki:

![Należy porównać wyniki](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Te wyniki pokazują, że model utworzonych za pomocą **wzmocnione regresji drzewa decyzyjnego** ma niższy głównego oznacza pierwiastek błędu niż model oparty na **regresji lasu decyzji**.

Oba algorytmy mają niższe błąd na zestaw danych szkoleniowych niż niewidzianych testowanie zestawu danych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 1 - regresji. Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Próba 3 — klasyfikacji: Prognozowanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 - klasyfikacji: Przewidywanie zmienności](ui-sample-classification-predict-churn.md)
