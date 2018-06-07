---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows
author: dlepow
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 8f3d57f8f1f3631421618e31e943606a16e6bf5b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670023"
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nvidia-tesla-cuda-drivers"></a>Sterowniki NVIDIA tesla — (CUDA)

Tesla — NVIDIA (CUDA) sterowniki NC, NCv2 NCv3 i maszyn wirtualnych serii ND (opcjonalny w przypadku serii wirtualizacją sieci) są obsługiwane tylko w systemach operacyjnych wymienionych w poniższej tabeli. Łącza pobierania sterowników są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Zamiast ręcznej instalacji sterowników CUDA na maszynie Wirtualnej systemu Windows Server, można wdrożyć Azure [maszyny wirtualnej nauki danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje DSVM dla systemu Windows Server 2016 wstępnie instalować sterowniki NVIDIA CUDA, CUDA głębokie Neuronowej biblioteki sieciowej i inne narzędzia.


| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA siatki

Microsoft ponownie dystrybuuje instalatorów sterownika NVIDIA siatki dla serii wirtualizacją sieci maszyn wirtualnych użyć jako wirtualne stacje robocze lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych z wirtualizacją sieci Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują licencjonowania siatki wirtualnej oprogramowania procesora GPU na platformie Azure.

| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [Siatka 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [Siatka 6.1 (391.58)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |