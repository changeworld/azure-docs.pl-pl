---
title: Limity przydziału, jednostki SKU i dostępność regionów w usłudze Azure Kubernetes Service (AKS)
description: Więcej informacji na temat domyślne limity przydziału, rozmiary jednostki SKU maszyny Wirtualnej węzła ograniczone i dostępność regionów dla usługi Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: mlearned
ms.openlocfilehash: 318846cddecdf020e2e751d3a0b9e05fc83bba73
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2019
ms.locfileid: "67614551"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w usłudze Azure Kubernetes Service (AKS)

Wszystkich usług platformy Azure, Ustaw limity i przydziały dla zasobów i funkcji. Niektóre jednostki SKU maszyny wirtualnej (VM) są również może być używana.

Ten artykuł szczegółowo opisuje domyślne limity zasobów dla zasobów usługi Azure Kubernetes Service (AKS) i dostępność usługi AKS w regionach platformy Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruktura aprowizowana

Do infrastruktury aprowizowanej mają zastosowanie wszystkie inne ograniczenia dotyczące sieci, mocy obliczeniowej i magazynu. Aby sprawdzić odpowiednie limity, zobacz [limity usług i subskrypcji platformy Azure](../azure-subscription-service-limits.md).

> [!IMPORTANT]
> Podczas uaktualniania klastra usługi AKS dodatkowe zasoby są tymczasowo używane. Te zasoby obejmują dostępnych adresów IP w podsieci sieci wirtualnej lub limit przydziału procesorów wirtualnych maszyn wirtualnych. Użycie kontenerów systemu Windows Server (obecnie dostępna w wersji zapoznawczej w usłudze AKS), to jedyny zalecanych do zastosowania najnowszych aktualizacji w węzłach jest w trakcie operacji uaktualniania. Proces uaktualniania klastra nie powiodło się może wskazywać, że nie masz dostępny IP adres miejsca lub procesora wirtualnego vCPU limit przydziału do obsługi tych zasobów tymczasowych. Aby uzyskać więcej informacji na temat procesu uaktualniania węzła systemu Windows Server, zobacz [uaktualnienia pulę węzłów w usłudze AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Ograniczone rozmiarów maszyn wirtualnych

Każdy węzeł w klastrze AKS zawiera ustaloną ilość zasobów obliczeniowych, takich jak pamięci i procesorów wirtualnych. Jeśli węzeł AKS zawiera zasoby obliczeniowe za mało, zasobników może zakończyć się niepowodzeniem do poprawnego działania. Aby upewnić się, że wymagane *systemu kubernetes* zasobników i aplikacjach niezawodnie można zaplanować, nie należy używać następujących jednostek SKU maszyn wirtualnych w usłudze AKS:

- Standardowa_A0
- Standardowa_A1
- Standardowa_A1_v2
- Standard_B1s
- Standard_B1ms
- Standardowa_F1
- Standardowa_F1s

Aby uzyskać więcej informacji o typach maszyn wirtualnych i ich zasoby obliczeniowe, zobacz [rozmiary maszyn wirtualnych na platformie Azure][vm-skus].

## <a name="region-availability"></a>Dostępność w danym regionie

Najbardziej aktualną listę, gdzie można wdrożyć i uruchamiaj klastry, zobacz [dostępność w poszczególnych regionach AKS][region-availability].

## <a name="next-steps"></a>Kolejne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli zasób obsługuje wzrost, żądanie zwiększenia za pośrednictwem [żądania pomocy technicznej platformy Azure][azure-support] (dla **typ problemu**, wybierz opcję **przydziału**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
