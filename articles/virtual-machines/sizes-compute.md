---
title: Rozmiary maszyn wirtualnych platformy Azure — zoptymalizowane pod kątem obliczeń | Dokumenty firmy Microsoft
description: Wyświetla listę różnych rozmiarów zoptymalizowanych pod kątem obliczeń dostępnych dla maszyn wirtualnych na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych, dysków danych i kart sieciowych, a także przepustowości magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: e08d593f641c42f9ad605fda013206e70a34e52f
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269639"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>Oblicz optymalizację rozmiarów maszyn wirtualnych

Rozmiary zoptymalizowanych maszyn wirtualnych mają wysoki współczynnik procesora CPU do pamięci. Te rozmiary są dobre dla serwerów sieci web o średnim natężeniu ruchu, urządzeń sieciowych, procesów wsadowych i serwerów aplikacji. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych. Zawiera również informacje o przepływności magazynu i przepustowości sieci dla każdego rozmiaru w tej grupie.

[Seria Fsv2](fsv2-series.md) jest oparta na procesorze Intel® Xeon® Platinum 8168. Posiada trwałą prędkość zegara Turbo całego rdzenia 3,4 GHz i maksymalną jednordzeniową częstotliwość turbo 3,7 GHz. Instrukcje Intel® AVX-512 są nowe w przypadku procesorów Intel Scalable. Te instrukcje zapewniają maksymalnie 2-krotny wzrost wydajności do obciążeń przetwarzania wektorowego zarówno w operacjach zmiennoprzecinkowych pojedynczej, jak i podwójnej precyzji. Innymi słowy, są one bardzo szybkie dla każdego obciążenia obliczeniowego.

Przy niższej cenie katalogowej za godzinę seria Fsv2 jest najlepszą wartością w wydajności cenowej w portfolio platformy Azure na podstawie jednostki obliczeniowej azure (ACU) na procesor wirtualny.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
