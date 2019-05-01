---
title: Limity przydziału, jednostki SKU i dostępność regionów w usłudze Azure Kubernetes Service (AKS)
description: Więcej informacji na temat domyślne limity przydziału, rozmiary jednostki SKU maszyny Wirtualnej węzła ograniczone i dostępność regionów dla usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: iainfou
ms.openlocfilehash: abeb9ef6e467b62cf7332e01e1b77c710b9ba4f4
ms.sourcegitcommit: 524625dd12e0537173616a991802075e2dc9da12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2019
ms.locfileid: "64413033"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Limity przydziału, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionów w usłudze Azure Kubernetes Service (AKS)

Wszystkich usług platformy Azure dotyczą określone limity i przydziały dla zasobów i funkcji. Rozmiar węzła niektórych maszyn wirtualnych (VM) jednostek SKU są następnie ograniczyć do użycia.

Ten artykuł szczegółowo opisuje domyślne limity zasobów dla zasobów usługi Azure Kubernetes Service (AKS), jak również dostępność usługi AKS w regionach platformy Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruktura aprowizowana

Do infrastruktury aprowizowanej mają zastosowanie wszystkie inne ograniczenia dotyczące sieci, mocy obliczeniowej i magazynu. Zobacz [Azure subscription and service limits](../azure-subscription-service-limits.md) (Limity subskrypcji i usługi Azure), aby sprawdzić odpowiednie limity.

## <a name="restricted-vm-sizes"></a>Ograniczone rozmiarów maszyn wirtualnych

Każdy węzeł w klastrze AKS zawiera ustaloną ilość zasobów obliczeniowych, takich jak pamięci i procesorów wirtualnych. Węzłów AKS zawiera zasoby obliczeniowe niewystarczający, może nie działać poprawnie zasobników. Aby upewnić się, że wymagane *systemu kubernetes* niezawodnie można zaplanować zasobników i aplikacji, nie można używać następujących jednostek SKU maszyn wirtualnych w usłudze AKS:

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

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
