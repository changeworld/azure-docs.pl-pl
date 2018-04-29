---
title: Limity pamięci i - Azure SQL Data Warehouse | Dokumentacja firmy Microsoft
description: Wyświetl limitów pamięci i przydzielone do różnych poziomów wydajności i klasy zasobu w usłudze Azure SQL Data Warehouse.
services: sql-data-warehouse
author: ronortloff
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 04/17/2018
ms.author: rortloff
ms.reviewer: igorstan
ms.openlocfilehash: 4e6e95e8601e7ab8b836e2aa1aa21ef4d5779954
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2018
---
# <a name="memory-and-concurrency-limits-for-azure-sql-data-warehouse"></a>Limity pamięci i magazyn danych SQL Azure
Wyświetl limitów pamięci i przydzielone do różnych poziomów wydajności i klasy zasobu w usłudze Azure SQL Data Warehouse. Aby uzyskać więcej informacji i dotyczą te możliwości planu zarządzania obciążenia, zobacz [klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md). 

## <a name="performance-tiers"></a>Warstwy wydajności

Magazyn danych SQL oferuje dwie warstwy wydajności, które są zoptymalizowane pod kątem obciążeń analitycznych. Warstwę wydajności to opcja, która określa konfigurację magazynu danych. Ta opcja jest jedną z opcji pierwszy wprowadzone podczas tworzenia magazynu danych.  

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T140/player]

- **Warstwa wydajności zoptymalizowana pod kątem elastyczności** oddziela warstwy obliczeń i magazynu w architekturze. Ta opcja najlepiej działa dla obciążeń, które mogą w pełni korzystać z rozdzielenia obliczeń i magazynu dzięki częstemu skalowaniu w celu obsługi krótkich okresów szczytowej aktywności. Ta warstwa obliczeń ma najniższą cenę wejściową i możliwość skalowania do obsługi większości obciążeń klientów.

- **Warstwa wydajności zoptymalizowana pod kątem obliczeń** wykorzystuje najnowszy sprzęt platformy Azure do wprowadzania nowej pamięci podręcznej NVMe Solid State Disk, która przechowuje dane, do których najczęściej następuje dostęp, w pobliżu procesorów CPU, co jest dokładnie tym, czego potrzebujesz. Dzięki automatycznemu podziałowi magazynu na warstwy ta warstwa wydajności najlepiej działa dla kompleksowych zapytań, ponieważ wszystkie operacje We/Wy są przechowywane lokalnie względem warstwy obliczeń. Ponadto magazyn kolumn jest rozszerzony w celu przechowywania nieograniczonej ilości danych w usłudze SQL Data Warehouse. Warstwa wydajności zoptymalizowana pod kątem obliczeń zapewnia najwyższy poziom skalowalności, dzięki któremu można skalować do 30 000 jednostek obliczeniowych magazynu danych (cDWU). Wybierz tę warstwę dla obciążeń, które ciągle wymagają niezwykle wysokiej wydajności.

## <a name="data-warehouse-limits"></a>Limity magazynu danych
Maksymalna pojemność magazynu danych na różne poziomy wydajności można znaleźć w poniższych tabelach. Aby zmienić poziom wydajności, zobacz [skalowanie możliwości obliczeniowych - portalu](quickstart-scale-compute-portal.md).

### <a name="optimized-for-elasticity"></a>Zoptymalizowane pod kątem elastyczności

Poziomy usług dla zoptymalizowany pod kątem elastyczność wydajności warstwy należą do zakresu od DW100 do DW6000. 

