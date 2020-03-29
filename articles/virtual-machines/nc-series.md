---
title: Seria NC — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii NC.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: eab7dfe79aa5cdf234c8bc9472387214f7df3563
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78164529"
---
# <a name="nc-series"></a>Seria NC

Maszyny wirtualne z serii NC są zasilane przez kartę [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) i procesor Intel Xeon E5-2690 v3 (Haswell). Użytkownicy mogą szybciej przeglądać dane, wykorzystując CUDA do zastosowań w eksploracji energii, symulacji awarii, renderowania śledzenia promieni, uczenia głębokiego i innych. Konfiguracja NC24r zapewnia interfejs sieciowy o niskiej opóźnień i wysokiej przepustowości zoptymalizowany pod kątem ściśle łączonych obciążeń równoległych.

Pamięć masowa w wersji Premium: nie jest obsługiwana

Buforowanie magazynu w wersji Premium: nie jest obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|
| Standardowa_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standardowa_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standardowa_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standardowa_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 procesor GPU = połowa karty K80.

*Obsługa technologii RDMA

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure, należy zainstalować sterowniki gpu nvidia.

[Rozszerzenie sterownika GPU NVIDIA](./extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub zarządzaj nim za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub Usługi Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterowników procesora graficznego NVIDIA, aby](./extensions/hpccompute-gpu-windows.md) zapoznać się z obsługiwanymi systemami operacyjnymi i krokami wdrażania. Aby uzyskać ogólne informacje na temat rozszerzeń maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).

Jeśli zdecydujesz się zainstalować sterowniki GPU NVIDIA ręcznie, zobacz [Konfiguracja sterowników GPU serii N dla systemu Windows](./windows/n-series-driver-setup.md) lub N dla systemu [Linux](./linux/n-series-driver-setup.md) dla obsługiwanych systemów operacyjnych, sterowników, instrukcji instalacji i weryfikacji.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
