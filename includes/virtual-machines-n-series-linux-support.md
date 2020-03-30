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
ms.openlocfilehash: 5dccdb6c357635e78b076b1560bf6c0c62c03753
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77135112"
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nvidia-cuda-drivers"></a>Sterowniki NVIDIA CUDA

Sterowniki NVIDIA CUDA dla maszyn wirtualnych z serii NC, NCv2, NCv3, ND i NDv2 (opcjonalnie dla serii NV) są obsługiwane tylko w dystrybucjach systemu Linux wymienionych w poniższej tabeli. Informacje o sterowniku CUDA są aktualne w momencie publikacji. Najnowsze sterowniki CUDA można znaleźć na stronie internetowej [firmy NVIDIA.](https://developer.nvidia.com/cuda-zone) Upewnij się, że zainstalujesz lub uaktualnisz do najnowszych sterowników CUDA dla twojej dystrybucji. 

> [!TIP]
> Jako alternatywę dla ręcznej instalacji sterownika CUDA na maszynie wirtualnej z systemem Linux można wdrożyć obraz [maszyny wirtualnej nauki o danych](../articles/machine-learning/data-science-virtual-machine/overview.md) platformy Azure. Wersje DSVM dla Ubuntu 16.04 LTS lub CentOS 7.4 wstępnie instalują sterowniki NVIDIA CUDA, bibliotekę sieci neuronowych CUDA i inne narzędzia.

| Dystrybucja | Sterownik |
| --- | -- | 
| Ubuntu 16.04 LTS, 18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3, 7.4, 7.5, 7.6<br/><br/> Oparte na centos 7.3, 7.4, 7.5, 7.6, CentOS oparte 7.4 HPC | NVIDIA CUDA 10.1, odgałęzienie sterowników R418 |

### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA GRID

Firma Microsoft redystrybuuje instalatory sterowników NVIDIA GRID dla maszyn wirtualnych z serii NV i NVv3 używanych jako wirtualne stacje robocze lub do aplikacji wirtualnych. Zainstaluj tylko te sterowniki GRID na maszynach wirtualnych usługi Azure NV, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Sterowniki te obejmują licencjonowanie oprogramowania GRID Virtual GPU na platformie Azure. Nie trzeba konfigurowanić serweru licencji nvidia vGPU.

| Dystrybucja | Sterownik |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.0 do 7.6<br/><br/>CentOS-based 7,0 do 7,6<br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 10.1, odgałęzienie sterowników R440|

> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
