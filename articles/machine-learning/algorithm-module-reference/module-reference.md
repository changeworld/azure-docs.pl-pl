---
title: Dokumentacja algorytmów i modułów
description: Informacje o modułach dostępnych w programie Azure Machine Learning Designer (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 02/22/2020
ms.openlocfilehash: ece4b7a85ff5738900b8f999cc2f14ba35ecab0d
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920097"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer-preview"></a>Informacje o algorytmach & module dla projektanta Azure Machine Learning (wersja zapoznawcza)

Ta zawartość referencyjna zawiera informacje techniczne na temat każdego z algorytmów uczenia maszynowego i modułów dostępnych w programie Azure Machine Learning Designer (wersja zapoznawcza).

Każdy moduł reprezentuje zestaw kodu, który może zostać uruchomiony niezależnie i wykonać zadanie uczenia maszynowego z uwzględnieniem wymaganych danych wejściowych. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brakująca wartość zastępcza lub analiza statystyczna.

Aby uzyskać pomoc dotyczącą wybierania algorytmów, zobacz 
* [Jak wybierać algorytmy](../how-to-select-algorithms.md)
* [Arkusz Ściągawka algorytmu Azure Machine Learning](../algorithm-cheat-sheet.md)

> [!TIP]
> W każdym potoku projektanta można uzyskać informacje dotyczące określonego modułu. Wybierz moduł, a następnie wybierz łącze **więcej pomocy** w okienku **szybkie pomoc** .

## <a name="data-preparation-modules"></a>Moduły przygotowywania danych


| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Wprowadzanie i wyprowadzanie danych | Przenieś dane ze źródeł w chmurze do potoku. Zapisuj wyniki lub dane pośrednie w usłudze Azure Storage, w bazie danych SQL lub Hive, podczas uruchamiania potoku lub korzystaj z magazynu w chmurze, aby wymieniać dane między potokami.  | [Wprowadź dane ręcznie](enter-data-manually.md) <br/> [Eksportuj dane](export-data.md) <br/> [Importuj dane](import-data.md) |
| Transformacja danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizowanie lub pakowania danych, Zmniejszanie liczby wymiarów i konwertowanie danych między różnymi formatami plików.| [Dodaj kolumny](add-columns.md) <br/> [Dodaj wiersze](add-rows.md) <br/> [Zastosuj operacje matematyczne](apply-math-operation.md) <br/> [Zastosuj transformację SQL](apply-sql-transformation.md) <br/> [Wyczyść brakujące dane](clean-missing-data.md) <br/> [Przytnij wartości](clip-values.md) <br/> [Konwertuj na CSV](convert-to-csv.md) <br/> [Konwertuj na zestaw danych](convert-to-dataset.md) <br/> [Konwertuj na wartości wskaźnika](convert-to-indicator-values.md) <br/> [Edytuj metadane](edit-metadata.md) <br/> [Dołącz dane](join-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Partycja i przykład](partition-and-sample.md)  <br/> [Usuń zduplikowane wiersze](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Wybierz kolumny Przekształć](select-columns-transform.md) <br/> [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Podziel dane](split-data.md) |
| Wybór funkcji | Wybierz podzestaw odpowiednich, przydatnych funkcji do użycia podczas tworzenia modelu analitycznego. | [Wybór funkcji oparty na filtrze](filter-based-feature-selection.md) <br/> [Ważność funkcji permutacji](permutation-feature-importance.md) |
| Funkcje statystyczne | Zapewniają szeroką gamę metod statystycznych związanych z nauką danych. | [Podsumowywanie danych](summarize-data.md)|

## <a name="machine-learning-algorithms"></a>Algorytmy uczenia maszynowego

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Regresja | Przewidywanie wartości. | [Regresja drzewa decyzyjnej](boosted-decision-tree-regression.md) <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md) <br/> [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowych](neural-network-regression.md)  <br/> |
| Obsługa klastrów | Grupuj dane jednocześnie.| [K-oznacza klastrowanie](k-means-clustering.md)
| Klasyfikacja | Przewidywanie klasy.  Wybierz jeden z dwuklasowego algorytmu lub algorytmów wieloklasowych.| [Wieloklasowe drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Las decyzyjny wieloklasowej](multiclass-decision-forest.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Wieloklasowa sieć neuronowych](multiclass-neural-network.md) <br/> [Jeden i wszystkie wieloklasowe](one-vs-all-multiclass.md) <br/> [Średnia Perceptron średniej klasy](two-class-averaged-perceptron.md) <br/>  [Dwuklasowe drzewo decyzyjne](two-class-boosted-decision-tree.md)  <br/> [Las decyzyjny dwóch klas](two-class-decision-forest.md) <br/>  [Regresja logistyczna dla dwóch klas](two-class-logistic-regression.md) <br/> [Sieć neuronowych z dwiema klasami](two-class-neural-network.md) <br/> [Maszyna wektorowa obsługi dwóch klas](two-class-support-vector-machine.md) | 

## <a name="modules-for-building-and-evaluating-models"></a>Moduły do kompilowania i oceniania modeli

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
| Trenowanie modelu | Uruchom dane za pomocą algorytmu. |  [Model klastra uczenia](train-clustering-model.md) <br/> [Model uczenia](train-model.md)  <br/> [Dostrajanie parametrów modelu](tune-model-hyperparameters.md) |
| Ocenianie i ocenianie modelu | Zmierz dokładność nauczonego modelu. | [Zastosuj transformację](apply-transformation.md) <br/> [Przypisywanie danych do klastrów](assign-data-to-clusters.md) <br/> [Model krzyżowego sprawdzania poprawności](cross-validate-model.md) <br/> [Oceń model](evaluate-model.md) <br/> [Model oceny](score-model.md) |
| Język Python | Napisz kod i Osadź go w module, aby zintegrować Język Python z potokiem. | [Utwórz model języka Python](create-python-model.md) <br/> [Wykonaj skrypt języka Python](execute-python-script.md) |
| Język R | Napisz kod i Osadź go w module, aby zintegrować język R z potokiem. | [Wykonaj skrypt języka R](execute-r-script.md) |
| Analiza tekstu | Udostępniaj wyspecjalizowane narzędzia obliczeniowe służące do pracy z tekstem ze strukturą i bez struktury. | [Wyodrębnij funkcje N gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Mieszanie funkcji](feature-hashing.md) <br/> [Przetwarzaj wstępnie tekst](preprocess-text.md) |
| Zalecenie | Kompiluj modele rekomendacji. | [Oceń zalecaną](evaluate-recommender.md) <br/> [Polecający wskaźnik SVD](score-svd-recommender.md) <br/> [Polecający pociąg](train-SVD-recommender.md) |
| Wykrywanie anomalii | Tworzenie modeli wykrywania anomalii. | [Wykrywanie anomalii oparte na UPW](pca-based-anomaly-detection.md) <br/> [Wyszkol model wykrywania anomalii](train-anomaly-detection-model.md) |

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków](designer-error-codes.md) , które mogą wystąpić przy użyciu modułów w programie Azure Machine Learning Designer.

## <a name="next-steps"></a>Następne kroki

* [Samouczek: Tworzenie modelu w projektancie w celu przewidywania cen Autokorekty](../tutorial-designer-automobile-price-train-score.md)
