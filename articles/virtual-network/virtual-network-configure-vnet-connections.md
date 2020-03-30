---
title: Konfigurowanie i sprawdzanie poprawności połączeń sieci wirtualnej lub sieci VPN
description: Wskazówki krok po kroku dotyczące konfigurowania i sprawdzania poprawności różnych wdrożeń sieci VPN i sieci wirtualnych platformy Azure
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
ms.openlocfilehash: dddf402455292e19bf0fcda3c50d9ce10d5888d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71099056"
---
# <a name="configure-and-validate-virtual-network-or-vpn-connections"></a>Konfigurowanie i sprawdzanie poprawności połączeń sieci wirtualnej lub sieci VPN

Ten przewodnik zawiera wskazówki krok po kroku, aby skonfigurować i sprawdzić poprawność różnych wdrożeń sieci VPN i sieci wirtualnej platformy Azure. Scenariusze obejmują routing tranzytowy, połączenia między sieciami, protokół BGP (Border Gateway Protocol), połączenia wielolokalowe i połączenia typu "punkt-lokacja".

Bramy sieci VPN platformy Azure umożliwiają elastyczność w organizowaniu niemal każdego rodzaju topologii połączonych sieci wirtualnych na platformie Azure. Na przykład można połączyć sieci wirtualne:

- W różnych regionach.
- Między typami sieci wirtualnej (Usługa Azure Resource Manager a classic).
- W ramach platformy Azure lub w lokalnym środowisku hybrydowym.
- W różnych subskrypcjach. 

## <a name="network-to-network-vpn-connection"></a>Połączenie sieciowej sieci VPN

Łączenie sieci wirtualnej z inną siecią wirtualną (sieć-do sieci) za pośrednictwem sieci VPN jest podobne do łączenia sieci wirtualnej z lokalną lokalizacją lokacji. Oba typy połączeń używają bramy sieci VPN, aby zapewnić bezpieczny tunel za pośrednictwem protokołu IPsec i IKE. Sieci wirtualne mogą być zlokalizowane w tych samych lub różnych regionach i mogą funkcjonować w ramach tej samej lub różnych subskrypcji.

![Połączenie między siecią a siecią za pomocą protokołu IPsec](./media/virtual-network-configure-vnet-connections/4034386_en_2.png)
 
Jeśli sieci wirtualne znajdują się w tym samym regionie, warto rozważyć połączenie ich przy użyciu komunikacji równorzędnej sieci wirtualnej. Komunikacja równorzędna w sieci wirtualnej nie korzysta z bramy sieci VPN. Zwiększa przepływność i zmniejsza opóźnienie. Aby skonfigurować połączenie komunikacji równorzędnej w sieci wirtualnej, wybierz pozycję **Konfiguruj i sprawdź poprawność komunikacji równorzędnej sieci wirtualnej**.

Jeśli sieci wirtualne zostały utworzone za pomocą modelu wdrażania usługi Azure Resource Manger, wybierz pozycję **Konfiguruj i sprawdź poprawność sieci wirtualnej Menedżera zasobów do połączenia sieci wirtualnej Menedżera zasobów,** aby skonfigurować połączenie sieci VPN.

Jeśli jedna z sieci wirtualnych została utworzona za pośrednictwem klasycznego modelu wdrażania platformy Azure, a druga została utworzona za pośrednictwem Menedżera zasobów, wybierz pozycję **Konfiguruj i sprawdź poprawność klasycznej sieci wirtualnej do połączenia sieci wirtualnej Menedżera zasobów,** aby skonfigurować połączenie sieci VPN.

### <a name="configure-virtual-network-peering-for-two-virtual-networks-in-the-same-region"></a>Konfigurowanie komunikacji równorzędnej sieci wirtualnej dla dwóch sieci wirtualnych w tym samym regionie

Przed rozpoczęciem implementacji i konfigurowania komunikacji równorzędnej sieci wirtualnej platformy Azure upewnij się, że spełniasz następujące wymagania wstępne:

