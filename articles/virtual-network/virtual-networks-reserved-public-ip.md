---
title: Zarządzanie zarezerwowanymi adresami IP platformy Azure (wersja klasyczna) | Microsoft Docs
description: Informacje na temat zarezerwowanych adresów IP (klasyczny) i sposobu zarządzania nimi przy użyciu Azure PowerShell i interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: c1de86c1d12109853bb5d6d1aac4143caab9199f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75638225"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Adresy Zastrzeżony adres IP (wdrożenie klasyczne)

 Adresy IP na platformie Azure dzielą się na dwie kategorie: dynamiczne i zarezerwowane. Publiczne adresy IP zarządzane przez platformę Azure są domyślnie dynamiczne. Oznacza to, że adres IP używany przez daną usługę w chmurze (VIP) lub dostęp do maszyny wirtualnej lub wystąpienia roli bezpośrednio (ILPIP) może ulec zmianie od czasu do czasu, gdy zasoby są wyłączone lub zatrzymane (cofnięto przydział).

Aby uniemożliwić zmianę adresów IP, można zarezerwować adres IP. Zastrzeżone adresy IP mogą być używane tylko jako adres VIP, co zapewnia, że adres IP usługi w chmurze pozostaje taki sam, nawet gdy zasoby są wyłączone lub zatrzymane (cofnięto przydział). Ponadto można skonwertować istniejące dynamiczne adresy IP używane jako adresy VIP do zastrzeżonego adresu.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak zarezerwować statyczny publiczny adres IP przy użyciu [Menedżer zasobów model wdrażania](virtual-network-ip-addresses-overview-arm.md).

Aby dowiedzieć się więcej o adresach IP na platformie Azure, przeczytaj artykuł dotyczący [adresów IP](virtual-network-ip-addresses-overview-classic.md) .

## <a name="when-do-i-need-a-reserved-ip"></a>Kiedy należy zastrzeżony adres IP?
* **Chcesz upewnić się, że adres IP jest zarezerwowany w subskrypcji**. Jeśli chcesz zarezerwować adres IP, który nie jest wydawany z Twojej subskrypcji w żadnej sytuacji, należy użyć zastrzeżonego publicznego adresu IP.  
* **Chcesz, aby adres IP był nadal używany przez usługę w chmurze, nawet w przypadku stanu zatrzymania lub cofania przydziału (maszyny wirtualne)** . Jeśli chcesz uzyskiwać dostęp do usługi przy użyciu adresu IP, który nie jest zmieniany, nawet jeśli maszyny wirtualne w usłudze w chmurze zostaną zamknięte lub zatrzymane (cofnięto przydział).
* **Chcesz mieć pewność, że ruch wychodzący z platformy Azure korzysta z przewidywalnego adresu IP**. Zapora lokalna jest skonfigurowana tak, aby zezwalać tylko na ruch z określonych adresów IP. Dzięki zapewnieniu adresu IP znany jest źródłowy adres IP i nie trzeba aktualizować reguł zapory ze względu na zmianę adresu IP.

## <a name="faqs"></a>Często zadawane pytania
- Czy można użyć zastrzeżonego adresu IP dla wszystkich usług platformy Azure?
    Nie. Zastrzeżone adresy IP mogą być używane tylko dla ról maszyn wirtualnych i wystąpień usługi w chmurze udostępnianych za pośrednictwem adresu VIP.
- Ile adresów IP zarezerwowanych istnieje?
    Aby uzyskać szczegółowe informacje, zobacz artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) .
