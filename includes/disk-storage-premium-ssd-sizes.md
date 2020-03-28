---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/07/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 7b2d4777772d842898cfcdd04f1c6d926cdbf0ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76837565"
---
| Rozmiary najwyższej jakości ssd | P1* | P2* | P3* | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Rozmiar dysku w gib | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1,024 | 2048 | 4,096 | 8192 | 16 384 | 32 767 |
| Liczba operacji wejścia/wyjścia na sekundę na dysk | 120 | 120 | 120 | 120 | 240 | 500 | 1100 | 2300 | 5000 | 7500 | 7500 | 16 000 | 18 000 | 20 000 |
| Przepływność na dysk | 25 MiB/s | 25 MiB/s | 25 MiB/s | 25 MiB/s | 50 MiB/s | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s| 500 MiB/s | 750 MiB/s | 900 MiB/s |
| Maksymalna liczba we/wy serii na dysk** | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 |
| Maksymalna przepustowość serii na dysk** | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s | 170 MiB/s |
| Maksymalny czas trwania serii** | 30 min.  | 30 min.  | 30 min.  | 30 min.  | 30 min.  | 30 min.  | 30 min.  | 30 min.  |
| Kwalifikuje się do rezerwacji | Nie  | Nie  | Nie  | Nie  | Nie  | Nie  | Nie  | Nie  | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku | Tak, do jednego roku |

\*Oznacza rozmiar dysku, który jest obecnie w wersji zapoznawczej, aby uzyskać informacje o dostępności regionalnej, zobacz [Nowe rozmiary dysku: Zarządzane i niezarządzane](https://docs.microsoft.com/azure/virtual-machines/linux/faq-for-disks#new-disk-sizes-managed-and-unmanaged).  
\*\*Oznacza funkcję, która jest obecnie w wersji zapoznawczej, zobacz [Rozerwanie dysku, aby](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) uzyskać więcej informacji.
