---
title: 'Oceń polecania: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, w jaki sposób używać modułu "Oceń polecania" w usłudze Azure Machine Learning, aby oszacować dokładność prognoz modelu zalecanych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: dad11f0e03e55186dfee1e7a4f1f82fb44275693
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517997"
---
# <a name="evaluate-recommender"></a>Oceń zalecaną

W tym artykule opisano, jak używać modułu **oceny polecania** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu mierzenia dokładności prognoz dokonywanych przez model rekomendacji. Korzystając z tego modułu, można oszacować cztery różne rodzaje zaleceń:  
  
-   Klasyfikacje przewidziane dla danego użytkownika i elementu  
  
-   Elementy zalecane dla danego użytkownika  
  
Podczas tworzenia prognoz przy użyciu modelu rekomendacji dla każdego z obsługiwanych typów prognoz są zwracane nieco inne wyniki. W module **oceny zaleca** się wywnioskowanie rodzaju przewidywania z formatu kolumn zestawu danych z oceną. Na przykład, **wynikowy zestaw danych** może zawierać:

- Potrójne oceny elementów użytkownika
- Użytkownicy i ich zalecane elementy

Moduł stosuje również odpowiednie metryki wydajności w oparciu o typ wykonywanej prognozowania. 

  
## <a name="how-to-configure-evaluate-recommender"></a>Jak skonfigurować zalecaną ocenę

Moduł **szacowania polecania** porównuje dane wyjściowe prognoz przez model rekomendacji z odpowiednimi danymi "rzeczywistości". Na przykład moduł [polecający ten wynik](score-svd-recommender.md) umożliwia tworzenie oceny zestawów danych, które można analizować za pomocą **zalecenia oceny**.

### <a name="requirements"></a>Wymagania

Aby **oszacować polecał** , wymagane są następujące zestawy danych jako dane wejściowe. 
  
#### <a name="test-dataset"></a>Testowy zestaw danych

**Zestaw danych testu** zawiera dane "podstawowe prawdy" w postaci **potrójnych elementów użytkownika**.  

#### <a name="scored-dataset"></a>Wynikowy zestaw danych

**Wynikowy zestaw danych** zawiera przewidywania, które zostały wygenerowane przez model rekomendacji.  
  
Kolumny w tym drugim zestawie danych zależą od rodzaju prognoz wykonywanych podczas oceniania. Na przykład, wynikowy zestaw danych może zawierać dowolne z następujących elementów:

- Użytkownicy, elementy i klasyfikacje, które użytkownik może przyznać dla elementu
- Lista użytkowników i elementów zalecanych dla nich 

### <a name="metrics"></a>Metryki

Metryki wydajności dla modelu są generowane na podstawie typu danych wejściowych. Aby uzyskać szczegółowe informacje, zobacz następujące sekcje:

+ [Oceń przewidywane klasyfikacje](#evaluate-predicted-ratings)
+ [Oceń zalecenia dotyczące elementów](#evaluate-item-recommendations)

## <a name="evaluate-predicted-ratings"></a>Oceń przewidywane klasyfikacje  

Podczas oceniania przewidywanych klasyfikacji zestaw danych z oceną (drugie dane wejściowe do **oceny**) musi zawierać **trzykrotne oceny elementu użytkownika**, spełniając następujące wymagania:
  
-   Pierwsza kolumna zestawu danych zawiera identyfikatory użytkowników.  
  
-   Druga kolumna zawiera identyfikatory elementów.  
  
-   Trzecia kolumna zawiera odpowiednie oceny elementów użytkownika.  
  
> [!IMPORTANT] 
> Aby Ocena zakończyła się pomyślnie, nazwy kolumn muszą być odpowiednio `User`, `Item`i `Rating`.  
  
**Zalecamy** przeprowadzenie oceny w porównaniu do prognozowanych ocen zestawu danych, a następnie obliczy **średni błąd bezwzględny** (Mae) i **średni błąd oznaczający** , że (RMSE).



## <a name="evaluate-item-recommendations"></a>Oceń zalecenia dotyczące elementów

Podczas oceniania zalecenia dotyczącego elementu Użyj oceny zestawu danych, który zawiera zalecane elementy dla każdego użytkownika:
  
-   Pierwsza kolumna zestawu danych musi zawierać identyfikator użytkownika.  
  
-   Wszystkie kolejne kolumny powinny zawierać odpowiadające im identyfikatory elementów uporządkowane według odpowiedniego elementu. 

    Przed nawiązaniem połączenia z tym zestawem danych zalecamy posortowanie zestawu danych, tak aby najbardziej odpowiednie elementy były wcześniej dostępne.  



> [!IMPORTANT] 
> Aby **można było wykonać odpowiednie** działania, należy używać nazw kolumn `User`, `Item 1`, `Item 2`, `Item 3` i tak dalej.  
  
Funkcja **oceniania zaleca** obliczanie średniego znormalizowanego zysku z rabatem skumulowanym (**NDCG**) i zwraca go do wyjściowego zestawu danych.  
  
Ponieważ nie jest możliwe uzyskanie informacji o rzeczywistym "obmiocie prawdy" dla zalecanych elementów, zaleca się, aby **zalecamy** użycie klasyfikacji elementów użytkownika w testowym zestawie danych jako zyski w obliczeniach NDCG. Aby oszacować, moduł oceniania polecania musi generować tylko zalecenia dotyczące elementów mających klasyfikacje w języku prawdy (w zestawie danych testowych).  
  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 
