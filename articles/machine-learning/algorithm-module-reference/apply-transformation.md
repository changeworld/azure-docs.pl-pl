---
title: 'Zastosuj transformację: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Zastosuj transformację w usłudze Azure Machine Learning, aby zmodyfikować wejściowy zestaw danych na podstawie wcześniej obliczonej transformacji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78395259"
---
# <a name="apply-transformation-module"></a>Zastosuj moduł transformacji

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonej transformacji.

Na przykład jeśli użyto z-scores do normalizacji danych szkoleniowych przy użyciu modułu **Normalizuj dane,** należy użyć wartości z-score, która została obliczona do treningu podczas fazy oceniania. W usłudze Azure Machine Learning można zapisać metodę normalizacji jako transformację, a następnie za pomocą **zastosuj transformację,** aby zastosować wynik z do danych wejściowych przed ocenianiem.

## <a name="how-to-save-transformations"></a>Jak zapisywać przekształcenia

Projektant umożliwia zapisywanie przekształceń danych jako **zestawów danych,** dzięki czemu można ich używać w innych potokach.

1. Wybierz moduł przekształcania danych, który został pomyślnie uruchomiony.

1. Wybierz kartę **Wyjścia + Dzienniki.**

1. Wybierz **ikonę Zapisz,** aby zapisać **transformację wyników**.

## <a name="how-to-use-apply-transformation"></a>Jak korzystać z zastosuj transformację  
  
1. Dodaj **zastosuj transformację** modułu do potoku. Ten moduł można znaleźć w sekcji **Ocena oceniania modelu & palety** modułów. 
  
1. Znajdź zapisaną transformację, której chcesz użyć w obszarze **Zestawy** > danych**Moje zestawy danych** w palecie modułów.

1. Połącz dane wyjściowe zapisanej transformacji z lewym portem wejściowym modułu **Zastosuj transformację.**

    Zestaw danych powinien mieć dokładnie ten sam schemat (liczba kolumn, nazwy kolumn, typy danych) co zestaw danych, dla którego transformacja została zaprojektowana po raz pierwszy.  
  
1. Podłącz dane wyjściowe zestawu danych żądanego modułu do prawego portu wejściowego modułu **Zastosuj transformację.**
  
1. Aby zastosować transformację do nowego zestawu danych, uruchom potok.  

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 