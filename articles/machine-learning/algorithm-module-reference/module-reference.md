---
title: Dokumentacja algorytmów i modułów
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej na temat modułów dostępnych w interfejsie Azure Machine Learning wizualizacji
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 76b70c742289ac98e49338b267660a30abba9557
ms.sourcegitcommit: d470d4e295bf29a4acf7836ece2f10dabe8e6db2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2019
ms.locfileid: "70210452"
---
# <a name="algorithm--module-reference-overview"></a>Omówienie algorytmu & modułu

Ta zawartość referencyjna zawiera informacje techniczne na temat każdego z algorytmów uczenia maszynowego i modułów dostępnych w interfejsie wizualnym (wersja zapoznawcza) usługi Azure Machine Learning Service.

Każdy moduł reprezentuje zestaw kodu, który może zostać uruchomiony niezależnie i wykonać zadanie uczenia maszynowego z uwzględnieniem wymaganych danych wejściowych. Moduł może zawierać określony algorytm lub wykonać zadanie, które jest ważne w uczeniu maszynowym, takie jak brakująca wartość zastępcza lub analiza statystyczna.

> [!TIP]
> W dowolnym doświadczeniu w interfejsie wizualizacji możesz uzyskać informacje dotyczące określonego modułu. Wybierz moduł, a następnie wybierz łącze **więcej pomocy** w okienku **szybkie pomoc** .

## <a name="modules"></a>Moduły

Moduły są zorganizowane według funkcjonalności:

| Funkcja | Opis | Moduł |
| --- |--- | ---- |
| Konwersje formatów danych | Konwertuj dane między różnymi formatami plików używanymi w usłudze Machine Learning. | [Konwertuj na CSV](convert-to-csv.md) |
| Dane wejściowe i wyjściowe | Przenieś dane ze źródeł w chmurze do eksperymentu. Zapisuj wyniki lub dane pośrednie w usłudze Azure Storage, w bazie danych SQL lub Hive, w trakcie działania eksperymentu lub korzystaj z magazynu w chmurze, aby wymieniać dane między eksperymentami.  | [Importuj dane](import-data.md)<br/>[Eksportuj dane](export-data.md)<br/>[Wprowadź dane ręcznie](enter-data-manually.md) |
| Przekształcenie danych | Operacje na danych, które są unikatowe dla uczenia maszynowego, takie jak normalizowanie lub pakowania danych, wybór funkcji i zmniejszenie liczby wymiarów.| [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Edytuj metadane](edit-metadata.md) <br/> [Wyczyść brakujące dane](clean-missing-data.md) <br/>  [Mieszanie funkcji](feature-hashing.md) <br/>  [Wyodrębnij funkcje N gramów z tekstu](extract-n-gram-features-from-text.md) <br/> [Dodaj kolumny](add-columns.md) <br/> [Dodaj wiersze](add-rows.md) <br/> [Usuń zduplikowane wiersze](remove-duplicate-rows.md) <br/> [Przetwarzaj wstępnie tekst](preprocess-text.md) <br/> [Dołącz dane](join-data.md) <br/> [Podziel dane](split-data.md) <br/> [Normalizowanie danych](normalize-data.md) <br/> [Partycja i przykład](partition-and-sample.md) |
| Moduły Python i R | Napisz kod i Osadź go w module, aby zintegrować Języki Python i R z Twoim eksperymentem. | [Wykonaj skrypt języka Python](execute-python-script.md)   <br/> [Utwórz model języka Python](create-python-model.md) <br/> [Wykonaj skrypt języka R](execute-r-script.md)
|  | **Algorytmy uczenia maszynowego**: | |
| Klasyfikacja | Przewidywanie klasy.  Wybierz jeden z dwuklasowego algorytmu lub algorytmów wieloklasowych.| [Las decyzyjny wieloklasowej](multiclass-decision-forest.md) <br/> [Wieloklasowe drzewo decyzyjne](multiclass-boosted-decision-tree.md) <br/> [Wieloklasowa regresja logistyczna](multiclass-logistic-regression.md)  <br/> [Multiclass Neural Network](multiclass-neural-network.md)  <br/>  [Regresja logistyczna dla dwóch klas](two-class-logistic-regression.md)  <br/>[Średnia Perceptron średniej klasy](two-class-averaged-perceptron.md) <br/> [Dwuklasowe&nbsp;&nbsp;drzewo decyzyjne&nbsp;](two-class-boosted-decision-tree.md)  <br/> [Las decyzyjny dwóch klas](two-class-decision-forest.md)  <br/> [Sieć neuronowych z dwiema klasami](two-class-neural-network.md)  <br/> [Maszyna&#8209;wektorowa&nbsp;&nbsp;obsługi&nbsp;dwóch klas](two-class-support-vector-machine.md) 
| Klastrowanie | Grupuj dane jednocześnie.| [K-oznacza klastrowanie](k-means-clustering.md)
| Regresji | Przewidywanie wartości. | [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowych](neural-network-regression.md)  <br/> [Regresja lasu decyzyjnego](decision-forest-regression.md)  <br/> [Regresja drzewa&nbsp;&nbsp;decyzyjnej&nbsp;](boosted-decision-tree-regression.md)
|  | **Kompiluj i Szacuj modele**: | |
| Szkolenie   | Uruchom dane za pomocą algorytmu. | [Model uczenia](train-model.md)  <br/> [Model klastra uczenia](train-clustering-model.md)    |
| Ocena modelu | Zmierz dokładność nauczonego modelu. |  [Oceń model](evaluate-model.md)
| Wynik | Zyskaj przewidywania od modelu, który właśnie został przeszkolony. | [Zastosuj transformację](apply-transformation.md)<br/>[Przypisywanie&nbsp;danych&nbsp;doklastrów&nbsp;](assign-data-to-clusters.md) <br/>[Model oceny](score-model.md)

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikatach o błędach i kodach wyjątków](machine-learning-module-error-codes.md) , które mogą wystąpić przy użyciu modułów w interfejsie Visual Azure Machine Learning usługi.
