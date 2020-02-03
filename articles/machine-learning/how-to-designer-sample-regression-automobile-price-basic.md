---
title: 'Projektant: przewidywanie cen samochodów (podstawowy)'
titleSuffix: Azure Machine Learning
description: Utwórz model regresji ML, aby przewidzieć cenę samochodów, bez konieczności pisania jednego wiersza kodu za pomocą narzędzia Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: 6f4fe941cc44211f9f5d5e77b11043257b43a8ea
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76963301"
---
# <a name="use-regression-to-predict-car-prices-with-azure-machine-learning-designer"></a>Korzystanie z regresji do przewidywania cen samochodów za pomocą narzędzia Azure Machine Learning Designer

**Projektant (wersja zapoznawcza) — przykład 1**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dowiedz się, jak utworzyć model regresji uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu narzędzia Projektant (wersja zapoznawcza).

Ten potok pociąga za **regresor liniowy** , aby przewidzieć cenę samochodu w oparciu o funkcje techniczne, takie jak marka, model, na mocy i rozmiar. Ponieważ próbujesz odpowiedzieć na pytanie "jak dużo?" jest to tzw. problem z regresją. Można jednak zastosować te same podstawowe kroki w tym przykładzie, aby rozwiązać dowolny typ problemu uczenia maszynowego, niezależnie od tego, czy jest to regresja, klasyfikacja, klastrowanie i tak dalej.

Podstawowe kroki związane z modelem uczenia maszynowego są następujące:

1. Pobieranie danych
1. Wstępnie przetwórz dane
1. Trenowanie modelu
1. Ocenianie modelu

Oto końcowy, ukończony wykres potoku. Ten artykuł zawiera uzasadnienie dla wszystkich modułów, dzięki czemu można podejmować podobne decyzje.

![Wykres potoku](./media/how-to-designer-sample-regression-automobile-price-basic/overall-graph.png)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknij przykład 1, aby otworzyć. it


## <a name="get-the-data"></a>Pobieranie danych

W tym przykładzie jest używany zestaw **danych cen samochodów (RAW)** , który pochodzi z REPOZYTORIUM Machine Learning UCI. Zestaw danych zawiera 26 kolumn zawierających informacje o urządzeniach przenośnych, takich jak marka, model, Cena, funkcje pojazdu (na przykład liczba cylindrów), MPG i Ocena ryzyka dla ubezpieczenia. Celem tego przykładu jest przewidywanie ceny samochodu.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Główne zadania przygotowywania danych obejmują czyszczenie danych, integrację, transformację, redukcję i dyskretyzacji lub podziału. W projektancie można znaleźć moduły do wykonywania tych operacji oraz inne zadania przetwarzania wstępnego danych w grupie **Przekształcanie danych** w panelu po lewej stronie.

Użyj modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ), aby wykluczyć znormalizowane straty, które mają wiele wartości. Następnie użyj **czyste brakujące dane** , aby usunąć wiersze, które mają brakujące wartości. Ułatwia to utworzenie czystego zestawu danych szkoleniowych.

![Przetwarzanie wstępne danych](./media/how-to-designer-sample-regression-automobile-price-basic/data-processing.png)

## <a name="train-the-model"></a>Trenowanie modelu

Problemy dotyczące uczenia maszynowego są różne. Typowe zadania uczenia maszynowego obejmują klasyfikację, klastrowanie, regresję i systemy zalecające, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań dotyczących przypadku użycia. Po wybraniu algorytmu należy dostosować jego parametry, aby szkolić dokładniejszy model. Następnie należy oszacować wszystkie modele na podstawie metryk, takich jak dokładność, intelligibility i wydajność.

Ponieważ celem tego przykładu jest przewidywanie cen samochodów, a kolumna etykiety (cena) zawiera liczby rzeczywiste, model regresji jest dobrym rozwiązaniem. Biorąc pod uwagę, że liczba funkcji jest stosunkowo mała (mniej niż 100) i te funkcje nie są rozrzedzone, granica decyzji może być nieliniowa. Dlatego używamy **regresji lasów decyzyjnych** dla tego potoku.

Użyj modułu **Split Data** , aby losowo podzielić dane wejściowe, tak aby zestaw danych szkoleniowy zawierał 70% danych oryginalnych, a test DataSet zawiera 30% oryginalnych danych.

## <a name="test-evaluate-and-compare"></a>Testowanie, obliczanie i porównywanie

Podziel zestaw danych i Użyj różnych zestawów DataSets do uczenia i przetestowania modelu, aby zapewnić bardziej obiektywną ocenę modelu.

Po przeszkoleniu modelu można użyć **modelu oceny** i **oszacować moduły modelu** , aby wygenerować przewidywane wyniki i oszacować modele.

**Model oceny** generuje prognozy dla zestawu danych testowych przy użyciu nauczonego modelu. Aby sprawdzić wynik, wybierz port wyjściowy **modelu oceny** , a następnie wybierz opcję **Wizualizuj**.

![Wynik oceny](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-score-1225.png)

Przekaż wyniki do modułu **oceny modelu** , aby wygenerować metryki oceny. Aby sprawdzić wynik, wybierz port wyjściowy **oszacowania modelu** , a następnie wybierz opcję **Wizualizuj**.

![Oceń wynik](./media/how-to-designer-sample-regression-automobile-price-basic/sample1-evaluate-1225.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla projektanta:

- [Przykład 2-Regresja: porównanie algorytmów do prognozowania cen samochodów](how-to-designer-sample-regression-automobile-price-compare-algorithms.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)