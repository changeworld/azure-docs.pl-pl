---
title: Publiczne adresy IP na poziomie wystąpienia platformy Azure | Microsoft Docs
description: Opis publicznych adresów IP na poziomie wystąpienia (ILPIP) i sposób zarządzania nimi przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 07eef6ec-7dfe-4c4d-a2c2-be0abfb48ec5
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/03/2018
ms.author: genli
ms.openlocfilehash: d92832d1eee995e8883dc6c8ed0f58c9755e40f8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058407"
---
# <a name="instance-level-public-ip-classic-overview"></a>Publiczny adres IP na poziomie wystąpienia (klasyczny) — Omówienie
Publiczny adres IP na poziomie wystąpienia (ILPIP) jest publicznym adresem IP, który można przypisać bezpośrednio do maszyny wirtualnej lub Cloud Services wystąpienia roli, a nie do usługi w chmurze, w której znajduje się maszyna wirtualna lub wystąpienie roli. ILPIP nie ma miejsca na wirtualny adres IP (VIP) przypisany do usługi w chmurze. Jest to dodatkowy adres IP, którego można użyć do nawiązania bezpośredniego połączenia z maszyną wirtualną lub wystąpieniem roli.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca Tworzenie maszyn wirtualnych za pośrednictwem Menedżer zasobów. Zapoznaj się ze sposobem działania [adresów IP](virtual-network-ip-addresses-overview-classic.md) na platformie Azure.

![Różnica między ILPIP i VIP](./media/virtual-networks-instance-level-public-ip/Figure1.png)

Jak pokazano na rysunku 1, usługa w chmurze jest dostępna przy użyciu adresu VIP, podczas gdy pojedyncze maszyny wirtualne są zwykle dostępne przy&lt;użyciu adresu&gt;VIP: numer portu. Przypisanie ILPIP do określonej maszyny wirtualnej może uzyskać dostęp do tej maszyny wirtualnej bezpośrednio przy użyciu tego adresu IP.

Podczas tworzenia usługi w chmurze na platformie Azure odpowiednie rekordy A DNS są tworzone automatycznie, aby zezwolić na dostęp do usługi za pośrednictwem w pełni kwalifikowanej nazwy domeny (FQDN), zamiast korzystać z rzeczywistego adresu VIP. Ten sam proces występuje dla elementu ILPIP, co pozwala na dostęp do maszyny wirtualnej lub wystąpienia roli przy użyciu nazwy FQDN, a nie ILPIP. Na przykład jeśli utworzysz usługę w chmurze o nazwie *contosoadservice*i skonfigurujesz rolę sieci Web o nazwie *contosoweb* z dwoma wystąpieniami, a w pliku. `domainNameLabel` cscfg zostanie ustawiona wartość *WebPublicIP*, platforma Azure rejestruje następujące rekordy a dla Liczba


* WebPublicIP.0.contosoadservice.cloudapp.net
* WebPublicIP.1.contosoadservice.cloudapp.net
* Przyciski ...


> [!NOTE]
> Dla każdej maszyny wirtualnej lub wystąpienia roli można przypisać tylko jeden ILPIP. Możesz użyć maksymalnie 5 ILPIPs na subskrypcję. ILPIPs nie są obsługiwane w przypadku maszyn wirtualnych z obsługą wiele kart sieciowych.
> 
> 

## <a name="why-would-i-request-an-ilpip"></a>Dlaczego mam zażądać ILPIP?
Jeśli chcesz mieć możliwość nawiązywania połączenia z maszyną wirtualną lub wystąpieniem roli za pomocą przypisanego do niego adresu IP, zamiast korzystać z adresów VIP usługi w&lt;chmurze:&gt;numer portu, zażądaj ILPIP dla maszyny wirtualnej lub wystąpienia roli.

