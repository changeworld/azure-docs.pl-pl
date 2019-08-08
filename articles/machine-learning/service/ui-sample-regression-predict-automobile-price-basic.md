---
title: Ubytk Przewidywanie ceny
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak utworzyć model uczenia maszynowego, aby przewidzieć cenę samochodów, bez konieczności pisania pojedynczego wiersza kodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 95373bdd58332f49307004067cdd04b9c313b935
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845948"
---
# <a name="sample-1---regression-predict-price"></a>Przykład 1 — regresja: Przewidywanie ceny

Dowiedz się, jak utworzyć model regresji uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji.

Ten eksperyment pociąga za **regresor Las decyzyjny** , aby przewidzieć cenę samochodu w oparciu o funkcje techniczne, takie jak marka, model, możliwości i rozmiar. Ponieważ próbujemy odpowiedzieć na pytanie "jak dużo?" jest to tzw. problem z regresją. Można jednak zastosować te same podstawowe kroki w tym eksperymentie, aby rozwiązać problem związany z każdym typem uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Podstawowe kroki związane z modelem uczenia maszynowego są następujące:

1. Pobieranie danych
1. Wstępnie przetwórz dane
1. Uczenie modelu
1. Ocenianie modelu

Oto końcowy, ukończony wykres eksperymentu, nad którym pracujemy. Udostępnimy uzasadnienie dla wszystkich modułów, dzięki czemu będziesz w stanie podjąć podobne decyzje.

![Wykres eksperymentu](media/ui-sample-regression-predict-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla przykładowego eksperymentu 1:

    ![Otwieranie eksperymentu](media/ui-sample-regression-predict-automobile-price-basic/open-sample1.png)

## <a name="get-the-data"></a>Pobieranie danych

W tym doświadczeniu używany jest zestaw **danych cen samochodów (RAW)** , który pochodzi z REPOZYTORIUM Machine Learning UCI. Zestaw danych zawiera 26 kolumn zawierających informacje o urządzeniach przenośnych, takich jak marka, model, Cena, funkcje pojazdu (na przykład liczba cylindrów), MPG i Ocena ryzyka dla ubezpieczenia. Celem tego eksperymentu jest przewidywanie ceny samochodu.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Główne zadania przygotowywania danych obejmują czyszczenie danych, integrację, transformację, redukcję i dyskretyzacji lub podziału. W interfejsie wizualizacji można znaleźć moduły do wykonywania tych operacji oraz inne zadania przetwarzania wstępnego danych w grupie **Przekształcanie danych** w panelu po lewej stronie.

Używamy modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ) do wykluczania znormalizowanych strat z wieloma brakującymi wartościami. Następnie użyj **czystego danych** , aby usunąć wiersze, które mają brakujące wartości. Ułatwia to utworzenie czystego zestawu danych szkoleniowych.

![Przetwarzanie wstępne danych](./media/ui-sample-regression-predict-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Uczenie modelu

Problemy dotyczące uczenia maszynowego są różne. Typowe zadania uczenia maszynowego obejmują klasyfikację, klastrowanie, regresję i systemy zalecające, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań dotyczących przypadku użycia. Po wybraniu algorytmu należy dostosować jego parametry, aby szkolić dokładniejszy model. Następnie należy oszacować wszystkie modele na podstawie metryk, takich jak dokładność, intelligibility i wydajność.

Ponieważ celem tego eksperymentu jest przewidywanie cen samochodów, a kolumna etykieta (cena) zawiera liczby rzeczywiste, model regresji jest dobrym rozwiązaniem. Biorąc pod uwagę, że liczba funkcji jest stosunkowo mała (mniej niż 100) i te funkcje nie są rozrzedzone, granica decyzji może być nieliniowa. Dlatego używamy **regresji lasów decyzyjnych** dla tego eksperymentu.

Używamy modułu **Split Data** , aby losowo podzielić dane wejściowe w taki sposób, że zestaw danych szkoleniowych zawiera 70% danych oryginalnych, a test DataSet zawiera 30% oryginalnych danych.

## <a name="test-evaluate-and-compare"></a>Testowanie, obliczanie i porównywanie

 W celu przeprowadzenia oceny modelu zestawu danych i przetestowania modelu należy użyć różnych zestawów

Po przeszkoleniu modelu używamy **modelu oceny** i oceny modułów **modelu** , aby generować przewidywane wyniki i szacować modele.

**Model oceny** generuje prognozy dla zestawu danych testowych przy użyciu nauczonego modelu. Aby sprawdzić wynik, wybierz port wyjściowy **modelu oceny** , a następnie wybierz opcję **Wizualizuj**.

![Wynik oceny](./media/ui-sample-regression-predict-automobile-price-basic/score-result.png)

Następnie przekazujemy wyniki do modułu **oceny modelu** , aby generować metryki oceny. Aby sprawdzić wynik, wybierz port wyjściowy **oszacowania modelu** , a następnie wybierz opcję **Wizualizuj**.

![Oceń wynik](./media/ui-sample-regression-predict-automobile-price-basic/evaluate-result.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 2 — Regresja: Porównanie algorytmów do prognozowania cen samochodów](ui-sample-regression-predict-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: Prognozowanie zmian](ui-sample-classification-predict-churn.md)
- [Przykład 6 — Klasyfikacja: Przewidywanie opóźnień lotów](ui-sample-classification-predict-flight-delay.md)