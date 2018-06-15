---
title: Limity pamięci i - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wyświetl limitów pamięci i przydzielone do różnych poziomów wydajności i klasy zasobu w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 05/07/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 46d41e3ee85deb20f189bc9c82a255178f3d7eee
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942261"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limity pamięci i magazyn danych SQL Azure
Wyświetl limitów pamięci i przydzielone do różnych poziomów wydajności i klasy zasobu w usłudze Azure SQL Data Warehouse. Aby uzyskać więcej informacji i dotyczą te możliwości planu zarządzania obciążenia, zobacz [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md). 

Obecnie dostępne są dwie generacje z magazynu danych SQL — Gen1 i Gen2. Zaleca się wykorzystać Gen2 z usługi SQL Data Warehouse można uzyskać najlepszą wydajność dla obciążenia magazynu danych. Gen2 wprowadza nową pamięć podręczną NVMe pełny stan dysku podtrzymujące najczęściej używanych danych bliski procesorów CPU. Spowoduje to usunięcie zdalnego we/wy dla znacznym i najbardziej wymagających obciążeń. Oprócz wydajności Gen2 oferuje najwyższego poziomu skali, dzięki któremu można skalować do 30 000 jednostki magazynu danych i podając nieograniczony magazyn kolumnowym. Firma Microsoft będzie nadal obsługuje poprzedniej generacji (Gen1) usługi SQL Data Warehouse i zachować te same funkcje; Jednak firma Microsoft zachęca do [uaktualnienia do Gen2](upgrade-to-latest-generation.md) najwcześniejszą wygodne. 

