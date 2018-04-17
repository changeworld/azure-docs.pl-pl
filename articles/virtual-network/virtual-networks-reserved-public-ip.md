---
title: Zarządzanie Azure zastrzeżone adresy IP (klasyczne) - PowerShell | Dokumentacja firmy Microsoft
description: Zrozumienie zastrzeżone adresy IP (klasyczne) i jak nimi zarządzać przy użyciu programu PowerShell.
services: virtual-network
documentationcenter: na
author: genli
manager: cshepard
editor: tysonn
ms.assetid: 34652a55-3ab8-4c2d-8fb2-43684033b191
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/10/2016
ms.author: genli
ms.openlocfilehash: 370f30a61727e6c349f6e043f1aed2d9dba47fca
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="reserved-ip-addresses-classic"></a>Zastrzeżone adresy IP (klasyczne)

> [!div class="op_single_selector"]
> * [Azure Portal](virtual-network-deploy-static-pip-arm-portal.md)
> * [Program PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](virtual-network-deploy-static-pip-arm-cli.md)
> * [Szablon](virtual-network-deploy-static-pip-arm-template.md)
> * [PowerShell (klasyczny)](virtual-networks-reserved-public-ip.md)

Adresy IP na platformie Azure można podzielić na dwie kategorie: dynamiczne i zastrzeżone. Publiczne adresy IP, zarządzane przez usługę Azure są dynamiczne domyślnie. Oznacza to, że adres IP używany dla określonej chmury usługi (VIP) lub uzyskać dostępu do maszyny Wirtualnej lub bezpośrednio wystąpienia roli (ILPIP) mogą ulec zmianie od czasu do czasu, po zasoby są zamknięte lub zatrzymana (cofnięciu przydziału).

Aby uniemożliwić zmianę adresów IP, można zastrzec adresu IP. Zastrzeżonych adresów IP może służyć wyłącznie jako adresu VIP, zapewniając, że adres IP dla usługi w chmurze jest taka sama, nawet jeśli zasoby są zamknięte lub zatrzymana (cofnięciu przydziału). Ponadto można przekonwertować istniejące dynamiczne adresy IP używane jako adresu VIP do zastrzeżonego adresu IP.

> [!IMPORTANT]
> Platforma Azure oferuje dwa różne modele wdrażania związane z tworzeniem zasobów i pracą z nimi: [model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny](../azure-resource-manager/resource-manager-deployment-model.md). Ten artykuł dotyczy klasycznego modelu wdrożenia. Firma Microsoft zaleca, aby w przypadku większości nowych wdrożeń korzystać z modelu opartego na programie Resource Manager. Dowiedz się, jak Zarezerwuj statyczny publiczny adres IP przy użyciu [modelu wdrażania usługi Resource Manager](virtual-network-ip-addresses-overview-arm.md).

Aby dowiedzieć się więcej na temat adresów IP na platformie Azure, przeczytaj [adresów IP](virtual-network-ip-addresses-overview-classic.md) artykułu.

## <a name="when-do-i-need-a-reserved-ip"></a>Jeśli potrzebujesz zastrzeżony adres IP?
* **Aby upewnić się, że adres IP jest zarezerwowana w ramach subskrypcji**. Jeśli chcesz zastrzeżenia adresu IP, który nie jest zwalniany z subskrypcją w żadnym przypadku należy używać zastrzeżonego publicznego adresu IP.  
* **Ma IP pozostanie z usługi w chmurze nawet w poprzek zatrzymana lub alokację stanu (VM)**. Jeśli chcesz, aby usługi można uzyskać dostęp przy użyciu adresu IP który nie powoduje zmiany, nawet wtedy, gdy maszyny wirtualne w usłudze w chmurze są zamknięte lub zatrzymać (cofnięciu przydziału).
* **Aby zagwarantować, że ruch wychodzący z platformy Azure używał przewidywalną adres IP**. Może być Zapora lokalnymi skonfigurowany tak, aby zezwolić tylko na ruch z określonych adresów IP. Przez zarezerwowanie adresów IP, które znasz źródłowy adres IP i nie trzeba zaktualizować reguł zapory z powodu zmiany adresu IP.

