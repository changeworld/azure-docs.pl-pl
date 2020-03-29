---
title: 'Oceń polecający: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Oceń polecający w usłudze Azure Machine Learning, aby ocenić dokładność prognoz modelu rekomendatora.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38144d5df04427a82989b78843466ecd55386196
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76312264"
---
# <a name="evaluate-recommender"></a>Ocena modułu poleceń

W tym artykule opisano, jak używać modułu Oceń polecaj w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Celem jest pomiar dokładności prognoz, które dokonał model rekomendacji. Za pomocą tego modułu, można ocenić różne rodzaje zaleceń:  
  
-   Oceny przewidywane dla użytkownika i elementu    
-   Elementy zalecane dla użytkownika  
  
Podczas tworzenia prognoz przy użyciu modelu rekomendacji, nieco inne wyniki są zwracane dla każdego z tych obsługiwanych typów prognozowania. Oceniaj rekomendatora moduł wywnicza rodzaj przewidywania z formatu kolumny zestawu danych ocena. Na przykład zestaw danychscored może zawierać:

- Potrójne oceny przedmiotu użytkownika
- Użytkownicy i ich zalecane elementy

Moduł stosuje również odpowiednie metryki wydajności, na podstawie typu przewidywanie. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Jak skonfigurować rekomendatora oceny

Oceniaj recommender moduł porównuje dane wyjściowe przewidywania przy użyciu modelu rekomendacji z odpowiednimi "prawdy ziemi" danych. Na przykład [wynik SVD Recommender](score-svd-recommender.md) moduł tworzy ocenione zestawy danych, które można analizować przy użyciu polecenia oceniać.

### <a name="requirements"></a>Wymagania

Ocena recommender wymaga następujących zestawów danych jako dane wejściowe. 
  
#### <a name="test-dataset"></a>Testowy zestaw danych

Testowy zestaw danych zawiera dane "prawdy gruntu" w postaci potrójnych klasyfikacji pozycji użytkownika.  

#### <a name="scored-dataset"></a>Zestaw danych oceny

Zestaw danychscored zawiera prognoz, które model rekomendacji generowane.  
  
Kolumny w tym drugim zestawie danych zależą od rodzaju przewidywanie, które zostały wykonane podczas procesu oceniania. Na przykład zestaw danychscored może zawierać jedną z następujących czynności:

- Użytkownicy, elementy i oceny, które użytkownik prawdopodobnie da dla elementu
- Lista użytkowników i przedmiotów zalecanych dla nich 

### <a name="metrics"></a>Metryki

Metryki wydajności dla modelu są generowane na podstawie typu danych wejściowych. W poniższych sekcjach podano szczegóły.

## <a name="evaluate-predicted-ratings"></a>Ocena przewidywanych ocen  

Podczas oceny przewidywanych ocen, oceniony zestaw danych (drugie dane wejściowe do Oceny relecatora) musi zawierać potrójne oceny elementu użytkownika, które spełniają te wymagania:
  
-   Pierwsza kolumna zestawu danych zawiera identyfikatory użytkownika.    
-   Druga kolumna zawiera identyfikatory elementów.  
-   Trzecia kolumna zawiera odpowiednie oceny elementu użytkownika.  
  
> [!IMPORTANT] 
> Aby ocena powiodła się, `User`nazwy `Item`kolumn `Rating`muszą być odpowiednio , i , .  
  
Polecenie Realec evaluate porównuje oceny w zestawie danych "prawdy gruntu" z przewidywanymi ocenami ocenionego zestawu danych. Następnie oblicza średni błąd bezwzględny (MAE) i główny średni błąd kwadratowy (RMSE).



## <a name="evaluate-item-recommendations"></a>Ocena rekomendacji dotyczących pozycji

Podczas oceny zaleceń dotyczących elementów należy użyć zestawu danych z oceną, który zawiera zalecane elementy dla każdego użytkownika:
  
-   Pierwsza kolumna zestawu danych musi zawierać identyfikator użytkownika.    
-   Wszystkie kolejne kolumny powinny zawierać odpowiednie zalecane identyfikatory towarów, uporządkowane według znaczenia elementu dla użytkownika. 

Przed nawiązaniem połączenia tego zestawu danych zaleca się sortowanie zestawu danych, tak aby najważniejsze elementy były najważniejsze.  

> [!IMPORTANT] 
> Aby polecenie Rekomendatora do `User`pracy, nazwy kolumn muszą być , `Item 1`, `Item 2` `Item 3` i tak dalej.  
  
Ocena Recommender oblicza średni znormalizowany zdyskontowany przyrost skumulowany (NDCG) i zwraca go w wyjściowym zestawie danych.  
  
Ponieważ nie można poznać rzeczywistej "prawdy gruntu" dla zalecanych elementów, Program Evaluate Recommender używa ocen elementu użytkownika w zestawie danych testowych jako zyski w obliczeniach NDCG. Aby ocenić, moduł oceniający rekomendujący musi tworzyć tylko zalecenia dla elementów z oceną "prawdy gruntu" (w zestawie danych testowych).  
  

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
