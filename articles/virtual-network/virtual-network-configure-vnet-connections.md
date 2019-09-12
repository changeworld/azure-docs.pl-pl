---
title: Konfigurowanie i weryfikowanie połączeń sieci wirtualnej lub sieci VPN
description: Wskazówki krok po kroku dotyczące konfigurowania i weryfikowania różnych wdrożeń sieci VPN i wirtualnych platformy Azure
services: virtual-network
documentationcenter: na
author: v-miegge
manager: dcscontentpm
editor: ''
ms.assetid: 0433a4f4-b5a0-476d-b398-1506c57eafa2
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2019
ms.author: kaushika
ms.openlocfilehash: fc4b649ce8d082d8d854c4c19b617c088ff3141c
ms.sourcegitcommit: 3e7646d60e0f3d68e4eff246b3c17711fb41eeda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70901895"
---
# <a name="configure-and-validate-vnet-or-vpn-connections"></a>Konfigurowanie i weryfikowanie połączeń sieci wirtualnej lub sieci VPN

Ten Przewodnik instruktażowy zawiera wskazówki krok po kroku dotyczące konfigurowania i weryfikowania różnych wdrożeń sieci VPN i wirtualnych platformy Azure w scenariuszach takich jak, routing tranzytowy, Sieć wirtualna-sieć wirtualna, protokół BGP, wiele lokacji, punkt-lokacja i tak dalej.

Bramy sieci VPN platformy Azure umożliwiają elastyczność rozmieszczenia niemal dowolnego rodzaju topologii połączonej Virtual Network (VNet) na platformie Azure: możesz łączyć sieci wirtualnych między regionami, między typami sieci wirtualnej (Azure Resource Manager a Klasyczny), w ramach platformy Azure lub lokalnego środowiska hybrydowego, w różnych subskrypcjach i tak dalej. 

## <a name="scenario-1-vnet-to-vnet-vpn-connection"></a>Scenariusz 1: Połączenie sieci VPN typu sieć wirtualna-sieć wirtualna

Połączenie sieci wirtualnej z inną siecią wirtualną (VNet-to-VNet) za pośrednictwem sieci VPN jest podobne do łączenia sieci wirtualnej z lokacją lokalną. Oba typy połączeń używają bramy sieci VPN, aby zapewnić bezpieczny tunel przy użyciu **protokołu IPSec/IKE**. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Rysunek 1. Sieć wirtualna-sieć wirtualna z protokołem IPsec

Jeśli Twoje sieci wirtualnych znajdują się w tym samym regionie, warto rozważyć połączenie ich za pomocą komunikacji równorzędnej w sieci wirtualnej, która nie korzysta z bramy VPN, zwiększa przepływność i zmniejsza opóźnienia, wybiera pozycję **Konfiguruj i Weryfikuj wirtualne sieci równorzędne** w celu skonfigurowania komunikacji równorzędnej sieci wirtualnej połączenia.

Jeśli sieci wirtualnych zostały utworzone przy użyciu modelu wdrażania usługi Azure Resource Manager, wybierz pozycję **Konfiguruj i sprawdź poprawność Menedżer zasobów sieci wirtualnej do Menedżer zasobów połączenia sieci wirtualnej** , aby skonfigurować połączenie VPN.

Jeśli jeden z sieci wirtualnych jest tworzony przy użyciu klasycznego modelu wdrażania platformy Azure, drugi jest tworzony przez Menedżer zasobów, wybierz pozycję **Konfiguruj i sprawdź poprawność klasycznej sieci wirtualnej do Menedżer zasobów połączenie** z siecią wirtualną w celu skonfigurowania połączenia sieci VPN.

### <a name="configuration-1-configure-vnet-peering-for-two-vnets-in-the-same-region"></a>Konfiguracja 1: Skonfiguruj komunikację równorzędną sieci wirtualnych dla dwóch sieci wirtualnych w tym samym regionie

Przed rozpoczęciem wdrażania komunikacji równorzędnej Azure VNet upewnij się, że spełniono następujące wymagania wstępne, aby skonfigurować komunikację równorzędną sieci wirtualnych:

