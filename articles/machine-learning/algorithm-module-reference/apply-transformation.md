---
title: 'Zastosuj transformację: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Zastosuj transformację w Azure Machine Learning zmodyfikować wejściowy zestaw danych na podstawie wcześniej obliczonego przekształcenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: 19385870d184654d902cd40b45d4be3646c87b46
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73493903"
---
# <a name="apply-transformation-module"></a>Zastosuj moduł transformacji

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonego przekształcenia.  
  
Na przykład jeśli zostały użyte wyniki z przeznaczeniem do normalizacji danych szkoleniowych przy użyciu modułu **normalizowanie danych** , warto użyć wartości z-Score, która została obliczona dla szkolenia w fazie oceniania. W Azure Machine Learning można zapisać metodę normalizacji jako transformację, a następnie za pomocą **zastosowania przekształcenia zastosować** wynikowe dane wejściowe przed oceną.
  
Azure Machine Learning zapewnia obsługę tworzenia i stosowania wielu różnych rodzajów niestandardowych transformacji. Na przykład możesz chcieć zapisać, a następnie ponownie użyć transformacji, aby:  
  
- Usuń lub Zastąp brakujące wartości, używając **czystego brakujących danych**
- Normalizowanie danych przy użyciu **normalizacji danych**
  

## <a name="how-to-use-apply-transformation"></a>Jak używać transformacji zastosowania  
  
1. Dodaj moduł **przekształcenia Zastosuj** do potoku. Ten moduł można znaleźć w obszarze **Machine Learning**, w kategorii **wynik** . 
  
2. Znajdź istniejącą transformację do użycia jako dane wejściowe.  Wcześniej zapisane przekształcenia można znaleźć w grupie **transformacje** w okienku nawigacji po lewej stronie.  
  
   
  
3. Połącz zestaw danych, który ma zostać przekształcony. Zestaw danych powinien mieć dokładnie ten sam schemat (liczbę kolumn, nazwy kolumn, typy danych) jako zestaw danych, dla którego przekształcenie zostało po raz pierwszy zaprojektowane.  
  
4. Nie trzeba ustawiać innych parametrów, ponieważ podczas definiowania transformacji jest wykonywane wszystkie dostosowania.  
  
5. Aby zastosować transformację do nowego zestawu danych, uruchom potok.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 