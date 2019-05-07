---
title: Dokumentacja algorytmów i modułów
titleSuffix: Azure Machine Learning service
description: Dowiedz się więcej o moduły dostępne w interfejsie visual usługi Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8825f1dc3b66a5c4981ba25a90813aec63975b1f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65145130"
---
# <a name="algorithm--module-reference-overview"></a>Omówienie algorytmów i modułów

Odwołanie do kolejnych tematach znajdują się informacje techniczne na wszystkich algorytmów uczenia maszynowego i moduły dostępne w interfejsie visual usługi Azure Machine Learning (wersja zapoznawcza).

Każdy moduł reprezentuje zestaw kodu, która może działać niezależnie i wykonać usługi machine learning zadania, biorąc pod uwagę wymagane dane wejściowe. Moduł może zawierać określonego algorytmu, lub wykonać zadanie, które jest ważny w celu uczenia maszynowego, takich jak brakujące zastąpienia wartości lub analiza statystyczna.

> [!TIP]
> Z każdego doświadczenia w interfejsie visual można uzyskać informacji na temat określonego modułu. Wybierz moduł, a następnie wybierz **uzyskać dodatkową Pomoc** łącze w **ułatwić szybkie** okienka.

## <a name="modules"></a>Moduły

Moduły są uporządkowane według funkcji:

| Funkcjonalność | Opis | Moduł |
| --- |--- | ---- |
| Konwersje formatów danych | Konwertuj dane między różne formaty plików używanych w uczenia maszynowego, | [Konwertuj do formatu CSV](convert-to-csv.md) |
| Dane wejściowe i wyjściowe | Przenoszenie danych ze źródeł w chmurze do eksperymentu. Zapis z wyników lub danych pośrednich do usługi Azure Storage, bazy danych SQL lub Hive, podczas uruchamiania eksperymentu, lub wymiany danych między eksperymentów za pomocą magazynu w chmurze.  | [Importowanie danych](import-data.md)<br/>[Eksportowanie danych](export-data.md)<br/>[Ręcznie wprowadź dane jednostki](enter-data-manually.md) |
| Transformacja danych | Operacje na danych, które są unikatowe dla usługi machine learning, takich jak normalizowanie lub pakowania danych, wybór funkcji i wymiarowości.| [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) <br/> [Edytuj metadane](edit-metadata.md) <br/> [Czyszczenie brakujących danych](clean-missing-data.md) <br/> [Dodawanie kolumn](add-columns.md) <br/> [Dodawanie wierszy](add-rows.md) <br/> [Usuń zduplikowane wiersze](remove-duplicate-rows.md) <br/> [Podział danych](split-data.md) <br/> [Normalizacji danych](normalize-data.md) <br/> [Partycja i próbka](partition-and-sample.md) |
| Moduł Python | Pisanie kodu i osadzenie go w module integracji języka Python z eksperymentu. | [Uruchom skrypt języka Python](execute-python-script.md)   <br/> [Utwórz Model języka Python](create-python-model.md)
|  | **Algorytmy uczenia maszynowego**: | |
| Klasyfikacja | Przewidywanie klasy.  Wybierz z pliku binarnego (dwuklasowych) lub wieloklasowej algorytmów.| [Las decyzyjny kontra](multiclass-decision-forest.md) <br/> [Wieloklasowej regresji logistycznej](multiclass-logistic-regression.md)  <br/> [Multiclass Neural Network](multiclass-neural-network.md)  <br/>  [Regresja logistyczna Two-Class](two-class-logistic-regression.md)  <br/>[Two-Class uśredniane Perceptron](two-class-averaged-perceptron.md) <br/> [Two-Class&nbsp;wzmocnione&nbsp;decyzji&nbsp;drzewa](two-class-boosted-decision-tree.md)  <br/> [Las decyzyjny z dwoma — klasa](two-class-decision-forest.md)  <br/> [Sieć Neuronowa Two-Class](two-class-neural-network.md)  <br/> [Dwa&#8209;klasy&nbsp;pomocy technicznej&nbsp;wektor&nbsp;maszyny](two-class-support-vector-machine.md) 
| Klastrowanie | Grupowanie danych w obrębie.| [K-średnich klastrowania](k-means-clustering.md)
| Regresji | Wartość prognozowana. | [Regresja liniowa](linear-regression.md)  <br/> [Regresja sieci neuronowych](neural-network-regression.md)  <br/> [Las decyzyjny z regresji](decision-forest-regression.md)  <br/> [Wzmocnione&nbsp;decyzji&nbsp;drzewa&nbsp;regresji](boosted-decision-tree-regression.md)
|  | **Tworzenie i oceniać modele**: | |
| Szkolenie   | Uruchom danych za pomocą tego algorytmu. | [Trenowanie modelu](train-model.md)  <br/> [Szkolenie Model klastrowania](train-clustering-model.md)    |
| Ocena modelu | Mierzenia dokładności trenowanego modelu. |  [Ocena modelu](evaluate-model.md)
| Wynik | Uzyskiwać prognozy z przeszkolonego już modelu. | [Zastosuj przekształcenie](apply-transformation.md)<br/>[Przypisz&nbsp;danych&nbsp;do&nbsp;klastrów](assign-data-to-clusters.md) <br/>[Score Model](score-model.md)

## <a name="error-messages"></a>Komunikaty o błędach

Dowiedz się więcej o [komunikaty o błędach i kody wyjątków](machine-learning-module-error-codes.md) mogą wystąpić przy użyciu modułów w interfejsie visual usługi Azure Machine Learning.
