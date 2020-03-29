---
title: Rozmiary maszyn wirtualnych platformy Azure — magazyn | Dokumenty firmy Microsoft
description: Wyświetla listę różnych rozmiarów zoptymalizowanych pod kątem magazynu dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77913340"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci masowej

Zoptymalizowane pod kątem pamięci masowej rozmiary maszyn wirtualnych oferują wysoką przepustowość dysku i we/wy i są idealne dla baz danych Big Data, SQL, NoSQL, hurtowni danych i dużych transakcyjnych baz danych.  Przykłady obejmują Cassandra, MongoDB, Cloudera i Redis. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepływności i przepustowości sieci w lokalnej pamięci masowej dla każdego zoptymalizowanego rozmiaru.

[Seria Lsv2](lsv2-series.md) charakteryzuje się wysoką przepustowością, małym opóźnieniem, bezpośrednio mapowaną lokalną pamięcią NVMe działającą na [procesorze AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) z doładowaniem całego rdzenia 2,55 GHz i maksymalnym wzmocnieniem 3,0 GHz. Maszyny wirtualne serii Lsv2 mają rozmiary od 8 do 80 vCPU w jednoczesnej konfiguracji wielowątkowej.  Na procesory wirtualne przypada 8 GiB pamięci, a na urządzenie L80s v2 jest dostępne 1,92 TB NVMe SSD M.2 na 8 procesorów wirtualnych.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.

Dowiedz się, jak zoptymalizować wydajność maszyn wirtualnych z serii Lsv2 dla [systemu Windows](windows/storage-performance.md) lub [Linux.](linux/storage-performance.md)
