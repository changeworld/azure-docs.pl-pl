---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 50d896f2a835136316945b26a3191861125faa25
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68306118"
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nvidia-cuda-drivers"></a>Sterowniki NVIDIA CUDA

Sterowniki NVIDIA CUDA dla maszyn wirtualnych z serii NC, NCv2, Seria NCV3, ND i NDv2 (opcjonalne dla serii NV) są obsługiwane tylko w przypadku dystrybucji systemu Linux wymienionych w poniższej tabeli. Informacje o sterowniku CUDA są aktualne w momencie publikacji. Najnowsze sterowniki CUDA można znaleźć w witrynie internetowej [firmy NVIDIA](https://developer.nvidia.com/cuda-zone) . Upewnij się, że instalujesz lub uaktualniasz do najnowszych sterowników CUDA dla dystrybucji. 

> [!TIP]
> Alternatywą dla ręcznej instalacji sterownika CUDA na maszynie wirtualnej z systemem Linux jest wdrożenie obrazu [Data Science Virtual Machine](../articles/machine-learning/data-science-virtual-machine/overview.md) platformy Azure. Wersje DSVM dla Ubuntu 16,04 LTS lub CentOS 7,4 wstępnie instalują sterowniki NVIDIA CUDA, bibliotekę sieciową CUDA głębokiej neuronowych oraz inne narzędzia.

| Dystrybucja | Sterownik |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7,3, 7,4, 7,5, 7,6<br/><br/> CentOS-based 7,3, 7,4, 7,5, 7,6, CentOS-based 7,4 | NVIDIA CUDA 10,1, gałąź sterownika R418 |

### <a name="nvidia-grid-drivers"></a>Sterowniki sieci NVIDIA

Firma Microsoft redystrybuuje Instalatory sterowników NVIDIA GRID dla maszyn wirtualnych z serii NV i NVv3 używanych jako wirtualne stacje robocze lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych usługi Azure NV, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują Licencjonowanie oprogramowania wirtualnej procesora GPU na platformie Azure. Nie trzeba konfigurować serwera licencji oprogramowania NVIDIA vGPU.

| Dystrybucja | Sterownik |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7,0 do 7,6<br/><br/>CentOS-based 7,0 do 7,6<br/><br/>SUSE Linux Enterprise Server 12 z dodatkiem SP2 | NVIDIA GRID 9,0, gałąź sterownika R430|

> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
