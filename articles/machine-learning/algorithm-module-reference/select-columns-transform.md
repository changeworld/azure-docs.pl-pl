---
title: 'Wybierz przekształć kolumny: Odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Przekształcanie wybranych kolumn w usłudze Azure Machine Learning, aby utworzyć transformację, która wybiera ten sam podzbiór kolumn, co w danym zestawie danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: a5264c14294f84858cd489f5892b8cdd19e117d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79455914"
---
# <a name="select-columns-transform"></a>Wybieranie przekształcenia kolumn

W tym artykule opisano, jak używać modułu Przekształcanie wybranych kolumn w projektancie usługi Azure Machine Learning (wersja zapoznawcza). Celem modułu Przekształcanie wybranych kolumn jest zapewnienie, że przewidywalny, spójny zestaw kolumn jest używany w dalszych operacjach uczenia maszynowego.

Ten moduł jest przydatny w przypadku zadań, takich jak ocenianie, które wymagają określonych kolumn. Zmiany w dostępnych kolumnach może przerwać potoku lub zmienić wyniki.

Do utworzenia i zapisania zestawu kolumn służy polecenie Wybierz przekształcenie kolumn. Następnie użyj [zastosuj transformację](apply-transformation.md) moduł, aby zastosować te wybory do nowych danych.

## <a name="how-to-use-select-columns-transform"></a>Jak korzystać z funkcji Przekształć wybrane kolumny

W tym scenariuszu przyjęto założenie, że chcesz użyć wyboru funkcji do generowania dynamicznego zestawu kolumn, które będą używane do szkolenia modelu. Aby upewnić się, że wybory kolumn są takie same dla procesu oceniania, należy użyć Wybierz kolumny Transform moduł do przechwytywania wyborów kolumn i zastosować je w innym miejscu w potoku.

1. Dodaj wejściowy zestaw danych do potoku w projektancie.

2. Dodaj wystąpienie [wyboru operacji opartej na filtrze](filter-based-feature-selection.md).

3. Podłącz moduły i skonfiguruj moduł wyboru funkcji, aby automatycznie znaleźć kilka najlepszych funkcji w wejściowym zestawie danych.

4. Dodaj wystąpienie [modelu pociągu](train-model.md) i użyj danych wyjściowych wyboru funkcji [opartych na filtrze](filter-based-feature-selection.md) jako danych wejściowych dla szkolenia.

    > [!IMPORTANT]
    > Ponieważ ważność operacji jest oparta na wartościach w kolumnie, nie wiadomo z wyprzedzeniem, które kolumny mogą być dostępne dla danych wejściowych do [modelu pociągu](train-model.md).  
5. Dołącz wystąpienie modułu Przekształć kolumny Wybierz. 

    Ten krok generuje zaznaczenie kolumny jako transformację, którą można zapisać lub zastosować do innych zestawów danych. Ten krok gwarantuje, że kolumny zidentyfikowane w wyborze funkcji są zapisywane dla innych modułów do ponownego użycia.

6. Dodaj moduł [Model wyników.](score-model.md) 

   *Nie należy łączyć wejściowego zestawu danych.* Zamiast tego dodaj zastosuj [moduł Transformacja](apply-transformation.md) i połącz dane wyjściowe transformacji wyboru operacji.

   > [!IMPORTANT]
   > Nie można oczekiwać zastosowania [wyboru funkcji opartych na filtrze](filter-based-feature-selection.md) do zestawu danych oceniania i uzyskania tych samych wyników. Ponieważ wybór operacji jest oparty na wartościach, może wybrać inny zestaw kolumn, co spowodowałoby niepowodzenie operacji oceniania.
7. Prześlij potok.

Ten proces zapisywania, a następnie stosowanie wyboru kolumny zapewnia, że ten sam schemat danych jest dostępny do szkolenia i oceniania.


## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
