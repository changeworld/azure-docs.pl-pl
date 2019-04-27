---
title: Przenoszenie maszyny Wirtualnej (klasycznej) lub wystąpienie roli usług Cloud Services do innej podsieci - programu Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak przenieść maszyny wirtualne (klasyczne) i wystąpień roli usług Cloud Services do innej podsieci przy użyciu programu PowerShell.
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
ms.openlocfilehash: 787a50a0cbf16089cd15f922b494cd12d680cb43
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60640399"
---
# <a name="move-a-vm-classic-or-cloud-services-role-instance-to-a-different-subnet-using-powershell"></a>Przenoszenie maszyny Wirtualnej (klasycznej) lub wystąpienie roli usług Cloud Services do innej podsieci przy użyciu programu PowerShell
Można użyć programu PowerShell, aby przenieść maszyny wirtualne (klasyczne) z jednej podsieci do drugiego w tej samej sieci wirtualnej (VNet). Wystąpienia roli mogą być przenoszone przez edycję pliku CSCFG, a nie przy użyciu programu PowerShell.

> [!NOTE]
> W tym artykule opisano sposób przenoszenia maszyn wirtualnych wdrażanych za pośrednictwem klasycznego modelu wdrażania tylko.
> 
> 

Dlaczego należy przenieść maszyny wirtualne do innej podsieci? Podsieci migracji jest przydatne, gdy starsze podsieci jest zbyt mała i nie może zostać rozszerzona ze względu na istniejące działających maszyn wirtualnych w tej podsieci. W takim przypadku można tworzyć nowe, większej podsieci i migracji maszyn wirtualnych do nowej podsieci, a następnie po zakończeniu migracji można usunąć starego pustą podsieć.

## <a name="how-to-move-a-vm-to-another-subnet"></a>Jak przenieść Maszynę wirtualną do innej podsieci
Aby przenieść Maszynę wirtualną, Uruchom polecenia cmdlet programu PowerShell Set-AzureSubnet, używając w poniższym przykładzie jako szablonu. W poniższym przykładzie mamy przechodzenia TestVM z jej obecny podsieci do podsieci-2. Pamiętaj edytować przykładu tak, aby odzwierciedlały dane środowisko. Należy pamiętać, że przy każdym uruchomieniu polecenia cmdlet Update-AzureVM jako część procedury zostanie ponownie uruchomiony maszynę Wirtualną jako część procesu aktualizacji.

    Get-AzureVM –ServiceName TestVMCloud –Name TestVM `
    | Set-AzureSubnet –SubnetNames Subnet-2 `
    | Update-AzureVM

Jeśli określono wewnętrznej statyczny prywatny adres IP dla maszyny Wirtualnej, należy wyczyścić to ustawienie, przed można przenieść maszynę Wirtualną do nowej podsieci. W takim przypadku należy użyć następującego polecenia:

    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM
    Get-AzureVM -ServiceName TestVMCloud -Name TestVM `
    | Set-AzureSubnet -SubnetNames Subnet-2 `
    | Update-AzureVM

## <a name="to-move-a-role-instance-to-another-subnet"></a>Aby przenieść wystąpienia roli do innej podsieci
Aby przenieść wystąpienia roli, przeprowadź edycję pliku CSCFG. W poniższym przykładzie mamy przenosisz "Role0" w sieci wirtualnej *VNETName* z jej obecny podsieci, aby *podsieci 2*. Ponieważ została już wdrożona w wystąpieniu roli, po prostu zmienisz nazwę podsieci = 2 podsieci. Pamiętaj edytować przykładu tak, aby odzwierciedlały dane środowisko.

    <NetworkConfiguration>
        <VirtualNetworkSite name="VNETName" />
        <AddressAssignments>
           <InstanceAddress roleName="Role0">
                <Subnets><Subnet name="Subnet-2" /></Subnets>
           </InstanceAddress>
        </AddressAssignments>
    </NetworkConfiguration> 
