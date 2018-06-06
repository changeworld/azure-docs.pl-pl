---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0264f92fa10bd503a2811ce40ee0b8d4edd5f3b1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34669836"
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nvidia-cuda-drivers"></a>Sterowniki NVIDIA CUDA

Sterowniki NVIDIA CUDA NC, NCv2 NCv3 i maszyn wirtualnych serii ND (opcjonalny w przypadku serii wirtualizacją sieci) są obsługiwane tylko w dystrybucje systemu Linux wymienione w poniższej tabeli. Informacji o sterowniku CUDA są aktualne w momencie publikacji. Najnowsze sterowniki CUDA, odwiedź stronę [NVIDIA](https://developer.nvidia.com/cuda-zone) witryny sieci Web. Upewnij się, czy instalacji lub uaktualnienia do najnowszej wersji sterowników CUDA dla dystrybucji. 

> [!TIP]
> Alternatywą dla ręcznego CUDA instalacja sterownika maszyny wirtualnej systemu Linux platformy Azure można wdrożyć [maszyny wirtualnej nauki danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje DSVM Ubuntu 16.04 LTS i CentOS 7.4 wstępnie instalować sterowniki NVIDIA CUDA, CUDA głębokie Neuronowej biblioteki sieciowej i inne narzędzia.

| Dystrybucja | Sterownik |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 lub 7.4<br/><br/> Na podstawie centOS 7.3 lub 7.4, na podstawie CentOS 7.4 HPC | NVIDIA CUDA 9.1, sterownika gałęzi R390 |

### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA siatki

Microsoft ponownie dystrybuuje instalatorów sterownika NVIDIA siatki dla serii wirtualizacją sieci maszyn wirtualnych użyć jako wirtualne stacje robocze lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych z wirtualizacją sieci Azure, tylko na dystrybucje wymienione w poniższej tabeli. Te sterowniki obejmują licencjonowania siatki wirtualnej oprogramowania procesora GPU na platformie Azure.

| Dystrybucja | Sterownik |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 lub 7.4<br/><br/>CentOS 7.3 lub 7.4 | NVIDIA siatki w wersji 6.0 sterownika gałęzi R390|



> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
