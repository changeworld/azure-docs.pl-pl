---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 04/26/2019
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: f3c1a946afb7f51a1c7543dabae7f2f42ff466be
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65199340"
---
Seria H maszyn wirtualnych (VM) są przeznaczone do klasy kierownicze wydajność, skalowalność MPI i ekonomiczność dla różnych obciążeń HPC w rzeczywistych warunkach.

Maszyny wirtualne z serii HB są zoptymalizowane pod kątem zastosowań zależnych od przepustowości pamięci, takich jak dynamika płynów, jawna analiza elementów skończonych i modelowanie pogody. Maszyny wirtualne HB dysponują 60 rdzeniami procesorów AMD EPYC 7551, 4 GB pamięci RAM na rdzeń procesora CPU i nie korzystają z hiperwątkowości. Platforma AMD EPYC zapewnia ponad 260 GB/s przepustowości pamięci.

Maszyny wirtualne z serii połączenia Hybrydowego są zoptymalizowane pod kątem aplikacje gęstą obliczeń, takich jak analizy elementów skończonych niejawne, molekularnym dynamics i związane z chemią obliczeniową. Maszyny wirtualne HC dysponują 44 rdzeniami procesorów Intel Xeon Platinum 8168, 8 GB pamięci RAM na rdzeń procesora CPU i nie korzystają z hiperwątkowości. Platforma Intel Xeon Platinum obsługuje firmy Intel bogatego ekosystemu narzędzi programistycznych, takich jak biblioteka jądra matematyczne Intel.

Zarówno HB, jak i maszyny wirtualne połączenia Hybrydowego są wyposażone w 100 Gb/s InfiniBand EDR Mellanox w systemie fat nieblokującej na poziomie drzewa Konfiguracja spójną wydajność funkcji RDMA. HB i maszyny wirtualne połączenia Hybrydowego obsługuje standardowe sterowniki Mellanox/OFED w taki sposób, że wszystkie typy MPI i wersje, a także zleceń RDMA, są również obsługiwane.

Maszyny wirtualne z serii H są zoptymalizowane pod kątem aplikacje o wysokiej częstotliwości procesora CPU lub pamięci na podstawowe wymagania. Seria H maszyn wirtualnych funkcji 8 lub 16 procesorów Intel Xeon E5 2667 v3 procesora rdzenie, 7 lub 14 GB pamięci RAM na rdzeń procesora CPU, a nie wielowątkowość. Seria H oferuje 56 Gb/s InfiniBand o przepustowości fdr wraz Mellanox w systemie fat nieblokującej na poziomie drzewa Konfiguracja spójną wydajność funkcji RDMA. Maszyny wirtualne z serii H obsługuje Intel MPI 5.x i MS-MPI.

## <a name="hb-series"></a>HB serii

Magazyn w warstwie Premium: Obsługiwane usługi Premium Storage buforowania: Obsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowy częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczytowa) | Częstotliwość jednordzeniowy (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Karty sieciowe Ethernet maksymalna |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB60rs | 60 | AMD EPYC 7551 | 240 | 263 | 2.0 | 2.55 | 2.55 | 100 | Wszyscy | 700 | 4 | 1 |

<br>

## <a name="hc-series"></a>Seria połączenia Hybrydowego

Magazyn w warstwie Premium: Obsługiwane usługi Premium Storage buforowania: Obsługiwane


| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowy częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczytowa) | Częstotliwość jednordzeniowy (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Karty sieciowe Ethernet maksymalna |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon Platinum 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Wszyscy | 700 | 4 | 1 |


<br>

## <a name="h-series"></a>Seria H

ACU: 290-300

Magazyn w warstwie Premium:  Nieobsługiwane

Buforowanie Premium Storage:  Nieobsługiwane

| Rozmiar | Procesor wirtualny | Procesor | Pamięć (GB) | Przepustowość pamięci GB/s | Podstawowy częstotliwość procesora CPU (GHz) | Częstotliwość wszystkich rdzeni (GHz, szczytowa) | Częstotliwość jednordzeniowy (GHz, szczytowa) | Wydajność RDMA (GB/s) | Obsługa MPI | Magazyn tymczasowy (GB) | Maks. liczba dysków danych | Karty sieciowe Ethernet maksymalna |
| --- | --- |--- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standardowa_H8 | 8 | Intel Xeon E5 2667 v3 | 56 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standardowa_H16 | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 |  56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Standardowa_H8m | 8 | Intel Xeon E5 2667 v3 | 112 | 40 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 1000 | 32 | 2 |
| Standardowa_H16m | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Maszyna wirtualna standard_h16r — <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 112 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |
| Maszyna wirtualna standard_h16mr — <sup>1</sup> | 16 | Intel Xeon E5 2667 v3 | 224 | 80 | 3.2 | 3.3 | 3.6 | 56 | Intel 5.x, MS-MPI | 2000 | 64 | 4 |

<sup>1</sup> w przypadku aplikacji MPI dedykowana sieć zaplecza RDMA jest włączane przez sieć FDR InfiniBand, która zapewnia bardzo ultraniskie opóźnienia i wysoką przepustowość.

<br>
