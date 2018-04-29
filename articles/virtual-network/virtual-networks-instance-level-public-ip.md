---
title: Azure poziomie wystąpienia publicznego adresu IP (klasyczne) dotyczy | Dokumentacja firmy Microsoft
description: Zrozumienie wystąpienia adresów poziomu publicznego adresu IP (ILPIP) i jak nimi zarządzać przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 4b4350e6b1616450ce45f9e947cc3b639a341ae7
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="instance-level-public-ip-classic-overview"></a>Wystąpienia publicznego adresu IP (klasyczne) omówienie
Wystąpienie poziomu publicznego adresu IP (ILPIP) to publiczny adres IP, który można przypisać bezpośrednio do wystąpienia roli maszyny Wirtualnej lub usługi w chmurze, a nie do usługi w chmurze, które wystąpienie maszyny Wirtualnej lub roli znajdują się w. ILPIP nie została wykonana z wirtualnego adresu IP (VIP) przypisany do usługi w chmurze. Zamiast jest dodatkowy adres IP, który umożliwia bezpośrednie łączenie się z wystąpieniem maszyny Wirtualnej lub roli.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca tworzenie maszyn wirtualnych za pomocą Menedżera zasobów. Upewnij się, że rozumiesz, jak [adresów IP](virtual-network-ip-addresses-overview-classic.md) pracy na platformie Azure.

