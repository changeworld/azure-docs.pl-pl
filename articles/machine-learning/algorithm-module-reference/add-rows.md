---
title: 'Dodawanie wierszy: odwołanie do modułu'
titleSuffix: Azure Machine Learning service
description: Dowiedz się, jak połączyć dwa zestawy danych przy użyciu modułu Dodaj wiersze w usłudze Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/02/2019
ms.openlocfilehash: c8d8b6a873ee17d1658b0cb47de830848b215b89
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "72693247"
---
# <a name="add-rows-module"></a>Dodaj moduł wierszy

W tym artykule opisano moduł Visual Interface (wersja zapoznawcza) dla usługi Azure Machine Learning.

Ten moduł służy do łączenia dwóch zestawów danych. W trakcie łączenia wiersze drugiego zestawu danych są dodawane na końcu pierwszego zestawu danych.  
  
Łączenie wierszy jest przydatne w scenariuszach takich jak:  
  
+ Wygenerowałeś serię statystyk oceny i chcesz połączyć je w jedną tabelę, aby ułatwić raportowanie.  
  
+ Pracujesz z różnymi zestawami danych i chcesz połączyć zestawy danych w celu utworzenia końcowego zestawu.  

## <a name="how-to-use-add-rows"></a>Jak używać Dodawanie wierszy  

Aby połączyć wiersze z dwóch zestawów danych, wiersze muszą mieć dokładnie ten sam schemat. Oznacza to, że ta sama liczba kolumn i ten sam typ danych w kolumnach.

1.  Przeciągnij moduł **Dodaj wiersze** do potoku, aby znaleźć go w obszarze **Przekształcanie danych**, w kategorii **manipulowanie** .

2. Połącz zestawy danych z dwoma portami wejściowymi. Zestaw danych, który chcesz dołączyć, powinien być połączony z drugim (prawym) portem. 
  
3.  Uruchamianie potoku. Liczba wierszy w wyjściowym zestawie danych powinna być równa sumie wierszy wejściowych zestawów danych.

    Jeśli dodasz ten sam zestaw danych do obu wejść modułu **Dodaj wiersze** , zestaw danych zostanie zduplikowany. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning usługi. 