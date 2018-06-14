---
title: Sieć wirtualna platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Odpowiedzi na najczęściej zadawane pytania dotyczące sieci wirtualne Microsoft Azure.
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
ms.date: 03/01/2018
ms.author: jdial
ms.openlocfilehash: a5b4bac9e0d8bc10defaff251557129a70d8a022
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2018
ms.locfileid: "29854193"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Sieć wirtualna platformy Azure — często zadawane pytania (FAQ)

## <a name="virtual-network-basics"></a>Podstawy sieci wirtualnej

### <a name="what-is-an-azure-virtual-network-vnet"></a>Co to jest sieci wirtualnej platformy Azure (VNet)?
Sieci wirtualnej platformy Azure (VNet) to reprezentacja sieci w chmurze. Jest logiczną izolacją chmury Azure przeznaczoną dla Twojej subskrypcji. Można Użyj sieci wirtualnych, aby udostępnić i zarządzanie wirtualnych sieci prywatnych (VPN) na platformie Azure i, opcjonalnie, połączyć sieci wirtualnych z innych sieci wirtualnych na platformie Azure lub z lokalnej infrastruktury IT w celu utworzenia rozwiązania hybrydowe lub między lokalizacjami. Każdej sieci wirtualnej, którą utworzysz ma bloku CIDR i może odnosić się do innych sieci wirtualnych i lokalnej sieci tak długo, jak bloków CIDR nie mogą się pokrywać. Masz również kontroli ustawienia serwera DNS dla sieci wirtualnych i segmentacji sieci wirtualnej do podsieci.

Użyj sieci wirtualnych do:

* Utwórz dedykowane prywatnej tylko w chmurze sieci wirtualnej czasami konfiguracji między lokalizacjami nie jest wymagany do rozwiązania. Po utworzeniu sieci wirtualnej, usług i maszyn wirtualnych w ramach sieci wirtualnej mogą komunikować się bezpośrednio i bezpiecznie ze sobą w chmurze. Nadal można skonfigurować punktu końcowego połączenia dla maszyn wirtualnych i usług, które wymagają komunikacja z Internetem, w ramach rozwiązania.
* Bezpiecznie rozszerzanie centrum danych z sieci wirtualnych, można tworzyć tradycyjnych sieci VPN (S2S) do lokacji można bezpiecznie skalować wydajność centrum danych. Sieci VPN S2S używaj protokołu IPSEC do zapewnienia bezpiecznego połączenia między bramą sieci VPN firmy i Azure.
* Scenariuszach chmur hybrydowych Włącz sieci wirtualne zapewniają elastyczność do obsługi zakresu scenariuszy hybrydowych chmury. Można bezpiecznie łączyć z aplikacji opartych na chmurze do dowolnego typu systemów Unix i lokalnego systemu, takich jak Komputery mainframe firmy.

### <a name="how-do-i-get-started"></a>Jak rozpocząć?
Odwiedź stronę [dokumentacji sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/) rozpocząć pracę. Ta zawartość zawiera informacje o przegląd i wdrażania dla wszystkich funkcji VNet.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Sieci wirtualne można używać bez łączności między lokalizacjami?
Tak. Bez konieczności nawiązywania lokalnej można użyć sieci wirtualnej. Na przykład wyłącznie w sieć wirtualną platformy Azure można uruchomić kontrolerów domeny usługi Active Directory firmy Microsoft Windows Server i farmy programu SharePoint.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Można przeprowadzić optymalizacji sieci WAN między sieciami wirtualnymi lub sieci wirtualnej i Moje lokalnego centrum danych?

