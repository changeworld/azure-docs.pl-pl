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
ms.openlocfilehash: 6602eb4bacdc3b6382c1b6873a465cdfc0632693
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029343"
---
# <a name="algorithm--module-reference-overview"></a>Omówienie algorytmów i modułów

Odwołanie do kolejnych tematach znajdują się informacje techniczne na wszystkich algorytmów uczenia maszynowego i moduły dostępne w interfejsie visual usługi Azure Machine Learning (wersja zapoznawcza). 

Każdy moduł reprezentuje zestaw kodu, która może działać niezależnie i wykonać usługi machine learning zadania, biorąc pod uwagę wymagane dane wejściowe. Moduł może zawierać określonego algorytmu, lub wykonać zadanie, które jest ważny w celu uczenia maszynowego, takich jak brakujące zastąpienia wartości lub analiza statystyczna. 

> [!TIP]
> Z każdego doświadczenia w interfejsie visual można uzyskać informacji na temat określonego modułu. Wybierz moduł, a następnie wybierz **uzyskać dodatkową Pomoc** łącze w **ułatwić szybkie** okienka.

Moduły są uporządkowane według funkcji:

**Konwersje formatu danych**

  + [Konwertuj do formatu CSV ](convert-to-csv.md)

**Dane wejściowe i wyjściowe modułów** wykonywać pracę przenoszenie danych ze źródeł w chmurze do eksperymentu. Zapis z wyników lub danych pośrednich do usługi Azure Storage, bazy danych SQL lub Hive, podczas uruchamiania eksperymentu lub magazynu w chmurze umożliwia wymianę danych między eksperymentów.  

  + [Importowanie danych](import-data.md)

  + [Eksportowanie danych](export-data.md)

  + [Ręcznie wprowadź dane jednostki](enter-data-manually.md)


**Moduły przekształcania danych** obsługują operacje na danych, które są unikatowe dla usługi machine learning, takich jak normalizowanie lub pakowania danych, wybór funkcji i wymiarowości.

  + [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md)

  + [Edytuj metadane](edit-metadata.md)

  + [Czyszczenie brakujących danych](clean-missing-data.md)

  + [Dodawanie kolumn](add-columns.md)

  + [Dodawanie wierszy](add-rows.md)

  + [Usuń zduplikowane wiersze](remove-duplicate-rows.md)

  + [Podział danych](split-data.md)

  + [Normalizacji danych](normalize-data.md)

  + [Partycja i próbka](partition-and-sample.md)


**Algorytmy uczenia maszynowego** takich jak klastrowanie, pomocy technicznej wektor maszyny lub sieci neuronowych, które są dostępne w ramach indywidualne moduły, które można dostosować za pomocą odpowiednich parametrów zadania uczenia maszynowego.  
  + [Score Model](score-model.md)

  + [Przypisz dane do klastrów ](assign-data-to-clusters.md)

  + [Trenowanie modelu](train-model.md)

  + [Szkolenie Model klastrowania](train-clustering-model.md)

  + [Ocena modelu](evaluate-model.md)

  + [Zastosuj przekształcenie](apply-transformation.md)

  + [Regresja liniowa](linear-regression.md)

  + [Regresja sieci neuronowych](neural-network-regression.md)

  + [Las decyzyjny z regresji](decision-forest-regression.md)

  + [Regresja wzmocnionego drzewa decyzyjnego](boosted-decision-tree-regression.md)

  + [Two-Class wzmocnione drzewo decyzyjnego](two-class-boosted-decision-tree.md)

  + [Regresja logistyczna Two-Class](two-class-logistic-regression.md)

  + [Wieloklasowej regresji logistycznej](multiclass-logistic-regression.md)

  + [Multiclass Neural Network](multiclass-neural-network.md)

  + [Las decyzyjny kontra](multiclass-decision-forest.md)

  + [Two-Class uśredniane Perceptron](two-class-averaged-perceptron.md)

  + [Las decyzyjny z dwoma — klasa](two-class-decision-forest.md)

  + [Sieć Neuronowa Two-Class](two-class-neural-network.md)

  + [Obsługa Two-Class wektor maszyny](two-class-support-vector-machine.md)
  
  + [K-średnich klastrowania](k-means-clustering.md)


**Moduł Python** można łatwo uruchomić funkcję niestandardową. Pisanie kodu i osadzenie go w module, integracji języka Python z usługą eksperymentu.
  + [Uruchom skrypt języka Python](execute-python-script.md)

  + [Utwórz Model języka Python](create-python-model.md)


