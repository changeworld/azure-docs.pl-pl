---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 70ac106995324c758bde942d12191a01e3457e6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60365164"
---
> [!NOTE]
> Te przykłady nie dotyczą S2S/ExpressRoute współistnieją konfiguracje.
> Aby uzyskać więcej informacji na temat pracy z bramami w konfiguracji coexist zobacz [skonfigurować współistniejące połączenia.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Dodawanie bramy

Po dodaniu bramy do sieci wirtualnej przy użyciu modelu klasycznego zasobu, należy zmodyfikować plik konfiguracji sieci bezpośrednio przed utworzeniem bramy. Wartości w poniższych przykładach musi być obecny w pliku, aby utworzyć bramę. Jeśli Twoja sieć wirtualna miała wcześniej powiązany bramy, niektóre z tych wartości będą już obecne. Zmodyfikuj plik w celu uwzględnienia poniższych wartości.

### <a name="download-the-network-configuration-file"></a>Pobierz plik konfiguracji sieci

1. Pobierz plik konfiguracji sieci przy użyciu kroków w [pliku konfiguracji sieci](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) artykułu. Otwórz plik za pomocą edytora tekstów.
2. Lokacja sieci lokalnej należy dodać do pliku. Możesz użyć dowolnego prefiksu prawidłowy adres. Możesz dodać dowolny prawidłowy adres IP dla bramy sieci VPN. Adresu IP w tej sekcji nie są używane do operacji usługi ExpressRoute, ale są wymagane do weryfikacji pliku. W tym przykładzie "branch1" jest nazwą witryny. Można użyć innej nazwy, ale pamiętaj używać tej samej wartości w sekcji bramy pliku.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Przejdź do VirtualNetworkSites i zmodyfikuj pola.

   * Sprawdź, czy podsieć bramy, istnieje dla sieci wirtualnej. Jeśli nie, możesz dodać jeden w tej chwili. Nazwa musi mieć "nazwę GatewaySubnet".
   * Sprawdź, czy istnieje brama części pliku. Jeśli nie, należy go dodać. Jest to wymagane, aby skojarzyć sieci wirtualnej z lokacją sieci lokalnej (który reprezentuje sieć, do której się łączysz).
   * Sprawdź, czy połączenie typu = Dedicated. Jest to wymagane dla połączenia ExpressRoute.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Zapisz plik i przekaż go na platformę Azure.

### <a name="create-the-gateway"></a>Tworzenie bramy

Użyj poniższego polecenia, aby utworzyć bramę. Zastąp wszystkie wartości swoimi własnymi.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona

Użyj poniższego polecenia, aby sprawdzić, czy brama została utworzona. To polecenie również pobiera identyfikator bramy, które są wymagane dla innych operacji.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy

Istnieje szereg [jednostki SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Aby zmienić jednostkę SKU bramy w dowolnym momencie, można użyć następującego polecenia.

> [!IMPORTANT]
> To polecenie nie działa dla bramy UltraPerformance. Aby zmienić brama do bramy UltraPerformance, najpierw usuń istniejącą bramą ExpressRoute, a następnie utwórz nowe bramy UltraPerformance. W przypadku obniżania bramy z bramy UltraPerformance, najpierw usuń bramy UltraPerformance, a następnie utwórz nową bramę.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Usuwanie bramy

Użyj poniższego polecenia, aby usunąć bramę

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```