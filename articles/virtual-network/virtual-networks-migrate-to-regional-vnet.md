---
title: Migrowanie sieci wirtualnej platformy Azure (klasycznej) z grupy koligacji do regionu | Microsoft Docs
description: Dowiedz się, jak przeprowadzić migrację sieci wirtualnej (klasycznej) z grupy koligacji do regionu.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: d33d9ec4eadeaa3a082103f1ad699e2fc3010e3b
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058395"
---
# <a name="migrate-a-virtual-network-classic-from-an-affinity-group-to-a-region"></a>Migrowanie sieci wirtualnej (klasycznej) z grupy koligacji do regionu

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większość nowych wdrożeń korzystała z modelu wdrażania Menedżer zasobów.

Grupy koligacji zapewniają, że zasoby utworzone w ramach tej samej grupy koligacji są fizycznie hostowane przez serwery, które są blisko siebie, co umożliwia szybkie komunikowanie się tych zasobów. W przeszłości grupy koligacji były wymagane do tworzenia sieci wirtualnych (klasycznych). W tym czasie usługa Menedżera sieci, która zarządza sieciami wirtualnymi (klasyczna), może funkcjonować tylko w ramach zestawu serwerów fizycznych lub jednostki skalowania. Udoskonalenia architektury zwiększyły zakres zarządzania siecią do regionu.

W wyniku tych ulepszeń architektury grupy koligacji nie są już zalecane lub wymagane w przypadku sieci wirtualnych (klasycznych). Używanie grup koligacji dla sieci wirtualnych (klasycznych) jest zastępowane przez regiony. Sieci wirtualne (klasyczne), które są skojarzone z regionami, są nazywane regionalnymi sieciami wirtualnymi.

Zaleca się, aby nie używać grup koligacji w ogóle. W odniesieniu do wymagań sieci wirtualnej grupy koligacji były również ważne do użycia w celu zapewnienia, że zasoby, takie jak obliczenia (klasyczne) i magazyn (klasyczne), były umieszczone blisko siebie. Jednak w przypadku bieżącej architektury sieci platformy Azure te wymagania dotyczące umieszczania nie są już potrzebne.

> [!IMPORTANT]
> Mimo że nadal jest technicznie możliwe utworzenie sieci wirtualnej skojarzonej z grupą koligacji, nie ma żadnych istotnych przyczyn. Wiele funkcji sieci wirtualnych, takich jak grupy zabezpieczeń sieci, jest dostępnych tylko w przypadku korzystania z regionalnej sieci wirtualnej i nie są dostępne dla sieci wirtualnych skojarzonych z grupami koligacji.
> 
> 

## <a name="edit-the-network-configuration-file"></a>Edytuj plik konfiguracji sieciowej

1. Wyeksportuj plik konfiguracji sieci. Aby dowiedzieć się, jak wyeksportować plik konfiguracji sieci przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure (CLI) 1,0, zobacz [Konfigurowanie sieci wirtualnej przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md#export).
2. Edytuj plik konfiguracji sieciowej, zastępując **AffinityGroup** z **lokalizacją**. Określ [region](https://azure.microsoft.com/regions) platformy Azure dla **lokalizacji**.
   
   > [!NOTE]
   > **Lokalizacja** to region określony dla grupy koligacji skojarzonej z siecią wirtualną (klasyczną). Jeśli na przykład Twoja sieć wirtualna (klasyczna) jest skojarzona z grupą koligacji, która znajduje się w regionie zachodnie stany USA, **Lokalizacja** musi wskazywać zachodnie stany USA. 
   > 
   > 
   
    Edytuj następujące wiersze w pliku konfiguracji sieci, zastępując wartości własnymi: 
   
    **Stara wartość:** \<VirtualNetworkSitename = "VNetUSWest" AffinityGroup = "VNetDemoAG"\> 
   
    **Nowa wartość:** \<VirtualNetworkSitename = "VNetUSWest" Location = "zachodnie stany USA"\>
3. Zapisz zmiany i zaimportuj konfigurację sieci na platformę Azure. [](virtual-networks-using-network-configuration-file.md#import)

> [!NOTE]
> Ta migracja nie powoduje żadnego przestoju usług.
> 
> 

## <a name="what-to-do-if-you-have-a-vm-classic-in-an-affinity-group"></a>Co zrobić, jeśli masz maszynę wirtualną (klasyczną) w grupie koligacji
Maszyny wirtualne (klasyczne), które obecnie znajdują się w grupie koligacji, nie muszą zostać usunięte z grupy koligacji. Po wdrożeniu maszyny wirtualnej zostanie ona wdrożona w jednej jednostce skalowania. Grupy koligacji mogą ograniczyć zbiór dostępnych rozmiarów maszyn wirtualnych dla nowego wdrożenia maszyny wirtualnej, ale istniejąca wdrożona maszyna wirtualna jest już ograniczona do zestawu rozmiarów maszyn wirtualnych dostępnych w jednostce skalowania, w której wdrożono maszynę wirtualną. Ponieważ maszyna wirtualna została już wdrożona w jednostce skalowania, usunięcie maszyny wirtualnej z grupy koligacji nie ma wpływu na maszynę wirtualną.
