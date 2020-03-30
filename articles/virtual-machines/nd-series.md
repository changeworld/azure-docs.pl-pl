---
title: Seria ND — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii ND.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 163dc0d6ecd4f91273a82b04ce186f7a8df868af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465060"
---
# <a name="nd-series"></a>Seria ND

Maszyny wirtualne z serii ND są nowym dodatkiem do rodziny gpu przeznaczonych dla obciążeń AI i Deep Learning. Oferują doskonałe osiągi do treningu i wnioskowania. Wystąpienia ND są obsługiwane przez procesory graficzne [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) i procesory Intel Xeon E5-2690 v4 (Broadwell). Te wystąpienia zapewniają doskonałą wydajność dla operacji zmiennoprzecinkowych z jedną precyzją dla obciążeń AI wykorzystujących microsoft cognitive toolkit, TensorFlow, Caffe i inne struktury. Seria ND oferuje również znacznie większy rozmiar pamięci GPU (24 GB), dzięki czemu można dopasować znacznie większe modele sieci neuronowych. Podobnie jak seria NC, seria ND oferuje konfigurację z drugorzędną siecią o niskim opóźnieniu, dużą przepustowością przez RDMA i łącznością InfiniBand, dzięki czemu można uruchamiać zadania szkoleniowe na dużą skalę obejmujące wiele procesorów graficznych.

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

> [!IMPORTANT]
> Dla tej serii maszyn wirtualnych przydział vCPU (core) na region w subskrypcji jest początkowo ustawiony na 0. [Zażądaj zwiększenia przydziału procesora wirtualnego](../azure-supportability/resource-manager-core-quotas-request.md) dla tej serii w [dostępnym regionie](https://azure.microsoft.com/regions/services/).
>
| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna przepustowość dysku bez buforów: IOPS/MB/s | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |
| Standard_ND24rs* | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = jedna karta P40.

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
