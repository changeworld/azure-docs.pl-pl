---
title: Dokumentacja algorytmów i modułów
description: Informacje o modułach dostępnych w programie Azure Machine Learning Designer
titleSuffix: Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: peterclu
ms.author: peterlu
ms.date: 12/17/2019
ms.openlocfilehash: c265c2386b31fd02a5aa635c343be5d3cb227722
ms.sourcegitcommit: a9b1f7d5111cb07e3462973eb607ff1e512bc407
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76312181"
---
# <a name="algorithm--module-reference-for-azure-machine-learning-designer"></a>Informacje o algorytmach & module dla projektanta Azure Machine Learning

Ta zawartość referencyjna zawiera informacje techniczne na temat każdego z algorytmów uczenia maszynowego i modułów dostępnych w programie Azure Machine Learning Designer.

Każdy moduł reprezentuje zestaw kodu, który może zostać uruchomiony niezależnie i wykonać zadanie uczenia maszynowego z uwzględnieniem wymaganych danych wejściowych. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brakująca wartość zastępcza lub analiza statystyczna.

> [!TIP]
> W każdym potoku projektanta można uzyskać informacje dotyczące określonego modułu. Wybierz moduł, a następnie wybierz łącze **więcej pomocy** w okienku **szybkie pomoc** .

## <a name="modules"></a>Moduły

Moduły są zorganizowane według funkcjonalności:

| Funkcjonalność | Opis | Moduł |
| --- |--- | --- |
|  | **Przygotowanie danych**: | |
| Wprowadzanie i wyprowadzanie danych | Przenieś dane ze źródeł w chmurze do potoku. Zapisuj wyniki lub dane pośrednie w usłudze Azure Storage, w bazie danych SQL lub Hive, podczas uruchamiania potoku lub korzystaj z magazynu w chmurze, aby wymieniać dane między potokami.  | [Wprowadź dane ręcznie](enter-data-manually.md) <br/> [Eksportuj dane](export-data.md) <br/> [Importuj dane](import-data.md) |
| Transformacja danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizowanie lub pakowania danych, Zmniejszanie liczby wymiarów i konwertowanie danych między różnymi formatami plików.| [Dodaj kolumny](add-columns.md) <br/> [Dodawanie wierszy](add-rows.md) <br/> [Zastosuj operacje matematyczne](apply-math-operation.md) <br/> [Zastosuj transformację SQL](apply-sql-transformation.md) <br/> [Wyczyść brakujące dane](clean-missing-data.md) <br/> [Przytnij wartości](clip-values.md) <br/> [Konwertuj na CSV](convert-to-csv.md) <br/> [Konwertuj na zestaw danych](convert-to-dataset.md) <br/> [Edytuj metadane](edit-metadata.md) <br/> [Dołącz dane](join-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Partycja i przykład](partition-and-sample.md)  <br/> [Usuń zduplikowane wiersze](remove-duplicate-rows.md) <br/> [SMOTE](smote.md) <br/> [Wybierz kolumny Przekształć](select-columns-transform.md) <br/> [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Podziel dane](split-data.md) |
| Wybieranie funkcji | Wybierz podzestaw odpowiednich, przydatnych funkcji do użycia podczas tworzenia modelu analitycznego. | [Wybór funkcji oparty na filtrze](filter-based-feature-selection.md) <br/> [Ważność funkcji permutacji](permutation-feature-importance.md) |
| Funkcje statystyczne | Zapewniają szeroką gamę metod statystycznych związanych z nauką danych. | [Podsumowywanie danych](summarize-data.md)|
|  | **Algorytmy uczenia maszynowego**: | |
| Regresja | Przewidywanie wartości. | [Regresja drzewa decyzyjnej](boosted-decision-tree-regression.md) <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md) <br/> [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowych](neural-network-regression.md)  <br/> |
| Klastrowanie | Grupuj dane jednocześnie.| [K-oznacza klastrowanie](k-means-clustering.md)
| Klasyfikacja | Przewidywanie klasy.  Wybierz jeden z dwuklasowego algorytmu lub algorytmów wieloklasowych.| [Wieloklasowe drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Las decyzyjny wieloklasowej](multiclass-decision-forest.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Multiclass Neural Network](multiclass-neural-network.md) <br/> [Jeden i wszystkie wieloklasowe](one-vs-all-multiclass.md) <br/> [Średnia Perceptron średniej klasy](two-class-averaged-perceptron.md) <br/>  [Dwuklasowe drzewo decyzyjne](two-class-boosted-decision-tree.md)  <br/> [Las decyzyjny dwóch klas](two-class-decision-forest.md) <br/>  [Regresja logistyczna dla dwóch klas](two-class-logistic-regression.md) <br/> [Sieć neuronowych z dwiema klasami](two-class-neural-network.md) <br/> [Maszyna wektorowa obsługi dwóch klas](two-class-support-vector-machine.md) | 
|  | **Kompiluj i Szacuj modele**: | |
| Trenowanie modelu | Uruchom dane za pomocą algorytmu. |  [Model klastra uczenia](train-clustering-model.md) <br/> [Model uczenia](train-model.md)  <br/> [Dostrajanie parametrów modelu](tune-model-hyperparameters.md) |
| Ocenianie i ocenianie modelu | Zmierz dokładność nauczonego modelu. | [Zastosuj transformację](apply-transformation.md) <br/> [Przypisywanie danych do klastrów](assign-data-to-clusters.md) <br/> [Model krzyżowego sprawdzania poprawności](cross-validate-model.md) <br/> [Oceń model](evaluate-model.md) <br/> [Model oceny](score-model.md) |
| Język Python | Napisz kod i Osadź go w module, aby zintegrować Język Python z potokiem. | [Utwórz model języka Python](create-python-model.md) <br/> [Wykonaj skrypt języka Python](execute-python-script.md) |
| Język R | Napisz kod i Osadź go w module, aby zintegrować język R z potokiem. | [Wykonaj skrypt języka R](execute-r-script.md) |
| Analiza tekstu | Udostępniaj wyspecjalizowane narzędzia obliczeniowe służące do pracy z tekstem ze strukturą i bez struktury. | [Wyodrębnij funkcje N gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Mieszanie funkcji](feature-hashing.md) <br/> [Przetwarzaj wstępnie tekst](preprocess-text.md) |
| Zalecenie | Kompiluj modele rekomendacji. | [Oceń zalecaną](evaluate-recommender.md) <br/> [Polecający wskaźnik SVD](score-svd-recommender.md) <br/> [Polecający pociąg](train-SVD-recommender.md) |

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków](designer-error-codes.md) , które mogą wystąpić przy użyciu modułów w programie Azure Machine Learning Designer.
