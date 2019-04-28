---
title: Rozmiary maszyn wirtualnych systemu Linux platformy Azure — procesor GPU | Dokumentacja firmy Microsoft
description: Rozmiarów dostępnymi dla maszyn wirtualnych systemu Linux na platformie Azure listy różnych procesorów GPU zoptymalizowanych pod kątem. Wyświetla informacje o liczbie procesorów wirtualnych, dysków z danymi i kart sieciowych, a także magazynu przepustowości przepływność i sieci dla rozmiarów w tej serii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: jonbeck
ms.openlocfilehash: 8f50f090fe38382b8bc3cb7f669ab4025d36ff76
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60799431"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiarów maszyn wirtualnych zoptymalizowanych pod kątem procesora GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

Aby móc korzystać z możliwości procesora GPU platformy Azure maszyn wirtualnych serii N z systemem Linux, muszą być zainstalowane sterowniki procesora GPU firmy NVIDIA. [Rozszerzenia sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub siatki na maszynie Wirtualnej serii N. Zainstaluj lub zarządzać rozszerzenia za pomocą witryny Azure portal lub narzędzi, takich jak szablony wiersza polecenia platformy Azure lub usługi Azure Resource Manager. Zobacz [dokumentację dotyczącą rozszerzeń sterowników procesora GPU NVIDIA](../extensions/hpccompute-gpu-linux.md) obsługiwane dystrybucje i kroki związane z wdrażaniem. Aby uzyskać ogólne informacje dotyczące rozszerzeń maszyn wirtualnych, zobacz [rozszerzeń maszyny wirtualnej platformy Azure i funkcji](../extensions/overview.md).

Jeśli zdecydujesz się zainstalować sterowniki procesora GPU NVIDIA ręcznie, zobacz [GPU N-series driver Setup for Linux Konfiguracja](n-series-driver-setup.md) obsługiwanych dystrybucjach, sterowników i kroki instalacji i weryfikacji.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Nie należy instalować X serwera lub innych systemów, które używają `Nouveau` sterowników na maszynach wirtualnych kontrolera sieci systemu Ubuntu. Przed rozpoczęciem instalacji sterowników procesora GPU NVIDIA, należy wyłączyć `Nouveau` sterownika.  

## <a name="other-sizes"></a>O innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o tym, jak [usługi Azure compute jednostki (ACU)](acu.md) pozwalają porównać wydajności obliczeń w jednostkach SKU platformy Azure.