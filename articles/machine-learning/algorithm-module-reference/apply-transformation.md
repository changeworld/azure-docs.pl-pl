---
title: 'Zastosuj transformację: Dokumentacja modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak zmodyfikować wejściowy zestaw danych na podstawie wcześniej obliczonego przekształcenia przy użyciu modułu Zastosuj przekształcenie w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: 8b4ec9e71d9ce932a7b206467db91760f4a4fc47
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70128978"
---
# <a name="apply-transformation-module"></a>Zastosuj przekształcenie modułu

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonego przekształcenia.  
  
Na przykład jeśli zostały użyte wyniki z przeznaczeniem do normalizacji danych szkoleniowych przy użyciu modułu **normalizowanie danych** , warto użyć wartości z-Score, która została obliczona dla szkolenia w fazie oceniania. W Azure Machine Learning można zapisać metodę normalizacji jako transformację, a następnie za pomocą **zastosowania przekształcenia zastosować** wynikowe dane wejściowe przed oceną.
  
Azure Machine Learning zapewnia obsługę tworzenia i stosowania wielu różnych rodzajów niestandardowych transformacji. Na przykład możesz chcieć zapisać, a następnie ponownie użyć transformacji, aby:  
  
- Usuń lub Zastąp brakujące wartości, używając **czystego brakujących danych**
- Normalizowanie danych przy użyciu **normalizacji danych**
  

## <a name="how-to-use-apply-transformation"></a>Jak używać transformacji zastosowania  
  
1. Dodaj do eksperymentu moduł **Zastosuj transformację** . Ten moduł można znaleźć w obszarze **Machine Learning**, w kategorii **wynik** . 
  
2. Znajdź istniejącą transformację do użycia jako dane wejściowe.  Wcześniej zapisane przekształcenia można znaleźć w grupie **transformacje** w okienku nawigacji po lewej stronie.  
  
   
  
3. Połącz zestaw danych, który ma zostać przekształcony. Zestaw danych powinien mieć dokładnie ten sam schemat (liczbę kolumn, nazwy kolumn, typy danych) jako zestaw danych, dla którego przekształcenie zostało po raz pierwszy zaprojektowane.  
  
4. Nie trzeba ustawiać innych parametrów, ponieważ podczas definiowania transformacji jest wykonywane wszystkie dostosowania.  
  
5. Aby zastosować transformację do nowego zestawu danych, uruchom eksperyment.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 