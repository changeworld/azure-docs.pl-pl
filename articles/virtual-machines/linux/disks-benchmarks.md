---
title: Analiza porównawcza aplikacji na dysku Usługi Azure Disk Storage
description: Dowiedz się więcej o procesie porównywania aplikacji na platformie Azure.
author: roygara
ms.author: rogarana
ms.date: 01/11/2019
ms.topic: conceptual
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 640ec54e9634751d05c2cea90d7c03d02e7a3387
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75720059"
---
# <a name="benchmarking-a-disk"></a>Analiza porównawcza dysku

Analiza porównawcza to proces symulowania różnych obciążeń w aplikacji i pomiaru wydajności aplikacji dla każdego obciążenia. Korzystanie z kroków opisanych w [projektowaniu dla wysokiej wydajności artykułu](premium-storage-performance.md). Uruchamiając narzędzia do analizy porównawczej na maszynach wirtualnych obsługujących aplikację, można określić poziomy wydajności, które aplikacja może osiągnąć za pomocą magazynu w wersji Premium. W tym artykule przedstawiamy przykłady analizy porównawczej standardowej maszyny wirtualnej DS14 aprowizowana za pomocą dysków usługi Azure Premium Storage.

Użyliśmy wspólnych narzędzi porównawczych Iometer i FIO, odpowiednio dla Windows i Linux. Narzędzia te odradzają wiele wątków symulujących produkcję, takich jak obciążenie, i mierzą wydajność systemu. Za pomocą narzędzi można również skonfigurować parametry, takie jak rozmiar bloku i głębokość kolejki, których zwykle nie można zmienić dla aplikacji. Zapewnia to większą elastyczność w celu zwiększenia maksymalnej wydajności na wysokiej skali maszyny Wirtualnej aprowizowana za pomocą dysków w wersji premium dla różnych typów obciążeń aplikacji. Aby dowiedzieć się więcej o każdym narzędziu do analizy porównawczej odwiedź [Iometer](http://www.iometer.org/) i [FIO](http://freecode.com/projects/fio).

Aby postępować zgodnie z poniższymi przykładami, utwórz standardową maszynę wirtualną DS14 i dołącz dyski magazynu w wersji Premium 11 do maszyny Wirtualnej. Z 11 dysków skonfiguruj 10 dysków z buforowania hosta jako "Brak" i rozbieraj je na wolumin o nazwie NoCacheWrites. Skonfiguruj buforowanie hosta jako "ReadOnly" na pozostałym dysku i utwórz wolumin o nazwie CacheReads z tym dyskiem. Za pomocą tej konfiguracji można zobaczyć maksymalną wydajność odczytu i zapisu ze standardowej maszyny Wirtualnej DS14. Aby uzyskać szczegółowe kroki dotyczące tworzenia maszyny Wirtualnej DS14 z dyskami premium, przejdź do [projektowanie w celu uzyskania wysokiej wydajności.](premium-storage-performance.md)

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do naszego artykułu na [temat projektowania dla wysokiej wydajności](premium-storage-performance.md).

W tym artykule utworzysz listę kontrolną podobną do istniejącej aplikacji dla prototypu. Za pomocą narzędzi do analizy porównawczej można symulować obciążenia i mierzyć wydajność w aplikacji prototypowej. W ten sposób można określić, które oferty dysku można dopasować lub przekroczyć wymagania dotyczące wydajności aplikacji. Następnie można zaimplementować te same wskazówki dla aplikacji produkcyjnej.