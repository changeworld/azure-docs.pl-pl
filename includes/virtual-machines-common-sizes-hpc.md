---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 07/06/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 16e2a9cfbd9f08428fddade290117b27bc3401f7
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44369290"
---
Maszyn wirtualnych serii H są najnowsze o wysokiej wydajności obliczeniowej zmierzające do obsługi obciążeń, takich jak przetwarzanie wsadowe, analizy, modelowanie molekularne i analizy dynamiki płynów maszyn wirtualnych. Te 8 i 16 procesorów wirtualnych maszyny wirtualne są zbudowane na Intel Haswell E5-2667 V3 procesora technologii są w pamięć DDR4 i oparte na dyskach SSD magazynu tymczasowego. 

Seria H oferuje, obok znacznej mocy procesora CPU, różnorodne opcje dla sieci obsługujących technologię RDMA i niskie opóźnienia, korzystając z sieci InfiniBand o przepustowości FDR wraz z kilkoma konfiguracjami pamięci do obsługi obliczeń wymagających znacznego wykorzystania pamięci.



## <a name="h-series"></a>Seria H

ACU: 290–300

Usługa Premium Storage: Nieobsługiwane

Buforowanie Premium Storage: Nieobsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Maks. liczba dysków danych | Maksymalna przepływność dysków: liczba operacji we/wy na sekundę | Maksymalna liczba kart sieciowych |
| --- | --- | --- | --- | --- | --- | --- |
| Standardowa_H8 |8 |56 |1000 |32 |32 x 500 |2  |
| Standardowa_H16 |16 |112 |2000 |64 |64 x 500 |4 |
| Standardowa_H8m |8 |112 |1000 |32 |32 x 500 |2  |
| Standardowa_H16m |16 |224 |2000 |64 |64 x 500 |4  |
| Maszyna wirtualna standard_h16r — <sup>1</sup> |16 |112 |2000 |64 |64 x 500 |4  |
| Maszyna wirtualna standard_h16mr — <sup>1</sup> |16 |224 |2000 |64 |64 x 500 |4 |

<sup>1</sup> w przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączane przez sieć FDR InfiniBand, która zapewnia bardzo ultraniskie opóźnienia i wysoką przepustowość.

<br>






