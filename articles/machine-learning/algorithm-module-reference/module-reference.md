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
ms.date: 11/11/2019
ms.openlocfilehash: 938286f0dafdeb11473bef1b88f876d7918e76ca
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012756"
---
# <a name="algorithm--module-reference"></a>Dokumentacja algorytmów i modułów

Ta zawartość referencyjna zawiera informacje techniczne na temat każdego z algorytmów uczenia maszynowego i modułów dostępnych w programie Azure Machine Learning Designer (wersja zapoznawcza).

Każdy moduł reprezentuje zestaw kodu, który może zostać uruchomiony niezależnie i wykonać zadanie uczenia maszynowego z uwzględnieniem wymaganych danych wejściowych. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brakująca wartość zastępcza lub analiza statystyczna.

> [!TIP]
> W każdym potoku projektanta można uzyskać informacje dotyczące określonego modułu. Wybierz moduł, a następnie wybierz łącze **więcej pomocy** w okienku **szybkie pomoc** .

## <a name="modules"></a>Moduły

Moduły są zorganizowane według funkcjonalności:

| Funkcjonalność | Opis | Moduł |
| --- |--- | ---- |
| Dane wejściowe i wyjściowe | Przenieś dane ze źródeł w chmurze do potoku. Zapisuj wyniki lub dane pośrednie w usłudze Azure Storage, w bazie danych SQL lub Hive, podczas uruchamiania potoku lub korzystaj z magazynu w chmurze, aby wymieniać dane między potokami.  | [Importuj dane](import-data.md) <br/> [Wprowadź dane ręcznie](enter-data-manually.md) <br/>[Eksportuj dane](export-data.md) |
| Transformacja danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizowanie lub pakowania danych, Zmniejszanie liczby wymiarów i konwertowanie danych między różnymi formatami plików.| [Dodaj kolumny](add-columns.md) <br/> [Dodaj wiersze](add-rows.md) <br/> [Wyczyść brakujące dane](clean-missing-data.md) <br/> [Konwertuj na CSV](convert-to-csv.md) <br/> [Konwertuj na zestaw danych](convert-to-dataset.md) <br/> [Edytuj metadane](edit-metadata.md) <br/> [Dołącz dane](join-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Usuń zduplikowane wiersze](remove-duplicate-rows.md) <br/> [Wybierz kolumny Przekształć](select-columns-transform.md) <br/> [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) |
| Próbkowanie | Podziel dane na jeden lub więcej podzestawów podzbiorów, aby uczenie i testowanie modeli uczenia maszynowego.  | [Model krzyżowego sprawdzania poprawności](cross-validate-model.md) <br/> [Partycja i przykład](partition-and-sample.md) <br/> [SMOTE](smote.md) <br/> [Podziel dane](split-data.md) |
| Wybór funkcji | Wybierz podzestaw odpowiednich, przydatnych funkcji do użycia podczas tworzenia modelu analitycznego. | [Wybór funkcji oparty na filtrze](filter-based-feature-selection.md) <br/> [Ważność funkcji permutacji](permutation-feature-importance.md) |
| Python i R | Napisz kod i Osadź go w module, aby zintegrować Języki Python i R z potokiem. | [Utwórz model języka Python](create-python-model.md) <br/> [Wykonaj skrypt języka Python](execute-python-script.md)   <br/>  [Wykonaj skrypt języka R](execute-r-script.md)
| Analiza tekstu | Udostępniaj wyspecjalizowane narzędzia obliczeniowe służące do pracy z tekstem ze strukturą i bez struktury. | [Wyodrębnij funkcje N gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Mieszanie funkcji](feature-hashing.md) <br/> [Przetwarzaj wstępnie tekst](preprocess-text.md) |
|  | **Algorytmy uczenia maszynowego**: | |
| Klasyfikacja | Przewidywanie klasy.  Wybierz jeden z dwuklasowego algorytmu lub algorytmów wieloklasowych.| [Las decyzyjny wieloklasowej](multiclass-decision-forest.md) <br/> [Wieloklasowe drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Multiclass Neural Network](multiclass-neural-network.md) <br/> [Jeden i wszystkie wieloklasowe](one-vs-all-multiclass.md) <br/>  [Regresja logistyczna dla dwóch klas](two-class-logistic-regression.md)  <br/>[Średnia Perceptron średniej klasy](two-class-averaged-perceptron.md) <br/> [Dwuklasowe drzewo decyzyjne](two-class-boosted-decision-tree.md)  <br/> [Las decyzyjny dwóch klas](two-class-decision-forest.md)  <br/> [Sieć neuronowych z dwiema klasami](two-class-neural-network.md) <br/> [Maszyna wektorowa obsługi dwóch klas](two-class-support-vector-machine.md) | 
| Usługę | Grupuj dane jednocześnie.| [K-oznacza klastrowanie](k-means-clustering.md)
| Regresji | Przewidywanie wartości. | [Regresja drzewa decyzyjnej](boosted-decision-tree-regression.md) <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md) <br/> [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowych](neural-network-regression.md)  <br/> |
| Polecania | Kompiluj modele rekomendacji. | [Oceń zalecaną](evaluate-recommender.md) <br/> [Polecający wskaźnik SVD](score-svd-recommender.md) <br/> [Polecający pociąg](train-SVD-recommender.md) |
|  | **Kompiluj i Szacuj modele**: | |
| Uczenie   | Uruchom dane za pomocą algorytmu. | [Model uczenia](train-model.md)  <br/> [Model klastra uczenia](train-clustering-model.md) <br/>  [Dostrajanie parametrów modelu](tune-model-hyperparameters.md) |
| Ocena modelu | Zmierz dokładność nauczonego modelu. |  [Oceń model](evaluate-model.md) |
| Wynik | Zyskaj przewidywania od modelu, który właśnie został przeszkolony. | [Zastosuj transformację](apply-transformation.md)<br/>[Przypisywanie danych do klastrów](assign-data-to-clusters.md) <br/>[Model oceny](score-model.md) |
| Funkcje statystyczne | Zapewniają szeroką gamę metod statystycznych związanych z nauką danych. | [Zastosuj operacje matematyczne](apply-math-operation.md) <br/> [Podsumowywanie danych](summarize-data.md)|

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków](machine-learning-module-error-codes.md) , które mogą wystąpić przy użyciu modułów w programie Azure Machine Learning Designer.
