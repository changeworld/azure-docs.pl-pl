---
title: 'Usuwanie bramy sieci wirtualnej: środowiska PowerShell: klasyczny Portal Azure | Dokumentacja firmy Microsoft'
description: Usuń bramę sieci wirtualnej w klasycznym modelu wdrażania przy użyciu programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: b1bc18307227a728e2bc8fd95e30fdc1cbdb8c59
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "24994660"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (klasyczne)
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager — program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Classic — PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>
>

W tym artykule opisano, jak usunąć bramę sieci VPN w klasycznym modelu wdrażania przy użyciu programu PowerShell. Po usunięciu bramy sieci wirtualnej, zmodyfikuj plik konfiguracji sieci, aby usunąć elementy, które są już używane.

##<a name="connect"></a>Krok 1: Połączenia z platformą Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Zainstaluj najnowsze poleceń cmdlet programu PowerShell.

Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell Azure usługi zarządzania (ko). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Połącz z kontem platformy Azure. 

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Krok 2: Eksportowanie i wyświetlania pliku konfiguracji sieci

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. Możesz użyć tego pliku do obu widoku informacje o bieżącej konfiguracji, a także modyfikowanie konfiguracji sieciowej.

W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik w edytorze tekstów i wyświetlić nazwę klasycznej sieci wirtualnej. Po utworzeniu sieci wirtualnej w portalu Azure, pełna nazwa, która używa usługi Azure nie jest widoczne w portalu. Na przykład sieci wirtualnej, który wydaje się być o nazwie "ClassicVNet1" w portalu Azure może mieć wiele nazwę dłużej w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: "ClassicVNet1 ClassicRG1 grupy". Nazwy sieci wirtualnej są wyświetlane jako **"VirtualNetworkSite name ="**. Podczas uruchamiania polecenia cmdlet programu PowerShell, użyj nazw w pliku konfiguracji sieci.

## <a name="delete"></a>Krok 3: Usuń bramę sieci wirtualnej

Po usunięciu bramy sieci wirtualnej zostały odłączone wszystkich połączeń do sieci wirtualnej za pośrednictwem bramy. Jeśli masz P2S klientów podłączonych do sieci wirtualnej zostanie rozłączone bez ostrzeżenia.

W tym przykładzie usuwa bramę sieci wirtualnej. Upewnij się użyć pełnej nazwy sieci wirtualnej z pliku konfiguracji sieci.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

W przypadku powodzenia zwracany zawiera:

```
Status : Successful
```

## <a name="modify"></a>Krok 4: Modyfikowanie pliku konfiguracji sieci

Podczas usuwania bramy sieci wirtualnej, polecenie cmdlet nie powoduje modyfikacji pliku konfiguracji sieci. Musisz zmodyfikować plik w celu usunięcia elementów, które są już używane. Poniższe sekcje pomóc zmodyfikować pobranego pliku konfiguracji sieci.

### <a name="lnsref"></a>Odwołania do lokacji sieci lokalnej

Aby usunąć informacje o lokacji odniesienia, wprowadzić zmiany w konfiguracji **ConnectionsToLocalNetwork/elementu LocalNetworkSiteRef**. Usuwanie wyzwalaczy odwołanie lokacji lokalnej platformy Azure, aby usunąć tunelu. W zależności od konfiguracji, który został utworzony, nie masz **elementu LocalNetworkSiteRef** na liście.

```
<Gateway>
   <ConnectionsToLocalNetwork>
     <LocalNetworkSiteRef name="D1BFC9CB_Site2">
       <Connection type="IPsec" />
     </LocalNetworkSiteRef>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

Przykład:

```
<Gateway>
   <ConnectionsToLocalNetwork>
   </ConnectionsToLocalNetwork>
 </Gateway>
```

###<a name="lns"></a>Lokacje sieci lokalnej

Usuń lokalnych witryn, które są już używane. W zależności od konfiguracji został utworzony, jest możliwe, że nie masz **LocalNetworkSite** na liście.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
  <LocalNetworkSite name="Site3">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>57.179.18.164</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

W tym przykładzie firma Microsoft usunęła tylko Site3.

```
<LocalNetworkSites>
  <LocalNetworkSite name="Site1">
    <AddressSpace>
      <AddressPrefix>192.168.0.0/16</AddressPrefix>
    </AddressSpace>
    <VPNGatewayAddress>5.4.3.2</VPNGatewayAddress>
  </LocalNetworkSite>
 </LocalNetworkSites>
```

### <a name="clientaddresss"></a>Pula adresów klienta

Jeśli zostały połączeń P2S sieci wirtualnej, konieczne będzie **VPNClientAddressPool**. Usuń pule adresów klienta, odpowiadające bramy sieci wirtualnej, która została usunięta.

```
<Gateway>
    <VPNClientAddressPool>
      <AddressPrefix>10.1.0.0/24</AddressPrefix>
    </VPNClientAddressPool>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

Przykład:

```
<Gateway>
  <ConnectionsToLocalNetwork />
 </Gateway>
```

### <a name="gwsub"></a>GatewaySubnet

Usuń **GatewaySubnet** odpowiadający sieci wirtualnej.

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
   <Subnet name="GatewaySubnet">
     <AddressPrefix>10.11.1.0/29</AddressPrefix>
   </Subnet>
 </Subnets>
```

Przykład:

```
<Subnets>
   <Subnet name="FrontEnd">
     <AddressPrefix>10.11.0.0/24</AddressPrefix>
   </Subnet>
 </Subnets>
```

## <a name="upload"></a>Krok 5: Przekaż plik konfiguracji sieci

Zapisz zmiany i przekazać plik konfiguracji sieci na platformie Azure. Upewnij się, że w przypadku zmiany ścieżki pliku jako wymaganych w danym środowisku.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

W przypadku powodzenia zwracany zawiera coś podobnego do tego przykładu:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded