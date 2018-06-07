---
title: Rozmiar maszyny Wirtualnej systemu Linux platformy Azure - GPU | Dokumentacja firmy Microsoft
description: Wyświetla różne procesora GPU zoptymalizowanych pod kątem dostępne dla maszyn wirtualnych systemu Linux na platformie Azure. Wyświetla informacje o liczbie Vcpu, dysków z danymi i karty sieciowe, a także przepustowości przepływności i sieć magazynu rozmiarów w tej serii.
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
ms.date: 03/01/2018
ms.author: jonbeck
ms.openlocfilehash: 5b856ec14febefc96e77d3c131b746e597a3aa5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34653624"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Procesor GPU zoptymalizowanych pod kątem rozmiarów maszyn wirtualnych

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

Sterownik instalacji i weryfikacja kroki opisane w artykule [N-series sterownik Instalatora dla systemu Linux](n-series-driver-setup.md).

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Nie należy zainstalować X serwera lub innych systemów, które używają `Nouveau` sterowników na maszynach wirtualnych NC Ubuntu. Przed zainstalowaniem wersji sterowników procesora GPU NVIDIA, musisz wyłączyć `Nouveau` sterownika.  

## <a name="other-sizes"></a>Innych rozmiarach
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzedniej generacji](sizes-previous-gen.md)

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej na temat [jednostki (ACU) rozwiązań usługi obliczenia Azure](acu.md) ułatwia porównanie wydajności obliczeniowej różnych jednostki SKU Azure.