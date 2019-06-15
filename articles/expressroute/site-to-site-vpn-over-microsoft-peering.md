---
title: Konfigurowanie sieci VPN lokacja lokacja za pośrednictwem komunikacji równorzędnej Azure - ExpressRoute — firmy Microsoft | Dokumentacja firmy Microsoft
description: Konfigurowanie połączeń protokołu IPsec/IKE z platformą Azure za pośrednictwem komunikacji równorzędnej obwodu usługi ExpressRoute firmy Microsoft przy użyciu bramy sieci VPN typu lokacja lokacja.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f35ed65b25d469b524e7174affecb45ad7c4735c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66115772"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurowanie sieci VPN lokacja lokacja za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute

Ten artykuł pomaga skonfigurować bezpieczne zaszyfrowane połączenia między swoją siecią lokalną i sieci wirtualnych platformy Azure (Vnet) za pośrednictwem połączenia prywatnego usługi ExpressRoute. Możesz użyć programu Microsoft komunikację równorzędną, aby ustanowić tunel protokołu IPsec/IKE sieci VPN lokacja lokacja między sieciami wirtualnymi platformy Azure i wybranych dla sieci lokalnych. Konfigurowanie bezpieczny tunel za pośrednictwem usługi ExpressRoute umożliwia wymianę danych z poufności, powtarzaniu, autentyczności i integralności.