| Poziom wydajności | Maksymalna liczba równoczesnych zapytań | Węzły obliczeniowe | Dystrybucje w każdym węźle obliczeń | Maksymalna ilość pamięci, na dystrybucji (MB) | Maksymalna ilość pamięci, na magazynie danych (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW100         | 4                      | 1             | 60                             | 400                              |  24                                |
| DW200         | 8                      | 2             | 30                             | 800                              |  48                                |
| DW300         | 12                     | 3             | 20                             | 1,200                            |  72                                |
| DW400         | 16                     | 4             | 15                             | 1,600                            |  96                                |
| DW500         | 20                     | 5             | 12                             | 2,000                            | 120                                |
| DW600         | 24                     | 6             | 10                             | 2,400                            | 144                                |
| DW1000        | 32                     | 10            | 6                              | 4,000                            | 240                                |
| DW1200        | 32                     | 12            | 5                              | 4,800                            | 288                                |
| DW1500        | 32                     | 15            | 4                              | 6,000                            | 360                                |
| DW2000        | 32                     | 20            | 3                              | 8000                            | 480                                |
| DW3000        | 32                     | 30            | 2                              | 12,000                           | 720                                |
| DW6000        | 32                     | 60            | 1                              | 24,000                           | 1440                               |

### <a name="optimized-for-compute"></a>Zoptymalizowane pod kątem obliczeń

Zoptymalizowane dla warstwy wydajności obliczeniowej zapewnia 2,5 x większa ilość pamięci na zapytanie niż zoptymalizowane dla warstwy wydajności elastyczność. Ta dodatkowa pamięć pomaga zoptymalizowana dla warstwy wydajności obliczeniowej dostarczania jego duża wydajność.  Poziomy wydajności zoptymalizowany pod kątem obliczeń wydajności warstwy należą do zakresu od DW1000c do DW30000c. 

| Poziom wydajności | Maksymalna liczba równoczesnych zapytań | Węzły obliczeniowe | Dystrybucje w każdym węźle obliczeń | Maksymalna ilość pamięci, na dystrybucji (GB) | Maksymalna ilość pamięci, na magazynie danych (GB) |
|:-------------:|:----------------------:|:-------------:|:------------------------------:|:--------------------------------:|:----------------------------------:|
| DW1000c       | 32                     | 2             | 30                             |  10                              |   600                              |
| DW1500c       | 32                     | 3             | 20                             |  15                              |   900                              |
| DW2000c       | 32                     | 4             | 15                             |  20                              |  1200                              |
| DW2500c       | 32                     | 5             | 12                             |  25                              |  1500                              |
| DW3000c       | 32                     | 6             | 10                             |  30                              |  1800                              |
| DW5000c       | 32                     | 10            | 6                              |  50                              |  3000                              |
| DW6000c       | 32                     | 12            | 5                              |  60                              |  3600                              |
| DW7500c       | 32                     | 15            | 4                              |  75                              |  4500                              |
| DW10000c      | 32                     | 20            | 3                              | 100                              |  6000                              |
| DW15000c      | 32                     | 30            | 2                              | 150                              |  9000                              |
| DW30000c      | 32                     | 60            | 1                              | 300                              | 18000                              |

Maksymalna cDWU jest DW30000c, który ma 60 węzłów obliczeniowych i jeden dystrybucji na węźle obliczeń. Na przykład 600 TB magazynu danych na DW30000c przetwarza około 10 TB na każdym węźle obliczeń.


## <a name="concurrency-maximums"></a>Maksymalne wartości współbieżności
Aby upewnić się, że każda kwerenda ma za mało zasobów do wykonania, SQL Data Warehouse ścieżki obliczeniowe wykorzystania zasobów przypisując współbieżności miejsc do każdego zapytania. System umieszcza zapytania kolejki gdzie poczekaj aż do wystarczającej ilości [miejsc współbieżności](resource-classes-for-workload-management.md#concurrency-slots) są dostępne. 

Współbieżność miejsc również określić priorytety procesora CPU. Aby uzyskać więcej informacji, zobacz [analizowanie obciążenia](analyze-your-workload.md)

### <a name="optimized-for-compute"></a>Zoptymalizowane pod kątem obliczeń
W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy zasobu dynamicznego](resource-classes-for-workload-management.md). Te dotyczą zoptymalizowane dla warstwy wydajności obliczeniowej.

**Klasy zasobu dynamicznego**
| Poziom wydajności | Maksymalna liczba jednoczesnych kwerend | Współbieżność dostępnych | Używane przez smallrc gniazd | Używane przez mediumrc gniazd | Używane przez largerc gniazd | Używane przez xlargerc gniazd |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW1000c       | 32                         |   40                        | 1                     |  8                     |  16                   |  32                    |
| DW1500c       | 32                         |   60                        | 1                     |  8                     |  16                   |  32                    |
| DW2000c       | 32                         |   80                        | 1                     | 16                     |  32                   |  64                    |
| DW2500c       | 32                         |  100                        | 1                     | 16                     |  32                   |  64                    |
| DW3000c       | 32                         |  120                        | 1                     | 16                     |  32                   |  64                    |
| DW5000c       | 32                         |  200                        | 1                     | 32                     |  64                   | 128                    |
| DW6000c       | 32                         |  240                        | 1                     | 32                     |  64                   | 128                    |
| DW7500c       | 32                         |  300                        | 1                     | 64                     | 128                   | 128                    |
| DW10000c      | 32                         |  400                        | 1                     | 64                     | 128                   | 256                    |
| DW15000c      | 32                         |  600                        | 1                     | 64                     | 128                   | 256                    |
| DW30000c      | 32                         | 1200                        | 1                     | 64                     | 128                   | 256                    |

**Klasy statyczne zasobów**

W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy statyczne zasobów](resource-classes-for-workload-management.md).  