## <a name="faq"></a>Często zadawane pytania
1. Dla wszystkich usług platformy Azure można używać zastrzeżonego adresu IP? <br>
    Nie. Zastrzeżonych adresów IP można używać tylko dla maszyn wirtualnych i role wystąpienia usługi w chmurze za pośrednictwem adresu VIP.
2. Jak wiele zastrzeżonych adresów IP może mieć? <br>
    Aby uzyskać więcej informacji, zobacz [Azure ogranicza](../azure-subscription-service-limits.md#networking-limits) artykułu.
3. Dla zarezerwowanych adresów IP jest opłatę? <br>
    Czasami. Aby uzyskać szczegółowe informacje o cenach, zobacz [zastrzeżonego adresu IP Address szczegóły cennika](http://go.microsoft.com/fwlink/?LinkID=398482) strony.
4. Jak zastrzeżenia adresu IP? <br>
    Korzystając z programu PowerShell, [interfejsu API REST zarządzania Azure](https://msdn.microsoft.com/library/azure/dn722420.aspx), lub [portalu Azure](https://portal.azure.com) do zarezerwowania adresu IP w regionie platformy Azure. Zastrzeżony adres IP jest skojarzony z subskrypcją.
5. Czy można używać zastrzeżonego adresu IP z sieci oparte na grupach koligacji wirtualnych? <br>
    Nie. Zastrzeżonych adresów IP są obsługiwane tylko w regionalnych sieci wirtualnych. Zastrzeżonych adresów IP nie są obsługiwane dla sieci wirtualnych, które są skojarzone z grup koligacji. Aby uzyskać więcej informacji o sposobie kojarzenia sieci wirtualnej z region lub grupę koligacji, zobacz [o sieci regionalnych wirtualnych i grup koligacji](virtual-networks-migrate-to-regional-vnet.md) artykułu.

## <a name="manage-reserved-vips"></a>Zarządzanie zarezerwowanych adresów VIP

Upewnij się, zostanie zainstalowany i skonfigurowany programu PowerShell, wykonując kroki opisane w [Instalowanie i konfigurowanie programu PowerShell](/powershell/azure/overview) artykułu. 

Przed użyciem zastrzeżonych adresów IP, należy ją dodać do subskrypcji. Aby utworzyć zastrzeżonego adresu IP z puli publicznych adresów IP dostępne w *środkowe stany USA* lokalizacji, uruchom następujące polecenie:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"
```

Zwróć uwagę, że nie można określić adresu IP, jakie są zastrzeżone. Aby wyświetlić adresy IP, które są zastrzeżone w ramach subskrypcji, uruchom następujące polecenie programu PowerShell i zwróć uwagę na wartości dla *nazwa zastrzeżonego adresu IP* i *adres*:

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
>Po utworzeniu zastrzeżonego adresu IP przy użyciu programu PowerShell nie można określić grupę zasobów, aby utworzyć zastrzeżonego adresu IP w. Azure miejsca go do grupy zasobów o nazwie *sieci domyślne* automatycznie. Jeśli utworzysz zastrzeżonego adresu IP za pomocą [portalu Azure](http://portal.azure.com), można określić dowolną grupę zasobów, możesz wybrać. Jeśli utworzysz zastrzeżonego adresu IP w grupie zasobów innych niż *sieci domyślne* jednak zawsze, gdy odwołanie zastrzeżonego adresu IP za pomocą polecenia takie jak `Get-AzureReservedIP` i `Remove-AzureReservedIP`, musi odwoływać się do nazwy  *Zarezerwowana nazwa grupy zasobów ip — Nazwa grupy*.  Na przykład w przypadku utworzenia zastrzeżony adres IP o nazwie *myReservedIP* w grupie zasobów o nazwie *myResourceGroup*, musi odwoływać się do nazwy zastrzeżonego adresu IP jako *myResourceGroup grupy myReservedIP*.   

Gdy adres IP jest zarezerwowany, pozostaje skojarzona z subskrypcją dopóki nie zostaną usunięte. Aby usunąć zastrzeżonego adresu IP, uruchom następujące polecenie programu PowerShell:

```powershell
Remove-AzureReservedIP -ReservedIPName "MyReservedIP"
```

## <a name="reserve-the-ip-address-of-an-existing-cloud-service"></a>Rezerwacja adresu IP istniejącej usługi w chmurze
Adres IP istniejącej usługi w chmurze może zarezerwować przez dodanie `-ServiceName` parametru. Do zarezerwowania adresu IP usługi w chmurze *TestService* w *środkowe stany USA* lokalizacji, uruchom następujące polecenie programu PowerShell:

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US" -ServiceName TestService
```

## <a name="associate-a-reserved-ip-to-a-new-cloud-service"></a>Skojarzyć zastrzeżonego adresu IP do nowej usługi w chmurze
Poniższy skrypt tworzy nowy zastrzeżony adres IP, a następnie kojarzy ją do nowej usługi w chmurze o nazwie *TestService*.

```powershell
New-AzureReservedIP –ReservedIPName MyReservedIP –Location "Central US"

$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService -ReservedIPName MyReservedIP -Location "Central US"
```

> [!NOTE]
> Podczas tworzenia zastrzeżonego adresu IP do użycia z usługą w chmurze, nadal odwołujesz się do maszyny Wirtualnej za pomocą *VIP:&lt;numer portu >* dla komunikacji przychodzącej. Rezerwacja adresu IP oznacza to, że można połączyć się z maszyną Wirtualną bezpośrednio. Zastrzeżony adres IP jest przypisany do usługi w chmurze, która została wdrożona maszyna wirtualna. Jeśli chcesz się połączyć z maszyną wirtualną przez IP bezpośrednio, należy skonfigurować publicznego adresu IP poziomie wystąpienia. Publiczny adres IP poziomie wystąpienia jest typu publicznego adresu IP (nazywane ILPIP) przypisanej bezpośrednio do maszyny Wirtualnej. Nie można zarezerwować. Aby uzyskać więcej informacji, przeczytaj [poziomie wystąpienia publicznego adresu IP (ILPIP)](virtual-networks-instance-level-public-ip.md) artykułu.
> 

## <a name="remove-a-reserved-ip-from-a-running-deployment"></a>Usuwanie zastrzeżonego adresu IP z wykonywanego wdrożenia
Aby usunąć zastrzeżonego adresu IP, dodać do nowej usługi w chmurze, uruchom następujące polecenie programu PowerShell:

```powershell
Remove-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService
```

> [!NOTE]
> Usuwanie zastrzeżonego adresu IP z wykonywanego wdrożenia nie powoduje usunięcia zastrzeżenia z subskrypcji. Po prostu powoduje zwolnienie IP ma być używany przez inny zasób w ramach subskrypcji.
> 

## <a name="associate-a-reserved-ip-to-a-running-deployment"></a>Skojarzyć zastrzeżonego adresu IP do uruchomionego wdrożenia
Następujące polecenia tworzenia usługi w chmurze o nazwie *TestService2* z nowej maszyny Wirtualnej o nazwie *TestVM2*. Istniejącego zastrzeżonego adresu IP o nazwie *MyReservedIP* jest następnie skojarzonego z usługą w chmurze.

```powershell
$image = Get-AzureVMImage|?{$_.ImageName -like "*RightImage-Windows-2012R2-x64*"}

New-AzureVMConfig -Name TestVM2 -InstanceSize Small -ImageName $image.ImageName `
| Add-AzureProvisioningConfig -Windows -AdminUsername adminuser -Password MyP@ssw0rd!! `
| New-AzureVM -ServiceName TestService2 -Location "Central US"

Set-AzureReservedIPAssociation -ReservedIPName MyReservedIP -ServiceName TestService2
```

## <a name="associate-a-reserved-ip-to-a-cloud-service-by-using-a-service-configuration-file"></a>Skojarzyć zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji usługi
Można również skojarzyć zastrzeżonego adresu IP do usługi w chmurze przy użyciu pliku konfiguracji (CSCFG) usługi. Następujące xml przykładowych pokazano, jak skonfigurować usługi w chmurze Użyj zastrzeżonego adresu VIP o nazwie *MyReservedIP*:

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

## <a name="next-steps"></a>Kolejne kroki
* Zrozumienie sposobu [adresowanie IP](virtual-network-ip-addresses-overview-classic.md) działa w klasycznym modelu wdrażania.
* Dowiedz się więcej o [prywatne adresy IP zarezerwowane](virtual-networks-reserved-private-ip.md).
* Dowiedz się więcej o [adresy IP publicznego poziom wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md).

