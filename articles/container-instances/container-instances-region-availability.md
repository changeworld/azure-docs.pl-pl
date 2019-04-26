---
title: Dostępność zasobów w usłudze Azure Container Instances
description: Dostępność zasobów obliczeniowych i pamięci dla usługi Azure Container Instances w różnych regionach platformy Azure.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 03/01/2019
ms.author: danlep
ms.openlocfilehash: 1ca23a95c746139963aa70ed20bb888152fd5cd8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60537754"
---
# <a name="resource-availability-for-azure-container-instances-in-azure-regions"></a>Dostępność zasobów dla usługi Azure Container Instances w regionach platformy Azure

Ten artykuł szczegółowo opisuje dostępności zasobów obliczeniowych i pamięci usługi Azure Container Instances w regionach platformy Azure. 

Wartości prezentowane są maksymalna zasobów dostępnych na wdrożenie [grupy kontenerów](container-instances-container-groups.md). Wartości są aktualne w momencie publikacji. Aby uzyskać aktualne informacje, należy użyć interfejsu API [List Capabilities](/rest/api/container-instances/listcapabilities/listcapabilities). 

> [!NOTE]
> Grupy kontenerów utworzone w ramach tych limitów zasobów podlegają dostępności w obrębie regionu wdrożenia. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby uniknąć niepowodzenia wdrażania, spróbuj wdrożyć wystąpienia z niższym ustawień zasobów, lub spróbuj przeprowadzić wdrożenie w późniejszym czasie.

Aby uzyskać informacje o limitach przydziałów i inne limity we wdrożeniach, zobacz [przydziały i limity dla usługi Azure Container Instances](container-instances-quotas.md).

## <a name="availability---general"></a>Dostępność — ogólne

| Lokalizacja | System operacyjny | Procesor CPU | Pamięć (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada Środkowa, Południowo-środkowe stany USA, Środkowe stany USA, Wschodnie stany USA 2 | Linux | 4 | 16 |
| Wschodnie stany USA, Europa Północna, Europa Zachodnia, Zachodnie stany USA, Zachodnie stany USA 2 | Linux | 4 | 14 |
| Japonia Wschodnia | Linux | 2 | 8 |
| Australia Wschodnia, Azja Południowo-Wschodnia | Linux | 2 | 7 |
| Indie Środkowe, Azja Wschodnia, Północno-środkowe stany USA, Indie Południowe | Linux | 2 | 3,5 |
| Wschodnie stany USA, Europa Zachodnia, Zachodnie stany USA | Windows | 4 | 14 |
| Australia Wschodnia, Kanada Środkowa, Indie Środkowe, Środkowe stany USA, Azja Wschodnia, Wschodnie stany USA 2, Japonia Wschodnia, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Indie Południowe, Azja Południowo-Wschodnia, Zachodnie stany USA 2 | Windows | 2 | 3,5 |

## <a name="availability---virtual-network-deployment-preview"></a>Dostępność — wdrożenia sieci wirtualnej (wersja zapoznawcza)

Następujących regionów i zasoby są dostępne dla grupy kontenerów, wdrożonych w [sieci wirtualnej platformy Azure](container-instances-vnet.md) (wersja zapoznawcza)

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="availability---gpu-resources-preview"></a>Dostępność — zasoby procesora GPU (wersja zapoznawcza)

Następujących regionów i zasoby są dostępne dla grupy kontenerów, wdrożona za pomocą [zasoby procesora GPU](container-instances-gpu.md) (wersja zapoznawcza)

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]
[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

## <a name="next-steps"></a>Kolejne kroki

Powiadamiaj zespół wiedzieć, czy chcesz się dodatkowych regionów lub dostępność zasobów zwiększona [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Aby uzyskać informacje dotyczące rozwiązywania problemów z wdrażaniem wystąpienia kontenera, zobacz [Rozwiązywanie problemów dotyczących wdrażania za pomocą usługi Azure Container Instances](container-instances-troubleshooting.md).
