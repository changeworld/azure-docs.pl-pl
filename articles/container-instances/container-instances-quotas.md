---
title: Limity przydziałów i dostępność regionów dla usługi Azure Container Instances
description: Domyślne limity przydziałów i dostępność regionów dla usługi Azure Container Instances.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 02/27/2018
ms.author: danlep
ms.openlocfilehash: 2694e8cdc4f1918aab36794804ff48f5a70b44be
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2018
ms.locfileid: "50739689"
---
# <a name="quotas-and-region-availability-for-azure-container-instances"></a>Limity przydziałów i dostępność regionów dla usługi Azure Container Instances

Wszystkich usług platformy Azure dotyczą określone limity i przydziały dla zasobów i funkcji. W poniższych sekcjach szczegółowo opisano domyślne limity zasobów dla kilku zasobów usługi Azure Container Instances (ACI), jak również dostępność usługi ACI w regionach platformy Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-instances-limits](../../includes/container-instances-limits.md)]

## <a name="region-availability"></a>Dostępność w danym regionie

Usługa Azure Container Instances jest dostępna w następujących regionach z określonymi limitami dotyczącymi procesora CPU i pamięci.

| Lokalizacja | System operacyjny | Procesor CPU | Pamięć (GB) |
| -------- | -- | :---: | :-----------: |
| Wschodnie stany USA, Europa Północna, Europa Zachodnia, Zachodnie stany USA, Zachodnie stany USA 2 | Linux | 4 | 14 |
| Australia Wschodnia, Wschodnie stany USA 2, Azja Południowo-Wschodnia | Linux | 2 | 7 |
| Kanada Środkowa, Indie środkowe, Południowo-środkowe stany USA | Linux | 2 | 3,5 |
| Wschodnie stany USA, Europa Zachodnia, Zachodnie stany USA | Windows | 4 | 14 |
| Australia Wschodnia, Kanada Środkowa, Indie Środkowe, Wschodnie stany USA 2, Europa Północna, Południowo-środkowe stany USA, Azja Południowo-Wschodnia, Zachodnie stany USA 2 | Windows | 2 | 3,5 |

Wystąpienia kontenerów utworzone w ramach tych limitów zasobów podlegają dostępności w obrębie regionu wdrożenia. Gdy region jest mocno obciążony, wdrażanie wystąpień może zakończyć się niepowodzeniem. Aby uniknąć niepowodzenia wdrażania, spróbuj wdrożyć wystąpienia z niższymi ustawieniami procesora CPU i pamięci lub spróbuj przeprowadzić wdrożenie w późniejszym terminie.

Poinformuj zespół o dodatkowych wymaganych regionach lub zwiększeniu limitów procesora/pamięci na stronie [aka.ms/aci/feedback](https://aka.ms/aci/feedback).

Aby uzyskać więcej informacji dotyczących rozwiązywania problemów z wdrażaniem wystąpienia kontenera, zobacz [Rozwiązywanie problemów z wdrażaniem przy użyciu usługi Azure Container Instances](container-instances-troubleshooting.md).

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
