---
title: 'Konfigurowanie protokołu BGP dla bramy sieci VPN platformy Azure: Resource Manager i interfejsu wiersza polecenia | Dokumentacja firmy Microsoft'
description: W tym artykule przedstawiono konfigurowanie protokołu BGP z bramy sieci VPN platformy Azure przy użyciu usługi Azure Resource Manager i interfejsu wiersza polecenia.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: cherylmc
ms.openlocfilehash: 98cd606ce930624ec5c591ffd8f13e0feae1a6c4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
ms.locfileid: "23884041"
---
# <a name="how-to-configure-bgp-on-an-azure-vpn-gateway-by-using-cli"></a>Jak można skonfigurować protokołu BGP dla bramy sieci VPN platformy Azure przy użyciu interfejsu wiersza polecenia

Ten artykuł pomaga włączyć protokół BGP dla połączenia sieci VPN typu lokacja-lokacja (S2S) między lokalizacjami i połączenia do wirtualnymi (to znaczy połączenia między sieciami wirtualnymi) przy użyciu modelu wdrażania usługi Azure Resource Manager i wiersza polecenia platformy Azure.

## <a name="about-bgp"></a>BGP — informacje

BGP to standardowy protokół routingu najczęściej używanych w Internecie do wymiany informacji routingu i uzyskiwanie między dwie lub więcej sieci. Protokół BGP umożliwia bramy sieci VPN i lokalnymi urządzeniami sieci VPN, nazywane elementów równorzędnych BGP lub sąsiadów, w celu wymiany tras. Trasy informować obu bram o dostępności i uzyskiwanie dla prefiksów przechodzić przez bramy lub routery związane. Protokół BGP można także włączyć routing tranzytowy między wieloma sieciami przez propagowania tras, na których bramy BGP uczy się z jednego elementu równorzędnego protokołu BGP, wszystkie inne elementy równorzędne BGP.

Aby uzyskać więcej informacji o zaletach protokołu BGP oraz zrozumieniu zagadnień dotyczących używania protokołu BGP i wymagania techniczne, zobacz [Omówienie protokołu BGP z bramy sieci VPN platformy Azure](vpn-gateway-bgp-overview.md).

Ten artykuł ułatwia wykonywanie następujących zadań:

* [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) (wymagane)

  Można wykonać jedną z następujących sekcji lub obie:

