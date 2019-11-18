---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn;amverma
ms.custom: include file
ms.openlocfilehash: 489ac7fa37c10a27de971151f0be35c647d2186f
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116717"
---
Zoptymalizowane maszyny wirtualne platformy Azure HPC są przeznaczone do zapewniania wydajności klasy lidera, skalowalności MPI i opłacalności dla różnych aplikacji w świecie rzeczywistym.
 
## <a name="infiniband-networking-for-large-scale-hpc"></a>Sieci InfiniBand dla dużej skali HPC
Funkcja maszyn wirtualnych HBv2 200 GB/s — karta Mellanox HDR InfiniBand, podczas gdy maszyna wirtualna o wartości HB i HC z funkcją 100 GB/s Mellanox EDR InfiniBand. Każdy z tych typów maszyn wirtualnych jest podłączony do nieblokującego drzewa FAT w celu zoptymalizowanej i spójnej wydajności RDMA.

Maszyny wirtualne HBv2 obsługują Routing adaptacyjny oraz transport podłączony dynamicznie (DCT) w dodatkowych standardach do standardowych wersji RC i UD Transports. Te funkcje rozszerzają wydajność, skalowalność i spójność aplikacji, a ich użycie jest zdecydowanie zalecane.  

Maszyny wirtualne HBv2, HB i HC obsługują standardowe sterowniki Mellanox/OFED. W związku z tym obsługiwane są wszystkie czasowniki RDMA i typy MPI. Każdy z tych typów maszyn wirtualnych obsługuje również sprzętowe odciążanie MPI zbiory w celu zwiększenia wydajności aplikacji.
 
Oryginalne maszyny wirtualne serii H — funkcja 56 GB/s, karta Mellanox FDR InfiniBand. Aby korzystać z interfejsu InfiniBand w serii H, klienci muszą wdrażać przy użyciu oficjalnie obsługiwanych obrazów określonych dla tego typu maszyn wirtualnych w portalu Azure Marketplace. 


## <a name="hbv2-series"></a>Seria HBv2
Maszyny wirtualne z serii HBv2 są zoptymalizowane pod kątem aplikacji opartych na przepustowości pamięci, takich jak dynamika płynów, skończone analizy elementów i symulacja zbiornika. HBv2 maszyny wirtualne z funkcją 120 AMD EPYC 7742 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez jednoczesnego wielowątkowości. Każda maszyna wirtualna w HBv2 zapewnia do 340 GB/s przepustowości pamięci oraz do 4 teraflopa obliczeń FP64. 

Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs | 120 | PROCESOR AMD EPYC 7742 | 480 | 350 | 2.45 | 2.45 | 3.4 | 200 | Wszystkie | 480 + 960 | 8 | 1 |

<br>

## <a name="hb-series"></a>Seria HB
Maszyny wirtualne z serii HB są zoptymalizowane pod kątem zastosowań zależnych od przepustowości pamięci, takich jak dynamika płynów, jawna analiza elementów skończonych i modelowanie pogody. HB VM funkcja 60 AMD EPYC 7551 rdzeni procesora, 4 GB pamięci RAM na rdzeń procesora CPU i bez jednoczesnego wielowątkowości. Maszyna wirtualna HB oferuje do 260 GB/s przepustowości pamięci.  

ACU: 199-216

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Wszystkie | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Seria HC
Maszyny wirtualne z serii HC są zoptymalizowane pod kątem aplikacji opartych na gęstym obliczaniu, takich jak niejawne ograniczone analizy elementów, biocząsteczkowa i obliczeniowa. Maszyny wirtualne HC dysponują 44 rdzeniami procesorów Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora CPU i nie korzystają z hiperwątkowości. Platforma Intel Xeon Platinum obsługuje bogate ekosystemy narzędzi programistycznych firmy Intel, takich jak biblioteka jądra matematyczna firmy Intel. 

ACU: 297-315

Premium Storage: obsługiwane

Buforowanie Premium Storage: obsługiwane


| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych Ethernet |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Wszystkie | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Seria H
Maszyny wirtualne z serii H są zoptymalizowane pod kątem aplikacji z wysoką częstotliwością procesora CPU lub dużą ilością pamięci na podstawowe wymagania. Maszyny wirtualne serii H funkcja 8 lub 16 rdzeni procesora Intel Xeon E5 2667 v3, do 14 GB pamięci RAM na rdzeń procesora CPU i bez wielowątkowości. Maszyna wirtualna serii H zapewnia do 80 GB/s przepustowości pamięci.

ACU: 290–300

Premium Storage: nieobsługiwane

Buforowanie Premium Storage: nieobsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowa częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczyt) | Częstotliwość jednordzeniowa (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych Ethernet |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standardowa_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 |  - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standardowa_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standardowa_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | - | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16r <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standard_H16mr <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3,2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> w przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączona przez sieć FDR InfiniBand.

<br>