* **Aktywne FTP** — przypisanie ILPIP do maszyny wirtualnej może odbierać ruch na dowolnym porcie. Punkty końcowe nie są wymagane do odbierania ruchu przez maszynę wirtualną.  Szczegółowe informacje na temat protokołu FTP można znaleźć w temacie [Omówienie protokołu FTP](https://en.wikipedia.org/wiki/File_Transfer_Protocol#Protocol_overview) .
* **Wychodzący** ruch wychodzący IP pochodzący z maszyny wirtualnej jest mapowany na ILPIP, ponieważ źródło i ILPIP jednoznacznie IDENTYFIKUJą maszynę wirtualną do jednostek zewnętrznych.

> [!NOTE]
> W przeszłości adres ILPIP był określany jako publiczny adres IP (PIP).
> 

## <a name="manage-an-ilpip-for-a-vm"></a>Zarządzanie ILPIPą dla maszyny wirtualnej
Poniższe zadania umożliwiają tworzenie, przypisywanie i usuwanie ILPIPs z maszyn wirtualnych:

### <a name="how-to-request-an-ilpip-during-vm-creation-using-powershell"></a>Jak zażądać ILPIP podczas tworzenia maszyny wirtualnej przy użyciu programu PowerShell
Poniższy skrypt programu PowerShell tworzy usługę w chmurze o nazwie *FTPService*, pobiera obraz z platformy Azure, tworzy maszynę wirtualną o nazwie *FTPInstance* przy użyciu pobranego obrazu, ustawia maszynę wirtualną do używania ILPIP i dodaje maszynę wirtualną do nowej usługi:

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
Jeśli chcesz określić inne konto magazynu jako lokalizację nowego dysku maszyny wirtualnej, możesz użyć parametru **MediaLocation** :

```powershell
    New-AzureVMConfig -Name FTPInstance -InstanceSize Small -ImageName $image.ImageName `
     -MediaLocation https://management.core.windows.net/<SubscriptionID>/services/storageservices/<StorageAccountName> `
    | Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
    | Set-AzurePublicIP -PublicIPName ftpip | New-AzureVM -ServiceName FTPService -Location "Central US"
```

### <a name="how-to-retrieve-ilpip-information-for-a-vm"></a>Jak pobrać informacje o ILPIP dla maszyny wirtualnej
Aby wyświetlić informacje ILPIP dla maszyny wirtualnej utworzonej przy użyciu poprzedniego skryptu, uruchom następujące polecenie programu PowerShell i obserwuj wartości dla *PublicIPAddress* i *PublicIPName*:

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

### <a name="how-to-remove-an-ilpip-from-a-vm"></a>Jak usunąć ILPIP z maszyny wirtualnej
Aby usunąć ILPIP dodane do maszyny wirtualnej w poprzednim skrypcie, uruchom następujące polecenie programu PowerShell:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Remove-AzurePublicIP | Update-AzureVM
```

### <a name="how-to-add-an-ilpip-to-an-existing-vm"></a>Jak dodać ILPIP do istniejącej maszyny wirtualnej
Aby dodać ILPIP do maszyny wirtualnej utworzonej przy użyciu skryptu, uruchom następujące polecenie:

```powershell
Get-AzureVM -ServiceName FTPService -Name FTPInstance | Set-AzurePublicIP -PublicIPName ftpip2 | Update-AzureVM
```

## <a name="manage-an-ilpip-for-a-cloud-services-role-instance"></a>Zarządzanie ILPIP dla wystąpienia roli Cloud Services

Aby dodać ILPIP do wystąpienia roli Cloud Services, wykonaj następujące czynności:

1. Pobierz plik cscfg dla usługi w chmurze, wykonując czynności opisane w artykule [jak skonfigurować Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .
2. Zaktualizuj plik. cscfg przez dodanie `InstanceAddress` elementu. Poniższy przykład dodaje ILPIP o nazwie *MyPublicIP* do wystąpienia roli o nazwie *WebRole1*: 

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
3. Przekaż plik cscfg dla usługi w chmurze, wykonując kroki opisane w artykule [jak skonfigurować Cloud Services](../cloud-services/cloud-services-how-to-configure-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#reconfigure-your-cscfg) .

### <a name="how-to-retrieve-ilpip-information-for-a-cloud-service"></a>Jak pobrać informacje ILPIP dla usługi w chmurze
Aby wyświetlić informacje ILPIP na wystąpienie roli, uruchom następujące polecenie programu PowerShell i obserwuj wartości dla *PublicIPAddress*, *PublicIPName*, *PublicIPDomainNameLabel* i *PublicIPFqdns*:

```powershell
Add-AzureAccount

$roles = Get-AzureRole -ServiceName <Cloud Service Name> -Slot Production -RoleName WebRole1 -InstanceDetails

$roles[0].PublicIPAddress
$roles[1].PublicIPAddress
```

Można również użyć `nslookup` do kwerendy rekordu podrzędnego poddomeny:

```batch
nslookup WebPublicIP.0.<Cloud Service Name>.cloudapp.net
``` 

## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z działaniem [adresów IP](virtual-network-ip-addresses-overview-classic.md) w klasycznym modelu wdrażania.
* Dowiedz się więcej na temat [zarezerwowanych adresów IP](virtual-networks-reserved-public-ip.md).