- Czy jest naliczana opłata za zastrzeżone adresy IP?
    Również. Aby uzyskać szczegółowe informacje o cenach, zobacz stronę [szczegółów cennika adresu zastrzeżony adres IP](https://go.microsoft.com/fwlink/?LinkID=398482) .
- Jak mogę zarezerwować adresu IP?
    Aby zarezerwować adres IP w regionie świadczenia usługi Azure, można użyć programu PowerShell, [interfejsu API REST zarządzania platformy Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx)lub [Azure Portal](https://portal.azure.com) . Zastrzeżony adres IP jest skojarzony z subskrypcją.
- Czy można użyć zastrzeżonego adresu IP z koligacją opartą na grupach sieci wirtualnych?
    Nie. Zastrzeżone adresy IP są obsługiwane tylko w regionie sieci wirtualnych. Zastrzeżone adresy IP nie są obsługiwane w przypadku sieci wirtualnych skojarzonych z grupami koligacji. Aby uzyskać więcej informacji na temat kojarzenia sieci wirtualnej z regionem lub grupą koligacji, zobacz artykuł [Informacje o regionalnych sieci wirtualnych i grupach koligacji](virtual-networks-migrate-to-regional-vnet.md) .

## <a name="manage-reserved-vips"></a>Zarządzanie zastrzeżonymi Vipmi

### <a name="using-azure-powershell-classic"></a>Używanie Azure PowerShell (klasyczne)

Aby można było używać zarezerwowanych adresów IP, należy dodać je do subskrypcji. Utwórz zastrzeżony adres IP z puli publicznych adresów IP dostępnych w lokalizacji *środkowe stany USA* w następujący sposób:

> [!NOTE]
> W przypadku klasycznego modelu wdrażania należy zainstalować wersję programu Service Management Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie modułu zarządzania usługami programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Należy jednak zauważyć, że nie można określić, który adres IP jest zarezerwowany. Aby wyświetlić adresy IP zarezerwowane w ramach subskrypcji, uruchom następujące polecenie programu PowerShell i zwróć uwagę na wartości parametrów *ReservedIPName* i *Address*:

```powershell
Get-AzureReservedIP
```

Oczekiwane dane wyjściowe:

    ReservedIPName       : MyReservedIP
    Address              : 23.101.114.211
    Id                   : d73be9dd-db12-4b5e-98c8-bc62e7c42041
    Label                :
    Location             : Central US
    State                : Created
    InUse                : False
    ServiceName          :
    DeploymentName       :
    OperationDescription : Get-AzureReservedIP
    OperationId          : 55e4f245-82e4-9c66-9bd8-273e815ce30a
    OperationStatus      : Succeeded

>[!NOTE]
>Podczas tworzenia zastrzeżonego adresu IP przy użyciu programu PowerShell nie można określić grupy zasobów, aby utworzyć zastrzeżony adres IP w programie. Platforma Azure umieszcza je w grupie zasobów o nazwie *default-Networking* automatycznie. Jeśli utworzysz zastrzeżony adres IP przy użyciu [Azure Portal](https://portal.azure.com), możesz określić dowolną grupę zasobów. Jeśli jednak zostanie utworzony zastrzeżony adres IP w grupie zasobów innej niż *Domyślna —* w przypadku odwołania do zastrzeżonego adresu IP za pomocą poleceń takich jak `Get-AzureReservedIP` i `Remove-AzureReservedIP`, należy odwołać się do nazwy *grupy zasobów Grupa-Grupa-nazwa zastrzeżone-IP-Name*.  Jeśli na przykład utworzysz zastrzeżony adres IP o nazwie *myReservedIP* w grupie zasobów o nazwie Moja *zasobów*, musisz odwołać się do nazwy zastrzeżonego adresu IP jako *Grupa zasobów myReservedIP*.   


Gdy adres IP jest zarezerwowany, pozostaje on skojarzony z subskrypcją, dopóki nie zostanie usunięty. Usuń zastrzeżony adres IP w następujący sposób:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure (wersja klasyczna)
Utwórz zastrzeżony adres IP z puli publicznych adresów IP dostępnych w lokalizacji *środkowe stany USA* przy użyciu klasycznego interfejsu wiersza polecenia platformy Azure:

> [!NOTE]
> W przypadku wdrożenia klasycznego należy użyć klasycznego interfejsu wiersza polecenia platformy Azure. Aby uzyskać informacje o instalowaniu klasycznego interfejsu wiersza polecenia platformy Azure, zobacz [Instalowanie klasycznego interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Polecenie:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Przykład:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Możesz wyświetlić adresy IP zarezerwowane w ramach subskrypcji przy użyciu interfejsu wiersza polecenia platformy Azure w następujący sposób: 

Polecenie:
```azurecli
azure network reserved-ip list
```
Gdy adres IP jest zarezerwowany, pozostaje on skojarzony z subskrypcją, dopóki nie zostanie usunięty. Usuń zastrzeżony adres IP w następujący sposób:

Polecenie:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Przykład:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Zarezerwuj adres IP istniejącej usługi w chmurze
Można zarezerwować adres IP istniejącej usługi w chmurze, dodając parametr `-ServiceName`. Zarezerwuj adres IP usługi w chmurze *TestService* w lokalizacji *środkowe stany USA* w następujący sposób:

- Używanie Azure PowerShell (klasyczny):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Korzystanie z interfejsu wiersza polecenia platformy Azure (wersja klasyczna):
  
    Polecenie:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Przykład:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Skojarz zastrzeżony adres IP z nową usługą w chmurze
Poniższy skrypt tworzy nowy zastrzeżony adres IP, a następnie kojarzy go z nową usługą w chmurze o nazwie *TestService*.

### <a name="using-azure-powershell-classic"></a>Używanie Azure PowerShell (klasyczne)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Gdy tworzysz zastrzeżony adres IP do użycia z usługą w chmurze, nadal odwołujesz się do maszyny wirtualnej przy użyciu *adresu VIP:&lt;numer portu >* na potrzeby komunikacji przychodzącej. Rezerwowanie adresu IP nie oznacza, że można połączyć się bezpośrednio z maszyną wirtualną. Zastrzeżony adres IP jest przypisywany do usługi w chmurze, w której została wdrożona maszyna wirtualna. Jeśli chcesz połączyć się bezpośrednio z maszyną wirtualną za pomocą adresu IP, musisz skonfigurować publiczny adres IP na poziomie wystąpienia. Publiczny adres IP na poziomie wystąpienia to typ publicznego adresu IP (nazywany ILPIP) przypisany bezpośrednio do maszyny wirtualnej. Nie może być zarezerwowane. Aby uzyskać więcej informacji, przeczytaj artykuł [publiczny adres IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Usuwanie zastrzeżonego adresu IP z działającego wdrożenia

Usuń zastrzeżony adres IP dodany do nowej usługi w chmurze w następujący sposób: 
### <a name="using-azure-powershell-classic"></a>Używanie Azure PowerShell (klasyczne)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure (wersja klasyczna)
Polecenie:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Przykład:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Usunięcie zastrzeżonego adresu IP z działającego wdrożenia nie powoduje usunięcia rezerwacji z subskrypcji. Po prostu zwalnia adres IP do użycia przez inny zasób w ramach subskrypcji.
> 

Aby całkowicie usunąć zarezerwowany adres IP z subskrypcji, uruchom następujące polecenie:

Polecenie:

```azurecli
azure network reserved-ip delete <name>
```
Przykład:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Skojarz zastrzeżony adres IP z uruchomionym wdrożeniem

### <a name="using-azure-powershell-classic"></a>Używanie Azure PowerShell (klasyczne)

Następujące polecenia tworzą usługę w chmurze o nazwie *TestService2* z nową maszyną wirtualną o nazwie *TestVM2*. Istniejący zastrzeżony adres IP o nazwie *MyReservedIP* jest następnie skojarzony z usługą w chmurze.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure (wersja klasyczna)
Nowy zastrzeżony adres IP można skojarzyć z uruchomionym wdrożeniem usługi w chmurze przy użyciu interfejsu wiersza polecenia platformy Azure w następujący sposób:

Polecenie:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Przykład:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Kojarzenie zastrzeżonego adresu IP z usługą w chmurze przy użyciu pliku konfiguracji usługi
Można także skojarzyć zastrzeżony adres IP z usługą w chmurze przy użyciu pliku konfiguracji usługi (CSCFG). Poniższy przykładowy kod XML pokazuje, jak skonfigurować usługę w chmurze do korzystania z zastrzeżonego adresu VIP o nazwie *MyReservedIP*:
```
   <?xml version="1.0" encoding="utf-8"?>
    <ServiceConfiguration serviceName="ReservedIPSample" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2014-01.2.3">
      <Role name="WebRole1">
        <Instances count="1" />
        <ConfigurationSettings>
          <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
        </ConfigurationSettings>
      </Role>
      <NetworkConfiguration>
        <AddressAssignments>
          <ReservedIPs>
           <ReservedIP name="MyReservedIP"/>
          </ReservedIPs>
        </AddressAssignments>
      </NetworkConfiguration>
    </ServiceConfiguration>
```
## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z działaniem [adresów IP](virtual-network-ip-addresses-overview-classic.md) w klasycznym modelu wdrażania.
* Dowiedz się więcej na temat [zarezerwowanych prywatnych adresów IP](virtual-networks-reserved-private-ip.md).
* Dowiedz się więcej o [publicznych adresach IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md).

