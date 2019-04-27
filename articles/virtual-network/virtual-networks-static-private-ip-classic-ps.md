---
title: Konfigurowanie prywatnych adresów IP dla maszyny wirtualne (klasyczne) — program Azure PowerShell | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatnych adresów IP dla maszyny wirtualne (klasyczne) przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: 60c7b489-46ae-48af-a453-2b429a474afd
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3fa82c2397b4c2601cdd943158054e1c8efb6b57
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60596351"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-powershell"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej (klasycznej) przy użyciu programu PowerShell

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Możesz również [Zarządzanie statyczny prywatny adres IP w modelu wdrażania usługi Resource Manager](virtual-networks-static-private-ip-arm-ps.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Poniższe przykładowe polecenia programu PowerShell oczekiwać proste środowisko już utworzony. Jeśli chcesz uruchamiać polecenia, ponieważ są one wyświetlane w tym dokumencie, najpierw utworzyć środowisko testowe opisane w [tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-netcfg-ps.md).

## <a name="how-to-verify-if-a-specific-ip-address-is-available"></a>Jak sprawdzić, czy określony adres IP jest dostępna
Sprawdzenie, czy adres IP *192.168.1.101* jest dostępny w sieci wirtualnej o nazwie *TestVNet*, uruchom następujące polecenie programu PowerShell i sprawdź wartość *IsAvailable*:

    Test-AzureStaticVNetIP –VNetName TestVNet –IPAddress 192.168.1.101 

Oczekiwane dane wyjściowe:

    IsAvailable          : True
    AvailableAddresses   : {}
    OperationDescription : Test-AzureStaticVNetIP
    OperationId          : fd3097e1-5f4b-9cac-8afa-bba1e3492609
    OperationStatus      : Succeeded

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak określić statyczny prywatny adres IP podczas tworzenia maszyny Wirtualnej
Poniższy skrypt programu PowerShell tworzy nową usługę w chmurze o nazwie *TestService*, następnie pobierze obraz na platformie Azure, utworzenie maszyny Wirtualnej o nazwie *DNS01* w nowej usługi w chmurze korzystając z pobrane obrazu ustawia maszyny Wirtualnej znajdować się w podsieci o nazwie *frontonu*i ustawia *192.168.1.7* jako statyczny prywatny adres IP dla maszyny Wirtualnej:

    New-AzureService -ServiceName TestService -Location "Central US"
    $image = Get-AzureVMImage | where {$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}
    New-AzureVMConfig -Name DNS01 -InstanceSize Small -ImageName $image.ImageName |
      Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! |
      Set-AzureSubnet –SubnetNames FrontEnd |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      New-AzureVM -ServiceName TestService –VNetName TestVNet

Oczekiwane dane wyjściowe:

    WARNING: No deployment found in service: 'TestService'.
    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    New-AzureService     fcf705f1-d902-011c-95c7-b690735e7412 Succeeded      
    New-AzureVM          3b99a86d-84f8-04e5-888e-b6fc3c73c4b9 Succeeded  

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej utworzone za pomocą skryptu powyżej, uruchom następujące polecenie programu PowerShell i sprawdź wartości *IpAddress*:

    Get-AzureVM -Name DNS01 -ServiceName TestService

Oczekiwane dane wyjściowe:

    DeploymentName              : TestService
    Name                        : DNS01
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : Provisioning
    IpAddress                   : 192.168.1.7
    InstanceStateDetails        : Windows is preparing your computer for first use...
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : DNS01
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

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny Wirtualnej
Aby usunąć statyczny prywatny adres IP dodane do maszyny Wirtualnej w skrypcie powyżej, uruchom następujące polecenie programu PowerShell:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Remove-AzureStaticVNetIP |
      Update-AzureVM

Oczekiwane dane wyjściowe:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       052fa6f6-1483-0ede-a7bf-14f91f805483 Succeeded

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny Wirtualnej
Aby dodać statyczną prywatnych adresów IP do maszyny Wirtualnej utworzonej przy użyciu skryptu powyżej runt on następujące polecenie:

    Get-AzureVM -ServiceName TestService -Name DNS01 |
      Set-AzureStaticVNetIP -IPAddress 192.168.1.7 |
      Update-AzureVM

Oczekiwane dane wyjściowe:

    OperationDescription OperationId                          OperationStatus
    -------------------- -----------                          ---------------
    Update-AzureVM       77d8cae2-87e6-0ead-9738-7c7dae9810cb Succeeded 

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawić adresy IP w ramach systemu operacyjnego

Zalecane jest, że nie zostanie statycznie przypisany prywatny adres IP, przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile to konieczne. Jeśli ręcznie ustawić jako prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest ten sam adres jako prywatny adres IP przypisany do maszyny Wirtualnej platformy Azure, lub można utracić łączność z maszyną wirtualną. Należy nigdy ręcznie przypisać publiczny adres IP przypisany do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Kolejne kroki
* Dowiedz się więcej o [publiczny adres IP zarezerwowany](virtual-networks-reserved-public-ip.md) adresów.
* Dowiedz się więcej o [na poziomie wystąpienia publicznego adresu IP (ILPIP)](virtual-networks-instance-level-public-ip.md) adresów.
* Zapoznaj się z [zarezerwowanych adresów IP REST API](https://msdn.microsoft.com/library/azure/dn722420.aspx).

