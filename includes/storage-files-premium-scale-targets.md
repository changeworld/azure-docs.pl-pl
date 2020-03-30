---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: b28427b3ede0cfaeb9e08d3c73b15ea7f2961f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71180064"
---
#### <a name="additional-premium-file-share-level-limits"></a>Dodatkowe limity poziomu udziału plików premium

|Obszar  |Środowisko docelowe  |
|---------|---------|
|Minimalny wzrost/spadek rozmiaru    |1 Gib      |
|Bazowe we/wy    |1 IOPS na GiB, do 100 000|
|Rozerwanie we/wy    |3x IOPS na GiB, do 100 000|
|Szybkość ruchu wychodzącego         |60 MiB/s + 0,06 * Aprowizowana GiB        |
|Wskaźnik transferu wnikacza| 40 MiB/s + 0,04 * Aprowizowana GiB |

#### <a name="file-level-limits"></a>Limity poziomów plików

|Obszar  |Plik Premium  |Plik standardowy |
|---------|---------|---------|
|Rozmiar                  |1 TiB         |1 TiB|
|Maksymalna liczba IOPS na plik     |5000         |1000|
|Dojście współbieżne    |2000         |2000|
|Ruch wychodzący  |300 MiB/s|      Zobacz standardowe wartości przepływności plików|
|Ruch przychodzący  |200 MiB/s| Zobacz standardowe wartości przepływności plików|
|Przepływność| Zobacz wartości transferu ruchu przychodzącego/wychodzącego pliku premium| Do 60 MiB/s|
