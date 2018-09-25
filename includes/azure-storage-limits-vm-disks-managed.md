---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dcd94cbd7963c13bd739b1b004fe5ac506d3ea9d
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006492"
---
**Standardowa zarządzana maszyn wirtualnych dysków twardych**

| Typ dysku standardowego  | S4               | S6               | S10             | S15 | S20              | S30              | S40              | S50              | S60              | S70              | S80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku w GiB          | 32             | 64             | 128            | 256  | 512            | 1,024    | 2048     | 4095    | 8192     | 16 384     | 32,767     |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | Maks. 500              | Maks. 500              | Maks. 500              | Maks. 500 | Maks. 500              | Maks. 500              | Maks. 500             | Maks. 500              | Maksymalnie 1300              | Do 2000              | Do 2000              |
| Przepływność na dysk | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s | Maksymalnie 60 MiB/s| Maksymalnie 300 MiB/s | Maks. 500 MiB/s | Maks. 500 MiB/s |

**Dyski SSD standardowa zarządzanej maszyny wirtualnej**

| Typ dysku SSD w warstwie standardowa  | E10               | E15               | E20             | E30 | E40              | E50              | E60              | E70              | E80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku w GiB           | 128             | 256             | 512            | 1,024  | 2048            | 4095     | 8192     | 16 384     | 32,767    |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | Maks. 500              | Maks. 500              | Maks. 500              | Maks. 500 | Maks. 500              | Maks. 500              | Maks. 500             | Maks. 500              | Maksymalnie 1300              | Do 2000              | Do 2000              |
| Przepływność na dysk | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s | Maksymalnie 60 MB/s| Maksymalnie 300 MiB/s |  Maks. 500 MiB/s | Maks. 500 MiB/s |

**Zarządzane dyski maszyny wirtualnej w warstwie Premium: limity poszczególnych dysków**

| Typ dysku w warstwie Premium  | P4               | P6               | P10             | P15 | P20              | S30              | P40              | P50              | P60              | P70              | P80              |
|---------------------|---------------------|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|
| Rozmiar dysku w GiB           | 32             | 64             | 128            | 256  | 512            | 1,024    | 2048     | 4095    | 8192     | 16 384     | 32,767     |
| Liczba operacji wejścia/wyjścia na sekundę na dysk       | Do 120 | Maksymalnie 240              | Maks. 500              | Maksymalnie 1100 | Maksymalnie 2300              | Maksymalnie 5000              | Maksymalnie 7500             | Maksymalnie 7500              | Maksymalnie 12 500              | Maksymalnie 15 000              | Maksymalnie 20 000              |
| Przepływność na dysk | Maksymalnie 25 MiB/s | Maksymalnie 50 MiB/s | Maksymalnie 100 MiB/s | Maksymalnie 125 MiB/s | Maksymalnie 150 MiB/s | Maksymalnie 200 MiB/s | Do 250 MiB/s | Do 250 MiB/s| Maksymalnie 480 MiB/s | Do 750 MiB/s | Do 750 MiB/s |

**Zarządzane dyski maszyny wirtualnej w warstwie Premium: limity poszczególnych maszyn wirtualnych**

| Zasób | Limit domyślny |
| --- | --- |
| Maksymalna liczba operacji wejścia/wyjścia na sekundę na maszynę wirtualną |80 000 operacji We/Wy z maszyn wirtualnych GS5 |
| Maksymalna przepływność na maszynę wirtualną |2000 MB/s przy użyciu maszyn wirtualnych GS5 |
