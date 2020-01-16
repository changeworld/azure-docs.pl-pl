---
title: Statyczny wewnętrzny prywatny adres IP — maszyna wirtualna platformy Azure — klasyczna
description: Informacje o statycznych wewnętrznych adresach IP (DIP) i sposobach zarządzania nimi
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 93444c6f-af1b-41f8-a035-77f5c0302bf0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: a13a0a54e9ded48cc5848843f4c329b2dea90f65
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975232"
---
# <a name="how-to-set-a-static-internal-private-ip-address-using-powershell-classic"></a>Jak ustawić statyczny wewnętrzny prywatny adres IP przy użyciu programu PowerShell (wersja klasyczna)
W większości przypadków nie trzeba określać statycznego wewnętrznego adresu IP dla maszyny wirtualnej. Maszyny wirtualne w sieci wirtualnej automatycznie otrzymają wewnętrzny adres IP z określonego zakresu. Jednak w niektórych przypadkach określenie statycznego adresu IP dla określonej maszyny wirtualnej ma sens. Na przykład jeśli maszyna wirtualna ma działać w systemie DNS lub będzie kontrolerem domeny. Statyczny wewnętrzny adres IP pozostaje razem z maszyną wirtualną, nawet w stanie zatrzymania/anulowania aprowizacji. 

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../azure-resource-manager/management/deployment-models.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby większość nowych wdrożeń korzystała z [modelu wdrażania Menedżer zasobów](virtual-networks-static-private-ip-arm-ps.md).
> 
> 
> ## <a name="install-the-azure-powershell-service-management-module"></a>Instalowanie modułu Azure PowerShell Service Management

Przed uruchomieniem poniższych poleceń upewnij się, że na komputerze jest zainstalowany [moduł zarządzania usługami Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0
) . Aby uzyskać historię wersji Azure PowerShell modułu zarządzania usługami, zobacz [Azure module w Galeria programu PowerShell](https://www.powershellgallery.com/packages/Azure/5.3.0).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak sprawdzić, czy określony adres IP jest dostępny
Aby sprawdzić, czy adres IP *10.0.0.7* jest dostępny w sieci wirtualnej o nazwie *TestVnet*, uruchom następujące polecenie programu PowerShell i sprawdź wartość parametru *IsAvailable*.


    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 10.0.0.7 

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

> [!NOTE]
> Jeśli chcesz przetestować powyższe polecenie w bezpiecznym środowisku, postępuj zgodnie ze wskazówkami w temacie [Tworzenie sieci wirtualnej (klasycznej)](virtual-networks-create-vnet-classic-pportal.md) , aby utworzyć sieć wirtualną o nazwie *TestVnet* i upewnić się, że używa ona przestrzeni adresowej *10.0.0.0/8* .
> 
> 

## <a name="how-to-specify-a-static-internal-ip-when-creating-a-vm"></a>Jak określić statyczny wewnętrzny adres IP podczas tworzenia maszyny wirtualnej
Poniższy skrypt programu PowerShell tworzy nową usługę w chmurze o nazwie *TestService*, a następnie pobiera obraz z platformy Azure, a następnie tworzy maszynę wirtualną o nazwie *TestVM* w nowej usłudze w chmurze przy użyciu pobranego obrazu, ustawia maszynę wirtualną w podsieci o nazwie *Subnet-1*i ustawia *10.0.0.7* jako statyczny wewnętrzny adres IP dla maszyny wirtualnej:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzureSubnet –SubnetNames Subnet-1 `
    | Set-AzureStaticVNetIP -IPAddress 10.0.0.7 `
    | New-AzureVM -ServiceName "TestService" –VNetName TestVnet

## <a name="how-to-retrieve-static-internal-ip-information-for-a-vm"></a>Jak pobrać statyczne wewnętrzne informacje o adresie IP dla maszyny wirtualnej
Aby wyświetlić statyczne wewnętrzne informacje o adresie IP dla maszyny wirtualnej utworzonej za pomocą powyższego skryptu, uruchom następujące polecenie programu PowerShell i obserwuj wartości dla *adresu IP*:

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

## <a name="how-to-remove-a-static-internal-ip-from-a-vm"></a>Jak usunąć statyczny wewnętrzny adres IP z maszyny wirtualnej
Aby usunąć statyczny wewnętrzny adres IP dodany do maszyny wirtualnej w powyższym skrypcie, uruchom następujące polecenie programu PowerShell:

    Get-AzureVM -ServiceName TestService -Name TestVM `
    | Remove-AzureStaticVNetIP `
    | Update-AzureVM

## <a name="how-to-add-a-static-internal-ip-to-an-existing-vm"></a>Jak dodać statyczny wewnętrzny adres IP do istniejącej maszyny wirtualnej
Aby dodać statyczny wewnętrzny adres IP do maszyny wirtualnej utworzonej przy użyciu skryptu powyżej, uruchom następujące polecenie:

    Get-AzureVM -ServiceName TestService000 -Name TestVM `
    | Set-AzureStaticVNetIP -IPAddress 10.10.0.7 `
    | Update-AzureVM

## <a name="next-steps"></a>Następne kroki
[Zastrzeżony adres IP](virtual-networks-reserved-public-ip.md)

[Publiczny adres IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md)

[Interfejsy API REST Zastrzeżony adres IP](https://msdn.microsoft.com/library/azure/dn722420.aspx)

