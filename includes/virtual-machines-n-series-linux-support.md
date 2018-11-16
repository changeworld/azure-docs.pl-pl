---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 11/08/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: eef61421de2a87750caac228d12888421f7442a8
ms.sourcegitcommit: 275eb46107b16bfb9cf34c36cd1cfb000331fbff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/15/2018
ms.locfileid: "51716242"
---
## <a name="supported-distributions-and-drivers"></a>Obsługiwane dystrybucje i sterowniki

### <a name="nvidia-cuda-drivers"></a>Sterowniki NVIDIA CUDA

Sterowniki NVIDIA CUDA dla NC, NCv2, NCv3, ND i NDv2 maszyny wirtualne z serii (opcjonalnie na potrzeby seria NV) są obsługiwane tylko na dystrybucje systemu Linux, wymienione w poniższej tabeli. Informacje o sterownik CUDA są aktualne w momencie publikacji. Aby uzyskać najnowsze sterowniki CUDA, odwiedź stronę [NVIDIA](https://developer.nvidia.com/cuda-zone) witryny sieci Web. Upewnij się, instalowania lub uaktualnienia do najnowsze sterowniki CUDA dla Twojej dystrybucji. 

> [!TIP]
> Jako alternatywy dla ręcznej instalacji sterownik CUDA na maszynie Wirtualnej systemu Linux, możesz wdrożyć platformy Azure [maszyny wirtualnej do nauki o danych](../articles/machine-learning/data-science-virtual-machine/overview.md) obrazu. Wersje maszyny DSVM dla Ubuntu 16.04 LTS i CentOS 7.4 przed instalacją sterowniki NVIDIA CUDA, CUDA głębokiego neuronowych biblioteki sieciowej i innych narzędzi.

| Dystrybucja | Sterownik |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux w wersji 7.3 lub wersji 7.4<br/><br/> Opartych na systemie centOS 7.3 lub 7.4, opartych na systemie CentOS 7.4 HPC | NVIDIA CUDA 10.0, gałąź sterownika R410 |

### <a name="nvidia-grid-drivers"></a>Sterowniki NVIDIA GRID

Microsoft dystrybuuje instalatory sterownik technologii NVIDIA GRID NV i maszyny wirtualne z serii NVv2 używany jako wirtualnych stacji roboczych lub aplikacje wirtualne. Zainstaluj tylko te sterowniki siatki na maszynach wirtualnych serii NV Azure, tylko w systemach operacyjnych wymienionych w poniższej tabeli. Te sterowniki obejmują licencji na program siatki wirtualnej oprogramowania procesora GPU na platformie Azure. Nie trzeba skonfigurować serwer licencji oprogramowania vGPU firmy NVIDIA.

| Dystrybucja | Sterownik |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux w wersji 7.3 lub wersji 7.4<br/><br/>Opartych na systemie centOS 7.3 lub wersji 7.4 | Procesory GPU NVIDIA GRID 6.2, gałąź sterownika R390|

> [!WARNING] 
> Instalacja oprogramowania innych firm na produktach systemu Red Hat może wpłynąć na warunki wsparcia systemu Red Hat. Zobacz [artykuł bazy wiedzy systemu Red Hat](https://access.redhat.com/articles/1067).
>
