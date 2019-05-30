---
title: 'Regresja: Przewidywanie ceny'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak tworzyć usługi machine learning modelu do prognozowania cen samochodów bez konieczności pisania nawet jednego wiersza kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: article
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 9dfa4b62f5cb79a5716f6f29651e85d0f8a3a409
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65787858"
---
# <a name="sample-1---regression-predict-price"></a>Przykład 1 - regresji. Przewidywanie ceny

Dowiedz się, jak tworzyć usługi machine learning model regresji, bez napisania choćby jednego wiersza kodu za pomocą interfejsu wizualnego.

To eksperymentować pociągów **decyzji regresor lasu** do prognozowania samochodu jego ceny na podstawie funkcji technicznych, takich jak marka, model, moc i rozmiar. Ponieważ chcemy znaleźć odpowiedź na pytanie "Ile?" jest to problem regresji. Jednakże można zastosować te same czynności podstawowe, w tym eksperymencie rozwiązania dowolnego typu maszyny nauczanym problemem, czy jest to regresji, klasyfikacji, klastrowania i tak dalej.

Podstawowe kroki model uczenia maszynowego szkoleń są:

1. Pobieranie danych
1. Wstępne przetwarzanie danych
1. Uczenie modelu
1. Ocenianie modelu

Oto ostateczną, zakończone wykres eksperymentu, który będziemy nad tym pracować. Firma Microsoft udostępni racjonalne uzasadnienie dla wszystkich modułów dzięki czemu można zmieniać podobne decyzje na własną rękę.

![Wykres eksperymentu](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz **Otwórz** przycisku w eksperymencie przykład 1:

    ![Otwórz eksperyment](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Pobieranie danych

W tym eksperymencie używamy **danych dotyczących cen samochodów (Raw)** zestaw danych, który pochodzi z repozytorium Learning maszyny UCI. Zestaw danych zawiera 26 kolumn, które zawierają informacje dotyczące samochodów, w tym marka, model, cena, funkcje pojazdu (np. liczba cylindrów), MPG i ubezpieczenia ryzyka. Celem tego eksperymentu jest przewidzieć cenę samochodu.

## <a name="pre-process-the-data"></a>Wstępne przetwarzanie danych

Zadania podrzędne przygotowania danych głównego obejmują czyszczenia danych, integracji, przekształcania, redukcji oraz dyskretyzacji lub zaokrąglania. W interfejsie visual można znaleźć modułów do wykonywania tych operacji i innych danych, przetwarzanie wstępne zadań w **przekształcania danych** grupy w panelu po lewej stronie.

Używamy **Select Columns in Dataset** modułu, aby wykluczyć kolumny znormalizowane straty, które mają wiele wartości. Następnie używamy **Clean Missing Data** można usunąć wierszy z brakującymi wartościami. Pozwala to utworzyć czystego zestaw danych szkoleniowych.

![Przetwarzanie wstępne danych](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Uczenie modelu

Machine learning problemy różnią się. Typowe zadania uczenia maszynowego obejmują klasyfikacji i klastrowania, regresji i polecania systemów, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań przypadków użycia. Po pobraniu algorytmu, należy dostosować jego parametry to w opracowywaniu dokładniejszych modeli. Następnie należy ocenić wszystkie modele, w oparciu o metryki, takie jak wydajność, dokładność i zrozumiałość informacji.

Ponieważ celem tego eksperymentu jest do prognozowania cen samochodów, a kolumna etykiety (cena) zawiera liczb rzeczywistych, model regresji jest dobrym wyborem. Biorąc pod uwagę, że wiele funkcji jest stosunkowo mały (mniej niż 100), a te funkcje nie są rozrzedzony, granic decyzji prawdopodobnie będzie nieliniowych. Dlatego używamy **regresji lasu decyzji** w tym eksperymencie.

Używamy **podziału danych** modułu, aby losowo podzielić dane wejściowe, aby zestaw danych szkoleniowych zawiera 70% oryginalne dane i testowania zestaw danych zawiera 30% oryginalne dane.

## <a name="test-evaluate-and-compare"></a>Testowanie, oceny i porównania

 Możemy podzielić zestawu danych i używać różnych zestawów danych do nauczenia i przetestowania modelu dokonanie oceny modelu więcej cel.

Po model jest uczony, używamy **Score Model** i **Evaluate Model** modułów do generowania wyników i Ewaluacja modeli.

**Ocenianie modelu** generuje prognoz dla zestawu danych testowych za pomocą uczonego modelu. Aby sprawdzić wynik, wybierz port wyjściowy **Score Model** , a następnie wybierz **Visualize**.

![Wynik oceny](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Firma Microsoft następnie przekazać wyniki do **Evaluate Model** modułu, aby wygenerować metryk oceny. Aby sprawdzić wynik, wybierz port wyjściowy **Evaluate Model** , a następnie wybierz **Visualize**.

![Wynik oceny](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Kolejne kroki

Zapoznaj się z innymi przykładami dostępne dla interfejsu wizualnego:

- [Przykład 2 - regresji. Porównaj algorytmy Prognozowanie cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Próba 3 — klasyfikacji: Prognozowanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 - klasyfikacji: Prognozowanie ryzyka kredytowego (koszt poufnych)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 - klasyfikacji: Przewidywanie zmienności](ui-sample-classification-predict-churn.md)