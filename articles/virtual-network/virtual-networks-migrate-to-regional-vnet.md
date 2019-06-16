---
title: Migrowanie sieci wirtualnej platformy Azure (wersja klasyczna) z grupy koligacji do regionu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację sieci wirtualnej (klasycznej) z grupy koligacji do regionu.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: ''
tags: azure-service-management
ms.assetid: 84febcb9-bb8b-4e79-ab91-865ad9de41cb
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: genli
ms.openlocfilehash: d3bb93d12a217e6d9066d037ff92f071b6139ab3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60648639"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrowanie sieci wirtualnej (klasycznej) z grupy koligacji do regionu

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca się, że większości nowych wdrożeń korzystać z modelu wdrażania usługi Resource Manager.

Grupy koligacji upewnij się, że zasoby utworzone w ramach tej samej grupie koligacji fizycznie znajdują się przez serwery, które znajdują się blisko siebie, włączanie tych zasobów do komunikowania się szybsze. W przeszłości grupy koligacji były wymagane do tworzenia sieci wirtualnych (klasyczne). W tym czasie usługa Menedżera sieci, która zarządzanych sieci wirtualne (klasyczne) może działać tylko w ramach zestawu serwerów fizycznych lub jednostki skalowania. Udoskonalenia architektury większą zakres zarządzania siecią w regionie.

W wyniku tych ulepszeń architektury grupy koligacji są już zalecane lub wymagane dla sieci wirtualne (klasyczne). Możliwość korzystania z grup koligacji dla sieci wirtualnych (model klasyczny) jest zastępowany przez regionów. Sieci wirtualne (klasyczne), które są skojarzone z regionów są nazywane regionalnych sieci wirtualnych.

Zaleca się, że nie używasz grup koligacji ogólnie rzecz biorąc. Oprócz wymagań sieci wirtualnej grupy koligacji zostały również ważne, aby użyć w celu zapewnienia zasobów, takich jak zasoby obliczeniowe (wersja klasyczna) i magazyn (klasyczny), zostały umieszczone obok siebie. Jednak przy użyciu bieżącego architektury sieci platformy Azure, te wymagania dotyczące umieszczania są już potrzebne.

> [!IMPORTANT]
> Chociaż nadal technicznie możliwe utworzyć sieć wirtualną, która jest skojarzona z grupą koligacji, nie ma żadnych istotny powód, aby to zrobić. Wiele funkcji sieci wirtualnej, takie jak sieciowe grupy zabezpieczeń, są dostępne tylko po użyciu regionalnej sieci wirtualnej, a nie są dostępne dla sieci wirtualnych, które są skojarzone z grup koligacji.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Edytuj plik konfiguracji sieci

1. Wyeksportuj plik konfiguracji sieci. Aby dowiedzieć się, jak wyeksportować plik konfiguracji sieci przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI) 1.0, zobacz [Skonfiguruj sieć wirtualną przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md#export).
2. Edytuj plik konfiguracji sieci, zastępując **AffinityGroup** z **lokalizacji**. Określ platformę Azure [region](https://azure.microsoft.com/regions) dla **lokalizacji**.
   
   > [!NOTE]
   > **Lokalizacji** to region, który został określony dla grupy koligacji, która jest skojarzona z siecią wirtualną (wersja klasyczna). Na przykład, jeśli sieć wirtualna (klasyczna) jest skojarzona z grupą koligacji, która znajduje się w regionie zachodnie stany USA, podczas migracji Twoje **lokalizacji** musi się odnosić do regionu zachodnie stany USA. 
   > 
   > 
   
    Edytuj następujące wiersze w pliku konfiguracyjnym sieci, zastępując wartości swoimi własnymi: 
   
    **Stara wartość:** \<VirtualNetworkSitename="VNetUSWest" AffinityGroup="VNetDemoAG"\> 
   
    **Nowa wartość:** \<VirtualNetworkSitename="VNetUSWest" Location="West US"\>
3. Zapisz zmiany i [zaimportować](virtual-networks-using-network-configuration-file.md#import) konfiguracja sieci na platformie Azure.

> [!NOTE]
> Ta migracja nie powoduje żadnych przestojów do usług.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Co zrobić, jeśli masz maszyny Wirtualnej (klasycznej) w grupie koligacji
Maszyny wirtualne (klasyczne) są obecnie w grupie koligacji nie trzeba usunąć z grupy koligacji. Po wdrożeniu maszyny Wirtualnej jest wdrażana jednostki skalowania pojedynczej. Grupy koligacji, można ograniczyć zestaw dostępnych rozmiarów maszyn wirtualnych na potrzeby nowego wdrożenia maszyny Wirtualnej, ale istniejącej maszyny Wirtualnej, która jest wdrażana jest już ograniczony zbiór dostępnych w jednostce skalowania, w którym wdrożono maszynę Wirtualną rozmiarów maszyn wirtualnych. Ponieważ maszyna wirtualna została już wdrożona na jednostkę skalowania, usuwając Maszynę wirtualną z grupy koligacji nie ma wpływu na maszynie Wirtualnej.
