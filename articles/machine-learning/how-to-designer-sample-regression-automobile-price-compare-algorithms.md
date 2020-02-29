---
title: 'Projektant: przykład przewidywania cen samochodów (Zaawansowane)'
titleSuffix: Azure Machine Learning
description: Kompiluj & porównać wiele modeli regresji ML, aby przewidzieć cenę samochodów na podstawie funkcji technicznych w programie Azure Machine Learning Designer.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.date: 12/25/2019
ms.openlocfilehash: a80a1567c84ff3c2eda8ad22391aa862bb7d9d82
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77915830"
---
# <a name="train--compare-multiple-regression-models-to-predict-car-prices-with-azure-machine-learning-designer"></a>Uczenie & porównanie wielu modeli regresji w celu przewidywania cen samochodów przy użyciu projektanta Azure Machine Learning

**Projektant (wersja zapoznawcza) — przykład 2**

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

Dowiedz się, jak utworzyć potok uczenia maszynowego bez pisania pojedynczego wiersza kodu przy użyciu narzędzia Projektant (wersja zapoznawcza). Ten przykład pociąga za siebie i porównuje wiele modeli regresji, aby przewidzieć cenę samochodu w oparciu o jego funkcje techniczne. Udostępnimy uzasadnienie dotyczące opcji wybranych w tym potoku, aby móc rozwiązywać własne problemy z uczeniem maszynowym.

Jeśli dopiero zaczynasz korzystać z uczenia maszynowego, zapoznaj się z [podstawową wersją](how-to-designer-sample-regression-automobile-price-basic.md) tego potoku.

Oto ukończony wykres dla tego potoku:

[![Graf potoku](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png)](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/graph.png#lightbox)

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [aml-ui-prereq](../../includes/aml-ui-prereq.md)]

4. Kliknij pozycję przykład 2, aby ją otworzyć. 

## <a name="pipeline-summary"></a>Podsumowanie potoku

Wykonaj następujące kroki, aby skompilować potok uczenia maszynowego:

1. Pobierz dane.
1. Wstępnie przetwórz dane.
1. Uczenie modelu.
1. Testowanie, obliczanie i porównywanie modeli.

## <a name="get-the-data"></a>Pobieranie danych

W tym przykładzie jest używany zestaw **danych cen samochodów (RAW)** , który pochodzi z REPOZYTORIUM Machine Learning UCI. Ten zestaw danych zawiera 26 kolumn zawierających informacje o urządzeniach przenośnych, takich jak marka, model, Cena, funkcje pojazdu (na przykład liczba cylindrów), MPG i Ocena ryzyka dla ubezpieczenia.

## <a name="pre-process-the-data"></a>Wstępnie przetwórz dane

Główne zadania przygotowywania danych obejmują czyszczenie danych, integrację, transformację, redukcję i dyskretyzacji lub podziału. W projektancie można znaleźć moduły do wykonywania tych operacji oraz inne zadania przetwarzania wstępnego danych w grupie **Przekształcanie danych** w panelu po lewej stronie.

Użyj modułu **SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych** ), aby wykluczyć znormalizowane straty, które mają wiele wartości. Następnie użyj **czystego danych** , aby usunąć wiersze, które mają brakujące wartości. Ułatwia to utworzenie czystego zestawu danych szkoleniowych.

![Przetwarzanie wstępne danych](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/data-processing.png)

## <a name="train-the-model"></a>Uczenie modelu

Problemy dotyczące uczenia maszynowego są różne. Typowe zadania uczenia maszynowego obejmują klasyfikację, klastrowanie, regresję i systemy zalecające, z których każdy może wymagać innego algorytmu. Wybór algorytmu często zależy od wymagań dotyczących przypadku użycia. Po wybraniu algorytmu należy dostosować jego parametry, aby szkolić dokładniejszy model. Następnie należy oszacować wszystkie modele na podstawie metryk, takich jak dokładność, intelligibility i wydajność.

Ponieważ celem tego potoku jest przewidywanie cen samochodów, a kolumna etykieta (cena) zawiera liczby rzeczywiste, model regresji jest dobrym rozwiązaniem.

Aby porównać wydajność różnych algorytmów, używamy dwóch algorytmów nieliniowych, rozbudowanej **regresji drzewa decyzyjnego** oraz **regresji lasów decyzyjnych**w celu tworzenia modeli. Oba algorytmy mają parametry, które można zmienić, ale w tym przykładzie są używane wartości domyślne dla tego potoku.

Użyj modułu **Split Data** , aby losowo podzielić dane wejściowe, tak aby zestaw danych szkoleniowy zawierał 70% danych oryginalnych, a test DataSet zawiera 30% oryginalnych danych.

## <a name="test-evaluate-and-compare-the-models"></a>Testowanie, obliczanie i porównywanie modeli

Używasz dwóch różnych zestawów losowo wybranych danych do uczenia i przetestowania modelu, zgodnie z opisem w poprzedniej sekcji. Podziel zestaw danych i Użyj różnych zestawów DataSets do uczenia i przetestowania modelu, aby zapewnić bardziej obiektywną ocenę modelu.

Po przeszkoleniu modelu należy użyć **modelu oceny** i **oszacować moduły modelu** , aby generować przewidywane wyniki i oszacować modele. **Model oceny** generuje prognozy dla zestawu danych testowych przy użyciu nauczonego modelu. Następnie Przekaż wyniki do **oceny modelu** , aby generować metryki oceny.



Oto wyniki:

![Porównaj wyniki](./media/how-to-designer-sample-regression-automobile-price-compare-algorithms/result.png)

Wyniki te pokazują, że model zbudowany z **podwyższaną regresją drzewa decyzyjnego** ma dolny, średni błąd oznaczający, że nie jest on modelem opartym na **regresji lasu decyzji**.



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z innymi przykładami dostępnymi dla projektanta:

- [Przykład 1-regresja: przewidywanie ceny za samochód](how-to-designer-sample-regression-automobile-price-basic.md)
- [Przykład 3 — Klasyfikacja z wyborem funkcji: prognozowanie dochodu](how-to-designer-sample-classification-predict-income.md)
- [Przykład 4 — Klasyfikacja: przewidywanie ryzyka kredytowego (z uwzględnieniem kosztów)](how-to-designer-sample-classification-credit-risk-cost-sensitive.md)
- [Przykład 5 — Klasyfikacja: przewidywalność zmian](how-to-designer-sample-classification-churn.md)
- [Przykład 6 — Klasyfikacja: przewidywanie opóźnień lotów](how-to-designer-sample-classification-flight-delay.md)
- [Przykład 7 — Klasyfikacja tekstu: zestaw danych witryny Wikipedia SP 500](how-to-designer-sample-text-classification.md)
