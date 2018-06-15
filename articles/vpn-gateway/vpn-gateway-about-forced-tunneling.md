---
title: 'Skonfigurować wymuszanie tunelowania dla połączenia usługi Azure Site to Site: klasycznym | Dokumentacja firmy Microsoft'
description: Jak przekierować lub "force" cały ruch do Internetu powiązane z powrotem do lokalizacji lokalnej.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-service-management
ms.assetid: 5c0177f1-540c-4474-9b80-f541fa44240b
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/01/2017
ms.author: cherylmc
ms.openlocfilehash: 79bf6892c823da282c3e763921e830f986419854
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23883523"
---
# <a name="configure-forced-tunneling-using-the-classic-deployment-model"></a>Konfigurowanie wymuszonego tunelowania przy użyciu klasycznego modelu wdrażania

Wymuszanie tunelowania umożliwia przekierowanie lub "force" cały ruch do Internetu z powrotem do lokalizacji lokalnej za pośrednictwem tunelu VPN typu lokacja-lokacja dla inspekcji i inspekcji. Jest to wymagane krytycznych dla większości organizacji IT zasad. Bez tunelowania wymuszonego, ruch do Internetu z maszyn wirtualnych na platformie Azure będzie zawsze przechodzenie od infrastruktury sieci platformy Azure bezpośrednio się z Internetem, bez opcji pozwala sprawdzać lub kontrolować ruch. Nieautoryzowany dostęp do Internetu potencjalnie może spowodować ujawnienie informacji lub inne rodzaje naruszeń zabezpieczeń.

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

W tym artykule przedstawiono konfigurowanie wymuszonego tunelowania dla sieci wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania. Tunelowanie wymuszone można skonfigurować przy użyciu programu PowerShell, nie za pośrednictwem portalu. Jeśli chcesz skonfigurować wymuszanie tunelowania dla modelu wdrażania usługi Resource Manager, wybierz klasycznego artykułu z poniższej listy rozwijanej:

> [!div class="op_single_selector"]
> * [PowerShell — model klasyczny](vpn-gateway-about-forced-tunneling.md)
> * [Program PowerShell — model usługi Resource Manager](vpn-gateway-forced-tunneling-rm.md)
> 
> 

## <a name="requirements-and-considerations"></a>Wymagania i uwagi
Wymuszanie tunelowania na platformie Azure jest skonfigurowana za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika (przez). Ruch jest przekierowywany do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure. W poniższej sekcji przedstawiono bieżące ograniczenia tabeli routingu i tras dla sieci wirtualnej platformy Azure:

* Każda podsieć sieci wirtualnej ma wbudowane, tabelę routingu systemu. W tabeli routingu system ma następujące trzy grupy tras:

  * **Tras lokalnych sieci wirtualnej:** bezpośrednio do miejsca docelowego maszyny wirtualne w tej samej sieci wirtualnej.
  * **Trasy lokalnymi:** bramy do sieci VPN platformy Azure.
  * **Trasa domyślna:** bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nie jest objęty przez poprzednie dwie trasy zostaną usunięte.
* Wraz z wydaniem trasy zdefiniowane przez użytkownika można utworzyć tabeli routingu, aby dodać trasy domyślnej i późniejszym skojarzeniu tabeli routingu do adresy podsieci Twojej sieci wirtualnej mogą być używane w taki sposób, aby włączyć tunelowanie wymuszone korzysta z tych podsieci.
* Należy określić "domyślnej witryny" między lokacjami lokalnego między lokalizacjami podłączony do sieci wirtualnej.
* Wymuszanie tunelowania musi być skojarzony z sieć wirtualną, która ma dynamiczne routingu bramy sieci VPN (nie bramy statyczne).
* ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą ten mechanizm, ale zamiast tego jest włączana przez anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute. Zobacz [dokumentacja usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Aby uzyskać więcej informacji.

## <a name="configuration-overview"></a>Przegląd konfiguracji
W poniższym przykładzie tunelowane frontonu nie wymusza podsieci. Obciążeń w podsieci frontonu mogą nadal akceptować i odpowiadać na żądania klientów i z Internetu. Podsieci warstwie pośredniej i wewnętrznej bazy danych zostało wymuszone tunelowane. Wszystkie połączenia wychodzące z tymi dwoma podsieciami z Internetem zostanie wymuszone lub przekierowany z powrotem do lokacji lokalnej za pośrednictwem jeden tunel S2S VPN.

Dzięki temu można ograniczyć i inspekcję dostępu do Internetu na maszynach wirtualnych lub w chmurze usługi na platformie Azure, pozostawiając włączyć architektury usługa wielowarstwowa wymagane. Możesz również zastosować wymuszanie tunelowania do całej sieci wirtualnych, jeśli w sieci wirtualne nie żadne obciążenia skierowane do Internetu.

![Wymuszone tunelowanie](./media/vpn-gateway-about-forced-tunneling/forced-tunnel.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed rozpoczęciem konfiguracji sprawdź, czy dysponujesz następującymi elementami.

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial/).
* Skonfigurowanej sieci wirtualnej. 
* Najnowsza wersja poleceń cmdlet programu Azure PowerShell. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/overview) (Instalowanie i konfigurowanie programu Azure PowerShell).

