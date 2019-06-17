---
title: Azure poziomie wystąpienia publiczne adresy IP na (klasyczny) | Dokumentacja firmy Microsoft
description: Zrozumienie adresy poziomu publicznego adresu IP (ILPIP) wystąpienie i jak nimi zarządzać przy użyciu programu PowerShell.
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
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: 2f6db23e02c836dea6d640757d12275b654ad468
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60186809"
---
# <a name="instance-level-public-ip-classic-overview"></a>Wystąpienie poziomu omówienie publicznego adresu IP (klasyczny)
Wystąpienie poziomu publicznego adresu IP (ILPIP) to publiczny adres IP, który można przypisać bezpośrednio do wystąpienia roli maszyny Wirtualnej lub usługi w chmurze, a nie do usługi w chmurze, w tej sieci maszyny Wirtualnej lub w wystąpieniu roli. ILPIP nie przyjmuje miejscem, w którym programu virtual IP (VIP) przypisany do usługi w chmurze. Jest raczej, dodatkowe adresu IP, który służy do nawiązania bezpośredniego połączenia z maszyny Wirtualnej lub w wystąpieniu roli.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca tworzenie maszyn wirtualnych przy użyciu usługi Resource Manager. Upewnij się, że rozumiesz, jak [adresów IP](virtual-network-ip-addresses-overview-classic.md) działają na platformie Azure.

![Różnica między ILPIP i adresów VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Jak pokazano na rysunku 1, usługa w chmurze odbywa się przy użyciu adresu VIP, podczas gdy poszczególne maszyny wirtualne są zwykle dostępne przy użyciu adresu VIP:&lt;numer portu&gt;. Przypisując ILPIP do określonej maszyny Wirtualnej, że można uzyskać dostępu do maszyny Wirtualnej bezpośrednio przy użyciu tego adresu IP.

Podczas tworzenia usługi w chmurze na platformie Azure, odpowiednie rekordy A systemu DNS są tworzone automatycznie zezwolić na dostęp do usługi za pośrednictwem Pełna nazwa domeny (FQDN) zamiast rzeczywistego adresu VIP. Ten sam proces ma miejsce dla ILPIP, zezwalając na dostęp do wystąpienia maszyny Wirtualnej lub roli w pełni kwalifikowaną nazwę domeny, zamiast ILPIP. Na przykład jeśli utworzyć usługę w chmurze o nazwie *contosoadservice*, i skonfiguruj rolę sieci web o nazwie *contosoweb* z dwoma wystąpieniami i w pliku .cscfg `domainNameLabel` jest ustawiona na  *WebPublicIP*Azure rejestrów następujących A rejestruje wystąpień:


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* Przyciski ...


> [!NOTE]
> Można przypisać tylko jedną ILPIP dla każdego wystąpienia maszyny Wirtualnej lub roli. Możesz użyć maksymalnie 5 ILPIPs na subskrypcję. ILPIPs nie są obsługiwane dla maszyn wirtualnych z wieloma kartami.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Dlaczego może zażądać ILPIP?
Jeśli chcesz mieć możliwość nawiązania połączenia z wystąpieniem maszyny Wirtualnej lub roli przez adres IP przypisany do niego bezpośrednio, zamiast korzystać z chmury usługi adresów VIP:&lt;numer portu&gt;, żądanie ILPIP dla maszyny Wirtualnej lub wystąpienia roli.

* **Aktywne FTP** -przypisując ILPIP do maszyny Wirtualnej, może ona odbierać ruch na dowolnym porcie. Punkty końcowe nie są wymagane dla maszyny Wirtualnej do odbierania ruchu.  Zobacz [Omówienie protokołu FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) szczegółowe informacje na temat protokołu FTP.
* **IP ruchu wychodzącego** — ruch wychodzący pochodzące z maszyny Wirtualnej jest mapowany na ILPIP jako źródła i ILPIP unikatowo identyfikuje maszyny Wirtualnej do jednostek zewnętrznych.

> [!NOTE]
> W przeszłości adres ILPIP była określana jako publiczny adres IP (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Zarządzanie ILPIP dla maszyny Wirtualnej
Następujące zadania umożliwiają tworzenie i przypisywanie oraz usunąć ILPIPs z maszyn wirtualnych:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Jak utworzyć żądanie ILPIP podczas tworzenia maszyny Wirtualnej przy użyciu programu PowerShell
Poniższy skrypt programu PowerShell tworzy usługi w chmurze o nazwie *FTPService*, pobiera obraz z platformy Azure, utworzenie maszyny Wirtualnej o nazwie *FTPInstance* korzystając z pobrane obrazu maszyny Wirtualnej do użycia ILPIP ustawia i dodaje Maszyna wirtualna do nowej usługi:

```powershell
New-AzureService -ServiceName FTPService -Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

#Set "current" storage account for the subscription. It will be used as the location of new VM disk

Set-AzureSubscription -SubscriptionName <SubName> -CurrentStorageAccountName <StorageAccountName>

#Create a new VM configuration object

New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"

```
Jeśli chcesz określić inne konto magazynu jako lokalizację nowego dysku maszyny Wirtualnej, możesz użyć **MediaLocation** parametru:

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Jak pobrać informacje ILPIP dla maszyny Wirtualnej
Aby wyświetlić informacje ILPIP, aby maszyna wirtualna utworzona za pomocą poprzedniego skryptu, uruchom następujące polecenie programu PowerShell i sprawdź wartości *publicznego adresu IP* i *PublicIPName*:

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
Aby usunąć ILPIP dodane do maszyny Wirtualnej w poprzednim skrypcie, uruchom następujące polecenie programu PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Jak dodać ILPIP do istniejącej maszyny Wirtualnej
Aby dodać ILPIP do maszyny Wirtualnej utworzonej za pomocą poprzedniego skryptu, uruchom następujące polecenie:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Zarządzanie ILPIP dla wystąpienia roli usług Cloud Services

Aby dodać ILPIP wystąpienie roli usług Cloud Services, wykonaj następujące czynności:

1. Pobierz plik .cscfg dla usługi w chmurze, wykonując kroki opisane w [jak skonfigurować usługi w chmurze](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artykułu.
2. Zaktualizuj plik .cscfg, dodając `InstanceAddress` elementu. Poniższy przykład dodaje ILPIP o nazwie *MyPublicIP* wystąpienia roli o nazwie *WebRole1*: 

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
          <PublicIP name="MyPublicIP" domainNameLabel="WebPublicIP" />
            </PublicIPs>
          </InstanceAddress>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
    ```
3. Przekaż plik .cscfg dla usługi w chmurze, wykonując kroki opisane w [jak skonfigurować usługi w chmurze](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) artykułu.

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Jak pobrać informacje ILPIP dla usługi w chmurze
Aby wyświetlić informacje ILPIP dla każdego wystąpienia roli, uruchom następujące polecenie programu PowerShell i sprawdź wartości *publicznego adresu IP*, *PublicIPName*, *PublicIPDomainNameLabel* i *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Można także użyć `nslookup` zapytań do domeny podrzędnej przez rekord:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie sposobu [adresowania IP](virtual-network-ip-addresses-overview-classic.md) działa w klasycznym modelu wdrażania.
* Dowiedz się więcej o [zastrzeżone adresy IP](virtual-networks-reserved-public-ip.md).
