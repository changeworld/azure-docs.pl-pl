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
ms.openlocfilehash: 40e5a1bf940e46aed566a1e3fa6dcb4e6b2d9230
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135134"
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nvidia-tesla-cuda-drivers"></a>Sterowniki NVIDIA Tesla (CUDA)

Sterowniki NVIDIA Tesla (CUDA) dla maszyn wirtualnych z serii NC, NCv2, NCv3, ND i NDv2 (opcjonalnie dla serii NV) są obsługiwane tylko w systemach operacyjnych wymienionych w poniższej tabeli. Linki do pobrania sterownika są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Jako alternatywę dla ręcznej instalacji sterownika CUDA na maszynie wirtualnej systemu Windows Server można wdrożyć obraz [maszyny wirtualnej nauki o danych](../articles/machine-learning/data-science-virtual-machine/overview.md) platformy Azure. Wersje DSVM dla systemu Windows Server 2016 wstępnie instalują sterowniki NVIDIA CUDA, bibliotekę sieci głębokiej sieci neuronowej CUDA i inne narzędzia.


| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (exe) |

### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA GRID

Firma Microsoft redystrybuuje instalatory sterowników NVIDIA GRID dla maszyn wirtualnych z serii NV i NVv3 używanych jako wirtualne stacje robocze lub do aplikacji wirtualnych. Zainstaluj tylko te sterowniki GRID na maszynach wirtualnych z serii NV platformy Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Sterowniki te obejmują licencjonowanie oprogramowania GRID Virtual GPU na platformie Azure. Nie trzeba ująć serwera licencji nvidia vGPU.

Należy pamiętać, że rozszerzenie Nvidia zawsze zainstaluje najnowszy sterownik. Udostępniamy łącza do poprzedniej wersji tutaj dla klientów, którzy mają zależność od starszej wersji.

W systemach Windows Server 2019, Windows Server 2016 i Windows 10 (do kompilacji 1909):
- [SIATKA 10,1 (442,06)](https://go.microsoft.com/fwlink/?linkid=874181) (exe)
- [SIATKA 10,0 (441,66)](https://download.microsoft.com/download/2/a/3/2a316e62-3be9-4ddb-ae8e-c04b6df6e22d/441.66_grid_win10_server2016_server2019_64bit_international.exe) (exe) 

W systemach Windows Server 2012 R2, Windows Server 2008 R2, Windows 8 i Windows 7: 
- [SIATKA 10,1 (442,06)](https://go.microsoft.com/fwlink/?linkid=874184) (exe)
- [SIATKA 10,0 (441,66)](https://download.microsoft.com/download/d/8/0/d80091f8-0d55-47c2-958a-bacd136f432a/441.66_grid_win7_win8_server2008R2_server2012R2_64bit_international.exe) (exe)  