![Różnica między ILPIP i adresu VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Jak pokazano na rysunku 1, usługa w chmurze jest dostępny przy użyciu adresu VIP, podczas gdy poszczególnych maszyn wirtualnych są zwykle dostępne przy użyciu adresu VIP:&lt;numer portu&gt;. Przypisując ILPIP do określonej maszyny Wirtualnej, że można uzyskać dostępu do maszyny Wirtualnej bezpośrednio przy użyciu tego adresu IP.

Podczas tworzenia usługi w chmurze na platformie Azure, odpowiednie rekordy A systemu DNS są tworzone automatycznie, aby zezwolić na dostęp do usługi za pośrednictwem pełną nazwę domeny (FQDN) zamiast rzeczywistego adresu VIP. Ten sam proces odbywa się w ILPIP zezwalania na dostęp do maszyny Wirtualnej lub roli wystąpienia przez nazwę FQDN zamiast ILPIP. Na przykład w przypadku utworzenia usługi w chmurze o nazwie *contosoadservice*, i skonfigurować rolę sieci web o nazwie *contosoweb* z dwoma wystąpieniami Azure rejestruje następujące rekordy A wystąpień:

* contosoweb\_IN_0.contosoadservice.cloudapp.net
* contosoweb\_IN_1.contosoadservice.cloudapp.net 

> [!NOTE]
> Można przypisać tylko jednego ILPIP dla każdego wystąpienia maszyny Wirtualnej lub roli. Możesz użyć maksymalnie 5 ILPIPs na subskrypcję. ILPIPs nie są obsługiwane dla maszyn wirtualnych z wieloma kartami Sieciowymi.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Dlaczego może zażądać ILPIP?
Jeśli chcesz można było nawiązać połączenia z wystąpieniem maszyny Wirtualnej lub roli przy użyciu adresu IP przypisane do niego bezpośrednio, zamiast używania chmury usługi VIP:&lt;numer portu&gt;, żądania ILPIP dla maszyny Wirtualnej lub wystąpienia roli.

* **Aktywne FTP** -przypisując ILPIP do maszyny Wirtualnej, może odbierać ruch na dowolnym porcie. Punkty końcowe nie są wymagane dla maszyny Wirtualnej, aby odbierać ruch.  Zobacz (https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview)[Omówienie protokołu FTP] szczegółowe informacje na temat protokołu FTP.
* **Wychodzącego** — ruch wychodzący z maszyny Wirtualnej jest mapowany na ILPIP jako źródło i ILPIP unikatowo identyfikuje maszynę Wirtualną do jednostek zewnętrznych.

> [!NOTE]
> W przeszłości adres ILPIP została przekazana jako publiczny adres IP (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Zarządzanie ILPIP dla maszyny Wirtualnej
Następujące zadania umożliwiają tworzenia, przypisywania i usuwania ILPIPs z maszyn wirtualnych:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Jak utworzyć żądanie ILPIP podczas tworzenia maszyny Wirtualnej przy użyciu programu PowerShell
Poniższy skrypt programu PowerShell tworzy usługi w chmurze o nazwie *FTPService*, pobiera obraz z platformy Azure, tworzy Maszynę wirtualną o nazwie *FTPInstance* przy użyciu obrazu pobrane, ustawia maszynę Wirtualną do obsługi ILPIP i dodaje maszyny Wirtualnej do nowej usługi:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"} `
New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Jak można pobrać informacji o ILPIP dla maszyny Wirtualnej
Aby wyświetlić informacje ILPIP dla maszyny Wirtualnej utworzone z poprzednim skryptu, uruchom następujące polecenie programu PowerShell i sprawdź wartości *PublicIPAddress* i *PublicIPName*:

```powershell
Get-AzureVM -Name FTPInstance -ServiceName FTPService
```

Oczekiwane dane wyjściowe:
 
    DeploymentName              : FTPService
    Name                        : FTPInstance
    Label                       : 
    VM                          : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVM
    InstanceStatus              : ReadyRole
    IpAddress                   : 100.74.118.91
    InstanceStateDetails        : 
    PowerState                  : Started
    InstanceErrorCode           : 
    InstanceFaultDomain         : 0
    InstanceName                : FTPInstance
    InstanceUpgradeDomain       : 0
    InstanceSize                : Small
    HostName                    : FTPInstance
    AvailabilitySetName         : 
    DNSName                     : http://ftpservice888.cloudapp.net/
    Status                      : ReadyRole
    GuestAgentStatus            :   Microsoft.WindowsAzure.Commands.ServiceManagement.Model.GuestAgentStatus
    ResourceExtensionStatusList : {Microsoft.Compute.BGInfo}
    PublicIPAddress             : 104.43.142.188
    PublicIPName                : ftpip
    NetworkInterfaces           : {}
    ServiceName                 : FTPService
    OperationDescription        : Get-AzureVM
    OperationId                 : 568d88d2be7c98f4bbb875e4d823718e
    OperationStatus             : OK

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Jak usunąć ILPIP z maszyny Wirtualnej
Aby usunąć ILPIP dodane do maszyny Wirtualnej w ramach poprzedniego skryptu, uruchom następujące polecenie programu PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Jak dodać ILPIP do istniejącej maszyny Wirtualnej
Aby dodać ILPIP do maszyny Wirtualnej utworzone za pomocą skryptu poprzedniej, uruchom następujące polecenie:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Zarządzanie ILPIP dla wystąpienia roli usługi w chmurze

Aby dodać ILPIP do wystąpienia roli usługi w chmurze, wykonaj następujące czynności:

1. Pobierz plik .cscfg dla usługi w chmurze, wykonując kroki opisane w [sposobu skonfigurowania usługi w chmurze](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artykułu.
2. Zaktualizuj plik .cscfg przez dodanie `InstanceAddress` elementu. Poniższy przykład dodaje ILPIP o nazwie *MyPublicIP* do wystąpienia roli o nazwie *WebRole1*: 

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ILPIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
          <ConfigurationSettings>
        <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
          </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <InstanceAddress roleName="WebRole1">
        <PublicIPs>
          <PublicIP name="MyPublicIP" domainNameLabel="MyPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Przekaż plik .cscfg dla usługi w chmurze, wykonując kroki opisane w [sposobu skonfigurowania usługi w chmurze](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artykułu.

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie sposobu [adresowanie IP](virtual-network-ip-addresses-overview-classic.md) działa w klasycznym modelu wdrażania.
* Dowiedz się więcej o [zastrzeżone adresy IP](virtual-networks-reserved-public-ip.md).
