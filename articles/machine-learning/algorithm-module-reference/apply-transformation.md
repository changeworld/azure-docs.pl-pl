---
title: 'Zastosuj przekształcenia: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak Użyj modułu Zastosuj przekształcenie w usłudze Azure Machine Learning, aby zmodyfikować wejściowego zestawu danych oparte na wcześniej obliczanej transformacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 810f375642af49814049589cb83ad17fea578b13
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028713"
---
# <a name="apply-transformation-module"></a>Zastosuj przekształcenie modułu

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do modyfikowania wejściowego zestawu danych oparte na wcześniej obliczanej transformacji.  
  
Na przykład w przypadku korzystania z wyniki do normalizacji danych szkoleniowych przy użyciu **normalizacji danych** modułu, czy chcesz użyć została obliczona wartość wyniku z szkolenia w fazie oceny także. W usłudze Azure Machine Learning można zapisać jako przekształcenia, a następnie za pomocą metody normalizacji **Zastosuj przekształcenie** dotyczą wynik z danych wejściowych przed oceniania.
  
Usługa Azure Machine Learning zapewnia obsługę tworzenia, a następnie zastosowanie wielu różnych rodzajów niestandardowe przekształcenia. Na przykład możesz chcieć Zapisz, a następnie ponowne używanie przekształceń w celu:  
  
- Usuń lub Zamień brakujące wartości, przy użyciu **Clean Missing Data**
- Normalizacji danych za pomocą **normalizacji danych**
  

## <a name="how-to-use-apply-transformation"></a>Jak używać Zastosuj przekształcenia  
  
1. Dodaj **Zastosuj przekształcenie** modułu do eksperymentu. Można znaleźć w tym module w ramach **uczenia maszynowego**w **wynik** kategorii. 
  
2. Zlokalizuj istniejący przekształcenie do użycia jako dane wejściowe.  Przekształcenia zapisane wcześniej, można znaleźć w **przekształca** grupy w okienku nawigacji po lewej stronie.  
  
   
  
3. Połącz z zestawu danych, który chcesz przekształcić. Zestaw danych powinien mieć dokładnie ten sam schemat (liczba kolumn, nazwy kolumn i typy danych) jako zestawu danych, dla którego pierwotnie opracowano transformacji.  
  
4. Żadne inne parametry należy ustawić, ponieważ wszystkie dostosowania odbywa się podczas definiowania transformacji.  
  
5. Aby zastosować przekształcenie do nowego zestawu danych, uruchamianie eksperymentu.  

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 