---
title: 'Przypisywanie danych do klastra: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z modułu Przypisywanie danych do klastra w usłudze Azure Machine Learning w celu oceny modelu klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 207172f10277589af2b22ae2f41b07234a0925b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477718"
---
# <a name="module-assign-data-to-clusters"></a>Moduł: Przypisywanie danych do klastrów

W tym artykule opisano sposób korzystania z modułu *Przypisywanie danych do klastrów* w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Moduł generuje prognoz za pośrednictwem modelu klastrowania, który został przeszkolony za pomocą algorytmu *klastrowania k-oznacza.*

Moduł Przypisz dane do klastrów zwraca zestaw danych zawierający prawdopodobne przypisania dla każdego nowego punktu danych. 

## <a name="how-to-use-assign-data-to-clusters"></a>Jak używać przypisywania danych do klastrów
  
1. W projektancie usługi Azure Machine Learning znajdź wcześniej przeszkolony model klastrowania. Model klastrowania można utworzyć i uszkolić przy użyciu jednej z następujących metod:  
  
    - Skonfiguruj algorytm klastrowania k oznacza przy użyciu [K-Means clustering](k-means-clustering.md) modułu i szkolić modelu przy użyciu zestawu danych i modułu modelu klastrowania pociągu (w tym artykule).  
  
    - Można również dodać istniejący uczony model klastrowania z grupy **Zapisane modele** w obszarze roboczym.

2. Dołącz przeszkolony model do lewego portu wejściowego **Przypisywanie danych do klastrów**.  

3. Dołącz nowy zestaw danych jako dane wejściowe. 

   W tym zestawie danych etykiety są opcjonalne. Ogólnie rzecz biorąc klastrowanie jest metodą uczenia się bez nadzoru. Nie oczekuje się, aby znać kategorie z wyprzedzeniem. Jednak kolumny wejściowe muszą być takie same jak kolumny, które były używane w szkoleniu modelu klastrowania lub występuje błąd.

    > [!TIP]
    > Aby zmniejszyć liczbę kolumn zapisywanych do projektanta z prognoz klastra, użyj [wybierz kolumny w zestawie danych](select-columns-in-dataset.md)i wybierz podzbiór kolumn. 
    
4. Pozostaw pole wyboru Sprawdź, czy chcesz, aby wynik był zaznaczony **lub wyczyść** zaznaczenie tylko wyników, jeśli wyniki mają zawierać pełny zestaw danych wejściowych, w tym kolumnę wyświetlaą wyniki (przypisania klastra).
  
    Jeśli to pole wyboru zostanie wyczyszczone, zwracane są tylko wyniki. Ta opcja może być przydatna podczas tworzenia prognoz w ramach usługi sieci web.
  
5.  Prześlij potok.  
  
### <a name="results"></a>Wyniki

+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, a następnie wybierz polecenie **Wizualizuj**. Lub Wybierz moduł i przełącz się na kartę **Wyjścia** w prawym panelu, kliknij ikonę histogramu w **wyjściach portu,** aby zwizualizować wynik.

