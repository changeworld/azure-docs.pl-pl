---
title: 'Migracja sieci wirtualnej z klasycznej do usługi Resource Manager — usługi ExpressRoute: Azure: Program PowerShell | Dokumentacja firmy Microsoft'
description: Ta strona opisano, jak przeprowadzić migrację do usługi Resource Manager po przeniesieniu obwodu usługi ExpressRoute skojarzonych sieci wirtualnych.
services: expressroute
author: ganesr
ms.service: expressroute
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: ganesr;cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2e33454ac0ee97385386043706f4b8b73090f57a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60363856"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migrowanie usługi ExpressRoute skojarzonych sieci wirtualnych z wersji klasycznej do usługi Resource Manager

W tym artykule wyjaśniono, jak przeprowadzić migrację usługi ExpressRoute skojarzonych sieci wirtualnych z klasycznego modelu wdrażania do modelu wdrażania usługi Azure Resource Manager po przeniesieniu obwód usługi ExpressRoute. 

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Sprawdź, czy masz najnowszą wersję modułów programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Upewnij się, że użytkownik przejrzał [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfiguracji.
* Zapoznaj się z informacjami, który znajduje się w obszarze [przenoszenie obwodu usługi ExpressRoute z modelu klasycznego do usługi Resource Manager](expressroute-move.md). Upewnij się, że w pełni rozumiesz limity i ograniczenia.
* Sprawdź, że obwód jest w pełni funkcjonalne w klasycznym modelu wdrażania.
* Upewnij się, że grupa zasobów, który został utworzony w modelu wdrażania usługi Resource Manager.
* Zapoznaj się z następującą dokumentacją migracji zasobów:

    * [Obsługiwane platformy migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Często zadawane pytania: Obsługiwane platformy migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Przegląd najbardziej typowych błędów migracji i środki zaradcze](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Obsługiwane i nieobsługiwane scenariusze

* Obwód usługi ExpressRoute można przenieść z klasycznego środowiska usługi Resource Manager, bez żadnych przestojów. Można przenieść dowolnym obwodzie usługi ExpressRoute z klasycznego środowiska usługi Resource Manager bez przestojów. Postępuj zgodnie z instrukcjami w [przenoszenie obwodów usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell](expressroute-howto-move-arm.md). Jest to warunek wstępny do przeniesienia zasobów podłączone do sieci wirtualnej.
* Sieci wirtualne, bramy i skojarzonych z nimi wdrożeń w ramach sieci wirtualnej, które są dołączone do obwodu usługi ExpressRoute z tą samą subskrypcją mogą zostać zmigrowane do środowiska usługi Resource Manager, bez żadnych przestojów. Możesz wykonać kroki opisane w dalszej części przeprowadzić migrację zasobów, takich jak sieci wirtualne, bramy i maszyny wirtualne wdrożone w ramach sieci wirtualnej. Należy się upewnić, że sieci wirtualne są skonfigurowane prawidłowo przed migracją. 
* Sieci wirtualne, bramy i skojarzonych z nimi wdrożeń w ramach sieci wirtualnej, które nie znajdują się w innej subskrypcji niż obwód usługi ExpressRoute wymaga pewien Przestój, aby ukończyć migrację. Ostatnia sekcja dokumentu w tym artykule opisano kroki, aby wykonać migrację zasobów.
* Nie można przeprowadzić migracji sieci wirtualnej przy użyciu zarówno bramę usługi ExpressRoute, jak i bramy sieci VPN.
* Migracja między subskrypcjami obwodu usługi ExpressRoute nie jest obsługiwana. Aby uzyskać więcej informacji, zobacz [usług, których nie można przenieść](../azure-resource-manager/resource-group-move-resources.md#services-that-cannot-be-moved).

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Przenoszenie obwodu usługi ExpressRoute z wersji klasycznej do usługi Resource Manager
Należy przenieść obwód usługi ExpressRoute z klasycznego środowiska usługi Resource Manager przed podjęciem próby migracji zasobów, które są dołączone do obwodu usługi ExpressRoute. Aby wykonać to zadanie, zobacz następujące artykuły:

* Zapoznaj się z informacjami, który znajduje się w obszarze [przenoszenie obwodu usługi ExpressRoute z modelu klasycznego do usługi Resource Manager](expressroute-move.md).
* [Przenoszenie obwodu z wersji klasycznej do usługi Resource Manager przy użyciu programu Azure PowerShell](expressroute-howto-move-arm.md).
* Za pomocą portalu zarządzania usługami platformy Azure. Można wykonać przepływu pracy [utworzenia nowego obwodu usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i wybierz opcję importowania. 

Ta operacja nie wymaga przestojów. Można kontynuować do transferu danych między środowiskiem lokalnym a systemem firmy Microsoft, w trakcie migracji.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migrowanie sieci wirtualnych, bramy i skojarzonych z nimi wdrożeń

Kroki, które należy wykonać w celu migracji zależą od tego, czy Twoje zasoby znajdują się w tej samej subskrypcji i/lub różnych subskrypcji.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migrowanie sieci wirtualnych, bramy i skojarzonych z nimi wdrożeń w innej subskrypcji niż obwód usługi ExpressRoute
W tej sekcji opisano kroki, aby wykonać migrację sieci wirtualnej, bramy i skojarzonych z nimi wdrożeń w innej subskrypcji niż obwód usługi ExpressRoute. Bez przerwy w działaniu jest skojarzony z tej migracji. Można nadal używać wszystkich zasobów przez proces migracji. Płaszczyzna zarządzania jest zablokowana, gdy trwa migracja. 

1. Upewnij się, czy obwód usługi ExpressRoute zostały przeniesione z klasycznego środowiska usługi Resource Manager.
2. Upewnij się, że sieci wirtualnej zostało odpowiednio przygotowane do migracji.
3. Zarejestruj swoją subskrypcję dla migracji zasobów. Aby zarejestrować subskrypcję w celu migracji zasobów, użyj następującego fragmentu kodu programu PowerShell:

   ```powershell 
   Select-AzSubscription -SubscriptionName <Your Subscription Name>
   Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
   ```
4. Sprawdzanie poprawności, przygotowywania i migracji. Aby przenieść sieć wirtualną, należy użyć następującego fragmentu kodu programu PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
   Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
   ```

   Można również przerwać migracji, uruchamiając następujące polecenie cmdlet programu PowerShell:

   ```powershell
   Move-AzureVirtualNetwork -Abort $vnetName
   ```

## <a name="next-steps"></a>Kolejne kroki
* [Obsługiwane platformy migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Rozbudowana technicznie migracja z obsługą platformy od modelu klasycznego do modelu opartego na usłudze Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Często zadawane pytania: Obsługiwane platformy migracji zasobów rozwiązania IaaS z wersji klasycznej do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Przegląd najbardziej typowych błędów migracji i środki zaradcze](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
