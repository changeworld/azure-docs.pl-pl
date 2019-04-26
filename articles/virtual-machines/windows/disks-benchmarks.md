---
title: Analiza porównawcza aplikacji przy użyciu usługi Azure Disk Storage - dysków zarządzanych
description: Dowiedz się więcej o procesie testów porównawczych aplikacji na platformie Azure.
services: virtual-machines-windows,storage
author: rockboyfor
ms.author: v-yeche
origin.date: 01/11/2019
ms.date: 04/01/2019
ms.topic: article
ms.service: virtual-machines-windows
ms.tgt_pltfrm: windows
ms.subservice: disks
ms.openlocfilehash: 8db1fb3c9b3ed551cd668cf14105eb8bfb486251
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60679828"
---
# <a name="benchmarking-a-disk"></a>Analiza porównawcza dysku

Analiza porównawcza jest proces symulowanie różnych obciążeń w swojej aplikacji i pomiaru wydajności aplikacji dla poszczególnych obciążeń. Korzystając z procedury opisanej w [projektowanie pod kątem wysokiej wydajności artykułu](premium-storage-performance.md), zebrano wymagań dotyczących wydajności aplikacji. Uruchamiając narzędzi porównawczych dla maszyn wirtualnych hostujących aplikację, należy określić poziomów wydajności, które aplikacja może osiągnąć dzięki usłudze Premium Storage. W tym artykule firma Microsoft udostępnia użytkownikowi przykłady testów porównawczych standardowa DS14 maszyny Wirtualnej z systemem aprowizowane przy użyciu dysków usługi Premium Storage dla platformy Azure.

Odpowiednio użyliśmy typowych narzędzi porównawczych Iometer i FIO, Windows i Linux. Te narzędzia zduplikować wiele wątków, symulując produkcji, takich jak obciążenia, a zadaniem jest mierzenie wydajności systemu. Za pomocą narzędzi można również skonfigurować parametry, takie jak głębokość bloku rozmiaru i kolejki, które normalnie nie można zmienić dla aplikacji. Zapewnia większą elastyczność w przypadku dysków maksymalnej wydajności na dużą skalę maszyny Wirtualnej obsługiwane za pomocą dysków w warstwie premium dla różnego rodzaju obciążeń aplikacji. Aby dowiedzieć się więcej na temat każdego z narzędzi porównawczych odwiedź [Iometer](http://www.iometer.org/) i [FIO](http://freecode.com/projects/fio).

Aby skorzystać z poniższych przykładów, Utwórz Maszynę wirtualną standardowa DS14 i Dołącz 11 dysków usługi Premium Storage do maszyny Wirtualnej. 11 dysków skonfiguruj 10 dysków z hostem pamięci podręcznej jako "None" i stripe ich na wolumin o nazwie NoCacheWrites. Skonfiguruj buforowanie jako "Tylko do odczytu" na pozostałe dysku hosta i Utwórz wolumin o nazwie CacheReads z tego dysku. Przy użyciu tej konfiguracji, jesteś w stanie wyświetlić maksymalną wydajność odczytu i zapisu z standardowa DS14 maszyny Wirtualnej. Aby uzyskać szczegółowe instrukcje dotyczące tworzenia DS14 maszyny Wirtualnej z dyskami SSD w warstwie premium, przejdź do [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md).

[!INCLUDE [virtual-machines-disks-benchmarking](../../../includes/virtual-machines-managed-disks-benchmarking.md)]

## <a name="next-steps"></a>Kolejne kroki

Postępuj zgodnie z instrukcjami naszych projektowania w artykule o wysokiej wydajności. Możesz utworzyć listę kontrolną podobne do istniejących aplikacji prototypu. Za pomocą narzędzi Benchmarking można symulować obciążenia i mierzenie wydajności aplikacji prototypu. W ten sposób można określić, który dysk oferty można dopasować lub przekroczenie wymagań dotyczących wydajności aplikacji. Następnie można zaimplementować te same wytyczne dla aplikacji produkcyjnych.

> [!div class="nextstepaction"]
> Zobacz artykuł [projektowanie pod kątem wysokiej wydajności](premium-storage-performance.md) rozpocząć.