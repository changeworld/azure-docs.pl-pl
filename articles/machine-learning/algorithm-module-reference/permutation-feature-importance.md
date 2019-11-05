---
title: 'Ważność funkcji permutacji: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu ważności funkcji permutacji w usłudze Azure Machine Learning, aby obliczyć wyniki ważności funkcji permutacji zmiennych funkcji w ramach przeszkolonego modelu i zestawu danych testowych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 8a2ddb92101957a3dcadebb17dffa39113825e4b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517945"
---
# <a name="permutation-feature-importance"></a>Ważność funkcji permutacji

W tym artykule opisano, jak używać modułu [ważności funkcji permutacji](permutation-feature-importance.md) w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu obliczenia zestawu wyników ważności funkcji dla zestawu danych. Te wyniki służą do określenia najlepszych funkcji do użycia w modelu.

W tym module wartości funkcji są losowo przebierane, jedna kolumna w danym momencie, a wydajność modelu jest mierzona przed i po. Można wybrać jedną z metryk standardowych dostarczanych w celu mierzenia wydajności.

Wyniki zwracane przez moduł reprezentują **zmianę** wydajności przeszkolonego modelu, po permutacji. Ważne funkcje są zwykle bardziej poufne dla procesu Shuffling i w związku z tym spowodują zwiększenie ważności. 

Ten artykuł zawiera dobry ogólny przegląd ważności funkcji permutacji, jej teoretyczną podstawę i jej aplikacje w usłudze Machine Learning: [ważność funkcji permutacji](http://blogs.technet.com/b/machinelearning/archive/2015/04/14/permutation-feature-importance.aspx)  

## <a name="how-to-use-permutation-feature-importance"></a>Jak używać ważności funkcji permutacji

Aby wygenerować zestaw wyników funkcji, musisz mieć już szkolony model, a także zestaw danych testowych.  

1.  Dodaj moduł **ważności funkcji permutacji** do potoku. Ten moduł można znaleźć w kategorii **wybór funkcji** . 

2.  Połącz model szkolony z lewej strony. Model musi być modelem regresji lub modelem klasyfikacji.  

3.  Po prawej stronie Wprowadź połączenie zestawu danych, który jest inny niż zestaw danych używany do uczenia modelu. Ten zestaw danych jest używany do oceniania na podstawie przeszkolonego modelu i do oceny modelu po zmianie wartości funkcji.  

4.  W przypadku **losowego inicjatora**wpisz wartość, która ma być używana jako inicjator dla losowości. Jeśli określisz wartość 0 (wartość domyślna), jest generowana liczba na podstawie zegara systemowego.

     Wartość inicjatora jest opcjonalna, ale należy podać wartość, jeśli chcesz, aby odtwarzalność w przebiegach tego samego potoku.  

5.  Aby uzyskać **metrykę mierzenia wydajności**, wybierz jedną metrykę, która ma być używana podczas obliczania jakości modelu po permutacji.  

     Azure Machine Learning Designer obsługuje następujące metryki, w zależności od tego, czy oceniasz model klasyfikacji czy regresji:  

    -   **Classification** (Klasyfikacja)

        Dokładność, precyzja, odwołanie, średnia utrata dziennika  

    -   **Ubytk**

        Precyzja, odwoływanie, średnie Błędy bezwzględne, główny, średni, błąd względny  

     Aby uzyskać bardziej szczegółowy opis tych metryk oceny i sposobu ich obliczania, zobacz temat [ocenianie modelu](evaluate-model.md).  

6.  Uruchamianie potoku.  

7.  Moduł generuje listę kolumn funkcji i skojarzonych z nimi ocen, które są klasyfikowane według kolejności wyników, malejąco.  


##  <a name="technical-notes"></a>Uwagi techniczne

Ta sekcja zawiera szczegóły implementacji, porady i odpowiedzi na często zadawane pytania.

### <a name="how-does-this-compare-to-other-feature-selection-methods"></a>Jak jest porównywane z innymi metodami wyboru funkcji?

Ważność funkcji permutacji działa przez losowe zmiany wartości każdej kolumny funkcji, jednej kolumny w danym momencie, a następnie oceny modelu. 

Klasyfikacje zapewniane przez znaczenie funkcji permutacji są często różne od tych, które są uzyskiwane z [wyboru funkcji opartych na filtrach](filter-based-feature-selection.md), co oblicza wyniki **przed utworzeniem** modelu. 

Jest to spowodowane tym, że ważność funkcji permutacji nie mierzy skojarzenia między funkcją a wartością docelową, ale zamiast tego przechwytuje wpływ poszczególnych funkcji na przewidywania z modelu.
  
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
