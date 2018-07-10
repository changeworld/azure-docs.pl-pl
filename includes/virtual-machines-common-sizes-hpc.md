---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 05/22/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: 36d59638984c31a98067b95fded9626f31a7f278
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2018
ms.locfileid: "34669357"
---
Maszyn wirtualnych serii H są najnowsze obliczenia o wysokiej wydajności, że w przypadku najwyższych potrzeb obliczeniowych, takich jak modelowanie molekularne i analizy obliczeniowe dynamiki płynów, której jest maszyn wirtualnych. Te 8 i 16 procesorów wirtualnych maszyny wirtualne są zbudowane na Intel Haswell E5-2667 V3 procesora technologii są w pamięć DDR4 i oparte na dyskach SSD magazynu tymczasowego. 

Seria H oferuje, obok znacznej mocy procesora CPU, różnorodne opcje dla sieci obsługujących technologię RDMA i niskie opóźnienia, korzystając z sieci InfiniBand o przepustowości FDR wraz z kilkoma konfiguracjami pamięci do obsługi obliczeń wymagających znacznego wykorzystania pamięci.



## <a name="h-series"></a>Seria H

ACU: 290–300

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