Tak. Można wdrożyć [urządzenie wirtualne sieci WAN optymalizacji](https://azure.microsoft.com/marketplace/?term=wan+optimization) z kilku dostawców za pośrednictwem portalu Azure Marketplace.

## <a name="configuration"></a>Konfigurowanie

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Jakie narzędzia należy używać, aby utworzyć sieć wirtualną?
Następujące narzędzia służy do tworzenia lub konfigurowania sieci wirtualnej:

* Azure Portal
* PowerShell
* Interfejs wiersza polecenia platformy Azure
* Plik konfiguracji sieci (netcfg - tylko klasyczne sieci wirtualne). Zobacz [skonfigurować sieć wirtualną przy użyciu pliku konfiguracji sieci](virtual-networks-using-network-configuration-file.md) artykułu.

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Jakie zakresów adresów można używać w mojej sieci wirtualnych?
Wszelkie zakres adresów IP zdefiniowanych w [RFC 1918](http://tools.ietf.org/html/rfc1918). Na przykład 10.0.0.0/16.

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Czy można mieć publicznych adresów IP w mojej sieci wirtualnych
Tak. Aby uzyskać więcej informacji na temat zakresy publicznych adresów IP, zobacz [utworzyć sieć wirtualną](manage-virtual-network.md#create-a-virtual-network). Publiczne adresy IP nie są dostępne bezpośrednio z Internetu.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Czy istnieje ograniczona liczba podsieci w mojej sieci wirtualnej?
Tak. Zobacz [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) szczegółowe informacje. Przestrzeni adresowej podsieci nie może nakładać się wzajemnie.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Czy istnieją wszystkie ograniczenia dotyczące używania adresów IP w ramach tych podsieci?
Tak. Azure rezerwuje niektórych adresów IP w każdej podsieci. Imię i nazwisko adresy IP każdej podsieci są zastrzeżone dla protokołu zgodność, wraz z x.x.x.1 x.x.x.3 adresy każdej podsieci, które służą do usług Azure.

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Jak małe i jak duży może być sieci wirtualnych i podsieci?
Najmniejsza obsługiwana podsieci jest /29, a największa /8 (przy użyciu definicje podsieci CIDR).

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Czy mogę przenieść mój sieci VLAN na platformie Azure przy użyciu sieci wirtualnych?
Nie. Sieci wirtualne są nakładki warstwy 3. Azure nie obsługuje żadnych semantyki warstwy 2.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Czy mogę określić niestandardowe zasady routingu w mojej sieci wirtualnych i podsieci
Tak. Można utworzyć tabelę tras i skojarzyć go z podsiecią. Aby uzyskać więcej informacji dotyczących routingu na platformie Azure, zobacz [Omówienie routingu](virtual-networks-udr-overview.md#custom-routes).

### <a name="do-vnets-support-multicast-or-broadcast"></a>Sieci wirtualne obsługują multiemisji lub emisji?
Nie. Emisji i multiemisji nie są obsługiwane.

### <a name="what-protocols-can-i-use-within-vnets"></a>Jakie protokoły można używać w ramach sieci wirtualnych?
Można używać protokołów TCP, UDP i ICMP TCP/IP w ramach sieci wirtualnych. Emisji pojedynczej jest obsługiwana w ramach sieci wirtualnych, z wyjątkiem konfiguracji protokołu DHCP (Dynamic Host) za pośrednictwem emisji pojedynczej (źródłowy port UDP/68 / docelowy port UDP i 67). Multiemisji, emisji hermetyzowany IP-in-IP pakiety i pakiety Generic Routing Encapsulation (GRE) są zablokowane w ramach sieci wirtualnych. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Czy można wywołać Moje domyślne routery w sieci wirtualnej?
Nie.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Aby zdiagnozować łączności można używać tracert?
Nie.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Czy mogę dodać podsieci po utworzeniu sieci wirtualnej?
Tak. Podsieci można dodać do sieci wirtualnych w dowolnym momencie tak długo, jak zakres adresów podsieci nie jest częścią innej podsieci i dostępne miejsca w zakresie adresów sieci wirtualnej.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Rozmiar mojej podsieci można modyfikować po jej utworzeniu?
Tak. Można dodać, usunąć, rozszerzenia lub zmniejszyć rozmiar podsieci, jeśli nie ma żadnych maszyn wirtualnych wdrożonych w niej usługi.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Podsieci można zmodyfikować po ich utworzeniu?
Tak. Można dodać, usuwanie i modyfikowanie bloków CIDR używane przez sieci wirtualnej.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Czy Moje usług w sieci wirtualnej, można podłączyć do Internetu?
Tak. Wszystkie usługi wdrożyć w ramach sieci wirtualnej mogą łączyć ruch wychodzący do Internetu. Aby dowiedzieć się więcej na temat połączenia wychodzące internet na platformie Azure, zobacz [połączeń wychodzących](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Jeśli chcesz się połączyć przychodzącego z zasobem, który został wdrożony za pomocą Menedżera zasobów, muszą mieć publicznego adresu IP przypisane do niej. Aby dowiedzieć się więcej na temat publiczne adresy IP, zobacz [publicznego adresu IP, adresy](virtual-network-public-ip-address.md). Wdrożona na platformie Azure co usługa w chmurze Azure ma publicznie adresowanego wirtualne adresy IP przypisane do niej. Należy zdefiniować wejściowe punkty końcowe dla ról PaaS i maszyn wirtualnych do włączania tych usług do akceptowania połączeń z Internetu.

### <a name="do-vnets-support-ipv6"></a>Sieci wirtualne obsługują IPv6?
Nie. W tej chwili nie można używać protokołu IPv6 sieci wirtualnych. Możesz natomiast, przypisz IPv6 adresy usługi równoważenia obciążenia Azure załadować saldo maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [Omówienie protokołu IPv6 dla modułu równoważenia obciążenia Azure](../load-balancer/load-balancer-ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="can-a-vnet-span-regions"></a>Sieci wirtualnej może obejmować regionów?
Nie. Sieci wirtualnej jest ograniczony do jednego regionu. Sieć wirtualna jednak span stref dostępności. Aby dowiedzieć się więcej o strefach dostępności, zobacz [Availability zones overview (Omówienie stref dostępności)](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Możesz połączyć sieci wirtualnych w różnych regionach, z sieci wirtualnej komunikacji równorzędnej. Aby uzyskać więcej informacji, zobacz [komunikacji równorzędnej omówienie sieci wirtualnej](virtual-network-peering-overview.md)

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Do innej sieci wirtualnej platformy Azure można połączyć sieć wirtualną?
Tak. Jeden sieci wirtualnej mogą łączyć się przy użyciu innej sieci wirtualnej:
- **Sieci wirtualnej komunikacji równorzędnej**: Aby uzyskać więcej informacji, zobacz [sieci wirtualnej komunikacji równorzędnej — omówienie](virtual-network-peering-overview.md)
- **Brama sieci VPN Azure**: Aby uzyskać więcej informacji, zobacz [skonfigurować połączenia do wirtualnymi](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

## <a name="name-resolution-dns"></a>Rozpoznawanie nazw (domen DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Jakie są moje opcje DNS dla sieci wirtualnych?
Skorzystaj z tabeli decyzji na [rozpoznawanie nazwy dla maszyn wirtualnych i wystąpień roli](virtual-networks-name-resolution-for-vms-and-role-instances.md) dostępne opcje strony do przeprowadzenia wszystkich DNS.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Dla sieci wirtualnej można określić serwerów DNS?
Tak. Można określić adresy IP serwerów DNS w ustawieniach sieci wirtualnej. Ustawienie jest stosowane jako domyślne serwery DNS dla wszystkich maszyn wirtualnych w sieci wirtualnej.

### <a name="how-many-dns-servers-can-i-specify"></a>Jak wiele serwerów DNS można określić?
Odwołanie [Azure ogranicza](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Moje serwery DNS można zmodyfikować po utworzono sieci?
Tak. Lista serwerów DNS dla sieci wirtualnej można zmienić w dowolnym momencie. Lista serwerów DNS w przypadku zmiany, konieczne będzie ponowne uruchomienie wszystkich maszyn wirtualnych w sieci wirtualnej w kolejności ich do pobrania nowego serwera DNS.

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Co to jest DNS platformy Azure i działa z sieci wirtualnych?
DNS platformy Azure to usługa DNS wielodostępne oferowanych przez firmę Microsoft. Azure rejestruje wszystkich maszyn wirtualnych i wystąpień roli usługi chmury w tej usłudze. Ta usługa udostępnia rozpoznawanie nazw przez nazwę hosta dla maszyn wirtualnych i wystąpień roli zawartych w tej samej usługi w chmurze oraz nazwy FQDN dla maszyn wirtualnych i wystąpień roli w tej samej sieci wirtualnej. Aby dowiedzieć się więcej na temat DNS, zobacz [rozpoznawanie nazwy dla wystąpień roli maszyny wirtualne i usługi w chmurze](virtual-networks-name-resolution-for-vms-and-role-instances.md).

Jest to ograniczenie, aby pierwsze 100 usługi w chmurze w sieci wirtualnej do rozpoznawania nazw między dzierżawcy przy użyciu DNS platformy Azure. Jeśli używasz serwera DNS, to ograniczenie nie ma zastosowania.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Można zastąpić ustawienia DNS na podstawie usługi dla maszyny Wirtualnej lub w chmurze?
Tak. Można skonfigurować serwery DNS dla maszyny Wirtualnej lub w chmurze usługi, aby zastąpić domyślne ustawienia sieci. Jednak zalecane jest, że używasz możliwie DNS sieci.

### <a name="can-i-bring-my-own-dns-suffix"></a>Czy mogę przenieść mój własny sufiks DNS
Nie. Nie można określić niestandardowego sufiksu DNS dla Twojej sieci wirtualnych.

## <a name="connecting-virtual-machines"></a>Łączenie maszyny wirtualne

### <a name="can-i-deploy-vms-to-a-vnet"></a>Aby sieć wirtualną można wdrożyć maszyn wirtualnych?
Tak. Wszystkich interfejsów sieciowych (NIC) dołączona do maszyny Wirtualnej wdrożone za pośrednictwem modelu wdrażania usługi Resource Manager musi być podłączony do sieci wirtualnej. Opcjonalnie można podłączyć do sieci wirtualnej maszyn wirtualnych wdrożonych za pośrednictwem klasycznego modelu wdrażania.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Jakie są różne typy adresów IP, które można przypisać do maszyn wirtualnych?
* **Prywatne:** przypisane do każdej karty Sieciowej w ramach każdej maszyny Wirtualnej. Przypisany adres za pomocą metody statyczne lub dynamiczne. Prywatne adresy IP są przypisywane z zakresu, który określono w ustawieniach podsieci sieci wirtualnej. Zasoby wdrożone za pośrednictwem klasycznego modelu wdrażania są przypisywane prywatnych adresów IP, nawet jeśli nie masz połączenia sieci wirtualnej. Metody alokacji jest różny w zależności od tego, czy zasób został wdrożony za pomocą Menedżera zasobów lub klasycznego modelu wdrażania: 

  - **Menedżer zasobów**: prywatnego adresu IP przypisanego dynamicznej lub statycznej metody jest przypisana do maszyny wirtualnej (Resource Manager), aż do usunięcia zasobu. Różnica polega na zaznacz adres można przypisać przy użyciu statyczne, czy wybierze Azure przy użyciu dynamicznych. 
  - **Klasycznym**: prywatny adres IP przypisany za pomocą metody dynamicznej mogą ulec zmianie, gdy maszyna wirtualna (klasyczna) ponownego uruchomienia maszyny Wirtualnej po przejściu w stan zatrzymania (cofnięciu przydziału). Jeśli potrzebujesz upewnić się, że prywatnego adresu IP zasobu wdrożone za pośrednictwem klasycznego modelu wdrażania nigdy nie zmienia się, należy przypisać prywatnego adresu IP z metody statycznej.

* **Publicznego:** opcjonalnie przypisane do kart sieciowych dołączonych do maszyn wirtualnych wdrożonych za pośrednictwem modelu wdrażania usługi Azure Resource Manager. Adres można przypisać za pomocą metody statyczne lub dynamiczne alokacji. Wszystkie maszyny wirtualne i usługi w chmurze wystąpień roli wdrożone za pośrednictwem klasycznego modelu wdrażania istnieje w ramach usługi w chmurze, która jest przypisana *dynamiczne*, publiczny wirtualny adres IP (VIP). Publiczny *statycznych* adres IP, nazywany [zastrzeżony adres IP](virtual-networks-reserved-public-ip.md), opcjonalnie można przypisać jako adresu VIP. Publiczne adresy IP można przypisać do poszczególnych maszyn wirtualnych lub usługi w chmurze wystąpień roli wdrożone za pośrednictwem klasycznego modelu wdrażania. Te adresy są nazywane [wystąpienie poziomu publicznego adresu IP (ILPIP](virtual-networks-instance-level-public-ip.md) adresów i może być przypisywany dynamicznie.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Dla maszyny Wirtualnej, który utworzy w późniejszym czasie można rezerwować prywatnego adresu IP?
Nie. Nie można zarezerwować prywatnego adresu IP. Jeśli dostępny jest prywatny adres IP, jest przypisany do maszyny Wirtualnej lub roli wystąpienia przez serwer DHCP. Maszyna wirtualna może lub nie może być jedną, która ma przypisany prywatny adres IP. Można jednak zmienić prywatnego adresu IP maszyny wirtualnej utworzone, wszystkie dostępne prywatny adres IP.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Czy prywatnej zmiany adresów IP dla maszyn wirtualnych w sieci wirtualnej?
To zależy. Jeśli maszyna wirtualna została wdrożona za pomocą Menedżera zasobów nie, niezależnie od tego, czy został przypisany adres IP za pomocą metody statyczne lub dynamiczne alokacji. Jeśli maszyna wirtualna została wdrożona za pośrednictwem klasycznego modelu wdrażania, dynamicznych adresów IP można zmienić po uruchomieniu maszyny Wirtualnej po przejściu w stan zatrzymania (cofnięciu przydziału). Ten adres jest zwalniany z maszyny Wirtualnej wdrożone za pośrednictwem albo model wdrażania, gdy maszyna wirtualna zostanie usunięta.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Można ręcznie przypisać adresy IP do karty sieciowe w systemie operacyjnym maszyny Wirtualnej?
Tak, ale nie jest zalecane, chyba że niezbędne, np. gdy przypisywanie wielu adresów IP do maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Dodawanie wielu IP adresów do maszyny wirtualnej](virtual-network-multiple-ip-addresses-portal.md#os-config). Dołączenia adresu IP przypisanego do usługi Azure karty Sieciowej do maszyny Wirtualnej zmian i adresu IP w systemie operacyjnym maszyny Wirtualnej jest inny, utracić łączność z maszyną wirtualną.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Jeśli zatrzymać miejsce wdrożenia usługi w chmurze lub zamknięcie maszyny Wirtualnej z poziomu systemu operacyjnego, co się dzieje z mojej adresów IP?
Brak elementów. Adresy IP (VIP publiczne, publiczne i prywatne) pozostają przypisane do maszyny Wirtualnej lub miejsca wdrożenia usługi w chmurze.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Można przenieść maszyny wirtualne z jednej podsieci do innej podsieci w sieci wirtualnej bez ponownego wdrożenia?
Tak. Więcej informacji można znaleźć [jak przenieść do innej podsieci maszyny Wirtualnej lub roli wystąpienia](virtual-networks-move-vm-role-to-subnet.md) artykułu.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Moje maszyny wirtualnej można skonfigurować statyczny adres MAC?
Nie. Adres MAC nie można skonfigurować statycznie.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Adres MAC jest taka sama dla moich maszyny Wirtualnej po jej utworzeniu?
Tak, adres MAC jest taka sama dla maszyny Wirtualnej wdrożone za pośrednictwem usługi Resource Manager i klasycznych modeli wdrażania, dopóki zostanie usunięty. Wcześniej adres MAC został wydany, jeśli maszyna wirtualna została zatrzymana (cofnięciu przydziału), ale teraz adres MAC jest zachowywana nawet wtedy, gdy maszyna wirtualna jest w stanie deallocated.

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Można podłączyć do Internetu z maszyny Wirtualnej w sieci wirtualnej?
Tak. Wszystkie maszyny wirtualne i usługi w chmurze wystąpień roli wdrożyć w ramach sieci wirtualnej można połączyć z Internetem.

## <a name="azure-services-that-connect-to-vnets"></a>Usług Azure, które nawiązać połączenia z sieciami wirtualnymi

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Z sieci wirtualnej można używać aplikacji sieci Web usługi aplikacji Azure?
Tak. Można wdrażać aplikacje sieci Web w sieci wirtualnej przy użyciu ASE (środowiska usługi aplikacji). Jeśli masz połączenie punkt lokacja skonfigurowane dla sieci wirtualnej, wszystkie aplikacje sieci Web bezpiecznie łączyć i uzyskiwać dostęp do zasobów w sieci wirtualnej. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Tworzenie aplikacji sieci Web w środowisku usługi aplikacji](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Integracja aplikacji z sieci wirtualnej platformy Azure](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Przy użyciu integracji sieci wirtualnej i połączeń hybrydowych z aplikacji sieci Web](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json#hybrid-connections-and-app-service-environments)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Z sieci web i proces roboczy ról (PaaS) w sieci wirtualnej można wdrożyć usługi w chmurze?
Tak. Usługi w chmurze wystąpień roli w ramach sieci wirtualnych (opcjonalnie) można wdrożyć. Aby to zrobić, możesz Określ nazwę sieci wirtualnej i mapowania roli/podsieci w sekcji konfiguracji sieci konfiguracji usługi. Nie należy do żadnego z plików binarnych aktualizacji.

### <a name="can-i-connect-a-virtual-machine-scale-set-vmss-to-a-vnet"></a>Ustawianie skalowania maszyny wirtualnej (VMSS) można podłączyć do sieci wirtualnej?
Tak. VMSS należy nawiązać połączenie sieci wirtualnej.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Jest pełna lista Azure usługi, że można wdrożyć zasobów w sieci wirtualnej?

Tak, aby uzyskać więcej informacji, zobacz [integracji sieci wirtualnej dla usługi Azure](virtual-network-for-azure-services.md).

### <a name="which-azure-paas-resources-can-i-restrict-access-to-from-a-vnet"></a>Zasoby Azure PaaS można ograniczyć dostęp do z sieci wirtualnej?

Zasoby wdrożone za pośrednictwem niektórych usług Azure PaaS (takich jak usługi Azure Storage i Azure SQL Database), można ograniczyć dostęp do sieci do tylko do zasobów w sieci wirtualnej przy użyciu punktów końcowych usługi sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie punktów końcowych usługi sieci wirtualnej](virtual-network-service-endpoints-overview.md).

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Czy mogę przenieść mój usług i sieci wirtualnych
Nie. Nie można przenieść usług i sieci wirtualnych. Aby przenieść zasobu do innej sieci wirtualnej, należy usunąć i ponownie wdrożyć zasobu.

## <a name="security"></a>Bezpieczeństwo

### <a name="what-is-the-security-model-for-vnets"></a>Co to jest model zabezpieczeń dla sieci wirtualnych?
Sieci wirtualne są odizolowane od siebie i innych usług hostowanych w infrastrukturze Azure. Sieci wirtualnej jest granicą zaufania.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Czy mogę ograniczyć przepływ ruchu przychodzącego i wychodzącego do zasobów połączone sieci wirtualnej
Tak. Możesz zastosować [grup zabezpieczeń sieci](security-overview.md) do poszczególnych podsieci w sieci wirtualnej, kart sieciowych jest dołączony do sieci wirtualnej, lub obie.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Czy można zaimplementować zapory między zasobami połączone sieci wirtualnej?
Tak. Można wdrożyć [urządzenie wirtualne zapory sieciowej](https://azure.microsoft.com/marketplace/?term=firewall) z kilku dostawców za pośrednictwem portalu Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Dostępne Zabezpieczanie sieci wirtualne są informacje?
Tak. Aby uzyskać więcej informacji, zobacz [Przegląd zabezpieczeń sieci Azure](../security/security-network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="apis-schemas-and-tools"></a>Interfejsy API, schematy i narzędzia

### <a name="can-i-manage-vnets-from-code"></a>Sieci wirtualne można zarządzać z kodu?
Tak. Można użyć interfejsów API REST dla sieci wirtualnych w [usługi Azure Resource Manager](/rest/api/virtual-network) i [klasyczny (Zarządzanie usługą)](http://go.microsoft.com/fwlink/?LinkId=296833) modele wdrażania.

### <a name="is-there-tooling-support-for-vnets"></a>Istnieje już obsługę narzędzi dla sieci wirtualnych?
Tak. Dowiedz się więcej o korzystaniu z:
- Portalu Azure, aby wdrożyć sieci wirtualnych za pośrednictwem [usługi Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) i [klasycznego](virtual-networks-create-vnet-classic-pportal.md) modele wdrażania.
- PowerShell do zarządzania sieciami wirtualnymi, wdrażać przy użyciu [Resource Manager](/powershell/module/azurerm.network) i [klasycznego](/powershell/module/azure/?view=azuresmps-3.7.0) modele wdrażania.
- Azure interfejsu wiersza polecenia (CLI) do wdrażania i zarządzania wdrożone za pośrednictwem sieci wirtualnych [Resource Manager](/cli/azure/network/vnet) i [klasycznego](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-network-commands-to-manage-network-resources) modele wdrażania.  
