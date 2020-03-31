---
title: 'Azure ExpressRoute: Migrowanie klasycznych sieci wirtualnych do Menedżera zasobów'
description: Na tej stronie opisano sposób migracji sieci wirtualnych skojarzonych z programem ExpressRoute do Menedżera zasobów po przeniesieniu obwodu.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8033c80b72c19a9473ce7ecfaa8fe5a1da9f12ee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77061323"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrowanie sieci wirtualnych skojarzonych z usługą ExpressRoute z klasycznego do Menedżera zasobów

W tym artykule wyjaśniono, jak przeprowadzić migrację sieci wirtualnych skojarzonych z usługą ExpressRoute z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager po przeniesieniu obwodu usługi ExpressRoute. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Sprawdź, czy masz najnowsze wersje modułów programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview). Aby zainstalować moduł zarządzania usługami programu PowerShell (który jest potrzebny dla klasycznego modelu wdrażania), zobacz [Instalowanie modułu zarządzania usługami azure powershell](/powershell/azure/servicemanagement/install-azure-ps).
* Przed rozpoczęciem konfiguracji upewnij się, że zostały [sprawdzone wymagania wstępne,](expressroute-prerequisites.md) [wymagania dotyczące routingu](expressroute-routing.md)i [przepływy pracy.](expressroute-workflows.md)
* Przejrzyj informacje podane w obszarze [Przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów](expressroute-move.md). Upewnij się, że w pełni rozumiesz ograniczenia i ograniczenia.
* Sprawdź, czy obwód jest w pełni operacyjny w klasycznym modelu wdrażania.
* Upewnij się, że masz grupę zasobów, która została utworzona w modelu wdrażania Menedżera zasobów.
* Przejrzyj następującą dokumentację migracji zasobów:

    * [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Często zadawane pytania: Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Przegląd najczęstszych błędów i środków zaradczych migracji](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Scenariusze obsługiwane i nieobsługiwane

* Obwód usługi ExpressRoute można przenieść z klasycznego do środowiska Menedżera zasobów bez żadnych przestojów. Można przenieść dowolny obwód usługi ExpressRoute z klasycznego do środowiska Menedżera zasobów bez przestojów. Postępuj zgodnie z instrukcjami [podczas przenoszenia obwodów usługi ExpressRoute z klasycznego do modelu wdrażania Menedżera zasobów przy użyciu programu PowerShell](expressroute-howto-move-arm.md). Jest to warunek wstępny przenoszenia zasobów połączonych z siecią wirtualną.
* Sieci wirtualne, bramy i skojarzone wdrożenia w sieci wirtualnej, które są podłączone do obwodu usługi ExpressRoute w tej samej subskrypcji, mogą być migrowane do środowiska Menedżera zasobów bez żadnych przestojów. Można wykonać kroki opisane później do migracji zasobów, takich jak sieci wirtualne, bramy i maszyny wirtualne wdrożone w sieci wirtualnej. Należy upewnić się, że sieci wirtualne są poprawnie skonfigurowane przed ich migracją. 
* Sieci wirtualne, bramy i skojarzone wdrożenia w sieci wirtualnej, które nie są w tej samej subskrypcji co obwód usługi ExpressRoute, wymagają pewnego przestoju w celu ukończenia migracji. W ostatniej części dokumentu opisano kroki, które należy wykonać w celu migracji zasobów.
* Nie można przeprowadzić migracji sieci wirtualnej z bramą usługi ExpressRoute i bramą sieci VPN.
* Migracja subskrypcji krzyżowej obwodu usługi ExpressRoute nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz [Pomoc techniczna w zakresie przenoszenia usługi Microsoft.Network](../azure-resource-manager/management/move-support-resources.md#microsoftnetwork).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów
Przed podjęciem próby migracji zasobów dołączonych do obwodu usługi ExpressRoute należy przenieść obwód usługi ExpressRoute z klasyki do środowiska Menedżera zasobów. Aby wykonać to zadanie, zobacz następujące artykuły:

* Przejrzyj informacje podane w obszarze [Przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów](expressroute-move.md).
* [Przenoszenie obwodu z klasycznego do Menedżera zasobów przy użyciu programu Azure PowerShell](expressroute-howto-move-arm.md).
* Użyj portalu zarządzania usługami Azure. Można wykonać przepływ pracy, aby [utworzyć nowy obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i wybrać opcję importu. 

Ta operacja nie wymaga przestojów. W trakcie migracji można nadal przesyłać dane między lokalami a firmą Microsoft.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrowanie sieci wirtualnych, bram i skojarzonych wdrożeń

Kroki, które należy wykonać w celu migracji zależą od tego, czy zasoby są w tej samej subskrypcji, różnych subskrypcji lub obu.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrowanie sieci wirtualnych, bram i skojarzonych wdrożeń w tej samej subskrypcji co obwód usługi ExpressRoute
W tej sekcji opisano kroki, które należy wykonać w celu migracji sieci wirtualnej, bramy i skojarzonych wdrożeń w tej samej subskrypcji co obwód usługi ExpressRoute. Z tą migracją nie jest skojarzony żaden przestój. Można nadal używać wszystkich zasobów za pośrednictwem procesu migracji. Płaszczyzna zarządzania jest zablokowana, gdy trwa migracja. 

1. Upewnij się, że obwód usługi ExpressRoute został przeniesiony z klasycznego do środowiska Menedżera zasobów.
2. Upewnij się, że sieć wirtualna została odpowiednio przygotowana do migracji.
3. Zarejestruj swoją subskrypcję w celu migracji zasobów. Aby zarejestrować subskrypcję w celu migracji zasobów, użyj następującego fragmentu kodu programu PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Sprawdzanie poprawności, przygotowywanie i migrowanie. Aby przenieść sieć wirtualną, użyj następującego fragmentu kodu programu PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Migrację można również przerwać, uruchamiając następujące polecenie cmdlet programu PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Następne kroki
* [Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Często zadawane pytania: Migracja zasobów IaaS obsługiwana przez platformę z klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Przegląd najczęstszych błędów i środków zaradczych migracji](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
