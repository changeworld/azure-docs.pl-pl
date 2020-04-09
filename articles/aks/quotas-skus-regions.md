---
title: Limity zasobów, jednostek SKU, regionów
titleSuffix: Azure Kubernetes Service
description: Dowiedz się więcej o domyślnych przydziałach, rozmiarach jednostek SKU maszyn wirtualnych z ograniczeniami i dostępności regionu usługi Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/09/2019
ms.openlocfilehash: 054d6ff4fc105d84192ac81feda97515f6cfae49
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886776"
---
# <a name="quotas-virtual-machine-size-restrictions-and-region-availability-in-azure-kubernetes-service-aks"></a>Przydziały, ograniczenia rozmiaru maszyny wirtualnej i dostępność regionu w usłudze Azure Kubernetes (AKS)

Wszystkie usługi platformy Azure ustawiają domyślne limity i przydziały zasobów i funkcji. Niektóre jednostki SKU maszyny wirtualnej są również ograniczone do użytku.

W tym artykule opisano domyślne limity zasobów usługi Azure Kubernetes Service (AKS) oraz dostępność usługi AKS w regionach platformy Azure.

## <a name="service-quotas-and-limits"></a>Limity i przydziały dotyczące usługi

[!INCLUDE [container-service-limits](../../includes/container-service-limits.md)]

## <a name="provisioned-infrastructure"></a>Infrastruktura aprowizowana

Do infrastruktury aprowizowanej mają zastosowanie wszystkie inne ograniczenia dotyczące sieci, mocy obliczeniowej i magazynu. Aby zapoznać się z odpowiednimi limitami, zobacz [Limity subskrypcji i usług platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md).

> [!IMPORTANT]
> Podczas uaktualniania klastra AKS dodatkowe zasoby są tymczasowo używane. Zasoby te obejmują dostępne adresy IP w podsieci sieci wirtualnej lub przydziału vCPU maszyny wirtualnej. Jeśli używasz kontenerów systemu Windows Server (obecnie w wersji zapoznawczej w aks), jedynym zatwierdzonym podejściem do zastosowania najnowszych aktualizacji do węzłów jest wykonanie operacji uaktualniania. Nieudany proces uaktualniania klastra może wskazywać, że nie masz dostępnego miejsca adresu IP lub przydziału procesora wirtualnego do obsługi tych zasobów tymczasowych. Aby uzyskać więcej informacji na temat procesu uaktualniania węzłów systemu Windows Server, zobacz [Uaktualnianie puli węzłów w programie AKS][nodepool-upgrade].

## <a name="restricted-vm-sizes"></a>Ograniczone rozmiary maszyn wirtualnych

Każdy węzeł w klastrze AKS zawiera stałą ilość zasobów obliczeniowych, takich jak procesor wirtualny i pamięć. Jeśli węzeł AKS zawiera niewystarczające zasoby obliczeniowe, zasobników może nie działać poprawnie. Aby upewnić się, że wymagane zasobniki *systemu kube* i aplikacje mogą być niezawodnie zaplanowane, **nie należy używać następujących jednostek SKU maszyn wirtualnych w aks:**

- Standardowa_A0
- Standardowa_A1
- Standardowa_A1_v2
- Standard_B1s
- Standard_B1ms
- Standardowa_F1
- Standardowa_F1s

Aby uzyskać więcej informacji na temat typów maszyn wirtualnych i ich zasobów obliczeniowych, zobacz [Rozmiary maszyn wirtualnych na platformie Azure.][vm-skus]

## <a name="region-availability"></a>Dostępność w danym regionie

Aby uzyskać najnowszą listę miejsc, w których można wdrażać i uruchamiać klastry, zobacz [Dostępność regionu AKS][region-availability].

## <a name="next-steps"></a>Następne kroki

Niektóre domyślne limity i przydziały mogą zostać zwiększone. Jeśli twój zasób obsługuje zwiększenie, zażądaj zwiększenia za pośrednictwem [żądania pomocy technicznej platformy Azure][azure-support] (dla typu **problemu**, wybierz **przydział).**

<!-- LINKS - External -->
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest
[region-availability]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

<!-- LINKS - Internal -->
[vm-skus]: ../virtual-machines/linux/sizes.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