* Wirtualne sieci równorzędne muszą znajdować się w tym samym regionie platformy Azure.
* Sieci wirtualne równorzędne muszą mieć nienakładające się przestrzenie adresów IP.
* Wirtualne sieci równorzędne obejmują dwie sieci wirtualne. Nie istnieje żadna pochodna relacja przechodnia między komunikacjami równorzędnymi. Na przykład jeśli zachodzi komunikacja równorzędna między sieciami VNetA i VNetB oraz komunikacja równorzędna między sieciami VNetB i VNetC, *nie* zachodzi komunikacja równorzędna między sieciami VNetA i VNetC.

W przypadku spełnienia wymagań można wykonać instrukcje [tworzenia sieci wirtualnej komunikacji równorzędnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) w celu utworzenia i skonfigurowania komunikacji równorzędnej wirtualnej.

Aby sprawdzić konfigurację komunikacji równorzędnej sieci wirtualnej, użyj następujących metod:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. W polu zawierającym *zasoby wyszukiwania* tekstu w górnej części Azure Portal wpisz *sieci wirtualne*. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, kliknij je.
3. W wyświetlonym bloku **sieci wirtualne** kliknij sieć wirtualną, dla której chcesz utworzyć komunikację równorzędną.
4. W okienku, które zostanie wyświetlone dla wybranej sieci wirtualnej, kliknij pozycję **Komunikacja równorzędna** w sekcji **Ustawienia** .
5. Kliknij pozycję Komunikacja równorzędna, w której chcesz sprawdzić konfigurację.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
Korzystając z programu Azure PowerShell, uruchom polecenie [Get-AzureRmVirtualNetworkPeering](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) w celu uzyskania komunikacji równorzędnej sieci wirtualnej, na przykład:

```
PS C:\Users\User1> Get-AzureRmVirtualNetworkPeering -VirtualNetworkName Vnet10-01 -ResourceGroupName dev-vnets
Name                             : LinkToVNET10-02
Id                               : /subscriptions/GUID/resourceGroups/dev-vnets/providers/Microsoft.Network/virtualNetworks/VNET10-01/virtualNetworkPeerings/LinkToVNET10-0
2
Etag                             : W/"GUID"
ResourceGroupName                : dev-vnets
VirtualNetworkName               : vnet10-01
ProvisioningState                : Succeeded
RemoteVirtualNetwork             : {
                                  "Id": "/subscriptions/GUID/resourceGroups/DEV-VNET
                                   s/providers/Microsoft.Network/virtualNetworks/VNET10-02"
                                   }
AllowVirtualNetworkAccess        : True
AllowForwardedTraffic            : False
AllowGatewayTransit              : False
UseRemoteGateways                : False
RemoteGateways                   : null
RemoteVirtualNetworkAddressSpace : null
```

### <a name="connection-type-1-connect-a-resource-manager-vnet-to-anther-resource-manager-vnet-azure-resource-manager-to-azure-resource-manager"></a>Typ połączenia 1: Łączenie Menedżer zasobów sieci wirtualnej z siecią wirtualną Menedżer zasobów innym (Azure Resource Manager do Azure Resource Manager)

Można skonfigurować połączenie z jednej sieci wirtualnej Menedżer zasobów bezpośrednio do innej Menedżer zasobów sieci wirtualnej lub skonfigurować połączenie przy użyciu protokołu IPsec.

### <a name="configuration-2-configure-vpn-connection-between-resource-manager-vnets"></a>Konfiguracja 2: Skonfiguruj połączenie sieci VPN między Menedżer zasobów sieci wirtualnych

