---
title: Sieć wirtualna platformy Azure — często zadawane pytania
titlesuffix: Azure Virtual Network
description: Odpowiedzi na najczęściej zadawane pytania na temat sieci wirtualnych Microsoft Azure.
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
ms.openlocfilehash: 25c71019227c52bf0c1530dcdf655fc7575d8032
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65148518"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Sieć wirtualna platformy Azure — często zadawane pytania (FAQ)

## <a name="virtual-network-basics"></a>Wirtualne sieci — podstawy

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co to jest Azure Virtual Network (VNet)?
Usługi Azure Virtual Network (VNet) jest reprezentacją Twojej własnej sieci w chmurze. Jest logiczną izolacją chmury Azure przeznaczoną dla Twojej subskrypcji. Można Użyj sieci wirtualnych, aby aprowizować i zarządzać wirtualnych sieci prywatnych (VPN) na platformie Azure i, opcjonalnie połączyć sieci wirtualne z innymi sieciami wirtualnymi na platformie Azure lub z lokalną infrastrukturą informatyczną w celu tworzenia rozwiązań hybrydowych i obejmujących wiele lokalizacji. Każda sieć wirtualna, którą tworzysz ma bloku CIDR i mogą być połączone z innymi sieciami wirtualnymi i lokalnymi sieciami, tak długo, jak bloków CIDR nakłada się na. Masz również kontroli ustawień serwera DNS dla sieci wirtualnych i segmentacji sieci wirtualnej w podsieci.

Użyj sieci wirtualnych:

* Utwórz dedykowane prywatne tylko na chmurze sieci wirtualnej czasami nie wymagają konfiguracji między środowiskami lokalnymi dla Twojego rozwiązania. Podczas tworzenia sieci wirtualnej, usług i maszyn wirtualnych w ramach sieci wirtualnej mogą komunikować się bezpośrednio i bezpiecznie ze sobą w chmurze. Nadal można skonfigurować punktu końcowego połączenia dla maszyn wirtualnych i usług, które wymagają komunikację z Internetem, jako część rozwiązania.
* Bezpiecznie Rozszerz centrum danych przy użyciu sieci wirtualnych, możesz tworzyć tradycyjnych lokacja lokacja (S2S) sieci VPN na bezpieczne skalowanie pojemności Twojego Centrum danych. Sieci VPN S2S używaj protokołu IPSEC w celu zapewnienia bezpiecznego połączenia między korporacyjną bramą sieci VPN i usługi Azure.
* Włącz scenariuszy w chmurze hybrydowej sieci wirtualne zapewniają elastyczność obsługuje szeroką gamę scenariuszy hybrydowych w chmurze. Możesz bezpiecznie łączyć aplikacje oparte na chmurze do dowolnego typu i lokalnym systemem, takich jak Komputery mainframe i komputerach z systemem Unix.

