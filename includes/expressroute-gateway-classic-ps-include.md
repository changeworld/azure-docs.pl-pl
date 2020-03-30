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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67182965"
---
> [!NOTE]
> Te przykłady nie mają zastosowania do konfiguracji współistnienia S2S/ExpressRoute.
> Aby uzyskać więcej informacji na temat pracy z bramami w konfiguracji współistniejącej, zobacz [Konfigurowanie współistniejących połączeń.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Dodawanie bramy

Po dodaniu bramy do sieci wirtualnej przy użyciu klasycznego modelu zasobów należy zmodyfikować plik konfiguracji sieci bezpośrednio przed utworzeniem bramy. Wartości w poniższych przykładach muszą znajdować się w pliku, aby utworzyć bramę. Jeśli sieć wirtualna wcześniej miała skojarzoną bramę, niektóre z tych wartości będą już obecne. Zmodyfikuj plik, aby odzwierciedlić poniższe wartości.

### <a name="download-the-network-configuration-file"></a>Pobierz plik konfiguracji sieci

1. Pobierz plik konfiguracji sieci, wykonując czynności opisane w artykule [w pliku konfiguracji sieci.](../articles/virtual-network/virtual-networks-using-network-configuration-file.md) Otwórz plik za pomocą edytora tekstu.
2. Dodaj do pliku lokalną witrynę sieciową. Można użyć dowolnego prawidłowego prefiksu adresu. Można dodać dowolny prawidłowy adres IP bramy sieci VPN. Wartości adresów w tej sekcji nie są używane dla operacji usługi ExpressRoute, ale są wymagane do sprawdzania poprawności pliku. W przykładzie "branch1" jest nazwą witryny. Możesz użyć innej nazwy, ale pamiętaj, aby użyć tej samej wartości w sekcji Brama pliku.

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
3. Przejdź do witryn VirtualNetworkSites i zmodyfikuj pola.

   * Sprawdź, czy podsieć bramy istnieje dla sieci wirtualnej. Jeśli tak nie jest, możesz dodać go w tej chwili. Nazwa musi być "GatewaySubnet".
   * Sprawdź, czy istnieje sekcja Brama pliku. Jeśli tak nie jest, dodaj go. Jest to wymagane do skojarzenia sieci wirtualnej z lokacją sieci lokalnej (która reprezentuje sieć, z którą nawiązujesz połączenie).
   * Sprawdź, czy typ połączenia = Dedykowany. Jest to wymagane w przypadku połączeń usługi ExpressRoute.

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

Użyj poniższego polecenia, aby utworzyć bramę. Zastąp wszelkie wartości dla własnych.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona

Użyj poniższego polecenia, aby sprawdzić, czy brama została utworzona. To polecenie pobiera również identyfikator bramy, który jest potrzebny do innych operacji.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy

Istnieje wiele jednostek [SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Za pomocą następującego polecenia można zmienić jednostkę SKU bramy w dowolnym momencie.

> [!IMPORTANT]
> To polecenie nie działa dla bramy UltraPerformance. Aby zmienić bramę na bramę UltraPerformance, najpierw usuń istniejącą bramę usługi ExpressRoute, a następnie utwórz nową bramę UltraPerformance. Aby obniżyć poziom bramy z bramy UltraPerformance, należy najpierw usunąć bramę UltraPerformance, a następnie utworzyć nową bramę.
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