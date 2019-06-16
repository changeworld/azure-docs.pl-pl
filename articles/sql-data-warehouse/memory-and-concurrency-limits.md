---
title: Limity pamięci i współbieżności w usłudze Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wyświetl limity pamięci i współbieżności, przydzielone do różnych poziomów wydajności i klasy zasobów w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: workload management
ms.date: 03/15/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 3bc1fef1842911e9b2cfb65b3c8cc72e4b615010
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66241269"
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limity pamięci i współbieżności dla usługi Azure SQL Data Warehouse
Wyświetl limity pamięci i współbieżności, przydzielone do różnych poziomów wydajności i klasy zasobów w usłudze Azure SQL Data Warehouse. Aby uzyskać więcej informacji i Zastosuj te możliwości do planu zarządzania obciążenia, zobacz [klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md). 

Obecnie istnieją dwie generacje z usługą SQL Data Warehouse — Gen1 i Gen2. Zaleca się, że wykorzystać Gen2 SQL Data Warehouse w celu uzyskania najlepszej wydajności dla obciążenia magazynu danych. Gen2 wprowadza nowej pamięci podręcznej NVMe Solid State Disk, który utrzymuje często używanych danych w pobliżu procesorów CPU. Spowoduje to usunięcie zdalnego operacji We/Wy o znacznym wykorzystaniu i najbardziej wymagających obciążeń. Oprócz wydajności Gen2 oferuje najwyższy poziom skali, dzięki któremu można skalować do 30 000 jednostek magazynu danych i zapewniając nieograniczony magazyn kolumnowy. Firma Microsoft nadal obsługuje poprzedniej generacji (Gen1) usługi SQL Data Warehouse i zachować te same funkcje; Jednak firma Microsoft zachęca do [Przeprowadź uaktualnienie do Gen2](upgrade-to-latest-generation.md) przy najbliższej sposobności. 

## <a name="data-warehouse-capacity-settings"></a>Ustawienia pojemności magazynu danych
W poniższej tabeli przedstawiono maksymalną pojemność magazynu danych na różne poziomy wydajności. Aby zmienić poziom wydajności, zobacz [skalowanie zasobów obliczeniowych — portal](quickstart-scale-compute-portal.md).

### <a name="gen2"></a>Gen2

Gen2 — zapewnia 2,5 x większa ilość pamięci na zapytanie niż Gen1. Ta dodatkowa pamięć pomaga Gen2 dostarczać jego wysoka wydajność.  Poziomy wydajności dla zakresu Gen2 od DW100c do DW30000c. 

| Poziom wydajności | Węzły obliczeniowe | Dystrybucje na węzeł obliczeniowy | Ilość pamięci na magazyn danych (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c            | 1             | 60                             |   300                          |
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

Maksymalna jednostka DWU Gen2 jest DW30000c, która ma 60 węzłów obliczeniowych i jednym dystrybucję na węźle obliczeniowym. Na przykład 600 TB magazynu danych na DW30000c przetwarza około 10 TB na każdym węźle obliczeniowym.

### <a name="gen1"></a>Gen1

Poziomy usług Gen1 należą do zakresu od DW100 do DW6000. 

| Poziom wydajności | Węzły obliczeniowe | Dystrybucje na węzeł obliczeniowy | Ilość pamięci na magazyn danych (GB) |
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
Aby upewnić się, że każde zapytanie ma za mało zasobów do wydajnego wykonywania, usługa SQL Data Warehouse śledzi wykorzystanie zasobów przez przypisywanie gniazd współbieżności każda kwerenda. System umieszcza zapytania do kolejki, w oparciu o ważności oraz liczby gniazd współbieżności. Zapytania oczekiwać w kolejce do momentu wystarczającej liczby gniazd współbieżności są dostępne. [Znaczenie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) i gniazd współbieżności określić priorytet procesora CPU. Aby uzyskać więcej informacji, zobacz [analizowanie obciążenia](analyze-your-workload.md)

### <a name="gen2"></a>Gen2
 
**Statycznych klas zasobów**

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend oraz liczby gniazd współbieżności dla każdego [klasy zasobów statycznych](resource-classes-for-workload-management.md).  

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Dostępnych gniazd współbieżności | Używane przez staticrc10 gniazd | Używane przez staticrc20 gniazd | Używane przez staticrc30 gniazd | Używane przez staticrc40 gniazd | Używane przez staticrc50 gniazd | Używane przez staticrc60 gniazd | Używane przez staticrc70 gniazd | Używane przez staticrc80 gniazd |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW2000c       | 48                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 48                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 64                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW5000c       | 64                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 128                        |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 128                        |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 128                        |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 128                        |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 128                        | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

**Zasób dynamiczny klas**

> [!NOTE]
> Klasa zasobów smallrc na Gen2 dynamicznie dodaje pamięć zwiększa poziom usług, i obsługuje tylko 32 maksymalna liczba jednoczesnych kwerend DW1000c oraz 4 i DW100c.  Po wystąpieniu skalowania poza DW1500c gniazd współbieżności i użycie pamięci przez smallrc zwiększa się wraz ze wzrostem poziomu usługi. 
>
>

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend oraz liczby gniazd współbieżności dla każdego [dynamicznej klasy zasobów](resource-classes-for-workload-management.md). W odróżnieniu od Gen1 klasach zasobów dynamicznych na Gen2 są naprawdę dynamiczne.  Gen2 używa alokacji 3-10-22 — 70 procent pamięci dla klasy małych medium dużych xlarge zasobów we wszystkich poziomów usług.

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Dostępnych gniazd współbieżności | Używane przez smallrc gniazd | Używane przez mediumrc gniazd | Używane przez largerc gniazd | Gniazda posługują się xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
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



#### <a name="gen1"></a>Gen1

Statycznych klas zasobów

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend oraz liczby gniazd współbieżności dla każdego [klasy zasobów statycznych](resource-classes-for-workload-management.md) na **Gen1**.

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Gniazd współbieżności maksymalną | Używane przez staticrc10 gniazd | Używane przez staticrc20 gniazd | Używane przez staticrc30 gniazd | Używane przez staticrc40 gniazd | Używane przez staticrc50 gniazd | Używane przez staticrc60 gniazd | Używane przez staticrc70 gniazd | Używane przez staticrc80 gniazd |
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

Zasób dynamiczny klas
> [!NOTE]
> Klasa zasobów smallrc na Gen1 przydziela stałą ilość pamięci na zapytanie, w podobny sposób do staticrc10 klasy zasobów statycznych.  Ponieważ smallrc są statyczne, ma możliwość skalowania do nawet 128 zapytań jednoczesnych. 
>
>

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend oraz liczby gniazd współbieżności dla każdego [dynamicznej klasy zasobów](resource-classes-for-workload-management.md) na **Gen1**.

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Dostępnych gniazd współbieżności | Używane przez smallrc gniazd | Używane przez mediumrc gniazd | Używane przez largerc gniazd | Gniazda posługują się xlargerc |
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


Po spełnieniu jednego z tych progów nowych zapytań są umieszczane w kolejce i są stosowane na podstawie first-out pierwszy w.  Zgodnie z zakończeniem zapytania i liczby zapytań i gniazd dzielą się poniżej granic, usługa SQL Data Warehouse zwalnia umieszczonych w kolejce zapytań. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak korzystać z klasy zasobów na potrzeby optymalizacji dalsze obciążenia sprawdź następujące artykuły:
* [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md)
* [Analizowanie obciążenia](analyze-your-workload.md)

