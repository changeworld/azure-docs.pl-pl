---
title: Ustawienia bramy sieci VPN dla stosu Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat ustawień dla bram sieci VPN, używanych w usłudze Azure stosu.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: fa8d3adc-8f5a-4b4f-8227-4381cf952c56
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2018
ms.author: brenduns
ms.openlocfilehash: b732770b2eace07690d112e81c6916b16b2cb5b0
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2018
---
# <a name="vpn-gateway-configuration-settings-for-azure-stack"></a>Ustawienia konfiguracji bramy sieci VPN Azure stosu

*Dotyczy: Azure stosu zintegrowanych systemów i Azure stosu Development Kit*

Brama sieci VPN jest typu bramy sieci wirtualnej, która wysyła szyfrowanego ruchu sieciowego między sieci wirtualnej Azure stosu i zdalnego bramy sieci VPN. Zdalne bramy sieci VPN może być na platformie Azure, urządzenie w centrum danych lub urządzeń w innej lokacji.  Jeśli istnieje połączenie sieciowe między dwoma punktami końcowymi, można ustanowić bezpiecznego połączenia między dwiema sieciami VPN lokacja-lokacja (S2S).

Połączenie bramy sieci VPN zależy od konfiguracji wielu zasobów, z których każdy zawiera konfigurowalnych ustawień. Informacje zawarte w tym artykule omówiono w nim, zasoby i ustawienia, które odnoszą się do bramy sieci VPN do sieci wirtualnej utworzonej w modelu wdrażania usługi Resource Manager. Możesz znaleźć opisy i diagramy topologii dla każdego rozwiązania połączenia w [o bramy sieci VPN Azure stosu](azure-stack-vpn-gateway-about-vpn-gateways.md).

## <a name="vpn-gateway-settings"></a>Ustawienia bramy sieci VPN

### <a name="gateway-types"></a>Typy bram
Każdej sieci wirtualnej Azure stosu obsługuje bramy jednej sieci wirtualnej, która musi być typu **Vpn**.  Ta obsługa różni się od Azure, która obsługuje dodatkowe typy.  

Podczas tworzenia bramy sieci wirtualnej, należy się upewnić, że typ bramy jest poprawny dla danej konfiguracji. Brama sieci VPN wymaga `-GatewayType Vpn`.

Przykład:
```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn
-VpnType RouteBased
```

### <a name="gateway-skus"></a>Jednostki SKU bramy
Podczas tworzenia bramy sieci wirtualnej musisz wybrać jednostkę SKU bramy do użycia. Wybierz jednostki SKU, które spełniają wymagania, na podstawie typów obciążeń, przepustowości, funkcji i umowy SLA.

>[!NOTE]
> Klasyczne sieci wirtualne powinny nadal używać starych jednostek SKU. Aby uzyskać więcej informacji o starych jednostkach SKU bramy, zobacz artykuł [Working with virtual network gateway SKUs (old)](/azure/vpn-gateway/vpn-gateway-about-skus-legacy) (Praca z jednostkami SKU (starymi) bramy sieci wirtualnej).

Stos Azure oferuje następujące jednostki SKU bramy sieci VPN:

|   | Przepływność bramy sieci VPN |Max tuneli protokołu IPsec bramy sieci VPN |
|-------|-------|-------|
|**Podstawowy SKU**  | 100 Mb/s  | 10    |
|**Standardowy SKU**           | 100 Mb/s  | 10    |
|**SKU wysokiej wydajności** | 200 Mb/s    | 30    |

### <a name="resizing-gateway-skus"></a>Zmiana rozmiaru jednostki SKU bramy
Stos Azure nie obsługuje zmiany rozmiaru jednostek magazynowych między obsługiwanych starszej wersji jednostki SKU.

Podobnie stos Azure nie obsługuje rozmiaru z obsługiwanych starszych jednostki SKU (podstawowa, standardowa i wysokowydajnej) do nowszej wersji produktu, obsługiwane przez platformę Azure (VpnGw1 VpnGw2 i VpnGw3).

### <a name="configure-the-gateway-sku"></a>Konfigurowanie bramy jednostki SKU
#### <a name="azure-stack-portal"></a>Portal Azure stosu
Jeśli używasz portalu Azure stosu Utwórz bramę sieci wirtualnej Menedżera zasobów za pomocą listy rozwijanej można wybrać jednostka SKU bramy. Dostępne są opcje odpowiadają typu bramy i wybranego typu sieci VPN.