* Wirtualne sieci równorzędne muszą znajdować się w tym samym regionie platformy Azure.
* Sieci wirtualne typu peered muszą mieć przestrzenie adresów IP, które nie nakładają się na siebie.
* Wirtualne sieci równorzędne obejmują dwie sieci wirtualne. Nie ma pochodnych relacji przechodnich między komunikacją równorzędnie. Na przykład jeśli sieć wirtualna jest równorzędna z siecią wirtualną, a sieć wirtualna jest równorzędna z siecią wirtualną, sieć wirtualna *nie* jest równorzędna z siecią wirtualną.

Po spełnieniu wymagań można wykonać [samouczek: Łączenie sieci wirtualnych z komunikacją równorzędową sieci wirtualnej za pomocą witryny Azure portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) do tworzenia i konfigurowania komunikacji równorzędnej.

Aby sprawdzić konfigurację komunikacji równorzędnej, użyj następującej metody:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma niezbędne [role i uprawnienia](virtual-network-manage-peering.md#permissions).
2. W polu zawierającym tekst **Wyszukiwanie zasobów** u góry portalu wpisz polecenie **Sieci wirtualne**. Gdy w wynikach wyszukiwania pojawią się **sieci wirtualne,** wybierz je.
3. W **wyświetlonym** bloku Sieci wirtualne wybierz sieć wirtualną, dla której chcesz utworzyć komunikację równorzędną.
4. W okienku wyświetlanym dla sieci wirtualnej wybierz pozycję **Peerings** w sekcji **Ustawienia.**
5. Wybierz komunikację równorzędną i wyświetl wyniki konfiguracji.

![Wybór sprawdzania konfiguracji komunikacji równorzędnej sieci wirtualnej](./media/virtual-network-configure-vnet-connections/4034496_en_1.png)
 
W przypadku programu Azure PowerShell uruchom polecenie [Get-AzureRmVirtualNetworkPeering,](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworkpeering?view=azurermps-4.1.0) aby uzyskać komunikację równorzędną w sieci wirtualnej. Oto przykład:

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

### <a name="connect-a-resource-manager-virtual-network-to-another-resource-manager-virtual-network"></a>Łączenie sieci wirtualnej Menedżera zasobów z inną siecią wirtualną Menedżera zasobów

Połączenie z jednej sieci wirtualnej Menedżera zasobów można skonfigurować bezpośrednio z sieci wirtualnej Menedżera zasobów do innej sieci wirtualnej Menedżera zasobów. Możesz też skonfigurować połączenie za pomocą protokołu IPsec.

### <a name="configure-a-vpn-connection-between-resource-manager-virtual-networks"></a>Konfigurowanie połączenia sieci VPN między sieciami wirtualnymi Menedżera zasobów

Aby skonfigurować połączenie między sieciami wirtualnymi Menedżera zasobów bez protokołu IPsec, zobacz [Konfigurowanie połączenia bramy sieciowej do sieci przy użyciu portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

Aby skonfigurować połączenie z protokołem IPsec między dwiema sieciami wirtualnymi Menedżera zasobów, wykonaj kroki od 1 do 5 w [temacie Tworzenie połączenia lokacja lokacja w portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) dla każdej sieci wirtualnej.

> [!Note]
> Te kroki działają tylko dla sieci wirtualnych w tej samej subskrypcji. Jeśli sieci wirtualne są w różnych subskrypcji, należy użyć programu PowerShell, aby nawiązać połączenie. Zobacz artykuł [PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps).

### <a name="validate-the-vpn-connection-between-resource-manager-virtual-networks"></a>Sprawdzanie poprawności połączenia sieci VPN między sieciami wirtualnymi Menedżera zasobów

![Klasyczne połączenie sieci wirtualnej z siecią wirtualną usługi Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034493_en_2.png)

Aby sprawdzić, czy połączenie sieci VPN jest poprawnie skonfigurowane, postępuj zgodnie z tymi instrukcjami.

> [!Note] 
> Liczby po składnikach sieci wirtualnej w tych krokach odpowiadają liczbom na poprzednim diagramie.

1. Upewnij się, że w połączonych sieciach wirtualnych nie ma nakładających się przestrzeni adresowych.
2. Sprawdź, czy zakres adresów dla sieci wirtualnej usługi Azure Resource Manager (1) jest dokładnie zdefiniowany w wystąpieniu **obiektu połączenia** (4).
3. Sprawdź, czy zakres adresów dla sieci wirtualnej usługi Azure Resource Manager (6) jest dokładnie zdefiniowany w wystąpieniu **obiektu połączenia** (3).
4. Sprawdź, czy klucze wstępnie udostępnione są pasujące w obiektach połączenia.
5. Sprawdź, czy usługa VIP bramy sieci wirtualnej usługi Azure Resource Manager (2) jest dokładnie zdefiniowana w wystąpieniu **obiektu Połączenia** (4).
6. Sprawdź, czy usługa VIP bramy sieci wirtualnej usługi Azure Resource Manager (5) jest dokładnie zdefiniowana w wystąpieniu **obiektu Połączenia** (3).

### <a name="connect-a-classic-virtual-network-to-a-resource-manager-virtual-network"></a>Łączenie klasycznej sieci wirtualnej z siecią wirtualną Menedżera zasobów

Można utworzyć połączenie między sieciami wirtualnymi, które znajdują się w różnych subskrypcjach i w różnych regionach. Można również połączyć sieci wirtualne, które mają już połączenia z sieciami lokalnymi, o ile typ bramy został skonfigurowany jako oparty na marszruty.

Aby skonfigurować połączenie między klasyczną siecią wirtualną a siecią wirtualną Menedżera zasobów, zobacz [Łączenie sieci wirtualnych z różnych modeli wdrażania przy użyciu portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-connect-different-deployment-models-portal).

![Klasyczne połączenie sieci wirtualnej z siecią wirtualną usługi Azure Resource Manager](./media/virtual-network-configure-vnet-connections/4034389_en_2.png)

Aby sprawdzić konfigurację podczas łączenia klasycznej sieci wirtualnej z siecią wirtualną usługi Azure Resource Manager, wykonaj te instrukcje.

> [!Note] 
> Liczby po składnikach sieci wirtualnej w tych krokach odpowiadają liczbom na poprzednim diagramie. 

1. Upewnij się, że w połączonych sieciach wirtualnych nie ma nakładających się przestrzeni adresowych.
2. Sprawdź, czy zakres adresów dla sieci wirtualnej usługi Azure Resource Manager (6) jest dokładnie zdefiniowany w klasycznej definicji sieci lokalnej (3).
3. Sprawdź, czy zakres adresów dla klasycznej sieci wirtualnej (1) jest dokładnie zdefiniowany w wystąpieniu **obiektu usługi** Azure Resource Manager Connection (4).
4. Sprawdź, czy klasyczny adres VIP bramy sieci wirtualnej (2) jest dokładnie zdefiniowany w wystąpieniu **obiektu usługi** Azure Resource Manager (4).
5. Sprawdź, czy brama sieci wirtualnej usługi Azure Resource Manager (5) jest dokładnie zdefiniowana w klasycznym wystąpieniu **definicji sieci lokalnej** (3).
6. Sprawdź, czy klucze wstępnie udostępnione są zgodne w obu połączonych sieciach wirtualnych:
   - Klasyczna sieć **wirtualna: Definicja sieci lokalnej** (3)
   - Sieć wirtualna usługi Azure Resource Manager: **obiekt połączenia** (4)

## <a name="create-a-point-to-site-vpn-connection"></a>Tworzenie połączenia sieci VPN typu punkt-lokacja

Konfiguracja typu "punkt-lokacja"*(P2S* na poniższym diagramie) umożliwia utworzenie bezpiecznego połączenia z indywidualnego komputera klienckiego z siecią wirtualną. Połączenia typu "punkt-lokacja" są przydatne, gdy chcesz połączyć się z siecią wirtualną z lokalizacji zdalnej, na przykład z domu lub konferencji. Są one również przydatne, gdy masz tylko kilku klientów, którzy muszą połączyć się z siecią wirtualną. 

Połączenie sieci VPN typu punkt-lokacja jest inicjowane z komputera klienckiego za pośrednictwem macierzystego klienta sieci VPN systemu Windows. Łączący się klienci używają certyfikatów do uwierzytelniania.

![Połączenie typu punkt-lokacja](./media/virtual-network-configure-vnet-connections/4034387_en_3.png)

Połączenia typu "punkt-lokacja" nie wymagają urządzenia sieci VPN. Tworzą połączenie sieci VPN za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol). Połączenie typu "punkt-lokacja" można połączyć z siecią wirtualną przy użyciu różnych narzędzi wdrażania i modeli wdrażania:

* [Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu portalu Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal)
* [Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu portalu Azure (klasyczny)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal)
* [Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)

### <a name="validate-your-point-to-site-connection"></a>Sprawdzanie poprawności połączenia typu "punkt-lokacja"

Artykuł [Rozwiązywanie problemów: Problemy z połączeniem typu punkt-lokacja platformy Azure](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems) są częstymi problemami z połączeniami typu punkt-lokacja.

## <a name="create-a-multisite-vpn-connection"></a>Tworzenie wielolokaliowego połączenia sieci VPN

Połączenie lokacja-lokacja *(S2S)* można dodać do sieci wirtualnej, która ma już połączenie lokacja-lokacja, połączenie typu lokacja-lokacja lub połączenie między siecią a siecią. Ten rodzaj połączenia jest często nazywany konfiguracją *wielolokacjową.* 

![Połączenie wielolokalowe](./media/virtual-network-configure-vnet-connections/4034497_en_2.png)

Obecnie platforma Azure obsługuje dwa modele wdrażania: model wdrażania przy użyciu usługi Azure Resource Manager i model klasyczny. Oba modele nie są ze sobą w pełni kompatybilne. Aby skonfigurować połączenie wielolokalowe z różnymi modelami, zobacz następujące artykuły:

* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal)
* [Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN (klasyczny)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-multi-site)

