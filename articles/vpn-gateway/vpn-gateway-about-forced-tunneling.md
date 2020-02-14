---
title: 'Azure VPN Gateway: Konfigurowanie wymuszonego tunelowania — połączenia między lokacjami: klasyczny'
description: Jak przekierowywanie lub "wymusić" cały ruch z Internetu do Twojej lokalizacji lokalnej.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: fe06257127ff352f68fb27d3507cee0229e31498
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201581"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu klasycznego modelu wdrażania

Wymuszane tunelowanie umożliwia przekierowywanie lub „wymuszanie” przekazywania całego ruchu internetowego z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN między lokacjami do celów przeprowadzania inspekcji. Jest to wymagane krytycznych dla większości organizacji IT zasad. Bez wymuszonego tunelowania ruchu skierowanego do Internetu z maszynami wirtualnymi na platformie Azure zawsze będzie przechodzić od infrastruktury sieci platformy Azure bezpośrednio się z Internetem, bez opcji, aby możliwe było sprawdzać lub kontrolować ruch. Nieautoryzowany dostęp do Internetu potencjalnie może prowadzić do ujawnienia informacji lub inne rodzaje naruszeń zabezpieczeń.

[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

W tym artykule przedstawiono konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania. Tunelowanie wymuszone można skonfigurować przy użyciu programu PowerShell, nie za pośrednictwem portalu. Jeśli chcesz skonfigurować Wymuszone tunelowanie dla Menedżer zasobówgo modelu wdrażania, wybierz pozycję Menedżer zasobów artykuł z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Wymagania i uwagi
Wymuszone tunelowanie w systemie Azure jest skonfigurowany za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika (UDR). Przekierowywanie ruchu do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure. W poniższej sekcji przedstawiono bieżące ograniczenie tabeli routingu i trasy dla usługi Azure Virtual Network:

* Każda podsieć sieci wirtualnej ma wbudowane i tabelę routingu systemu. Tabela routingu system ma trzy następujące grupy trasy:

  * **Lokalne trasy sieci wirtualnej:** Bezpośrednio do docelowych maszyn wirtualnych w tej samej sieci wirtualnej.
  * **Trasy lokalne:** Z bramą sieci VPN platformy Azure.
  * **Trasa domyślna:** Bezpośrednio z Internetem. Pakiety przeznaczone do prywatnych adresów IP nieuwzględnionych przez poprzednie dwie trasy zostaną usunięte.
* Wraz z wydaniem trasy zdefiniowane przez użytkownika można utworzyć tabeli routingu, aby dodać trasę domyślną i skojarz tabeli routingu do swoje podsieci sieci wirtualnej można włączyć tunelowania wymuszonego tych podsieci.
* Należy ustawić "Domyślna witryna" wśród wielu lokacji lokalnych podłączone do sieci wirtualnej.
* Wymuszone tunelowanie muszą być skojarzone z siecią wirtualną, która ma sieci VPN bramy o dynamicznym routingu (nie brama statyczna).
* Usługa ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą tego mechanizmu, ale zamiast tego można włączyć, konfigurując anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute. Aby uzyskać więcej informacji, zobacz [dokumentację ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) .

## <a name="configuration-overview"></a>Przegląd konfiguracji
W poniższym przykładzie frontonu podsieci nie jest wymuszone tunelowanie. Obciążenia należących do podsieci frontonu może nadal akceptować i odpowiadać na żądania klientów i z Internetu. Jest wymuszone w warstwie pośredniej i zaplecze oparte na podsieci tunelowania. Wszystkie połączenia wychodzące z tymi dwoma podsieciami z Internetem zostanie wymuszone lub przekierowany z powrotem do lokacji lokalnej przy użyciu jednej z tuneli sieci VPN S2S.

Dzięki temu można ograniczyć i inspekcja dostępu do Internetu na maszynach wirtualnych lub usług na platformie Azure w chmurze przy jednoczesnym dalszym Włącz architektury wielowarstwowej usługi wymagane. Możesz również zastosować wymuszonego tunelowania całego sieciami wirtualnymi, jeśli żadne obciążenia dostępnego z Internetu w swoich sieciach wirtualnych.

![Wymuszone tunelowanie](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami:

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Skonfigurowanej sieci wirtualnej. 
* [!INCLUDE [vpn-gateway-classic-powershell](../../includes/vpn-gateway-powershell-classic-locally.md)]

### <a name="to-sign-in"></a>Aby się zalogować

1. Otwórz konsolę programu PowerShell z podwyższonym poziomem uprawnień. Aby przełączyć się do zarządzania usługami, użyj tego polecenia:

   ```powershell
   azure config mode asm
   ```
2. Połącz się ze swoim kontem. Użyj poniższego przykładu w celu łatwiejszego nawiązania połączenia:

   ```powershell
   Add-AzureAccount
   ```

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania
Poniższa procedura pomoże określić wymuszonego tunelowania dla sieci wirtualnej. Kroki konfiguracji odnoszą się do pliku konfiguracji sieci w sieci wirtualnej.

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

W tym przykładzie sieć wirtualna "MultiTier wirtualna-sieć wirtualna" ma trzy podsieci: podsieci "Frontend", "Midtier" i "Zaplecze", przy użyciu połączeń cztery obejmujących: "DefaultSiteHQ" i trzy gałęzie. 

Kroki zostaną DefaultSiteHQ jako domyślnego połączenia witryny dla wymuszonego tunelowania i skonfigurować Midtier i podsieci wewnętrznej bazy danych do użycia wymuszonego tunelowania.

1. Tworzenie tabeli routingu. Użyj następującego polecenia cmdlet do tworzenia tabeli tras.

   ```powershell
   New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
   ```

2. Dodaj domyślną trasę do tabeli routingu. 

   Poniższy przykład dodaje domyślną trasę do tabeli routingu, utworzony w kroku 1. Należy zauważyć, że są obsługiwane tylko trasy jest prefiks lokalizacji docelowej "0.0.0.0/0" do następnego skoku "Bramy VPNGateway".

   ```powershell
   Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
   ```

3. Kojarzenie tabeli routingu do podsieci. 

   Po utworzeniu tabeli routingu i dodaje trasę, skorzystaj z następującego przykładu, aby dodać lub kojarzenie tabeli tras do podsieci sieci wirtualnej. W przykładzie dodano tabeli tras "MyRouteTable" podsieciami Midtier i wewnętrznej bazy danych z MultiTier sieciami wirtualnymi.

   ```powershell
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
   Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
   ```

4. Przypisz domyślną witrynę dla wymuszonego tunelowania. 

   W poprzednim kroku przykładowe skrypty polecenia cmdlet utworzona tabela routingu i skojarzoną tabelę tras z dwoma podsieciami sieci wirtualnej. Krok pozostały jest wybierz lokację lokalną między połączeniami obejmującymi wiele lokacji sieci wirtualnej jako domyślnej witryny lub tunelu.

   ```powershell
   $DefaultSite = @("DefaultSiteHQ")
   Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
   ```

## <a name="additional-powershell-cmdlets"></a>Dodatkowe polecenia cmdlet programu PowerShell
### <a name="to-delete-a-route-table"></a>Można usunąć tabeli tras

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Aby wyświetlić listę tabelę tras

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Aby usunąć trasę z tabeli tras

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Aby usunąć trasę z podsieci

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Aby wyświetlić listę tabeli tras skojarzonej z podsiecią

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Aby usunąć domyślną witrynę z bramy sieci VPN sieci wirtualnej

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```
