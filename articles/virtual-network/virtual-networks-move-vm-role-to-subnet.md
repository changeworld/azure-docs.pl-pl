---
title: Przenieś maszynę wirtualną (klasyczną) lub Cloud Services wystąpienie roli do innej podsieci — Azure PowerShell | Microsoft Docs
description: Dowiedz się, jak przenosić maszyny wirtualne (klasyczne) i Cloud Services wystąpienia roli do innej podsieci przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: de4135c7-dc5b-4ffa-84cc-1b8364b7b427
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 275d59a7bddd8b2b609169218afcd15e9a0ce913
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058370"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Przenoszenie maszyny wirtualnej (klasycznej) lub Cloud Services wystąpienia roli do innej podsieci przy użyciu programu PowerShell
Możesz użyć programu PowerShell do przenoszenia maszyn wirtualnych (klasycznych) z jednej podsieci do drugiej w tej samej sieci wirtualnej (VNet). Wystąpienia ról można przenosić przez edytowanie pliku CSCFG zamiast przy użyciu programu PowerShell.

> [!NOTE]
> W tym artykule wyjaśniono, jak przenieść maszyny wirtualne wdrożone tylko przy użyciu klasycznego modelu wdrażania.
> 
> 

Dlaczego należy przenieść maszyny wirtualne do innej podsieci? Migracja podsieci jest przydatna, gdy Starsza podsieć jest za mała i nie może być rozwinięta z powodu istniejących uruchomionych maszyn wirtualnych w tej podsieci. W takim przypadku można utworzyć nową, większą podsieć i przeprowadzić migrację maszyn wirtualnych do nowej podsieci, po zakończeniu migracji można usunąć starą pustą podsieć.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Jak przenieść maszynę wirtualną do innej podsieci
Aby przenieść maszynę wirtualną, uruchom polecenie cmdlet Set-AzureSubnet programu PowerShell, używając poniższego przykładu jako szablonu. W poniższym przykładzie przeniesiemy TestVM z jego obecnej podsieci do podsieci-2. Pamiętaj, aby edytować przykład w celu odzwierciedlenia Twojego środowiska. Należy pamiętać, że za każdym razem, gdy uruchamiasz polecenie cmdlet Update-AzureVM w ramach procedury, spowoduje to ponowne uruchomienie maszyny wirtualnej w ramach procesu aktualizacji.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Jeśli określono statyczny wewnętrzny prywatny adres IP dla maszyny wirtualnej, należy wyczyścić to ustawienie, aby można było przenieść maszynę wirtualną do nowej podsieci. W takim przypadku należy użyć następujących instrukcji:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Aby przenieść wystąpienie roli do innej podsieci
Aby przenieść wystąpienie roli, edytuj plik CSCFG. W poniższym przykładzie przeniesiemy "Role0" w sieci wirtualnej *VNETName* z jej obecnej podsieci do *podsieci-2*. Ponieważ wystąpienie roli zostało już wdrożone, wystarczy zmienić nazwę podsieci = Subnet-2. Pamiętaj, aby edytować przykład w celu odzwierciedlenia Twojego środowiska.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
