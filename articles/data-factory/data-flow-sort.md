---
title: Przekształcenie sortowania Azure Data Factory mapowanie przepływu danych
description: Przekształcenie sortowania Azure Data Factory danych mapowania
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: f9d2699c870de21daea9ce421c79f089c04b99fa
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029120"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Przekształcenia sortowania przepływu danych Azure Data Factory



![](media/data-flow/sort.png "Sortuj") ustawienia sortowania

Transformacja sortowania pozwala sortować wiersze przychodzące w bieżącym strumieniu danych. Wiersze wychodzące z transformacji sortowania będą następnie zgodne z ustawionymi regułami kolejności. Można wybrać poszczególne kolumny i posortować je ASC lub gru przy użyciu wskaźnika strzałki obok każdego pola. Jeśli musisz zmodyfikować kolumnę przed zastosowaniem sortowania, kliknij pozycję "kolumny obliczane", aby uruchomić Edytor wyrażeń. Dzięki temu można utworzyć wyrażenie dla operacji sortowania, zamiast po prostu zastosować kolumnę sortowania.

## <a name="case-insensitive"></a>Bez uwzględniania wielkości liter
Możesz włączyć opcję "bez uwzględniania wielkości liter", jeśli chcesz zignorować wielkość liter podczas sortowania pól ciągów lub tekstowych.

"Sortuj tylko w obrębie partycji" wykorzystuje Partycjonowanie danych Spark. Sortując dane przychodzące tylko w ramach każdej partycji, przepływy danych mogą sortować dane podzielone na partycje zamiast sortować cały strumień danych.

Każdy warunek sortowania w transformacji sortowania można zmienić. Dlatego jeśli chcesz przenieść kolumnę powyżej pierwszeństwa sortowania, Przechwyć ten wiersz z myszą i przenieś go w górę lub w dół na liście sortowania.

Partycjonowanie efektów podczas sortowania

Przepływ danych ADF jest wykonywany w klastrach danych Big Data platformy Spark z danymi dystrybuowanymi między wieloma węzłami i partycjami. Należy pamiętać o tym, że podczas tworzenia architektury przepływu danych w zależności od przekształcenia sortowania w celu zachowania danych w tej samej kolejności. Jeśli zdecydujesz się na ponowne Partycjonowanie danych w kolejnej transformacji, możesz utracić sortowanie z powodu tego reshuffling danych.

## <a name="next-steps"></a>Następne kroki

Po sortowaniu możesz chcieć użyć [przekształcenia agregacji](data-flow-aggregate.md)
