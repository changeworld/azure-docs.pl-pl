---
title: 'Przypisywanie danych do klastra: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać modułu przypisywanie danych do klastra w usłudze Azure Machine Learning do oceny modelu klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 56f608044716ea3655576c11aa7a62343215f508
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128985"
---
# <a name="module-assign-data-to-clusters"></a>Elementu Przypisywanie danych do klastrów

W tym artykule opisano sposób korzystania z modułu *przypisywanie danych do klastrów* w Azure Machine Learning interfejsie wizualnym. Moduł generuje przewidywania za pośrednictwem modelu klastrowania, który został przeszkolony przy użyciu tego algorytmu.

Moduł przypisz dane do klastrów zwraca zestaw danych, który zawiera prawdopodobne przypisania dla każdego nowego punktu danych. 


## <a name="how-to-use-assign-data-to-clusters"></a>Jak używać przypisywania danych do klastrów
  
1. W Azure Machine Learning interfejsie wizualizacji Znajdź wcześniej przeszkolony model klastra. Model klastrowania można utworzyć i wyszkolić przy użyciu jednej z następujących metod:  
  
    - Konfiguruje K-oznacza algorytm klastrowania przy użyciu modułu [k-oznaczanie klastrowania](k-means-clustering.md) i uczenie modelu przy użyciu zestawu danych i modułu klastra usługi Uczenie (ten artykuł).  
  
    - Możesz również dodać istniejący model przeszkolonych klastrów z grupy **zapisane modele** w obszarze roboczym.

2. Dołącz model przeszkolony do lewego portu wejściowego przypisywania **danych do klastrów**.  

3. Dołącz nowy zestaw danych jako dane wejściowe. 

   W tym zestawie danych etykiety są opcjonalne. Ogólnie rzecz biorąc, klastrowanie to nienadzorowana Metoda uczenia się. Nie oczekuje się, że kategorie z góry są znane. Jednak kolumny wejściowe muszą być takie same jak kolumny, które były używane podczas uczenia modelu klastrowania lub wystąpi błąd.

    > [!TIP]
    > Aby zmniejszyć liczbę kolumn, które są zapisywane w interfejsie z prognoz klastra, użyj [opcji wybierz kolumny w zestawie danych](select-columns-in-dataset.md)i wybierz podzestaw kolumn. 
    
4. Pozostaw zaznaczone pole wyboru **Dołącz do dołączenia lub usuń zaznaczenie tylko dla wyniku** , jeśli chcesz, aby wyniki zawierały pełny wejściowy zestaw danych, w tym kolumnę, w której są wyświetlane wyniki (przypisania klastra).
  
    Jeśli wyczyścisz to pole wyboru, tylko wyniki zostaną zwrócone. Ta opcja może być przydatna podczas tworzenia prognoz w ramach usługi sieci Web.
  
5.  Uruchom eksperyment.  
  
### <a name="results"></a>Wyniki

+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **zestawy danych wynikowych**, a następnie wybierz polecenie **Wizualizuj**.

