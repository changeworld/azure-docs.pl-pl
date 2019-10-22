---
title: 'Przykład interfejsu wizualizacji #3: regresja do ceny i porównywanie algorytmów'
titleSuffix: Azure Machine Learning
description: W tym artykule przedstawiono sposób tworzenia złożonego potoku uczenia maszynowego bez konieczności pisania jednego wiersza kodu przy użyciu interfejsu wizualizacji. Dowiedz się, jak uczenie i porównywanie wielu modeli regresji w celu przewidywania cen samochodu w oparciu o funkcje techniczne
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: 651644eaae910792aac2144531d09afc4cde7153
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692783"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Przykład 2-Regresja: przewidywanie ceny i porównywanie algorytmów

Dowiedz się, jak utworzyć złożony potok uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu interfejsu wizualizacji. Ten przykład pociąga za siebie i porównuje wiele modeli regresji, aby przewidzieć cenę samochodu w oparciu o jego funkcje techniczne. Udostępnimy uzasadnienie dotyczące opcji wybranych w tym potoku, aby móc rozwiązywać własne problemy z uczeniem maszynowym.

Jeśli dopiero zaczynasz korzystać z uczenia maszynowego, zapoznaj się z [podstawową wersją](how-to-ui-sample-regression-predict-automobile-price-basic.md) tego potoku.

Oto ukończony wykres dla tego potoku:

[![Graph potoku](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/how-to-ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla potoku przykładowego 2:

    ![Otwórz potok](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="pipeline-summary"></a>Podsumowanie potoku

Wykonaj następujące kroki, aby skompilować potok uczenia maszynowego:

1. Pobierz dane.
1. Wstępnie przetwórz dane.
1. Uczenie modelu.
1. Testowanie, obliczanie i porównywanie modeli.

## <a name="get-the-data"></a>Pobieranie danych

W tym przykładzie jest używany zestaw **danych cen samochodów (RAW)** , który pochodzi z REPOZYTORIUM Machine Learning UCI. Ten zestaw danych zawiera 26 kolumn zawierających informacje o urządzeniach przenośnych, takich jak marka, model, Cena, funkcje pojazdu (na przykład liczba cylindrów), MPG i Ocena ryzyka dla ubezpieczenia.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Główne zadania przygotowywania danych obejmują czyszczenie danych, integrację, transformację, redukcję i dyskretyzacji lub podziału. W interfejsie graficznym można znaleźć moduły do wykonywania tych operacji oraz inne zadania przetwarzania wstępnego danych w grupie **Przekształcanie danych** w lewym panelu.

Użyj modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ), aby wykluczyć znormalizowane straty, które mają wiele wartości. Następnie użyj **czystego danych** , aby usunąć wiersze, które mają brakujące wartości. Ułatwia to utworzenie czystego zestawu danych szkoleniowych.

![Przetwarzanie wstępne danych](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Trenowanie modelu

Problemy dotyczące uczenia maszynowego są różne. Typowe zadania uczenia maszynowego obejmują klasyfikację, klastrowanie, regresję i systemy zalecające, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań dotyczących przypadku użycia. Po wybraniu algorytmu należy dostosować jego parametry, aby szkolić dokładniejszy model. Następnie należy oszacować wszystkie modele na podstawie metryk, takich jak dokładność, intelligibility i wydajność.

Ponieważ celem tego potoku jest przewidywanie cen samochodów, a kolumna etykieta (cena) zawiera liczby rzeczywiste, model regresji jest dobrym rozwiązaniem. Biorąc pod uwagę, że liczba funkcji jest stosunkowo mała (mniej niż 100) i te funkcje nie są rozrzedzone, granica decyzji może być nieliniowa.

Aby porównać wydajność różnych algorytmów, używamy dwóch algorytmów nieliniowych, rozbudowanej **regresji drzewa decyzyjnego** oraz **regresji lasów decyzyjnych**w celu tworzenia modeli. Oba algorytmy mają parametry, które można zmienić, ale w tym przykładzie są używane wartości domyślne dla tego potoku.

Użyj modułu **Split Data** , aby losowo podzielić dane wejściowe, tak aby zestaw danych szkoleniowy zawierał 70% danych oryginalnych, a test DataSet zawiera 30% oryginalnych danych.

## <a name="test-evaluate-and-compare-the-models"></a>Testowanie, obliczanie i porównywanie modeli

Używasz dwóch różnych zestawów losowo wybranych danych do uczenia i przetestowania modelu, zgodnie z opisem w poprzedniej sekcji. Podziel zestaw danych i Użyj różnych zestawów DataSets do uczenia i przetestowania modelu, aby zapewnić bardziej obiektywną ocenę modelu.

Po przeszkoleniu modelu należy użyć **modelu oceny** i **oszacować moduły modelu** , aby generować przewidywane wyniki i oszacować modele. **Model oceny** generuje prognozy dla zestawu danych testowych przy użyciu nauczonego modelu. Następnie Przekaż wyniki do **oceny modelu** , aby generować metryki oceny.

W tym potoku są używane dwa wystąpienia **obliczenia modelu** do porównywania dwóch par modeli.

Najpierw należy porównać dwa algorytmy w zestawie danych szkoleniowych.
Następnie należy porównać dwa algorytmy w zestawie danych testowych.

Oto wyniki:

![Porównaj wyniki](media/how-to-ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Wyniki te pokazują, że model zbudowany z **podwyższaną regresją drzewa decyzyjnego** ma dolny, średni błąd oznaczający, że nie jest on modelem opartym na **regresji lasu decyzji**.

Oba algorytmy mają niższy błąd w zestawie danych szkoleniowych niż na niewidocznym zestawie danych testowych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 3 — Klasyfikacja: przewidywanie ryzyka kredytowego](how-to-ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-ui-sample-classification-predict-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-ui-sample-classification-predict-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: Recenzje książek](how-to-ui-sample-text-classification.md)