| Poziom usług | Maksymalna liczba jednoczesnych kwerend | Współbieżność dostępnych |staticrc10 | staticrc20 | staticrc30 | staticrc40 | staticrc50 | staticrc60 | staticrc70 | staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW1000c       | 32                         |   40                        | 1         | 2          | 4          | 8          | 16         | 32         | 32         |  32        |
| DW1500c       | 32                         |   60                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2000c       | 32                         |   80                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW2500c       | 32                         |  100                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000c       | 32                         |  120                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW5000c       | 32                         |  200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW6000c       | 32                         |  240                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW7500c       | 32                         |  300                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW10000c      | 32                         |  400                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW15000c      | 32                         |  600                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |
| DW30000c      | 32                         | 1200                        | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

### <a name="optimized-for-elasticity"></a>Zoptymalizowane pod kątem elastyczności
W poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy zasobu dynamicznego](resource-classes-for-workload-management.md).  Te dotyczą zoptymalizowane dla warstwy wydajności elastyczność.

**Klasy zasobu dynamicznego**

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
| DW2000        | 32                         |  80                         | 1       | 16       | 32      |  64      |
| DW3000        | 32                         | 120                         | 1       | 16       | 32      |  64      |
| DW6000        | 32                         | 240                         | 1       | 32       | 64      | 128      |

**Klasy statyczne zasobów** w poniższej tabeli przedstawiono maksymalna liczba jednoczesnych kwerend i gniazda współbieżności dla każdego [klasy statyczne zasobów](resource-classes-for-workload-management.md).  Te dotyczą zoptymalizowane dla warstwy wydajności elastyczność.

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
| DW2000        | 32                         |  80                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW3000        | 32                         | 120                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         |  64        |
| DW6000        | 32                         | 240                       | 1         | 2          | 4          | 8          | 16         | 32         | 64         | 128        |

Po spełnieniu jednego z tych progów, nowego zapytania są umieszczone w kolejce i wykonywane na podstawie FIFO pierwszy w.  Kończy zapytania i liczba zapytań i gniazda spadną poniżej limitów, SQL Data Warehouse zwalnia umieszczonych w kolejce zapytań. 

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej o tym, jak skorzystać z klasy zasobów w celu zoptymalizowania dodatkowe obciążenie Przejrzyj następujące artykuły:
* [Klasy zasobów do zarządzania obciążenia](resource-classes-for-workload-management.md)
* [Analiza obciążenia](analyze-your-workload.md)

