---
title: Seria NCv3 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii NCv3.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ae3604a9ea82e6e50ba4d639d36572f7b052e4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302801"
---
# <a name="ncv3-series"></a>Seria NCv3

Maszyny wirtualne z serii NCv3 są obsługiwane przez procesory graficzne [NVIDIA Tesla V100.](https://www.nvidia.com/data-center/tesla-v100/) Te procesory graficzne mogą zapewnić 1,5-krotną wydajność obliczeniową serii NCv2. Klienci mogą korzystać z tych zaktualizowanych procesorów graficznych do tradycyjnych obciążeń HPC, takich jak modelowanie zbiorników, sekwencjonowanie DNA, analiza białek, symulacje Monte Carlo i inne. Konfiguracja NC24rs v3 zapewnia interfejs sieciowy o niskiej opóźnień i wysokiej przepustowości zoptymalizowany pod kątem ściśle łączonych obciążeń równoległych. Oprócz procesorów graficznych maszyny wirtualne z serii NCv3 są również obsługiwane przez procesory Intel Xeon E5-2690 v4 (Broadwell).

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

> [!IMPORTANT]
> Dla tej serii maszyn wirtualnych przydział vCPU (core) w subskrypcji jest początkowo ustawiony na 0 w każdym regionie. [Zażądaj zwiększenia przydziału procesora wirtualnego](../azure-supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>
| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v3    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v3   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v3   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v3* | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = jedna karta V100.

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
