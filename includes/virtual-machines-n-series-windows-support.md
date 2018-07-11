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
ms.openlocfilehash: a07f1622f2d41d105c79fa5354d97aa079567e62
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935785"
---
## <a name="supported-operating-systems-and-drivers"></a>Obsługiwane systemy operacyjne i sterowniki

### <a name="nvidia-tesla-cuda-drivers"></a>Sterowniki NVIDIA Tesla (CUDA)

Sterowniki NVIDIA Tesla (CUDA) dla NC, NCv2, NCv3 i maszyny wirtualne z serii ND (opcjonalnie na potrzeby seria NV) są obsługiwane tylko w systemach operacyjnych wymienionych w poniższej tabeli. Linki pobierania sterownika są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki, odwiedź witrynę internetową firmy [NVIDIA](http://www.nvidia.com/).

> [!TIP]
> Jako alternatywy dla ręcznej instalacji sterownik CUDA na maszynę Wirtualną z systemem Windows Server, można wdrożyć platformy Azure [maszyny wirtualnej do nauki o danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje maszyny DSVM systemu Windows Server 2016 przed instalacją sterowniki NVIDIA CUDA, CUDA głębokiego neuronowych biblioteki sieciowej i innych narzędzi.


| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2016-international.exe) (.exe) |
| Windows Server 2012 R2 | [397.44](http://us.download.nvidia.com/Windows/Quadro_Certified/397.44/397.44-tesla-desktop-winserver2008-2012r2-64bit-international.exe) (.exe) |


### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA GRID

Microsoft dystrybuuje instalatory sterownik technologii NVIDIA GRID dla maszyn wirtualnych z serii NV, używane jako wirtualnych stacji roboczych lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych serii NV Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują licencji na program siatki wirtualnej oprogramowania procesora GPU na platformie Azure.

| System operacyjny | Sterownik |
| -------- |------------- |
| Windows Server 2016<br/><br/>Windows 10 | [Siatka 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=836843) (.exe) |
| Windows Server 2012 R2 | [Siatka 6.2 (391.81)](https://go.microsoft.com/fwlink/?linkid=836844) (.exe)  |