---
title: Transformacja sortowania przepływu danych mapowania
description: Transformacja sortowania danych mapowania danych usługi Azure
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/08/2018
ms.openlocfilehash: c09439c5f54ae4b0884e9e25ae9a5a488f935bac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930221"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Przekształcenia sortowania przepływu danych w fabryce danych platformy Azure



![Ustawienia sortowania](media/data-flow/sort.png "Sortowanie")

Transformacja sortowania umożliwia sortowanie przychodzących wierszy w bieżącym strumieniu danych. Wiersze wychodzące z transformacji sortowania będą następnie zgodne z ustawionymi regułami porządkowania. Można wybrać poszczególne kolumny i posortować je asc lub DEC, używając wskaźnika strzałki obok każdego pola. Jeśli musisz zmodyfikować kolumnę przed zastosowaniem sortowania, kliknij "Obliczone kolumny", aby uruchomić edytor wyrażeń. Zapewni to możliwość utworzenia wyrażenia dla operacji sortowania zamiast po prostu stosowania kolumny dla sortowania.

## <a name="case-insensitive"></a>Bez uwzględniania wielkości liter
Możesz włączyć "Bez uwzględniania wielkości liter", jeśli chcesz zignorować przypadek podczas sortowania ciągów lub pól tekstowych.

"Sortuj tylko w obrębie partycji" wykorzystuje partycjonowanie danych Platformy Spark. Sortując dane przychodzące tylko w obrębie każdej partycji, przepływy danych mogą sortować dane na partycje zamiast sortować cały strumień danych.

Każdy z warunków sortowania w transformacji sortowania można zmienić. Jeśli więc chcesz przenieść kolumnę wyżej w priorytecie sortowania, chwyć ten wiersz za pomocą myszy i przesuń go wyżej lub niżej na liście sortowania.

Efekty partycjonowania na sortowaniu

Przepływ danych usługi ADF jest wykonywany na klastrach platformy Spark dużych zbiorów danych z danymi rozproszonymi w wielu węzłach i partycjach. Ważne jest, aby pamiętać o tym podczas owego egoarniania przepływu danych, jeśli jesteś w zależności od transformacji Sortowanie, aby zachować dane w tej samej kolejności. Jeśli zdecydujesz się na partycjonowanie danych w kolejnej transformacji, możesz utracić sortowanie z powodu przetasowania danych.

## <a name="next-steps"></a>Następne kroki

Po sortowaniu można użyć [agregacji transformacji](data-flow-aggregate.md)
