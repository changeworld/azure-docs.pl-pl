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
ms.openlocfilehash: 1610ea93eed03fe6efe28e63a7151409e1946f5b
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38988718"
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
- Wschodnie stany USA 2
- Japonia Wschodnia
- Europa Północna
- Południowe Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
