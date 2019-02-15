---
title: Usługi Azure Data Factory danych przepływ sortowania transformacji
description: Usługi Azure Data Factory danych sortowania sprzężenia transformacji
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 55cd303399d34eecd8f787e1e5af09c5d904fb44
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2019
ms.locfileid: "56272113"
---
# <a name="azure-data-factory-data-sort-transformations"></a>Przekształcenia sortowania danych fabryki danych platformy Azure

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Ustawienia sortowania](media/data-flow/sort.png "sortowania")

Przekształcenie sortowania umożliwia sortowanie przychodzących wierszy, na podstawie bieżącego strumienia danych. Wychodzące wiersze z transformacji sortowania następnie postępuj zgodnie z ustawionymi zasadami szeregowania. Można wybrać poszczególnych kolumn i posortować je ASC lub gru, za pomocą wskaźnika strzałkę obok każdego pola. Jeśli trzeba zmodyfikować kolumny przed zastosowaniem sortowania, kliknij "Obliczane kolumny", aby uruchomić edytora wyrażeń. Zapewni to możliwość tworzenia wyrażenia dla operacji sortowania, zamiast prostego zastosowania kolumny do sortowania.

Można włączyć na "Bez uwzględniania wielkości liter", jeśli chcesz ignorowana w przypadku pól ciągów lub tekst sortowania.

Partycjonowanie danych Spark korzysta z "Sort tylko w ramach partycji". Sortując dane przychodzące, tylko w ramach każdej partycji, przepływu danych można sortować danych podzielonych na partycje, zamiast sortowanie strumienia danych.

Każdy warunek sortowania w transformacji sortowania można zmieniać. Więc jeśli potrzebujesz przenieść kolumny wyższy priorytet sortowania, Pobierz tego wiersza przy użyciu myszy i przenieś ją wyższą lub niższą liście sortowania.

Wpływ partycjonowania na sortowania

Przepływ danych usługi ADF jest wykonywane na danych big data klastry Spark przy użyciu danych rozproszonych w wielu partycji i węzłów. Należy pamiętać, to podczas tworzenia architektury przepływu danych, jeśli zależy transformacji sortowania do przechowywania danych w tej samej kolejności. Jeśli zdecydujesz się na partycje dane w kolejne przekształcenie, możesz utracić sortowanie z powodu losowego grupowania danych.
