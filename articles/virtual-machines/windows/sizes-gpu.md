---
title: Rozmiary maszyn wirtualnych z systemem Windows Azure — procesor GPU
description: Wyświetla różne rozmiary procesora GPU dostępne dla maszyn wirtualnych z systemem Windows na platformie Azure. Wyświetla informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines-windows
documentationcenter: ''
author: jonbeck7
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/11/2019
ms.author: jonbeck
ms.openlocfilehash: 1f26ad56734dfec0dbf7fc8198b8a91a706a843b
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75371075"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem procesora GPU

[!INCLUDE [virtual-machines-common-sizes-gpu](../../../includes/virtual-machines-common-sizes-gpu.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N z systemem Windows, należy zainstalować sterowniki NVIDIA lub AMD GPU. 

[Rozszerzenie sterownika NVIDIA GPU](../extensions/hpccompute-gpu-windows.md) instaluje odpowiednie sterowniki NVIDIA CUDA lub Grid na maszynie wirtualnej z serii N. Zainstaluj rozszerzenie lub Zarządzaj nim przy użyciu Azure Portal lub narzędzi, takich jak szablony Azure PowerShell lub Azure Resource Manager. Zapoznaj się z [dokumentacją rozszerzenia sterownika graficznego procesora NVIDIA](../extensions/hpccompute-gpu-windows.md) dla obsługiwanych systemów operacyjnych i kroków wdrażania. Aby uzyskać ogólne informacje o rozszerzeniach maszyn wirtualnych, zobacz [rozszerzenia i funkcje maszyny wirtualnej platformy Azure](../extensions/overview.md).

W przypadku ręcznego instalowania sterowników procesora GPU firmy NVIDIA należy zapoznać się z tematem [Konfiguracja sterowników procesora GPU dla systemu Windows](n-series-driver-setup.md) w przypadku obsługiwanych systemów operacyjnych, sterowników i kroków instalacji i weryfikacji.

Aby ręcznie zainstalować sterowniki procesora GPU AMD, zobacz [Instalator sterowników procesora GPU AMD dla systemu Windows](n-series-amd-driver-setup.md) , który obsługuje systemy operacyjne, sterowniki oraz czynności instalacyjne i weryfikacyjne.

[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

## <a name="other-sizes"></a>Inne rozmiary
- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem magazynu](sizes-storage.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

