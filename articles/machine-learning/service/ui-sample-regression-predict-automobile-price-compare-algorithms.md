---
title: 'Przykład interfejsu wizualizacji #3: regresja do ceny i porównywanie algorytmów'
titleSuffix: Azure Machine Learning service
description: W tym artykule przedstawiono sposób tworzenia złożonego eksperymentu uczenia maszynowego bez konieczności pisania jednego wiersza kodu przy użyciu interfejsu wizualizacji. Dowiedz się, jak uczenie i porównywanie wielu modeli regresji w celu przewidywania cen samochodu w oparciu o funkcje techniczne
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: xiaoharper
ms.author: zhanxia
ms.reviewer: sgilley
ms.date: 05/10/2019
ms.openlocfilehash: d05a601196ec4a5349a0acb4763098d9716c17f5
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989999"
---
# <a name="sample-2---regression-predict-price-and-compare-algorithms"></a>Przykład 2 — Regresja: Przewidywanie cen i porównywanie algorytmów

Dowiedz się, jak utworzyć złożony eksperyment uczenia maszynowego bez pisania jednego wiersza kodu przy użyciu interfejsu wizualizacji. Ten przykład pociąga za siebie i porównuje wiele modeli regresji, aby przewidzieć cenę samochodu w oparciu o jego funkcje techniczne. Udostępnimy uzasadnienie dotyczące opcji wybranych w tym doświadczeniu, aby móc rozwiązywać własne problemy z uczeniem maszynowym.

Jeśli dopiero zaczynasz korzystać z uczenia maszynowego, możesz zapoznać się z [podstawową wersją](ui-sample-regression-predict-automobile-price-basic.md) tego eksperymentu, aby zobaczyć podstawowy eksperyment regresji.

Oto ukończony wykres dla tego eksperymentu:

[![Wykres eksperymentu](media/ui-sample-regression-predict-automobile-price-compare-algorithms/graph.png)](media/ui-sample-classification-predict-credit-risk-cost-sensitive/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../../includes/aml-ui-prereq.md)]

4. Wybierz przycisk **Otwórz** dla eksperymentu przykładowego 2:

    ![Otwieranie eksperymentu](media/ui-sample-regression-predict-automobile-price-compare-algorithms/open-sample2.png)

## <a name="experiment-summary"></a>Podsumowanie eksperymentu

Te kroki są używane do kompilowania eksperymentu:

1. Pobierz dane.
1. Wstępnie przetwórz dane.
1. Uczenie modelu.
1. Testowanie, obliczanie i porównywanie modeli.

## <a name="get-the-data"></a>Pobieranie danych

W tym doświadczeniu używany jest zestaw **danych cen samochodów (RAW)** , który pochodzi z REPOZYTORIUM Machine Learning UCI. Ten zestaw danych zawiera 26 kolumn zawierających informacje o urządzeniach przenośnych, takich jak marka, model, Cena, funkcje pojazdu (na przykład liczba cylindrów), MPG i Ocena ryzyka dla ubezpieczenia. Celem tego eksperymentu jest przewidywanie ceny samochodu.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Główne zadania przygotowywania danych obejmują czyszczenie danych, integrację, transformację, redukcję i dyskretyzacji lub podziału. W interfejsie graficznym można znaleźć moduły do wykonywania tych operacji oraz inne zadania przetwarzania wstępnego danych w grupie **Przekształcanie danych** w lewym panelu.

W tym doświadczeniu używamy modułu **SELECT Columns in DataSet** do wykluczania znormalizowanych strat, które mają wiele wartości. Następnie użyj **czystego danych** , aby usunąć wiersze, które mają brakujące wartości. Ułatwia to utworzenie czystego zestawu danych szkoleniowych.

![Przetwarzanie wstępne danych](media/ui-sample-regression-predict-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Uczenie modelu

Problemy dotyczące uczenia maszynowego są różne. Typowe zadania uczenia maszynowego obejmują klasyfikację, klastrowanie, regresję i systemy zalecające, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań dotyczących przypadku użycia. Po wybraniu algorytmu należy dostosować jego parametry, aby szkolić dokładniejszy model. Następnie należy oszacować wszystkie modele na podstawie metryk, takich jak dokładność, intelligibility i wydajność.

Ponieważ celem tego eksperymentu jest przewidywanie cen samochodów, a kolumna etykieta (cena) zawiera liczby rzeczywiste, model regresji jest dobrym rozwiązaniem. Biorąc pod uwagę, że liczba funkcji jest stosunkowo mała (mniej niż 100) i te funkcje nie są rozrzedzone, granica decyzji może być nieliniowa.

Aby porównać wydajność różnych algorytmów, używamy dwóch algorytmów nieliniowych, rozbudowanej **regresji drzewa decyzyjnego** oraz **regresji lasów decyzyjnych**w celu tworzenia modeli. Oba algorytmy mają parametry, które można zmienić, ale dla tego eksperymentu są używane wartości domyślne.

Używamy modułu **Split Data** , aby losowo podzielić dane wejściowe w taki sposób, że zestaw danych szkoleniowych zawiera 70% danych oryginalnych, a test DataSet zawiera 30% oryginalnych danych.

## <a name="test-evaluate-and-compare-the-models"></a>Testowanie, obliczanie i porównywanie modeli

Korzystamy z dwóch różnych zestawów losowo wybranych danych do uczenia i przetestowania modelu, zgodnie z opisem w poprzedniej sekcji. W celu przeprowadzenia oceny modelu zestawu danych i przetestowania modelu należy użyć różnych zestawów

Po przeszkoleniu modelu używamy **modelu oceny** i oceny modułów **modelu** , aby generować przewidywane wyniki i szacować modele. **Model oceny** generuje prognozy dla zestawu danych testowych przy użyciu nauczonego modelu. Następnie przekazujemy wyniki do **oceny modelu** , aby generować metryki oceny.

W tym doświadczeniu stosujemy dwa wystąpienia **obliczenia modelu** w celu porównania dwóch par modeli.

Najpierw należy porównać dwa algorytmy w zestawie danych szkoleniowych.
Po drugie porównamy dwa algorytmy w zestawie danych testowych.
Oto wyniki:

![Porównaj wyniki](media/ui-sample-regression-predict-automobile-price-compare-algorithms/result.png)

Wyniki te pokazują, że model zbudowany z **podwyższaną regresją drzewa decyzyjnego** ma dolny, średni błąd oznaczający, że nie jest on modelem opartym na regresji **lasu decyzji**.

Oba algorytmy mają niższy błąd w zestawie danych szkoleniowych niż na niewidocznym zestawie danych testowych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla interfejsu wizualizacji:

- [Przykład 1 — regresja: Przewidywanie ceny samochodów](ui-sample-regression-predict-automobile-price-basic.md)
- [Przykład 3 — Klasyfikacja: Przewidywanie ryzyka kredytowego](ui-sample-classification-predict-credit-risk-basic.md)
- [Przykład 4 — Klasyfikacja: Przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](ui-sample-classification-predict-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: Prognozowanie zmian](ui-sample-classification-predict-churn.md)
- [Przykład 6 — Klasyfikacja: Przewidywanie opóźnień lotów](ui-sample-classification-predict-flight-delay.md)