* [Nawiąż połączenie między różnymi lokalizacjami, z protokołem BGP](#crossprembgp)
* [Ustanów połączenie sieci wirtualnej do sieci wirtualnej z protokołem BGP](#v2vbgp)

Każda z tych trzech części formularzy podstawowego bloku konstrukcyjnego włączenia protokołu BGP w połączenie sieciowe. Po ukończeniu wszystkich trzech sekcjach kompilacji topologii jak pokazano na poniższym diagramie:

![Topologii protokołu BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Można łączyć w tych sekcjach, aby utworzyć bardziej złożonych sieci tranzytowej wieloskokowych, który nie spełnia wymagań.

## <a name ="enablebgp"></a>Włącz protokół BGP dla bramy sieci VPN

Ta sekcja jest wymagana przed wykonaniem kroków w innych częściach dwóch konfiguracji. Następujące kroki konfiguracji Ustaw parametry BGP bramy sieci VPN platformy Azure, jak pokazano na poniższym diagramie:

![Protokół BGP bramy](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Przed rozpoczęciem

Zainstaluj najnowszą wersję polecenia interfejsu wiersza polecenia (2.0 lub nowszej). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-azure-cli) i [Rozpoczynanie pracy z interfejsem wiersza polecenia platformy Azure 2.0](/cli/azure/get-started-with-azure-cli).

### <a name="step-1-create-and-configure-testvnet1"></a>Krok 1: Tworzenie i konfigurowanie TestVNet1

#### <a name="Login"></a>1. Nawiązywanie połączenia z subskrypcją

[!INCLUDE [CLI login](../../includes/vpn-gateway-cli-login-include.md)]

#### <a name="2-create-a-resource-group"></a>2. Tworzenie grupy zasobów

Poniższy przykład tworzy grupę zasobów o nazwie TestRG1 w lokalizacji "eastus". Jeśli masz już grupę zasobów w regionie, w którym chcesz utworzyć sieć wirtualną, można użyć niż jeden.

```azurecli
az group create --name TestBGPRG1 --location eastus
```

#### <a name="3-create-testvnet1"></a>3. Utworzenie sieci TestVNet1

Poniższy przykład tworzy sieć wirtualną o nazwie TestVNet1 i trzy podsieci: GatewaySubnet frontonu i wewnętrznej bazy danych. W przypadku zastępując wartości, ważne jest zawsze nazwę podsieci bramy w szczególności GatewaySubnet. W przypadku nadania jej innej nazwy proces tworzenia bramy zakończy się niepowodzeniem.

Pierwsze polecenie tworzy przestrzeń adresowa frontonu i podsieci frontonu. Drugie polecenie tworzy dodatkową przestrzeń adresów w podsieci wewnętrznej bazy danych. Trzeci i czwarty polecenia Utwórz podsieci wewnętrznej bazy danych i GatewaySubnet.

```azurecli
az network vnet create -n TestVNet1 -g TestBGPRG1 --address-prefix 10.11.0.0/16 -l eastus --subnet-name FrontEnd --subnet-prefix 10.11.0.0/24 
 
az network vnet update -n TestVNet1 --address-prefixes 10.11.0.0/16 10.12.0.0/16 -g TestBGPRG1 
 
az network vnet subnet create --vnet-name TestVNet1 -n BackEnd -g TestBGPRG1 --address-prefix 10.12.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet1 -n GatewaySubnet -g TestBGPRG1 --address-prefix 10.12.255.0/27 
```

### <a name="step-2-create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Krok 2: Tworzenie bramy sieci VPN dla TestVNet1 z parametrami BGP

#### <a name="1-create-the-public-ip-address"></a>1. Utwórz publiczny adres IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostaną przydzielone do bramy sieci VPN, utworzony dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP -g TestBGPRG1 --allocation-method Dynamic 
```

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Tworzenie bramy sieci VPN z numerem AS

Utwórz bramę sieci wirtualnej dla sieci TestVNet1. Protokół BGP wymaga bramy sieci VPN opartej na trasach. Należy również dodatkowy parametr `-Asn` można ustawić dla TestVNet1 numer systemu autonomicznego (ASN). Tworzenie bramy może trochę potrwać (45 minut lub więcej) do wykonania. 

Po uruchomieniu tego polecenia przy użyciu `--no-wait` parametru nie widać żadnych opinie i danych wyjściowych. `--no-wait` Parametr umożliwia bramy do utworzenia w tle. Nie oznacza to od razu utworzyć bramę sieci VPN.

```azurecli
az network vnet-gateway create -n VNet1GW -l eastus --public-ip-address GWPubIP -g TestBGPRG1 --vnet TestVNet1 --gateway-type Vpn --sku HighPerformance --vpn-type RouteBased --asn 65010 --no-wait
```

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. Uzyskaj adres IP elementu równorzędnego BGP platformy Azure

Po utworzeniu bramy, musisz uzyskać elementu równorzędnego protokołu BGP adres IP dla bramy sieci VPN platformy Azure. Ten adres jest potrzebne do skonfigurowania bramy sieci VPN jako element równorzędny BGP dla lokalnego urządzenia sieci VPN.

Uruchom następujące polecenie, a następnie sprawdź `bgpSettings` sekcji w górnej części danych wyjściowych:

```azurecli
az network vnet-gateway list -g TestBGPRG1 
 
  
"bgpSettings": { 
      "asn": 65010, 
      "bgpPeeringAddress": "10.12.255.30", 
      "peerWeight": 0 
    }
```

Po utworzeniu bramy służy tej bramy do nawiązywania połączenia między lokalizacjami lub wirtualnymi do połączenia z protokołem BGP.

## <a name ="crossprembgp"></a>Nawiąż połączenie między różnymi lokalizacjami, z protokołem BGP

Nawiązanie połączenia między różnymi lokalizacjami, należy utworzyć bramę sieci lokalnej do reprezentowania lokalnego urządzenia sieci VPN. Następnie należy połączyć bramy sieci VPN platformy Azure z bramy sieci lokalnej. Chociaż te kroki są podobne do tworzenia innych połączeń, zawierają dodatkowe właściwości, które są wymagane, aby określić parametry konfiguracji protokołu BGP.

![Protokół BGP dla między lokalizacjami](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)


### <a name="step-1-create-and-configure-the-local-network-gateway"></a>Krok 1: Tworzenie i konfigurowanie bramy sieci lokalnej

Tego ćwiczenia w dalszym ciągu przy tworzeniu konfiguracji przedstawione na diagramie. Należy pamiętać o zastąpieniu przykładowych wartości tymi, które mają zostać użyte w danej konfiguracji. Podczas pracy z bramy sieci lokalnej, należy przestrzegać następujących czynności:

* Brama sieci lokalnej mogą znajdować się w tej samej lokalizacji i grupy zasobów jako brama VPN, lub można ją w innej lokalizacji i grupy zasobów. Ten przykład przedstawia bramy w różnych grupach zasobów w różnych lokalizacjach.
* Minimalna prefiks, który musisz zadeklarować dla bramy sieci lokalnej jest adres hosta swój adres IP elementu równorzędnego protokołu BGP na urządzeniu sieci VPN. W takim przypadku jest /32 prefiks 10.52.255.254/32.
* Dla przypomnienia należy użyć różne numery ASN BGP między sieci lokalnej i sieć wirtualna platformy Azure. Jeśli są one takie same, należy zmienić numer ASN Twojej sieci wirtualnej, jeśli lokalnego urządzenia sieci VPN już umożliwia ASN elementów równorzędnych z innych sąsiadów BGP.

Przed kontynuowaniem upewnij się, że zostały wykonane [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) sekcji tego ćwiczenia i że istnieje połączenie 1 subskrypcji. Zwróć uwagę, że w tym przykładzie, Utwórz nową grupę zasobów. Ponadto dwóch dodatkowych parametrów dla bramy sieci lokalnej: `Asn` i `BgpPeerAddress`.

```azurecli
az group create -n TestBGPRG5 -l eastus2 
 
az network local-gateway create --gateway-ip-address 23.99.221.164 -n Site5 -g TestBGPRG5 --local-address-prefixes 10.51.255.254/32 --asn 65050 --bgp-peering-address 10.51.255.254
```

### <a name="step-2-connect-the-vnet-gateway-and-local-network-gateway"></a>Krok 2: Łączenie bramy sieci wirtualnej i Brama sieci lokalnej

W tym kroku należy utworzyć połączenie z TestVNet1 do Site5. Należy określić `--enable-bgp` parametr, aby włączyć protokół BGP dla tego połączenia. 

W tym przykładzie Brama sieci wirtualnej i Brama sieci lokalnej znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić identyfikator zasobu cały dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi.

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz identyfikator VNet1GW zasobu

Użyj dane wyjściowe z następującego polecenia, aby uzyskać identyfikator zasobu dla VNet1GW:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

W danych wyjściowych, Znajdź `"id":` wiersza. Należy wartości w cudzysłowie, aby utworzyć połączenie w następnej sekcji.

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

Skopiuj wartości po `"id":` do edytora tekstów, takiego jak Notatnik, dzięki czemu można łatwo je wklejać podczas tworzenia połączenia. 

```
"id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
```

#### <a name="2-get-the-resource-id-of-site5"></a>2. Pobierz identyfikator Site5 zasobu

Aby uzyskać identyfikator Site5 zasobu z danych wyjściowych, użyj następującego polecenia:

```azurecli
az network local-gateway show -n Site5 -g TestBGPRG5
```

#### <a name="3-create-the-testvnet1-to-site5-connection"></a>3. Utwórz połączenie TestVNet1-Site5

W tym kroku należy utworzyć połączenie z TestVNet1 do Site5. Jak już wspomniano, istnieje możliwość połączenia zarówno protokołu BGP, jak i z systemem innym niż BGP dla tej samej bramy sieci VPN platformy Azure. Chyba, że Protokół BGP jest włączony we właściwości połączenia, Azure nie włączyć protokół BGP dla tego połączenia, nawet jeśli parametry protokołu BGP jest już skonfigurowane na obu bram. Zamień na subskrypcję, identyfikatory własne.

```azurecli
az network vpn-connection create -n VNet1ToSite5 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "abc123" --local-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG5/providers/Microsoft.Network/localNetworkGateways/Site5 --no-wait
```

W tym ćwiczeniu poniższy przykład zawiera listę parametrów wprowadzenia w sekcji konfiguracji protokołu BGP lokalnego urządzenia sieci VPN:

```
Site5 ASN            : 65050
Site5 BGP IP         : 10.52.255.254
Prefixes to announce : (for example) 10.51.0.0/16 and 10.52.0.0/16
Azure VNet ASN       : 65010
Azure VNet BGP IP    : 10.12.255.30
Static route         : Add a route for 10.12.255.30/32, with nexthop being the VPN tunnel interface on your device
eBGP Multihop        : Ensure the "multihop" option for eBGP is enabled on your device if needed
```

Po kilku minutach połączenie powinno zostać ustanowione. Sesja komunikacji równorzędnej BGP rozpoczyna się po nawiązaniu połączenia protokołu IPsec.

## <a name ="v2vbgp"></a>Ustanów połączenie sieci wirtualnej do sieci wirtualnej z protokołem BGP

W tej sekcji dodaje połączenie do wirtualnymi z obsługą protokołu BGP, jak pokazano na poniższym diagramie: 

![Protokół BGP dla sieci wirtualnej do sieci wirtualnej](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Poniższe instrukcje kontynuować w poprzednich sekcjach. Aby utworzyć i skonfigurować TestVNet1 i bramy sieci VPN z protokołem BGP, należy wykonać [Włącz protokół BGP dla bramy sieci VPN](#enablebgp) sekcji.

### <a name="step-1-create-testvnet2-and-the-vpn-gateway"></a>Krok 1: Tworzenie TestVNet2 i bramy sieci VPN

Należy się upewnić, że przestrzeń adresową nową sieć wirtualną TestVNet2, nie mogą się pokrywać ze wszystkimi zakresy sieci wirtualnej.

W tym przykładzie sieci wirtualne należą do tej samej subskrypcji. Można skonfigurować do wirtualnymi połączeń między różnych subskrypcji. Aby dowiedzieć się więcej, zobacz [skonfigurować połączenia do wirtualnymi](vpn-gateway-howto-vnet-vnet-cli.md). Upewnij się, że możesz dodać `-EnableBgp $True` podczas tworzenia połączenia, aby włączyć protokół BGP.

#### <a name="1-create-a-new-resource-group"></a>1. Utworzenie nowej grupy zasobów

```azurecli
az group create -n TestBGPRG2 -l westus
```

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Utwórz TestVNet2 w nowej grupy zasobów

Pierwsze polecenie tworzy przestrzeń adresowa frontonu i podsieci frontonu. Drugie polecenie tworzy dodatkową przestrzeń adresów w podsieci wewnętrznej bazy danych. Trzeci i czwarty polecenia Utwórz podsieci wewnętrznej bazy danych i GatewaySubnet.

```azurecli
az network vnet create -n TestVNet2 -g TestBGPRG2 --address-prefix 10.21.0.0/16 -l westus --subnet-name FrontEnd --subnet-prefix 10.21.0.0/24 
 
az network vnet update -n TestVNet2 --address-prefixes 10.21.0.0/16 10.22.0.0/16 -g TestBGPRG2 
 
az network vnet subnet create --vnet-name TestVNet2 -n BackEnd -g TestBGPRG2 --address-prefix 10.22.0.0/24 
 
az network vnet subnet create --vnet-name TestVNet2 -n GatewaySubnet -g TestBGPRG2 --address-prefix 10.22.255.0/27
```

#### <a name="3-create-the-public-ip-address"></a>3. Utwórz publiczny adres IP

Prześlij żądanie dotyczące publicznego adresu IP. Publiczny adres IP zostaną przydzielone do bramy sieci VPN, utworzony dla sieci wirtualnej.

```azurecli
az network public-ip create -n GWPubIP2 -g TestBGPRG2 --allocation-method Dynamic
```

#### <a name="4-create-the-vpn-gateway-with-the-as-number"></a>4. Tworzenie bramy sieci VPN z numerem AS

Tworzenie bramy sieci wirtualnej dla TestVNet2. Należy zmienić domyślny numer ASN w Twojej bramy sieci VPN platformy Azure. Numery ASN dla sieci wirtualnych połączonych musi być inna, aby włączyć protokół BGP i routing tranzytowy.
 
```azurecli
az network vnet-gateway create -n VNet2GW -l westus --public-ip-address GWPubIP2 -g TestBGPRG2 --vnet TestVNet2 --gateway-type Vpn --sku Standard --vpn-type RouteBased --asn 65020 --no-wait
```

### <a name="step-2-connect-the-testvnet1-and-testvnet2-gateways"></a>Krok 2: Łączenie bram TestVNet1 i TestVNet2

W tym kroku należy utworzyć połączenie z TestVNet1 do Site5. Aby włączyć protokół BGP dla tego połączenia, należy określić `--enable-bgp` parametru.

W poniższym przykładzie Brama sieci wirtualnej i Brama sieci lokalnej znajdują się w różnych grupach zasobów. Gdy bramy znajdują się w różnych grupach zasobów, należy określić identyfikator zasobu cały dwóch bram, aby skonfigurować połączenie między sieciami wirtualnymi. 

#### <a name="1-get-the-resource-id-of-vnet1gw"></a>1. Pobierz identyfikator VNet1GW zasobu 

Pobierz identyfikator VNet1GW zasobu z danych wyjściowych następujące polecenie:

```azurecli
az network vnet-gateway show -n VNet1GW -g TestBGPRG1
```

#### <a name="2-get-the-resource-id-of-vnet2gw"></a>2. Pobierz identyfikator VNet2GW zasobu

Pobierz identyfikator VNet2GW zasobu z danych wyjściowych następujące polecenie:

```azurecli
az network vnet-gateway show -n VNet2GW -g TestBGPRG2
```

#### <a name="3-create-the-connections"></a>3. Tworzenie połączeń

Utwórz połączenie z TestVNet1 TestVNet2 i połączenie z TestVNet2 TestVNet1. Zamień na subskrypcję, identyfikatory własne.

```azurecli
az network vpn-connection create -n VNet1ToVNet2 -g TestBGPRG1 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW --enable-bgp -l eastus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW
```

```azurecli
az network vpn-connection create -n VNet2ToVNet1 -g TestBGPRG2 --vnet-gateway1 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG2/providers/Microsoft.Network/virtualNetworkGateways/VNet2GW --enable-bgp -l westus --shared-key "efg456" --vnet-gateway2 /subscriptions/<subscription ID>/resourceGroups/TestBGPRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW
```

> [!IMPORTANT]
> Włącz protokół BGP dla *zarówno* połączenia.
> 
> 

Po wykonaniu tych kroków, połączenie zostanie nawiązane za kilka minut. Sesja komunikacji równorzędnej BGP będą się po zakończeniu połączenia do wirtualnymi.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu połączenia można dodać maszyn wirtualnych do sieci wirtualne. Aby uzyskać instrukcje, zobacz [Utwórz maszynę wirtualną](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
