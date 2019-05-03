---
title: 'Uczenie modelu klastrowania: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu uczenie modelu klastrowanie w usłudze Azure Machine Learning to w opracowywaniu modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 41cdec1d7f1c3932b17da6f9b1de518071f3f542
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028083"
---
# <a name="train-clustering-model"></a>Szkolenie Model klastrowania

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do nauczenia model klastrowania.

Moduł przyjmuje nieprzeszkolonych model klastrowania, która została już skonfigurowana przy użyciu [klastrowania K-średnich](k-means-clustering.md) modułu, a szkolenie modeli modelu przy użyciu zestawu danych etykietą lub bez etykiety. Moduł tworzy zarówno trenowanego modelu, który służy do prognozowania oraz zestaw przypisania klastra dla każdego przypadku w danych szkoleniowych.

> [!NOTE]
> Klastrowania nie modelu można być uczony przy użyciu [Train Model](train-model.md) moduł, który jest moduł ogólnego do szkolenia modeli uczenia maszynowego. To dlatego, że [Train Model](train-model.md) działa tylko w przypadku algorytmów uczenia nadzorowanego. K-średnich i inne algorytmy klastrowania umożliwiają uczenie nienadzorowane, co oznacza, że algorytm można uzyskać z poziomu danych bez etykiety.  
  
## <a name="how-to-use-train-clustering-model"></a>Jak używać Train Model klastrowania  
  
1.  Dodaj **Train Model klastrowania** modułu do eksperymentu w programie Studio. Można znaleźć modułu w obszarze **modułów usługi Machine Learning**w **Train** kategorii.  
  
2. Dodaj [klastrowania K-średnich](k-means-clustering.md) moduł innego niestandardowego modułu, który tworzy klaster zgodne modelu lub ustawić parametry model klastrowania.  
    
3.  Dołącz zestaw danych szkoleniowych do wprowadzania po prawej stronie **Train Model klastrowania**.
  
5.  W **zestawu kolumn**, zaznacz kolumny z zestawu danych do użycia podczas tworzenia klastrów. Pamiętaj wybrać kolumny, które funkcje dobrej: na przykład należy unikać używania identyfikatorów lub inne kolumny, które mają unikatowe wartości lub kolumny, które mają te same wartości.

    Jeśli etykieta jest dostępny, możesz go użyć jako funkcję lub opuszczenie go.  
  
6. Wybierz opcję **Check dołączania lub usuń zaznaczenie dla wyniku tylko**, jeśli chcesz wyprowadzić dane szkoleniowe, wraz z nową etykietę klastra.

    Jeśli wyłączysz tę opcję, tylko przypisania klastra są dane wyjściowe. 

7. Uruchom eksperyment, lub kliknij przycisk **Train Model klastrowania** modułu, a następnie wybierz pozycję **Uruchom wybrane**.  
  
### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:


+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, wybierz **wynik zestawów danych**i kliknij przycisk **Visualize**.

+ Aby zapisać uczonego modelu do nowszych ponownego użycia, kliknij prawym przyciskiem myszy moduł, wybierz **modelu Trained**i kliknij przycisk **zapisania Trenowanego modelu**.

+ Aby wygenerować wyniki z modelu, należy użyć [przypisywania danych do klastrów](assign-data-to-clusters.md).



## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 