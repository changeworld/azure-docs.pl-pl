---
title: Zarządzanie Azure zastrzeżone adresy IP (klasyczny) | Dokumentacja firmy Microsoft
description: Zastrzeżone adresy IP (klasyczny) i jak nimi zarządzać przy użyciu programu Azure PowerShell i wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/12/2018
ms.author: genli
ms.openlocfilehash: 8afed4eb1add0ba3a7db474e54b2f78a0babab06
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60789081"
---
# <a name="reserved-ip-addresses-classic-deployment"></a>Zastrzeżone adresy IP (wdrażanie klasyczne)

 Adresy IP na platformie Azure można podzielić na dwie kategorie: dynamiczne i zastrzeżonych. Publiczne adresy IP, zarządzane przez platformę Azure są dynamiczne domyślnie. Oznacza to, że adres IP używany dla danej usługi w chmurze (VIP) lub do dostępu do maszyny Wirtualnej lub wystąpienia roli bezpośrednio (ILPIP) można zmienić od czasu do czasu, gdy zasoby są zamknięty lub zatrzymana (przydział zostanie cofnięty).

Aby uniemożliwić zmianę adresów IP, możesz zarezerwować adres IP. Zastrzeżone adresy IP, może służyć jedynie jako adres VIP, zapewniając, że adres IP dla usługi w chmurze pozostaje taki sam, nawet gdy zasoby są zamknięty lub zatrzymana (przydział zostanie cofnięty). Ponadto można przekonwertować istniejących dynamiczne adresy IP używane jako adres VIP do zastrzeżonego adresu IP.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi:  [model wdrażania przy użyciu usługi Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak Zarezerwuj statyczny publiczny adres IP, korzystając [modelu wdrażania usługi Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Aby dowiedzieć się więcej o adresach IP na platformie Azure, przeczytaj [adresów IP](virtual-network-ip-addresses-overview-classic.md) artykułu.

## <a name="when-do-i-need-a-reserved-ip"></a>Jeśli potrzebujesz zastrzeżonego adresu IP?
* **Chcesz się upewnić, że adres IP jest zarezerwowana w ramach subskrypcji**. Jeśli chcesz zarezerwować adres IP, który nie jest zwalniane z subskrypcji w żadnym wypadku, należy używać zastrzeżonych publicznych adresów IP.  
* **Ma adres IP pozostanie z usługi w chmurze nawet na zatrzymane lub stanu (VM) z cofniętą alokacją**. Jeśli chcesz, aby usługi były dostępne przy użyciu adresu IP to nie ulega zmianie, nawet wtedy, gdy maszyny wirtualne w usłudze w chmurze są zamykane lub zatrzymano (cofnięto przydział).
* **Chcesz się upewnić, że ruch wychodzący z platformy Azure używa przewidywalne adresu IP**. Może być skonfigurowany tak, aby zezwolić tylko na ruch z określonych adresów IP zapory w środowisku lokalnym. Rezerwowanie adresów IP, możesz znać adres IP źródła i trzeba zaktualizować reguły zapory z powodu zmiany adresu IP.

## <a name="faqs"></a>Często zadawane pytania
- Czy można używać zastrzeżonego adresu IP, dla wszystkich usług platformy Azure?
    Nie. Zastrzeżone adresy IP należy używać tylko dla maszyn wirtualnych i ról wystąpień usługi w chmurze dostępnych za pośrednictwem adresu VIP.
- Jak wiele zastrzeżonych adresów IP można mieć?
    Aby uzyskać więcej informacji, zobacz [limitów platformy Azure](../azure-subscription-service-limits.md#networking-limits) artykułu.
- Zastrzeżone adresy IP jest opłaty?
    Czasami. Aby uzyskać szczegółowe informacje o cenach, zobacz [zastrzeżonego adresu IP adres — szczegóły cennika](https://go.microsoft.com/fwlink/?LinkID=398482) strony.
- Jak zastrzec adres IP?
    Można użyć programu PowerShell, [interfejsu API REST zarządzania Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), lub [witryny Azure portal](https://portal.azure.com) do rezerwowania adresu IP w regionie platformy Azure. Zastrzeżony adres IP jest skojarzony z subskrypcją.
- Czy można używać zastrzeżonego adresu IP, za pomocą sieci wirtualne oparte na grupie koligacji?
    Nie. Zastrzeżone adresy IP są obsługiwane tylko w regionalnych sieciach wirtualnych. Zastrzeżone adresy IP nie są obsługiwane dla sieci wirtualnych, które są skojarzone z grup koligacji. Aby uzyskać więcej informacji o kojarzeniu sieci wirtualnej z region lub grupa koligacji, zobacz [o regionalnej sieci i grup koligacji](virtual-networks-migrate-to-regional-vnet.md) artykułu.

## <a name="manage-reserved-vips"></a>Zarządzanie zarezerwowanych adresów VIP

### <a name="using-azure-powershell-classic"></a>Przy użyciu programu Azure PowerShell (wersja klasyczna)

Przed użyciem zarezerwowane adresy IP, należy dodać go do swojej subskrypcji. Utwórz zastrzeżony adres IP z puli publicznych adresów IP dostępne w *środkowe stany USA* lokalizacji w następujący sposób:

> [!NOTE]
> Dla klasycznego modelu wdrażania należy zainstalować wersję zarządzania usługą programu Azure PowerShell. Aby uzyskać więcej informacji, zobacz [Instalowanie modułu Azure PowerShell Service Management](https://docs.microsoft.com/powershell/azure/servicemanagement/install-azure-ps?view=azuresmps-4.0.0). 

  ```powershell
    New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
  ```
Należy zauważyć, że nie można określić adresu IP, co jest zarezerwowana. Aby zobaczyć, jakie adresy IP są zarezerwowane w ramach subskrypcji, uruchom następujące polecenie programu PowerShell i zwróć uwagę na wartości dla *nazwa zastrzeżonego adresu IP* i *adres*:

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
>Kiedy tworzysz zastrzeżonego adresu IP przy użyciu programu PowerShell, nie można określić grupę zasobów, aby utworzyć zastrzeżonego adresu IP w. Azure umieszcza go do grupy zasobów o nazwie *domyślnej sieci* automatycznie. Jeśli tworzysz zastrzeżonego adresu IP za pomocą [witryny Azure portal](https://portal.azure.com), możesz określić wszystkie grupy zasobów, możesz wybrać. Jeśli tworzysz zastrzeżonego adresu IP w grupie zasobów innej niż *domyślnej sieci* jednak zawsze, gdy odwołujesz się zastrzeżonego adresu IP za pomocą poleceń takich jak `Get-AzureReservedIP` i `Remove-AzureReservedIP`, musi odwoływać się do nazwy  *Nazwa grupy zasobów zarezerwowanych adresów ip — Nazwa grupy*.  Na przykład, jeśli tworzysz zastrzeżony adres IP o nazwie *myReservedIP* w grupie zasobów o nazwie *myResourceGroup*, musi odwoływać się nazwa zastrzeżonego adresu IP jako *grupie myResourceGroup myReservedIP*.   


Po jest zastrzeżony adres IP, pozostaje skojarzona z subskrypcji do momentu ich usunięcia. Usuń zastrzeżony adres IP w następujący sposób:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

### <a name="using-azure-cli-classic"></a>Przy użyciu wiersza polecenia platformy Azure (wersja klasyczna)
Utwórz zastrzeżony adres IP z puli publicznych adresów IP dostępne w *środkowe stany USA* lokalizacji przy użyciu następuje klasyczny interfejs wiersza polecenia platformy Azure:

> [!NOTE]
> Dla wdrożenia klasycznego należy użyć klasycznego wiersza polecenia platformy Azure. Aby uzyskać informacje o instalowaniu klasycznego wiersza polecenia platformy Azure, zobacz [zainstalować Azure klasyczny interfejs wiersza polecenia](https://docs.microsoft.com/cli/azure/install-classic-cli?view=azure-cli-latest)
  
 Polecenie:
 
```azurecli
azure network reserved-ip create <name> <location>
```
Przykład:
 ```azurecli
 azure network reserved-ip create MyReservedIP centralus
 ```

Można wyświetlić, jakie adresy IP zostało zastrzeżonych w subskrypcji przy użyciu wiersza polecenia platformy Azure w następujący sposób: 

Polecenie:
```azurecli
azure network reserved-ip list
```
Po jest zastrzeżony adres IP, pozostaje skojarzona z subskrypcji do momentu ich usunięcia. Usuń zastrzeżony adres IP w następujący sposób:

Polecenie:

 ```azurecli
 azure network reserved-ip delete <name>
 ```
  Przykład:  
 ```azurecli
 azure network reserved-ip delete MyReservedIP
 ```
## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Zastrzec adres IP istniejącej usługi w chmurze
Możesz zarezerwować adres IP istniejącej usługi w chmurze, dodając `-ServiceName` parametru. Zastrzec adres IP usługi w chmurze *TestService* w *środkowe stany USA* lokalizacji w następujący sposób:

- Przy użyciu programu Azure PowerShell (klasyczny):

  ```powershell
  New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
  ```

- Przy użyciu wiersza polecenia platformy Azure (model klasyczny):
  
    Polecenie:

    ```azurecli
     azure network reserved-ip create <name> <location> -r <service-name> -d <deployment-name>
    ```
    Przykład:

    ```azurecli
      azure network reserved-ip create MyReservedIP centralus -r TestService -d asmtest8942
    ```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Skojarzenie zastrzeżonego adresu IP z nową usługą w chmurze
Poniższy skrypt tworzy nowy zastrzeżony adres IP, a następnie kojarzy ją do nowej usługi w chmurze o nazwie *TestService*.

### <a name="using-azure-powershell-classic"></a>Przy użyciu programu Azure PowerShell (wersja klasyczna)
```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```
> [!NOTE]
> Kiedy tworzysz zastrzeżonego adresu IP do użycia z usługą w chmurze, możesz nadal odwołują się do maszyny Wirtualnej przy użyciu *adresów VIP:&lt;numer portu >* dla komunikacji przychodzącej. Rezerwowanie adresów IP nie oznacza, że możesz bezpośrednio łączyć się maszyna wirtualna. Zastrzeżony adres IP jest przypisany do usługi w chmurze, która została wdrożona maszyna wirtualna do. Jeśli chcesz połączyć z maszyną wirtualną na podstawie IP bezpośrednio, należy skonfigurować publiczny adres IP na poziomie wystąpienia. Publiczny adres IP na poziomie wystąpienia jest typem publicznym adresem IP (nazywanego ILPIP), to przypisać bezpośrednio do maszyny Wirtualnej. Nie można zarezerwować. Aby uzyskać więcej informacji, przeczytaj [publicznego adresu IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md) artykułu.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Usuń zastrzeżony adres IP z bieżącym wdrożeniu

Usuń zastrzeżony adres IP, dodawane do nowej usługi w chmurze w następujący sposób: 
### <a name="using-azure-powershell-classic"></a>Przy użyciu programu Azure PowerShell (wersja klasyczna)

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

### <a name="using-azure-cli-classic"></a>Przy użyciu wiersza polecenia platformy Azure (wersja klasyczna)
Polecenie:

```azurecli
azure network reserved-ip disassociate <name> <service-name> <deployment-name>
```

Przykład:

```azurecli
azure network reserved-ip disassociate MyReservedIP TestService asmtest8942
```

> [!NOTE]
> Usuwanie zastrzeżonego adresu IP z bieżącym wdrożeniu nie powoduje usunięcia rezerwacji z Twojej subskrypcji. Po prostu powoduje zwolnienie adresu IP, który będzie używany przez inny zasób w ramach subskrypcji.
> 

Aby całkowicie usunąć zastrzeżonego adresu IP z subskrypcji, uruchom następujące polecenie:

Polecenie:

```azurecli
azure network reserved-ip delete <name>
```
Przykład:

```azurecli
azure network reserved-ip delete MyReservedIP
```

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Skojarzenie zastrzeżonego adresu IP do uruchomionego wdrożenia

### <a name="using-azure-powershell-classic"></a>Przy użyciu programu Azure PowerShell (wersja klasyczna)

Następujące polecenia tworzą usługi w chmurze o nazwie *TestService2* przy użyciu nowej maszyny Wirtualnej o nazwie *TestVM2*. Istniejące zarezerwowanych adresów IP o nazwie *MyReservedIP* jest następnie powiązanych z usługą w chmurze.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

### <a name="using-azure-cli-classic"></a>Przy użyciu wiersza polecenia platformy Azure (wersja klasyczna)
Nowy zastrzeżony adres IP można skojarzyć do Twojej uruchomionego wdrażania usługi w chmurze przy użyciu wiersza polecenia platformy Azure w następujący sposób:

Polecenie:
```azurecli
azure network reserved-ip associate <name> <service-name> <deployment-name>
```
Przykład:
```azurecli
azure network reserved-ip associate MyReservedIP TestService asmtest8942
```
## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Skojarzenie zastrzeżonego adresu ip do usługi w chmurze przy użyciu pliku konfiguracji usługi
Można również skojarzyć zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji (CSCFG) usługi. Następujący przykładowy kod xml pokazuje, jak skonfigurować usługę chmurową, aby używać zastrzeżonego adresu VIP o nazwie *MyReservedIP*:
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
## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie sposobu [adresowania IP](virtual-network-ip-addresses-overview-classic.md) działa w klasycznym modelu wdrażania.
* Dowiedz się więcej o [prywatne adresy IP zarezerwowane](virtual-networks-reserved-private-ip.md).
* Dowiedz się więcej o [adresy IP publicznego poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md).

