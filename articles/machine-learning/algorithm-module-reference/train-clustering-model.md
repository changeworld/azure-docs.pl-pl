---
title: 'Model klastrowania pociągu: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak korzystać z modułu modelu klastrowania pociągu w usłudze Azure Machine Learning do uczenia modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c29baf3cdda998a2ab78c84f3311b84d37086bcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477429"
---
# <a name="train-clustering-model"></a>Trenowanie modelu klastrowania

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do szkolenia modelu klastrowania.

Moduł przyjmuje nieprzeszkolony model klastrowania, który został już skonfigurowany przy użyciu modułu [klastrowania K-Means,](k-means-clustering.md) i trenuje model przy użyciu zestawu danych oznaczonych etykietą lub bez etykiety. Moduł tworzy zarówno wyszkolony model, który można użyć do przewidywania, jak i zestaw przypisań klastra dla każdego przypadku w danych szkoleniowych.

> [!NOTE]
> Nie można uszkoć modelu klastrowania przy użyciu modułu [Model pociągu,](train-model.md) który jest modułem ogólnym do szkolenia modeli uczenia maszynowego. To [dlatego, że Model pociągu](train-model.md) działa tylko z nadzorowanych algorytmów uczenia. K-środki i inne algorytmy klastrowania umożliwiają nienadzorowane uczenie się, co oznacza, że algorytm może uczyć się na danych bez etykiety.  
  
## <a name="how-to-use-train-clustering-model"></a>Jak korzystać z modelu klastrowania pociągu  

1.  Dodaj **moduł modelu klastrowania pociągu** do potoku w projektancie. Moduł można znaleźć w obszarze **Moduły uczenia maszynowego**w kategorii **Pociąg.**  
  
2. Dodaj moduł [klastrowania K-Means](k-means-clustering.md) lub inny moduł niestandardowy, który tworzy zgodny model klastrowania i ustaw parametry modelu klastrowania.  
    
3.  Dołącz zestaw danych szkoleniowych do po prawej stronie wejścia **modelu klastrowania pociągu**.
  
5.  W **polu Zestaw kolumn**wybierz kolumny z zestawu danych, które mają być używane w klastrach. Pamiętaj, aby wybrać kolumny, które tworzą dobre funkcje: na przykład unikaj używania identyfikatorów lub innych kolumn, które mają unikatowe wartości, lub kolumn, które mają te same wartości.

    Jeśli etykieta jest dostępna, można jej użyć jako elementu lub pozostawić ją na zewnątrz.  
  
6. Wybierz opcję **Sprawdź, czy należy dołączyć lub odznacz tylko wynik**, jeśli chcesz wyprowadzić dane szkoleniowe razem z nową etykietą klastra.

    Jeśli usuniesz zaznaczenie tej opcji, tylko przypisania klastra są dane wyjściowe. 

7. Prześlij potok lub kliknij moduł **Modelu klastrowania pociągu** i wybierz pozycję **Uruchom zaznaczone**.  
  
### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:

+ Aby zapisać migawkę uczonego modelu, wybierz kartę **Wyjścia** w prawym panelu modułu **Modelu pociągu.** Wybierz ikonę **Zarejestruj zestaw danych,** aby zapisać model jako moduł wielokrotnego wyboru.

+ Aby wygenerować wyniki z modelu, należy użyć funkcji [Przypisywanie danych do klastrów](assign-data-to-clusters.md).

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 