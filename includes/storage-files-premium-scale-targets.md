---
author: roygara
ms.service: storage
ms.topic: include
ms.date: 06/07/2019
ms.author: rogarana
ms.openlocfilehash: 368f08272173b019873dfe20e1164d6baf72ff5e
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67542660"
---
#### <a name="additional-premium-file-share-level-limits"></a>Dodatkowe premium limity poziomu udziału plików

|Obszar  |Cel  |
|---------|---------|
|Zwiększenie/zmniejszenie minimalnego rozmiaru    |1 GiB      |
|Liczba operacji we/wy na sekundę punktu odniesienia    |1 operacje We/Wy za GiB maksymalnie 100 000|
|Operacje We/Wy, przenoszenie obsługi dużego ruchu    |3 x IOPS za GiB maksymalnie 100 000|
|Współczynnik ruchu wychodzącego         |60 MiB/s i 0,06 * aprowizowane GiB        |
|Szybkość transferu danych przychodzących| 40 MiB/s i 0,04 * aprowizowane GiB |

#### <a name="file-level-limits"></a>Limity poziomu pliku

|Obszar  |Plik — wersja Premium  |Standardowy plik |
|---------|---------|---------|
|Size                  |1 TiB         |1 TiB|
|Maksymalna liczba operacji We/Wy na pliku     |5,000         |1000|
|Uchwyty współbieżne    |2000         |2000|
|Ruch przychodzący  |300 MiB/s|      Zobacz wartości przepływności standardowego pliku|
|Ruch wychodzący   |200 Mib/s| Zobacz wartości przepływności standardowego pliku|
|Przepływność| Zobacz wartości ruchem przychodzącym/wychodzącym pliku — Wersja premium| Maksymalnie 60 MiB/s|