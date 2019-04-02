---
title: Limity przydziałów i dostępność regionów dla usługi Azure Kubernetes Service (AKS)
description: Domyślne limity przydziałów i dostępność regionów dla usługi Azure Kubernetes Service (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: iainfou
ms.openlocfilehash: 8feeaf2e8ee99405ed0de8291fc97dc50db6a386
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2019
ms.locfileid: "58805231"
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
- Australia Południowo-Wschodnia
- Kanada Środkowa
- Kanada Wschodnia
- Indie Środkowe
- Środkowe stany USA
- Azja Wschodnia
- Wschodnie stany USA
- Wschodnie stany USA 2
- Francja Środkowa
- Japonia Wschodnia
- Korea Środkowa
- Korea Południowa
- Europa Północna
- Azja Południowo-Wschodnia
- Środkowo-południowe stany USA
- Indie Południowe
- Południowe Zjednoczone Królestwo
- Zachodnie Zjednoczone Królestwo
- Europa Zachodnia
- Zachodnie stany USA
- Zachodnie stany USA 2

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Aby zażądać zwiększenia dotyczącego co najmniej jednego zasobu, dla którego takie zwiększenie jest obsługiwane, prześlij [żądanie pomocy technicznej platformy Azure][azure-support] (wybierz wartość „Limit przydziału” dla pozycji **Typ problemu**).

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
