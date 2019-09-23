---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2019
ms.locfileid: "71180064"
---
#### <a name="additional-premium-file-share-level-limits"></a>Dodatkowe limity poziomów udziałów plików w warstwie Premium

|Obszar  |Cel  |
|---------|---------|
|Minimalny wzrost/zmniejszenie rozmiaru    |1 GiB      |
|Liczba operacji we/wy na sekundę punktu odniesienia    |1 Liczba operacji we/wy na sekundę, do 100 000|
|Rozerwanie operacji we/wy    |3 razy dziennie operacji we/wy na GiB, do 100 000|
|Szybkość ruchu wychodzącego         |60 MiB/s + 0,06 * GiB aprowizacji        |
|Szybkość transferu danych przychodzących| 40 MiB/s + 0,04 * GiB aprowizacji |

#### <a name="file-level-limits"></a>Limity poziomów plików

|Obszar  |Plik Premium  |Plik standardowy |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Maksymalna liczba operacji we/wy na plik     |5,000         |1000|
|Równoczesne uchwyty    |2000         |2000|
|Ruch wychodzący  |300 MiB/s|      Zobacz standardowe wartości przepływności plików|
|Ruch przychodzący  |200 MiB/s| Zobacz standardowe wartości przepływności plików|
|Przepływność| Zobacz wartości wejściowe/wyjściowe plików w warstwie Premium| Do 60 MiB/s|
