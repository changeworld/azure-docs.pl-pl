---
title: Konfigurowanie protokołu BGP w usłudze Azure VPN Gateway:CLI
description: W tym artykule okonywanie konfiguracji protokołu BGP przy użyciu bramy sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i interfejsu wiersza polecenia.
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 42a07ac00fd8a26918164f6547bf57c2b021d14c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75863618"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Jak skonfigurować usługę BGP w bramie sieci VPN platformy Azure przy użyciu interfejsu wiersza polecenia

Ten artykuł ułatwia włączenie protokołu BGP na międzylokacyjnym połączeniu sieci VPN lokacja lokacja (S2S) i połączeniu sieci wirtualnej z siecią wirtualną (czyli połączeniu między sieciami wirtualnymi) przy użyciu modelu wdrażania usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure.

## <a name="about-bgp"></a>BGP — informacje

Protokół BGP jest standardowym protokołem routingu powszechnie używanym w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub więcej sieciami. BGP umożliwia bramom sieci VPN i lokalnym urządzeniom sieci VPN, nazywanym odpowiednikami BGP lub sąsiadami, wymianę tras. Trasy informują obie bramy o dostępności i osiągalności prefiksów, aby przejść przez zaangażowane bramy lub routery. BGP można również włączyć routingu tranzytowego między wieloma sieciami, propagując trasy, których brama BGP uczy się od jednego elementu równorzędnego BGP do wszystkich innych elementów równorzędnych BGP.

Aby uzyskać więcej informacji na temat korzyści płynących z protokołu BGP oraz aby zrozumieć wymagania techniczne i zagadnienia związane z korzystaniem z protokołu BGP, zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure.](vpn-gateway-bgp-overview.md)

Ten artykuł pomaga w następujących zadaniach:

* [Włącz usługę BGP dla bramy sieci VPN](#enablebgp) (wymagane)

  Następnie można wykonać jedną z następujących sekcji lub obie te czynności:

* [Ustanawianie połączenia międzylokacowego z usługą BGP](#crossprembgp)
* [Ustanawianie połączenia sieci wirtualnej z siecią wirtualną z usługą BGP](#v2vbgp)

Każda z tych trzech sekcji stanowi podstawowy blok konstrukcyjny włączania protokołu BGP w łączności sieciowej. Jeśli ukończysz wszystkie trzy sekcje, tworzysz topologię, jak pokazano na poniższym diagramie:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Można połączyć te sekcje, aby zbudować bardziej złożoną sieć transportu multihop, która spełnia Twoje potrzeby.

## <a name="enable-bgp-for-your-vpn-gateway"></a><a name ="enablebgp"></a>Włączanie protokołu BGP dla bramy sieci VPN

Ta sekcja jest wymagana przed wykonaniem któregokolwiek z kroków w pozostałych dwóch sekcjach konfiguracji. Następujące kroki konfiguracji skonfigurować parametry Protokołu BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![Brama BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2.0 lub nowszych). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: Tworzenie i konfigurowanie Sieci TestVNet1

#### <a name="1-connect-to-your-subscription"></a><a name="Login"></a>1. Połącz się z subskrypcją

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Tworzenie grupy zasobów

Poniższy przykład tworzy grupę zasobów o nazwie TestRG1 w lokalizacji "eastus". Jeśli masz już grupę zasobów w regionie, w którym chcesz utworzyć sieć wirtualną, możesz użyć tej grupy.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Utwórz TestVNet1

Poniższy przykład tworzy sieć wirtualną o nazwie TestVNet1 i trzy podsieci: GatewaySubnet, FrontEnd i BackEnd. Podczas zastępowania wartości należy zawsze nadawać nazwę podsieci bramy w szczególności w sieci GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

Pierwsze polecenie tworzy przestrzeń adresową frontu i podsieć FrontEnd. Drugie polecenie tworzy dodatkową przestrzeń adresową dla podsieci Wewnętrznej bazy danych. Polecenia trzeciego i czwartego tworzą podsieć wewnętrznej bazy danych i sieć GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Tworzenie bramy sieci VPN dla TestVNet1 z parametrami BGP

#### <a name="1-create-the-public-ip-address"></a>1. Tworzenie publicznego adresu IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN utworzonej dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Utwórz bramę sieci VPN z numerem AS

Utwórz bramę sieci wirtualnej dla sieci TestVNet1. BGP wymaga bramy sieci VPN opartej na trasach. Potrzebny jest również `-Asn` dodatkowy parametr, aby ustawić numer systemu autonomicznego (ASN) dla TestVNet1. Utworzenie bramy może potrwać trochę czasu (45 minut lub więcej). 

Jeśli to polecenie zostanie `--no-wait` uruchomione przy użyciu parametru, nie zobaczysz żadnych opinii ani danych wyjściowych. Parametr `--no-wait` umożliwia utworzenie bramy w tle. Nie oznacza to, że brama sieci VPN jest tworzona natychmiast.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskiwanie równorzędnego adresu IP usługi Azure BGP

Po utworzeniu bramy należy uzyskać równorzędny adres IP protokołu BGP na bramie sieci VPN platformy Azure. Ten adres jest potrzebny do skonfigurowania bramy sieci VPN jako elementu równorzędnego BGP dla lokalnych urządzeń sieci VPN.

Uruchom następujące polecenie i `bgpSettings` sprawdź sekcję u góry danych wyjściowych:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po utworzeniu bramy można użyć tej bramy do ustanowienia połączenia międzylokalnościowego lub połączenia sieci wirtualnej z siecią wirtualną z usługą BGP.

## <a name="establish-a-cross-premises-connection-with-bgp"></a><a name ="crossprembgp"></a>Ustanawianie połączenia międzylokacowego z usługą BGP

Aby ustanowić połączenie międzylokacje, należy utworzyć bramę sieci lokalnej do reprezentowania lokalnego urządzenia sieci VPN. Następnie należy połączyć bramę sieci VPN platformy Azure z bramą sieci lokalnej. Mimo że te kroki są podobne do tworzenia innych połączeń, obejmują one dodatkowe właściwości wymagane do określenia parametrów konfiguracji BGP.

![BGP dla różnych pomieszczeń](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: Tworzenie i konfigurowanie bramy sieci lokalnej

To ćwiczenie kontynuuje tworzenie konfiguracji pokazanej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji. Podczas pracy z bramami sieci lokalnej należy pamiętać o następujących czynnościach:

* Brama sieci lokalnej może znajdować się w tej samej lokalizacji i grupie zasobów co brama sieci VPN lub w innej lokalizacji i grupie zasobów. W tym przykładzie przedstawiono bramy w różnych grupach zasobów w różnych lokalizacjach.
* Minimalny prefiks, który należy zadeklarować dla bramy sieci lokalnej, to adres hosta równorzędnego adresu IP protokołu BGP na urządzeniu sieci VPN. W takim przypadku jest to /32 prefiks 10.51.255.254/32.
* Przypominamy, że należy użyć różnych sieci ASN BGP między sieciami lokalnymi a siecią wirtualną platformy Azure. Jeśli są takie same, należy zmienić sieć wirtualną ASN, jeśli lokalne urządzenia sieci VPN już używają asn do komunikacji równorzędnej z innymi sąsiadami protokołu BGP.

Przed kontynuowaniem upewnij się, że ukończono sekcję [Włącz usługę BGP dla bramy sieci VPN](#enablebgp) w tym ćwiczeniu i że nadal masz połączenie z subskrypcją 1. Należy zauważyć, że w tym przykładzie utworzysz nową grupę zasobów. Zwróć również uwagę na dwa dodatkowe parametry `Asn` `BgpPeerAddress`bramy sieci lokalnej: i .

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: Podłącz bramę sieci wirtualnej i bramę sieci lokalnej

W tym kroku utworzysz połączenie z TestVNet1 do Site5. Należy określić `--enable-bgp` parametr, aby włączyć BGP dla tego połączenia. 

W tym przykładzie brama sieci wirtualnej i brama sieci lokalnej znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić identyfikator całego zasobu dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz identyfikator zasobu VNet1GW

Użyj danych wyjściowych z następującego polecenia, aby uzyskać identyfikator zasobu dla sieci wirtualnej1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

Na wyjściu znajdź `"id":` linię. Aby utworzyć połączenie w następnej sekcji, potrzebne są wartości w cudzysłowie.

Przykładowe dane wyjściowe:

```
{ 
  "activeActive": false, 
  "bgpSettings": { 
    "asn": 65010, 
    "bgpPeeringAddress": "10.12.255.30", 
    "peerWeight": 0 
  }, 
  "enableBgp": true, 
  "etag": "W/\"<your etag number>\"", 
  "gatewayDefaultSite": null, 
  "gatewayType": "Vpn", 
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
```

Skopiuj wartości do `"id":` edytora tekstu, takiego jak Notatnik, aby można było je łatwo wkleić podczas tworzenia połączenia. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Uzyskaj identyfikator zasobu site5

Użyj następującego polecenia, aby uzyskać identyfikator zasobu Site5 z danych wyjściowych:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Tworzenie połączenia TestVNet1-to-Site5

W tym kroku utworzysz połączenie z TestVNet1 do Site5. Jak wspomniano wcześniej, możliwe jest, aby mieć połączenia BGP i innych niż BGP dla tej samej bramy sieci VPN platformy Azure. Jeśli protokół BGP nie jest włączony we właściwości połączenia, platforma Azure nie włączy protokołu BGP dla tego połączenia, nawet jeśli parametry protokołu BGP są już skonfigurowane w obu bramkach. Zastąp identyfikatory subskrypcji własnymi.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

W tym ćwiczeniu w poniższym przykładzie wymieniono parametry, które należy wprowadzić w sekcji konfiguracji protokołu BGP lokalnego urządzenia sieci VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Po kilku minutach połączenie powinno zostać ustanowione. Sesja komunikacji równorzędnej BGP rozpoczyna się po nawiązaniu połączenia IPsec.

## <a name="establish-a-vnet-to-vnet-connection-with-bgp"></a><a name ="v2vbgp"></a>Ustanawianie połączenia sieci wirtualnej z siecią wirtualną z usługą BGP

W tej sekcji dodano połączenie sieci wirtualnej z siecią wirtualną z usługą BGP, jak pokazano na poniższym diagramie: 

![BGP dla sieci wirtualnej do sieci wirtualnej](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje są kontynuowane w krokach w poprzednich sekcjach. Aby utworzyć i skonfigurować testvnet1 i bramę sieci VPN za pomocą protokołu BGP, należy ukończyć sekcję [Włącz usługę BGP dla bramy sieci VPN.](#enablebgp)

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: Tworzenie usługi TestVNet2 i bramy sieci VPN

Ważne jest, aby upewnić się, że przestrzeń adresowa IP nowej sieci wirtualnej TestVNet2 nie pokrywa się z żadnym z zakresów sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia sieci wirtualnej z siecią wirtualną między różnymi subskrypcjami. Aby dowiedzieć się więcej, zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną](vpn-gateway-howto-vnet-vnet-cli.md). Upewnij się, `-EnableBgp $True` że podczas tworzenia połączeń, aby włączyć BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Tworzenie nowej grupy zasobów

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

Pierwsze polecenie tworzy przestrzeń adresową frontu i podsieć FrontEnd. Drugie polecenie tworzy dodatkową przestrzeń adresową dla podsieci Wewnętrznej bazy danych. Polecenia trzeciego i czwartego tworzą podsieć wewnętrznej bazy danych i sieć GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Tworzenie publicznego adresu IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN utworzonej dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Utwórz bramę sieci VPN z numerem AS

Utwórz bramę sieci wirtualnej dla TestVNet2. Należy zastąpić domyślną sieć ASN w swoich bramach sieci VPN platformy Azure. Sieci ASN dla połączonych sieci wirtualnych muszą być różne, aby włączyć BGP i routing tranzytowy.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: Podłączanie bram TestVNet1 i TestVNet2

W tym kroku utworzysz połączenie z TestVNet1 do Site5. Aby włączyć usługę BGP dla tego `--enable-bgp` połączenia, należy określić parametr.

W poniższym przykładzie brama sieci wirtualnej i brama sieci lokalnej znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić identyfikator całego zasobu dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz identyfikator zasobu VNet1GW 

Pobierz identyfikator zasobu VNet1GW z danych wyjściowych następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Pobierz identyfikator zasobu VNet2GW

Pobierz identyfikator zasobu VNet2GW z danych wyjściowych następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Tworzenie połączeń

Utwórz połączenie z TestVNet1 do TestVNet2 i połączenie z TestVNet2 do TestVNet1. Zastąp identyfikatory subskrypcji własnymi.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Włącz protokołu BGP dla *obu* połączeń.
> 
> 

Po wykonaniu tych kroków połączenie zostanie nawiązane w ciągu kilku minut. Sesja komunikacji równorzędnej protokołu BGP będzie się po zakończeniu połączenia sieci wirtualnej z siecią wirtualną.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu połączenia można dodać maszyny wirtualne do sieci wirtualnych. Aby uzyskać instrukcje, zobacz [Tworzenie maszyny wirtualnej](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
