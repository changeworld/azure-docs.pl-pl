---
title: 'Przypisz dane do klastra: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak używać danych Przypisz do modułu klastra w usłudze Azure Machine Learning do score model klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1c2d2a02ecfb617551dd9174b87f363d57b151a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65467197"
---
# <a name="module-assign-data-to-clusters"></a>Moduł: Przypisz dane do klastrów

W tym artykule opisano sposób używania *przypisywania danych do klastrów* modułu w interfejsie visual usługi Azure Machine Learning. Moduł generuje prognozy za pośrednictwem model klastrowania, która została wyszkolonego przy *klastrowania K-średnich* algorytmu.

Przypisz dane moduł klastrów zwraca zestaw danych, który zawiera prawdopodobne przypisania dla każdego nowego punktu danych. 


## <a name="how-to-use-assign-data-to-clusters"></a>Jak za pomocą przypisywania danych do klastrów
  
1. W usłudze Azure Machine Learning interfejs graficzny Znajdź wcześniej uczony model klastrowania. Można tworzyć i wytrenuj model klastrowania przy użyciu jednej z następujących metod:  
  
    - Konfigurowanie algorytmu klastrowania K-średnich przy użyciu [klastrowania K-średnich](k-means-clustering.md) moduł i szkolenie modelu przy użyciu zestawu danych i modułów uczenia modelu klastra (w tym artykule).  
  
    - Możesz również dodać istniejące uczony model klastrowania z **zapisane modeli** grupy w obszarze roboczym.

2. Dołącz uczonego modelu do lewy port wejściowy z **przypisywania danych do klastrów**.  

3. Dołącz nowy zestaw danych jako danych wejściowych. 

   W tym zestawie danych etykiety są opcjonalne. Ogólnie rzecz biorąc klastrowania jest metodą nauki nienadzorowanych. Nie powinny kategorie wiedzieć z wyprzedzeniem. Jednak kolumny wejściowe musi być taka sama, jak kolumny, które były używane w szkolenia, który model klastrowania, lub występuje błąd.

    > [!TIP]
    > Aby zmniejszyć liczbę kolumn, które są zapisywane do interfejsu z prognozy klastra, należy użyć [Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md)i wybierz podzbiór kolumn. 
    
4. Pozostaw **Check dołączania lub usuń zaznaczenie dla wyniku tylko** pole wyboru jest zaznaczone, jeśli chcesz, aby wyniki zawierają pełną wejściowego zestawu danych, łącznie z kolumny, która służy do wyświetlania wyników (przypisania klastra).
  
    Jeśli usuniesz zaznaczenie tego pola wyboru, zwracane są tylko wyniki. Ta opcja może być przydatne podczas tworzenia prognoz jako część usługi sieci web.
  
5.  Uruchom eksperyment.  
  
### <a name="results"></a>Wyniki

+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, wybierz **wynik zestawów danych**, a następnie wybierz pozycję **Visualize**.

