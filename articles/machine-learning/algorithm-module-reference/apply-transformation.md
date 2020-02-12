---
title: 'Zastosuj transformację: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Zastosuj transformację w Azure Machine Learning zmodyfikować wejściowy zestaw danych na podstawie wcześniej obliczonego przekształcenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 443b021b266a202775e94e44acac3a91a2b70617
ms.sourcegitcommit: b95983c3735233d2163ef2a81d19a67376bfaf15
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77137584"
---
# <a name="apply-transformation-module"></a>Zastosuj przekształcenie modułu

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonego przekształcenia.  
  
Na przykład jeśli zostały użyte wyniki z przeznaczeniem do normalizacji danych szkoleniowych przy użyciu modułu **normalizowanie danych** , warto użyć wartości z-Score, która została obliczona dla szkolenia w fazie oceniania. W Azure Machine Learning można zapisać metodę normalizacji jako transformację, a następnie za pomocą **zastosowania przekształcenia zastosować** wynikowe dane wejściowe przed oceną.
  
Azure Machine Learning zapewnia obsługę tworzenia i stosowania wielu różnych rodzajów niestandardowych transformacji. Na przykład możesz chcieć zapisać, a następnie ponownie użyć transformacji, aby:  
  
- Usuń lub Zastąp brakujące wartości, używając **czystego brakujących danych**
- Normalizowanie danych przy użyciu **normalizacji danych**
  

## <a name="how-to-use-apply-transformation"></a>Jak używać transformacji zastosowania  
  
1. Dodaj moduł **przekształcenia Zastosuj** do potoku. Ten moduł można znaleźć w kategorii **oceny &** oceniania. 
  
2. Znajdź istniejącą transformację do użycia jako dane wejściowe. Wcześniej zapisane przekształcenia można znaleźć w grupie **Moje zestawy danych** w obszarze Kategorie **zestawów danych** w lewym drzewie modułu.  
  
   
  
3. Połącz zestaw danych, który ma zostać przekształcony. Zestaw danych powinien mieć dokładnie ten sam schemat (liczbę kolumn, nazwy kolumn, typy danych) jako zestaw danych, dla którego przekształcenie zostało po raz pierwszy zaprojektowane.  
  
4. Nie trzeba ustawiać innych parametrów, ponieważ podczas definiowania transformacji jest wykonywane wszystkie dostosowania.  
  
5. Aby zastosować transformację do nowego zestawu danych, uruchom potok.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 