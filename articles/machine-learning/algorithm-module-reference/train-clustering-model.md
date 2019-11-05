---
title: 'Model usługi Uczenie: informacje o module'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu uczenie modelu klastra w Azure Machine Learning do uczenia modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 1b26fc251aeb527041b1e648f19f9dd67f7701b7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73490390"
---
# <a name="train-clustering-model"></a>Trenowanie modelu klastrowania

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do uczenia modelu klastrowania.

Moduł przyjmuje niepociąg Model klastrowania, który został już skonfigurowany przy użyciu modułu [K-oznacza klastrowanie](k-means-clustering.md) , i połączy model przy użyciu zestawu danych z etykietą lub bez etykiety. Moduł tworzy zarówno model szkolony, którego można użyć do prognozowania, jak i zestaw przypisań klastra dla każdego przypadku w danych szkoleniowych.

> [!NOTE]
> Model klastrowania nie można być szkolony przy użyciu modułu [uczenie modelu](train-model.md) , który jest modułem ogólnym do uczenia modeli uczenia maszynowego. Wynika to z faktu, że [model uczenia](train-model.md) działa tylko z algorytmami uczenia nadzorowanego. K-oznacza i inne algorytmy klastrowania umożliwiają nienadzorowane uczenie, co oznacza, że algorytm może poznać dane nieoznaczone.  
  
## <a name="how-to-use-train-clustering-model"></a>Jak używać modelu klastrów uczenia  

1.  Dodaj moduł **uczenie modelu klastra** do potoku w projektancie. Moduł można znaleźć w obszarze **Machine Learning modułów**w kategorii **szkolenie** .  
  
2. Dodaj moduł [K-oznaczający klastrowanie](k-means-clustering.md) lub inny niestandardowy moduł, który tworzy zgodny Model klastrowania, i ustaw parametry modelu klastrowania.  
    
3.  Dołączanie zestawu danych szkoleniowych do prawego wejścia **modelu klastra**szkoleń.
  
5.  W obszarze **zestaw kolumn**wybierz kolumny z zestawu danych, które mają być używane podczas tworzenia klastrów. Pamiętaj, aby wybrać kolumny, które tworzą dobre funkcje: na przykład Unikaj używania identyfikatorów lub innych kolumn, które mają unikatowe wartości, lub kolumn, które mają wszystkie te same wartości.

    Jeśli etykieta jest dostępna, możesz użyć jej jako funkcji lub pozostawić ją.  
  
6. Wybierz opcję, zaznacz pole **wyboru Dołącz lub usuń zaznaczenie tylko dla wyniku**, jeśli chcesz, aby dane szkoleniowe były wyprowadzane razem z nową etykietą klastra.

    W przypadku zaznaczenia tej opcji tylko przypisania klastra są wyprowadzane. 

7. Uruchom potok lub kliknij moduł **uczenie modelu klastra** , a następnie wybierz pozycję **Uruchom wybrane**.  
  
### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:


+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **zestawy danych wynikowych**, a następnie kliknij polecenie **Wizualizuj**.

+ Aby zapisać przeszkolony model do późniejszego ponownego użycia, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **przeszkolony model**i kliknij pozycję **Zapisz jako przeszkolony model**.

+ Aby wygenerować wyniki z modelu, użyj [przypisywania danych do klastrów](assign-data-to-clusters.md).



## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 