#### <a name="powershell"></a>PowerShell
W poniższym przykładzie programu PowerShell określa GatewaySku — jako VpnGw1.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig -GatewaySku VpnGw1
-GatewayType Vpn -VpnType RouteBased
```
### <a name="connection-types"></a>Typy połączeń
W modelu wdrażania Menedżera zasobów każdej konfiguracji wymaga typu połączenia bramy określonej sieci wirtualnej. Dostępne wartości programu PowerShell Menedżera zasobów - ConnectionType są następujące:

- IPsec

W poniższym przykładzie programu PowerShell tworzone jest połączenie S2S wymagającego typ połączenia protokołu IPsec.  

```PowerShell
New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg
-Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local
-ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'
```

### <a name="vpn-types"></a>Typy sieci VPN
Podczas tworzenia bramy sieci wirtualnej dla konfiguracji bramy sieci VPN, należy określić typ sieci VPN. Możesz wybrać typ sieci VPN zależy od topologii połączenia, który chcesz utworzyć.  Typ sieci VPN można również są zależne od sprzętu, którego używasz. Konfiguracje S2S wymagają urządzenia sieci VPN. Niektóre urządzenia sieci VPN obsługują tylko określonego typu sieci VPN.

> [!IMPORTANT]  
> W tej chwili stosu Azure obsługuje tylko typ sieci VPN oparte na trasy. Jeśli urządzenie obsługuje tylko sieci VPN oparte na zasadach, połączenia z tych urządzeń z usługi Azure stosu nie są obsługiwane.

- **PolicyBased**: *(obsługiwane przez platformę Azure, ale nie przez stos Azure)* sieci VPN oparte na zasadach szyfrują i kierowania pakietów przez tunel protokołu IPsec na podstawie zasad protokołu IPsec, które są skonfigurowane przy użyciu kombinacji prefiksów adresów między siecią lokalną a stos sieci wirtualnej platformy Azure. Zasady (lub selektor ruchu) są zazwyczaj zdefiniowane jako lista dostępu w konfiguracji urządzenia sieci VPN.

- **RouteBased**: RouteBased VPN używają "tras" IP przekazywania lub tabeli routingu do kierowania pakietów do odpowiednich interfejsów tuneli. W dalszej kolejności interfejsy tuneli szyfrują lub odszyfrowują pakiety wchodzące do tuneli lub wychodzące z nich. Zasady (lub selektor ruchu) dla sieci VPN z siecią typu RouteBased są skonfigurowane jako dowolny z każdym (lub symbole wieloznaczne). Wartość dla typu RouteBased sieci VPN jest RouteBased.

W poniższym przykładzie programu PowerShell określa VpnType — jako RouteBased. Podczas tworzenia bramy musisz upewnić się, że typ -VpnType jest prawidłowy dla danej konfiguracji.

```PowerShell
New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
-Location 'West US' -IpConfigurations $gwipconfig
-GatewayType Vpn -VpnType RouteBased
```
### <a name="gateway-requirements"></a>Wymagania dotyczące bramy
W poniższej tabeli wymieniono wymagania dla bram sieci VPN.

| |PolicyBased Basic bramy sieci VPN | RouteBased Basic bramy sieci VPN | RouteBased Standard bramy sieci VPN | Brama sieci VPN RouteBased wysokiej wydajności|
|--|--|--|--|--|
| **Połączenie lokacja-lokacja (połączeń S2S)** | Nieobsługiwane | Konfiguracja sieci VPN z siecią typu RouteBased | Konfiguracja sieci VPN z siecią typu RouteBased | Konfiguracja sieci VPN z siecią typu RouteBased |
| **Metoda uwierzytelniania**  | Nieobsługiwane | Klucz wstępny dla połączeń S2S  | Klucz wstępny dla połączeń S2S  | Klucz wstępny dla połączeń S2S  |   
| **Maksymalna liczba połączeń S2S**  | Nieobsługiwane | 10 | 10| 30|
|**Aktywna Obsługa routingu (BGP)** | Nieobsługiwane | Nieobsługiwane | Obsługiwane | Obsługiwane |

### <a name="gateway-subnet"></a>Podsieć bramy
Przed utworzeniem bramy sieci VPN, należy utworzyć podsieć bramy. Podsieć bramy zawiera adresy IP, których bramy sieci wirtualnej maszyn wirtualnych i usług. Podczas tworzenia bramy sieci wirtualnej maszyny wirtualne bramy są wdrażane do podsieci bramy i skonfigurowane wymagane ustawienia bramy sieci VPN. Nie należy wdrażać cokolwiek innego (na przykład dodatkowe maszyny wirtualne) do podsieci bramy. Podsieć bramy musi być o nazwie "GatewaySubnet" działała poprawnie. Nazw podsieci bramy "GatewaySubnet" umożliwia stosu Azure do identyfikowania podsieci w celu wdrożenia bramy sieci wirtualnej maszyn wirtualnych i usług.

Podczas tworzenia podsieci bramy należy określić liczbę zawartych w niej adresów IP. Adresy IP w podsieci bramy są przydzielone do usługi bramy i maszyny wirtualne bramy. Niektóre konfiguracje wymagają większej liczby adresów IP niż inne. Przyjrzyj się instrukcje dotyczące konfiguracji, który chcesz utworzyć i sprawdź, czy podsieci bramy, w której chcesz utworzyć spełnia te wymagania. Można ponadto upewnij się, że podsieć bramy zawiera za mało adresów IP w celu uwzględnienia możliwych przyszłych dodatkowe konfiguracje. Podczas tworzenia podsieci bramy jak /29, zaleca się utworzenie podsieć bramy /28 i większych (/ 28, /27, /26 itp.). W ten sposób dodania funkcji w przyszłości, nie trzeba zerwanie bramy, a następnie usuń i Utwórz ponownie podsieć bramy, aby umożliwić więcej adresów IP.

W poniższym przykładzie programu PowerShell Menedżera zasobów zawiera podsieć bramy o nazwie GatewaySubnet. Można zauważyć, że w notacji CIDR określa /27, co umożliwia obsługę za mało adresów IP w przypadku większości konfiguracji, które obecnie istnieją.

```PowerShell
Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27
```

> [!IMPORTANT]
> Podczas pracy z podsieciami bramy należy unikać kojarzenia sieciowej grupy zabezpieczeń (NSG, Network Security Group) z podsiecią bramy. Skojarzenie sieciowej grupy zabezpieczeń z tą podsiecią spowoduje, że brama sieci VPN przestanie działać zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat grup zabezpieczeń sieci, zobacz [co to jest grupa zabezpieczeń sieci?](/azure/virtual-network/virtual-networks-nsg).

### <a name="local-network-gateways"></a>Bramy sieci lokalnej
Podczas tworzenia konfiguracji bramy sieci VPN platformy Azure, Brama sieci lokalnej często reprezentuje Twojej lokalizacji lokalnej. W stosie Azure reprezentuje wszystkie zdalnego urządzenia sieci VPN, który znajduje się poza stosu Azure.  Może to być urządzenia sieci VPN w centrum danych, zdalnego centrum danych lub bramy sieci VPN w systemie Azure.

Nadaj nazwę, publiczny adres IP urządzenia sieci VPN w bramie sieci lokalnej i określić prefiksy adresów, które znajdują się w lokalizacji lokalnej. Azure analizuje prefiksy adresów docelowy dla ruchu sieciowego, sprawdza konfigurację, którą określono dla bramy sieci lokalnej i odpowiednio kieruje pakiety.

W poniższym przykładzie programu PowerShell tworzy nową bramę sieci lokalnej:

```PowerShell
New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
-Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'
```
Czasami trzeba zmodyfikować ustawienia bramy sieci lokalnej. Na przykład podczas dodawania lub modyfikowania zakres adresów, lub jeśli zmieni adres IP urządzenia sieci VPN. Zobacz [zmodyfikować ustawienia bramy sieci lokalnej za pomocą programu PowerShell](/azure/vpn-gateway/vpn-gateway-modify-local-network-gateway).

## <a name="ipsecike-parameters"></a>Parametry IPsec i IKE
Po skonfigurowaniu połączenia sieci VPN w stosie Azure należy skonfigurować połączenie obu końców.  W przypadku konfigurowania połączenia VPN między stosu Azure i urządzenie, takie jak przełącznik lub router, który działa jako brama sieci VPN, czy urządzenie może zapytać o dodatkowe ustawienia.

W przeciwieństwie do usługi Azure, który obsługuje wielu ofert jako inicjator i obiekt odpowiadający, stos Azure obsługuje tylko jedną ofertę.

###  <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokołu IKE — faza 1 (tryb główny)
| Właściwość              | Wartość|
|-|-|
| Wersja IKE           | IKEv2 |
|Grupa Diffie’ego-Hellmana   | Grupa 2 (1024 bity) |
| Metoda uwierzytelniania | Klucz wstępny |
|Szyfrowanie i algorytmy wyznaczania wartości skrótu | AES256, SHA256 |
|Okres istnienia skojarzeń zabezpieczeń (czas)     | 28 800 sekund|

### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokołu IKE — faza 2 (tryb szybki)
| Właściwość| Wartość|
|-|-|
|Wersja IKE |IKEv2 |
|Szyfrowania i tworzenia skrótów algorytmy (szyfrowanie)     | GCMAES256|
|Szyfrowania i tworzenia skrótów algorytmy (uwierzytelnianie) | GCMAES256|
|Okres istnienia skojarzeń zabezpieczeń (czas)  | 27,700 sekund |
|Okres istnienia skojarzeń zabezpieczeń (bajty) | 819,200       |
|Doskonałe utajnienie przekazywania (PFS) |PFS2048 |
|Wykrywanie nieaktywnych elementów równorzędnych | Obsługiwane|  
