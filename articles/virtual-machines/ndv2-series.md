---
title: Seria NDv2 — maszyny wirtualne platformy Azure
description: Dane techniczne maszyn wirtualnych serii NDv2.
services: virtual-machines
author: vikancha
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 8a2d275501816dd504130b255b73a752c5615f0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247286"
---
# <a name="updated-ndv2-series"></a>Zaktualizowano serię NDv2

Maszyna wirtualna z serii NDv2 to nowy dodatek do rodziny procesorów graficznych zaprojektowany z myślą o potrzebach najbardziej wymagających obciążeń AI przyspieszanych przez procesory graficzne, uczenia maszynowego, symulacji i hpc.

NDv2 jest zasilany przez 8 procesorów graficznych NVIDIA Tesla V100 NVLINK, z których każdy ma 32 GB pamięci GPU. Każdy NDv2 VM ma również 40 niedemotliwych rdzeni Intel Xeon Platinum 8168 (Skylake) i 672 GiB pamięci systemowej.

Wystąpienia NDv2 zapewniają doskonałą wydajność dla obciążeń HPC i AI z wykorzystaniem jąder obliczeniowych zoptymalizowanych pod kątem procesora GRAFICZNEGO CUDA oraz wielu narzędzi AI, ML i analitycznych, które obsługują akcelerację GPU "out-of-box", takich jak TensorFlow, Pytorch, Caffe, RAPIDS i inne Ram.

Krytycznie, NDv2 jest przeznaczony zarówno do intensywnego skalowania obliczeniowego (wykorzystanie 8 procesorów GPU na maszynę wirtualną), jak i skalowania w poziomie (wykorzystanie wielu maszyn wirtualnych współpracujących ze sobą). Seria NDv2 obsługuje teraz sieć zaplecza EDR 100-gigabitowego InfiniBand EDR, podobną do tej dostępnej w serii HB HPC VM, aby umożliwić klastrowanie o wysokiej wydajności dla równoległych scenariuszy, w tym rozproszonych szkoleń dla AI i ML. Ta sieć zaplecza obsługuje wszystkie główne protokoły InfiniBand, w tym protokoły stosowane przez biblioteki NCCL2 firmy NVIDIA, co pozwala na bezproblemowe klastrowanie procesorów graficznych.

> Podczas [włączania infiniband](https://docs.microsoft.com/azure/virtual-machines/workloads/hpc/enable-infiniband) na maszynie wirtualnej ND40rs_v2, należy użyć sterownika Mellanox OFED 4.7-1.0.0.1.
>
> Ze względu na zwiększoną pamięć GPU, nowy ND40rs_v2 maszyny Wirtualnej wymaga użycia [maszyn wirtualnych generacji 2](https://docs.microsoft.com/azure/virtual-machines/windows/generation-2) i obrazów z portalu Marketplace. 
>
> Uwaga: ND40s_v2 z 16 GB pamięci na GPU nie jest już dostępny w wersji zapoznawczej i został zastąpiony przez zaktualizowany ND40rs_v2.

<br>

Pamięć masowa w wersji premium: obsługiwane

Buforowanie magazynu w wersji Premium: obsługiwane

Migracja na żywo: nie jest obsługiwana

Aktualizacje zachowywania pamięci: nie są obsługiwane

InfiniBand: Obsługiwane

| Rozmiar | Procesor wirtualny | Pamięć: GiB | Magazyn tymczasowy (SSD): GiB | Procesory GPU | Pamięć GPU: GiB | Maks. liczba dysków danych | Maksymalna przepływność niebuforowanych dysków: liczba operacji we/wy na sekundę / MB/s | Maksymalna przepustowość sieci | Maksymalna liczba kart sieciowych |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000 / 800 | 24000 Mb/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby korzystać z możliwości procesorów GPU maszyn wirtualnych z serii N platformy Azure, należy zainstalować sterowniki gpu nvidia.

[Rozszerzenie sterownika GPU NVIDIA](./extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub GRID na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub zarządzaj nim za pomocą witryny Azure portal lub narzędzi, takich jak szablony programu Azure PowerShell lub Usługi Azure Resource Manager. Aby uzyskać ogólne informacje na temat rozszerzeń maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](./extensions/overview.md).

Jeśli zdecydujesz się zainstalować sterowniki GPU NVIDIA ręcznie, zobacz [Konfiguracja sterowników GPU serii N dla systemu Linux](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [jednostki obliczeniowe platformy Azure (ACU)](acu.md) mogą pomóc w porównywaniu wydajności obliczeniowej w jednostkach SKU platformy Azure.