Aby skonfigurować połączenie między Menedżer zasobów sieci wirtualnych bez protokołu IPsec, zobacz [Konfigurowanie połączenia bramy sieci VPN typu sieć wirtualna-sieć wirtualna przy użyciu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Aby skonfigurować połączenie z protokołem IPsec między dwoma Menedżer zasobów sieci wirtualnych, wykonaj kroki 1-5 w temacie [Tworzenie połączenia lokacja-lokacja w Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) dla każdej sieci wirtualnej.

> [!Note]
> Następujące kroki działają tylko w przypadku sieci wirtualnych należących do tej samej subskrypcji. Jeśli Twoje sieci wirtualne znajdują się w różnych subskrypcjach, do utworzenia połączenia musisz użyć programu PowerShell. Zobacz artykuł [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-vpn-connection-between-resource-manager-vnets"></a>Sprawdź poprawność połączenia sieci VPN między Menedżer zasobów sieci wirtualnych

![IMAGE](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Rysunek 4 — klasyczne połączenie sieci wirtualnej z Azure Resource Manager sieci wirtualnej

Aby sprawdzić, czy połączenie sieci VPN jest prawidłowo skonfigurowane, postępuj zgodnie z instrukcjami:

> [!Note]
> Liczba składników sieci wirtualnej, takich jak "Sieć wirtualna 1" w poniższych krokach, odpowiada liczbom na rysunku 4.

1. Sprawdź nakładające się przestrzenie adresowe w połączonej sieci wirtualnych.

   > [!Note]
   > Nie mogą występować nakładające się przestrzenie adresowe w sieci wirtualnej 1 i sieci wirtualnej 6. 

2. Sprawdź, czy zakres adresów Azure Resource Manager VNET 1 jest prawidłowo zdefiniowany w **obiekcie połączenia** 4.
3. Sprawdź, czy Azure Resource Manager zakres adresów sieci VNET 6 jest dokładnie zdefiniowany w **obiekcie połączenia** 3.
4. Sprawdź, czy klucze wstępne są zgodne z obydwoma obiektami połączeń 3 i 4.
5. Sprawdź, czy adres VIP Azure Resource Manager sieci wirtualnej 2 jest prawidłowo zdefiniowany w **obiekcie połączenia** 4.
6. Sprawdź, czy adres VIP Azure Resource Manager sieci wirtualnej 5 jest prawidłowo zdefiniowany w **obiekcie połączenia** 3.

### <a name="connection-type-2-connect-a-classic-vnet-to-a-resource-manager-vnet"></a>Typ połączenia 2: Łączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżera zasobów

Można utworzyć połączenie między sieci wirtualnychami, które znajdują się w różnych subskrypcjach i w różnych regionach. Można także połączyć sieci wirtualnych, które mają już połączenia z sieciami lokalnymi, o ile typ bramy został skonfigurowany jako oparty na trasie.

Aby skonfigurować połączenie między klasyczną siecią wirtualną i siecią wirtualną Menedżer zasobów, zobacz [łączenie sieci wirtualnych z różnych modeli wdrażania przy użyciu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal) , aby uzyskać więcej informacji.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Rysunek 5 — klasyczne połączenie sieci wirtualnej z Azure Resource Manager sieci wirtualnej

Aby sprawdzić konfigurację podczas łączenia klasycznej sieci wirtualnej z siecią wirtualną Azure Resource Manager, postępuj zgodnie z instrukcjami:

> [!Note]
> Liczba składników sieci wirtualnej, takich jak "Sieć wirtualna 1" w poniższych krokach, odpowiada liczbom na rysunku 5.

1. Sprawdź nakładające się przestrzenie adresowe w połączonej sieci wirtualnych.

   > [!Note]
   > Nie mogą występować nakładające się przestrzenie adresowe w sieci wirtualnej 1 i sieci wirtualnej 6

2. Sprawdź, czy Azure Resource Manager zakres adresów sieci VNet 6 jest zdefiniowany dokładnie w klasycznej definicji sieci lokalnej 3.
3. Sprawdź, czy klasyczny zakres adresów sieci wirtualnej 1 jest zdefiniowany prawidłowo w **obiekcie połączenia** Azure Resource Manager 4.
4. Sprawdź, czy adres VIP klasycznej bramy sieci wirtualnej 2 jest prawidłowo zdefiniowany w **obiekcie połączenia** Azure Resource Manager 4.
5. Sprawdź, czy adres VIP Azure Resource Manager sieci wirtualnej 5 jest zdefiniowany dokładnie w klasycznej **definicji sieci lokalnej** 3.
6. Sprawdź, czy klucze wstępne są zgodne z obydwoma połączonymi sieci wirtualnych:
   1. Klasyczna Sieć wirtualna — definicja sieci lokalnej 3
   2. Azure Resource Manager Sieć wirtualna — obiekt połączenia 4

## <a name="scenario-2-point-to-site-vpn-connection"></a>Scenariusz 2: Połączenie sieci VPN typu punkt-lokacja

Konfiguracja typu punkt-lokacja pozwala utworzyć indywidualne bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenia punkt-lokacja przydają się w przypadku, gdy celem użytkownika jest połączenie się z siecią wirtualną z lokalizacji zdalnej, np. z domu lub z konferencji, lub gdy konieczne jest połączenie z siecią wirtualną jedynie niewielkiej liczby klientów. Połączenie sieci VPN typu punkt-lokacja jest inicjowane z komputera klienckiego za pomocą natywnego klienta VPN systemu Windows. Łączący się klienci używają certyfikatów do uwierzytelniania.

![IMAGE](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Rysunek 2. połączenie punkt-lokacja

Połączenia punkt-lokacja nie wymagają urządzenia sieci VPN. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol). Połączenie typu punkt-lokacja z siecią wirtualną można połączyć przy użyciu różnych narzędzi wdrażania i modeli wdrażania:

* [Konfigurowanie połączenia punkt-lokacja z siecią wirtualną przy użyciu witryny Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu Azure Portal (klasyczny)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Konfigurowanie połączenia typu punkt-lokacja z siecią wirtualną przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connections"></a>Weryfikowanie połączeń punkt-lokacja

Artykuł [Rozwiązywanie problemów: Problemy](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) z połączeniem punkt-lokacja na platformie Azure przeprowadzą między typowymi problemami dotyczącymi połączeń punkt-lokacja.

## <a name="scenario-3-multi-site-vpn-connection"></a>Scenariusz 3: Połączenie sieci VPN z obsługą wiele lokacji

Połączenie typu lokacja-lokacja (S2S) można dodać do sieci wirtualnej, która ma już połączenie S2S, połączenie punkt-lokacja lub połączenie między sieciami wirtualnymi, tym rodzaju połączenie jest często nazywane konfiguracją **obejmującą wiele lokacji** . 

![IMAGE](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Rysunek 3 — połączenie z obsługą kilku lokacji

Obecnie platforma Azure współpracuje z dwoma modelami wdrażania: Menedżer zasobów i klasyczny. Dwa modele nie są w pełni zgodne ze sobą. Aby skonfigurować połączenie w **wielu lokacjach** z różnymi modelami, należy zapoznać się z następującymi artykułami:

* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN (klasyczne)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Te kroki nie dotyczą ExpressRoute i istniejących konfiguracji połączeń typu lokacja-lokacja. Aby uzyskać więcej informacji na temat współistniejących połączeń, zobacz [współistniejące połączenia ExpressRoute/S2S](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="scenario-4-configure-transit-routing"></a>Scenariusz 4: Konfigurowanie routingu tranzytowego

Routing przechodni to konkretny Scenariusz routingu, w którym można połączyć wiele sieci w topologii łańcucha łańcuchowego. Ten Routing umożliwia zasobom w sieci wirtualnych na końcu "łańcucha" komunikowanie się ze sobą za pomocą sieci wirtualnych między. Bez routingu przechodniego sieci lub urządzenia połączone za pomocą koncentratora nie mogą nawiązać połączenia ze sobą.

### <a name="configuration-1-configure-transit-routing-in-a-point-to-site-connection"></a>Konfiguracja 1: Konfigurowanie routingu tranzytowego w ramach połączenia typu punkt-lokacja

W tym scenariuszu należy skonfigurować połączenie VPN lokacja-lokacja między VNetA i Vnetc, a także skonfigurować punkt do sieci VPN dla klienta, aby połączyć się z bramą VNetA. Następnie chcesz włączyć routing tranzytowy dla klientów punkt-lokacja, aby połączyć się z usługą Vnetc, który przechodzi przez VNetA. Ten scenariusz jest obsługiwany, gdy protokół BGP jest włączony w lokacji do sieci VPN między VNetA i Vnetc. Aby uzyskać więcej informacji, zobacz [Informacje o routingu sieci VPN typu punkt-lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configuration-2-configure-transit-routing-in-an-expressroute-connection"></a>Konfiguracja 2: Konfigurowanie routingu tranzytowego w ramach połączenia ExpressRoute

Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem specjalnego połączenia prywatnego wspieranego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365.  Aby uzyskać więcej informacji, zobacz [ExpressRoute Overview (przegląd](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)).

![IMAGE](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

Rysunek 6. połączenie prywatnej komunikacji równorzędnej ExpressRoute z usługą Azure sieci wirtualnych

> [!Note]
> Firma Microsoft zaleca, aby w przypadku, gdy VNetA i Vnetc znajdują się w tym samym regionie geopolitycznym, który [łączy sieci wirtualnych do obwodu ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) zamiast konfigurować Routing przechodni. Jeśli Twoje sieci wirtualnych znajdują się w różnych regionach geopolitycznych, możesz je również połączyć bezpośrednio z własnym obwodem, jeśli masz [ExpressRoute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Jeśli masz ExpressRoute i współistnienie lokacja-lokacja, routing tranzytowy nie jest obsługiwany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie współistniejących połączeń ExpressRoute i lokacja-lokacja, aby uzyskać więcej informacji](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Jeśli włączono ExpressRoute do łączenia sieci lokalnych z siecią wirtualną platformy Azure, możesz włączyć komunikację równorzędną sieci wirtualnej między sieci wirtualnych, które mają być przechodnie. Aby umożliwić sieciom lokalnym łączenie się ze zdalną siecią wirtualną, należy skonfigurować [komunikację równorzędną sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Wirtualne sieci równorzędne są dostępne tylko dla sieci wirtualnych w tym samym regionie.

Aby sprawdzić, czy skonfigurowano trasę tranzytową dla komunikacji równorzędnej w sieci wirtualnej, postępuj zgodnie z instrukcjami:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. [Utwórz komunikację równorzędną między siecią wirtualną a i B](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) tak jak na diagramie wcześniej (rysunek 6). 
3. W okienku, które zostanie wyświetlone dla wybranej sieci wirtualnej, kliknij pozycję **Komunikacja równorzędna** w sekcji **Ustawienia** .
4. Kliknij łącze komunikacji równorzędnej, które chcesz wyświetlić i **skonfigurować** , aby sprawdzić, czy włączono **Zezwalanie na tranzyt bramy** na VNetA podłączonym do obwodu ExpressRoute i **Używanie bramy zdalnej** na zdalnym vnetc niepołączonym z ExpressRoute zwarci.

### <a name="configuration-3-configure-transit-routing-in-a-vnet-peering-connection"></a>Konfiguracja 3: Konfigurowanie routingu tranzytowego w połączeniu komunikacji równorzędnej sieci wirtualnej

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej, użytkownicy mogą również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do infrastruktury lokalnej. Aby skonfigurować trasę tranzytową w przypadku komunikacji równorzędnej sieci wirtualnych, sprawdź [połączenia sieci wirtualnej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json)z siecią wirtualną.

> [!Note]
> Tranzyt bramy nie jest obsługiwany w relacji komunikacji równorzędnej między sieciami wirtualnymi utworzonymi za pomocą różnych modeli wdrażania. Aby przesyłanie danych za pomocą bramy działało, obie sieci wirtualne będące w relacji komunikacji równorzędnej muszą zostać utworzone za pomocą usługi Resource Manager.

Aby sprawdzić, czy skonfigurowano trasę tranzytową dla komunikacji równorzędnej w sieci wirtualnej, postępuj zgodnie z instrukcjami:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma wymagane [role i uprawnienia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#roles-permissions).
2. W polu zawierającym zasoby wyszukiwania tekstu w górnej części Azure Portal wpisz *sieci wirtualne*. Gdy **sieci wirtualne** pojawiają się w wynikach wyszukiwania, kliknij je.
3. W wyświetlonym bloku **sieci wirtualne** kliknij sieć wirtualną, dla której chcesz sprawdzić ustawienie komunikacji równorzędnej.
4. W okienku, które zostanie wyświetlone dla wybranej sieci wirtualnej, kliknij pozycję **Komunikacja równorzędna** w sekcji **Ustawienia** .
5. Kliknij komunikację równorzędną, którą chcesz wyświetlić, i sprawdź, czy włączono opcję **Zezwalaj na tranzyt bramy** i **Użyj bramy zdalnej** w obszarze **Konfiguracja**.

![IMAGE](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configuration-4-configure-transit-routing-in-a-vnet-to-vnet-connection"></a>Konfiguracja 4: Konfigurowanie routingu tranzytowego w ramach połączenia między sieciami wirtualnymi

Aby skonfigurować Routing tranzytowy między sieci wirtualnych, należy włączyć protokół BGP dla wszystkich pośrednich połączeń między sieciami wirtualnymi przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell. Aby uzyskać instrukcje, zobacz [jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Ruch tranzytowy za pośrednictwem usługi Azure VPN Gateway jest możliwy przy użyciu klasycznego modelu wdrażania, ale zależy od statycznie zdefiniowanych przestrzeni adresowych w pliku konfiguracyjnym sieci. Protokół BGP nie jest jeszcze obsługiwany w połączeniu z sieciami wirtualnymi platformy Azure i bramami sieci VPN przy użyciu klasycznego modelu wdrażania. Bez protokołu BGP ręczne Definiowanie przestrzeni adresowych tranzytu jest podatne na błędy i nie jest zalecane.

> [!Note]
> Klasyczne połączenia między sieciami wirtualnymi są konfigurowane przy użyciu Azure Portal (klasyczny) lub przy użyciu pliku konfiguracji sieci w portalu klasycznym. Nie można utworzyć ani zmodyfikować klasycznej sieci wirtualnej za pomocą modelu wdrażania Azure Resource Manager lub Azure Portal. Aby uzyskać więcej informacji na temat routingu tranzytowego dla klasycznego sieci wirtualnych, zobacz [koncentrator & szprychy, łańcuchy łańcucha i Sieć wirtualna z pełnym siatką w usłudze Azure ARM przy użyciu sieci VPN (1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configuration-5-configure-transit-routing-in-a-site-to-site-connection"></a>Konfiguracja 5: Konfigurowanie routingu tranzytowego w ramach połączenia typu lokacja-lokacja

Aby skonfigurować Routing tranzytowy między siecią lokalną i sieci wirtualnej z połączeniem lokacja-lokacja, należy włączyć protokół BGP dla wszystkich pośrednich połączeń lokacja-lokacja przy użyciu modelu wdrażania Menedżer zasobów i programu PowerShell, zobacz artykuł [jak skonfigurować Instrukcje dotyczące protokołu BGP w bramach sieci VPN platformy Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) .

Ruch tranzytowy za pośrednictwem usługi Azure VPN Gateway jest możliwy przy użyciu klasycznego modelu wdrażania, ale zależy od statycznie zdefiniowanych przestrzeni adresowych w pliku konfiguracyjnym sieci. Protokół BGP nie jest jeszcze obsługiwany w połączeniu z sieciami wirtualnymi platformy Azure i bramami sieci VPN przy użyciu klasycznego modelu wdrażania. Bez protokołu BGP ręczne Definiowanie przestrzeni adresowych tranzytu jest podatne na błędy i nie jest zalecane.

> [!Note]
> Klasyczne połączenia typu lokacja-lokacja są konfigurowane przy użyciu Azure Portal (klasyczny) lub przy użyciu pliku konfiguracji sieci w portalu klasycznym. Nie można utworzyć ani zmodyfikować klasycznej sieci wirtualnej za pomocą modelu wdrażania Azure Resource Manager lub Azure Portal. Aby uzyskać więcej informacji na temat routingu tranzytowego dla klasycznego sieci wirtualnych, zobacz [koncentrator & szprychy, łańcuchy łańcucha i Sieć wirtualna z pełnym siatką w usłudze Azure ARM przy użyciu sieci VPN (1)](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="scenario-5-configure-bgp-for-a-vpn-gateway"></a>Scenariusz 5: Konfigurowanie protokołu BGP dla bramy sieci VPN

Protokół BGP jest standardowym protokołem routingu używanym w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub wieloma sieciami. Gdy protokół BGP jest używany w kontekście sieci wirtualnych platformy Azure, protokół BGP włącza bramy sieci VPN platformy Azure i lokalne urządzenia sieci VPN, znane jako elementy równorzędne protokołu BGP lub sąsiedzi. Wymienia "trasy", które będą informować bramę o dostępności i możliwości uzyskania dostępu do tych prefiksów, aby przejść przez bramę lub routery. Protokół BGP umożliwia również włączenie routingu tranzytowego między wieloma sieciami poprzez propagowanie tras, których brama BGP uczy się od jednego elementu równorzędnego BGP, we wszystkich innych elementach równorzędnych BGP. Aby uzyskać więcej informacji, zobacz [Omówienie protokołu BGP z bramami sieci VPN platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurowanie protokołu BGP dla połączenia sieci VPN

Aby skonfigurować połączenie sieci VPN korzystające z protokołu BGP, zobacz [jak skonfigurować protokół BGP na bramach sieci VPN platformy Azure za pomocą programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

> [!Note]
> 1. Włącz protokół BGP na bramie Virtual Network, tworząc dla niego liczbę AS. Bramy podstawowe nie obsługują protokołu BGP. Aby sprawdzić jednostkę SKU bramy, przejdź do sekcji Przegląd w bloku VPN Gateway w Azure Portal. Jeśli jednostka SKU jest **podstawowa**, należy zmienić jednostkę SKU ([zmieniając rozmiar bramy](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)) na jednostkę SKU **VpnGw1** . Sprawdzenie, czy jednostka SKU spowoduje przestoje 20-30 minut. Gdy tylko brama ma poprawną jednostkę SKU, można ją dodać za pomocą polecenia [Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) programu PowerShell. Po skonfigurowaniu numeru AS adres IP elementu równorzędnego protokołu BGP dla bramy zostanie udostępniony automatycznie.
> 2. LocalNetworkGateway musi być dostarczony **ręcznie** z numerem AS i adresem elementu równorzędnego protokołu BGP. Można ustawić wartości **ASN** i **-BgpPeeringAddress** za pomocą polecenia [New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) lub [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) programu PowerShell. Niektóre z nich są zarezerwowane dla platformy Azure i nie można ich używać zgodnie [z opisem w temacie BGP z platformą azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview#bgp-faq).
> 3. Na koniec obiekt połączenia musi mieć włączony protokół BGP. Wartość **-EnableBGP** można ustawić na `$True` za pomocą opcji [New-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) lub [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Weryfikowanie konfiguracji protokołu BGP

Aby sprawdzić, czy protokół BGP jest skonfigurowany prawidłowo, można uruchomić polecenie `get-AzureRmVirtualNetworkGateway` cmdlet `get-AzureRmLocalNetworkGateway`, a następnie wyszukać dane wyjściowe powiązane z protokołem BGP w części BgpSettingsText. Na przykład: BgpSettingsText:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="scenario-6-highly-available-active-active-vpn-connection"></a>Scenariusz 6: Połączenie sieci VPN o wysokiej dostępności (aktywne)

Kluczowe różnice między bramami Active-Active i Active-Standby:

* Należy utworzyć dwie konfiguracje IP bramy z dwoma publicznymi adresami IP
* Należy ustawić flagę *EnableActiveActiveFeature*
* Jednostka SKU bramy musi mieć wartość VpnGw1, VpnGw2, VpnGw3

Aby zapewnić wysoką dostępność połączeń między różnymi lokalizacjami i między sieciami wirtualnymi, należy wdrożyć wiele bram sieci VPN i ustanowić wiele połączeń równoległych między firmami i platformą Azure. Aby zapoznać się z omówieniem opcji łączności i topologii, zobacz [wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)dostępności.

Aby utworzyć aktywne-aktywne połączenia między lokacjami i między sieciami wirtualnymi, postępuj zgodnie z instrukcjami podanymi w temacie [Konfigurowanie aktywnych i aktywnych połączeń VPN S2S z usługą Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) , aby skonfigurować bramę sieci VPN platformy Azure w trybie aktywny/aktywny.

> [!Note]  
> 1. Po dodaniu adresów do bramy sieci lokalnej dla włączonego protokołu BGP Tryb aktywny-do-aktywny *dodaje tylko adresy/32 elementów równorzędnych protokołu BGP*. W przypadku dodania kolejnych adresów będą one uznawane za trasy statyczne i mają pierwszeństwo przed trasami protokołu BGP.
> 2. Dla sieci lokalnych, które nawiązują połączenie z platformą Azure, należy użyć innego protokołu BGP. (Jeśli są takie same, należy zmienić numer ASN sieci wirtualnej, jeśli lokalne urządzenie sieci VPN już używa numeru ASN do komunikacji równorzędnej z innymi sąsiadami BGP).

## <a name="scenario-7-change-an-azure-vpn-gateway-type-after-deployment"></a>Scenariusz 7: Zmiana typu bramy sieci VPN platformy Azure po wdrożeniu

Nie można zmienić typu bramy sieci wirtualnej platformy Azure z zasad opartych na trasach ani w inny sposób bezpośrednio. Bramę należy usunąć po tym, że adres IP i klucz wstępny (PSK) won'tbe zachowywane. Następnie można utworzyć nową bramę żądanego typu. Aby usunąć i utworzyć bramę, wykonaj następujące czynności:

1. Usuń wszystkie połączenia skojarzone z oryginalną bramą.
2. Usuń bramę przy użyciu Azure Portal, programu PowerShell lub klasycznego programu PowerShell: 
   * [Usuwanie bramy sieci wirtualnej przy użyciu Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (wersja klasyczna)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Wykonaj kroki opisane w temacie [Tworzenie bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#a-namevnetgatewaya4-create-the-vpn-gateway) , aby utworzyć nową bramę żądanego typu i ukończyć konfigurację sieci VPN.

> [!Note]
> Ten proces zajmie około 60 minut.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