### <a name="how-do-i-get-started"></a>Jak rozpocząć?
Odwiedź stronę [dokumentacja usługi Virtual network](https://docs.microsoft.com/azure/virtual-network/) na rozpoczęcie pracy. Ta zawartość zawiera przegląd i wdrożenie informacje dla wszystkich funkcji sieci wirtualnej.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Sieci wirtualne można używać bez łączności między środowiskami lokalnymi?
Tak. Możesz użyć sieci wirtualnej, bez łączenia go w środowisku lokalnym. Na przykład wyłącznie w sieci wirtualnej platformy Azure może uruchomić kontrolery domeny usługi Active Directory Microsoft Windows Server i farmy programu SharePoint.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Czy można wykonać Optymalizacja sieci WAN między sieciami wirtualnymi lub sieci wirtualnej i Moje lokalne centrum danych?
Tak. Możesz wdrożyć [wirtualnego urządzenia sieciowego Optymalizacja sieci WAN](https://azure.microsoft.com/marketplace/?term=wan+optimization) z kilku dostawców w portalu Azure Marketplace.

## <a name="configuration"></a>Konfigurowanie

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jakie narzędzia należy używać do tworzenia sieci wirtualnej?
Można użyć następujących narzędzi do tworzenia i konfigurowania sieci wirtualnej:

* Azure Portal
* PowerShell
* Interfejs wiersza polecenia platformy Azure
* Plik konfiguracji sieci (netcfg — tylko klasyczne sieci wirtualne). Zobacz [Konfigurowanie sieci wirtualnej przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md) artykułu.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Zakresy adresów, które można użyć w mojej sieci wirtualne?
Dowolny zakres adresów IP określonych w [RFC 1918](https://tools.ietf.org/html/rfc1918). Na przykład: 10.0.0.0/16. Nie można dodać poniższe zakresy adresów:
* 224.0.0.0/4 multiemisji)
* 255.255.255.255/32 (emisji)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Link-local)
* 168.63.129.16/32 (Internal DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>W mojej sieci wirtualne mogą mieć publiczne adresy IP?
Tak. Aby uzyskać więcej informacji na temat zakresy publicznych adresów IP, zobacz [tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network). Publiczne adresy IP nie są bezpośrednio dostępne z Internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Czy istnieje ograniczona liczba podsieci w mojej sieci wirtualnej?
Tak. Zobacz [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Aby uzyskać szczegółowe informacje. Przestrzeni adresowej podsieci nie może nakładać się na siebie nawzajem.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Czy istnieją jakiekolwiek ograniczenia dotyczące używania adresów IP w ramach tych podsieci?
Tak. Platforma Azure rezerwuje pewnych adresów IP w każdej podsieci. Pierwsze i ostatnie adresy IP każdej podsieci są zarezerwowane dla zgodności protokołów, a adresy x.x.x.1 x.x.x.3 każdej podsieci, które są używane dla usług platformy Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Jak małe i jak duży może być sieci wirtualnych i podsieci?
Najmniejsza obsługiwana podsieci jest rozmiarze/29, a największa /8 (przy użyciu definicje podsieci CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Czy mogę przenieść mój sieci VLAN na platformie Azure przy użyciu sieci wirtualnych?
Nie. Sieci wirtualne znajdują się warstwy 3 nakładki. Azure nie obsługuje żadnych semantyki warstwy 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>W mojej sieci wirtualnych i podsieci można określić niestandardowe zasady routingu?
Tak. Można utworzyć tabelę tras i skojarzyć ją z podsiecią. Aby uzyskać więcej informacji o trasach na platformie Azure, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Sieci wirtualne obsługują multiemisji lub emisji?
Nie. Multiemisji i emisji nie są obsługiwane.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jakie protokołów można używać w ramach sieci wirtualnych?
Możesz użyć protokoły TCP, UDP i ICMP TCP/IP w ramach sieci wirtualnych. Emisji pojedynczej jest obsługiwana w ramach sieci wirtualnych, z wyjątkiem DHCP Dynamic Host Configuration Protocol () za pośrednictwem emisji (port UDP/68 źródłowy / docelowy port UDP/67). Multiemisji, emisji i pakietów hermetyzowanych IP-in-IP oraz pakiety Generic Routing Encapsulation (GRE) są blokowane w ramach sieci wirtualnych. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Czy mogę wysłać polecenie ping do mojego routery domyślne w ramach sieci wirtualnej?
Nie.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Czy można używać polecenia tracert, diagnozować łączność?
Nie.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Czy można dodać podsieci po utworzeniu sieci wirtualnej?
Tak. Podsieci można dodać do sieci wirtualnych w dowolnym momencie tak długo, jak zakres adresów podsieci nie jest częścią innej podsieci i dostępnego miejsca, które pozostanie w zakresie adresów sieci wirtualnej.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Rozmiar mojej podsieci można zmodyfikować po utworzeniu?
Tak. Można dodać, usunąć, rozwiń węzeł lub zmniejszyć podsieć, jeśli nie ma żadnych maszyn wirtualnych ani usług wdrożonych w niej.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Podsieci można zmodyfikować po ich utworzeniu?
Tak. Możesz dodawać, usuwania i modyfikowania bloków CIDR posługują się z siecią wirtualną.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Jeśli używam wykaz usług w sieci wirtualnej, czy można połączyć się z Internetem?
Tak. Wszystkie usługi wdrożonego w ramach sieci wirtualnej, można połączyć z Internetem. Aby dowiedzieć się więcej na temat połączeń wychodzących internetowych na platformie Azure, zobacz [połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli chcesz się połączyć dla ruchu przychodzącego z zasobem, który został wdrożony za pomocą usługi Resource Manager, zasobu musi mieć przypisany do niego publiczny adres IP. Aby dowiedzieć się więcej na temat publicznych adresów IP, zobacz [publiczne adresy IP](virtual-network-public-ip-address.md). Każdej usługi w chmurze platformy Azure wdrożonych na platformie Azure ma adresowaną publicznie adresu VIP do niej przypisany. Należy zdefiniować wejściowych punktów końcowych dla ról PaaS i punkty końcowe dla maszyn wirtualnych włączyć te usługi do akceptowania połączeń z Internetu.

### <a name="do-vnets-support-ipv6"></a>Sieci wirtualne są obsługiwane przez protokół IPv6?
Nie. Z sieciami wirtualnymi w tej chwili nie możesz użyć protokołu IPv6. Możesz natomiast, przypisz IPv6 adresów do modułów równoważenia obciążenia Azure, aby załadować saldo maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Omówienie protokołu IPv6 dla usługi Azure Load Balancer](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Sieć wirtualną, może obejmować regionach?
Nie. Sieć wirtualna jest ograniczona do jednego regionu. Sieć wirtualna jednak zakresu strefy dostępności. Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview (Omówienie stref dostępności)](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Łączenie z sieciami wirtualnymi w różnych regionach, za pomocą komunikacji równorzędnej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Czy można połączyć sieć wirtualną z inną siecią wirtualną na platformie Azure?
Tak. Jednej sieci wirtualnej można połączyć z inną siecią wirtualną za pomocą:
- **Wirtualne sieci równorzędne**: Aby uzyskać więcej informacji, zobacz [Omówienie komunikacji równorzędnej sieci wirtualnej](virtual-network-peering-overview.md)
- **Usługi Azure VPN Gateway**: Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Rozpoznawanie nazw (domen DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jakie są moje opcje serwera DNS dla sieci wirtualnych?
Skorzystaj z tabeli decyzji na [rozpoznawania nazw dla maszyn wirtualnych i wystąpień roli](virtual-networks-name-resolution-for-vms-and-role-instances.md) dostępne opcje strony przeprowadzenie Cię za pośrednictwem systemu DNS.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Czy mogę określić, serwery DNS dla sieci wirtualnej?
Tak. W ustawieniach sieci wirtualnej, można określić adresy IP serwera DNS. To ustawienie jest stosowane jako domyślne serwery DNS dla wszystkich maszyn wirtualnych w sieci wirtualnej.

### <a name="how-many-dns-servers-can-i-specify"></a>Jak wiele serwerów DNS można określić?
Odwołanie [limitów platformy Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Moje serwery DNS można zmodyfikować po użytkownik utworzył sieci?
Tak. Lista serwerów DNS dla sieci wirtualnej można zmienić w dowolnym momencie. Jeśli zmienisz lista serwerów DNS, konieczne będzie ponowne uruchomienie wszystkich maszyn wirtualnych w sieci wirtualnej w kolejności ich do pobrania nowego serwera DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co to jest dostarczane do usługi Azure DNS i działa z sieciami wirtualnymi?
DNS platformy Azure jest oferowana przez firmę Microsoft usługa DNS dla wielu dzierżawców. Azure rejestruje wszystkich maszyn wirtualnych i wystąpień ról usługi w chmurze w tej usłudze. Ta usługa zapewnia rozpoznawanie nazwy hosta dla maszyn wirtualnych i wystąpień roli zawartych w tej samej usługi w chmurze i nazwy FQDN dla maszyn wirtualnych i wystąpień roli w tej samej sieci wirtualnej. Aby dowiedzieć się więcej o usłudze DNS, zobacz [rozpoznawania nazw dla wystąpień ról maszyn wirtualnych i usług w chmurze](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Istnieje ograniczenie do pierwsze 100 usług w chmurze w sieci wirtualnej do rozpoznawania nazw międzydzierżawowa, przy użyciu systemu DNS platformy Azure. To ograniczenie nie ma zastosowania, jeśli używasz własnego serwera DNS.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Czy można zastąpić ustawienia DNS na podstawie usługi na maszynie Wirtualnej lub w chmurze?
Tak. Możesz ustawić serwerów DNS na maszynie Wirtualnej lub usługi w chmurze, aby zastąpić domyślne ustawienia sieciowe. Jednak zalecane jest użycie możliwie w całej sieci DNS.

### <a name="can-i-bring-my-own-dns-suffix"></a>Czy mogę przenieść moją własną sufiks DNS
Nie. Nie można określić niestandardowe sufiks DNS dla sieci wirtualnych.

## <a name="connecting-virtual-machines"></a>Łączenie maszyn wirtualnych

### <a name="can-i-deploy-vms-to-a-vnet"></a>Czy mogę wdrożyć maszyny wirtualne z siecią wirtualną?
Tak. Wszystkie interfejsy sieciowe (NIC) dołączonych do maszyny Wirtualnej wdrożonej za pomocą modelu wdrażania usługi Resource Manager muszą być podłączone do sieci wirtualnej. Maszyny wirtualne wdrożone za pośrednictwem klasycznego modelu wdrażania można opcjonalnie połączyć z siecią wirtualną.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jakie są różne typy adresów IP, które można przypisać do maszyn wirtualnych?
* **Prywatny:** Przypisane do poszczególnych kart Sieciowych w ramach każdej maszyny Wirtualnej. Adres jest przypisany, używając metody statyczne lub dynamiczne. Prywatne adresy IP są przypisywane z zakresu, który określono w ustawieniach podsieci sieci wirtualnej. Zasoby wdrożone za pośrednictwem klasycznego modelu wdrażania są przypisywane prywatnych adresów IP, nawet jeśli nie masz połączenia z siecią wirtualną. Zachowanie metody alokacji różni się w zależności od tego, czy zasób został wdrożony za pomocą usługi Resource Manager lub Klasyczny model wdrażania: 

  - **Menedżer zasobów**: Przypisany przy użyciu metody dynamiczny lub statyczny prywatny adres IP pozostanie przydzielony do maszyny wirtualnej (Resource Manager), aż do usunięcia zasobu. Różnica polega na, wybierz adres, który zostanie przypisany przy użyciu statycznej i wybierze platformy Azure, korzystając z dynamicznych. 
  - **Klasyczne**: Prywatny adres IP są przypisywane przy użyciu metody dynamicznej mogą ulec zmianie, gdy maszyna wirtualna (klasyczna) ponownego uruchomienia maszyny Wirtualnej po przejściu w stan zatrzymania (przydział zostanie cofnięty). Jeśli potrzebujesz upewnić się, że prywatny adres IP zasobu wdrożone za pośrednictwem klasycznego modelu wdrażania nigdy się nie zmienia, należy przypisać prywatny adres IP za pomocą metody statycznej.

* **Publiczny:** Opcjonalnie przypisać do kart sieciowych dołączonych do maszyn wirtualnych wdrożonych za pomocą modelu wdrażania usługi Azure Resource Manager. Przy użyciu metody alokacji statycznej lub dynamicznej można przypisać adresu. Wszystkie maszyny wirtualne i usługi w chmurze wystąpień roli, wdrożone za pośrednictwem klasycznego modelu wdrażania istnieje w ramach usługi w chmurze, która jest przypisana *dynamiczne*, publiczny wirtualny adres IP (VIP). Publiczny *statyczne* adres IP, nazywany [zastrzeżony adres IP](virtual-networks-reserved-public-ip.md), opcjonalnie może być przypisany jako adres VIP. Publiczne adresy IP można przypisać do poszczególnych maszyn wirtualnych ani usług w chmurze wystąpień roli wdrożone za pośrednictwem klasycznego modelu wdrażania. Te adresy są nazywane [poziomu publicznego adresu IP wystąpienia (ILPIP](virtual-networks-instance-level-public-ip.md) adresów, a także mogą być przypisywane dynamicznie.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Czy mogę zarezerwować prywatny adres IP dla maszyny Wirtualnej, która utworzy w późniejszym czasie?
Nie. Nie można zarezerwować prywatny adres IP. Jeśli prywatny adres IP jest dostępna, jest przypisany do maszyny Wirtualnej lub wystąpienie roli przez serwer DHCP. Maszyna wirtualna może lub nie może być ten, który ma przypisany prywatny adres IP. Prywatny adres IP już utworzonej maszyny Wirtualnej, można jednak zmienić na dowolny dostępny prywatny adres IP.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Czy prywatne zmiana adresów IP dla maszyn wirtualnych w sieci wirtualnej?
To zależy. Jeśli maszyna wirtualna została wdrożona przy użyciu usługi Resource Manager nie, niezależnie od tego, czy został przypisany adres IP przy użyciu metody alokacji statycznej lub dynamicznej. Jeśli maszyna wirtualna była wdrożona za pomocą klasycznego modelu wdrażania, dynamiczne adresy IP można zmienić po uruchomieniu maszyny Wirtualnej po przejściu w stan zatrzymania (przydział zostanie cofnięty). Adres jest zwalniany z maszyny Wirtualnej wdrożone za pośrednictwem modelu wdrażania przy użyciu obu, gdy maszyna wirtualna zostanie usunięty.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Można ręcznie przypisać adresy IP do kart sieciowych w ramach systemu operacyjnego maszyny Wirtualnej?
Tak, ale nie jest to zalecane, chyba że to konieczne, takie jak kiedy przypisywanie wielu adresów IP do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Dodawanie wielu adresów IP adresy do maszyny wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config). Jeśli adres IP przypisany do karty Sieciowej z platformy Azure dołączonych do maszyny Wirtualnej zmiany i adres IP w ramach systemu operacyjnego maszyny Wirtualnej jest inny, utracić łączność z maszyną wirtualną.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Jeśli zatrzymać miejsce wdrożenia usługi w chmurze lub zamknij maszynę Wirtualną z poziomu systemu operacyjnego, co się dzieje z moich adresów IP?
Brak elementów. Adresy IP (publicznych adresów VIP, publiczne i prywatne) pozostają przypisane do miejsce wdrożenie usługi w chmurze lub maszyny Wirtualnej.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Czy mogę przenieść maszyny wirtualne z jednej podsieci do innej podsieci w sieci wirtualnej bez konieczności ponownego wdrażania?
Tak. Więcej informacji można znaleźć [sposób przenoszenia maszyny Wirtualnej lub wystąpienie roli do innej podsieci](virtual-networks-move-vm-role-to-subnet.md) artykułu.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Czy można skonfigurować statyczny adres MAC w przypadku mojej maszyny Wirtualnej?
Nie. Adres MAC nie można skonfigurować statycznie.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Adres MAC pozostaną takie same dla mojej maszyny Wirtualnej po jej utworzeniu?
Tak, adres MAC, pozostaje taka sama dla maszyny Wirtualnej wdrożone za pośrednictwem usługi Resource Manager i klasycznych modeli wdrażania, dopóki zostanie usunięty. Adres MAC zostało wydane wcześniej, jeśli maszyna wirtualna została zatrzymana (przydział zostanie cofnięty), ale teraz adres MAC jest zachowywane nawet wtedy, gdy maszyna wirtualna jest w stanie cofnięcia przydziału. Adres MAC jest przypisana do interfejsu sieciowego, do momentu usunięcia interfejsu sieciowego lub prywatny adres IP przypisany do podstawowa konfiguracja adresów IP podstawowy interfejs sieciowy zostanie zmieniony. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Czy można połączyć się z Internetem z maszyny Wirtualnej w sieci wirtualnej?
Tak. Wszystkie maszyny wirtualne i usługi w chmurze wystąpień roli, wdrożonego w ramach sieci wirtualnej można połączyć się z Internetem.

## <a name="azure-services-that-connect-to-vnets"></a>Usługi platformy Azure, które łączą się z sieciami wirtualnymi

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Czy można używać usługi Azure App Service Web Apps, z siecią wirtualną?
Tak. Możesz wdrażać aplikacje sieci Web w sieci wirtualnej przy użyciu środowisko ASE wewnętrznego (App Service Environment). W przypadku połączenia punkt lokacja skonfigurowanego dla sieci wirtualnej, wszystkie aplikacje sieci Web można bezpiecznie połączyć i uzyskiwać dostęp do zasobów w sieci wirtualnej. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Tworzenie aplikacji sieci Web w środowisku usługi App Service](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integrowanie aplikacji z siecią wirtualną platformy Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Przy użyciu połączeń hybrydowych i integracja z siecią wirtualną z funkcją Web Apps](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Czy mogę wdrożyć usługi w chmurze przy użyciu ról sieć web i proces roboczy (PaaS) w sieci wirtualnej?
Tak. (Opcjonalnie) można wdrożyć wystąpień roli usług Cloud Services w ramach sieci wirtualnych. Aby to zrobić, należy określić nazwę sieci wirtualnej i mapowania podsieć/roli w sekcji konfiguracji usługi w konfiguracji sieci. Nie trzeba do aktualizacji plików binarnych.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Czy można połączyć zestawu skalowania maszyn wirtualnych (VMSS) do sieci wirtualnej?
Tak. Zestawu skalowania maszyn wirtualnych należy połączyć z siecią wirtualną.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Jest pełna lista Azure usług, czy mogę wdrożyć zasobów w sieci wirtualnej?
Tak, aby uzyskać więcej informacji, zobacz [Integracja sieci wirtualnej dla usług platformy Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Jakie zasoby PaaS platformy Azure można ograniczyć dostęp do sieci wirtualnej?

Zasoby wdrożone za pośrednictwem niektórych usług PaaS platformy Azure (np. usługi Azure Storage i Azure SQL Database), można ograniczyć dostęp do sieci, tylko do zasobów w sieci wirtualnej za pomocą punktów końcowych usługi sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Czy mogę przenieść mój usług i sieciami wirtualnymi
Nie. Nie można przenieść usług i sieciami wirtualnymi. Aby przenieść zasób do innej sieci wirtualnej, należy do usunięcia i ponownego wdrożenia zasobów.

## <a name="security"></a>Bezpieczeństwo

### <a name="what-is-the-security-model-for-vnets"></a>Co to jest model zabezpieczeń dla sieci wirtualnych?
Sieci wirtualne są odizolowane od siebie i innych usług, hostowanych w infrastrukturze platformy Azure. Sieć wirtualna jest granicy zaufania.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Do zasobów połączonych z siecią wirtualną można ograniczyć ruch przychodzący i wychodzący przepływ?
Tak. Można zastosować [sieciowych grup zabezpieczeń](security-overview.md) do poszczególnych podsieci w sieci wirtualnej, kart sieciowych dołączonych do sieci wirtualnej i / lub.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Czy można zaimplementować zapory między zasobami podłączonej do sieci wirtualnej?
Tak. Możesz wdrożyć [zapory sieciowe urządzenie wirtualne](https://azure.microsoft.com/marketplace/?term=firewall) z kilku dostawców w portalu Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Dostępne na temat zabezpieczania sieci wirtualne są informacje?
Tak. Aby uzyskać więcej informacji, zobacz [Omówienie zabezpieczeń sieci platformy Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>Interfejsy API, schematów i narzędzia

### <a name="can-i-manage-vnets-from-code"></a>Z poziomu kodu może zarządzać sieciami wirtualnymi?
Tak. Można użyć interfejsów API REST dla sieci wirtualnych w [usługi Azure Resource Manager](/rest/api/virtual-network) i [klasyczny (Zarządzanie usługami)](https://go.microsoft.com/fwlink/?LinkId=296833) modeli wdrażania.

### <a name="is-there-tooling-support-for-vnets"></a>Czy istnieje obsługę narzędzi dla sieci wirtualnych?
Tak. Dowiedz się więcej o korzystaniu z:
- Witryny Azure portal, aby wdrożyć sieci wirtualnych za pośrednictwem [usługi Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) i [klasycznego](virtual-networks-create-vnet-classic-pportal.md) modeli wdrażania.
- Zarządzanie sieciami wirtualnymi wdrożonymi za pośrednictwem programu PowerShell [usługi Resource Manager](/powershell/module/az.network) i [klasycznego](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) modeli wdrażania.
- Interfejsu wiersza polecenia platformy Azure (CLI), wdrażanie i zarządzanie nimi sieci wirtualnych wdrożonych za pośrednictwem [usługi Resource Manager](/cli/azure/network/vnet) i [klasycznego](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) modeli wdrażania.  

## <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

### <a name="what-is-vnet-peering"></a>Co to jest komunikacja równorzędna sieci wirtualnych?
Komunikacji równorzędnej sieci wirtualnej (lub komunikacji równorzędnej sieci wirtualnych) umożliwia łączenie sieci wirtualnych. Połączenia komunikacji równorzędnej sieci wirtualnej między sieciami wirtualnymi umożliwia kierowanie ruchu między nimi prywatnie przy użyciu adresów IPv4. Tak, jakby leżą one w tej samej sieci maszyn wirtualnych w wirtualnych sieciach równorzędnych mogą komunikować się ze sobą. Te sieci wirtualne można w tym samym regionie lub w różnych regionach (znany także jako globalnych wirtualnych sieci równorzędnych). Można również tworzyć połączenia komunikacji równorzędnej sieci wirtualnej między subskrypcjami platformy Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>W innym regionie można utworzyć połączenie komunikacji równorzędnej z siecią wirtualną?
Tak. Globalne wirtualne sieci równorzędne pozwala nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych regionach. Globalne wirtualne sieci równorzędne jest dostępna we wszystkich publicznych regionach platformy Azure i regionów chmury w Chinach. Nie można globalnie komunikacji równorzędnej z publicznych regionach platformy Azure w regionach chmury krajowe. Globalnej komunikacji równorzędnej nie ma obecnie w chmurze dla instytucji rządowych.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Jakie są ograniczenia związane z globalnych wirtualnych sieci równorzędnych i moduły równoważenia obciążenia?
Jeśli dwie sieci wirtualne znajdują się w innym regionie (globalnych wirtualnych sieci równorzędnych), nie można połączyć się z zasobami, które używają podstawowego modułu równoważenia obciążenia. Możesz połączyć do zasobów, które używają standardowego modułu równoważenia obciążenia.
Podstawowe usługi równoważenia obciążenia, co oznacza, że nie może komunikować się do nich w globalnych wirtualnych sieci równorzędnych jest używany przez następujące zasoby:
- Maszyny wirtualne za modułem równoważenia obciążenia podstawowe
- Usługi VM Scale Sets z modułami równoważenia obciążenia podstawowe 
- Pamięć podręczna Redis 
- Usługa Application Gateway (wersja 1) jednostek SKU
- Service Fabric
- Zawsze włączone programu SQL Server
- SQL MI
- API Managemenet
- Active Directory Domain Service (ADDS)
- Logic Apps
- HD Insight
-   Azure Batch
- AKS
- Środowisko usługi App Service

Możesz połączyć się do tych zasobów za pośrednictwem usługi ExpressRoute lub sieci wirtualnej między sieciami wirtualnymi za pośrednictwem bram sieci wirtualnej.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Można włączyć komunikację równorzędną sieci wirtualnej Jeśli Moje sieci wirtualne należą do subskrypcji w ramach różnych dzierżaw usługi Azure Active Directory?
Tak. Jest możliwe ustalenie, komunikacja równorzędna sieci wirtualnych (czy lokalnych lub globalnych), jeśli Twoje subskrypcje należą do różnych dzierżaw usługi Azure Active Directory. Można to zrobić za pomocą programu PowerShell lub interfejsu wiersza polecenia. Portal nie jest jeszcze obsługiwana.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Mojej sieci wirtualnej połączenie komunikacji równorzędnej jest *zainicjowano* stanu, dlaczego nie mogę połączyć?
Jeśli połączenie komunikacji równorzędnej jest w stanie zainicjowano, oznacza to, że utworzono tylko jedno połączenie. Można utworzyć łącza dwukierunkowy, aby możliwe było nawiązanie połączenia. Na przykład aby nawiązać komunikację równorzędną między sieciami wirtualnymi A siecią wirtualną b, łącze należy utworzyć z zachodzi komunikacja równorzędna między do Vnetc i Vnetc, aby komunikacja równorzędna między. Tworzenie łączy główny zarówno zmieni stan na *połączone.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mojej sieci wirtualnej połączenie komunikacji równorzędnej jest *Disconnected* stanu, dlaczego nie można utworzyć połączenie komunikacji równorzędnej?
Jeśli połączenie komunikacji równorzędnej sieci wirtualnej jest w stanie rozłączenia, oznacza to, że jeden z linków utworzony został usunięty. Aby ponownie ustanowić połączenie komunikacji równorzędnej, konieczne będzie usunięcie linku i ponowne utworzenie.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Czy można peer mojej sieci wirtualnej z siecią wirtualną w innej subskrypcji?
Tak. Komunikacja równorzędna może sieci wirtualnych między subskrypcjami i regionach.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Można nawiązywana jest komunikacja równorzędna dwóch sieci wirtualnych za pomocą dopasowania i nakładających się zakresów adresów?
Nie. Aby włączyć komunikację równorzędną sieci wirtualnej nie może nakładać przestrzeni adresowych.

### <a name="how-much-do-vnet-peering-links-cost"></a>Ile zrobić, wirtualne sieci równorzędne łącza kosztów?
Brak opłat za utworzenie połączenia komunikacji równorzędnej sieci wirtualnej. Transfer danych we połączenia komunikacji równorzędnej jest rozliczane. [Zobacz tutaj](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Ruch komunikacji równorzędnej sieci wirtualnej są szyfrowane?
Nie. Ruch między zasobami w wirtualnych sieciach równorzędnych jest prywatny i izolowany. Pozostaje całkowicie Backbone firmy Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Dlaczego jest moje połączenie komunikacji równorzędnej w stanie rozłączonym?
Połączenia komunikacji równorzędnej, przejdź do sieci wirtualnej *Disconnected* stanie po usunięciu jedno połączenie komunikacji równorzędnej sieci wirtualnej. Aby ponownie ustanowić połączenia komunikacji równorzędnej, musisz usunąć obu połączeń.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Jeśli nawiązywana jest komunikacja równorzędna zachodzi komunikacja równorzędna między do Vnetc, nawiązywana jest komunikacja równorzędna Vnetc zachodzi komunikacja równorzędna między oznacza, że komunikacja równorzędna między i komunikacja skomunikowane równorzędnie?
Nie. Przechodnie komunikacja równorzędna nie jest obsługiwana. Należy równorzędne zachodzi komunikacja równorzędna między i oraz tego, została wykonana.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Czy istnieją jakiekolwiek ograniczenia przepustowości, dla połączenia komunikacji równorzędnej?
Nie. Wirtualne sieci równorzędne, lokalnych lub globalnych, nie nakłada żadnych ograniczeń przepustowości. Przepustowość jest ograniczona tylko maszyna wirtualna lub zasób obliczeniowy.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Które regiony platformy Azure są dostępne dla PODSŁUCHU sieci wirtualnej?
Naciśnij pozycję sieci wirtualnej w wersji zapoznawczej jest dostępna we wszystkich regionach platformy Azure. Interfejsy sieciowe monitorowane, NACIŚNIJ zasób sieci wirtualnej i rozwiązania modułu zbierającego lub analiza musi zostać wdrożony w tym samym regionie.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Virtual Network TAP obsługuje wszystkie funkcje filtrowania dublowanego pakietów?
Funkcje filtrowania nie są obsługiwane w wersji zapoznawczej wybierz sieć wirtualną. Podczas konfiguracji wzorca TAP jest dodawana do interfejsu sieciowego głęboką kopię danych przychodzących i ruch wychodzący na interfejs sieciowy jest przesyłany strumieniowo do miejsca docelowego, naciśnij pozycję.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Wiele konfiguracji wzorca TAP można dodać do interfejsu sieciowego monitorowanych?
Interfejs sieciowy monitorowanych może mieć tylko jedną konfigurację wzorca TAP. Skontaktuj się z poszczególnych [rozwiązania partnerskie](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) dla funkcji do przesyłania strumieniowego wiele kopii ruch wzorca TAP do wybranych przez siebie narzędzi analizy.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Ten sam zasób sieci wirtualnej w wzorca TAP agregować ruchu z monitorowanych interfejsów w więcej niż jednej sieci wirtualnej?
Tak. Tej samej sieci wirtualnej zasobów wzorca TAP może być używany do agregacji ruchu na zdublowanym z monitorowanych interfejsów w równorzędnych sieciach wirtualnych w tej samej subskrypcji lub innej subskrypcji. Moduł równoważenia obciążenia zasobów wybierz sieć wirtualną i docelowy lub docelowy interfejs sieciowy musi być w tej samej subskrypcji. Wszystkie subskrypcje muszą znajdować się pod tą samą dzierżawą usługi Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Czy istnieją wszystkie zagadnienia związane z wydajnością na jest przesyłany ruch produkcyjny włączyć Konfiguracja wzorca TAP sieci wirtualnej w interfejsie sieciowym?

Virtual network TAP jest dostępna w wersji zapoznawczej. W trakcie okresu zapoznawczego nie ma żadnych umowę dotyczącą poziomu usług. Ta funkcja nie należy używać w przypadku obciążeń produkcyjnych. Po włączeniu interfejsu sieciowego maszyny wirtualnej z konfiguracją wzorca TAP tych samych zasobów na hoście platformy azure przydzielone do maszyny wirtualnej do wysyłania ruchu produkcyjnego umożliwia pełnią funkcję dublowania i wysyłania pakietów dublowanego. Wybierz poprawnego [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiar maszyny wirtualnej, aby upewnić się, że wystarczające zasoby są dostępne dla maszyny wirtualnej do wysyłania ruchu produkcyjnego i dublowanych ruchu.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>Jest accelerated networking for [Linux](create-vm-accelerated-networking-cli.md) lub [Windows](create-vm-accelerated-networking-powershell.md) obsługiwane z usługą virtual network TAP?

Będzie można dodać wzorca TAP konfigurację interfejsu sieciowego dołączonych do maszyny wirtualnej, w którym włączono z przyspieszoną siecią. Ale wydajności i opóźnień w maszynie wirtualnej będzie miało wpływ na dodawaniu, naciśnij pozycję konfiguracji, ponieważ Odciążanie dublowanie ruch nie jest obecnie obsługiwane przez platformę Azure funkcji przyspieszonej łączności sieciowej.

## <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Co to jest prawo sekwencji operacji do skonfigurowania punktów końcowych usługi dla usługi platformy Azure?
Istnieją dwa kroki, aby zabezpieczanie zasobów usługi platformy Azure za pośrednictwem punktów końcowych usługi:
1. Włącz punkty końcowe usługi dla usługi platformy Azure.
2. Skonfiguruj listy ACL sieci wirtualnej usługi platformy Azure.

Pierwszym krokiem jest operacji po stronie sieci, a drugi krok jest operacji po stronie zasobów usługi. Oba kroki można wykonać przez administratora w tej samej lub różnych administratorów, w oparciu o RBAC uprawnienia do roli administratora. Zaleca się, że pierwszym włączeniu punktów końcowych usługi sieci wirtualnej przed skonfigurowaniem list ACL sieci wirtualnej po stronie usługi Azure. W związku z tym kroki muszą być wykonywane w kolejności wymienionych powyżej, aby skonfigurować punkty końcowe usługi sieci wirtualnej.

>[!NOTE]
> Aby można ograniczyć dostęp usługi platformy Azure do dozwolonych sieci wirtualnej i podsieci, należy wykonać operacje opisane powyżej. Tylko włączenie punktów końcowych usługi dla usługi platformy Azure na stronie sieci nie umożliwiają ograniczony dostęp. Ponadto możesz również ustawić list ACL sieci wirtualnej po stronie usługi Azure.

Niektóre usługi (takie jak bazy danych SQL i bazy danych cosmos DB) zezwala na wyjątki, powyżej sekwencji za pośrednictwem **IgnoreMissingVnetServiceEndpoint** flagi. Gdy flaga jest ustawiona **True**, listy ACL sieci wirtualnej można ustawić po stronie usługi platformy Azure, przed skonfigurowaniem punkty końcowe usługi sieci po stronie. Usługi platformy Azure zapewniają tej flagi, aby pomóc klientom w przypadkach, gdy skonfigurowano określonych zaporach adresów IP usług platformy Azure i włączenie punktów końcowych usługi sieci po stronie może prowadzić do wrzucania łączności, ponieważ źródłowy adres IP zmienia się z publicznego adresu IPv4 prywatny adres. Definiowanie list ACL sieci wirtualnej po stronie usługi Azure, przed ustawieniem punkty końcowe usługi sieci po stronie może pomóc uniknąć zrzutu łączności.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Czy wszystkich usług platformy Azure znajdują się w sieci wirtualnej platformy Azure, udostępniane przez klienta? Jak działa punkt końcowy usługi sieci wirtualnej z usługami platformy Azure?

Nie, nie wszystkie usługi platformy Azure znajdują się w sieci wirtualnej klienta. Większość danych platformy Azure usług, takich jak Azure Storage, Azure SQL i usługi Azure Cosmos DB są wielodostępnych usług, które mogą być udostępniane za pośrednictwem publicznych adresów IP. Dowiedz się więcej na temat integracji sieci wirtualnej dla usług platformy Azure [tutaj](virtual-network-for-azure-services.md). 

Gdy używasz funkcji punktów końcowych usługi sieci wirtualnej (Włączanie punktu końcowego usługi sieci wirtualnej na stronie sieci i konfigurowania odpowiednich ACL sieci wirtualnej po stronie usługi platformy Azure), dostęp do usługi platformy Azure jest ograniczony z dozwolonych sieci wirtualnej i podsieci.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>W jaki sposób punkt końcowy usługi sieci wirtualnej zapewnia zabezpieczeń

Funkcja punktu końcowego usługi sieci wirtualnej (Włączanie punktu końcowego usługi sieci wirtualnej na stronie sieci i konfigurowania odpowiednich ACL sieci wirtualnej po stronie usługi platformy Azure) ogranicza dostęp usługi platformy Azure, do dozwolonych sieci wirtualnej i podsieci, zapewniając w ten sposób zabezpieczenia na poziomie sieci i izolacji ruch usługi platformy Azure. Cały ruch przy użyciu punktów końcowych usługi sieci wirtualnej przepływy za pośrednictwem sieci szkieletowej firmy Microsoft, zapewniając w ten sposób kolejną warstwę izolacji w publicznym Internecie. Ponadto klienci mogą wybrać opcję całkowitego usunięcia publicznego dostępu do Internetu dla zasobów usługi platformy Azure i zezwolić na ruch tylko z własną siecią wirtualną za pośrednictwem zapory adresów IP i list ACL sieci wirtualnej, w związku z tym chroni zasoby usługi platformy Azure przed nieautoryzowanym dostęp.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Co to jest sieć wirtualna ochrona punktu końcowego usługi — zasoby sieci wirtualnej lub usługi platformy Azure?
Punkty końcowe usługi sieci wirtualnej chronić zasoby usługi platformy Azure. Zasoby sieci wirtualnej są chronione przy użyciu sieciowych grup zabezpieczeń (NSG).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Czy jest jakaś opłata dotyczące korzystania z punktów końcowych usługi sieci wirtualnej?

Nie, nie ma żadnych dodatkowych kosztów za pomocą punktów końcowych usługi sieci wirtualnej.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Czy mogę włączyć punkty końcowe usługi sieci wirtualnej i konfigurowanie listy ACL sieci wirtualnej, jeśli sieć wirtualną i zasoby usługi platformy Azure należą do różnych subskrypcji?

Tak, jest to możliwe. Sieci wirtualne i zasoby usługi platformy Azure może być w tej samej lub różnych subskrypcji. Jedynym wymaganiem jest, że sieć wirtualną i zasoby usługi platformy Azure musi być w tej samej dzierżawie usługi Active Directory (AD).

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Czy mogę włączyć punkty końcowe usługi sieci wirtualnej i konfigurowanie list ACL sieci wirtualnej, jeśli sieć wirtualną i zasoby usługi platformy Azure należą do różnych dzierżaw usługi AD?
Nie, punkty końcowe usługi sieci wirtualnej i listy kontroli dostępu w sieci wirtualnej nie są obsługiwane dla dzierżaw usługi AD.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-express-route-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Adres IP lokalnego urządzenia, która jest połączona za pośrednictwem bramy usługi Azure Virtual Network (VPN) lub brama usługi Express route dostęp usługi PaaS platformy Azure za pośrednictwem punktów końcowych usługi sieci wirtualnej?
Domyślnie nie można uzyskać dostępu do zasobów usługi platformy Azure zabezpieczonych w sieciach wirtualnych z sieci lokalnych. Jeśli chcesz zezwolić na ruch ze środowiska lokalnego, należy także zezwolić na publiczny (zazwyczaj translatora adresów Sieciowych) IP adresów z sieci lokalnej lub usługi ExpressRoute. Te adresy IP można dodawać za pośrednictwem konfiguracji zapory adresów IP dla zasobów usługi platformy Azure.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-with-in-a-virtual-network-or-across-multiple-virtual-networks"></a>Aby zabezpieczyć usługi platformy Azure w wielu podsieciach z sieci wirtualnej lub między wieloma sieciami wirtualnymi można używać funkcji punktu końcowego usługi sieci wirtualnej?
Aby zabezpieczyć usługi platformy Azure w wielu podsieciach sieci wirtualnej lub przez wiele sieci wirtualnych, niezależnie włączyć punkty końcowe usługi na stronie sieci w każdym z tych podsieci i zabezpieczyć zasoby usługi platformy Azure do wszystkich tych podsieciach, konfigurując odpowiednie listy ACL sieci wirtualnej po stronie usługi platformy Azure.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Jak filtrować ruch wychodzący z sieci wirtualnej do usług platformy Azure i nadal korzystać z punktów końcowych usługi?
jeśli chcesz sprawdzić lub filtrować ruch kierowany do usługi platformy Azure z sieci wirtualnej, możesz wdrożyć urządzenie sieci wirtualnej w ramach tej sieci wirtualnej. Punkty końcowe usługi mogą następnie zastosować do podsieci, gdzie wirtualne urządzenie sieciowe to zasoby wdrożone i bezpieczne usługi platformy Azure tylko do tej podsieci za pośrednictwem list ACL sieci wirtualnej. W tym scenariuszu może być również przydatne, jeśli chcesz ograniczyć dostęp do usługi platformy Azure z sieci wirtualnej tylko do określonych zasobów platformy Azure przy użyciu filtrowania sieciowych urządzeń wirtualnych. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Co się stanie, gdy uzyskujesz dostęp do konta usługi platformy Azure z sieci wirtualnej listy kontroli dostępu (ACL) można włączyć spoza sieci wirtualnej?
Zwracany jest błąd 403 protokołu HTTP lub HTTP 404.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Czy na podsieci sieci wirtualnej utworzonej w różnych regionach, które mogą uzyskać dostęp do konta usługi platformy Azure w innym regionie? 
Tak, dla większości usług platformy Azure, sieci wirtualnych utworzonych w różnych regionach mogą uzyskać dostęp do usług platformy Azure w innym regionie za pośrednictwem punktów końcowych usługi sieci wirtualnej. Na przykład jeśli konto usługi Azure Cosmos DB znajduje się w regionie zachodnie stany USA lub wschodnie stany USA i sieci wirtualne znajdują się w wielu regionach, sieci wirtualnej można uzyskać dostęp do usługi Azure Cosmos DB. Storage i SQL są wyjątki są regionalnych z natury i sieci wirtualnej i usługi platformy Azure muszą być w tym samym regionie.
  
### <a name="can-an-azure-service-have-both-vnet-acl-and-an-ip-firewall"></a>Usługi platformy Azure mają listy ACL sieci wirtualnej i zapory adresów IP?
Tak, listy ACL sieci wirtualnej i zapory adresów IP mogą współistnieć. Obie funkcje uzupełniają się wzajemnie zapewnienie izolacji i zabezpieczeń.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Co się stanie, jeśli usuniesz sieci wirtualnej lub podsieci, która zawiera punkt końcowy usługi włączona dla usługi platformy Azure?
Usuwanie sieci wirtualnych i podsieci są obsługiwane, nawet wtedy, gdy punkty końcowe usługi są włączane dla usług platformy Azure i są niezależne operacje. W przypadkach, gdy usług platformy Azure mają listy ACL sieci wirtualnej, skonfiguruj dla tych sieci wirtualnych i podsieci informacje listy kontroli dostępu w sieci wirtualnej skojarzone z tym usługa jest wyłączona podczas usuwania sieci wirtualnej lub podsieci, która ma włączony punkt końcowy usługi sieci wirtualnej.
 
### <a name="what-happens-if-azure-service-account-that-has-vnet-service-endpoint-enabled-is-deleted"></a>Co się stanie po usunięciu konta usługi platformy Azure, które ma włączonego punktu końcowego usługi sieci wirtualnej?
Usuwanie konta usługi Azure jest operacją niezależne i jest obsługiwane, nawet wtedy, gdy punkt końcowy usługi jest włączona na stronie sieci i list ACL sieci wirtualnej skonfigurowano pod kątem po stronie usługi Azure. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Co stanie się źródłowy adres IP zasobu (np. maszyn wirtualnych w podsieci), który ma włączonego punktu końcowego usługi sieci wirtualnej?
Po włączeniu punktów końcowych usługi sieci wirtualnej, źródłowe adresy IP zasobów w podsieci sieci wirtualnej zmienia się z używania publicznych adresów IPV4 na użycie prywatnych adresów IP platformy Azure sieci wirtualnej dla ruchu do usługi platformy Azure. Należy pamiętać, że może to spowodować określonych zapory adresów IP, które są ustawione na publiczny adres IPV4 wcześniej na usługi platformy Azure nie powiedzie się. 

### <a name="does-service-endpoint-route-always-take-precedence"></a>Trasy punktu końcowego usługi zawsze podąża priorytet?
Punkty końcowe usługi Dodaj trasa systemowa, która pierwszeństwo trasy protokołu BGP i zapewniają optymalny routing ruchu punkt końcowy usług sieci. Punktów końcowych usługi zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Microsoft Azure. Aby uzyskać więcej informacji na temat jak platforma Azure wybiera trasę zobacz [wirtualnych platformy Azure z routingiem ruchu sieciowego](virtual-networks-udr-overview.md).
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Jak działa sieciowej grupy zabezpieczeń w podsieci z punktami końcowymi usługi?
Aby uzyskać dostęp do usługi platformy Azure, sieciowe grupy zabezpieczeń muszą zezwalać na łączność wychodząca. Jeśli sieciowych grup zabezpieczeń nie są otwarte dla cały ruch wychodzący z Internetu, ruch punktu końcowego usługi powinien działać. Można również ograniczyć ruch wychodzący do usługi adresy IP tylko przy użyciu tagów usługi.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Jakie uprawnienia należy skonfigurować punkty końcowe usługi?
Punkty końcowe usługi można niezależnie konfigurować wykonać w ramach sieci wirtualnej przez użytkownika z dostępem do zapisu do sieci wirtualnej. Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do elementu **Microsoft.Network/JoinServicetoaSubnet** dla dodawanych podsieci. To uprawnienie jest domyślnie w roli administratora wbudowane usługi i mogą być modyfikowane przez tworzenie ról niestandardowych. Dowiedz się więcej o wbudowanych ról i przypisywaniu określonych uprawnień do [ról niestandardowych](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Można filtrować ruchu w sieci wirtualnej do usług platformy Azure, dzięki czemu tylko zasoby określonych usług platformy azure, za pośrednictwem punktów końcowych usługi sieci wirtualnej? 

Zasad punktów końcowych usługi sieci wirtualnej (VNet) umożliwiają filtrowanie ruchu w sieci wirtualnej do usług platformy Azure, dzięki czemu zasoby tylko do określonych usług platformy Azure za pośrednictwem punktów końcowych usługi. Punkt końcowy zasady zapewniają szczegółową kontrolę dostępu z ruchem w sieci wirtualnej do usług platformy Azure. Dowiedz się więcej na temat zasad punktów końcowych usługi [tutaj](virtual-network-service-endpoint-policies-overview.md).
 
### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Czy istnieją jakiekolwiek ograniczenia na liczbę sieci wirtualnej punkty końcowe usługi można skonfigurować w mojej sieci wirtualnej?
Nie ma żadnego limitu całkowitej liczby punktów końcowych usługi sieci wirtualnej w sieci wirtualnej. W przypadku zasobów usług platformy Azure (np. konta usługi Azure Storage) usługi mogą wymuszać limity liczby podsieci używanych do zabezpieczania zasobów. W poniższej tabeli przedstawiono pewne ograniczenia na przykład: 

|||
|---|---|
|Usługa platformy Azure| Ograniczenia dotyczące reguł sieci wirtualnej|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Centrum zdarzeń Azure|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> Limity są poddawane zmiany według własnego uznania usługi platformy Azure. Zapoznaj się z dokumentacją odpowiedniej usługi, aby uzyskać szczegółowe informacje z usługi. 




  



