---
title: Limity przydziałów i dostępność regionów dla usługi Azure Kubernetes Service (AKS)
description: Domyślne limity przydziałów i dostępność regionów dla usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: overview
ms.date: 06/13/2018
ms.author: iainfou
ms.openlocfilehash: 6d4fa9a06f3baeb51505c3e6060e9689428f8386
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37100723"
---
# <a name="quotas-and-region-availability-for-azure-kubernetes-service-aks"></a>Limity przydziałów i dostępność regionów dla usługi Azure Kubernetes Service (AKS)

Wszystkich usług platformy Azure dotyczą określone limity i przydziały dla zasobów i funkcji. W poniższych sekcjach szczegółowo opisano domyślne limity zasobów dla kilku zasobów usługi Azure Kubernetes Service (AKS), jak również dostępność usługi AKS w regionach świadczenia usługi Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruktura aprowizowana

Do infrastruktury aprowizowanej mają zastosowanie wszystkie inne ograniczenia dotyczące sieci, mocy obliczeniowej i magazynu. Zobacz [Azure subscription and service limits](../azure-subscription-service-limits.md) (Limity subskrypcji i usługi Azure), aby sprawdzić odpowiednie limity.

## <a name="region-availability"></a>Dostępność w danym regionie

Usługa Azure Kubernetes Service (AKS) jest dostępna w następujących regionach:

- Australia Wschodnia
- Kanada Środkowa
- Kanada Wschodnia
- Środkowe stany USA
- Wschodnie stany USA
- Europa Północna
- Południowe Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
