---
title: Statyczne wewnętrzne prywatny IP - maszyny Wirtualnej platformy Azure — Model Klasyczny
description: Omówienie statyczne wewnętrzne adresy IP (spadku) oraz jak nimi zarządzać
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
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: b83a6e2c81eac9993c481561e3cebbed681d2c4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60640331"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Jak skonfigurować statyczne wewnętrzne prywatny adres IP przy użyciu programu PowerShell (wersja klasyczna)
W większości przypadków nie trzeba określić statyczny adres IP wewnętrznego dla maszyny wirtualnej. Maszyny wirtualne w sieci wirtualnej automatycznie otrzyma wewnętrznego adresu IP z zakresu, który określisz. Jednak w niektórych przypadkach Określanie statyczny adres IP dla konkretnej maszyny Wirtualnej ma sens. Na przykład, jeśli maszyna wirtualna będzie uruchamiany DNS lub zostanie kontrolerem domeny. Statyczny adres IP wewnętrznego pozostaje z maszyną Wirtualną, nawet za pośrednictwem do stanu zatrzymania/anulowania aprowizacji. 

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca się, że większości nowych wdrożeń korzystać [modelu wdrażania usługi Resource Manager](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Instalowanie modułu Azure PowerShell Service Management

Przed uruchomieniem poniższych poleceń upewnij się, że [modułu zarządzania usługami programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) jest zainstalowany na komputerze. Historii wersji modułu Azure PowerShell Service Management, zobacz [modułu platformy Azure w galerii programu PowerShell](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak sprawdzić, czy określony adres IP jest dostępna
Sprawdzenie, czy adres IP *10.0.0.7* jest dostępny w sieci wirtualnej o nazwie *TestVnet*, uruchom następujące polecenie programu PowerShell i sprawdź wartość *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Jeśli chcesz przetestować polecenia powyżej w bezpiecznym środowisku postępuj zgodnie z wytycznymi podanymi w [tworzenie sieci wirtualnej (klasycznej)](virtual-networks-create-vnet-classic-pportal.md) tworzenie sieci wirtualnej o nazwie *TestVnet* i upewnij się, używa ona *10.0.0.0/8*  przestrzeń adresowa.
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Jak określić statyczny adres IP wewnętrznego, podczas tworzenia maszyny Wirtualnej
Poniższy skrypt programu PowerShell tworzy nową usługę w chmurze o nazwie *TestService*, pobiera obraz z platformy Azure, a następnie tworzy maszynę Wirtualną o nazwie *TestVM* w nową usługę w chmurze przy użyciu obrazu pobrane, ustawia Maszyna wirtualna była w podsieci o nazwie *Subnet-1*i ustawia *10.0.0.7* jako wewnętrzny statyczny adres IP dla maszyny Wirtualnej:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Jak pobrać statyczne wewnętrzne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić statycznych informacji wewnętrznych adresów IP dla maszyny Wirtualnej utworzone za pomocą skryptu powyżej, uruchom następujące polecenie programu PowerShell i sprawdź wartości *IpAddress*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Jak usunąć wewnętrzny statyczny adres IP z maszyny Wirtualnej
Aby usunąć statyczny adres IP wewnętrznego, dodane do maszyny Wirtualnej w skrypcie powyżej, uruchom następujące polecenie programu PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Jak dodać statycznego adresu IP wewnętrznej do istniejącej maszyny Wirtualnej
Aby dodać wewnętrznej statyczny adres IP do maszyny Wirtualnej utworzonej przy użyciu skryptu powyżej, uruchom następujące polecenie:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Kolejne kroki
[Zastrzeżony adres IP](virtual-networks-reserved-public-ip.md)

[Publiczny adres IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Interfejsy API REST zastrzeżonego adresu IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)

