---
title: Migracja sieci wirtualnej platformy Azure (klasyczne) z grupą koligacji w regionie | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przeprowadzić migrację sieci wirtualnej (klasyczne) z grupą koligacji do regionu.
services: virtual-network
documentationcenter: na
author: genli
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
ms.openlocfilehash: b8b4fbdbf92e49a0287cd6c440513213279d24d0
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migracji sieci wirtualnej (klasyczne) z grupą koligacji w regionie

> [!IMPORTANT]
> Platforma Azure ma dwa różne modele wdrażania do tworzenia i pracy z zasobami: [Resource Manager i Model Klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większości nowych wdrożeń korzystać modelu wdrażania usługi Resource Manager.

Grupy koligacji upewnij się, że zasoby utworzone w tej samej grupie koligacji fizycznie są obsługiwane przez serwery, które są blisko siebie włączenie tych zasobów do komunikowania się szybszy. W przeszłości grupy koligacji były wymagane do tworzenia sieci wirtualnych (klasyczny). W tym czasie usługa Menedżera sieci, zarządzaną sieciami wirtualnymi (klasyczne) można pracować tylko w ramach zestawu serwerów fizycznych lub jednostki skalowania. Ulepszenia architektury nastąpiło nasilenie zakres zarządzania siecią w regionie.

W wyniku tych usprawnień architektury grup koligacji nie jest już zalecane lub wymagane dla sieci wirtualnych (klasyczny). Sposób korzystania z grup koligacji dla sieci wirtualnych (klasyczne) zastępuje regionów. Sieci wirtualne (klasyczne) skojarzonych z regiony są nazywane regionalnych sieci wirtualnych.

Zaleca się, że grupy koligacji nie używaj ogólnie. Oprócz wymagań sieci wirtualnej grupy koligacji były również należy użyć, aby zapewnić zasoby, takie jak zasobów obliczeniowych (klasyczne) i magazynu (klasyczne), zostały umieszczone obok siebie. Jednak z bieżącej architektury sieci platformy Azure, te wymagania umieszczania nie są już wymagane.

> [!IMPORTANT]
> Jest on nadal technicznie możliwe utworzyć sieć wirtualną, która jest skojarzona z grupą koligacji, nie istnieje w tym celu atrakcyjnych przyczyna. Wiele funkcji sieci wirtualnej, takich jak grupy zabezpieczeń sieciowych, są dostępne tylko po użyciu regionalną sieć wirtualną, a nie są dostępne dla sieci wirtualnych, które są skojarzone z grup koligacji.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Przeprowadź edycję pliku konfiguracji sieci

1. Eksportowanie plików konfiguracji sieci. Aby dowiedzieć się, jak wyeksportować plik konfiguracji sieci przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI) 1.0, zobacz [skonfigurować sieć wirtualną przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md#export).
2. Edytowanie pliku konfiguracji sieci, zastępując **AffinityGroup** z **lokalizacji**. Określ Azure [region](https://azure.microsoft.com/regions) dla **lokalizacji**.
   
   > [!NOTE]
   > **Lokalizacji** to region określona dla grupy koligacji, która jest skojarzona z sieci wirtualnej (klasyczny). Na przykład, jeśli w Twojej sieci wirtualnej (klasyczne) jest skojarzona z grupą koligacji znajdującego się w zachodnie stany USA, podczas migracji Twoje **lokalizacji** musi wskazywać zachodnie stany USA. 
   > 
   > 
   
    Edytuj zastąpionymi wartościami z własnego następujące wiersze w pliku konfiguracji sieci: 
   
    **Stara wartość:** \<VirtualNetworkSitename = AffinityGroup "VNetUSWest" = "VNetDemoAG"\> 
   
    **Nowa wartość:** \<VirtualNetworkSitename = "VNetUSWest" Lokalizacja = "Zachodnie stany USA"\>
3. Zapisz zmiany i [zaimportować](virtual-networks-using-network-configuration-file.md#import) konfigurację sieci na platformie Azure.

> [!NOTE]
> Ta migracja nie powoduje żadnych przestojów do usług.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Co zrobić, jeśli masz maszyny Wirtualnej (klasyczne) w grupie koligacji
Maszyny wirtualne (klasyczne) są obecnie w grupie koligacji nie trzeba można usunąć z grupy koligacji. Po wdrożeniu maszyny Wirtualnej jest wdrażana na jednostkę skalowania pojedynczego. Grupy koligacji, można ograniczyć zestaw dostępnych rozmiarów maszyn wirtualnych do nowego wdrożenia maszyny Wirtualnej, ale istniejącej maszyny Wirtualnej, które zostało wdrożone już jest ograniczony do zestawu rozmiary maszyny Wirtualnej dostępne jednostki skalowania, w której maszyna wirtualna jest wdrożona. Ponieważ maszyna wirtualna została już wdrożona na jednostkę skalowania, usunięcie maszyny Wirtualnej z grupy koligacji nie ma wpływu na maszynie Wirtualnej.
