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
ms.openlocfilehash: 508d13356a7dd221b0767a0cedd60b4f1402baf4
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/16/2019
ms.locfileid: "68286295"
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nvidia-tesla-cuda-drivers"></a>Sterowniki NVIDIA Tesla (CUDA)

Sterowniki NVIDIA Tesla (CUDA) dla maszyn wirtualnych z serii NC, NCv2, Seria NCV3, ND i NDv2 (opcjonalne dla serii NV) są obsługiwane tylko w systemach operacyjnych wymienionych w poniższej tabeli. Linki pobierania sterowników są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](https://www.nvidia.com/).

> [!TIP]
> Alternatywą dla ręcznej instalacji sterownika CUDA na maszynie wirtualnej z systemem Windows Server jest wdrożenie obrazu [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) platformy Azure. Wersje DSVM dla systemu Windows Server 2016 wstępnie instalują sterowniki NVIDIA CUDA, bibliotekę sieciową CUDA głęboki neuronowych oraz inne narzędzia.


| OS | Sterownik |
| -------- |------------- |
| Windows Server 2016 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2016-international.exe) (. exe) |
| Windows Server 2012 R2 | [398,75](https://us.download.nvidia.com/Windows/Quadro_Certified/398.75/398.75-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (. exe) |

### <a name="nvidia-grid-drivers"></a>Sterowniki sieci NVIDIA

Firma Microsoft redystrybuuje Instalatory sterowników NVIDIA GRID dla maszyn wirtualnych z serii NV i NVv3 używanych jako wirtualne stacje robocze lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych z serii NV platformy Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują Licencjonowanie oprogramowania wirtualnej procesora GPU na platformie Azure. Nie trzeba konfigurować serwera licencji oprogramowania NVIDIA vGPU.

| OS | Sterownik |
| -------- |------------- |
| Windows Server 2019<br/><br/>Windows Server 2016<br/><br/>Windows 10 | [Siatka 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874181) (. exe) |
| Windows Server 2012 R2<br/><br/>Windows Server 2008 R2<br/><br/>Windows 8<br/><br/>Windows 7 | [Siatka 9,0 (431,02)](https://go.microsoft.com/fwlink/?linkid=874184) (. exe)  |
