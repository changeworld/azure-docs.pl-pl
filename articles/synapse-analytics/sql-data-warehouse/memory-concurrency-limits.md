---
title: Ograniczenia pamięci i współbieżności
description: Wyświetlanie limitów pamięci i współbieżności przydzielonych do różnych poziomów wydajności i klas zasobów w usłudze Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: c427c832eb613dddbff33ef6e67af63112e2f136
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586066"
---
# <a name="memory-and-concurrency-limits-for-azure-synapse-analytics"></a>Limity pamięci i współbieżności dla usługi Azure Synapse Analytics
Wyświetlanie limitów pamięci i współbieżności przydzielonych do różnych poziomów wydajności i klas zasobów w usłudze Azure Synapse Analytics.  

## <a name="data-warehouse-capacity-settings"></a>Ustawienia pojemności magazynu danych
W poniższych tabelach przedstawiono maksymalną pojemność magazynu danych na różnych poziomach wydajności. Aby zmienić poziom wydajności, zobacz [Skalowanie obliczeń — portal](quickstart-scale-compute-portal.md).

### <a name="service-levels"></a>Poziomy usług

Poziomy usług wahają się od DW100c do DW30000c.

| Poziom wydajności | Węzły obliczeniowe | Dystrybucje na węzeł obliczeniowy | Pamięć na magazyn danych (GB) |
|:-----------------:|:-------------:|:------------------------------:|:------------------------------:|
| DW100c            | 1             | 60                             |    60                          |
| DW200c            | 1             | 60                             |   120                          |
| DW300c            | 1             | 60                             |   180                          |
| DW400c            | 1             | 60                             |   240                          |
| DW500c.            | 1             | 60                             |   300                          |
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

Maksymalny poziom usług to DW30000c, który ma 60 węzłów obliczeniowych i jedną dystrybucję na węzeł obliczeniowy. Na przykład magazyn danych o pojemności 600 TB w dw30000c przetwarza około 10 TB na węzeł obliczeniowy.

## <a name="concurrency-maximums-for-workload-groups"></a>Maksymalne wartości współbieżności dla grup obciążenia
Wraz z [wprowadzeniem grup obciążenia](sql-data-warehouse-workload-isolation.md)pojęcie gniazda współbieżności nie ma już zastosowania.  Zasoby na żądanie są przydzielane w procentach i określone w definicji grupy obciążenia.  Jednak nawet przy usuwaniu gniazd współbieżności istnieją minimalne ilości zasobów potrzebnych dla zapytań na podstawie poziomu usługi.  W poniższej tabeli zdefiniowano minimalną ilość zasobów potrzebnych na zapytanie między poziomami usług i skojarzoną współbieżność, którą można osiągnąć. 

|Poziom usług|Maksymalna liczba równoczesnych zapytań|Min % wspieranych dla REQUEST_MIN_RESOURCE_GRANT_PERCENT|
|---|---|---|
|DW100c|4|25%|
|DW200c|8|12.5%|
|DW300c|12|8%|
|DW400c|16|6.25%|
|DW500c.|20|5%|
|DW1000c|32|3%|
|DW1500c|32|3%|
|DW2000c|48|2%|
|DW2500c|48|2%|
|DW3000c|64|1.5%|
|DW5000c|64|1.5%|
|DW6000c|128|0,75%|
|DW7500c|128|0,75%|
|DW10000c|128|0,75%|
|DW15000c|128|0,75%|
|DW30000c|128|0,75%|
||||

## <a name="concurrency-maximums-for-resource-classes"></a>Maksymalne wartości współbieżności dla klas zasobów
Aby upewnić się, że każda kwerenda ma wystarczającą ilość zasobów do wykonania wydajnie, wykorzystanie zasobów jest śledzone przez przypisanie gniazd współbieżności do każdej kwerendy. System umieszcza zapytania w kolejce na podstawie znaczeń znaczeń i współbieżności. Kwerendy czekać w kolejce, aż dostępna jest wystarczająca liczba gniazd współbieżności. [Znaczenie](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-workload-importance) i współbieżność szczeliny określają priorytety procesora CPU. Aby uzyskać więcej informacji, zobacz [Analizowanie obciążenia](analyze-your-workload.md)

**Statyczne klasy zasobów**

W poniższej tabeli przedstawiono maksymalną liczbę równoczesnych zapytań i gniazd współbieżności dla każdej [statycznej klasy zasobów](resource-classes-for-workload-management.md).  

| Poziom usług | Maksymalna liczba równoczesnych zapytań | Dostępne gniazda współbieżności | Szczeliny używane przez staticrc10 | Szczeliny używane przez staticrc20 | Szczeliny używane przez staticrc30 | Szczeliny używane przez staticrc40 | Szczeliny używane przez staticrc50 | Szczeliny używane przez staticrc60 | Szczeliny używane przez staticrc70 | Szczeliny używane przez staticrc80 |
|:-------------:|:--------------------------:|:---------------------------:|:---------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|:----------:|
| DW100c        |  4                         |    4                        | 1         | 2          | 4          | 4          | 4         |  4         |  4         |  4         |
| DW200c        |  8                         |    8                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |  8        |
| DW300c        | 12                         |   12                        | 1         | 2          | 4          | 8          |  8         |  8         |  8         |   8        |
| DW400c        | 16                         |   16                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
| DW500c.        | 20                         |   20                        | 1         | 2          | 4          | 8          | 16         | 16         | 16         |  16        |
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

**Dynamiczne klasy zasobów**

W poniższej tabeli przedstawiono maksymalną liczbę równoczesnych zapytań i gniazd współbieżności dla każdej [klasy zasobów dynamicznych](resource-classes-for-workload-management.md). Dynamiczne klasy zasobów używają alokacji procentowej pamięci 3-10-22-70 dla małych i średnich i dużych klas zasobów na wszystkich poziomach usług.

| Poziom usług | Maksymalna liczba równoczesnych zapytań | Dostępne gniazda współbieżności | Szczeliny używane przez smallrc | Szczeliny używane przez mediumrc | Szczeliny używane przez largerc | Szczeliny używane przez xlargerc |
|:-------------:|:--------------------------:|:---------------------------:|:---------------------:|:----------------------:|:---------------------:|:----------------------:|
| DW100c        |  4                         |    4                        | 1                     |  1                     |  1                    |   2                    |
| DW200c        |  8                         |    8                        | 1                     |  1                     |  1                    |   5                    |
| DW300c        | 12                         |   12                        | 1                     |  1                     |  2                    |   8                    |
| DW400c        | 16                         |   16                        | 1                     |  1                     |  3                    |  11                    |
| DW500c.        | 20                         |   20                        | 1                     |  2                     |  4                    |  14                    |
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


Gdy nie ma wystarczającej liczby gniazd współbieżności wolne do rozpoczęcia wykonywania kwerendy, kwerendy są umieszczane w kolejce i wykonywane na podstawie ważności.  Jeśli istnieje równoważne znaczenie, kwerendy są wykonywane na zasadzie pierwszego w, pierwszego wyjścia.  Po zakończeniu kwerendy i liczba zapytań i gniazd spadnie poniżej limitów, USŁUGA SQL Data Warehouse zwalnia zapytania w kolejce. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat wykorzystania klas zasobów w celu dalszej optymalizacji obciążenia, zapoznaj się z następującymi artykułami:
* [Klasy zasobów do zarządzania obciążeniem](resource-classes-for-workload-management.md)
* [Analizowanie obciążenia](analyze-your-workload.md)