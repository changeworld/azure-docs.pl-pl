---
title: 'Usuwanie bramy sieci wirtualnej: klasyczna platforma Azure'
description: Usuń bramę sieci wirtualnej przy użyciu programu PowerShell w klasycznym modelu wdrażania.
titleSuffix: Azure VPN Gateway
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: cherylmc
ms.openlocfilehash: e7283f5e28edc6f7beaad3a2743aa155f6ea6e14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77198653"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (klasyczna)

> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager — program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasyczny - PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Ten artykuł ułatwia usunięcie bramy sieci VPN w klasycznym modelu wdrażania przy użyciu programu PowerShell. Po usunięciu bramy sieci wirtualnej zmodyfikuj plik konfiguracji sieci, aby usunąć elementy, których już nie używasz.

## <a name="step-1-connect-to-azure"></a><a name="connect"></a>Krok 1: Połącz się z platformą Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Zainstaluj najnowsze polecenia cmdlet programu PowerShell.

[!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="2-connect-to-your-azure-account"></a>2. Połącz się ze swoim kontem platformy Azure.

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

1. Otwórz konsolę programu PowerShell z podwyższonymi prawami. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

   ```powershell
   azure config mode asm
   ```
2. Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Add-AzureAccount
   ```

## <a name="step-2-export-and-view-the-network-configuration-file"></a><a name="export"></a>Krok 2: Eksportowanie i wyświetlanie pliku konfiguracji sieci

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. Ten plik służy zarówno do wyświetlania bieżących informacji o konfiguracji, jak i do modyfikowania konfiguracji sieci.

W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik za pomocą edytora tekstu i wyświetl nazwę klasycznej sieci wirtualnej. Podczas tworzenia sieci wirtualnej w witrynie Azure portal, pełna nazwa używana przez platformę Azure nie jest widoczna w portalu. Na przykład sieć wirtualna, która wydaje się być o nazwie "ClassicVNet1" w witrynie Azure portal, może mieć znacznie dłuższą nazwę w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: "Group ClassicRG1 ClassicVNet1". Nazwy sieci wirtualnych są wyświetlane jako **"Nazwa VirtualNetworkSite ="**. Nazwy w pliku konfiguracji sieciowej należy używać podczas uruchamiania poleceń cmdlet programu PowerShell.

## <a name="step-3-delete-the-virtual-network-gateway"></a><a name="delete"></a>Krok 3: Usuwanie bramy sieci wirtualnej

Po usunięciu bramy sieci wirtualnej wszystkie połączenia z siecią wirtualną za pośrednictwem bramy są rozłączane. Jeśli masz klientów P2S podłączonych do sieci wirtualnej, zostaną one rozłączone bez ostrzeżenia.

W tym przykładzie usuwa bramę sieci wirtualnej. Upewnij się, że z pliku konfiguracji sieci użyto pełnej nazwy sieci wirtualnej.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Jeśli się powiedzie, zwrot pokazuje:

```
Status : Successful
```

## <a name="step-4-modify-the-network-configuration-file"></a><a name="modify"></a>Krok 4: Modyfikowanie pliku konfiguracji sieci

Po usunięciu bramy sieci wirtualnej polecenie cmdlet nie modyfikuje pliku konfiguracji sieci. Należy zmodyfikować plik, aby usunąć elementy, które nie są już używane. Poniższe sekcje ułatwią modyfikowanie pobranego pliku konfiguracji sieci.

### <a name="local-network-site-references"></a><a name="lnsref"></a>Odwołania do lokacji sieci lokalnej

Aby usunąć informacje o lokalizacji, należy wprowadzić zmiany konfiguracji **w connectionsToLocalNetwork/LocalNetworkSiteRef**. Usunięcie odwołania do lokacji lokalnej powoduje wyzwolenie platformy Azure w celu usunięcia tunelu. W zależności od utworzonej konfiguracji na liście może nie być na liście **LocalNetworkSiteRef.**

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

### <a name="local-network-sites"></a><a name="lns"></a>Witryny sieci lokalnej

Usuń wszystkie witryny lokalne, z których już nie korzystasz. W zależności od utworzonej konfiguracji jest możliwe, że nie masz **LocalNetworkSite** na liście.

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

W tym przykładzie usunęliśmy tylko Site3.

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

### <a name="client-addresspool"></a><a name="clientaddresss"></a>Szpuli adresów klienta

Jeśli miałeś połączenie P2S z siecią wirtualną, będziesz miał **VPNClientAddressPool**. Usuń pule adresów klienta, które odpowiadają usuniętej bramie sieci wirtualnej.

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

### <a name="gatewaysubnet"></a><a name="gwsub"></a>GatewaySubnet

Usuń **GatewaySubnet,** który odpowiada sieci wirtualnej.

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

## <a name="step-5-upload-the-network-configuration-file"></a><a name="upload"></a>Krok 5: Prześlij plik konfiguracji sieci

Zapisz zmiany i przekaż plik konfiguracji sieci na platformę Azure. Upewnij się, że ścieżka pliku jest zmieniana w razie potrzeby w twoim środowisku.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Jeśli się powiedzie, return pokazuje coś podobnego do tego przykładu:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
