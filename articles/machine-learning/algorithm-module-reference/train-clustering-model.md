---
title: 'Model klastra uczenia: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu uczenie modelu klastra w usłudze Azure Machine Learning do uczenia modeli klastrowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ms.openlocfilehash: 4883b1420913eb4e5f3bd5f13a95e410370d9184
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128472"
---
# <a name="train-clustering-model"></a>Trenowanie modelu klastrowania

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do uczenia modelu klastrowania.

Moduł przyjmuje niepociąg Model klastrowania, który został już skonfigurowany przy użyciu modułu [K-oznacza klastrowanie](k-means-clustering.md) , i połączy model przy użyciu zestawu danych z etykietą lub bez etykiety. Moduł tworzy zarówno model szkolony, którego można użyć do prognozowania, jak i zestaw przypisań klastra dla każdego przypadku w danych szkoleniowych.

> [!NOTE]
> Model klastrowania nie można być szkolony przy użyciu modułu [uczenie modelu](train-model.md) , który jest modułem ogólnym do uczenia modeli uczenia maszynowego. Wynika to z faktu, że [model uczenia](train-model.md) działa tylko z algorytmami uczenia nadzorowanego. K-oznacza i inne algorytmy klastrowania umożliwiają nienadzorowane uczenie, co oznacza, że algorytm może poznać dane nieoznaczone.  
  
## <a name="how-to-use-train-clustering-model"></a>Jak używać modelu klastrów uczenia  
  
1.  Dodaj moduł " **szkolenie" modelu klastra** do eksperymentu w programie Studio. Moduł można znaleźć w obszarze **Machine Learning modułów**w kategorii **szkolenie** .  
  
2. Dodaj moduł [K-oznaczający klastrowanie](k-means-clustering.md) lub inny niestandardowy moduł, który tworzy zgodny Model klastrowania, i ustaw parametry modelu klastrowania.  
    
3.  Dołączanie zestawu danych szkoleniowych do prawego wejścia **modelu klastra**szkoleń.
  
5.  W obszarze **zestaw kolumn**wybierz kolumny z zestawu danych, które mają być używane podczas tworzenia klastrów. Pamiętaj, aby wybrać kolumny, które tworzą dobre funkcje: na przykład Unikaj używania identyfikatorów lub innych kolumn, które mają unikatowe wartości, lub kolumn, które mają wszystkie te same wartości.

    Jeśli etykieta jest dostępna, możesz użyć jej jako funkcji lub pozostawić ją.  
  
6. Wybierz opcję, zaznacz pole **wyboru Dołącz lub usuń zaznaczenie tylko dla wyniku**, jeśli chcesz, aby dane szkoleniowe były wyprowadzane razem z nową etykietą klastra.

    W przypadku zaznaczenia tej opcji tylko przypisania klastra są wyprowadzane. 

7. Uruchom eksperyment lub kliknij moduł uczenie **modelu klastra** i wybierz pozycję **Uruchom wybrane**.  
  
### <a name="results"></a>Wyniki

Po zakończeniu szkolenia:


+  Aby wyświetlić wartości w zestawie danych, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **zestawy danych wynikowych**, a następnie kliknij polecenie **Wizualizuj**.

+ Aby zapisać przeszkolony model do późniejszego ponownego użycia, kliknij prawym przyciskiem myszy moduł, wybierz pozycję **przeszkolony model**i kliknij pozycję **Zapisz jako przeszkolony model**.

+ Aby wygenerować wyniki z modelu, użyj przypisywania [danych do klastrów](assign-data-to-clusters.md).



## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 