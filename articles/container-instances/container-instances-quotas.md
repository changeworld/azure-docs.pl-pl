---
title: Limity przydziałów i dostępność regionów dla usługi Azure Container Instances
description: Limity przydziałów, ograniczenia i dostępność regionów dla usługi Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/15/2019
ms.author: danlep
ms.openlocfilehash: c676989b4b882f2b1887a1b6a5091b60027f61d0
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/16/2019
ms.locfileid: "56328416"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Limity przydziałów i dostępność regionów dla usługi Azure Container Instances

Wszystkich usług platformy Azure dotyczą określone limity i przydziały dla zasobów i funkcji. W poniższych sekcjach szczegółowo opisano domyślne limity dla kilku zasobów usługi Azure Container Instances, jak również dostępność tej usługi w regionach świadczenia usługi Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="feature-availability"></a>Dostępność funkcji

Usługa Azure Container Instances umożliwia planowanie kontenerów systemów Windows i Linux przy użyciu tego samego interfejsu API. Jednak poniższe funkcje są obecnie dostępne tylko w grupach kontenerów systemu Linux. Planowane jest wprowadzenie obsługi w systemie Windows.

* Wiele kontenerów na grupę kontenerów
* Instalowanie woluminów (Azure Files, emptyDir, GitRepo, secret)
* Sieć wirtualna (wersja zapoznawcza)
* Zasoby procesora GPU (wersja zapoznawcza)

## <a name="region-availability"></a>Dostępność w danym regionie

Usługa Azure Container Instances jest dostępna w następujących regionach z określonymi limitami dotyczącymi procesora CPU i pamięci w każdej grupie kontenerów. Wartości są aktualne w momencie publikacji. Aby uzyskać aktualne informacje, należy użyć interfejsu API [List Capabilities](/rest/api/container-instances/listcapabilities/listcapabilities). 

Limity dostępności i zasobów mogą się różnić w zależności od tego, czy usługa Azure Container Instances jest używana z [siecią wirtualną](container-instances-vnet.md) (wersja zapoznawcza), czy z [zasobami procesora GPU](container-instances-gpu.md) (wersja zapoznawcza).

| Lokalizacja | System operacyjny | Procesor CPU | Pamięć (GB) |
| -------- | -- | :---: | :-----------: |
| Kanada Środkowa, Południowo-środkowe stany USA, Środkowe stany USA, Wschodnie stany USA 2 | Linux | 4 | 16 |
| Wschodnie stany USA, Europa Północna, Europa Zachodnia, Zachodnie stany USA, Zachodnie stany USA 2 | Linux | 4 | 14 |
| Japonia Wschodnia | Linux | 2 | 8 |
| Australia Wschodnia, Azja Południowo-Wschodnia | Linux | 2 | 7 |
| Indie Środkowe, Azja Wschodnia, Północno-środkowe stany USA, Indie Południowe | Linux | 2 | 3,5 |
| Wschodnie stany USA, Europa Zachodnia, Zachodnie stany USA | Windows | 4 | 14 |
| Australia Wschodnia, Kanada Środkowa, Indie Środkowe, Środkowe stany USA, Azja Wschodnia, Wschodnie stany USA 2, Japonia Wschodnia, Północno-środkowe stany USA, Europa Północna, Południowo-środkowe stany USA, Indie Południowe, Azja Południowo-Wschodnia, Zachodnie stany USA 2 | Windows | 2 | 3,5 |

Wystąpienia kontenerów utworzone w ramach tych limitów zasobów podlegają dostępności w obrębie regionu wdrożenia. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby uniknąć niepowodzenia wdrażania, spróbuj wdrożyć wystąpienia z niższymi ustawieniami procesora CPU i pamięci lub spróbuj przeprowadzić wdrożenie w późniejszym terminie.

Poinformuj zespół o dodatkowych wymaganych regionach lub zwiększeniu limitów procesora/pamięci na stronie [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów z wdrażaniem wystąpienia kontenera, zobacz [Rozwiązywanie problemów z wdrażaniem przy użyciu usługi Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