>[!NOTE]
>Po skonfigurowaniu sieci VPN typu lokacja lokacja za pośrednictwem firmy Microsoft komunikacji równorzędnej, opłaty są naliczane za bramą sieci VPN i sieci VPN ruch wychodzący. Aby uzyskać więcej informacji, zobacz [cennik bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="architecture"></a>Architektura


  ![Omówienie połączenia](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Wysoką dostępność i nadmiarowość można skonfigurować wiele tuneli za pośrednictwem dwóch par MSEE PE obwód usługi ExpressRoute i włączyć równoważenie obciążenia między tuneli.

  ![Opcje wysokiej dostępności](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Za pomocą bramy sieci VPN lub przy użyciu odpowiednich wirtualnego urządzenia sieciowego (WUS) dostępnych za pośrednictwem witryny Azure Marketplace można przerywać działanie tuneli sieci VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft. Użytkownik może wymieniać trasy statycznie lub dynamicznie za pośrednictwem tuneli zaszyfrowanych bez narażania wymiana tras do podstawowej komunikacji równorzędnej firmy Microsoft. W przykładach w niniejszym artykule protokołu BGP (niezależne od sesji protokołu BGP, umożliwiający utworzenie komunikacji równorzędnej firmy Microsoft) służy do dynamicznej wymiany prefiksów za pośrednictwem tuneli zaszyfrowane.

>[!IMPORTANT]
>Dla strony lokalnymi zwykle komunikacji równorzędnej firmy Microsoft jest został przerwany w sieci obwodowej i prywatnej komunikacji równorzędnej jest zakończony w strefie sieci podstawowej. Dwie strefy zostałby rozdzielony za pomocą zapory. Jeśli konfigurujesz komunikację równorzędną Microsoft wyłącznie dla włączania bezpiecznego tunelowania za pośrednictwem usługi ExpressRoute, pamiętaj, aby filtrować tylko publiczne adresy IP zainteresowania, które są wprowadzenie anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft.
>
>

## <a name="workflow"></a>Przepływ pracy

1. Skonfiguruj komunikację równorzędną Microsoft dla obwodu usługi ExpressRoute.
2. Anonsowanie wybranego Azure regionalnych prefiksy publiczne do sieci lokalnej za pośrednictwem komunikacji równorzędnej firmy Microsoft.
3. Konfigurowanie bramy sieci VPN i ustanowienia tuneli IPsec
4. Skonfiguruj lokalne urządzenie sieci VPN.
5. Utwórz połączenie lokacja lokacja protokołu IPsec/IKE.
6. (Opcjonalnie) Konfigurowanie zapór/filtrowania, wybierz lokalnego urządzenia sieci VPN.
7. Testowanie i weryfikowanie Komunikacja IPsec w obwodzie usługi ExpressRoute.

## <a name="peering"></a>1. Konfigurowanie komunikacji równorzędnej firmy Microsoft

Aby skonfigurować połączenia sieci VPN typu lokacja lokacja za pośrednictwem usługi ExpressRoute, możesz korzystać, komunikacja równorzędna firmy Microsoft usługi ExpressRoute.

* Aby skonfigurować nowy obwód usługi ExpressRoute, zacznij od [wymagania wstępne usługi ExpressRoute](expressroute-prerequisites.md) artykułu, a następnie [tworzenie i modyfikowanie obwodu ExpressRoute](expressroute-howto-circuit-arm.md).

* Jeśli masz już obwodu usługi ExpressRoute, ale nie mają skonfigurowane komunikacji równorzędnej firmy Microsoft, można skonfigurować za pomocą komunikacji równorzędnej firmy Microsoft [Utwórz i zmodyfikować komunikację równorzędną dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md#msft) artykułu.

Po skonfigurowaniu obwodu i komunikacji równorzędnej firmy Microsoft można łatwo wyświetlić za pomocą **Przegląd** strony w witrynie Azure portal.

![obwód](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="routefilter"></a>2. Konfigurowanie filtrów tras

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest zasadniczo lista wszystkich dozwolonych wartości społeczności protokołu BGP. 

![filtr tras](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

W tym przykładzie wdrożenia znajduje się tylko w *Azure zachodnie stany USA 2* regionu. Reguły filtru trasy zostanie dodany do Zezwalaj tylko anons prefiksy regionalnych Azure zachodnie stany USA 2, który ma wartość społeczności BGP *12076:51026*. Określ regionalnych prefiksy, które chcesz zezwolić, wybierając **reguły Zarządzaj**.

W ciągu filtru trasy, należy również wybrać obwodów usługi ExpressRoute, dla których ma zastosowanie filtru trasy. Obwody usługi ExpressRoute można wybrać, wybierając **dodać obwodu**. Na poprzednim rysunku filtru tras jest skojarzona z przykładu obwód usługi ExpressRoute.

### <a name="configfilter"></a>2.1 Konfigurowanie filtru tras

Konfigurowanie filtrów tras. Aby uzyskać instrukcje, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-portal.md).

### <a name="verifybgp"></a>2.2 Sprawdź trasy protokołu BGP

Po pomyślnie utworzono Microsoft komunikacji równorzędnej na obwód usługi ExpressRoute i skojarzony filtr tras obwodu, możesz sprawdzić trasy protokołu BGP otrzymane od Msee na urządzeniach PE, które są komunikacji równorzędnej z rozwiązaniami Msee. Polecenie weryfikacji różni się w zależności od systemu operacyjnego urządzenia PE.

#### <a name="cisco-examples"></a>Przykłady firmy Cisco

W tym przykładzie użyto polecenia Cisco IOS-XE. W tym przykładzie wirtualnego routingu i przekazywania wystąpień (VRF) jest używany do izolacji ruchu komunikacji równorzędnej.

```
show ip bgp vpnv4 vrf 10 summary
```

Następujące częściowe dane wyjściowe pokazują, że prefiksy 68 otrzymane od sąsiada \*.243.229.34 przy użyciu jego numer ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Aby wyświetlić listę prefiksów odebranych z sąsiada, skorzystaj z następującego przykładu:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Aby upewnić się, otrzymują odpowiedni zestaw prefiksów, użytkownik może sprawdzać. Następujące dane wyjściowe polecenia programu Azure PowerShell Wyświetla listę prefiksów anonsowanych za pośrednictwem komunikacji równorzędnej dla poszczególnych usług i dla każdego regionu platformy Azure firmy Microsoft:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="vpngateway"></a>3. Konfigurowanie bramy sieci VPN i tuneli protokołu IPsec

W tej sekcji są tworzone Tunele IPsec sieci VPN, między bramą sieci VPN platformy Azure i lokalnym urządzeniem sieci VPN. W przykładach użyto urządzenia sieci VPN Cisco Cloud Service routera (CSR1000).

Na poniższym diagramie przedstawiono sieci VPN IPsec tuneli określana pomiędzy lokalnym urządzeniem sieci VPN 1 i pary wystąpienie bramy sieci VPN platformy Azure. Dwa Tunele IPsec sieci VPN jest określana pomiędzy lokalnym urządzeniem sieci VPN 2 pary wystąpienie bramy sieci VPN platformy Azure nie jest przedstawione na diagramie i nie są wyświetlane szczegóły konfiguracji. Jednak mające tunele VPN dodatkowe zwiększa wysokiej dostępności.

  ![Tuneli sieci VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

Za pośrednictwem para tuneli protokołu IPsec zostanie utworzona sesja eBGP do wymiany tras sieci prywatnej. Na poniższym diagramie przedstawiono sesji eBGP odbywają się za pośrednictwem pary tunelu IPsec:

  ![sesje eBGP za pośrednictwem tunelu parę](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Na poniższym diagramie przedstawiono omówienie abstrakcyjną przykładowej sieci:

  ![przykład sieci](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Przykłady szablonów usługi Azure Resource Manager — informacje

W przykładach bramy sieci VPN i zakończeń tunelu IPsec są skonfigurowane przy użyciu szablonu usługi Azure Resource Manager. Jeśli jesteś nowym użytkownikiem przy użyciu szablonów usługi Resource Manager, lub podstawy szablonu usługi Resource Manager, zobacz [Omówienie struktury i składni szablonów usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md). Ten szablon w tej sekcji tworzy rozwiązanie środowiska platformy Azure (VNet). Jednak jeśli masz istniejącej sieci wirtualnej, można się odwołać je w szablonie. Jeśli użytkownik nie jest zaznajomiony z konfiguracjami lokacja lokacja protokołu IPsec/IKE bramy sieci VPN, zobacz [utworzyć połączenie lokacja lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Nie trzeba za pomocą szablonów usługi Azure Resource Manager, aby można było utworzyć tej konfiguracji. Można utworzyć tej konfiguracji, za pomocą witryny Azure portal lub programu PowerShell.
>
>

### <a name="variables3"></a>Wersji 3.1 Zadeklaruj zmienne w

W tym przykładzie deklaracje zmiennych odpowiadają przykładową sieć. Podczas deklarowania zmiennych, zmodyfikuj tę sekcję, aby odzwierciedlać Twojego środowiska.

* Zmienna **localAddressPrefix** jest tablicą adresów IP w środowisku lokalnym, aby zakończyć tuneli IPsec.
* **GatewaySku** Określa przepływność sieci VPN. Aby uzyskać więcej informacji na temat gatewaySku i vpnType zobacz [ustawienia konfiguracji bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby poznać ceny, zobacz [cennik bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Ustaw **vpnType** do **RouteBased**.

```json
"variables": {
  "virtualNetworkName": "SecureVNet",       // Name of the Azure VNet
  "azureVNetAddressPrefix": "10.2.0.0/24",  // Address space assigned to the VNet
  "subnetName": "Tenant",                   // subnet name in which tenants exists
  "subnetPrefix": "10.2.0.0/25",            // address space of the tenant subnet
  "gatewaySubnetPrefix": "10.2.0.224/27",   // address space of the gateway subnet
  "localGatewayName": "localGW1",           // name of remote gateway (on-premises)
  "localGatewayIpAddress": "X.243.229.110", // public IP address of the on-premises VPN device
  "localAddressPrefix": [
    "172.16.0.1/32",                        // termination of IPsec tunnel-1 on-premises 
    "172.16.0.2/32"                         // termination of IPsec tunnel-2 on-premises 
  ],
  "gatewayPublicIPName1": "vpnGwVIP1",    // Public address name of the first VPN gateway instance
  "gatewayPublicIPName2": "vpnGwVIP2",    // Public address name of the second VPN gateway instance 
  "gatewayName": "vpnGw",                 // Name of the Azure VPN gateway
  "gatewaySku": "VpnGw1",                 // Azure VPN gateway SKU
  "vpnType": "RouteBased",                // type of VPN gateway
  "sharedKey": "string",                  // shared secret needs to match with on-premises configuration
  "asnVpnGateway": 65000,                 // BGP Autonomous System number assigned to the VPN Gateway 
  "asnRemote": 65010,                     // BGP Autonmous Syste number assigned to the on-premises device
  "bgpPeeringAddress": "172.16.0.3",      // IP address of the remote BGP peer on-premises
  "connectionName": "vpn2local1",
  "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', variables('virtualNetworkName'))]",
  "gatewaySubnetRef": "[concat(variables('vnetID'),'/subnets/','GatewaySubnet')]",
  "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]",
  "api-version": "2017-06-01"
},
```

### <a name="vnet"></a>3.2 Tworzenie sieci wirtualnej (VNet)

Jeśli są kojarzenie istniejącej sieci wirtualnej przy użyciu tuneli sieci VPN, możesz pominąć ten krok.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/virtualNetworks",
  "name": "[variables('virtualNetworkName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "addressSpace": {
      "addressPrefixes": [
        "[variables('azureVNetAddressPrefix')]"
      ]
    },
    "subnets": [
      {
        "name": "[variables('subnetName')]",
        "properties": {
          "addressPrefix": "[variables('subnetPrefix')]"
        }
      },
      {
        "name": "GatewaySubnet",
        "properties": {
          "addressPrefix": "[variables('gatewaySubnetPrefix')]"
        }
      }
    ]
  },
  "comments": "Create a Virtual Network with Subnet1 and Gatewaysubnet"
},
```

### <a name="ip"></a>3.3 przydzielać publiczne adresy IP do wystąpienia bramy sieci VPN
 
Przypisz publiczny adres IP dla każdego wystąpienia bramy sieci VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName1')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the first instance of the VPN gateway"
  },
  {
    "apiVersion": "[variables('api-version')]",
    "type": "Microsoft.Network/publicIPAddresses",
    "name": "[variables('gatewayPublicIPName2')]",
    "location": "[resourceGroup().location]",
    "properties": {
      "publicIPAllocationMethod": "Dynamic"
    },
    "comments": "Public IP for the second instance of the VPN gateway"
  },
```

### <a name="termination"></a>3.4 Określ zakończenie działania tunelu sieci VPN w środowisku lokalnym (Brama sieci lokalnej)

Lokalne urządzenia sieci VPN są określane jako **bramy sieci lokalnej**. Poniższy fragment kodu json określa również zdalnego szczegóły elementu równorzędnego protokołu BGP:

```json
{
  "apiVersion": "[variables('api-version')]",
  "type": "Microsoft.Network/localNetworkGateways",
  "name": "[variables('localGatewayName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "localNetworkAddressSpace": {
      "addressPrefixes": "[variables('localAddressPrefix')]"
    },
    "gatewayIpAddress": "[variables('localGatewayIpAddress')]",
    "bgpSettings": {
      "asn": "[variables('asnRemote')]",
      "bgpPeeringAddress": "[variables('bgpPeeringAddress')]",
      "peerWeight": 0
    }
  },
  "comments": "Local Network Gateway (referred to your on-premises location) with IP address of remote tunnel peering and IP address of remote BGP peer"
},
```

### <a name="creategw"></a>3.5 Tworzenie bramy sieci VPN

Ta sekcja szablonu konfiguruje bramy sieci VPN z wymaganymi ustawieniami w konfiguracji aktywny aktywny. Należy pamiętać, następujące wymagania:

* Tworzenie bramy sieci VPN za pomocą **"RouteBased"** VpnType. To ustawienie jest wymagane, jeśli chcesz włączyć routing protokołu BGP między bramą sieci VPN i sieci VPN w środowisku lokalnym.
* Aby ustanowić tuneli VPN między tymi wystąpieniami bramy sieci VPN i urządzenia podanego w środowisku lokalnym w trybie aktywny aktywny **"activeActive"** parametr ma wartość **true** w szablonie usługi Resource Manager . Aby dowiedzieć się więcej na temat bram sieci VPN o wysokiej dostępności, zobacz [o wysokiej dostępności połączenie bramy sieci VPN](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Aby skonfigurować sesje eBGP między tunele VPN, należy określić dwa różne numery ASN po obu stronach. Zaleca się, aby określić prywatnych numerów ASN. Aby uzyskać więcej informacji, zobacz [Omówienie protokołu BGP i sieci VPN platformy Azure bram](../vpn-gateway/vpn-gateway-bgp-overview.md).

```json
{
"apiVersion": "[variables('api-version')]",
"type": "Microsoft.Network/virtualNetworkGateways",
"name": "[variables('gatewayName')]",
"location": "[resourceGroup().location]",
"dependsOn": [
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName1'))]",
  "[concat('Microsoft.Network/publicIPAddresses/', variables('gatewayPublicIPName2'))]",
  "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
],
"properties": {
  "ipConfigurations": [
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName1'))]"
        }
      },
      "name": "vnetGtwConfig1"
    },
    {
      "properties": {
        "privateIPAllocationMethod": "Dynamic",
        "subnet": {
          "id": "[variables('gatewaySubnetRef')]"
        },
        "publicIPAddress": {
          "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('gatewayPublicIPName2'))]"
        }
      },
          "name": "vnetGtwConfig2"
        }
      ],
      "sku": {
        "name": "[variables('gatewaySku')]",
        "tier": "[variables('gatewaySku')]"
      },
      "gatewayType": "Vpn",
      "vpnType": "[variables('vpnType')]",
      "enableBgp": true,
      "activeActive": true,
      "bgpSettings": {
        "asn": "[variables('asnVpnGateway')]"
      }
    },
    "comments": "VPN Gateway in active-active configuration with BGP support"
  },
```

### <a name="ipsectunnel"></a>3.6 ustanowienia tuneli IPsec

Ostateczna akcja skryptu tworzy tuneli IPsec między bramą sieci VPN platformy Azure i lokalnym urządzeniem sieci VPN.

```json
{
  "apiVersion": "[variables('api-version')]",
  "name": "[variables('connectionName')]",
  "type": "Microsoft.Network/connections",
  "location": "[resourceGroup().location]",
  "dependsOn": [
    "[concat('Microsoft.Network/virtualNetworkGateways/', variables('gatewayName'))]",
    "[concat('Microsoft.Network/localNetworkGateways/', variables('localGatewayName'))]"
  ],
  "properties": {
    "virtualNetworkGateway1": {
      "id": "[resourceId('Microsoft.Network/virtualNetworkGateways', variables('gatewayName'))]"
    },
    "localNetworkGateway2": {
      "id": "[resourceId('Microsoft.Network/localNetworkGateways', variables('localGatewayName'))]"
    },
    "connectionType": "IPsec",
    "routingWeight": 0,
    "sharedKey": "[variables('sharedKey')]",
    "enableBGP": "true"
  },
  "comments": "Create a Connection type site-to-site (IPsec) between the Azure VPN Gateway and the VPN device on-premises"
  }
```

## <a name="device"></a>4. Konfigurowanie lokalnego urządzenia sieci VPN

Brama sieci VPN platformy Azure jest zgodna z wielu urządzeń sieci VPN od różnych dostawców. Aby uzyskać informacje o konfiguracji i urządzeń, które zostały zatwierdzone do pracy z usługą VPN gateway, zobacz [urządzenia sieci VPN o](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Podczas konfigurowania urządzenia sieci VPN, potrzebne są następujące elementy:

* Klucz współużytkowany. Jest to ten sam klucz współużytkowany, którego należy określić podczas tworzenia połączenia sieci VPN typu lokacja lokacja. W przykładach użyto podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
* Publiczny adres IP bramy sieci VPN. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN przy użyciu witryny Azure portal, przejdź do bramy sieci wirtualnej, a następnie kliknij nazwę bramy.

Zazwyczaj eBGP elementów równorzędnych były bezpośrednio podłączone (często w ciągu połączenia WAN). Konfigurując eBGP za pośrednictwem tuneli IPsec sieci VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft usługi ExpressRoute, istnieją jednak wiele domen routingu między komputerami równorzędnymi eBGP. Użyj **wielokrotnego przeskoku ebgp** polecenie, aby ustanowić relację sąsiada eBGP między tymi dwoma nie-bezpośrednio połączonych elementów równorzędnych. Liczba całkowita, która następuje po wielokrotnego przeskoku ebgp polecenia określa wartość czasu wygaśnięcia w pakiety protokołu BGP. Polecenie **eibgp ścieżki maksymalnie 2** umożliwia równoważenie obciążenia ruchem między tych dwóch ścieżek protokołu BGP.

### <a name="cisco1"></a>Przykład CSR1000 Cisco

Poniższy przykład przedstawia konfigurację Cisco CSR1000 na maszynie wirtualnej funkcji Hyper-V, lokalnych urządzeń sieci VPN:

```
!
crypto ikev2 proposal az-PROPOSAL
 encryption aes-cbc-256 aes-cbc-128 3des
 integrity sha1
 group 2
!
crypto ikev2 policy az-POLICY
 proposal az-PROPOSAL
!
crypto ikev2 keyring key-peer1
 peer azvpn1
  address 52.175.253.112
  pre-shared-key secret*1234
 !
!
crypto ikev2 keyring key-peer2
 peer azvpn2
  address 52.175.250.191
  pre-shared-key secret*1234
 !
!
!
crypto ikev2 profile az-PROFILE1
 match address local interface GigabitEthernet1
 match identity remote address 52.175.253.112 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer1
!
crypto ikev2 profile az-PROFILE2
 match address local interface GigabitEthernet1
 match identity remote address 52.175.250.191 255.255.255.255
 authentication remote pre-share
 authentication local pre-share
 keyring local key-peer2
!
crypto ikev2 dpd 10 2 on-demand
!
!
crypto ipsec transform-set az-IPSEC-PROPOSAL-SET esp-aes 256 esp-sha-hmac
 mode tunnel
!
crypto ipsec profile az-VTI1
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE1
!
crypto ipsec profile az-VTI2
 set transform-set az-IPSEC-PROPOSAL-SET
 set ikev2-profile az-PROFILE2
!
!
interface Loopback0
 ip address 172.16.0.3 255.255.255.255
!
interface Tunnel0
 ip address 172.16.0.1 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.253.112
 tunnel protection ipsec profile az-VTI1
!
interface Tunnel1
 ip address 172.16.0.2 255.255.255.255
 ip tcp adjust-mss 1350
 tunnel source GigabitEthernet1
 tunnel mode ipsec ipv4
 tunnel destination 52.175.250.191
 tunnel protection ipsec profile az-VTI2
!
interface GigabitEthernet1
 description External interface
 ip address x.243.229.110 255.255.255.252
 negotiation auto
 no mop enabled
 no mop sysid
!
interface GigabitEthernet2
 ip address 10.0.0.1 255.255.255.0
 negotiation auto
 no mop enabled
 no mop sysid
!
router bgp 65010
 bgp router-id interface Loopback0
 bgp log-neighbor-changes
 network 10.0.0.0 mask 255.255.255.0
 network 10.1.10.0 mask 255.255.255.128
 neighbor 10.2.0.228 remote-as 65000
 neighbor 10.2.0.228 ebgp-multihop 5
 neighbor 10.2.0.228 update-source Loopback0
 neighbor 10.2.0.228 soft-reconfiguration inbound
 neighbor 10.2.0.228 filter-list 10 out
 neighbor 10.2.0.229 remote-as 65000    
 neighbor 10.2.0.229 ebgp-multihop 5
 neighbor 10.2.0.229 update-source Loopback0
 neighbor 10.2.0.229 soft-reconfiguration inbound
 maximum-paths eibgp 2
!
ip route 0.0.0.0 0.0.0.0 10.1.10.1
ip route 10.2.0.228 255.255.255.255 Tunnel0
ip route 10.2.0.229 255.255.255.255 Tunnel1
!
```

## <a name="firewalls"></a>5. Konfigurowanie filtrowania urządzeń sieci VPN i zapory (opcjonalnie)

Konfigurowanie zapory i filtrowanie zgodnie z wymaganiami.

## <a name="testipsec"></a>6. Testowanie i Walidacja tunelu IPsec

Stan tuneli IPsec można sprawdzić w bramie sieci VPN platformy Azure za pomocą poleceń programu Powershell:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Przykładowe dane wyjściowe:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Aby sprawdzić stan tuneli bramy sieci VPN Azure wystąpienia niezależnie, skorzystaj z następującego przykładu:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object -ExpandProperty TunnelConnectionStatus
```

Przykładowe dane wyjściowe:

```azurepowershell
Tunnel                           : vpn2local1_52.175.250.191
ConnectionStatus                 : Connected
IngressBytesTransferred          : 4877438
EgressBytesTransferred           : 8754071
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:30

Tunnel                           : vpn2local1_52.175.253.112
ConnectionStatus                 : Connected
IngressBytesTransferred          : 5660773
EgressBytesTransferred           : 8980589
LastConnectionEstablishedUtcTime : 11/04/2017 17:03:13
```

Możesz również sprawdzić stan tunelu na urządzeniu sieci VPN w środowisku lokalnym.

Przykład CSR1000 Cisco:

```
show crypto session detail
show crypto ikev2 sa
show crypto ikev2 session detail
show crypto ipsec sa
```

Przykładowe dane wyjściowe:

```
csr1#show crypto session detail

Crypto session current status

Code: C - IKE Configuration mode, D - Dead Peer Detection
K - Keepalives, N - NAT-traversal, T - cTCP encapsulation
X - IKE Extended Authentication, F - IKE Fragmentation
R - IKE Auto Reconnect

Interface: Tunnel1
Profile: az-PROFILE2
Uptime: 00:52:46
Session status: UP-ACTIVE
Peer: 52.175.250.191 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.250.191
      Desc: (none)
  Session ID: 3
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.250.191/4500 Active
          Capabilities:DN connid:3 lifetime:23:07:14
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 279 drop 0 life (KB/Sec) 4607976/433
        Outbound: #pkts enc'ed 164 drop 0 life (KB/Sec) 4607992/433

Interface: Tunnel0
Profile: az-PROFILE1
Uptime: 00:52:43
Session status: UP-ACTIVE
Peer: 52.175.253.112 port 4500 fvrf: (none) ivrf: (none)
      Phase1_id: 52.175.253.112
      Desc: (none)
  Session ID: 2
  IKEv2 SA: local 10.1.10.50/4500 remote 52.175.253.112/4500 Active
          Capabilities:DN connid:2 lifetime:23:07:17
  IPSEC FLOW: permit ip 0.0.0.0/0.0.0.0 0.0.0.0/0.0.0.0
        Active SAs: 2, origin: crypto map
        Inbound:  #pkts dec'ed 668 drop 0 life (KB/Sec) 4607926/437
        Outbound: #pkts enc'ed 477 drop 0 life (KB/Sec) 4607953/437
```

Protokół linii na wirtualnej interfejsu tunelu (VTI) nie zmienia się na "Konfigurowanie", dopóki nie zakończy się IKE Faza 2. Poniższe polecenie sprawdza skojarzenia zabezpieczeń:

```
csr1#show crypto ikev2 sa

IPv4 Crypto IKEv2  SA

Tunnel-id Local                 Remote                fvrf/ivrf            Status
2         10.1.10.50/4500       52.175.253.112/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3277 sec

Tunnel-id Local                 Remote                fvrf/ivrf            Status
3         10.1.10.50/4500       52.175.250.191/4500   none/none            READY
      Encr: AES-CBC, keysize: 256, PRF: SHA1, Hash: SHA96, DH Grp:2, Auth sign: PSK, Auth verify: PSK
      Life/Active Time: 86400/3280 sec

IPv6 Crypto IKEv2  SA

csr1#show crypto ipsec sa | inc encaps|decaps
    #pkts encaps: 177, #pkts encrypt: 177, #pkts digest: 177
    #pkts decaps: 296, #pkts decrypt: 296, #pkts verify: 296
    #pkts encaps: 554, #pkts encrypt: 554, #pkts digest: 554
    #pkts decaps: 746, #pkts decrypt: 746, #pkts verify: 746
```

### <a name="verifye2e"></a>Sprawdź łączność end-to-end między wewnętrznej sieci lokalnej i sieci wirtualnej platformy Azure

Jeśli tuneli IPsec i trasy statyczne są poprawnie ustawione, należy wykonać polecenie ping na adres IP zdalnego elementu równorzędnego protokołu BGP:

```
csr1#ping 10.2.0.228
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.228, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 5/5/5 ms

#ping 10.2.0.229
Type escape sequence to abort.
Sending 5, 100-byte ICMP Echos to 10.2.0.229, timeout is 2 seconds:
!!!!!
Success rate is 100 percent (5/5), round-trip min/avg/max = 4/5/6 ms
```

### <a name="verifybgp"></a>Weryfikowanie sesje protokołu BGP za pośrednictwem protokołu IPsec

Sprawdź stan elementu równorzędnego protokołu BGP, w bramie sieci VPN platformy Azure:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayBGPPeerStatus -VirtualNetworkGatewayName vpnGtw -ResourceGroupName SEA-C1-VPN-ER | ft
```

Przykładowe dane wyjściowe:

```azurepowershell
  Asn ConnectedDuration LocalAddress MessagesReceived MessagesSent Neighbor    RoutesReceived State    
  --- ----------------- ------------ ---------------- ------------ --------    -------------- -----    
65010 00:57:19.9003584  10.2.0.228               68           72   172.16.0.10              2 Connected
65000                   10.2.0.228                0            0   10.2.0.228               0 Unknown  
65000 07:13:51.0109601  10.2.0.228              507          500   10.2.0.229               6 Connected
```

Aby sprawdzić listę prefiksy sieci odebranych za pośrednictwem eBGP VPN koncentrator lokalnie, można filtrować według atrybutów "Origin":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

W przykładowych danych wyjściowych 65010 numer ASN jest numer systemu autonomicznego BGP w sieci VPN w środowisku lokalnym.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Aby wyświetlić listę trasy anonsowane:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayAdvertisedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG -Peer 10.2.0.228 | ft
```

Przykładowe dane wyjściowe:

```azurepowershell
AsPath LocalAddress Network        NextHop    Origin SourcePeer Weight
------ ------------ -------        -------    ------ ---------- ------
       10.2.0.229   10.2.0.0/24    10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.10/32 10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.5/32  10.2.0.229 Igp                  0
       10.2.0.229   172.16.0.1/32  10.2.0.229 Igp                  0
65010  10.2.0.229   10.1.10.0/25   10.2.0.229 Igp                  0
65010  10.2.0.229   10.0.0.0/24    10.2.0.229 Igp                  0
```

Przykład dotyczący CSR1000 Cisco lokalnie:

```
csr1#show ip bgp neighbors 10.2.0.228 routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.2.0.0/24      10.2.0.228                             0 65000 i
 r>   172.16.0.1/32    10.2.0.228                             0 65000 i
 r>   172.16.0.2/32    10.2.0.228                             0 65000 i
 r>   172.16.0.3/32   10.2.0.228                             0 65000 i

Total number of prefixes 4
```

Na liście sieci anonsowane CSR1000 Cisco środowiska lokalnego do bramy sieci VPN platformy Azure mogą być wyświetlane za pomocą następującego polecenia:

```
csr1#show ip bgp neighbors 10.2.0.228 advertised-routes
BGP table version is 7, local router ID is 172.16.0.10
Status codes: s suppressed, d damped, h history, * valid, > best, i - internal,
              r RIB-failure, S Stale, m multipath, b backup-path, f RT-Filter,
              x best-external, a additional-path, c RIB-compressed,
              t secondary path,
Origin codes: i - IGP, e - EGP, ? - incomplete
RPKI validation codes: V valid, I invalid, N Not found

     Network          Next Hop            Metric LocPrf Weight Path
 *>   10.0.0.0/24      0.0.0.0                  0         32768 i
 *>   10.1.10.0/25     0.0.0.0                  0         32768 i

Total number of prefixes 2
```

## <a name="next-steps"></a>Kolejne kroki

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](how-to-npm.md)

* [Dodawanie połączenia lokacja lokacja z siecią wirtualną przy użyciu istniejącego połączenia bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
