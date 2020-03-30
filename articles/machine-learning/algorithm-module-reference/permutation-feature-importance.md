---
title: 'Znaczenie funkcji permutacji: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Ważność funkcji permutacji w usłudze Azure Machine Learning, aby obliczyć wyniki ważności funkcji permutacji zmiennych funkcji, biorąc pod uwagę przeszkolony model i zestaw danych testowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/24/2020
ms.openlocfilehash: e4511cf4393172e7d2b1ab8a985c76d8f98d4015
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79456067"
---
# <a name="permutation-feature-importance"></a>Ważność funkcji permutacji

W tym artykule opisano, jak użyć modułu Ważność funkcji permutacji w projektancie usługi Azure Machine Learning (wersja zapoznawcza), aby obliczyć zestaw wyników ważności funkcji dla zestawu danych. Te wyniki są używane do określania najlepszych funkcji do użycia w modelu.

W tym module wartości operacji są losowo tasowane, po jednej kolumnie naraz. Wydajność modelu jest mierzona przed i po. Można wybrać jedną ze standardowych metryk do pomiaru wydajności.

Wyniki, które zwraca moduł reprezentują *zmiany* w wydajności modelu przeszkolonego, po permutacji. Ważne funkcje są zwykle bardziej wrażliwe na proces tasowania, więc będą one skutkować wyższą ocenę ważności. 

Ten artykuł zawiera omówienie funkcji permutacji, jej podstawy teoretycznej i jej aplikacji w uczeniu maszynowym: [Znaczenie funkcji permutacji](https://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx).  

## <a name="how-to-use-permutation-feature-importance"></a>Jak korzystać ze charakterystycznego znaczenia funkcji permutacji

Generowanie zestawu wyników funkcji wymaga, aby mieć już przeszkolony model, a także testowy zestaw danych.  

1.  Dodaj moduł Ważność funkcji permutacji do potoku. Ten moduł można znaleźć w kategorii **Wybór funkcji.** 

2.  Podłącz przeszkolony model do lewego wejścia. Model musi być modelem regresji lub modelem klasyfikacji.  

3.  Po prawej stronie wejścia połącz zestaw danych. Najlepiej wybrać taki, który różni się od zestawu danych, który był używany do szkolenia modelu. Ten zestaw danych jest używany do oceniania na podstawie modelu przeszkolonego. Jest również używany do oceny modelu po zmianie wartości operacji.  

4.  W polu **Losowy materiał siewny**wprowadź wartość używaną jako materiał siewny do randomizacji. Jeśli określisz 0 (wartość domyślna), liczba jest generowana na podstawie zegara systemowego.

     Wartość inicjatora jest opcjonalna, ale należy podać wartość, jeśli chcesz odtwarzalność w przebiegach tego samego potoku.  

5.  Aby uzyskać **metrykę pomiaru wydajności,** wybierz jedną metrykę, która będzie używana podczas obliczania jakości modelu po permutacji.  

     Projektant usługi Azure Machine Learning obsługuje następujące metryki, w zależności od tego, czy oceniasz model klasyfikacji czy regresji:  

    -   **Classification** (Klasyfikacja)

        Dokładność, precyzja, przywołanie  

    -   **Regresji**

        Precyzja, odwołanie, średni błąd bezwzględny, średni błąd kwadratu korzenia, względny błąd bezwzględny, względny błąd kwadratu, współczynnik oznaczania  

     Aby uzyskać bardziej szczegółowy opis tych wskaźników oceny i sposobu ich obliczania, zobacz [Ocena modelu](evaluate-model.md).  

6.  Prześlij potok.  

7.  Moduł wyprowadza listę kolumn funkcji i wyniki skojarzone z nimi. Lista jest klasyfikowana w malejącej kolejności wyników.  


##  <a name="technical-notes"></a>Uwagi techniczne

Znaczenie funkcji permutacji polega na losowej zmianie wartości każdej kolumny obiektowej, po jednej kolumnie naraz. Następnie ocenia model. 

Rankingi, które zapewnia moduł, często różnią się od tych, które otrzymujesz z [wyboru funkcji opartych na filtrze.](filter-based-feature-selection.md) Wybór operacji oparty na filtrze oblicza wyniki *przed* utworzeniem modelu. 

Powodem różnicy jest to, że znaczenie funkcji permutacji nie mierzy skojarzenia między funkcją a wartością docelową. Zamiast tego przechwytuje, jak duży wpływ ma każda funkcja na prognozy z modelu.
  
## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
