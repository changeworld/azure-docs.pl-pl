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
ms.openlocfilehash: b370c6ef5be311ed9c8df2737fa1b01144d61011
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028773"
---
# <a name="assign-data-to-clusters"></a>Przypisz dane do klastrów

W tym artykule opisano sposób używania [przypisywania danych do klastrów](assign-data-to-clusters.md) modułu w interfejsie visual, aby wygenerować prognozy przy użyciu klastrowania model, który został uczony przy użyciu algorytmu klastrowania K-średnich.

Moduł zwraca zestaw danych, który zawiera prawdopodobne przypisania dla każdego nowego punktu danych. 


## <a name="how-to-use-assign-data-to-clusters"></a>Jak za pomocą przypisywania danych do klastrów
  
1.  W interfejsie visual zlokalizować poprzednio uczony model klastrowania. Można tworzyć i wytrenuj model klastrowania przy użyciu jednej z następujących metod:  
  
    - Konfigurowanie przy użyciu algorytmu K-średnich [klastrowania K-średnich](k-means-clustering.md) modułu, a następnie train model przy użyciu zestawu danych i [uczenie modelu klastrowania](train-clustering-model.md) modułu.  
  
  
    Możesz również dodać istniejące uczony model klastrowania z **zapisane modeli** grupy w obszarze roboczym.

2. Dołącz uczonego modelu do lewy port wejściowy z [przypisywania danych do klastrów](assign-data-to-clusters.md).  

3. Dołącz nowy zestaw danych jako danych wejściowych. W tym zestawie danych etykiety są opcjonalne. Ogólnie rzecz biorąc klastrowania jest metodą uczenie nienadzorowane, więc nie oczekuje się, że będzie wiadomo, kategorie z wyprzedzeniem.

    Jednak kolumny wejściowe musi być taka sama, jak kolumny, które były używane w szkolenia, który model klastrowania, lub występuje błąd.

    > [!TIP]
    > Aby zmniejszyć liczbę kolumn w danych wyjściowych z prognozy klastra, należy użyć [Select Columns in Dataset](select-columns-in-dataset.md)i wybierz podzbiór kolumn. 
    
4. Pozostaw opcję **Check dołączania lub usuń zaznaczenie dla wyniku tylko** wybrane, jeśli chcesz, aby wyniki zawierają pełną wejściowy zestaw danych, wraz z kolumną informujący o wyniki (przypisania klastra).
  
    Jeśli wyłączysz tę opcję, można odzyskać tylko wyniki. Może to być przydatne podczas tworzenia prognoz jako część usługi sieci web.
  
5.  Uruchom eksperyment.  
  
### <a name="results"></a>Wyniki

 
+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, wybierz **wynik zestawów danych**i kliknij przycisk **Visualize**.

