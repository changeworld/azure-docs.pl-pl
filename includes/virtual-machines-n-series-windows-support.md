---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 0f87b122f81fbd04767354e623dcb808466ee1be
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2019
ms.locfileid: "67673457"
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nvidia-tesla-cuda-drivers"></a>NVIDIA Tesla (CUDA) drivers

Sterowniki NVIDIA Tesla (CUDA) dla NC, NCv2, NCv3, ND i NDv2 maszyny wirtualne z serii (opcjonalnie na potrzeby seria NV) są obsługiwane tylko w systemach operacyjnych wymienionych w poniższej tabeli. Linki pobierania sterownika są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Jako alternatywy dla ręcznej instalacji sterownik CUDA na maszynę Wirtualną z systemem Windows Server, można wdrożyć platformy Azure [maszyny wirtualnej do nauki o danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje maszyny DSVM systemu Windows Server 2016 przed instalacją sterowniki NVIDIA CUDA, CUDA głębokiego neuronowych biblioteki sieciowej i innych narzędzi.


| OS | Sterownik |
| -------- |------------- |
| Windows Server 2016 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [398.75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA GRID

Microsoft dystrybuuje instalatory sterownik technologii NVIDIA GRID NV i maszyny wirtualne z serii NVv2 używany jako wirtualnych stacji roboczych lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych serii NV Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują licencji na program siatki wirtualnej oprogramowania procesora GPU na platformie Azure. Nie trzeba skonfigurować serwer licencji oprogramowania vGPU firmy NVIDIA.

| OS | Sterownik |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 (maksymalnie wersja 1809. Nie zastosowano poprawki/poprawka poziomu 0 1809 nie jest obsługiwana). | [Siatka 8.0 (425.31)](https://go.microsoft.com/fwlink/?linkid=874181) (.exe) |
| Windows Server 2012 R2 | [Siatka 8.0 (425.31)](https://go.microsoft.com/fwlink/?linkid=874184) (.exe)  |
