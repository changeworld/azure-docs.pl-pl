---
title: 'Dodaj wiersze: Odwołania do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak użyć modułu Dodaj wiersze w usłudze Azure Machine Learning do łączenia dwóch zestawów danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ffd693ea3452ef48dc3e05e7bc4a6d3988a487b0
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/02/2019
ms.locfileid: "65028758"
---
# <a name="add-rows-module"></a>Dodaj moduł wierszy

W tym artykule opisano moduł interfejs graficzny (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do łączenia dwóch zestawów danych. W złączenie wierszy drugiego zestawu są dodawane na końcu pierwszego zestawu danych.  
  
Łączenie wierszy jest przydatne w scenariuszach takich jak te:  
  
+ Został wygenerowany szereg Obliczanie statystyk, a chcesz połączyć je w jednej tabeli w celu raportowania łatwiejsze.  
  
+ Pracujesz w za pomocą różnych zestawów danych, a chcesz połączyć zestawy danych do utworzenia ostatecznego zestawu danych.  

## <a name="how-to-use-add-rows"></a>Jak używać Dodaj wiersze  

Do łączenia wierszy z dwóch zestawów danych, wiersze musi mieć ten sam schemat. Oznacza to, tę samą liczbę kolumn, a ten sam typ danych w kolumnach.

1.  Przeciągnij **Dodaj wiersze** modułu w eksperymencie, możesz go znaleźć w obszarze **przekształcania danych**w **manipulowanie** kategorii.

2. Zestawy danych należy nawiązać dwa porty wejściowe. Zestaw danych, który chcesz dołączyć powinny być połączone do drugiego (po prawej). 
  
3.  Uruchom eksperyment. Liczba wierszy w zestawie danych wyjściowych danych powinna być równa sum wierszy zarówno wejściowych zestawów danych.

    Po dodaniu tego samego zestawu danych do zarówno dane wejściowe **Dodaj wiersze** modułu, zestaw danych jest zduplikowana. 

## <a name="next-steps"></a>Kolejne kroki

Zobacz [zestaw dostępnych modułów](module-reference.md) do usługi Azure Machine Learning. 