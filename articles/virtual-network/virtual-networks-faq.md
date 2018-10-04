---
title: Sieć wirtualna platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na najczęściej zadawane pytania na temat sieci wirtualnych Microsoft Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 54bee086-a8a5-4312-9866-19a1fba913d0
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/16/2018
ms.author: jdial
ms.openlocfilehash: 2291136cf50439b7845c3a344be3218277257b34
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269483"
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
Dowolny zakres adresów IP określonych w [RFC 1918](http://tools.ietf.org/html/rfc1918). Na przykład: 10.0.0.0/16.

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
- **Bramy sieci VPN Azure**: Aby uzyskać więcej informacji, zobacz [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

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
* **Prywatne:** przypisane do poszczególnych kart Sieciowych w ramach każdej maszyny Wirtualnej. Adres jest przypisany, używając metody statyczne lub dynamiczne. Prywatne adresy IP są przypisywane z zakresu, który określono w ustawieniach podsieci sieci wirtualnej. Zasoby wdrożone za pośrednictwem klasycznego modelu wdrażania są przypisywane prywatnych adresów IP, nawet jeśli nie masz połączenia z siecią wirtualną. Zachowanie metody alokacji różni się w zależności od tego, czy zasób został wdrożony za pomocą usługi Resource Manager lub Klasyczny model wdrażania: 

  - **Menedżer zasobów**: przypisany przy użyciu metody dynamiczny lub statyczny prywatny adres IP pozostanie przydzielony do maszyny wirtualnej (Resource Manager), aż do usunięcia zasobu. Różnica polega na, wybierz adres, który zostanie przypisany przy użyciu statycznej i wybierze platformy Azure, korzystając z dynamicznych. 
  - **Klasyczne**: prywatnego adresu IP są przypisywane przy użyciu metody dynamiczne mogą ulec zmianie, gdy maszyna wirtualna (klasyczna) ponownego uruchomienia maszyny Wirtualnej po przejściu w stan zatrzymania (przydział zostanie cofnięty). Jeśli potrzebujesz upewnić się, że prywatny adres IP zasobu wdrożone za pośrednictwem klasycznego modelu wdrażania nigdy się nie zmienia, należy przypisać prywatny adres IP za pomocą metody statycznej.

* **Public:** opcjonalnie przypisane do kart sieciowych dołączonych do maszyn wirtualnych wdrożonych za pomocą modelu wdrażania usługi Azure Resource Manager. Przy użyciu metody alokacji statycznej lub dynamicznej można przypisać adresu. Wszystkie maszyny wirtualne i usługi w chmurze wystąpień roli, wdrożone za pośrednictwem klasycznego modelu wdrażania istnieje w ramach usługi w chmurze, która jest przypisana *dynamiczne*, publiczny wirtualny adres IP (VIP). Publiczny *statyczne* adres IP, nazywany [zastrzeżony adres IP](virtual-networks-reserved-public-ip.md), opcjonalnie może być przypisany jako adres VIP. Publiczne adresy IP można przypisać do poszczególnych maszyn wirtualnych ani usług w chmurze wystąpień roli wdrożone za pośrednictwem klasycznego modelu wdrażania. Te adresy są nazywane [poziomu publicznego adresu IP wystąpienia (ILPIP](virtual-networks-instance-level-public-ip.md) adresów, a także mogą być przypisywane dynamicznie.

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
Tak, adres MAC, pozostaje taka sama dla maszyny Wirtualnej wdrożone za pośrednictwem usługi Resource Manager i klasycznych modeli wdrażania, dopóki zostanie usunięty. Adres MAC zostało wydane wcześniej, jeśli maszyna wirtualna została zatrzymana (przydział zostanie cofnięty), ale teraz adres MAC jest zachowywane nawet wtedy, gdy maszyna wirtualna jest w stanie cofnięcia przydziału.

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
Tak. Można użyć interfejsów API REST dla sieci wirtualnych w [usługi Azure Resource Manager](/rest/api/virtual-network) i [klasyczny (Zarządzanie usługami)](http://go.microsoft.com/fwlink/?LinkId=296833) modeli wdrażania.

### <a name="is-there-tooling-support-for-vnets"></a>Czy istnieje obsługę narzędzi dla sieci wirtualnych?
Tak. Dowiedz się więcej o korzystaniu z:
- Witryny Azure portal, aby wdrożyć sieci wirtualnych za pośrednictwem [usługi Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) i [klasycznego](virtual-networks-create-vnet-classic-pportal.md) modeli wdrażania.
- Zarządzanie sieciami wirtualnymi wdrożonymi za pośrednictwem programu PowerShell [usługi Resource Manager](/powershell/module/azurerm.network) i [klasycznego](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) modeli wdrażania.
- Interfejsu wiersza polecenia platformy Azure (CLI), wdrażanie i zarządzanie nimi sieci wirtualnych wdrożonych za pośrednictwem [usługi Resource Manager](/cli/azure/network/vnet) i [klasycznego](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) modeli wdrażania.  

## <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

### <a name="what-is-vnet-peering"></a>Co to jest komunikacja równorzędna sieci wirtualnych?
Komunikacji równorzędnej sieci wirtualnej (lub komunikacji równorzędnej sieci wirtualnych) umożliwia łączenie sieci wirtualnych. Połączenia komunikacji równorzędnej sieci wirtualnej między sieciami wirtualnymi umożliwia kierowanie ruchu między nimi prywatnie przy użyciu adresów IPv4. Tak, jakby leżą one w tej samej sieci maszyn wirtualnych w wirtualnych sieciach równorzędnych mogą komunikować się ze sobą. Te sieci wirtualne można w tym samym regionie lub w różnych regionach (znany także jako globalnych wirtualnych sieci równorzędnych). Można również tworzyć połączenia komunikacji równorzędnej sieci wirtualnej między subskrypcjami platformy Azure.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>W innym regionie można utworzyć połączenie komunikacji równorzędnej z siecią wirtualną?
Tak. Globalne wirtualne sieci równorzędne pozwala nawiązać komunikację równorzędną między sieciami wirtualnymi w różnych regionach. Globalne wirtualne sieci równorzędne jest dostępna we wszystkich publicznych regionach platformy Azure. Nie można globalnie komunikacji równorzędnej z publicznych regionach platformy Azure do chmur krajowych. Globalnej komunikacji równorzędnej nie ma obecnie w chmurach krajowych.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Można włączyć komunikację równorzędną sieci wirtualnej Jeśli Moje sieci wirtualne należą do subskrypcji w ramach różnych dzierżaw usługi Azure Active Directory?
Tak. Jest możliwe ustalenie, komunikacja równorzędna sieci wirtualnych (czy lokalnych lub globalnych), jeśli Twoje subskrypcje należą do różnych dzierżaw usługi Azure Active Directory. Można to zrobić za pomocą programu PowerShell lub interfejsu wiersza polecenia. Portal nie jest jeszcze obsługiwana.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Mojej sieci wirtualnej połączenie komunikacji równorzędnej jest *zainicjowano* stanu, dlaczego nie mogę połączyć?
Jeśli połączenie komunikacji równorzędnej jest w stanie zainicjowano, oznacza to, że utworzono tylko jedno połączenie. Link dwukierunkowe musi zostać utworzona, aby możliwe było nawiązanie połączenia pomyślnie. Na przykład aby nawiązać komunikację równorzędną między sieciami wirtualnymi A siecią wirtualną b, łącze należy utworzyć z zachodzi komunikacja równorzędna między do Vnetc i Vnetc, aby komunikacja równorzędna między. Tworzenie łączy główny zarówno zmieni stan na *połączone.*

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mojej sieci wirtualnej połączenie komunikacji równorzędnej jest *Disconnected* stanu, dlaczego nie można utworzyć połączenie komunikacji równorzędnej?
Jeśli połączenie komunikacji równorzędnej sieci wirtualnej jest w stanie rozłączenia, oznacza to, że jeden z linków utworzony został usunięty. Aby ponownie ustanowić połączenie komunikacji równorzędnej, konieczne będzie usunięcie linku i ponowne utworzenie.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Czy można peer mojej sieci wirtualnej z siecią wirtualną w innej subskrypcji?
Tak. Komunikacja równorzędna może sieci wirtualnych między subskrypcjami i regionach.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Można nawiązywana jest komunikacja równorzędna dwóch sieci wirtualnych za pomocą dopasowania i nakładających się zakresów adresów?
Nie. Przestrzenie adresowe musi nie overalap o włączenie komunikacji równorzędnej sieci wirtualnej.

### <a name="how-much-do-vnet-peering-links-cost"></a>Ile zrobić, wirtualne sieci równorzędne łącza kosztów?
Brak opłat za utworzenie połączenia komunikacji równorzędnej sieci wirtualnej. Transfer danych we połączenia komunikacji równorzędnej jest rozliczane. [Zobacz tutaj](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Ruch komunikacji równorzędnej sieci wirtualnej są szyfrowane?
Nie. Ruch między zasobami w wirtualnych sieciach równorzędnych jest prywatny i izolowany. Pozostaje całkowicie Backbone firmy Microsoft.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Dlaczego jest moje połączenie komunikacji równorzędnej w stanie rozłączonym?
Połączenia komunikacji równorzędnej, przejdź do sieci wirtualnej *Disconnected* stanie po usunięciu jedno połączenie komunikacji równorzędnej sieci wirtualnej. Aby ponownie ustanowić połączenia komunikacji równorzędnej, musisz usunąć obu połączeń.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Jeśli nawiązywana jest komunikacja równorzędna zachodzi komunikacja równorzędna między do Vnetc, nawiązywana jest komunikacja równorzędna Vnetc zachodzi komunikacja równorzędna między oznacza, że komunikacja równorzędna między i komunikacja skomunikowane równorzędnie?
Nie. Przechodnie komunikacja równorzędna nie jest obsługiwana. Należy równorzędne zachodzi komunikacja równorzędna między i oraz tego, została wykonana.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Czy istnieją jakiekolwiek ograniczenia przepustowości, dla połączenia komunikacji równorzędnej?
Nie. Wirtualne sieci równorzędne, lokalnych lub globalnych, nie nakłada żadnych ograniczeń przepustowości. Przepustowość jest tylko limity przez zasób maszynę Wirtualną lub obliczeń.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Które regiony platformy Azure są dostępne dla PODSŁUCHU sieci wirtualnej?
W wersji zapoznawczej dla deweloperów ta funkcja jest dostępna w regionie zachodnio-środkowe stany USA. Interfejsy sieciowe monitorowane, NACIŚNIJ zasób sieci wirtualnej i rozwiązania modułu zbierającego lub analiza musi zostać wdrożony w tym samym regionie.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Virtual Network TAP obsługuje wszystkie funkcje filtrowania dublowanego pakietów?
Funkcje filtrowania nie są obsługiwane w wersji zapoznawczej wybierz sieć wirtualną. Podczas konfiguracji wzorca TAP jest dodawana do interfejsu sieciowego głęboką kopię danych przychodzących i ruch wychodzący na interfejs sieciowy jest przesyłany strumieniowo do miejsca docelowego, naciśnij pozycję.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Wiele konfiguracji wzorca TAP można dodać do interfejsu sieciowego monitorowanych?
Interfejs sieciowy monitorowanych może mieć tylko jedną konfigurację wzorca TAP. Skontaktuj się z poszczególnych [rozwiązania partnerskie](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) dla funkcji do przesyłania strumieniowego wiele kopii ruch wzorca TAP do wybranych przez siebie narzędzi analizy.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Ten sam zasób sieci wirtualnej w wzorca TAP agregować ruchu z monitorowanych interfejsów w więcej niż jednej sieci wirtualnej?
Tak. Tej samej sieci wirtualnej zasobów wzorca TAP może być używany do agregacji ruchu na zdublowanym z monitorowanych interfejsów w równorzędnych sieciach wirtualnych w tej samej subskrypcji lub innej subskrypcji. Moduł równoważenia obciążenia zasobów wybierz sieć wirtualną i docelowy lub docelowy interfejs sieciowy musi być w tej samej subskrypcji. Wszystkie subskrypcje muszą znajdować się pod tą samą dzierżawą usługi Azure Active Directory.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Czy istnieją wszystkie zagadnienia związane z wydajnością na jest przesyłany ruch produkcyjny włączyć Konfiguracja wzorca TAP sieci wirtualnej w interfejsie sieciowym?

Virtual network TAP jest dostępna w wersji zapoznawczej dla deweloperów. W trakcie okresu zapoznawczego nie ma żadnych umowę dotyczącą poziomu usług. Ta funkcja nie należy używać w przypadku obciążeń produkcyjnych. Po włączeniu interfejsu sieciowego maszyny wirtualnej z konfiguracją wzorca TAP tych samych zasobów na hoście platformy azure przydzielone do maszyny wirtualnej do wysyłania ruchu produkcyjnego umożliwia pełnią funkcję dublowania i wysyłania pakietów dublowanego. Wybierz poprawnego [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) rozmiar maszyny wirtualnej, aby upewnić się, że wystarczające zasoby są dostępne dla maszyny wirtualnej do wysyłania ruchu produkcyjnego i dublowanych ruchu.

### <a name="is-accelerated-networking-for-linuxcreate-vm-accelerated-networking-climd-or-windowscreate-vm-accelerated-networking-powershellmd-supported-with-virtual-network-tap"></a>Jest accelerated networking for [Linux](create-vm-accelerated-networking-cli.md) lub [Windows](create-vm-accelerated-networking-powershell.md) obsługiwane z usługą virtual network TAP?

Będzie można dodać wzorca TAP konfigurację interfejsu sieciowego dołączonych do maszyny wirtualnej, w którym włączono z przyspieszoną siecią. Ale wydajności i opóźnień w maszynie wirtualnej będzie miało wpływ na dodawaniu, naciśnij pozycję konfiguracji, ponieważ Odciążanie dublowanie ruch nie jest obecnie obsługiwane przez platformę Azure funkcji przyspieszonej łączności sieciowej.
