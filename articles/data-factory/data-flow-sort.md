---
title: Mapowanie transformacji sortowania przepływu danych w usłudze Azure Data Factory
description: Mapowanie transformacji sortowania danych w usłudze Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: d0482d1081c16dc89e7371c4c33de9b2bb4e4c2e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61348393"
---
# <a name="azure-data-factory-data-flow-sort-transformations"></a>Usługa Azure Data Factory danych przepływu sortowania przekształcenia

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Ustawienia sortowania](media/data-flow/sort.png "sortowania")

Przekształcenie sortowania umożliwia sortowanie przychodzących wierszy, na podstawie bieżącego strumienia danych. Wychodzące wiersze z transformacji sortowania następnie postępuj zgodnie z ustawionymi zasadami szeregowania. Można wybrać poszczególnych kolumn i posortować je ASC lub gru, za pomocą wskaźnika strzałkę obok każdego pola. Jeśli trzeba zmodyfikować kolumny przed zastosowaniem sortowania, kliknij "Obliczane kolumny", aby uruchomić edytora wyrażeń. Zapewni to możliwość tworzenia wyrażenia dla operacji sortowania, zamiast prostego zastosowania kolumny do sortowania.

## <a name="case-insensitive"></a>Bez uwzględniania wielkości liter
Można włączyć na "Bez uwzględniania wielkości liter", jeśli chcesz ignorowana w przypadku pól ciągów lub tekst sortowania.

Partycjonowanie danych Spark korzysta z "Sort tylko w ramach partycji". Sortując dane przychodzące, tylko w ramach każdej partycji, przepływu danych można sortować danych podzielonych na partycje, zamiast sortowanie strumienia danych.

Każdy warunek sortowania w transformacji sortowania można zmieniać. Więc jeśli potrzebujesz przenieść kolumny wyższy priorytet sortowania, Pobierz tego wiersza przy użyciu myszy i przenieś ją wyższą lub niższą liście sortowania.

Wpływ partycjonowania na sortowania

Przepływ danych usługi ADF jest wykonywane na danych big data klastry Spark przy użyciu danych rozproszonych w wielu partycji i węzłów. Należy pamiętać, to podczas tworzenia architektury przepływu danych, jeśli zależy transformacji sortowania do przechowywania danych w tej samej kolejności. Jeśli zdecydujesz się na partycje dane w kolejne przekształcenie, możesz utracić sortowanie z powodu losowego grupowania danych.

## <a name="next-steps"></a>Kolejne kroki

Po sortowaniu, warto użyć [przekształcenie agregacji](data-flow-aggregate.md)
