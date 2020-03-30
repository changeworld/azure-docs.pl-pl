---
title: 'Dodawanie wierszy: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Dodaj wiersze w usłudze Azure Machine Learning, aby połączyć dwa zestawy danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: cd9b5f8f182c4deab746d2c41e516a6ac23fb7aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477735"
---
# <a name="add-rows-module"></a>Dodawanie modułu Wiersze

W tym artykule opisano moduł w projektancie usługi Azure Machine Learning (wersja zapoznawcza).

Ten moduł służy do łączenia dwóch zestawów danych. W konkabitacji wiersze drugiego zestawu danych są dodawane na końcu pierwszego zestawu danych.  
  
Łączenie wierszy jest przydatne w scenariuszach, takich jak te:  
  
+ Wygenerowałeś serię statystyk oceny i chcesz połączyć je w jedną tabelę, aby ułatwić raportowanie.  
  
+ Pracujesz z różnymi zestawami danych i chcesz połączyć zestawy danych, aby utworzyć ostateczny zestaw danych.  

## <a name="how-to-use-add-rows"></a>Jak korzystać z funkcji Dodaj wiersze  

Aby łączyć wiersze z dwóch zestawów danych, wiersze muszą mieć dokładnie ten sam schemat. Oznacza to taką samą liczbę kolumn i ten sam typ danych w kolumnach.

1.  Przeciągnij moduł **Dodaj wiersze** do potoku, można go znaleźć w obszarze **Transformacja danych**.

2. Podłącz zestawy danych do dwóch portów wejściowych. Zestaw danych, który chcesz dołączyć, powinien być połączony z drugim portem (po prawej). 
  
3.  Prześlij potok. Liczba wierszy w wyjściowym zestawie danych powinna być równa sumie wierszy obu wejściowych zestawów danych.

    Jeśli dodasz ten sam zestaw danych do obu danych wejściowych modułu **Dodaj wiersze,** zestaw danych zostanie zduplikowany. 

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 