> [!Note]
> Kroki opisane w tych artykułach nie mają zastosowania do usługi Azure ExpressRoute i konfiguracji współistniejących połączeń między lokacjami. Aby uzyskać więcej informacji, zobacz [Usługi ExpressRoute i współistniejące połączenia między lokacjami](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

## <a name="configure-transit-routing"></a>Konfigurowanie routingu tranzytowego

Routing tranzytowy to określony scenariusz routingu, w którym można połączyć wiele sieci w topologii łańcucha łańcuchowego. Ta routing umożliwia zasoby w sieciach wirtualnych na obu końcach łańcucha do komunikowania się ze sobą za pośrednictwem sieci wirtualnych pomiędzy nimi. Bez routingu tranzytowego sieci lub urządzenia równorzędne za pośrednictwem koncentratora nie mogą się ze sobą skontaktować.

### <a name="configure-transit-routing-in-a-point-to-site-connection"></a>Konfigurowanie routingu tranzytowego w połączeniu typu punkt-lokacja

Wyobraź sobie scenariusz, w którym chcesz skonfigurować połączenie sieci VPN lokacji lokacji między siecią wirtualną i siecią wirtualną. Chcesz również skonfigurować sieć VPN typu punkt-lokacja, aby klient miał łączyć się z bramą sieci wirtualnej. Następnie chcesz włączyć routing tranzytowy dla klientów typu punkt-lokacja, aby połączyć się z siecią wirtualną, która przechodzi przez VNetA. 

Ten scenariusz jest obsługiwany, gdy protokół BGP jest włączony w sieci VPN lokacji lokacji między siecią wirtualną a siecią wirtualną. Aby uzyskać więcej informacji, zobacz [Informacje o routingu sieci VPN typu punkt-lokacja](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-point-to-site-routing).

### <a name="configure-transit-routing-in-an-expressroute-connection"></a>Konfigurowanie routingu tranzytowego w połączeniu usługi ExpressRoute

Usługa Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na chmurę Microsoft za pośrednictwem specjalnego połączenia prywatnego wspieranego przez dostawcę połączenia. Dzięki usłudze ExpressRoute można ustanowić połączenia z usługami Microsoft w chmurze, np. Microsoft Azure, Office 365 i Dynamics 365. Aby uzyskać więcej informacji, zobacz [Omówienie usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Prywatne połączenie równorzędne usługi ExpressRoute z sieciami wirtualnymi platformy Azure](./media/virtual-network-configure-vnet-connections/4034395_en_1.png)

> [!Note]
> Zaleca się, że jeśli VNetA i VNetB znajdują się w tym samym regionie geopolitycznym, [należy połączyć obie sieci wirtualne z obwodem usługi ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-arm) zamiast konfigurowania routingu tranzytowego. Jeśli sieci wirtualne znajdują się w różnych regionach geopolitycznych, możesz je również połączyć bezpośrednio z twoim obwodem, jeśli masz [expressroute Premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs#expressroute-premium). 

Jeśli masz usługi ExpressRoute i współistnienie lokacji, routing tranzytowy nie jest obsługiwany. Aby uzyskać więcej informacji, zobacz [Konfigurowanie usługi ExpressRoute i site-to-site przy użyciu programu PowerShell](https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager).

Jeśli usługa ExpressRoute została włączona w celu połączenia sieci lokalnych z siecią wirtualną platformy Azure, można włączyć komunikację równorzędną między sieciami wirtualnymi, w których chcesz mieć routing tranzytowy. Aby umożliwić sieciom lokalnym łączenie się ze zdalną siecią wirtualną, należy skonfigurować [komunikację równorzędno -sieci wirtualną](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview#gateways-and-on-premises-connectivity). 

> [!Note]
> Komunikacja równorzędna sieci wirtualnej jest dostępna tylko dla sieci wirtualnych w tym samym regionie.

Aby sprawdzić, czy routing tranzytowy został skonfigurowany do komunikacji równorzędnej w sieci wirtualnej, wykonaj następujące instrukcje:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma niezbędne [role i uprawnienia](virtual-network-manage-peering.md#permissions).
2. [Tworzenie komunikacji równorzędnej między siecią wirtualną i siecią wirtualną,](https://docs.microsoft.com/azure/virtual-network/virtual-network-create-peering) jak pokazano na wcześniejszym diagramie. 
3. W okienku wyświetlanym dla sieci wirtualnej wybierz pozycję **Peerings** w sekcji **Ustawienia.**
4. Wybierz komunikację równorzędnej, którą chcesz wyświetlić. Następnie wybierz pozycję **Konfiguracja,** aby sprawdzić, czy włączono opcję Zezwalaj na **przesyłanie bramy** w sieci VNetA podłączonej do obwodu usługi ExpressRoute, oraz **użyj bramy zdalnej** w zdalnej sieci wirtualnej wirtualnej niepodłączonej do obwodu usługi ExpressRoute.

### <a name="configure-transit-routing-in-a-virtual-network-peering-connection"></a>Konfigurowanie routingu tranzytowego w połączeniu komunikacji równorzędnej sieci wirtualnej

Gdy sieci wirtualne są połączone za pomocą komunikacji równorzędnej, użytkownicy mogą również skonfigurować bramę w wirtualnej sieci równorzędnej jako punkt tranzytowy do infrastruktury lokalnej. Aby skonfigurować trasę tranzytową w komunikacji równorzędnej w sieci wirtualnej, zobacz [Połączenia między sieciami](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-vnet-vnet-rm-ps?toc=/azure/virtual-network/toc.json).

> [!Note]
> Przesyłanie bramy nie jest obsługiwane w relacji komunikacji równorzędnej między sieciami wirtualnymi utworzonymi za pośrednictwem różnych modeli wdrażania. Obie sieci wirtualne w relacji komunikacji równorzędnej muszą zostać utworzone za pośrednictwem Menedżera zasobów, aby brama mogła zostać przejezdna do pracy.

Aby sprawdzić, czy trasa tranzytowa została skonfigurowana do komunikacji równorzędnej w sieci wirtualnej, wykonaj następujące instrukcje:

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com/) przy użyciu konta, które ma niezbędne [role i uprawnienia](virtual-network-manage-peering.md#permissions).
2. W polu zawierającym tekst **Wyszukiwanie zasobów** u góry portalu wpisz polecenie **Sieci wirtualne**. Gdy w wynikach wyszukiwania pojawią się **sieci wirtualne,** wybierz je.
3. W **wyświetlonym** bloku Sieci wirtualne wybierz sieć wirtualną, dla której chcesz sprawdzić ustawienie komunikacji równorzędnej.
4. W okienku wyświetlanym dla wybranej sieci wirtualnej wybierz pozycję **Komunikacja równorzędna** w sekcji **Ustawienia.**
5. Wybierz komunikację równorzędnej, którą chcesz wyświetlić. Sprawdź, czy włączono opcję **Zezwalaj na przesyłanie bramy** i **używaj bram zdalnych** w obszarze **Konfiguracja**.

![Wybór sprawdzania, czy trasa tranzytowa została skonfigurowana do komunikacji równorzędnej w sieci wirtualnej](./media/virtual-network-configure-vnet-connections/4035414_en_1.png)

### <a name="configure-transit-routing-in-a-network-to-network-connection"></a>Konfigurowanie routingu tranzytowego w połączeniu sieci-sieć

Aby skonfigurować routing tranzytowy między sieciami wirtualnymi, należy włączyć usługę BGP dla wszystkich pośrednich połączeń między sieciami przy użyciu modelu wdrażania Menedżera zasobów i programu PowerShell. Aby uzyskać [instrukcje, zobacz Jak skonfigurować usługę BGP w bramkach sieci VPN platformy Azure przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Ruch tranzytowy za pośrednictwem bram sieci VPN platformy Azure jest możliwy za pośrednictwem klasycznego modelu wdrażania, ale zależy to od statycznie zdefiniowanych przestrzeni adresowych w pliku konfiguracji sieci. BGP nie jest jeszcze obsługiwany w sieciach wirtualnych platformy Azure i bramy sieci VPN za pośrednictwem klasycznego modelu wdrażania. Bez protokołu BGP ręczne definiowanie przestrzeni adresowych tranzytowych jest podatne na błędy i nie zalecamy.

> [!Note]
> Możesz skonfigurować klasyczne połączenia między sieciami za pomocą klasycznego portalu azure lub przy użyciu pliku konfiguracji sieci w portalu klasycznym. Nie można utworzyć ani zmodyfikować klasycznej sieci wirtualnej za pomocą modelu wdrażania usługi Azure Resource Manager ani witryny Azure portal. Aby uzyskać więcej informacji na temat routingu tranzytowego dla klasycznych sieci wirtualnych, zobacz [blog programu Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

### <a name="configure-transit-routing-in-a-site-to-site-connection"></a>Konfigurowanie routingu tranzytowego w połączeniu lokacja-lokacja

Aby skonfigurować routing tranzytowy między siecią lokalną a siecią wirtualną z połączeniem lokacja-lokacja, należy włączyć usługę BGP dla wszystkich pośrednich połączeń lokacja-lokacja przy użyciu modelu wdrażania Menedżera zasobów i programu PowerShell. Zobacz [Jak skonfigurować usługę BGP na bramy sieci VPN platformy Azure przy użyciu programu PowerShell,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps) aby uzyskać instrukcje.

Ruch tranzytowy za pośrednictwem bram sieci VPN platformy Azure jest możliwy za pośrednictwem klasycznego modelu wdrażania, ale zależy to od statycznie zdefiniowanych przestrzeni adresowych w pliku konfiguracji sieci. BGP nie jest jeszcze obsługiwany w sieciach wirtualnych platformy Azure i bramy sieci VPN za pośrednictwem klasycznego modelu wdrażania. Bez protokołu BGP ręczne definiowanie przestrzeni adresowych tranzytowych jest podatne na błędy i nie zalecamy.

> [!Note]
> Możesz skonfigurować klasyczne połączenia lokacja-lokacja przy użyciu klasycznego portalu platformy Azure lub przy użyciu pliku konfiguracji sieci w portalu klasycznym. Nie można utworzyć ani zmodyfikować klasycznej sieci wirtualnej za pomocą modelu wdrażania usługi Azure Resource Manager ani witryny Azure portal. Aby uzyskać więcej informacji na temat routingu tranzytowego dla klasycznych sieci wirtualnych, zobacz [blog programu Microsoft Developer](https://blogs.msdn.microsoft.com/igorpag/2015/10/01/hubspoke-daisy-chain-and-full-mesh-vnet-topologies-in-azure-arm-using-vpn-v1/).

## <a name="configure-bgp-for-a-vpn-gateway"></a>Konfigurowanie protokołu BGP dla bramy sieci VPN

Protokół BGP jest standardowym protokołem routingu używanym w Internecie do wymiany informacji o routingu i osiągalności między dwiema lub więcej sieciami. Gdy BGP jest używany w kontekście sieci wirtualnych platformy Azure, umożliwia bramy sieci VPN platformy Azure i lokalne urządzenia sieci VPN, znane jako elementy równorzędne lub sąsiadów BGP. Wymieniają "trasy", które informują obie bramy o dostępności i osiągalności tych prefiksów, aby przejść przez zaangażowane bramy lub routery. 

BGP można również włączyć routingu tranzytowego między wieloma sieciami przez propagowanie tras, które brama BGP uczy się od jednego elementu równorzędnego BGP do wszystkich innych elementów równorzędnych BGP. Aby uzyskać więcej informacji, zobacz [Omówienie protokołu BGP z usługą Azure VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-overview).

### <a name="configure-bgp-for-a-vpn-connection"></a>Konfigurowanie protokołu BGP dla połączenia sieci VPN

Aby skonfigurować połączenie sieci VPN korzystające z protokołu BGP, zobacz [Jak skonfigurować usługę BGP w bramkach sieci Azure w sieci VPN przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-bgp-resource-manager-ps).

Włącz usługę BGP na bramie sieci wirtualnej, tworząc dla niej numer systemu autonomicznego (AS). Bramy podstawowe nie obsługują protokołu BGP. Aby sprawdzić jednostkę SKU bramy, przejdź do sekcji **Omówienie** bloku **bramy sieci VPN** w witrynie Azure portal. Jeśli jednostka SKU jest **podstawowa,** musisz zmienić jednostkę SKU (patrz [Zmiana rozmiaru bramy)](https://docs.microsoft.com/powershell/module/azurerm.network/resize-azurermvirtualnetworkgateway?view=azurermps-4.1.0&viewFallbackFrom=azurermps-4.0.0)na **VpnGw1**. 

Sprawdzenie jednostki SKU spowoduje 20 do 30 minut przestoju. Tak szybko, jak brama ma poprawną jednostkę SKU, można dodać numer AS za pomocą [polecenia Set-AzureRmVirtualNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgateway?view=azurermps-3.8.0) PowerShell. Po skonfigurowaniu numeru AS adres IP elementu równorzędnego BGP dla bramy zostanie podany automatycznie.

Należy ręcznie podać `LocalNetworkGateway` numer AS i adres równorzędny BGP. Można ustawić `ASN` i `-BgpPeeringAddress` wartości za pomocą [polecenia New-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermlocalnetworkgateway?view=azurermps-4.1.0) lub [Set-AzureRmLocalNetworkGateway](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermlocalnetworkgateway?view=azurermps-4.1.0) PowerShell polecenia. Niektóre numery AS są zarezerwowane dla platformy Azure i nie można ich używać zgodnie z opisem w [informacje o BGP z usługą Azure VPN Gateway.](../vpn-gateway/vpn-gateway-bgp-overview.md#faq)

Obiekt połączenia musi mieć włączony protokół BGP. Wartość można `-EnableBGP` ustawić `$True` na [new-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/new-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0) lub [Set-AzureRmVirtualNetworkGatewayConnection](https://docs.microsoft.com/powershell/module/azurerm.network/set-azurermvirtualnetworkgatewayconnection?view=azurermps-4.1.0).

### <a name="validate-the-bgp-configuration"></a>Sprawdzanie poprawności konfiguracji protokołu BGP

Aby sprawdzić, czy BGP jest poprawnie `get-AzureRmVirtualNetworkGateway` skonfigurowany, można uruchomić polecenia i `get-AzureRmLocalNetworkGateway` polecenia. Następnie można zauważyć wyjście związane z `BgpSettingsText` BGP w części. Przykład:

```
{

"Asn": AsnNumber,

"BgpPeeringAddress": "IP address",

"PeerWeight": 0

}
```

## <a name="create-a-highly-available-activeactive-vpn-connection"></a>Tworzenie wysoce dostępnego aktywnego/aktywnego połączenia sieci VPN

Kluczowe różnice między bramami aktywnymi/aktywnymi i aktywnymi/rezerwowymi to:

* Należy utworzyć dwie konfiguracje IP bramy z dwoma publicznymi adresami IP.
* Należy ustawić flagę **EnableActiveActiveFeature.**
* Jednostka SKU bramy musi być **VpnGw1**, **VpnGw2**lub **VpnGw3**.

Aby osiągnąć wysoką dostępność dla łączności międzylokacjowej i sieciowej, należy wdrożyć wiele bram sieci VPN i ustanowić wiele połączeń równoległych między sieciami a platformą Azure. Aby zapoznać się z omówieniem opcji łączności i topologii, zobacz [Wysoce dostępne połączenia międzylokacje i łączność między sieciami.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-highlyavailable)

Aby utworzyć aktywne/aktywne połączenia międzylokacyjnymi i sieciowo-sieciowe, postępuj zgodnie z instrukcjami w [temacie Konfigurowanie aktywnych/aktywnych połączeń sieci VPN S2S z bramami sieci VPN platformy Azure,](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-activeactive-rm-powershell) aby skonfigurować bramę sieci VPN platformy Azure w trybie aktywnym/aktywnym.

> [!Note]  
> * Po dodaniu adresów do bramy sieci lokalnej w trybie aktywnym/aktywnym z włączoną funkcją BGP *należy dodać tylko adresy /32 elementów równorzędnych BGP*. Jeśli dodasz więcej adresów, będą one traktowane jako trasy statyczne i mają pierwszeństwo przed trasami BGP.
> * Należy użyć różnych numerów BGP AS dla sieci lokalnych, które łączą się z platformą Azure. (Jeśli są takie same, musisz zmienić numer AS sieci wirtualnej, jeśli lokalne urządzenie sieci VPN już używa asn do obsługi równorzędnej z innymi sąsiadami BGP.)

## <a name="change-an-azure-vpn-gateway-type-after-deployment"></a>Zmienianie typu bramy sieci VPN platformy Azure po wdrożeniu

Nie można zmienić typu bramy sieci wirtualnej platformy Azure z opartego na zasadach na oparte na marszrutach lub w inny sposób bezpośrednio. Najpierw należy usunąć bramę. Następnie adres IP i klucz wstępny nie zostaną zachowane. Następnie można utworzyć nową bramę żądanego typu. 

Aby usunąć i utworzyć bramę, wykonaj następujące czynności:

1. Usuń wszystkie połączenia skojarzone z oryginalną bramą.
2. Usuń bramę przy użyciu portalu Azure, programu PowerShell lub klasycznego programu PowerShell: 
   * [Usuwanie bramy sieci wirtualnej przy użyciu witryny Azure portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-portal)
   * [Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-powershell)
   * [Usuwanie bramy sieci wirtualnej przy użyciu programu PowerShell (klasyczny)](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-delete-vnet-gateway-classic-powershell)
3. Wykonaj kroki opisane w [części Utwórz bramę sieci VPN,](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#VNetGateway) aby utworzyć nową bramę żądanego typu i ukończyć konfigurację sieci VPN.

> [!Note]
> Ten proces zajmie około 60 minut.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązywanie problemów z łącznością między maszynami wirtualnymi platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-troubleshoot-connectivity-problem-between-vms)

