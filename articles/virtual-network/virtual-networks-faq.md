---
title: Często zadawane pytania dotyczące sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Odpowiedzi na najczęściej zadawane pytania dotyczące sieci wirtualnych platformy Microsoft Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: e45d5393833973889b28a95ec86b89593a091f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244812"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące sieci wirtualnych platformy Azure

## <a name="virtual-network-basics"></a>Podstawy sieci wirtualnej

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co to jest sieć wirtualna platformy Azure??
Sieć wirtualna platformy Azure (VNet) jest reprezentacją własnej sieci w chmurze. Jest logiczną izolacją chmury Azure przeznaczoną dla Twojej subskrypcji. Sieci wirtualne umożliwiają aprowizję wirtualnych sieci prywatnych (VPN) na platformie Azure i zarządzanie nimi oraz opcjonalnie łączenie sieci wirtualnych z innymi sieciami wirtualnymi na platformie Azure lub z lokalną infrastrukturą informatyczną w celu tworzenia rozwiązań hybrydowych lub międzylokalnych. Każda utworzona sieć wirtualna ma swój własny blok CIDR i może być połączona z innymi sieciami wirtualnymi i sieciami lokalnymi, o ile bloki CIDR nie nakładają się na siebie. Masz również kontrolę nad ustawieniami serwera DNS dla sieci wirtualnych i segmentacji sieci wirtualnej do podsieci.

Użyj sieci wirtualnych, aby:

* Utwórz dedykowaną sieć wirtualną tylko w chmurze prywatnej. Czasami nie wymaga konfiguracji między lokalami dla rozwiązania. Podczas tworzenia sieci wirtualnej, usługi i maszyny wirtualne w sieci wirtualnej można komunikować się bezpośrednio i bezpiecznie ze sobą w chmurze. Nadal można skonfigurować połączenia punktów końcowych dla maszyn wirtualnych i usług, które wymagają komunikacji internetowej, jako część rozwiązania.

* Bezpiecznie rozszerz swoje centrum danych. Dzięki sieciom wirtualnym można tworzyć tradycyjne sieci VPN typu lokacja lokacja (S2S), aby bezpiecznie skalować pojemność centrum danych. Sieci VPN S2S używają protokołu IPSEC do zapewnienia bezpiecznego połączenia między firmową bramą sieci VPN a platformą Azure.

* Włącz scenariusze chmury hybrydowej. Sieci wirtualne zapewniają elastyczność do obsługi wielu scenariuszy chmury hybrydowej. Aplikacje oparte na chmurze można bezpiecznie łączyć z dowolnym typem systemu lokalnego, takiego jak komputery mainframe i systemy Unix.

