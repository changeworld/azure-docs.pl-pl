---
title: 'Azure ExpressRoute: Konfigurowanie sieci VPN S2S za pośrednictwem komunikacji równorzędnej firmy Microsoft'
description: Konfigurowanie łączności IPsec/IKE z platformą Azure za pośrednictwem obwodu komunikacji równorzędnej firmy Microsoft usługi ExpressRoute przy użyciu bramy sieci VPN typu lokacja lokacja lokacja.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: f3044a2701b0f1cd0e5f9ab3ab60c1d60cfb8f45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436812"
---
# <a name="configure-a-site-to-site-vpn-over-expressroute-microsoft-peering"></a>Konfigurowanie sieci VPN typu lokacja-lokacja za pośrednictwem usługi ExpressRoute Microsoft peering

Ten artykuł ułatwia konfigurowanie bezpiecznej szyfrowanej łączności między siecią lokalną a sieciami wirtualnymi platformy Azure za pośrednictwem połączenia prywatnego usługi ExpressRoute. Komunikacji równorzędnej firmy Microsoft można użyć do ustanowienia tunelu sieci VPN IPsec/IKE między wybranymi sieciami lokalnymi a sieciami wirtualnymi platformy Azure. Konfigurowanie bezpiecznego tunelu za pomocą usługi ExpressRoute umożliwia wymianę danych z poufnością, anty-powtórką, autentycznością i integralnością.

