---
title: Statyczne wewnętrzne prywatnej klasycznym IP - Azure VM-
description: Opis statyczne wewnętrzne adresy IP (DIP) i zarządzanie nimi
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2018
ms.author: genli
ms.openlocfilehash: 661d2f789ace8da68b6d65609d4584a11967a01f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34366617"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Jak ustawić statyczne wewnętrzne prywatny adres IP za pomocą programu PowerShell (klasyczne)
W większości przypadków nie trzeba określić statyczny adres IP wewnętrznego dla maszyny wirtualnej. Maszyn wirtualnych w sieci wirtualnej zostanie automatycznie otrzymują wewnętrzny adres IP z zakresu, który określisz. Jednak w niektórych przypadkach Określanie statycznego adresu IP dla określonej maszyny Wirtualnej ma sens. Na przykład, jeśli maszyna wirtualna jest, aby uruchomić DNS lub będzie kontroler domeny. Statyczny adres IP wewnętrznego utrzymane maszyny Wirtualnej, nawet za pośrednictwem stanu zatrzymania/deprovision. 

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby użyć większości nowych wdrożeń [modelu wdrażania usługi Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak sprawdzić, czy określony adres IP jest dostępna
Aby sprawdzić, czy adres IP *10.0.0.7* jest dostępny w sieci wirtualnej o nazwie *TestVnet*, uruchom następujące polecenie programu PowerShell i sprawdź wartość *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Jeśli chcesz przetestować polecenia powyżej w bezpiecznym środowisku postępuj zgodnie ze wskazówkami podanymi w [utworzyć sieć wirtualną (klasyczne)](virtual-networks-create-vnet-classic-pportal.md) tworzenie sieci wirtualnej o nazwie *TestVnet* i upewnij się, używa *10.0.0.0/8* przestrzeni adresów.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Jak określić statyczny adres IP wewnętrznego podczas tworzenia maszyny Wirtualnej
Poniższy skrypt programu PowerShell tworzy nową usługę w chmurze o nazwie *TestService*, pobiera obraz z platformy Azure, a następnie tworzy Maszynę wirtualną o nazwie *TestVM* nową usługę w chmurze przy użyciu obrazu pobrane, ustawia maszyna wirtualna może znajdować się w podsieci o nazwie *podsieć 1*i ustawia *10.0.0.7* jako statyczny adres IP wewnętrznego dla maszyny Wirtualnej:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Jak pobrać statyczne wewnętrzne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić informacje statyczne wewnętrzne IP dla maszyny Wirtualnej utworzone za pomocą skryptu powyżej, uruchom następujące polecenie programu PowerShell i sprawdź wartości *IpAddress*:

    Get-AzureVM -Name TestVM -ServiceName TestService

    DeploymentName              : TestService
    Name                        : TestVM
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 10.0.0.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : TestVM
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : rsR2-797
    AvailabilitySetName         : 
    DNSName                     : http://testservice000.cloudapp.net/
    Status                      : Provisioning
    GuestAgentStatus            : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 
    PublicIPName                : 
    NetworkInterfaces           : {}
    ServiceName                 : TestService
    OperationDescription        : Get-AzureVM
    OperationId                 : 34c1560a62f0901ab75cde4fed8e8bd1
    OperationStatus             : OK

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Jak usunąć statyczny adres IP wewnętrznego z maszyny Wirtualnej
Aby usunąć statyczny adres IP wewnętrznego dodane do maszyny Wirtualnej w skrypcie powyżej, uruchom następujące polecenie programu PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Jak dodać statyczny adres IP wewnętrznego do istniejącej maszyny Wirtualnej
Aby dodać wewnętrzny statycznego adresu IP do maszyny Wirtualnej utworzone za pomocą skryptu powyżej, uruchom następujące polecenie:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Kolejne kroki
[Zastrzeżony adres IP](virtual-networks-reserved-public-ip.md)

[Poziom wystąpienia publicznego adresu IP (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Interfejsy API REST zastrzeżonego adresu IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)

