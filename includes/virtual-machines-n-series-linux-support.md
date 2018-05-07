---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 7139ec67536a1c0e41c991db6d867b956f995c11
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC, NCv2 NCv3 i serii ND - NVIDIA CUDA sterowniki

CUDA sterownik informacje w poniższej tabeli są aktualne w momencie publikacji. Najnowsze sterowniki CUDA, odwiedź stronę [NVIDIA](https://developer.nvidia.com/cuda-zone) witryny sieci Web. Upewnij się, czy instalacji lub uaktualnienia do najnowszej wersji sterowników CUDA dla dystrybucji. 

> [!TIP]
> Alternatywą dla ręcznego CUDA instalacja sterownika maszyny wirtualnej systemu Linux platformy Azure można wdrożyć [maszyny wirtualnej nauki danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje DSVM Ubuntu 16.04 LTS i CentOS 7.4 wstępnie instalować sterowniki NVIDIA CUDA, CUDA głębokie Neuronowej biblioteki sieciowej i inne narzędzia.

| Dystrybucja | Sterownik |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 lub 7.4<br/><br/> Na podstawie centOS 7.3 lub 7.4, na podstawie CentOS 7.4 HPC | NVIDIA CUDA 9.1, sterownika gałęzi R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>Serii Trwałym - sterowniki NVIDIA siatki

Microsoft ponownie dystrybuuje instalatorów sterownika NVIDIA siatki dla maszyn wirtualnych z wirtualizacją sieci. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych z wirtualizacją sieci platformy Azure. Te sterowniki obejmują licencjonowania siatki wirtualnej oprogramowania procesora GPU na platformie Azure.

| Dystrybucja | Sterownik |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 lub 7.4<br/><br/>CentOS 7.3 lub 7.4 | NVIDIA siatki w wersji 6.0 sterownika gałęzi R390|



> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