## <a name="data-warehouse-capacity-settings"></a>Ustawienia pojemności magazynu danych
Maksymalna pojemność magazynu danych na różne poziomy wydajności można znaleźć w poniższych tabelach. Aby zmienić poziom wydajności, zobacz [skalowanie możliwości obliczeniowych - portalu](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Druga generacja

Gen2 zapewnia 2,5 x większa ilość pamięci na zapytanie niż Gen1. Ta dodatkowa pamięć pomaga Gen2 dostarczania jego duża wydajność.  Poziomy wydajności zakres Gen2 z DW1000c DW30000c. 

| Poziom wydajności | Węzły obliczeniowe | Dystrybucje w każdym węźle obliczeń | Ilość pamięci na magazynie danych (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW1000c           | 2             | 30                             |   600                          |
| DW1500c           | 3             | 20                             |   900                          |
| DW2000c           | 4             | 15                             |  1200                          |
| DW2500c           | 5             | 12                             |  1500                          |
| DW3000c           | 6             | 10                             |  1800                          |
| DW5000c           | 10            | 6                              |  3000                          |
| DW6000c           | 12            | 5                              |  3600                          |
| DW7500c           | 15            | 4                              |  4500                          |
| DW10000c          | 20            | 3                              |  6000                          |
| DW15000c          | 30            | 2                              |  9000                          |
| DW30000c          | 60            | 1                              | 18000                          |

Wartość maksymalna DWU Gen2 jest DW30000c, który ma 60 węzłów obliczeniowych i jeden dystrybucji na węźle obliczeń. Na przykład 600 TB magazynu danych na DW30000c przetwarza około 10 TB na każdym węźle obliczeń.

### <a name="gen1"></a>Pierwsza generacja

Poziomy usług Gen1 należą do zakresu od DW100 do DW6000. 

| Poziom wydajności | Węzły obliczeniowe | Dystrybucje w każdym węźle obliczeń | Ilość pamięci na magazynie danych (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100             | 1             | 60                             |  24                            |
| DW200             | 2             | 30                             |  48                            |
| DW300             | 3             | 20                             |  72                            |
| DW400             | 4             | 15                             |  96                            |
| DW500             | 5             | 12                             | 120                            |
| DW600             | 6             | 10                             | 144                            |
| DW1000            | 10            | 6                              | 240                            |
| DW1200            | 12            | 5                              | 288                            |
| DW1500            | 15            | 4                              | 360                            |
| DW2000            | 20            | 3                              | 480                            |
| DW3000            | 30            | 2                              | 720                            |
| DW6000            | 60            | 1                              | 1440                           |

## <a name="concurrency-maximums"></a>Maksymalne wartości współbieżności
Aby zapewnić, że każde zapytanie ma za mało zasobów do wykonania wydajnie, SQL Data Warehouse śledzi wykorzystania zasobów przypisując współbieżności miejsc do każdego zapytania. System umieszcza zapytania kolejki gdzie poczekaj aż do wystarczającej ilości [miejsc współbieżności](resource-classes-for-workload-management.md#concurrency-slots) są dostępne. Współbieżność miejsc również określić priorytety procesora CPU. Aby uzyskać więcej informacji, zobacz [analizowanie obciążenia](analyze-your-workload.md)

### <a name="gen2"></a>Druga generacja
 
**Klasy statyczne zasobów**

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy statyczne zasobów](resource-classes-for-workload-management.md).  

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Współbieżność dostępnych |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Klasy zasobu dynamicznego**

> [!NOTE]
> Klasa zasobów smallrc na Gen2 dynamicznie dodaje pamięci zgodnie z poziomu usługi zwiększa i obsługuje tylko maksymalny 32 zapytania jednoczesne.  Gniazda współbieżności i pamięci używanej przez zwiększa smallrc wraz ze wzrostem poziomu usługi. 
>
>

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy zasobu dynamicznego](resource-classes-for-workload-management.md). W odróżnieniu od Gen1 klasy zasobu dynamicznego na Gen2 są naprawdę dynamiczne.  Gen2 używa alokacji 3-10-22 — 70 procent pamięci dla klasy zasobu małych — średni — duże xlarge na wszystkich poziomów usług.

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Współbieżność dostępnych | Używane przez smallrc gniazd | Używane przez mediumrc gniazd | Używane przez largerc gniazd | Używane przez xlargerc gniazd |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  4                     |  8                    |  28                    |
| DW1500c       | 32                         |   60                        | 1                     |  6                     |  13                   |  42                    |
| DW2000c       | 32                         |   80                        | 2                     |  8                     |  17                   |  56                    |
| DW2500c       | 32                         |  100                        | 3                     | 10                     |  22                   |  70                    |
| DW3000c       | 32                         |  120                        | 3                     | 12                     |  26                   |  84                    |
| DW5000c       | 32                         |  200                        | 6                     | 20                     |  44                   | 140                    |
| DW6000c       | 32                         |  240                        | 7                     | 24                     |  52                   | 168                    |
| DW7500c       | 32                         |  300                        | 9                     | 30                     |  66                   | 210                    |
| DW10000c      | 32                         |  400                        | 12                    | 40                     |  88                   | 280                    |
| DW15000c      | 32                         |  600                        | 18                    | 60                     | 132                   | 420                    |
| DW30000c      | 32                         | 1200                        | 36                    | 120                    | 264                   | 840                    |



#### <a name="gen1"></a>Pierwsza generacja

Klasy statyczne zasobów

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy statyczne zasobów](resource-classes-for-workload-management.md) na **Gen1**.

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Maksymalna współbieżności gniazd |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:-------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100         | 4                          |   4                       | 1         | 2          | 4          | 4          |  4         |  4         |  4         |   4        |
| DW200         | 8                          |   8                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW300         | 12                         |  12                       | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400         | 16                         |  16                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500         | 20                         |  20                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW600         | 24                         |  24                       | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000        | 32                         |  40                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1200        | 32                         |  48                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500        | 32                         |  60                       | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000        | 48                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 64                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 128                        | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Klasy zasobu dynamicznego
> [!NOTE]
> Klasa zasobów smallrc na Gen1 przydziela stałej ilości pamięci dla kwerendy, w podobny sposób staticrc10 klasy statyczne zasobów.  Ponieważ smallrc jest statyczny, ma możliwość skalowania do 128 zapytania jednoczesne. 
>
>

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy zasobu dynamicznego](resource-classes-for-workload-management.md) na **Gen1**.

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Współbieżność dostępnych | smallrc | mediumrc | largerc | xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:-------:|:--------:|:-------:|:--------:|
| DW100         |  4                         |   4                         | 1       |  1       |  2      |   4      |
| DW200         |  8                         |   8                         | 1       |  2       |  4      |   8      |
| DW300         | 12                         |  12                         | 1       |  2       |  4      |   8      |
| DW400         | 16                         |  16                         | 1       |  4       |  8      |  16      |
| DW500         | 20                         |  20                         | 1       |  4       |  8      |  16      |
| DW600         | 24                         |  24                         | 1       |  4       |  8      |  16      |
| DW1000        | 32                         |  40                         | 1       |  8       | 16      |  32      |
| DW1200        | 32                         |  48                         | 1       |  8       | 16      |  32      |
| DW1500        | 32                         |  60                         | 1       |  8       | 16      |  32      |
| DW2000        | 48                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 64                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 128                        | 240                         | 1       | 32       | 64      | 128      |


Po spełnieniu jednego z tych progów, nowego zapytania są umieszczone w kolejce i wykonywane na podstawie FIFO pierwszy w.  Kończy zapytania i liczba zapytań i gniazda spadną poniżej limitów, SQL Data Warehouse zwalnia umieszczonych w kolejce zapytań. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak skorzystać z klasy zasobów w celu zoptymalizowania dodatkowe obciążenie Przejrzyj następujące artykuły:
* [Klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md)
* [Analiza obciążenia](analyze-your-workload.md)

