---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 689e45f96ed5e7aaea3aecd1193e9cd58f10e80d
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2018
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-tesla-cuda-drivers"></a>NC, NCv2, NCv3 i serii ND - sterowniki NVIDIA tesla — (CUDA)

Sterownik pobierania łączy w poniższej tabeli są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Zamiast ręcznej instalacji sterowników CUDA na maszynie Wirtualnej systemu Windows Server, można wdrożyć Azure [maszyny wirtualnej nauki danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje DSVM dla systemu Windows Server 2016 wstępnie instalować sterowniki NVIDIA CUDA, CUDA głębokie Neuronowej biblioteki sieciowej i inne narzędzia.


| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [390.85](http://us.download.nvidia.com/Windows/Quadro_Certified/390.85/390.85-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |

### <a name="nv-series---nvidia-grid-drivers"></a>Serii Trwałym - sterowniki NVIDIA siatki

Microsoft ponownie dystrybuuje instalatorów sterownika NVIDIA siatki dla maszyn wirtualnych z wirtualizacją sieci. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych z wirtualizacją sieci platformy Azure. Te sterowniki obejmują licencjonowania siatki wirtualnej oprogramowania procesora GPU na platformie Azure.

| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [Siatka 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [Siatka 6.0 (391.03)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |