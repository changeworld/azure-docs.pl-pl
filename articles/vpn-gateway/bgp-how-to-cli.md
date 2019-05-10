---
title: 'Konfigurowanie protokołu BGP w bramie sieci VPN platformy Azure: Resource Manager i interfejsu wiersza polecenia | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono konfigurowanie protokołu BGP z bramą sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i interfejsu wiersza polecenia.
services: vpn-gateway
documentationcenter: na
author: yushwang
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/25/2018
ms.author: yushwang
ms.openlocfilehash: 51402196c8429797b644357822a1e3c08982b384
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65209504"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Jak skonfigurować protokół BGP w bramie sieci VPN platformy Azure przy użyciu interfejsu wiersza polecenia

Ten artykuł ułatwia włączyć protokół BGP dla połączenia sieci VPN typu lokacja-lokacja (S2S) między środowiskami lokalnymi i połączenia sieć wirtualna-sieć wirtualna (oznacza to, że połączenie między sieciami wirtualnymi) przy użyciu modelu wdrażania usługi Azure Resource Manager i interfejsu wiersza polecenia platformy Azure.

## <a name="about-bgp"></a>BGP — informacje

BGP to standardowy protokół routingu, powszechnie używanym w Internecie do wymiany informacji routingu i osiągalności między dwiema lub wieloma sieciami. Protokół BGP umożliwia bram sieci VPN i lokalnych urządzeń sieci VPN, o nazwie elementów równorzędnych protokołu BGP lub sąsiednimi, w celu wymiany tras. Trasy informuje obu bram o dostępności i osiągalności dla prefiksów za pośrednictwem bram lub routerów zaangażowane. Protokół BGP można także włączyć routing tranzytowy między wieloma sieciami poprzez propagowanie tras, na których brama BGP uczy się od jednego elementu równorzędnego BGP do wszystkich innych elementów równorzędnych protokołu BGP.

Aby uzyskać więcej informacji o korzyściach z użycia protokołu BGP i zrozumienie wymagań technicznych oraz kwestii związanych z przy użyciu protokołu BGP, zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure](vpn-gateway-bgp-overview.md).

Ten artykuł zawiera następujące zadania:

* [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) (wymagane)

  Następnie możesz wykonać jedną z następujących sekcji i / lub:

* [Ustanów połączenie między środowiskami lokalnymi za pomocą protokołu BGP](#crossprembgp)
* [Ustanów połączenie sieć wirtualna-sieć wirtualna za pomocą protokołu BGP](#v2vbgp)

Każda z tych trzech części stanowi podstawowy blok konstrukcyjny włączania protokołu BGP w łączność sieciową. Jeśli możesz wykonać wszystkie trzy sekcje, tworzysz topologii, jak pokazano na poniższym diagramie:

![Topologii protokołu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Można połączyć te sekcje do utworzenia bardziej złożonych sieci wieloskokowych przesyłania, który odpowiada Twoim potrzebom.

## <a name ="enablebgp"></a>Włącz protokół BGP dla bramy sieci VPN

Ta sekcja jest wymagana, przed wykonaniem kroków w innych częściach dwóch konfiguracji. Następujące kroki konfiguracji Skonfiguruj parametry protokołu BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![BGP gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2.0 lub nowszą). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: Tworzenie i konfigurowanie sieci TestVNet1

#### <a name="Login"></a>1. Nawiązywanie połączenia z subskrypcją

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Tworzenie grupy zasobów

Poniższy przykład tworzy grupę zasobów o nazwie TestRG1 w lokalizacji "eastus". Jeśli masz już grupę zasobów w regionie, w którym chcesz utworzyć sieć wirtualną, możesz zamiast tego użyj ten.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Utworzenie sieci TestVNet1

Poniższy przykład tworzy sieć wirtualną o nazwie TestVNet1 oraz trzy podsieci: Podsieci GatewaySubnet, FrontEnd i wewnętrznej bazy danych. W przypadku zastępowania wartości ważne jest zawsze nadaj nazwę podsieci bramy specjalnie GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

Pierwsze polecenie tworzy przestrzeń adresową frontonu i podsieć frontonu. Drugie polecenie tworzy dodatkową przestrzeń adresową podsieci wewnętrznej bazy danych. Trzecia i czwarta polecenia Utwórz podsieci wewnętrznej bazy danych i podsieć GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Tworzenie bramy sieci VPN dla sieci TestVNet1 z parametrami protokołu BGP

#### <a name="1-create-the-public-ip-address"></a>1. Tworzenie publicznego adresu IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN, które tworzysz dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Tworzenie bramy sieci VPN z numerem AS

Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Protokół BGP wymaga bramy sieci VPN opartej na trasach. Należy również dodatkowy parametr `-Asn` można ustawić numer systemu autonomicznego (ASN) dla sieci TestVNet1. Tworzenie bramy może wymagać trochę czasu (45 minut lub więcej) do wykonania. 

Po uruchomieniu tego polecenia przy użyciu `--no-wait` parametru nie widać żadnych informacji zwrotnych ani danych wyjściowych. `--no-wait` Parametr umożliwia bramy do utworzenia w tle. Nie oznacza to, że Brama VPN jest utworzona natychmiast.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskaj adres IP elementu równorzędnego BGP platformy Azure

Po utworzeniu bramy należy uzyskać elementu równorzędnego protokołu BGP z adresem IP bramy sieci VPN platformy Azure. Ten adres jest wymagane do skonfigurowania bramy sieci VPN, jako element równorzędny BGP dla lokalnych urządzeń sieci VPN.

Uruchom następujące polecenie i sprawdź `bgpSettings` sekcji w górnej części danych wyjściowych:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po utworzeniu bramy można użyć tej bramy można ustanowić połączenia między środowiskami lokalnymi lub połączenia sieć wirtualna-sieć wirtualna za pomocą protokołu BGP.

## <a name ="crossprembgp"></a>Ustanów połączenie między środowiskami lokalnymi za pomocą protokołu BGP

Nawiązanie połączenia między środowiskami lokalnymi, musisz utworzyć bramę sieci lokalnej do reprezentowania swoje lokalne urządzenie sieci VPN. Następnie połączenie z bramy Azure VPN gateway, za pomocą bramy sieci lokalnej. Mimo że te kroki są podobne do tworzenia innych połączeń, zawierają dodatkowe właściwości, które są wymagane, aby określić parametry konfiguracji protokołu BGP.

![Protokół BGP dla między środowiskami lokalnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: Tworzenie i konfigurowanie bramy sieci lokalnej

To ćwiczenie w dalszym ciągu kompilacji konfiguracji przedstawionej na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji. Podczas pracy z bramami sieci lokalnej, należy przestrzegać następujących czynności:

* Brama sieci lokalnej może być w tej samej lokalizacji i grupie zasobów co bramy sieci VPN lub może być w innej lokalizacji i grupie zasobów. Ten przykład przedstawia bramy w różnych grupach zasobów w różnych lokalizacjach.
* To minimalny prefiks, który trzeba zadeklarować dla bramy sieci lokalnej jest adres hosta użytkownika adres IP elementu równorzędnego protokołu BGP na twoim urządzeniu sieci VPN. W tym przypadku jest/32 prefiks 10.51.255.254/32.
* Przypominamy należy użyć różne numery ASN protokołu BGP między sieciami w środowisku lokalnym i sieci wirtualnej platformy Azure. Jeśli są takie same, należy zmienić numer ASN Twojej sieci wirtualnej, jeśli Twoje lokalne urządzenia sieci VPN już umożliwia numer ASN komunikacji równorzędnej z innych sąsiadów protokołu BGP.

Przed kontynuowaniem upewnij się, że Ukończono [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) części tego ćwiczenia i że nadal masz połączenia z subskrypcją 1. Należy zauważyć, że w tym przykładzie utworzysz nową grupę zasobów. Zauważ również, dwa dodatkowe parametry dla bramy sieci lokalnej: `Asn` i `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: Łączenie bramy sieci wirtualnej i bramy sieci lokalnej

W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do Site5. Należy określić `--enable-bgp` parametru, aby włączyć protokół BGP dla tego połączenia. 

W tym przykładzie Brama sieci wirtualnej i bramy sieci lokalnej są w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić identyfikator zasobu cały dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz zasób identyfikator VNet1GW

Aby uzyskać identyfikator zasobu bramy vnet1gw, należy użyć dane wyjściowe z następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

W danych wyjściowych, Znajdź `"id":` wiersza. Będą one potrzebne w cudzysłowie, aby utworzyć połączenie w następnej sekcji.

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

Skopiuj wartości po `"id":` do edytora tekstów, takiego jak Notatnik, dzięki czemu można je było łatwo wkleić podczas tworzenia połączenia. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Pobierz zasób identyfikator Site5

Użyj następującego polecenia, aby uzyskać identyfikator Site5 z danych wyjściowych:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Tworzenie połączenia sieci wirtualnej TestVNet1 do Site5

W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do Site5. Jak już wspomniano, istnieje możliwość połączenia protokołu BGP i protokołów innych niż BGP dla tej samej bramy sieci VPN platformy Azure. Chyba, że Protokół BGP jest włączony we właściwości połączenia, Azure nie włączyć protokół BGP dla tego połączenia, nawet jeśli Protokół BGP parametry zostały już skonfigurowane na obu bram. Zastąp identyfikator subskrypcji własnymi.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

Na potrzeby tego ćwiczenia poniższy przykład wyświetla listę parametrów, aby wprowadzić w sekcji konfiguracji protokołu BGP dla lokalnego urządzenia sieci VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Po kilku minutach połączenie powinno zostać ustanowione. Sesja komunikacji równorzędnej protokołu BGP rozpoczyna się po nawiązaniu połączenia IPsec.

## <a name ="v2vbgp"></a>Ustanów połączenie sieć wirtualna-sieć wirtualna za pomocą protokołu BGP

W tej sekcji dodaje połączenia sieć wirtualna-sieć wirtualna za pomocą protokołu BGP, jak pokazano na poniższym diagramie: 

![Protokół BGP dla sieci wirtualnej między sieciami wirtualnymi](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje są kontynuacją kroki opisane w poprzednich sekcjach. Aby utworzyć i skonfigurować sieć TestVNet1 i bramę sieci VPN z protokołem BGP, należy wykonać [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) sekcji.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: Tworzenie TestVNet2 i bramy sieci VPN

Należy się upewnić, że przestrzeń adresów IP nowej sieci wirtualnej TestVNet2, nie nakłada się z żadnym z zakresów w Twojej sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować połączenia sieć wirtualna-sieć wirtualna między różnymi subskrypcjami. Aby dowiedzieć się więcej, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](vpn-gateway-howto-vnet-vnet-cli.md). Upewnij się, że dodano `-EnableBgp $True` podczas tworzenia połączeń, aby włączyć protokół BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Utwórz nową grupę zasobów

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupie zasobów

Pierwsze polecenie tworzy przestrzeń adresową frontonu i podsieć frontonu. Drugie polecenie tworzy dodatkową przestrzeń adresową podsieci wewnętrznej bazy danych. Trzecia i czwarta polecenia Utwórz podsieci wewnętrznej bazy danych i podsieć GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Tworzenie publicznego adresu IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN, które tworzysz dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Tworzenie bramy sieci VPN z numerem AS

Tworzenie bramy sieci wirtualnej dla TestVNet2. Konieczne jest przesłonięcie domyślny numer ASN na bram sieci VPN platformy Azure. Numery ASN dla połączonych sieci wirtualnych musi być inna, aby włączyć protokół BGP i routing tranzytowy.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: Łączenie bramy sieci wirtualnej TestVNet1 i TestVNet2

W tym kroku zostanie utworzone połączenie z sieci wirtualnej TestVNet1 do Site5. Aby włączyć protokół BGP dla tego połączenia, należy określić `--enable-bgp` parametru.

W poniższym przykładzie bramy sieci wirtualnej i bramy sieci lokalnej są w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić identyfikator zasobu cały dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz zasób identyfikator VNet1GW 

Pobierz zasób identyfikator sieci VNet1GW z danych wyjściowych następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Pobierz zasób identyfikator VNet2GW

Pobierz identyfikator VNet2GW zasobu z danych wyjściowych następującego polecenia:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Tworzenie połączeń

Utwórz połączenie z sieci wirtualnej TestVNet1 TestVNet2 i połączenie z TestVNet2 do sieci TestVNet1. Zastąp identyfikator subskrypcji własnymi.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Włącz protokół BGP dla *zarówno* połączeń.
> 
> 

Po wykonaniu tych kroków, połączenie zostanie nawiązane za kilka minut. Sesja komunikacji równorzędnej protokołu BGP będą się po zakończeniu połączenia sieć wirtualna-sieć wirtualna.

## <a name="next-steps"></a>Kolejne kroki

Po zakończeniu połączenia można dodać maszynami wirtualnymi, sieciami wirtualnymi. Aby uzyskać instrukcje, zobacz [Utwórz maszynę wirtualną](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
