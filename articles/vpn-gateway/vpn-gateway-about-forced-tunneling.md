---
title: 'Brama sieci VPN platformy Azure: konfigurowanie tunelowania wymuszonego — połączenia lokacja lokacja: klasyczna'
description: Jak przekierować lub "wymusić" cały ruch związany z Internetem z powrotem do lokalizacji lokalnej.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201581"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu klasycznego modelu wdrażania

Wymuszane tunelowanie umożliwia przekierowywanie lub „wymuszanie” przekazywania całego ruchu internetowego z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN między lokacjami do celów przeprowadzania inspekcji. Jest to krytyczne wymagania dotyczące zabezpieczeń dla większości korporacyjnych zasad IT. Bez wymuszonego tunelowania ruch związany z Internetem z maszyn wirtualnych na platformie Azure zawsze będzie przechodził z infrastruktury sieci platformy Azure bezpośrednio do Internetu, bez opcji umożliwiającej inspekcję lub inspekcję ruchu. Nieautoryzowany dostęp do Internetu może potencjalnie prowadzić do ujawnienia informacji lub innych rodzajów naruszeń bezpieczeństwa.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

W tym artykule można przejść przez konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania. Wymuszone tunelowanie można skonfigurować przy użyciu programu PowerShell, a nie za pośrednictwem portalu. Jeśli chcesz skonfigurować wymuszone tunelowanie dla modelu wdrażania Menedżera zasobów, wybierz artykuł Menedżera zasobów z następującej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Wymagania i uwagi
Wymuszone tunelowanie na platformie Azure jest konfigurowane za pomocą tras zdefiniowanych przez użytkownika w sieci wirtualnej (UDR). Przekierowanie ruchu do lokacji lokalnej jest wyrażone jako domyślna trasa do bramy sieci VPN platformy Azure. W poniższej sekcji wymieniono bieżące ograniczenie tabeli routingu i tras dla sieci wirtualnej platformy Azure:

* Każda podsieć sieci wirtualnej ma wbudowaną tabelę routingu systemowego. Tabela routingu systemowego ma następujące trzy grupy tras:

  * **Lokalne trasy sieci wirtualnej:** Bezpośrednio do docelowych maszyn wirtualnych w tej samej sieci wirtualnej.
  * **Trasy lokalne:** Do bramy sieci VPN platformy Azure.
  * **Trasa domyślna:** Bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nieobjętych poprzednimi dwiema trasami zostaną usunięte.
* Po wydaniu tras zdefiniowanych przez użytkownika można utworzyć tabelę routingu, aby dodać trasę domyślną, a następnie skojarzyć tabelę routingu z podsiecią sieci wirtualnej, aby włączyć wymuszone tunelowanie w tych podsieciach.
* Należy ustawić "lokację domyślną" wśród międzylokacyjnych lokacji lokalnych połączonych z siecią wirtualną.
* Wymuszone tunelowanie musi być skojarzone z siecią wirtualną, która ma dynamiczną routingową bramę sieci VPN (nie bramę statyczną).
* Wymuszone tunelowanie usługi ExpressRoute nie jest skonfigurowane za pomocą tego mechanizmu, ale zamiast tego jest włączone przez reklamowanie domyślnej trasy za pośrednictwem sesji komunikacji równorzędnej Usługi ExpressRoute BGP. Więcej informacji można znaleźć w [dokumentacji usługi ExpressRoute.](https://azure.microsoft.com/documentation/services/expressroute/)

## <a name="configuration-overview"></a>Przegląd konfiguracji
W poniższym przykładzie podsieć Fronton nie jest wymuszana tunelowanie. Obciążenia w podsieci Frontend można nadal akceptować i odpowiadać na żądania klientów z Internetu bezpośrednio. Podsieci mid-tier i backend są wymuszane tunelowanie. Wszelkie połączenia wychodzące z tych dwóch podsieci do Internetu zostaną wymuszone lub przekierowane z powrotem do lokacji lokalnej za pośrednictwem jednego z tuneli sieci VPN S2S.

Dzięki temu można ograniczyć i sprawdzić dostęp do Internetu z maszyn wirtualnych lub usług w chmurze na platformie Azure, przy jednoczesnym kontynuowaniu włączania architektury usług wielowarstwowych wymagane. Można również zastosować wymuszone tunelowanie do całych sieci wirtualnych, jeśli w sieciach wirtualnych nie ma obciążeń związanych z Internetem.

![Wymuszone tunelowanie](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Skonfigurowana sieć wirtualna. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Aby się zalogować

1. Otwórz konsolę programu PowerShell z podwyższonymi prawami. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

   ```powershell
   azure config mode asm
   ```
2. Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania
Poniższa procedura pomoże określić wymuszone tunelowanie dla sieci wirtualnej. Kroki konfiguracji odpowiadają plikowi konfiguracji sieci wirtualnej.

```xml
<VirtualNetworkSite name="MultiTier-VNet" Location="North Europe">
     <AddressSpace>
      <AddressPrefix>10.1.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="Frontend">
            <AddressPrefix>10.1.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Midtier">
            <AddressPrefix>10.1.1.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="Backend">
            <AddressPrefix>10.1.2.0/23</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.1.200.0/28</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="DefaultSiteHQ">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch1">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch2">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
            <LocalNetworkSiteRef name="Branch3">
              <Connection type="IPsec" />
            </LocalNetworkSiteRef>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSite>
```

W tym przykładzie sieć wirtualna "MultiTier-VNet" ma trzy podsieci: "Frontend", "Midtier" i "Backend" podsieci, z czterema połączeniami międzylokacjami: "DefaultSiteHQ" i trzema gałęziami. 

Kroki ustawią "DefaultSiteHQ" jako domyślne połączenie lokacji dla wymuszonego tunelowania i skonfigurują podsieci Midtier i Backend do używania wymuszonego tunelowania.

1. Tworzenie tabeli marszruty. Aby utworzyć tabelę tras, użyj następującego polecenia cmdlet.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Dodaj domyślną trasę do tabeli routingu. 

   Poniższy przykład dodaje domyślną trasę do tabeli routingu utworzonej w kroku 1. Należy zauważyć, że jedyną obsługiwaną trasą jest prefiks docelowy "0.0.0.0/0" do "VPNGateway" NextHop.

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Skojarz tabelę routingu z podsieciami. 

   Po utworzeniu tabeli routingu i dodaniu trasy użyj poniższego przykładu, aby dodać lub skojarzyć tabelę marszruty z podsiecią sieci wirtualnej. W przykładzie dodaje tabeli trasy "MyRouteTable" do midtier i wewnętrznej bazy danych podsieci sieci wirtualnej MultiTier-VNet.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Przypisz domyślną lokację do wymuszonego tunelowania. 

   W poprzednim kroku przykładowe skrypty poleceń cmdlet utworzyły tabelę routingu i skojarzyły tabelę marszruty z dwiema podsieciami sieci wirtualnej. Pozostałym krokiem jest wybranie lokacji lokalnej spośród połączeń wielolokalowych sieci wirtualnej jako domyślnej lokacji lub tunelu.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Dodatkowe polecenia cmdlet programu PowerShell
### <a name="to-delete-a-route-table"></a>Aby usunąć tabelę marszruty

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Aby wyświetlić tabelę tras

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Aby usunąć trasę z tabeli marszruty

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Aby usunąć trasę z podsieci

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Aby wyświetlić listę tabeli marszruty skojarzonej z podsiecią

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Aby usunąć witrynę domyślną z bramy sieci VPN sieci wirtualnej

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
