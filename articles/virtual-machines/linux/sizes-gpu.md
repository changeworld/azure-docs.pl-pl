---
title: Rozmiary maszyn wirtualnych z systemem Linux na platformie Azure — przyspieszone obliczenia | Microsoft Docs
description: Wyświetla różne rozmiary procesora GPU dostępne dla maszyn wirtualnych z systemem Linux na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines-linux
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: ed8af91701a5ed77636bb86e8798981f37546f23
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082136"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N platformy Azure z systemem Linux, należy zainstalować sterowniki graficznego procesora NVIDIA. [Rozszerzenie sterownika NVIDIA GPU](../extensions/hpccompute-gpu-linux.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim za pomocą Azure Portal lub narzędzi, takich jak szablony Azure Resource Manager interfejsu wiersza polecenia platformy Azure. Zapoznaj się z [dokumentacją rozszerzenia sterownika procesora GPU firmy NVIDIA](../extensions/hpccompute-gpu-linux.md) , aby poznać obsługiwane dystrybucje i kroki wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](../extensions/overview.md).

W przypadku ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem [Konfiguracja sterowników procesora GPU dla systemu Linux](n-series-driver-setup.md) dla obsługiwanych dystrybucji, sterowników i kroków instalacji i weryfikacji.


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* Nie należy instalować X serwera ani innych systemów, które używają `Nouveau` sterownika na maszynach wirtualnych Ubuntu NC. Przed zainstalowaniem sterowników procesora GPU firmy NVIDIA należy wyłączyć `Nouveau` sterownik.  

## <a name="other-sizes"></a>Inne rozmiary
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.