>[!NOTE]
>Podczas konfigurowania sieci VPN między lokacjami za pośrednictwem komunikacji równorzędnej firmy Microsoft pobierana jest opłata za bramę sieci VPN i wyjście sieci VPN. Aby uzyskać więcej informacji, zobacz [Cennik bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
>
>

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="architecture"></a><a name="architecture"></a>Architektura


  ![omówienie łączności](./media/site-to-site-vpn-over-microsoft-peering/IPsecER_Overview.png)


Aby uzyskać wysoką dostępność i nadmiarowość, można skonfigurować wiele tuneli na dwóch parach MSEE-PE obwodu usługi ExpressRoute i włączyć równoważenie obciążenia między tunelami.

  ![opcje wysokiej dostępności](./media/site-to-site-vpn-over-microsoft-peering/HighAvailability.png)

Tunele sieci VPN za pośrednictwem komunikacji równorzędnej firmy Microsoft można zakończyć za pomocą bramy sieci VPN lub przy użyciu odpowiedniego urządzenia wirtualnego sieci (NVA) dostępnego za pośrednictwem portalu Azure Marketplace. Można wymieniać trasy statycznie lub dynamicznie za pomocą zaszyfrowanych tuneli bez narażania wymiany trasy na podstawową komunikację równorzędną firmy Microsoft. W przykładach w tym artykule BGP (różni się od sesji BGP używane do tworzenia komunikacji równorzędnej firmy Microsoft) jest używany do dynamicznej wymiany prefiksów za pomocą zaszyfrowanych tuneli.

>[!IMPORTANT]
>Po stronie lokalnej zazwyczaj komunikacja równorzędna firmy Microsoft jest końnana w strefie DMZ, a prywatna komunikacja równorzędna jest końnana w strefie sieci podstawowej. Obie strefy będą segregowane przy użyciu zapór. Jeśli konfigurujesz komunikację równorzędnych firmy Microsoft wyłącznie w celu włączania bezpiecznego tunelowania za pośrednictwem usługi ExpressRoute, pamiętaj, aby filtrować tylko publiczne usługi IP będące przedmiotem zainteresowania, które są anonsowane za pośrednictwem komunikacji równorzędnej firmy Microsoft.
>
>

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

1. Konfigurowanie komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.
2. Anonsuj wybrane regionalne prefiksy publiczne platformy Azure do sieci lokalnej za pośrednictwem komunikacji równorzędnej firmy Microsoft.
3. Konfigurowanie bramy sieci VPN i ustanawianie tuneli IPsec
4. Skonfiguruj lokalne urządzenie sieci VPN.
5. Utwórz połączenie IPsec/IKE między lokacjami.
6. (Opcjonalnie) Skonfiguruj zapory/filtrowanie na lokalnym urządzeniu sieci VPN.
7. Przetestuj i sprawdź poprawność komunikacji IPsec za pomocą obwodu usługi ExpressRoute.

## <a name="1-configure-microsoft-peering"></a><a name="peering"></a>1. Konfigurowanie komunikacji równorzędnej firmy Microsoft

Aby skonfigurować połączenie sieci VPN między lokacjami za pośrednictwem usługi ExpressRoute, należy korzystać z komunikacji równorzędnej usługi ExpressRoute Firmy Microsoft.

* Aby skonfigurować nowy obwód usługi ExpressRoute, należy rozpocząć od artykułu [Wymagania wstępne usługi ExpressRoute,](expressroute-prerequisites.md) a następnie [utworzyć i zmodyfikować obwód usługi ExpressRoute](expressroute-howto-circuit-arm.md).

* Jeśli masz już obwód usługi ExpressRoute, ale nie masz skonfigurowanej komunikacji równorzędnej firmy Microsoft, skonfiguruj komunikację równorzędną firmy Microsoft przy użyciu artykułu [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute.](expressroute-howto-routing-arm.md#msft)

Po skonfigurowaniu obwodu i komunikacji równorzędnej firmy Microsoft można go łatwo wyświetlić za pomocą strony **Przegląd** w witrynie Azure portal.

![Obwodu](./media/site-to-site-vpn-over-microsoft-peering/ExpressRouteCkt.png)

## <a name="2-configure-route-filters"></a><a name="routefilter"></a>2. Konfigurowanie filtrów tras

Filtr tras umożliwia zidentyfikowanie usług, które mają być używane za pośrednictwem komunikacji równorzędnej firmy Microsoft w ramach obwodu usługi ExpressRoute. Jest to zasadniczo lista dozwolonych wszystkich wartości społeczności BGP. 

![filtr trasy](./media/site-to-site-vpn-over-microsoft-peering/route-filter.png)

W tym przykładzie wdrożenie jest tylko w regionie *Azure West US 2.* Reguła filtru marszruty jest dodawana, aby zezwalać tylko na anonse prefiksów regionalnych usługi Azure West US 2, których wartość społeczności BGP *jest 12076:51026.* Prefiksy regionalne, na które chcesz zezwolić, należy określić, wybierając pozycję **Zarządzaj regułą**.

W ramach filtru trasy należy również wybrać obwody usługi ExpressRoute, do których ma zastosowanie filtr trasy. Obwody usługi ExpressRoute można wybrać, wybierając opcję **Dodaj obwód**. Na poprzednim rysunku filtr trasy jest skojarzony z przykładowym obwodem usługi ExpressRoute.

### <a name="21-configure-the-route-filter"></a><a name="configfilter"></a>2.1 Konfigurowanie filtru trasy

Konfigurowanie filtru trasy. Aby zapoznać się z instrukcjami, zobacz [Konfigurowanie filtrów tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-portal.md).

### <a name="22-verify-bgp-routes"></a><a name="verifybgp"></a>2.2 Weryfikacja tras BGP

Po pomyślnym utworzeniu komunikacji równorzędnej firmy Microsoft w obwodzie usługi ExpressRoute i skojarzeniu filtru trasy z obwódem można sprawdzić trasy protokołu BGP odebrane od msee na urządzeniach pe, które są komunikacji równorzędnej z MSEE. Polecenie weryfikacji różni się w zależności od systemu operacyjnego urządzeń PE.

#### <a name="cisco-examples"></a>Przykłady cisco

W tym przykładzie użyto polecenia Cisco IOS-XE. W tym przykładzie wystąpienie routingu wirtualnego i przekazywania (VRF) służy do izolowania ruchu równorzędnego.

```
show ip bgp vpnv4 vrf 10 summary
```

Następujące dane wyjściowe częściowe pokazują, że od \*sąsiada odebrano 68 prefiksów .243.229.34 z ASN 12076 (MSEE):

```
...

Neighbor        V           AS MsgRcvd MsgSent   TblVer  InQ OutQ Up/Down  State/PfxRcd
X.243.229.34    4        12076   17671   17650    25228    0    0 1w4d           68
```

Aby wyświetlić listę prefiksów otrzymanych od sąsiada, użyj następującego przykładu:

```
sh ip bgp vpnv4 vrf 10 neighbors X.243.229.34 received-routes
```

Aby potwierdzić, że otrzymujesz poprawny zestaw prefiksów, można sprawdzić krzyżowo. Następujące dane wyjściowe polecenia programu Azure PowerShell zawierają listę prefiksów anonsowanych za pośrednictwem komunikacji równorzędnej firmy Microsoft dla każdej z usług i dla każdego regionu platformy Azure:

```azurepowershell-interactive
Get-AzBgpServiceCommunity
```

## <a name="3-configure-the-vpn-gateway-and-ipsec-tunnels"></a><a name="vpngateway"></a>3. Konfigurowanie bramy sieci VPN i tuneli IPsec

W tej sekcji tunele sieci VPN protokołu IPsec są tworzone między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN. W przykładach używane są urządzenia sieci VPN z usługą Cisco Cloud Service Router (CSR1000).

Na poniższym diagramie przedstawiono tunele sieci VPN protokołu IPsec ustanowione między lokalnym urządzeniem sieci VPN 1 a parą wystąpień bramy sieci VPN platformy Azure. Dwa tunele sieci VPN protokołu IPsec ustanowione między lokalnym urządzeniem sieci VPN 2 a parą wystąpień bramy sieci Vpn platformy Azure nie są zilustrowane na diagramie, a szczegóły konfiguracji nie są wyświetlane. Jednak posiadanie dodatkowych tuneli VPN zwiększa wysoką dostępność.

  ![Tunele VPN](./media/site-to-site-vpn-over-microsoft-peering/EstablishTunnels.png)

W parze tuneli IPsec ustanawia się sesję eBGP w celu wymiany tras sieci prywatnej. Na poniższym diagramie przedstawiono sesję eBGP ustanowioną na parze tuneli IPsec:

  ![Sesje eBGP nad parą tuneli](./media/site-to-site-vpn-over-microsoft-peering/TunnelBGP.png)

Na poniższym diagramie przedstawiono abstrakcyjny przegląd przykładowej sieci:

  ![przykładowa sieć](./media/site-to-site-vpn-over-microsoft-peering/OverviewRef.png)

### <a name="about-the-azure-resource-manager-template-examples"></a>Przykłady szablonów usługi Azure Resource Manager — informacje

W przykładach bramy sieci VPN i zakończenia tunelu IPsec są konfigurowane przy użyciu szablonu usługi Azure Resource Manager. Jeśli jesteś nowy w użyciu szablonów Menedżera zasobów lub aby zrozumieć podstawy szablonu Menedżera zasobów, zobacz [Opis struktury i składni szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md). Szablon w tej sekcji tworzy środowisko platformy Azure greenfield (VNet). Jednak jeśli masz istniejącą sieci wirtualnej, można odwoływać się do niej w szablonie. Jeśli nie znasz konfiguracji IPsec/IKE bramy sieci VPN, zobacz [Tworzenie połączenia lokacja-lokacja](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md).

>[!NOTE]
>Aby utworzyć tę konfigurację, nie trzeba używać szablonów usługi Azure Resource Manager. Tę konfigurację można utworzyć za pomocą witryny Azure portal lub programu PowerShell.
>
>

### <a name="31-declare-the-variables"></a><a name="variables3"></a>3.1 Deklarowanie zmiennych

W tym przykładzie deklaracje zmiennych odpowiadają przykładowej sieci. Podczas deklarowania zmiennych zmodyfikuj tę sekcję, aby odzwierciedlić środowisko.

* Zmienna **localAddressPrefix** jest tablicą lokalnych adresów IP, aby zakończyć tunele IPsec.
* **GatewaySku** określa przepustowość sieci VPN. Aby uzyskać więcej informacji na temat gatewaySku i vpnType, zobacz [ustawienia konfiguracji bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md#gwsku). Aby uzyskać informacje na ten sposób, zobacz [Cennik bramy sieci VPN](https://azure.microsoft.com/pricing/details/vpn-gateway).
* Ustaw **vpnType** na **RouteBased**.

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

### <a name="32-create-virtual-network-vnet"></a><a name="vnet"></a>3.2 Tworzenie sieci wirtualnej (VNet)

Jeśli kojarzysz istniejącą sieć wirtualną z tunelami sieci VPN, możesz pominąć ten krok.

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

### <a name="33-assign-public-ip-addresses-to-vpn-gateway-instances"></a><a name="ip"></a>3.3 Przypisywanie publicznych adresów IP do wystąpień bramy sieci VPN
 
Przypisywanie publicznego adresu IP dla każdego wystąpienia bramy sieci VPN.

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

### <a name="34-specify-the-on-premises-vpn-tunnel-termination-local-network-gateway"></a><a name="termination"></a>3.4 Określanie lokalnego zakończenia tunelu sieci VPN (brama sieci lokalnej)

Lokalne urządzenia sieci VPN są nazywane **bramą sieci lokalnej**. Poniższy fragment kodu json określa również zdalne szczegóły elementu równorzędnego BGP:

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

### <a name="35-create-the-vpn-gateway"></a><a name="creategw"></a>3.5 Tworzenie bramy sieci VPN

Ta sekcja szablonu konfiguruje bramę sieci VPN z wymaganymi ustawieniami dla konfiguracji aktywny-aktywny. Należy pamiętać o następujących wymaganiach:

* Utwórz bramę sieci VPN z **"RouteBased"** VpnType. To ustawienie jest obowiązkowe, jeśli chcesz włączyć routing BGP między bramą sieci VPN a siecią VPN lokalnie.
* Aby ustanowić tunele sieci VPN między dwoma wystąpieniami bramy sieci VPN a danym urządzeniem lokalnym w trybie aktywnym i aktywnym, parametr **"activeActive"** jest ustawiony na **true** w szablonie Menedżera zasobów. Aby dowiedzieć się więcej o wysoce dostępnych bramach sieci VPN, zobacz [Łączność bramy sieci VPN o wysokiej dostępności](../vpn-gateway/vpn-gateway-highlyavailable.md).
* Aby skonfigurować sesje eBGP między tunelami sieci VPN, należy określić dwa różne sieci ASN po obu stronach. Zaleca się określenie prywatnych numerów ASN. Aby uzyskać więcej informacji, zobacz [Omówienie bram sieci VPN BGP i platformy Azure](../vpn-gateway/vpn-gateway-bgp-overview.md).

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

### <a name="36-establish-the-ipsec-tunnels"></a><a name="ipsectunnel"></a>3.6 Ustanowienie tuneli IPsec

Ostateczna akcja skryptu tworzy tunele IPsec między bramą sieci VPN platformy Azure a lokalnym urządzeniem sieci VPN.

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

## <a name="4-configure-the-on-premises-vpn-device"></a><a name="device"></a>4. Konfigurowanie lokalnego urządzenia sieci VPN

Brama sieci VPN platformy Azure jest zgodna z wieloma urządzeniami sieci VPN różnych dostawców. Aby uzyskać informacje o konfiguracji i urządzenia, które zostały zweryfikowane do pracy z bramą sieci VPN, zobacz [Informacje o urządzeniach sieci VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md).

Podczas konfigurowania urządzenia sieci VPN potrzebne są następujące elementy:

* Klucz współużytkowany. Jest to ten sam klucz udostępniony, który można określić podczas tworzenia połączenia sieci VPN lokacja-lokacja. W przykładach użyto podstawowego klucza udostępnionego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
* Publiczny adres IP bramy sieci VPN. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN za pomocą witryny Azure Portal, przejdź do pozycji Bramy sieci wirtualnej, a następnie kliknij nazwę bramy.

Zazwyczaj elementy równorzędne eBGP są bezpośrednio połączone (często za pomocą połączenia WAN). Jednak podczas konfigurowania eBGP za pośrednictwem tuneli sieci VPN IPsec za pośrednictwem usługi ExpressRoute Microsoft komunikacji równorzędnej istnieje wiele domen routingu między elementami równorzędnymi eBGP. Użyj polecenia **ebgp-multihop,** aby ustanowić relację sąsiada eBGP między dwoma niesiemie bezpośrednio połączonymi elementówami równorzędnymi. Liczba całkowita następuje po poleceniu ebgp-multihop określa wartość TTL w pakietach BGP. Polecenie **maksymalna ścieżka eibgp 2** umożliwia równoważenie obciążenia ruchu między dwiema ścieżkami BGP.

### <a name="cisco-csr1000-example"></a><a name="cisco1"></a>Przykład Cisco CSR1000

W poniższym przykładzie przedstawiono konfigurację urządzenia Cisco CSR1000 na maszynie wirtualnej funkcji Hyper-V jako lokalne urządzenie sieci VPN:

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

## <a name="5-configure-vpn-device-filtering-and-firewalls-optional"></a><a name="firewalls"></a>5. Konfigurowanie filtrowania urządzeń sieci VPN i zapór sieciowych (opcjonalnie)

Skonfiguruj zaporę i filtrowanie zgodnie z wymaganiami.

## <a name="6-test-and-validate-the-ipsec-tunnel"></a><a name="testipsec"></a>6. Przetestuj i waliduj tunel IPsec

Stan tuneli IPsec można zweryfikować na bramie sieci VPN platformy Azure za pomocą poleceń programu Powershell:

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayConnection -Name vpn2local1 -ResourceGroupName myRG | Select-Object  ConnectionStatus,EgressBytesTransferred,IngressBytesTransferred | fl
```

Przykładowe dane wyjściowe:

```azurepowershell
ConnectionStatus        : Connected
EgressBytesTransferred  : 17734660
IngressBytesTransferred : 10538211
```

Aby niezależnie sprawdzić stan tuneli w wystąpieniach bramy sieci Vpn platformy Azure, użyj następującego przykładu:

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

Stan tunelu można również sprawdzić na lokalnym urządzeniu sieci VPN.

Przykład Cisco CSR1000:

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

Protokół liniowy w interfejsie wirtualnego tunelu (VTI) nie zmienia się na "up", dopóki faza 2 IKE nie zostanie ukończona. Następujące polecenie weryfikuje skojarzenie zabezpieczeń:

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

### <a name="verify-end-to-end-connectivity-between-the-inside-network-on-premises-and-the-azure-vnet"></a><a name="verifye2e"></a>Weryfikowanie łączności end-to-end między siecią lokalną a siecią wirtualną platformy Azure

Jeśli tunele IPsec są w górę, a trasy statyczne są poprawnie ustawione, powinieneś mieć możliwość pingowania adresu IP zdalnego elementu równorzędnego BGP:

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

### <a name="verify-the-bgp-sessions-over-ipsec"></a><a name="verifybgp"></a>Weryfikowanie sesji Protokołu BGP za sprawą protokołu IPsec

W bramie sieci VPN platformy Azure sprawdź stan elementu równorzędnego BGP:

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

Aby zweryfikować listę prefiksów sieci otrzymanych za pośrednictwem protokołu eBGP z koncentratora sieci VPN lokalnie, można filtrować według atrybutu "Origin":

```azurepowershell-interactive
Get-AzVirtualNetworkGatewayLearnedRoute -VirtualNetworkGatewayName vpnGtw -ResourceGroupName myRG  | Where-Object Origin -eq "EBgp" |ft
```

W przykładowym wyjściu ASN 65010 jest numerem autonomicznego systemu BGP w sieci VPN w środowisku lokalnym.

```azurepowershell
AsPath LocalAddress Network      NextHop     Origin SourcePeer  Weight
------ ------------ -------      -------     ------ ----------  ------
65010  10.2.0.228   10.1.10.0/25 172.16.0.10 EBgp   172.16.0.10  32768
65010  10.2.0.228   10.0.0.0/24  172.16.0.10 EBgp   172.16.0.10  32768
```

Aby wyświetlić listę reklamowanych tras:

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

Przykład dla lokalnego cisco CSR1000:

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

Lista sieci anonsowanych z lokalnego urządzenia Cisco CSR1000 do bramy sieci VPN platformy Azure może być wymieniona za pomocą następującego polecenia:

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

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](how-to-npm.md)

* [Dodawanie połączenia lokacja lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
