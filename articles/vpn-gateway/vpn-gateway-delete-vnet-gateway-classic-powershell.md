---
title: 'Usuwanie bramy sieci wirtualnej: Program PowerShell: Klasyczny Portal Azure | Dokumentacja firmy Microsoft'
description: Usuwanie bramy sieci wirtualnej w klasycznym modelu wdrażania przy użyciu programu PowerShell.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: cherylmc
ms.openlocfilehash: ca014e4f5fbc4a5695dbc5fedc85826c71a2a906
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60863984"
---
# <a name="delete-a-virtual-network-gateway-using-powershell-classic"></a>Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (wersja klasyczna)

> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](vpn-gateway-delete-vnet-gateway-portal.md)
> * [Resource Manager — program PowerShell](vpn-gateway-delete-vnet-gateway-powershell.md)
> * [Klasyczny — PowerShell](vpn-gateway-delete-vnet-gateway-classic-powershell.md)
>

Ten artykuł ułatwia usuwanie bramy sieci VPN w klasycznym modelu wdrażania przy użyciu programu PowerShell. Po usunięciu bramy sieci wirtualnej należy zmodyfikować plik konfiguracji sieci, aby usunąć elementy, które nie są już używane.

## <a name="connect"></a>Krok 1. Nawiązywanie połączenia z usługą Azure

### <a name="1-install-the-latest-powershell-cmdlets"></a>1. Zainstaluj najnowsze polecenia cmdlet programu PowerShell.

Pobierz i zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Service Management (SM). Aby uzyskać więcej informacji, zobacz [Instalowanie i konfigurowanie programu Azure PowerShell](/powershell/azure/overview).

### <a name="2-connect-to-your-azure-account"></a>2. Łączenie z kontem platformy Azure. 

Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień i połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

```powershell
Add-AzureAccount
```

## <a name="export"></a>Krok 2. Eksportowanie i wyświetlić plik konfiguracji sieci

Utwórz katalog na komputerze, a następnie wyeksportuj plik konfiguracji sieci do tego katalogu. Możesz użyć tego pliku do obu widoku informacje o bieżącej konfiguracji, a także modyfikowanie konfiguracji sieciowej.

W tym przykładzie plik konfiguracji sieci zostanie wyeksportowany do katalogu C:\AzureNet.

```powershell
Get-AzureVNetConfig -ExportToFile C:\AzureNet\NetworkConfig.xml
```

Otwórz plik w edytorze tekstu i Wyświetl nazwę klasycznej sieci wirtualnej. Po utworzeniu sieci wirtualnej w witrynie Azure portal, pełna nazwa, która używa usługi Azure nie jest widoczna w portalu. Na przykład sieci wirtualnej, który wydaje się być o nazwie "ClassicVNet1" w witrynie Azure portal może mieć wiele dłuższej nazwy w pliku konfiguracji sieci. Nazwa może wyglądać mniej więcej tak: "Group ClassicRG1 ClassicVNet1". Nazwy sieci wirtualnej są wyświetlane jako **"VirtualNetworkSite name ="**. Użyj nazw w pliku konfiguracji sieci, podczas uruchamiania poleceń cmdlet programu PowerShell.

## <a name="delete"></a>Krok 3. Usuwanie bramy sieci wirtualnej

Jeśli usuniesz bramy sieci wirtualnej, wszystkie połączenia z siecią wirtualną za pośrednictwem bramy są odłączone. W przypadku połączonych z siecią wirtualną klientów P2S zostaną rozłączone bez ostrzeżenia.

W tym przykładzie usuwa bramę sieci wirtualnej. Pamiętaj użyć pełnej nazwy sieci wirtualnej z pliku konfiguracji sieci.

```powershell
Remove-AzureVNetGateway -VNetName "Group ClassicRG1 ClassicVNet1"
```

Jeśli to się powiedzie, pokazuje zwracany:

```
Status : Successful
```

## <a name="modify"></a>Krok 4. Zmodyfikuj plik konfiguracji sieci

Po usunięciu bramy sieci wirtualnej, polecenie cmdlet nie powoduje modyfikacji pliku konfiguracji sieci. Należy zmodyfikować plik, aby usunąć elementy, które są już używane. Poniższe sekcje ułatwiają modyfikowanie pliku konfiguracji sieci, który został pobrany.

### <a name="lnsref"></a>Odwołania do lokacji sieci lokalnej

Aby usunąć informacje o lokacji odniesienia, wprowadzić zmiany w konfiguracji **ConnectionsToLocalNetwork/elementu LocalNetworkSiteRef**. Usuwanie wyzwalaczy odwołanie do lokacji lokalnej platformy Azure, aby usunąć tunel. W zależności od konfiguracji, który został utworzony, nie masz **elementu LocalNetworkSiteRef** na liście.

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

### <a name="lns"></a>Lokalne Lokacje sieciowe

Usuń lokalne witryny, nie są już używane. W zależności od konfiguracji został utworzony, jest możliwe, że nie masz **LocalNetworkSite** na liście.

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

Jeśli trzeba było połączenia P2S sieci wirtualnej, konieczne będzie **VPNClientAddressPool**. Usuń pule adresów klienta, które odnoszą się do bramy sieci wirtualnej, który został usunięty.

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

Usuń **GatewaySubnet** odnosi się do sieci wirtualnej.

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

## <a name="upload"></a>Krok 5. Przekaż plik konfiguracji sieci

Zapisz zmiany i przekazać plik konfiguracji sieci na platformę Azure. Upewnij się, że zmiany ścieżki pliku jako wymaganych w danym środowisku.

```powershell
Set-AzureVNetConfig -ConfigurationPath C:\AzureNet\NetworkConfig.xml
```

Jeśli to się powiedzie, zwracany pokazuje, podobnie jak w tym przykładzie:

```
OperationDescription        OperationId                      OperationStatus                                                
--------------------        -----------                      ---------------                                           
Set-AzureVNetConfig         e0ee6e66-9167-cfa7-a746-7casb9   Succeeded
```
