---
title: Dostępność zasobów w usłudze Azure Container Instances
description: Dostępność zasobów obliczeniowych i pamięci dla usługi Azure Container Instances w różnych regionach platformy Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 05/14/2019
ms.author: danlep
ms.openlocfilehash: 64b60178413e470cc7fe9b3991c6fc29b5a0f860
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794293"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostępność zasobów dla usługi Azure Container Instances w regionach platformy Azure

Ten artykuł szczegółowo opisuje dostępności zasobów obliczeniowych i pamięci usługi Azure Container Instances w regionach platformy Azure. 

Wartości prezentowane są maksymalna zasobów dostępnych na wdrożenie [grupy kontenerów](container-instances-container-groups.md). Wartości są aktualne w momencie publikacji. 

> [!NOTE]
> Grupy kontenerów utworzone w ramach tych limitów zasobów podlegają dostępności w obrębie regionu wdrożenia. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby uniknąć niepowodzenia wdrażania, spróbuj wdrożyć wystąpienia z niższym ustawień zasobów, lub spróbuj przeprowadzić wdrożenie w późniejszym czasie.

Aby uzyskać informacje o limitach przydziałów i inne limity we wdrożeniach, zobacz [przydziały i limity dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostępność — ogólne

Następujące regiony i zasoby są dostępne dla grupy kontenerów z systemem Linux i [obsługiwane](container-instances-faq.md#what-windows-base-os-images-are-supported) kontenerów opartych na systemie Windows Server 2016.

| Lokalizacja | System operacyjny | CPU | Pamięć (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada Środkowa, Indie środkowe, środkowe stany USA, Azja Wschodnia, wschodnie stany USA, wschodnie stany USA 2, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, południowe Zjednoczone Królestwo, zachodnie stany USA | Linux | 4 | 16 |
| Europa Zachodnia, zachodnie stany USA 2 | Linux | 4 | 14 |
| Australia Wschodnia, Japonia Wschodnia | Linux | 2 | 8 |
| Północnośrodkowa Ameryka, Indie Południowe | Linux | 2 | 3,5 |
| Europa Zachodnia | Windows | 4 | 16 |
| Zachodnie stany USA, Wschodnie stany USA | Windows | 4 | 14 |
| Australia Wschodnia, Kanada Środkowa, Indie środkowe, środkowe stany USA, Azja Wschodnia, wschodnie stany USA 2, Japonia, część wschodnia, Indie Południowe Północna środkowe stany USA, Europa Północna, południowo-środkowe stany USA, Azja południowo-wschodnia, południowe Zjednoczone Królestwo, zachodnie stany USA 2 | Windows | 2 | 3,5 |

## <a name="availability---windows-server-2019-ltsc-1809-deployments-preview"></a>Dostępność — LTSC 2019 r Server w systemie Windows, 1809 wdrożenia (wersja zapoznawcza)

Następujące regiony i zasoby są dostępne dla grupy kontenerów za pomocą kontenerów na podstawie 2019 r Server systemu Windows (wersja zapoznawcza).

| Lokalizacja | System operacyjny | CPU | Pamięć (GB) |
| -------- | -- | :---: | :-----------: |
| Azja południowo-wschodnia, Europa Północna, Europa Zachodnia, środkowe stany USA, wschodnie stany USA, zachodnie stany USA, zachodnie stany USA 2 | Windows | 4 | 16 |
| Wschodnie stany USA 2 | Windows | 2 | 3,5 |


## <a name="availability---virtual-network-deployment-preview"></a>Dostępność — wdrożenia sieci wirtualnej (wersja zapoznawcza)

Następujących regionów i zasoby są dostępne dla grupy kontenerów, wdrożonych w [sieci wirtualnej platformy Azure](container-instances-vnet.md) (wersja zapoznawcza).

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostępność — zasoby procesora GPU (wersja zapoznawcza)

Następujących regionów i zasoby są dostępne dla grupy kontenerów, wdrożona za pomocą [zasoby procesora GPU](container-instances-gpu.md) (wersja zapoznawcza).

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Kolejne kroki

Powiadamiaj zespół wiedzieć, czy chcesz się dodatkowych regionów lub dostępność zasobów zwiększona [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Aby uzyskać informacje dotyczące rozwiązywania problemów z wdrażaniem wystąpienia kontenera, zobacz [Rozwiązywanie problemów dotyczących wdrażania za pomocą usługi Azure Container Instances](container-instances-troubleshooting.md).
