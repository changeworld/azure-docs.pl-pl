---
title: Maszyna wirtualna (klasyczna) lub usługi w chmurze wystąpienia roli przeniesione do innej podsieci - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść maszyny wirtualne (klasyczne) i wystąpień roli usługi w chmurze do innej podsieci przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
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
ms.openlocfilehash: eb1576b7b842537dbdc49e6b9d402ef47ad9fae9
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Maszyna wirtualna (klasyczna) lub usługi w chmurze wystąpienia roli przeniesione do innej podsieci przy użyciu programu PowerShell
Za pomocą programu PowerShell można przenoszeniu maszyn wirtualnych (klasyczne) z jednej podsieci w tej samej sieci wirtualnej (VNet). Wystąpienia roli można przenieść edytowania pliku CSCFG, zamiast przy użyciu programu PowerShell.

> [!NOTE]
> W tym artykule opisano sposób przenoszenia maszyn wirtualnych wdrażanych za pośrednictwem klasycznego modelu wdrażania tylko.
> 
> 

Dlaczego należy przenieść maszyny wirtualne na inną podsieć? Podsieci migracji jest przydatne w przypadku starszych podsieci jest zbyt mała i nie można rozwijać z powodu istniejących uruchomionych maszyn wirtualnych w tej podsieci. W takim przypadku można tworzyć nowe, większy podsieci i migracji maszyn wirtualnych do nowej podsieci, a następnie po zakończeniu migracji, można usunąć starego podsieci puste.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Jak przenieść maszyny Wirtualnej na inną podsieć
Aby przenieść Maszynę wirtualną, uruchom polecenie cmdlet programu PowerShell Set-AzureSubnet, przy użyciu w przykładzie poniżej jako szablon. W poniższym przykładzie mamy przechodzenia TestVM z jego obecny podsieci do podsieci 2. Pamiętaj edytować w przykładzie, aby odzwierciedlić charakter lokalnego środowiska. Należy pamiętać, że po uruchomieniu polecenia cmdlet AzureVM aktualizacji w ramach procedury będzie uruchamiany ponownie maszyny Wirtualnej w ramach procesu aktualizacji.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Jeśli określono wewnętrzny statycznego prywatnego adresu IP dla maszyny Wirtualnej, należy wyczyścić to ustawienie, zanim będzie możliwe przeniesienie maszyny Wirtualnej do nowych podsieci. W takim przypadku należy użyć następujących czynności:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Aby przenieść do innej podsieci wystąpienia roli
Aby przenieść wystąpienia roli, przeprowadź edycję pliku CSCFG. W poniższym przykładzie mamy przechodzenia w sieci wirtualnej "Role0" *VNETName* z jej obecny podsieci, aby *2 podsieci*. Wystąpienia roli została już wdrożona, można będzie można zmienić nazwy podsieci = 2 podsieci. Pamiętaj edytować w przykładzie, aby odzwierciedlić charakter lokalnego środowiska.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
