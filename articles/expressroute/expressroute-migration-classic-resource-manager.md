---
title: 'Migracji sieci wirtualnych ExpressRoute skojarzone ze środowiska klasycznego do Menedżera zasobów: Azure: programu PowerShell | Dokumentacja firmy Microsoft'
description: Ta strona opisano sposób przeprowadzenia migracji do Menedżera zasobów skojarzone sieci wirtualne po przeniesieniu obwodu.
documentationcenter: na
services: expressroute
author: ganesr
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2017
ms.author: ganesr;cherylmc
ms.openlocfilehash: 336f68308f7d4b4dd3c7476a4fabd793939e9e85
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23850826"
---
# <a name="migrate-expressroute-associated-virtual-networks-from-classic-to-resource-manager"></a>Migracji sieci wirtualnych ExpressRoute skojarzone ze środowiska klasycznego do Menedżera zasobów

W tym artykule opisano sposób migracji sieci wirtualnych Azure ExpressRoute skojarzone z klasycznym modelu wdrażania modelu wdrażania usługi Azure Resource Manager po przeniesieniu obwodu usługi ExpressRoute. 


## <a name="before-you-begin"></a>Przed rozpoczęciem
* Sprawdź, czy masz najnowszą wersję modułów programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).
* Upewnij się, że użytkownik przejrzał [wymagania wstępne](expressroute-prerequisites.md), [wymagania dotyczące routingu](expressroute-routing.md), i [przepływy pracy](expressroute-workflows.md) przed rozpoczęciem konfigurowania.
* Przejrzyj informacje, które są przekazywane pod [przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów](expressroute-move.md). Upewnij się, że pełni rozumiesz limity i ograniczenia.
* Sprawdź, że obwód jest w pełni funkcjonalna w klasycznym modelu wdrażania.
* Upewnij się, że masz grupę zasobów, który został utworzony w modelu wdrażania usługi Resource Manager.
* Zapoznaj się z dokumentacją dotyczącą następujących migracji zasobów:

    * [Obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
    * [Często zadawane pytania: Obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
    * [Przejrzyj typowe błędy, migracji i środki zaradcze](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

## <a name="supported-and-unsupported-scenarios"></a>Obsługiwane i nieobsługiwane scenariusze

* Obwodu usługi ExpressRoute można przenosić z klasycznego środowiska usługi Resource Manager, bez żadnego przestoju. Wszelkie obwodu usługi expressroute z klasycznego można przenieść do środowiska usługi Resource Manager, bez przestojów. Postępuj zgodnie z instrukcjami [przenoszenie obwody usługi ExpressRoute z klasycznego modelu wdrażania usługi Resource Manager przy użyciu programu PowerShell](expressroute-howto-move-arm.md). To jest wymaganiem wstępnym, aby przenieść zasoby podłączone do sieci wirtualnej.
* Sieci wirtualne, bramy i skojarzonych z nimi wdrożeń w sieci wirtualnej, które są dołączone do obwodu usługi ExpressRoute w tej samej subskrypcji można poddać migracji do środowiska usługi Resource Manager, bez żadnego przestoju. Można wykonać kroki opisane później, aby przeprowadzić migrację zasobów, takich jak sieci wirtualnych, bramy i maszyn wirtualnych wdrożonych w ramach sieci wirtualnej. Pamiętaj, że sieci wirtualne są skonfigurowane prawidłowo przed ich migracją. 
* Sieci wirtualne, bramy i skojarzonych z nimi wdrożeń w sieci wirtualnej, które nie znajdują się w tej samej subskrypcji co obwodu ExpressRoute wymagają pewien Przestój, aby ukończyć migrację. W ostatniej sekcji dokumentu opisano procedurę można wykonać, aby przeprowadzić migrację zasobów.
* Nie można migrować sieci wirtualnej zarówno bramę usługi ExpressRoute, jak i bramy sieci VPN.

## <a name="move-an-expressroute-circuit-from-classic-to-resource-manager"></a>Przenieś obwodu usługi ExpressRoute ze środowiska klasycznego do Menedżera zasobów
Musisz przenieść obwodu usługi ExpressRoute z klasycznego środowiska usługi Resource Manager przygotowania do migracji zasobów, które są dołączone do obwodu usługi expressroute. Aby to zrobić, zobacz następujące artykuły:

* Przejrzyj informacje, które są przekazywane pod [przenoszenie obwodu usługi ExpressRoute z klasycznego do Menedżera zasobów](expressroute-move.md).
* [Przenieś obwód ze środowiska klasycznego do Menedżera zasobów przy użyciu programu Azure PowerShell](expressroute-howto-move-arm.md).
* Za pomocą portalu zarządzania usługi Azure. Możesz wykonać, aby przepływ pracy [tworzenie nowych obwodu ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i wybierz opcję importu. 

Ta operacja nie jest związana przestoju. Można kontynuować do transferu danych między lokalnym i firmy Microsoft, w trakcie migracji.

## <a name="migrate-virtual-networks-gateways-and-associated-deployments"></a>Migracja sieci wirtualnej, bramy i skojarzonych z nimi wdrożeń

Czynności, które należy wykonać, aby migrować zależą od tego, czy zasoby są w tej samej subskrypcji i/lub różnych subskrypcji.

### <a name="migrate-virtual-networks-gateways-and-associated-deployments-in-the-same-subscription-as-the-expressroute-circuit"></a>Migracja sieci wirtualnych, bramy i skojarzonych z nimi wdrożeń w tej samej subskrypcji co obwodu usługi expressroute
W tej sekcji opisano kroki, aby być zachowana w celu przeprowadzenia migracji sieci wirtualnej, bramy i skojarzonych z nimi wdrożeń w tej samej subskrypcji co obwodu usługi expressroute. Nie jest wyłączenie skojarzony z tym migracji. Możesz korzystać ze wszystkich zasobów w procesie migracji. Płaszczyzny zarządzania jest zablokowana w trakcie migracji. 

1. Upewnij się, że obwód usługi expressroute została przeniesiona z klasycznego środowiska Menedżera zasobów.
2. Upewnij się, że sieć wirtualna został przygotowany odpowiednio do migracji.
3. Zarejestruj subskrypcję dla migracji zasobów. Aby zarejestrować subskrypcji dla migracji zasobów, użyj następującego fragmentu kodu programu PowerShell:

  ```powershell 
  Select-AzureRmSubscription -SubscriptionName <Your Subscription Name>
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  Get-AzureRmResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
  ```
4. Sprawdzanie poprawności, przygotowywania i migracji. Aby przenieść sieci wirtualnej, użyj następującego fragmentu kodu programu PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
  Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
  ```

  Można również przerwać migracji, uruchamiając następujące polecenie cmdlet programu PowerShell:

  ```powershell
  Move-AzureVirtualNetwork -Abort $vnetName
  ```

## <a name="next-steps"></a>Następne kroki
* [Obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Techniczne szczegółowe informacje na temat obsługiwanych platform migracji ze środowiska klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager-deep-dive.md)
* [Często zadawane pytania: Obsługiwane platformy migracji zasobów IaaS ze środowiska klasycznego do usługi Azure Resource Manager](../virtual-machines/virtual-machines-windows-migration-classic-resource-manager.md)
* [Przejrzyj typowe błędy, migracji i środki zaradcze](../virtual-machines/windows/migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