### <a name="how-do-i-get-started"></a>Jak rozpocząć?
Odwiedź [dokumentację sieci wirtualnej,](https://docs.microsoft.com/azure/virtual-network/) aby rozpocząć. Ta zawartość zawiera informacje o omówieniu i wdrażaniu dla wszystkich funkcji sieci wirtualnej.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Czy mogę korzystać z sieci wirtualnych bez łączności między lokalizacjami?
Tak. Sieci wirtualnej można używać bez konieczności podłączania jej do lokalu. Na przykład można uruchomić kontrolery domeny usługi Microsoft Windows Server Active Directory i farmy programu SharePoint wyłącznie w sieci wirtualnej platformy Azure.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Czy można przeprowadzić optymalizację sieci WAN między sieciami wirtualnymi lub siecią wirtualną a lokalnym centrum danych?
Tak. Urządzenie [wirtualne sieci optymalizacji sieci WAN](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) od kilku dostawców za pośrednictwem portalu Azure Marketplace.

## <a name="configuration"></a>Konfigurowanie

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jakich narzędzi używam do tworzenia sieci wirtualnej?
Do utworzenia lub skonfigurowania sieci wirtualnej można użyć następujących narzędzi:

* Portal Azure
* PowerShell
* Interfejs wiersza polecenia platformy Azure
* Plik konfiguracji sieci (netcfg - tylko dla klasycznych sieci wirtualnych). Zobacz [konfigurowanie sieci wirtualnej przy użyciu pliku konfiguracji sieci.](virtual-networks-using-network-configuration-file.md)

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Jakich zakresów adresów mogę używać w sieciach wirtualnych?
Dowolny zakres adresów IP zdefiniowany w [RFC 1918](https://tools.ietf.org/html/rfc1918). Na przykład 10.0.0.0/16. Nie można dodać następujących zakresów adresów:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Transmisja)
* 127.0.0.0/8 (Sprzężenie zwrotne)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (Wewnętrzny DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Czy mogę mieć publiczne adresy IP w sieciach wirtualnych?
Tak. Aby uzyskać więcej informacji na temat zakresów publicznych adresów IP, zobacz [Tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network). Publiczne adresy IP nie są bezpośrednio dostępne z Internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Czy istnieje ograniczenie liczby podsieci w mojej sieci wirtualnej?
Tak. Szczegółowe informacje można znaleźć w [limitach platformy Azure.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Przestrzenie adresowe podsieci nie mogą nakładać się na siebie.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Czy istnieją jakieś ograniczenia dotyczące używania adresów IP w tych podsieciach?
Tak. Platforma Azure rezerwuje 5 adresów IP dla każdej podsieci. Są to x.x.x.0-x.x.x.3 i ostatni adres podsieci. x.x.x.1-x.x.x.3 jest zarezerwowany w każdej podsieci dla usług platformy Azure.   
- x.x.x.0: Adres sieciowy
- x.x.x.1: Zarezerwowane przez platformę Azure dla bramy domyślnej
- x.x.x.2, x.x.x.3: Zarezerwowane przez platformę Azure w celu mapowania obiektów IP DNS platformy Azure do przestrzeni sieci wirtualnej
- x.x.x.255: Adres emisji sieciowej

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Jak małe i jak duże mogą być sieci wirtualne i podsieci?
Najmniejsza obsługiwana podsieć IPv4 to /29, a największa to /8 (przy użyciu definicji podsieci CIDR).  Podsieci IPv6 musi mieć dokładnie /64 w rozmiarze.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Czy mogę przenieść moje sieci VLAN na platformę Azure przy użyciu sieci wirtualnych?
Nie. Sieci wirtualne są nakładkami warstwy 3. Platforma Azure nie obsługuje semantyki warstwy 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Czy mogę określić niestandardowe zasady routingu w sieciach wirtualnych i podsieciach?
Tak. Można utworzyć tabelę marszruty i skojarzyć ją z podsiecią. Aby uzyskać więcej informacji na temat routingu na platformie Azure, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Czy sieci wirtualne obsługują multiemisji lub emisji?
Nie. Multiemisji i emisji nie są obsługiwane.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jakich protokołów można używać w sieciach wirtualnych?
Protokoły TCP, UDP i ICMP TCP/IP można używać w sieciach wirtualnych. Emisja pojedyncza jest obsługiwana w sieciach wirtualnych, z wyjątkiem protokołu DHCP (Dynamic Host Configuration Protocol) za pośrednictwem emisji pojedynczej (portu źródłowego UDP/68 / portu docelowego UDP/67). Pakiety multiemisji, emisji, pakietów zhermetyzowanych ip-in-IP i pakietów GRE (Generic Routing Encapsulation) są blokowane w sieciach wirtualnych. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Czy mogę pingować domyślne routery w sieci wirtualnej?
Nie.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Czy mogę używać tracert do diagnozowania łączności?
Nie.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Czy mogę dodać podsieci po utworzeniu sieci wirtualnej?
Tak. Podsieci można dodać do sieci wirtualnych w dowolnym momencie, o ile zakres adresów podsieci nie jest częścią innej podsieci i w zakresie adresów sieci wirtualnej pozostało miejsce.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Czy mogę zmodyfikować rozmiar podsieci po jej utworzeniu?
Tak. Możesz dodawać, usuwać, rozszerzać lub zmniejszać podsieć, jeśli nie wdrożono w niej żadnych maszyn wirtualnych ani usług.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Czy mogę modyfikować podsieci po ich utworzeniu?
Tak. Można dodawać, usuwać i modyfikować bloki CIDR używane przez wirtualną siecię wirtualną.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Jeśli używam moich usług w sieci wirtualnej, czy mogę połączyć się z Internetem?
Tak. Wszystkie usługi wdrożone w sieci wirtualnej można połączyć wychodzących z Internetem. Aby dowiedzieć się więcej o wychodzących połączeniach internetowych na platformie Azure, zobacz [Połączenia wychodzące](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli chcesz połączyć przychodzące z zasobem wdrożonym za pośrednictwem Menedżera zasobów, zasób musi mieć przypisany publiczny adres IP. Aby dowiedzieć się więcej o publicznych adresach IP, zobacz [Publiczne adresy IP](virtual-network-public-ip-address.md). Każda usługa Azure Cloud Service wdrożona na platformie Azure ma publicznie adresowalny adres VIP przypisany do niego. Definiujesz wejściowe punkty końcowe dla ról PaaS i punktów końcowych dla maszyn wirtualnych, aby umożliwić tym usługom akceptowanie połączeń z Internetu.

### <a name="do-vnets-support-ipv6"></a>Czy sieci wirtualne obsługują IPv6?
Tak, sieci wirtualne mogą być tylko IPv4 lub podwójny stos (IPv4 + IPv6).  Aby uzyskać szczegółowe informacje, zobacz [Omówienie IPv6 dla sieci wirtualnych platformy Azure](./ipv6-overview.md).

### <a name="can-a-vnet-span-regions"></a>Czy sieci wirtualnej obejmuje regiony?
Nie. Sieci wirtualnej jest ograniczona do jednego regionu. Sieć wirtualna obejmuje jednak strefy dostępności. Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview (Omówienie stref dostępności)](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Sieci wirtualne można łączyć w różnych regionach za pomocą komunikacji równorzędnej sieci wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Czy mogę połączyć sieci wirtualnej z inną siecią wirtualną na platformie Azure?
Tak. Jedną siecię wirtualną można połączyć z inną siecią wirtualną, korzystając z:
- **Komunikacja równorzędna w sieci wirtualnej**: Aby uzyskać szczegółowe informacje, zobacz [omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md)
- **Brama sieci VPN platformy Azure:** Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Rozpoznawanie nazw (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jakie są moje opcje DNS dla sieci wirtualnych?
Użyj tabeli decyzji na stronie [Rozpoznawanie nazw maszyn wirtualnych i wystąpień ról,](virtual-networks-name-resolution-for-vms-and-role-instances.md) aby poprowadzić Cię przez wszystkie dostępne opcje DNS.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Czy mogę określić serwery DNS dla sieci wirtualnej?
Tak. Adresy IP serwera DNS można określić w ustawieniach sieci wirtualnej. To ustawienie jest stosowane jako domyślne serwery DNS dla wszystkich maszyn wirtualnych w sieci wirtualnej.

### <a name="how-many-dns-servers-can-i-specify"></a>Ile serwerów DNS można określić?
Odwołanie do [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Czy mogę zmodyfikować serwery DNS po utworzeniu sieci?
Tak. Listę serwerów DNS sieci wirtualnej można zmienić w dowolnym momencie. W przypadku zmiany listy serwerów DNS należy przeprowadzić odnowienie dzierżawy DHCP na wszystkich maszynach wirtualnych, których dotyczy problem, aby nowe ustawienia DNS zostały zastosowane. W przypadku maszyn wirtualnych z systemem operacyjnym `ipconfig /renew` Windows można to zrobić, wpisując je bezpośrednio na maszynie wirtualnej. W przypadku innych typów systemu operacyjnego zapoznaj się z dokumentacją odnawiania dzierżawy DHCP dla określonego typu systemu operacyjnego. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co to jest usługa DNS dostarczona przez platformę Azure i czy działa z sieciami wirtualnymi?
Usługa DNS dostarczona przez platformę Azure to wielodostępna usługa DNS oferowana przez firmę Microsoft. Platforma Azure rejestruje wszystkie wystąpienia ról maszyn wirtualnych i usług w chmurze w tej usłudze. Ta usługa zapewnia rozpoznawanie nazw według nazwy hosta dla maszyn wirtualnych i wystąpień ról zawartych w tej samej usłudze w chmurze oraz przez nazwę FQDN dla maszyn wirtualnych i wystąpień ról w tej samej sieci wirtualnej. Aby dowiedzieć się więcej o systemie DNS, zobacz [Rozpoznawanie nazw dla wystąpień ról maszyn wirtualnych i usług w chmurze](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Istnieje ograniczenie do pierwszych 100 usług w chmurze w sieci wirtualnej dla rozpoznawania nazw między dzierżawami przy użyciu usługi DNS dostarczonej przez platformę Azure. Jeśli używasz własnego serwera DNS, to ograniczenie nie ma zastosowania.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Czy mogę zastąpić ustawienia DNS na maszynie wirtualnej lub w chmurze?
Tak. Serwery DNS na maszynę wirtualną lub usługę w chmurze można ustawić w celu zastąpienia domyślnych ustawień sieciowych. Zaleca się jednak, aby w miarę możliwości korzystać z systemu DNS w całej sieci.

### <a name="can-i-bring-my-own-dns-suffix"></a>Czy mogę zabrać ze sobą własny sufiks DNS?
Nie. Nie można określić niestandardowego sufiksu DNS dla sieci wirtualnych.

## <a name="connecting-virtual-machines"></a>Łączenie maszyn wirtualnych

### <a name="can-i-deploy-vms-to-a-vnet"></a>Czy można wdrożyć maszyny wirtualne do sieci wirtualnej?
Tak. Wszystkie interfejsy sieciowe podłączone do maszyny Wirtualnej wdrożonej za pośrednictwem modelu wdrażania Menedżera zasobów muszą być połączone z siecią wirtualną. Maszyny wirtualne wdrożone za pośrednictwem klasycznego modelu wdrażania można opcjonalnie podłączyć do sieci wirtualnej.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jakie są różne typy adresów IP, które można przypisać do maszyn wirtualnych?
* **Prywatne:** Przypisana do każdej karty sieciowej w ramach każdej maszyny wirtualnej. Adres jest przypisywany przy użyciu metody statycznej lub dynamicznej. Prywatne adresy IP są przypisywane z zakresu określonego w ustawieniach podsieci sieci wirtualnej. Zasoby wdrożone za pośrednictwem klasycznego modelu wdrażania są przypisywane prywatne adresy IP, nawet jeśli nie są one połączone z siecią wirtualną. Zachowanie metody alokacji różni się w zależności od tego, czy zasób został wdrożony za pomocą Menedżera zasobów, czy klasycznego modelu wdrażania: 

  - **Menedżer zasobów:** Prywatny adres IP przypisany z metodą dynamiczną lub statyczną pozostaje przypisany do maszyny wirtualnej (Menedżera zasobów) do momentu usunięcia zasobu. Różnica polega na tym, że można wybrać adres do przypisania podczas korzystania z usługi statycznej, a platforma Azure wybiera podczas korzystania z dynamicznych. 
  - **Klasyczny**: Prywatny adres IP przypisany z metodą dynamiczną może ulec zmianie po ponownym uruchomieniu maszyny wirtualnej (klasycznej) maszyny wirtualnej po tym, jak została zatrzymana (cofnięta). Jeśli musisz upewnić się, że prywatny adres IP zasobu wdrożonego za pośrednictwem klasycznego modelu wdrażania nigdy się nie zmienia, przypisz prywatny adres IP za pomocą metody statycznej.

* **Publiczne:** Opcjonalnie przypisane do kart sieciowych dołączonych do maszyn wirtualnych wdrożonych za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Adres można przypisać za pomocą metody alokacji statycznej lub dynamicznej. Wszystkie wystąpienia ról maszyn wirtualnych i usług w chmurze wdrożone za pośrednictwem klasycznego modelu wdrażania istnieją w usłudze w chmurze, która jest przypisana *dynamiczny,* publiczny wirtualny adres IP (VIP). Publiczny *statyczny* adres IP, nazywany [zastrzeżonym adresem IP,](virtual-networks-reserved-public-ip.md)opcjonalnie można przypisać jako adres VIP. Publiczne adresy IP można przypisać do poszczególnych maszyn wirtualnych lub wystąpień roli usług w chmurze wdrożonych za pośrednictwem klasycznego modelu wdrażania. Adresy te są nazywane [publicznym ipem ip na poziomie wystąpienia (adresy ILPIP](virtual-networks-instance-level-public-ip.md) i mogą być przypisywane dynamicznie.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Czy mogę zarezerwować prywatny adres IP dla maszyny Wirtualnej, który utworzym w późniejszym czasie?
Nie. Nie można zarezerwować prywatnego adresu IP. Jeśli prywatny adres IP jest dostępny, jest on przypisywany do maszyny Wirtualnej lub wystąpienia roli przez serwer DHCP. Maszyna wirtualna może lub nie może być tym, który ma być przypisany prywatny adres IP. Można jednak zmienić prywatny adres IP już utworzonej maszyny Wirtualnej na dowolny dostępny prywatny adres IP.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Czy prywatne adresy IP zmieniają się dla maszyn wirtualnych w sieci wirtualnej?
To zależy. Jeśli maszyna wirtualna została wdrożona za pośrednictwem Menedżera zasobów, nie, niezależnie od tego, czy adres IP został przypisany z metodą alokacji statycznej czy dynamicznej. Jeśli maszyna wirtualna została wdrożona za pośrednictwem klasycznego modelu wdrażania, dynamiczne adresy IP mogą ulec zmianie po uruchomieniu maszyny wirtualnej po tym, jak została zatrzymana (cofnięta alokacja). Adres jest zwalniany z maszyny Wirtualnej wdrożonej za pośrednictwem modelu wdrażania po usunięciu maszyny Wirtualnej.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Czy mogę ręcznie przypisać adresy IP do kart sieciowych w systemie operacyjnym maszyny Wirtualnej?
Tak, ale nie jest zalecane, chyba że jest to konieczne, na przykład podczas przypisywania wielu adresów IP do maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie wielu adresów IP do maszyny wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config). Jeśli adres IP przypisany do karty sieciowej platformy Azure dołączonej do maszyny Wirtualnej ulegnie zmianie, a adres IP w systemie operacyjnym maszyny Wirtualnej jest inny, utracisz łączność z maszyną wirtualną.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Jeśli zatrzymam gniazdo wdrażania usługi w chmurze lub zamknę maszynę wirtualną z poziomu systemu operacyjnego, co się stanie z moimi adresami IP?
Nic. Adresy IP (publiczny adres VIP, publiczny i prywatny) pozostają przypisane do gniazda wdrażania usługi w chmurze lub maszyny Wirtualnej.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Czy można przenieść maszyny wirtualne z jednej podsieci do innej podsieci w sieci wirtualnej bez ponownego rozmieszczania?
Tak. Więcej informacji można znaleźć w artykule [Jak przenieść maszynę wirtualną lub wystąpienie roli do innej podsieci.](virtual-networks-move-vm-role-to-subnet.md)

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Czy mogę skonfigurować statyczny adres MAC dla mojej maszyny Wirtualnej?
Nie. Nie można skonfigurować statycznie adresu MAC.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Czy po utworzeniu adresu MAC pozostanie taki sam dla mojej maszyny Wirtualnej?
Tak, adres MAC pozostaje taki sam dla maszyny Wirtualnej wdrożonej za pośrednictwem Menedżera zasobów i klasycznych modeli wdrażania, dopóki nie zostanie usunięty. Wcześniej adres MAC został zwolniony, jeśli maszyna wirtualna została zatrzymana (cofnięto przydział), ale teraz adres MAC jest zachowywany nawet wtedy, gdy maszyna wirtualna jest w stanie zdeokujnym. Adres MAC pozostaje przypisany do interfejsu sieciowego do czasu usunięcia interfejsu sieciowego lub zmiany prywatnego adresu IP przypisanego do podstawowej konfiguracji IP podstawowego interfejsu sieciowego. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Czy mogę połączyć się z Internetem z maszyny Wirtualnej w sieci wirtualnej?
Tak. Wszystkie wystąpienia ról maszyn wirtualnych i usług w chmurze wdrożone w sieci wirtualnej mogą łączyć się z Internetem.

## <a name="azure-services-that-connect-to-vnets"></a>Usługi platformy Azure łączące się z sieciami wirtualnymi

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Czy mogę używać aplikacji sieci Web usługi Azure App Service z siecią wirtualną?
Tak. Aplikacje sieci Web można wdrożyć w sieci wirtualnej przy użyciu środowiska usługi ASE (App Service Environment), połączyć zaplecze aplikacji z sieciami wirtualnymi za pomocą integracji sieci wirtualnej i zablokować ruch przychodzący do aplikacji za pomocą punktów końcowych usługi. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Funkcje sieciowe usługi App Service](../app-service/networking-features.md)
* [Tworzenie aplikacji sieci Web w środowisku usługi aplikacji](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrowanie aplikacji z usługą Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Ograniczenia dostępu do usługi app service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Czy można wdrożyć usługi w chmurze z rolami sieci web i procesów procesowych (PaaS) w sieci wirtualnej?
Tak. Można (opcjonalnie) wdrożyć wystąpienia roli usług w chmurze w sieciach wirtualnych. W tym celu należy określić nazwę sieci wirtualnej i mapowania roli/podsieci w sekcji konfiguracji sieci konfiguracji usługi. Nie trzeba aktualizować żadnych plików binarnych.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Czy można podłączyć skalę maszyny wirtualnej ustawioną z siecią wirtualną?
Tak. Należy podłączyć skalę maszyny wirtualnej ustawioną do sieci wirtualnej.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Czy istnieje pełna lista usług platformy Azure, które można wdrożyć zasoby z sieci wirtualnej?
Tak, Aby uzyskać szczegółowe informacje, zobacz [Integracja sieci wirtualnej dla usług platformy Azure](virtual-network-for-azure-services.md).

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Jak ograniczyć dostęp do zasobów usługi Azure PaaS z sieci wirtualnej?

Zasoby wdrożone za pośrednictwem niektórych usług PaaS platformy Azure (takich jak Usługa Azure Storage i usługa Azure SQL Database), mogą ograniczać dostęp do sieci wirtualnej sieci wirtualnej za pomocą punktów końcowych usługi sieci wirtualnej lub łącza prywatnego platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md), [Łącze prywatne platformy Azure](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Czy mogę przenieść moje usługi do i z sieci wirtualnych?
Nie. Nie można przenosić usług do i z sieci wirtualnych. Aby przenieść zasób do innej sieci wirtualnej, należy usunąć i ponownie wdrożyć zasób.

## <a name="security"></a>Zabezpieczenia

### <a name="what-is-the-security-model-for-vnets"></a>Co to jest model zabezpieczeń sieci wirtualnych?
Sieci wirtualne są odizolowane od siebie, a inne usługi hostowane w infrastrukturze platformy Azure. Sieci wirtualnej jest granicą zaufania.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Czy można ograniczyć przepływ ruchu przychodzącego lub wychodzącego do zasobów połączonych z siecią wirtualną?
Tak. [Sieciowe grupy zabezpieczeń](security-overview.md) można stosować do poszczególnych podsieci w sieci wirtualnej, kart sieciowych dołączonych do sieci wirtualnej lub obu tych ekwi.).

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Czy można zaimplementować zaporę między zasobami połączonymi z siecią wirtualną?
Tak. Urządzenie [wirtualne sieci zapory](https://azure.microsoft.com/marketplace/?term=firewall) od kilku dostawców można wdrożyć za pośrednictwem portalu Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Czy dostępne są informacje dotyczące zabezpieczania sieci wirtualnych?
Tak. Aby uzyskać szczegółowe informacje, zobacz [Omówienie zabezpieczeń sieci platformy Azure](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>Interfejsy API, schematy i narzędzia

### <a name="can-i-manage-vnets-from-code"></a>Czy mogę zarządzać sieciami wirtualnymi z kodu?
Tak. Interfejsy API REST dla sieci wirtualnych można używać w [usłudze Azure Resource Manager](/rest/api/virtual-network) i [klasycznych](https://go.microsoft.com/fwlink/?LinkId=296833) modelach wdrażania.

### <a name="is-there-tooling-support-for-vnets"></a>Czy istnieje obsługa narzędzi dla sieci wirtualnych?
Tak. Dowiedz się więcej o używaniu:
- Witryna Azure portal do wdrażania sieci wirtualnych za pośrednictwem [usługi Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) i [klasycznych](virtual-networks-create-vnet-classic-pportal.md) modeli wdrażania.
- Program PowerShell do zarządzania sieciami wirtualnymi wdrożonymi za pośrednictwem [Menedżera zasobów](/powershell/module/az.network) i [klasycznych](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) modeli wdrażania.
- Interfejs wiersza polecenia platformy Azure (CLI) do wdrażania sieci wirtualnych wdrożonych za pośrednictwem [Menedżera zasobów](/cli/azure/network/vnet) i [klasycznych](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) modeli wdrażania oraz zarządzania nimi.  

## <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

### <a name="what-is-vnet-peering"></a>Co to jest komunikacja równorzędna sieci wirtualnej?
Komunikacja równorzędna sieci wirtualnej (lub komunikacja równorzędna sieci wirtualnej) umożliwia łączenie sieci wirtualnych. Połączenie równorzędne sieci wirtualnej między sieciami wirtualnymi umożliwia kierowanie ruchu między nimi prywatnie za pośrednictwem adresów IPv4. Maszyny wirtualne w sieciach wirtualnych komunikacji równorzędnej mogą komunikować się ze sobą tak, jakby mieściły się w tej samej sieci. Te sieci wirtualne mogą znajdować się w tym samym regionie lub w różnych regionach (nazywanych również globalną komunikacją równorzędną sieci wirtualnej). Połączenia równorzędne sieci wirtualnej można również utworzyć w ramach subskrypcji platformy Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Czy można utworzyć połączenie komunikacji równorzędnej z siecią wirtualną w innym regionie?
Tak. Globalna sieć wirtualna komunikacji równorzędnej umożliwia równorzędne sieci wirtualne w różnych regionach. Globalna komunikacja równorzędna sieci wirtualnej jest dostępna we wszystkich regionach publicznych platformy Azure, regionach chmury w Chinach i regionach chmury dla instytucji rządowych. Nie można globalnie równorzędne z regionów publicznych platformy Azure do regionów chmury krajowej.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Jakie są ograniczenia związane z globalną komunikacją równorzędnościową i modułami równoważenia obciążenia?
Jeśli dwie sieci wirtualne w dwóch różnych regionach są równorzędne za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej, nie można połączyć się z zasobami, które znajdują się za podstawowym modułem równoważenia obciążenia za pośrednictwem frontowego adresu IP modułu równoważenia obciążenia. To ograniczenie nie istnieje dla standardowego modułu równoważenia obciążenia.
Następujące zasoby można użyć podstawowych modułów równoważenia obciążenia, co oznacza, że nie można uzyskać do nich dotrzeć za pośrednictwem frontowego adresu IP modułu równoważenia obciążenia za pośrednictwem globalnej komunikacji równorzędnej sieci wirtualnej. Można jednak użyć globalnej sieci wirtualnej komunikacji równorzędnej, aby dotrzeć do zasobów bezpośrednio za pośrednictwem ich prywatnych adresów IP sieci wirtualnej, jeśli jest to dozwolone. 
- Maszyny wirtualne za podstawowymi modułami równoważenia obciążenia
- Zestawy skalowania maszyny wirtualnej z podstawowymi modułami równoważenia obciążenia 
- Pamięć podręczna Redis 
- SKU bramy aplikacji (w wersji 1)
- Service Fabric
- SQL MI
- API Management
- Usługa domenowa Active Directory (DODAJE)
- Logic Apps
- HDInsight
-   Azure Batch
- Środowisko usługi App Service

Można połączyć się z tymi zasobami za pośrednictwem usługi ExpressRoute lub sieci wirtualnej do sieci wirtualnej za pośrednictwem bram sieci wirtualnej.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Czy mogę włączyć komunikację równorzędną sieci wirtualnej, jeśli moje sieci wirtualne należą do subskrypcji w różnych dzierżawach usługi Azure Active Directory?
Tak. Istnieje możliwość ustanowienia komunikacji równorzędnej sieci wirtualnej (lokalnej lub globalnej), jeśli subskrypcje należą do różnych dzierżaw usługi Azure Active Directory. Można to zrobić za pośrednictwem programu PowerShell lub cli. Portal nie jest jeszcze obsługiwany.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Moje połączenie równorzędne sieci wirtualnej jest w stanie *inicjowanym,* dlaczego nie mogę się połączyć?
Jeśli połączenie komunikacji równorzędnej jest w stanie *zainicjowanym,* oznacza to, że utworzono tylko jedno łącze. Aby ustanowić pomyślne połączenie, należy utworzyć łącze dwukierunkowe. Na przykład do równorzędnej sieci wirtualnej A do sieci wirtualnej B, łącze musi zostać utworzone z sieci wirtualnej do sieci wirtualnej i z sieci wirtualnej do sieci wirtualnej. Utworzenie obu łączy spowoduje zmianę stanu na *Połączony*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Moje połączenie równorzędne sieci wirtualnej jest w stanie *Rozłączony,* dlaczego nie mogę utworzyć połączenia równorzędnego?
Jeśli połączenie równorzędne sieci wirtualnej jest w stanie *Rozłączone,* oznacza to, że jedno z utworzonych łączy zostało usunięte. Aby przywrócić połączenie komunikacji równorzędnej, należy usunąć łącze i ponownie utworzyć je.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Czy mogę równorzędnie sieci wirtualnej z siecią wirtualną w innej subskrypcji?
Tak. Sieci wirtualne można równorzędne w ramach subskrypcji i między regionami.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Czy mogę równorzędne dwie sieci wirtualne z pasującymi lub nakładającymi się zakresami adresów?
Nie. Przestrzenie adresowe nie mogą nakładać się na siebie, aby włączyć komunikację równorzędnych sieci wirtualnej.

### <a name="how-much-do-vnet-peering-links-cost"></a>Ile kosztują łącza komunikacji równorzędnej sieci wirtualnej?
Nie ma żadnych opłat za tworzenie połączenia równorzędnego sieci wirtualnej. Opłata za transfer danych między połączeniami komunikacji równorzędnej jest naliczana. [Zobacz tutaj](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Czy ruch komunikacji równorzędnej sieci wirtualnej jest szyfrowany?
Nie. Ruch między zasobami w sieciach wirtualnych w trybie równorzędnym jest prywatny i izolowany. Pozostaje całkowicie na Microsoft Backbone.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Dlaczego moje połączenie komunikacji równorzędnej jest w stanie *Rozłączony?*
Połączenia równorzędne sieci wirtualnej przechodzą do stanu *Rozłączone,* gdy jedno łącze równorzędne sieci wirtualnej zostanie usunięte. Aby ponownie nawiązać pomyślne połączenie komunikacji równorzędnej, należy usunąć oba łącza.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Jeśli równorzędne VNetA do sieci wirtualnej i równorzędne VNetB do sieci wirtualnej, czy to oznacza, VNetA i VNetC są równorzędne?
Nie. Przechodnie komunikacji równorzędnej nie jest obsługiwane. Aby to miało miejsce, należy wziąć udział w traktacie sieci wirtualnej i sieci wirtualnej.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Czy istnieją ograniczenia przepustowości dla połączeń równorzędnych?
Nie. Komunikacja równorzędna sieci wirtualnych, lokalna lub globalna, nie nakłada żadnych ograniczeń przepustowości. Przepustowość jest ograniczona tylko przez maszynę wirtualną lub zasób obliczeniowy.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Jak rozwiązać problemy z komunikacją równorzędna sieci wirtualnej?
Oto [przewodnik po rozwiązywaniu problemów,](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) który możesz wypróbować.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Które regiony platformy Azure są dostępne dla sieci wirtualnej TAP?
Wersja zapoznawcza TAP sieci wirtualnej jest dostępna we wszystkich regionach platformy Azure. Monitorowane interfejsy sieciowe, zasób TAP sieci wirtualnej oraz rozwiązanie do tworzenia lub analizy muszą zostać wdrożone w tym samym regionie.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Czy funkcja TAP sieci wirtualnej obsługuje funkcje filtrowania pakietów dublowanych?
Funkcje filtrowania nie są obsługiwane w wersji zapoznawczej tap sieci wirtualnej. Gdy konfiguracja TAP jest dodawana do interfejsu sieciowego, głęboka kopia całego ruchu przychodzącego i wychodzącego w interfejsie sieciowym jest przesyłana strumieniowo do miejsca docelowego TAP.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Czy do monitorowanego interfejsu sieciowego można dodać wiele konfiguracji TAP?
Monitorowany interfejs sieciowy może mieć tylko jedną konfigurację TAP. Skontaktuj się z indywidualnym [rozwiązaniem partnerskim,](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) aby uzyskać możliwość przesyłania strumieniowego wielu kopii ruchu TAP do wybranych narzędzi analitycznych.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Czy ten sam zasób TAP sieci wirtualnej agreguje ruch z monitorowanych interfejsów sieciowych w więcej niż jednej sieci wirtualnej?
Tak. Ten sam zasób TAP sieci wirtualnej może służyć do agregowania dublowanego ruchu z monitorowanych interfejsów sieciowych w sieciach wirtualnych równorzędnych w tej samej subskrypcji lub innej subskrypcji. Zasób TAP sieci wirtualnej i docelowy moduł równoważenia obciążenia lub docelowy interfejs sieciowy muszą znajdować się w tej samej subskrypcji. Wszystkie subskrypcje muszą znajdować się w tej samej dzierżawie usługi Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Czy są jakieś zagadnienia dotyczące wydajności ruchu produkcyjnego, jeśli włączę konfigurację TAP sieci wirtualnej w interfejsie sieciowym?

Tap sieci wirtualnej jest w wersji zapoznawczej. Podczas podglądu nie ma umowy dotyczącej poziomu usług. Możliwości nie powinny być używane dla obciążeń produkcyjnych. Gdy interfejs sieci maszyny wirtualnej jest włączony z konfiguracją TAP, te same zasoby na hoście platformy Azure przydzielone do maszyny wirtualnej w celu wysłania ruchu produkcyjnego są używane do wykonywania funkcji dublowania i wysyłania pakietów dublowanych. Wybierz odpowiedni rozmiar maszyny wirtualnej [systemu Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows,](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) aby upewnić się, że maszyny wirtualnej są dostępne wystarczające zasoby do wysyłania ruchu produkcyjnego i ruchu dublowanego.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Czy przyspieszona sieć dla [systemu Linux](create-vm-accelerated-networking-cli.md) lub [Windows](create-vm-accelerated-networking-powershell.md) jest obsługiwana za pomocą tap sieci wirtualnej?

Można dodać konfigurację TAP w interfejsie sieciowym dołączonym do maszyny wirtualnej, która jest włączona z przyspieszoną siecią. Ale wydajność i opóźnienie na maszynie wirtualnej będzie mieć wpływ na dodanie konfiguracji TAP, ponieważ odciążanie dla dublowania ruchu nie jest obecnie obsługiwane przez przyspieszone sieci platformy Azure.

## <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Jaka jest właściwa sekwencja operacji do konfigurowania punktów końcowych usługi w usłudze platformy Azure?
Istnieją dwa kroki, aby zabezpieczyć zasób usługi platformy Azure za pośrednictwem punktów końcowych usługi:
1. Włącz punkty końcowe usługi dla usługi Platformy Azure.
2. Konfigurowanie list ACL sieci wirtualnych w usłudze Platformy Azure.

Pierwszy krok to operacja po stronie sieci, a drugi to operacja po stronie zasobów usługi. Oba kroki mogą być wykonywane przez tego samego administratora lub różnych administratorów na podstawie uprawnień RBAC przyznanych roli administratora. Zaleca się, aby najpierw włączyć punkty końcowe usługi dla sieci wirtualnej przed skonfigurowanie list ACL sieci wirtualnych po stronie usługi Platformy Azure. W związku z tym kroki muszą być wykonywane w sekwencji wymienione powyżej, aby skonfigurować punkty końcowe usługi sieci wirtualnej.

>[!NOTE]
> Obie operacje opisane powyżej muszą zostać zakończone, zanim będzie można ograniczyć dostęp usługi platformy Azure do dozwolonej sieci wirtualnej i podsieci. Tylko włączenie punktów końcowych usługi dla usługi Azure po stronie sieci nie zapewnia ograniczonego dostępu. Ponadto należy również skonfigurować listy ACL sieci wirtualnych po stronie usługi platformy Azure.

Niektóre usługi (takie jak SQL i CosmosDB) zezwalają na wyjątki od powyższej sekwencji za pośrednictwem **ignoreMissingVserviceEndpoint** flagi. Po ustawieniu flagi **true,** listy ACL sieci wirtualnych można ustawić po stronie usługi platformy Azure przed skonfigurowaniem punktów końcowych usługi po stronie sieci. Usługi platformy Azure zapewniają tę flagę, aby pomóc klientom w przypadkach, gdy określone zapory IP są skonfigurowane w usługach platformy Azure i włączenie punktów końcowych usługi po stronie sieci może prowadzić do spadku łączności, ponieważ źródłowy adres IP zmienia się z publicznego adresu IPv4 na adres prywatny. Konfigurowanie list ACL sieci wirtualnych po stronie usługi platformy Azure przed ustawieniem punktów końcowych usługi po stronie sieci może pomóc uniknąć spadku łączności.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Czy wszystkie usługi platformy Azure znajdują się w sieci wirtualnej platformy Azure dostarczonej przez klienta? Jak punkt końcowy usługi sieci wirtualnej działa z usługami platformy Azure?

Nie, nie wszystkie usługi platformy Azure znajdują się w sieci wirtualnej klienta. Większość usług danych platformy Azure, takich jak Usługa Azure Storage, Azure SQL i Azure Cosmos DB, to usługi z wieloma dzierżawcami, do których można uzyskać dostęp za pośrednictwem publicznych adresów IP. Więcej informacji na temat integracji sieci wirtualnej dla usług platformy Azure można znaleźć [tutaj.](virtual-network-for-azure-services.md) 

Podczas korzystania z funkcji punktów końcowych usługi sieci wirtualnej (włączanie punktu końcowego usługi sieci wirtualnej po stronie sieci sieci wirtualnej i konfigurowanie odpowiednich list ACL sieci wirtualnych po stronie usługi platformy Azure), dostęp do usługi platformy Azure jest ograniczony z dozwolonej sieci wirtualnej i podsieci.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>W jaki sposób punkt końcowy usługi sieci wirtualnej zapewnia bezpieczeństwo?

Funkcja punktu końcowego usługi sieci wirtualnej (włączanie punktu końcowego usługi sieci wirtualnej po stronie sieci sieci wirtualnej i konfigurowanie odpowiednich list ACL sieci wirtualnych po stronie usługi platformy Azure) ogranicza dostęp usługi platformy Azure do dozwolonej sieci wirtualnej i podsieci, zapewniając w ten sposób zabezpieczenia na poziomie sieci i izolacji ruchu usługi platformy Azure. Cały ruch przy użyciu punktów końcowych usługi sieci wirtualnej przepływa przez szkielet firmy Microsoft, zapewniając w ten sposób kolejną warstwę izolacji od publicznego Internetu. Ponadto klienci mogą w pełni usunąć publiczny dostęp do internetu do zasobów usługi platformy Azure i zezwolić na ruch tylko z ich sieci wirtualnej za pośrednictwem kombinacji zapory IP i list ACL sieci wirtualnych, chroniąc w ten sposób zasoby usługi platformy Azure przed nieautoryzowanymi Dostęp.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Co chroni punkt końcowy usługi sieci wirtualnej — zasoby sieci wirtualnej lub usługi platformy Azure?
Punkty końcowe usługi sieci wirtualnej pomagają chronić zasoby usługi platformy Azure. Zasoby sieci wirtualnych są chronione za pośrednictwem sieciowych grup zabezpieczeń (NSG).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Czy istnieje koszt korzystania z punktów końcowych usługi sieci wirtualnej?

Nie, nie ma żadnych dodatkowych kosztów przy użyciu punktów końcowych usługi sieci wirtualnej.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Czy mogę włączyć punkty końcowe usługi sieci wirtualnej i skonfigurować listy ACL sieci wirtualnych, jeśli sieć wirtualna i zasoby usługi platformy Azure należą do różnych subskrypcji?

Tak, jest to możliwe. Sieci wirtualne i zasoby usługi platformy Azure mogą znajdować się w tych samych lub różnych subskrypcjach. Jedynym wymaganiem jest, że zarówno sieci wirtualnej i zasobów usługi platformy Azure musi znajdować się w tej samej dzierżawy usługi Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Czy mogę włączyć punkty końcowe usługi sieci wirtualnej i skonfigurować listy ACL sieci wirtualnych, jeśli sieć wirtualna i zasoby usługi platformy Azure należą do różnych dzierżaw ad?
Nie, punkty końcowe usługi sieci wirtualnej i listy ACL sieci wirtualnej nie są obsługiwane przez dzierżawy usługi AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Czy adres IP urządzenia lokalnego, który jest połączony za pośrednictwem bramy sieci wirtualnej platformy Azure (VPN) lub bramy usługi ExpressRoute, może uzyskiwać dostęp do usługi PaaS usługi Azure za pośrednictwem punktów końcowych usługi sieci wirtualnej?
Domyślnie nie można uzyskać dostępu do zasobów usługi platformy Azure zabezpieczonych w sieciach wirtualnych z sieci lokalnych. Jeśli chcesz zezwolić na ruch z lokalnego, należy również zezwolić na publiczne (zazwyczaj, NAT) adresy IP z lokalnego lub usługi ExpressRoute. Te adresy IP można dodać za pośrednictwem konfiguracji zapory IP dla zasobów usługi platformy Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Czy mogę używać funkcji punktu końcowego usługi sieci wirtualnej do zabezpieczania usługi platformy Azure w wielu podsieciach w sieci wirtualnej lub w wielu sieciach wirtualnych?
Aby zabezpieczyć usługi platformy Azure do wielu podsieci w sieci wirtualnej lub w wielu sieciach wirtualnych, włącz punkty końcowe usługi po stronie sieci w każdej z podsieci niezależnie, a następnie zabezpiecz zasoby usługi platformy Azure dla wszystkich podsieci, konfiguruj odpowiednie listy ACL sieci wirtualnych po stronie usługi platformy Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Jak filtrować ruch wychodzący z sieci wirtualnej do usług platformy Azure i nadal korzystać z punktów końcowych usługi?
jeśli chcesz sprawdzić lub filtrować ruch kierowany do usługi platformy Azure z sieci wirtualnej, możesz wdrożyć urządzenie sieci wirtualnej w ramach tej sieci wirtualnej. Następnie można zastosować punkty końcowe usługi do podsieci, w której wdrożono wirtualne urządzenie sieciowe i zabezpieczyć zasoby usługi platformy Azure tylko do tej podsieci za pośrednictwem list ACL sieci wirtualnej. W tym scenariuszu może być również przydatne, jeśli chcesz ograniczyć dostęp do usługi platformy Azure z sieci wirtualnej tylko do określonych zasobów platformy Azure przy użyciu filtrowania urządzeń wirtualnych sieci. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Co się stanie, gdy uzyskujesz dostęp do konta usługi platformy Azure, które ma włączoną listę kontroli dostępu do sieci wirtualnej (ACL) spoza sieci wirtualnej?
Zwracany jest błąd HTTP 403 lub HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Czy podsieci sieci wirtualnej są tworzone w różnych regionach, które mogą uzyskiwać dostęp do konta usługi platformy Azure w innym regionie? 
Tak, w przypadku większości usług platformy Azure sieci wirtualne utworzone w różnych regionach mogą uzyskiwać dostęp do usług platformy Azure w innym regionie za pośrednictwem punktów końcowych usługi sieci wirtualnej. Na przykład jeśli konto usługi Azure Cosmos DB znajduje się w zachodnie stany USA lub wschodnie stany USA, a sieci wirtualne znajdują się w wielu regionach, sieć wirtualna może uzyskać dostęp do usługi Azure Cosmos DB. Magazyn i SQL są wyjątkami i mają charakter regionalny i zarówno sieci wirtualnej i usługi platformy Azure muszą być w tym samym regionie.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Czy usługa platformy Azure może mieć zarówno listy ACL sieci wirtualnej, jak i zaporę IP?
Tak, sieć wirtualna ACL i zapora IP mogą współistnieć. Obie funkcje uzupełniają się wzajemnie, aby zapewnić izolację i bezpieczeństwo.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Co się stanie, jeśli usuniesz sieć wirtualną lub podsieć z włączonym punktem końcowym usługi dla usługi platformy Azure?
Usuwanie sieci wirtualnych i podsieci są niezależnymi operacjami i są obsługiwane nawet wtedy, gdy punkty końcowe usługi są włączone dla usług platformy Azure. W przypadkach, gdy usługi platformy Azure mają skonfigurowane listy ACL sieci wirtualnych dla tych sieci wirtualnych i podsieci, informacje o kolejce ACL sieci wirtualnej skojarzone z tą usługą platformy Azure są wyłączone, gdy sieci wirtualnej lub podsieci z włączonym punktem końcowym usługi sieci wirtualnej zostaną usunięte.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Co się stanie, jeśli konto usługi platformy Azure z włączonym punktem końcowym usługi sieci wirtualnej zostanie usunięte?
Usunięcie konta usługi platformy Azure jest niezależną operacją i jest obsługiwane nawet wtedy, gdy punkt końcowy usługi jest włączony po stronie sieci sieci wirtualnych, a listy ACL sieci wirtualnych są wyrejestowane po stronie usługi platformy Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Co się stanie ze źródłowym adresem IP zasobu (takiego jak maszyna wirtualna w podsieci), który ma włączony punkt końcowy usługi sieci wirtualnej?
Gdy punkty końcowe usługi sieci wirtualnej są włączone, źródłowe adresy IP zasobów w podsieci sieci wirtualnej przełączają się od używania publicznych adresów IPV4 do prywatnych adresów IP sieci azure dla ruchu do usługi Azure. Należy zauważyć, że może to spowodować, że określone zapory IP, które są ustawione na publiczny adres IPV4 wcześniej w usługach platformy Azure zakończy się niepowodzeniem. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Czy trasa punktu końcowego usługi zawsze ma pierwszeństwo?
Punkty końcowe usługi dodać trasę systemową, która ma pierwszeństwo przed trasami BGP i zapewnia optymalne routingu dla ruchu punktu końcowego usługi. Punkty końcowe usługi zawsze przyjmują ruch usługi bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Microsoft Azure. Aby uzyskać więcej informacji o tym, jak platforma Azure wybiera trasę, zobacz [Routing ruchu sieciowego usługi Azure Virtual](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Jak sieciowej sieciowej sieciowej w podsieci działa z punktami końcowymi usługi?
Aby uzyskać dostęp do usługi Azure, sieciowe sieciowe muszą zezwalać na łączność wychodzącą. Jeśli sieciowe sieciowe są otwarte dla całego ruchu wychodzącego w Internecie, ruch punktu końcowego usługi powinien działać. Ruch wychodzący można również ograniczyć do usług ip tylko przy użyciu tagów usługi.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Jakie uprawnienia są potrzebne do skonfigurowania punktów końcowych usługi?
Punkty końcowe usługi mogą być konfigurowane w sieci wirtualnej niezależnie przez użytkownika z dostępem do zapisu do sieci wirtualnej. Aby zabezpieczyć zasoby usługi platformy Azure do sieci wirtualnej, użytkownik musi mieć uprawnienia **Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action** dla dodawanych podsieci. To uprawnienie jest domyślnie uwzględnione we wbudowanej roli administratora usługi i można je modyfikować, tworząc role niestandardowe. Dowiedz się więcej na temat wbudowanych ról i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Czy można filtrować ruch sieci wirtualnej do usług platformy Azure, zezwalając tylko na określone zasoby usługi platformy Azure za pośrednictwem punktów końcowych usługi sieci wirtualnej? 

Zasady punktu końcowego usługi sieci wirtualnej (VNet) umożliwiają filtrowanie ruchu sieci wirtualnej do usług platformy Azure, umożliwiając tylko określone zasoby usługi platformy Azure za pośrednictwem punktów końcowych usługi. Zasady punktu końcowego zapewniają szczegółową kontrolę dostępu od ruchu sieci wirtualnej do usług platformy Azure. Więcej informacji na temat zasad punktu końcowego usługi można znaleźć [tutaj](virtual-network-service-endpoint-policies-overview.md).

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Czy usługa Azure Active Directory (Azure AD) obsługuje punkty końcowe usługi sieci wirtualnej?

Usługa Azure Active Directory (Azure AD) nie obsługuje punktów końcowych usługi natywnie. Pełną listę usług platformy Azure obsługujących punkty końcowe usługi sieci wirtualnej można zobaczyć [tutaj](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview). Należy zauważyć, że tag "Microsoft.AzureActiveDirectory" wymieniony w obszarze usług obsługujących punkty końcowe usługi jest używany do obsługi punktów końcowych usługi do ADLS Gen 1. W przypadku usługi ADLS Gen 1 integracja sieci wirtualnej dla usługi Azure Data Lake Storage Gen1 korzysta z zabezpieczeń punktu końcowego usługi sieci wirtualnej między siecią wirtualną a usługą Azure Active Directory (Azure AD) w celu wygenerowania dodatkowych oświadczeń zabezpieczeń w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu. Dowiedz się więcej o [integracji sieci wirtualnej usługi Azure Data Lake Store gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Czy są jakieś limity dotyczące liczby punktów końcowych usługi sieci wirtualnej, które można skonfigurować z sieci wirtualnej?
Nie ma limitu całkowitej liczby punktów końcowych usługi sieci wirtualnej w sieci wirtualnej. W przypadku zasobu usługi platformy Azure (takiego jak konto usługi Azure Storage) usługi mogą wymuszać limity liczby podsieci używanych do zabezpieczania zasobu. W poniższej tabeli przedstawiono kilka przykładowych limitów: 

|||
|---|---|
|Usługa platformy Azure| Limity reguł sieci wirtualnych|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Centrum zdarzeń Azure|   128|
|Azure Service Bus| 128|
|Usługa Azure Data Lake Store w wersji 1|  100|
 
>[!NOTE]
> Limity są poddawane zmianom według uznania usługi platformy Azure. Szczegółowe informacje na temat usług można znaleźć w odpowiedniej dokumentacji serwisowej. 




  