## <a name="configure-forced-tunneling"></a>Konfigurowanie wymuszonego tunelowania
Poniższa procedura umożliwia określenie, wymuszanie tunelowania do sieci wirtualnej. Odpowiada kroków konfiguracji do pliku konfiguracji sieci wirtualnej.

```
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

W tym przykładzie sieci wirtualnej "Sieci wirtualnej MultiTier" zawiera trzy podsieci: podsieci "Frontend", "Midtier" i "Zaplecze", w przypadku połączeń między różnymi lokalizacjami cztery: "DefaultSiteHQ" i trzy gałęzie. 

Kroki będą DefaultSiteHQ jako domyślnego połączenia lokacji dla wymuszone tunelowanie i skonfigurować Midtier i wymusić podsieci wewnętrznej bazy danych do użycia tunelowania.

1. Tworzy tabelę routingu. Użyj następującego polecenia cmdlet do utworzenia tabeli tras.

  ```powershell
  New-AzureRouteTable –Name "MyRouteTable" –Label "Routing Table for Forced Tunneling" –Location "North Europe"
  ```
2. Dodaj domyślną trasę do tabeli routingu. 

  Poniższy przykład dodaje domyślną trasę do tabeli routingu utworzone w kroku 1. Należy pamiętać, że obsługiwane tylko trasy jest prefiks docelowy "0.0.0.0/0" do następnego skoku "Właściwość VPNGateway".

  ```powershell
  Get-AzureRouteTable -Name "MyRouteTable" | Set-AzureRoute –RouteTable "MyRouteTable" –RouteName "DefaultRoute" –AddressPrefix "0.0.0.0/0" –NextHopType VPNGateway
  ```
3. Kojarzenie tabeli routingu do podsieci. 

  Po utworzeniu tabeli routingu i dodać trasy, skorzystaj z następującego przykładu, aby dodać lub skojarzyć tabeli tras z podsiecią sieci wirtualnej. W przykładzie dodano tabeli tras "MyRouteTable" z podsieciami z MultiTier wirtualnymi Midtier i wewnętrznej bazy danych.

  ```powershell
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Midtier" -RouteTableName "MyRouteTable"
  Set-AzureSubnetRouteTable -VirtualNetworkName "MultiTier-VNet" -SubnetName "Backend" -RouteTableName "MyRouteTable"
  ```
4. Przypisz domyślnej witryny dla wymuszonego tunelowania. 

  W poprzednim kroku przykładowe skrypty polecenia cmdlet utworzono tabelę routingu i skojarzona tabela tras do dwóch podsieci sieci wirtualnej. Pozostały krok polega na wybraniu lokalnej lokacji między połączeń obejmujący wiele lokacji sieci wirtualnej jako domyślnej witryny lub tunelu.

  ```powershell
  $DefaultSite = @("DefaultSiteHQ")
  Set-AzureVNetGatewayDefaultSite –VNetName "MultiTier-VNet" –DefaultSite "DefaultSiteHQ"
  ```

## <a name="additional-powershell-cmdlets"></a>Dodatkowe polecenia cmdlet programu PowerShell
### <a name="to-delete-a-route-table"></a>Można usunąć tabeli tras

```powershell
Remove-AzureRouteTable -Name <routeTableName>
```
  
### <a name="to-list-a-route-table"></a>Aby wyświetlić tabelę tras

```powershell
Get-AzureRouteTable [-Name <routeTableName> [-DetailLevel <detailLevel>]]
```

### <a name="to-delete-a-route-from-a-route-table"></a>Aby usunąć trasy z tabeli tras

```powershell
Remove-AzureRouteTable –Name <routeTableName>
```

### <a name="to-remove-a-route-from-a-subnet"></a>Aby usunąć trasy z podsiecią

```powershell
Remove-AzureSubnetRouteTable –VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-list-the-route-table-associated-with-a-subnet"></a>Aby wyświetlić listę tabeli tras skojarzony z podsiecią

```powershell
Get-AzureSubnetRouteTable -VirtualNetworkName <virtualNetworkName> -SubnetName <subnetName>
```

### <a name="to-remove-a-default-site-from-a-vnet-vpn-gateway"></a>Aby usunąć domyślną witrynę z bramy sieci VPN w sieci wirtualnej

```powershell
Remove-AzureVnetGatewayDefaultSite -VNetName <virtualNetworkName>
```