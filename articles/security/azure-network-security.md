---
title: Zabezpieczenia sieci platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat przetwarzania danych usług w chmurze zawierających szeroką gamę wystąpienia obliczeniowe i usług, które można skalować w górę i w dół automatycznie na potrzeby aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: swadhwa
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: cc64ef8d820db6a072b708323eb110d62ed0a83c
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2018
---
# <a name="azure-network-security"></a>Zabezpieczenia sieci na platformie Azure

Wiemy, że zabezpieczenia są zadania, co w chmurze i jak ważne jest aby znaleźć dokładne i aktualne informacje na temat zabezpieczeń platformy Azure. Najważniejsze przyczyny na potrzeby aplikacji i usług Azure ma korzystać z platformy Azure szerokiej gamy narzędzi zabezpieczeń i możliwości. Te narzędzia i funkcje pomocy umożliwiają tworzenie bezpiecznych rozwiązań na platformie Azure.

Microsoft Azure udostępnia poufność, integralność i dostępność danych klienta, jednoczesnym accountability przezroczysty. Aby lepiej zrozumieć kolekcja formantów zabezpieczeń sieci zaimplementowana w systemie Microsoft Azure z perspektywy klienta, w tym artykule "Zabezpieczenia sieciowe Azure", są zapisywane w zapewnić kompleksowe przyjrzeć się sieci dostępnych opcji zabezpieczeń Microsoft Azure.

Ten dokument jest przeznaczony do o szerokiej gamy kontrolek sieci, które można skonfigurować tak, aby zwiększyć bezpieczeństwo rozwiązania, które wdrażasz na platformie Azure. Jeśli interesuje Cię do zabezpieczania sieci szkieletowej z platformą Azure czego firmy Microsoft, zobacz sekcję zabezpieczeń platformy Azure w [Microsoft Trust Center](https://www.microsoft.com/trustcenter/security/azure-security).

## <a name="azure-platform"></a>Platforma Azure

Azure to platforma usługi chmury publicznej, która obsługuje szeroki wybór systemów operacyjnych, programowania języków, struktury, narzędzia, baz danych i urządzeń.  Kontenery systemu Linux można uruchamiać z integracją rozwiązania Docker; Tworzenie aplikacji za pomocą języka JavaScript, Python, .NET, PHP, Java i Node.js; Kompilacja zapleczy dla systemu iOS, Android i Windows urządzeń. Usługi w chmurze Azure obsługuje te same technologie miliony deweloperów i informatyków już zależne i zaufania.

Podczas tworzenia lub migracji zasobów informatycznych do dostawcy usług w chmurze publicznej, możesz używają możliwości Twojej organizacji do ochrony aplikacji i danych z usług i formanty zapewniają do zarządzania zabezpieczeniami elementów zawartości opartej na chmurze.

Infrastruktury platformy Azure umożliwiają z funkcji aplikacji do obsługi jednocześnie miliony klientów i zapewnia foundation godne zaufania, na którym firmy mogą spełnić ich wymagań dotyczących zabezpieczeń. Ponadto Azure udostępnia szeroką gamę kolekcji zabezpieczeń można skonfigurować opcje i możliwości ich kontroli, dzięki czemu można dostosować zabezpieczeń, aby spełnić unikatowe wymagania danej organizacji wdrożeń.

## <a name="abstract"></a>Abstrakcyjny

Usługi w chmurze publicznej Microsoft świadczenia usług hiperskali i infrastruktury, funkcje klasy korporacyjnej oraz wiele opcji na potrzeby łączności hybrydowej. Można uzyskać dostęp do tych usług za pośrednictwem Internetu lub z usługi Azure ExpressRoute, który zapewnia łączność w sieci prywatnej. Platforma Microsoft Azure umożliwia bezproblemowo rozszerzają infrastruktury do chmury i tworzenia wielu architektur. Ponadto stron trzecich można włączyć udoskonalone funkcje oferty usług zabezpieczeń oraz urządzenia wirtualnego.

Usługi sieciowe systemu Azure zwiększenie elastyczności, dostępności, odporności, bezpieczeństwa i integralności zgodnie z projektem. Ten dokument zawiera szczegółowe informacje na funkcje sieci platformy Azure i informacji na temat jak klienci mogą użyć funkcji natywnego zabezpieczeń platformy Azure w celu ochrony ich zasobów informacyjnych.

Zamierzone odbiorcy na dokumencie obejmują:

- Menedżerowie technicznych, Administratorzy sieci i deweloperów, którzy szukają rozwiązań zabezpieczeń dostępny i obsługiwany na platformie Azure.

-   MSP lub business procesu dyrektorzy, którzy w celu uzyskania ogólne omówienie Azure technologie sieciowe i usług, które mają zastosowanie w dyskusjach ze specjalistami wokół zabezpieczeń sieci w chmurze publicznej Azure.

## <a name="azure-networking-big-picture"></a>Azure networking duży obraz
Microsoft Azure obejmuje to niezawodna infrastruktura sieci do obsługi aplikacji i wymaganiami dotyczącymi łączności usługi. Łączność sieciowa będzie możliwe między zasobami znajdującymi się na platformie Azure, między lokalnymi i Azure hostowanych zasobów oraz do i z Internetu i Azure.

![Azure Networking duży obraz](media/azure-network-security/azure-network-security-fig-1.png)

[Infrastruktury sieci platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) pozwala bezpiecznie łączyć z zasobami Azure ze sobą w sieciach wirtualnych (sieci wirtualne). Sieci wirtualnej jest odzwierciedla w chmurze Twoją sieć. Sieci wirtualnej jest logiczną izolacją sieci chmury Azure przeznaczoną dla Twojej subskrypcji. Możesz połączyć sieci wirtualnych do sieci lokalnej.

Obsługuje platformy Azure w wersji dedykowanej łącza sieci WAN łączność z siecią lokalną i sieci wirtualnej platformy Azure z [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Powiązanie Azure i witryny sieci Web używa dedykowane połączenie, które nie przechodzi przez publicznej sieci Internet. Jeśli aplikacja Azure jest uruchomiona w wielu centrach danych, możesz użyć [usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) rozsyłanie żądań od użytkowników inteligentnie między wystąpieniami aplikacji. Można również kierować ruchem do usługi nie są uruchomione na platformie Azure, jeśli są one dostępne z Internetu.

## <a name="enterprise-view-of-azure-networking-components"></a>Widok Enterprise Azure składników sieciowych
Platforma Azure ma wiele składników sieciowych, które mają zastosowanie do dyskusji zabezpieczeń sieci. Firma Microsoft opisano te składniki sieci i skoncentrować się na problemy dotyczące zabezpieczeń związane z nimi.

> [!Note]
> Nie wszystkie aspekty sieci platformy Azure są opisane — omówiono tylko te uznawane za istotną w planowaniu i projektowaniu bezpiecznej infrastruktury sieciowej wokół usług i aplikacji, które wdrażasz na platformie Azure.

W tym dokumencie będzie obejmują następujące możliwości przedsiębiorstwa w zakresie obsługi platformy Azure:

-   Podstawowe połączenie sieciowe

-   Połączenia hybrydowe

-   Środki zabezpieczające.

-   Sprawdzanie poprawności sieci

### <a name="basic-network-connectivity"></a>Podstawowe połączenie sieciowe

[Sieci wirtualnej Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) usługa umożliwia bezpieczne łączenie z zasobami Azure ze sobą z sieciami wirtualnymi (VNet). Sieci wirtualnej jest odzwierciedla w chmurze Twoją sieć. Sieci wirtualnej jest logiczną izolacją infrastruktury sieci platformy Azure w wersji dedykowanej do subskrypcji. Można też połączyć sieci wirtualnych i przy użyciu sieci VPN typu lokacja lokacja sieci lokalnej i w wersji dedykowanej [łącza sieci WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Podstawowe połączenie sieciowe](media/azure-network-security/azure-network-security-fig-2.png)

Opis Użyj maszyn wirtualnych z serwerami hosta na platformie Azure, pytanie jest sposób łączenia tych maszyn wirtualnych do sieci. Odpowiedź jest, aby maszyny wirtualne łączyć się z [sieci wirtualnej Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Sieci wirtualnych platformy Azure, są tak jak wirtualnej sieci lokalnej za pomocą własnych rozwiązań platformy wirtualizacji, takich jak Microsoft Hyper-V lub VMware.

#### <a name="intra-vnet-connectivity"></a>Łączność sieci wirtualnej wewnątrz

Sieci wirtualne można połączyć ze sobą, włączanie zasoby podłączone do jednej sieci wirtualnej do komunikują się ze sobą za pośrednictwem sieci wirtualnych. Do sieci wirtualnych się ze sobą łączyć, można użyć jednego lub obu z następujących opcji:

- **Komunikacja równorzędna:** umożliwia zasoby podłączone do różnych sieci wirtualnych platformy Azure w ramach tej samej lokalizacji platformy Azure do komunikowania się ze sobą. Przepustowości i opóźnień w sieci wirtualnej jest taka sama, jak gdyby zasoby były podłączone do tej samej sieci wirtualnej. Aby dowiedzieć się więcej na temat komunikacji równorzędnej, przeczytaj [równorzędna sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

 ![Komunikacja równorzędna](media/azure-network-security/azure-network-security-fig-3.png)

- **Połączenia do wirtualnymi:** umożliwia zasoby podłączone do różnych sieć wirtualną Azure w ramach tego samego lub innego lokalizacji platformy Azure. W przeciwieństwie do komunikacji równorzędnej, przepustowości jest ograniczona między sieciami wirtualnymi, ponieważ ruch musi przepływać przez bramę sieci VPN platformy Azure.

![Połączenia do wirtualnymi](media/azure-network-security/azure-network-security-fig-4.png)


Aby dowiedzieć się więcej o nawiązywaniu połączeń sieci wirtualnych z połączeniem do wirtualnymi, przeczytaj [skonfigurować artykułu połączenia do wirtualnymi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Funkcje sieci wirtualnej platformy Azure:

Jak widać, sieci wirtualnej platformy Azure udostępnia maszyn wirtualnych, aby połączyć się z siecią, tak aby umożliwić im połączenie z innymi zasobami sieciowymi w bezpieczny sposób. Podstawowej łączności jest jednak tylko początek. Następujące funkcje usługi Azure Virtual Network ujawnić właściwości zabezpieczeń sieci wirtualnej platformy Azure:

-   Izolacja

-   Łączność z Internetem

-   Łączność zasobów platformy Azure

-   Łączność sieci wirtualnej

-   Łączność lokalna

-   Filtrowanie ruchu

-   Routing

**Izolacji**

Sieci wirtualne są [izolowanego](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) od siebie nawzajem. Można utworzyć oddzielne sieci wirtualnych do tworzenia, testowania i produkcji, która będzie używać tego samego [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) bloki adresów. Z drugiej strony możesz utworzyć wiele sieci wirtualnych, użyj innego bloków adresów CIDR i połączyć ze sobą sieci. Sieć wirtualną można podzielić na wiele podsieci.

Platforma Azure udostępnia rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i [usługi w chmurze](https://azure.microsoft.com/services/cloud-services/) wystąpień roli podłączone do sieci wirtualnej. Opcjonalnie można skonfigurować sieć wirtualną do użycia zamiast rozpoznawania nazw wewnętrznych Azure własne serwery DNS.

Można zaimplementować wiele sieci wirtualnych w każdym Azure [subskrypcji](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) i Azure [region](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. Dla każdej sieci wirtualnej można:

-   Określenie niestandardowej prywatnej przestrzeni adresowej IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Azure przypisuje zasoby podłączone do sieci wirtualnej prywatnego adresu IP z przestrzeni adresowej, możesz przypisać.

-   Segment sieci wirtualnej do co najmniej jednej podsieci i przydzielić część przestrzeni adresowej sieci wirtualnej dla każdej podsieci.

-   Użyj rozpoznawania nazw platformy Azure lub Podaj własny serwer DNS do użytku przez zasoby podłączone do sieci wirtualnej. Aby dowiedzieć się więcej na temat rozpoznawania nazw w sieci wirtualnych, przeczytaj [rozpoznawanie nazw dla maszyn wirtualnych i usług w chmurze](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Łączność z Internetem**

Wszystkie [Azure maszyn wirtualnych (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) i wystąpień roli usługi w chmurze podłączone do sieci wirtualnej mają dostęp do Internetu, domyślnie. Można również włączyć dostęp przychodzący do określonych zasobów, zgodnie z potrzebami. (VM) i wystąpień roli usługi w chmurze podłączone do sieci wirtualnej mają dostęp do Internetu, domyślnie. Można również włączyć dostęp przychodzący do określonych zasobów, zgodnie z potrzebami.

Wszystkie zasoby podłączone do sieci wirtualnej domyślnie mają łączność wychodząca z Internetem. Prywatny adres IP zasobu jest źródłowy adres sieciowy translacji (SNAT) do publicznego adresu IP przez infrastrukturę platformy Azure. Łączność domyślne można zmienić dzięki implementacji niestandardowego routingu i filtrowanie ruchu. Aby dowiedzieć się więcej na temat wychodzące połączenie z Internetem, przeczytaj [Opis połączeń wychodzących na platformie Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Do komunikacji przychodzącej zasobów platformy Azure z Internetu lub komunikacji ruch wychodzący do Internetu bez SNAT, musi on mieć przypisaną publicznego adresu IP. Aby dowiedzieć się więcej na temat publiczne adresy IP, przeczytaj [publicznego adresu IP, adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Łączność zasobów platformy Azure**

[Zasoby platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) takie jak usługi w chmurze i maszyn wirtualnych, które mogą być podłączone do tej samej sieci wirtualnej. Zasoby można połączyć ze sobą przy użyciu prywatnych adresów IP, nawet jeśli znajdują się w różnych podsieciach. Platforma Azure udostępnia domyślny routing między podsieciami, sieci wirtualnych i sieci lokalnej, więc nie trzeba konfigurować i zarządzać nimi trasy.

Możesz połączyć kilka zasobów platformy Azure do sieci wirtualnej, takie jak maszyn wirtualnych (VM), usługi w chmurze środowiska usługi App Service i zestawy skalowania maszyny wirtualnej. Połączenie maszyn wirtualnych do podsieci w sieci wirtualnej za pośrednictwem interfejsu sieciowego (NIC). Aby dowiedzieć się więcej na temat kart sieciowych, przeczytaj [interfejsy sieciowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Łączność sieci wirtualnej**

[Sieci wirtualne](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) może być połączone ze sobą, włączanie zasoby podłączone do żadnych sieci wirtualnej do komunikowania się z dowolnego zasobu w innych sieci wirtualnej.

Sieci wirtualne można połączyć ze sobą, włączanie zasoby podłączone do jednej sieci wirtualnej do komunikują się ze sobą za pośrednictwem sieci wirtualnych. Do sieci wirtualnych się ze sobą łączyć, można użyć jednego lub obu z następujących opcji:

- **Komunikacja równorzędna:** umożliwia zasoby podłączone do różnych sieci wirtualnych platformy Azure w ramach tej samej lokalizacji platformy Azure do komunikowania się ze sobą. Przepustowości i opóźnień między sieciami wirtualnymi jest taka sama tak, jakby zasoby były podłączone do tej samej VNet.To Dowiedz się więcej o komunikacji równorzędnej, przeczytaj [równorzędna sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Połączenia do wirtualnymi:** umożliwia zasoby podłączone do różnych sieć wirtualną Azure w ramach tego samego lub innego lokalizacji platformy Azure. W przeciwieństwie do komunikacji równorzędnej, przepustowości jest ograniczona między sieciami wirtualnymi, ponieważ ruch musi przepływać przez bramę sieci VPN platformy Azure. Aby dowiedzieć się więcej o nawiązywaniu połączeń sieci wirtualnych z połączeniem sieci wirtualnej do sieci wirtualnej. Aby dowiedzieć się więcej, przeczytaj [skonfigurować połączenia do wirtualnymi](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Łączność lokalna**

Sieci wirtualne można podłączyć do [lokalnymi](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci za pośrednictwem sieci prywatnej połączeń między siecią a Azure lub za pośrednictwem połączenia VPN lokacja lokacja za pośrednictwem Internetu.

Sieci lokalnej można nawiązać sieć wirtualną przy użyciu dowolnej kombinacji następujących opcji:

- **Punkt lokacja wirtualnej sieci prywatnej (VPN):** między jednym komputerze podłączone do sieci i sieci wirtualnej. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Połączenie korzysta z protokołu SSTP zapewnienie szyfrowaną komunikację przez Internet między Komputerem a sieci wirtualnej. Czas oczekiwania na VPN punkt lokacja jest nieprzewidywalne, ponieważ dane są przesyłane za pośrednictwem Internetu.

- **Sieć VPN lokacja lokacja:** między urządzenie sieci VPN i bramy sieci VPN platformy Azure. Ten typ połączenia pozwala dowolnego zasobu lokalnego zezwalają na dostęp do sieci wirtualnej. Połączenie jest IPsec i IKE sieci VPN, który zapewnia szyfrowaną komunikację przez Internet między urządzeniem lokalnymi i bramy sieci VPN platformy Azure. Czas oczekiwania na połączenie lokacja lokacja jest nieoczekiwane, ponieważ dane są przesyłane za pośrednictwem Internetu.

- **Azure ExpressRoute:** połączenie nawiązywane pomiędzy siecią i platformą Azure za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzą przez Internet. Czas oczekiwania na połączenie ExpressRoute jest przewidywalne, ponieważ ruch nie przechodzenie przez Internet. Aby dowiedzieć się więcej na temat wszystkich poprzednich opcji połączenia, przeczytaj [Diagram topologii połączenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrowanie ruchu**

Wystąpienia roli maszyny Wirtualnej i usługi w chmurze [ruch sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) można użyć do filtrowania ruchu przychodzącego i wychodzącego przez źródłowy adres IP i port docelowy adres IP i portu i protokołu.

Ruch sieciowy pomiędzy podsieciami możesz filtrować przy użyciu jednej lub obu poniższych opcji:

- **Sieciowe grupy zabezpieczeń (NSG):** każda grupa NSG może zawierać wiele reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego umożliwiające filtrować ruch źródłowy i docelowy adres IP, portu i protokołu. Grupy NSG można zastosować do każdej karty Sieciowej na maszynie wirtualnej. Można także zastosować grupy NSG do podsieci karty Sieciowej, lub innych zasobów platformy Azure jest połączona. Aby dowiedzieć się więcej na temat grup NSG, przeczytaj [sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Wirtualnych urządzeń sieciowych:** urządzenie sieci wirtualnej jest uruchomione oprogramowanie, które wykonuje funkcję sieci, takie jak Zapora maszyny Wirtualnej. Wyświetl listę NVAs dostępne w portalu Azure Marketplace. NVAs są również dostępne, które zapewniają funkcje ruchu Optymalizacja sieci WAN i innych sieci. NVAs zwykle są używane przez użytkownika lub trasy protokołu BGP. NVA można również użyć do filtrowania ruchu między sieciami wirtualnymi.

**Routing**

Opcjonalnie można zastąpić domyślne Azure routingu, konfigurowanie własnego trasy lub użyj trasy protokołu BGP za pośrednictwem bramy sieci.

Platforma Azure tworzy tabele tras, które umożliwiają zasoby podłączone do żadnej podsieci w dowolnej sieci wirtualnej do komunikowania się ze sobą, domyślnie. Możesz zaimplementować jedną lub obie poniższe opcje, aby zastąpić domyślne trasy tworzone przez platformę Azure:

- **Trasy zdefiniowane przez użytkownika:** tworzenia tabel tras niestandardowych trasy tego formantu, których ruch jest kierowany do dla każdej podsieci. Aby dowiedzieć się więcej na temat trasy zdefiniowane przez użytkownika, przeczytaj [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Trasy protokołu BGP:** połączenie Twojej sieci wirtualnej sieci lokalnej przy użyciu połączenia bramy sieci VPN platformy Azure lub usługi ExpressRoute, podczas rozpowszechniania tras BGP do Twojej sieci wirtualnych.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Połączenie z Internetem hybrydowego: połączyć się z siecią lokalną
Sieci lokalnej można nawiązać sieć wirtualną przy użyciu dowolnej kombinacji następujących opcji:

-   Łączność z Internetem

-   Sieć VPN punkt lokacja (P2S sieci VPN)

-   Sieci VPN typu lokacja lokacja (S2S sieci VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Połączenie z Internetem

Zgodnie z sugestią, jego nazwa, połączenie z Internetem sprawia, że obciążeń z Internetu, konfigurując można ujawnić różne publiczne punkty końcowe obciążeń, które na żywo w sieci wirtualnej. Te obciążenia mogą zostać ujawnione przy użyciu [modułu równoważenia obciążenia internetowy](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) lub po prostu przypisywane do publicznego adresu IP do maszyny Wirtualnej. W ten sposób staje się możliwe do wszelkich w Internecie, aby mieć dostęp do maszyny wirtualnej, zaporę hosta dostarczone [sieciowej grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), i [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) zezwala na to.

W tym scenariuszu może spowodować narażenie aplikacji, która musi być publiczny z Internetem oraz mieć możliwość nawiązania połączenia z dowolnego miejsca lub z określonych lokalizacji w zależności od konfiguracji obciążeń.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Sieć VPN punkt lokacja lub sieci VPN typu lokacja lokacja
Te dwa należące do tej samej kategorii. Potrzebuje Twojej sieci wirtualnej mają bramy sieci VPN i możesz połączyć do niego przy użyciu klienta sieci VPN dla stacji roboczej w ramach [konfiguracjępunktuwitryny](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) lub skonfigurować lokalną [urządzenia sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices) aby można było zakończyć VPN lokacja lokacja. W ten sposób urządzeń lokalnych, można połączyć się z zasobów w sieci wirtualnej.

Konfiguracja typu punkt-lokacja pozwala utworzyć indywidualne bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenie punkt-lokacja (P2S) to połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol).

![Sieć VPN punkt lokacja](media/azure-network-security/azure-network-security-fig-5.png)

Połączenia punkt-lokacja są przydatne, jeśli chcesz połączyć się z sieci wirtualnej z lokalizacji zdalnej, takich jak z domu lub Centrum konferencji, lub jeśli masz tylko kilku klientów, którzy muszą łączyć się z siecią wirtualną.

Połączenia typu punkt-lokacja nie wymagają do prawidłowego działania urządzenia sieci VPN ani publicznego adresu IP. Połączenie z siecią VPN jest nawiązywane z komputera klienckiego. W związku z tym P2S nie jest zalecane sposób łączenia na platformie Azure, w razie potrzeby trwałe połączenie z wielu urządzeń lokalnych, jak i komputerów do sieci platformy Azure.

![Sieci VPN typu lokacja lokacja](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Aby uzyskać więcej informacji o lokacji punktu połączenia, zobacz [punkt-lokacja FA v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2).

Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. To połączenie odbywa się za pośrednictwem Internetu oraz pozwala na informacji "tunnel" wewnątrz zaszyfrowanych łącza między siecią a Azure. Sieci VPN typu lokacja lokacja jest bezpieczne, dojrzała technologia, która jest wdrażany w przedsiębiorstwach wszystkich rozmiarów dekad. Tunel szyfrowanie odbywa się przy użyciu [trybu tunelowania IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Podczas sieci VPN typu lokacja lokacja jest technologią zaufanych, niezawodny i ustalonych ruchu w tunelu przechodzenie przez Internet. Ponadto przepustowość stosunkowo jest ograniczona do wysokości około 200 MB/s.

Jeśli potrzebujesz wyjątkowych poziom bezpieczeństwa i wydajności dla połączeń między różnymi lokalizacjami, zalecane jest użycie Azure ExpressRoute dla łączność między lokalizacjami. ExpressRoute jest dedykowanych sieci WAN łącza między Twojej lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Ponieważ jest to połączenie telco, dane nie są przesyłane w Internecie i w związku z tym nie jest narażony na potencjalne ryzyko związane z komunikacją internetową.

> [!Note]
> Aby uzyskać więcej informacji na temat bram sieci VPN, zobacz [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Dedykowane łącza sieci WAN
Microsoft Azure ExpressRoute umożliwia rozszerzenie sieci lokalnej do platformy Azure za pośrednictwem dedykowanego połączenia prywatne w ramach dostawcy łączności.

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

![ Dedykowane łącza sieci WAN](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Aby uzyskać informacje dotyczące połączenia sieci z firmą Microsoft za pomocą usługi ExpressRoute, zobacz [modeli połączenie ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) i [opis techniczny ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Jako przy użyciu opcji sieci VPN typu lokacja lokacja usługi ExpressRoute umożliwia łączenie się z zasobami, które nie są zawsze tylko jedna sieć wirtualną. W rzeczywistości w zależności od wersji, można połączyć do 10 sieci wirtualnych. Jeśli masz [dodatek premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), połączenia maksymalnie 100 sieci wirtualne są możliwe w zależności od przepustowości. Aby uzyskać więcej informacji, takich jak te typy połączeń wygląd, przeczytaj [Diagram topologii połączenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Środki zabezpieczające.
Sieci wirtualnej platformy Azure zapewnia bezpieczny, logiczną sieci, która jest odizolowana od innych sieci wirtualnych i obsługuje wiele kontroli bezpieczeństwa, używanych w sieci lokalnej. Klientom tworzenie własnych struktury przy użyciu: podsieci — ich użycia własnych zakresu prywatnych adresów IP, skonfigurować tabele tras, sieciowej grupy zabezpieczeń, listy kontroli dostępu (ACL), bramy i wirtualnych urządzeń do uruchamiania obciążeń w chmurze.

Poniżej przedstawiono kontroli bezpieczeństwa, używanych w sieci wirtualnej platformy Azure:

-   Kontrolę dostępu do sieci

-   Trasy zdefiniowane przez użytkownika

-   Urządzenie zabezpieczeń sieci

-   Application Gateway

-   Zapora aplikacji sieci Web platformy Azure

-   Kontrola dostępność sieci

#### <a name="network-access-controls"></a>Kontrolę dostępu do sieci
Gdy sieci wirtualnej platformy Azure (VNet) stanowi podstawę model sieci platformy Azure i zapewnia izolacji i ochrony, [grup zabezpieczeń sieci (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) są główne narzędzie służy do wymuszania i kontrolować reguły ruchu sieciowego na poziomie sieci.

![ Kontrolę dostępu do sieci](media/azure-network-security/azure-network-security-fig-8.png)


Można kontrolować dostęp przez zezwalanie na dostęp lub odmawianie komunikacji między obciążeń w ramach sieci wirtualnej, od systemów w sieci klienta za pośrednictwem łączności między lokalizacjami lub bezpośrednia komunikacja z Internetem.

Na diagramie zarówno sieci wirtualnych, jak i grupy NSG znajdują się w określonej warstwie Azure ogólną zabezpieczeń stosu, gdzie grup NSG, przez i wirtualnych urządzeń sieciowych może służyć do tworzenia granic zabezpieczeń do wdrożenia aplikacji w sieci chronionej ochrony.

Grupy NSG Użyj krotka 5, aby ocenić ruchu (i są używane w regułach, które można skonfigurować grupy NSG):

-   [Źródłowy i docelowy adres IP](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Port źródłowy i docelowy](https://technet.microsoft.com/library/dd197515)

-   Protokół: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) lub [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Oznacza to, że można kontrolować dostęp między jednej maszyny Wirtualnej i grupy maszyn wirtualnych lub jednej maszyny Wirtualnej do innego jednej maszyny Wirtualnej lub całej podsieci. Ponownie należy pamiętać, że jest to prosty pakietów filtrowania, pakietów nie pełnej kontroli. Brak weryfikacji protokołu lub identyfikatorów poziomu sieci i możliwości adresów IP należących do grupy zabezpieczeń sieci.

Grupa NSG zawiera kilka wbudowanych zasad, które należy zwrócić uwagę. Są to:

-   **Zezwolić na cały ruch w sieci wirtualnej określonej:** wszystkich maszyn wirtualnych w tej samej sieci wirtualnej platformy Azure mogą komunikować się ze sobą.

-   **Zezwalaj na równoważenia przychodząca obciążenia Azure:** ta reguła zezwala na ruch z dowolnego adresu źródłowego do dowolny docelowy adres usługi równoważenia obciążenia Azure.

-   **Odmów wszystkich przychodzących:** ta reguła blokuje cały ruch sourcing z Internetu, które zostały jawnie dozwolone.

-   **Zezwolić na cały ruch wychodzący do Internetu:** ta zasada umożliwia maszyn wirtualnych do nawiązania połączenia z Internetem. Jeśli nie chcesz, aby te połączenia zainicjowane, należy utworzyć regułę, aby zablokować te połączenia lub wymuszanie tunelowania wymuszonego.

#### <a name="system-routes-and-user-defined-routes"></a>Trasy systemowe i trasy zdefiniowane przez użytkownika

Po dodaniu maszynach wirtualnych (VM) do sieci wirtualnej (VNet) na platformie Azure można zauważyć, że maszyny wirtualne mogą komunikować się ze sobą za pośrednictwem sieci, automatycznie. Nie ma potrzeby określania bramy, nawet gdy maszyny wirtualne znajdują się w różnych podsieciach.

Dotyczy to także komunikacji z maszyn wirtualnych do publicznej sieci Internet, a nawet do sieci lokalnej, gdy obecne jest połączenie hybrydowe z platformy Azure do własnego centrum danych.

![Trasy systemowe](media/azure-network-security/azure-network-security-fig-9.png)

Taki przepływ komunikacji jest możliwy, ponieważ platforma Azure korzysta z szeregu tras systemowych do definiowania przepływu ruchu w sieci IP. Trasy systemowe sterują przepływem komunikacji według następujących scenariuszy:

-   Z tej samej podsieci.

-   Z jednej podsieci do drugiej w ramach sieci wirtualnej.

-   Z maszyny wirtualnej do sieci Internet.

-   Z sieci wirtualnej do innej sieci wirtualnej za pośrednictwem bramy sieci VPN.

-   Z sieci wirtualnej do innej sieci wirtualnej za pośrednictwem sieci wirtualnej komunikacji równorzędnej ([usługi łańcucha](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Z sieci wirtualnej do sieci lokalnej za pośrednictwem bramy sieci VPN.

W wielu przedsiębiorstwach istnieją ograniczeniami zabezpieczeń i wymagania dotyczące zgodności, które wymagają inspekcji lokalnymi wszystkich pakietów sieciowych w celu wymuszenia określonych zasad. Platforma Azure udostępnia mechanizm o nazwie [wymuszone tunelowanie](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) który przekierowuje ruch z maszyn wirtualnych do lokalnego, tworząc niestandardowe trasy lub [protokołu BGP (Border Gateway)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) anonsów za pośrednictwem usługi ExpressRoute lub sieci VPN.

Wymuszanie tunelowania na platformie Azure jest skonfigurowana za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika (przez). Ruch jest przekierowywany do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure.

W poniższej sekcji przedstawiono bieżące ograniczenia tabeli routingu i tras dla sieci wirtualnej platformy Azure:

-   Każda podsieć sieci wirtualnej ma wbudowane, tabelę routingu systemu. W tabeli routingu system ma następujące trzy grupy tras:

 -  **Tras lokalnych sieci wirtualnej:** bezpośrednio do miejsca docelowego maszyny wirtualne w tej samej sieci wirtualnej

 - **O lokalnych trasach:** bramy do sieci VPN platformy Azure

 -  **Trasa domyślna:** bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nie jest objęty przez poprzednie dwie trasy są usuwane.

-   Wraz z wydaniem trasy zdefiniowane przez użytkownika można utworzyć tabeli routingu, aby dodać trasy domyślnej i późniejszym skojarzeniu tabeli routingu do podsieci sieci wirtualnej można włączyć tunelowanie wymuszone na tych podsieci.

-   Należy określić "domyślnej witryny" między lokacjami lokalnego między lokalizacjami podłączony do sieci wirtualnej.

-   Wymuszanie tunelowania musi być skojarzony z sieć wirtualną, która ma dynamiczne routingu bramy sieci VPN (nie bramy statyczne).

- ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą ten mechanizm, ale zamiast tego jest włączana przez anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute.

> [!Note]
> Aby uzyskać więcej informacji, zobacz [dokumentacja usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Aby uzyskać więcej informacji.

#### <a name="network-security-appliances"></a>Urządzenia zabezpieczeń sieci
Gdy grup zabezpieczeń sieci i trasy zdefiniowane przez użytkownika zapewniają miary zabezpieczeń sieciowych w sieci i transportu warstw [OSI model](https://en.wikipedia.org/wiki/OSI_model), będą się zdarzyć, gdy mają lub konieczne włączenie zabezpieczeń na wyższych poziomach stosu sieciowego. W takich sytuacjach zalecamy wdrożenie zapewniana przez partnerów Azure urządzenia zabezpieczeń sieci wirtualnej.

![Urządzenia zabezpieczeń sieci](./media/azure-network-security/azure-network-security-fig-10.png)

Sieć platformy Azure, urządzenia zabezpieczeń poprawić bezpieczeństwo sieci wirtualnej i funkcji sieciowych i są one dostępne z wielu dostawców za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Aby zapewnić można wdrożyć te urządzenia wirtualnego zabezpieczeń:

-   Zapory wysokiej dostępności

-   Zapobieganie nieautoryzowanego dostępu

-   Wykrywania nieautoryzowanego dostępu

-   Zapory aplikacji sieci Web (WAFs)

-   Optymalizacja sieci WAN

-   Routing

-   Równoważenie obciążenia

-   Sieć VPN

-   Zarządzanie certyfikatami

-   Usługa Active Directory

-   Uwierzytelnianie wieloskładnikowe

#### <a name="application-gateway"></a>Brama aplikacji

[Brama aplikacji w Microsoft Azure](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) jest dedykowany urządzenie wirtualne, która udostępnia kontroler dostarczania aplikacji (ADC) jako usługa.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Brama aplikacji pozwala zoptymalizować wydajność kolektywu serwerów sieci web i dostępności dzięki przeniesieniu Procesora znacznym kończenia żądań SSL na bramie aplikacji (odciążanie protokołu SSL). Dostępne są także inne warstwy 7 routingu możliwości, takie jak:

-   Rozdzielanie ruchu przychodzącego

-   Koligacji na podstawie plików cookie sesji

-   Routing na podstawie ścieżki adresu URL

-   Może obsługiwać wiele witryn sieci Web za jednym bramy aplikacji


A [zapory aplikacji sieci web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) jest również udostępniany w ramach bramy aplikacji. To zapewnia ochronę do aplikacji sieci web z typowych luk w zabezpieczeniach sieci web i luk w zabezpieczeniach. Brama aplikacji można skonfigurować jako internetowy bramy, wewnętrzny tylko bramy lub obie te grupy.

Zapory aplikacji sieci Web dla bramy aplikacji może działać w trybie wykrywania i zapobiegania. Typowe przypadek użycia jest Administratorzy mogą działać w trybie wykrywania, aby przyjrzeć się szkodliwe wzorce ruchu. Po wykryciu potencjalnych luk włączenie trybu zapobiegania blokuje podejrzane ruchu przychodzącego.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Ponadto bramy aplikacji zapory aplikacji sieci Web ułatwia monitorowanie aplikacji sieci web przed atakami przy użyciu dziennika zapory aplikacji sieci Web w czasie rzeczywistym, które jest zintegrowany z [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) i [Centrum zabezpieczeń Azure](https://azure.microsoft.com/services/security-center/) śledzenia alerty zapory aplikacji sieci Web i ułatwia monitorowanie trendów.

Dziennik sformatowany JSON trafia bezpośrednio do konta magazynu klienta. Mają pełną kontrolę nad te dzienniki i można zastosować zasad przechowywania.

Te dzienniki mogą również pozyskiwania do własnych analytics systemu za pomocą [integracji dziennika Azure](https://aka.ms/AzLog). Dzienniki zapory aplikacji sieci Web są również zintegrowane z [analizy dzienników](../log-analytics/log-analytics-overview.md) , można wykonywać zaawansowane zapytania szczegółowe analizy dzienników.

#### <a name="azure-web-application-firewall-waf"></a>Zapora aplikacji sieci web platformy Azure (WAF)

Aplikacje sieci Web są coraz bardziej cele złośliwe ataki wykorzystujące wspólnej znanych luk w zabezpieczeniach, takie jak iniekcji kodu SQL, atakami skryptów krzyżowych i inne ataki, które są widoczne w [10 pierwszych OWASP](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Zapobieganie takie luki w zabezpieczeniach w aplikacji wymaga rygorystyczne konserwacji, poprawki i monitorowanie w wielu warstw Topologia aplikacji.

 ![Zapora aplikacji sieci Web platformy Azure (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Scentralizowanego [zapory aplikacji sieci web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) można chronić przed atakami opartymi na sieci web i upraszcza zarządzanie zabezpieczeniami bez konieczności wprowadzania zmian w aplikacji.

Zapora aplikacji sieci Web może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach sieci Web. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji sieci Web.

#### <a name="network-availability-controls"></a>Formanty dostępność sieci

Istnieją różne opcje umożliwiające dystrybucję ruchu sieciowego za pomocą platformy Microsoft Azure. Działanie tych opcji różni się między sobą, a same opcje wykorzystują różny zbiór funkcji i obsługują różne scenariusze. Można korzystać z nich osobno lub używać ich łącznie.

Formanty dostępności sieci są następujące:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Moduł równoważenia obciążenia Azure**

Zapewnia wysoką wydajność dostępności i sieci dla poszczególnych aplikacji. Jest równoważenia obciążenia warstwy 4 (TCP, UDP), który rozdziela przychodzący ruch między dobrej kondycji wystąpień usługi zdefiniowane w zestawie o zrównoważonym obciążeniu.

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Moduł równoważenia obciążenia Azure można skonfigurować w celu:

-   Równoważenia obciążenia przychodzącego Internet ruchu do maszyn wirtualnych. Ta konfiguracja jest określana jako [równoważenia obciążenia internetowy](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Ruch Równoważenie obciążenia między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługi w chmurze lub między komputerami lokalnymi i maszyn wirtualnych w sieci wirtualnej między lokalizacjami. Ta konfiguracja jest określana jako [równoważenia obciążenia wewnętrznego](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Przesyłanie zewnętrznego ruchu sieciowego do określonej maszyny wirtualnej.

Wszystkie zasoby w chmurze muszą publiczny adres IP, który ma być dostępny z Internetu. Infrastruktury chmury na platformie Azure używa bez obsługi routingu adresów IP do jej zasobów. Azure używa translatora adresów sieciowych (NAT) z publicznych adresów IP do komunikacji z Internetem.

 **Bramy aplikacji**

 Brama aplikacji w działa w warstwie aplikacji (warstwy 7 w stosie odwołanie sieci OSI). Działa jako usługa zwrotnego serwera proxy, kończy połączenie klienta i przekazuje żądania do punktów końcowych zaplecza.

 **Menedżer ruchu**

Microsoft Azure Traffic Manager pozwala kontrolować dystrybucję ruchu użytkowników dla punktów końcowych usług w różnych centrach danych. Punkty końcowe usługi obsługiwane przez usługę Traffic Manager obejmują maszynach wirtualnych platformy Azure, aplikacji sieci Web i usług w chmurze. Usługi Traffic Manager można również używać z zewnętrznymi punktami końcowymi poza platformą Azure.

Menedżer ruchu używa systemu nazw domen (DNS) do klienta żądania kierowane do najbardziej odpowiedniego punktu końcowego na podstawie [metody routingu ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) i kondycję punktów końcowych. Menedżer ruchu udostępnia szereg metod routingu ruchu do potrzeb inną aplikację, kondycji punktu końcowego [monitorowania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)i automatycznej pracy awaryjnej. Menedżer ruchu jest odporność na uszkodzenia, łącznie z awarią całej region platformy Azure.

Usługi Azure Traffic Manager pozwala kontrolować dystrybucję ruchu między punktami końcowymi aplikacji. Punkt końcowy jest internetowy usługi hostowanej wewnątrz lub na zewnątrz Azure.

Menedżer ruchu zapewnia dwie kluczowe korzyści:

-   Rozkład ruchu zgodnie z jedną z kilku [metody routingu ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Ciągłego monitorowania kondycji punktu końcowego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) i automatycznej pracy awaryjnej, gdy punkty końcowe nie powiedzie się.

Gdy klient próbuje połączyć się z usługą, jego rozpoznać nazwę DNS usługi na adres IP. Klient następnie łączy się z tego adresu IP do uzyskania dostępu do usługi. Menedżer ruchu używa DNS do kierować klientów do punktów końcowych określonej usługi na podstawie reguł metody routingu ruchu. Klienci łączą się z wybranego punktu końcowego bezpośrednio. Menedżer ruchu nie jest serwer proxy lub bramy. Menedżer ruchu nie widzi ruch przekazywanie między klientem a usługą.

### <a name="azure-network-validation"></a>Sprawdzanie poprawności sieci platformy Azure

Sprawdzanie poprawności sieci platformy Azure jest aby upewnić się, że sieć platformy Azure działa zgodnie z jest skonfigurowany i sprawdzania poprawności można wykonać przy użyciu usługi i funkcje dostępne dla monitorowania sieci. Monitor sieci Azure, umożliwia dostęp do nadmiar rejestrowania i diagnostyki możliwości, które dają insights, aby zrozumieć wydajność sieci, a kondycji. Te możliwości są udostępniane za pośrednictwem portalu, powłoki Power Shell, interfejsu wiersza polecenia, interfejs API Rest i zestawu SDK.

Azure bezpieczeństwa operacyjnego odwołuje się do usług, formanty i funkcje dostępne dla użytkowników do ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. Zabezpieczeń platformy Azure Operational w oparciu o strukturę, która zawiera wiedzę za pośrednictwem różnych funkcji, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development Lifecycle (SDL), program Microsoft Security odpowiedzi Centrum i głębokie świadomości bezpieczeństwa przez zagrożeń.

-   [Azure Operations Management Suite](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-overview)

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure obserwatora sieciowego](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Menedżer zasobów systemu Azure

Osoby i procesy, które działają na Microsoft Azure są prawdopodobnie najważniejszych funkcji zabezpieczeń platformy. W tej sekcji opisano funkcji firmy Microsoft globalnej infrastruktury centrum danych, które pomagają zwiększyć i zachować bezpieczeństwo, ciągłość działania i ochrony prywatności.

Infrastruktura aplikacji zwykle obejmuje wiele składników — może być maszynę wirtualną, konta magazynu i sieci wirtualnej lub aplikacji sieci web, bazy danych, serwer bazy danych i usług innych firm. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. Usługa Azure Resource Manager umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy.

Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu.

**Korzyści wynikające ze stosowania usługi Resource Manager**

Usługa Resource Manager zapewnia kilka korzyści:

-   Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

-   Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

-   Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

-   Możliwość definiowania zależności między zasobami, aby wdrażać je w odpowiedniej kolejności.

-   Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.

-   Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

-   Można również uprościć rozliczenia w organizacji, wyświetlając kosztów dla grupy zasobów, udostępnianie tagu.

> [!Note]
> Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli znasz wcześniejszy model wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz artykuł [Understanding Resource Manager deployment and classic deployment](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) (Opis wdrażania za pomocą usługi Resource Manager oraz wdrażania klasycznego).

## <a name="azure-network-logging-and-monitoring"></a>Monitorowanie i rejestrowanie sieć platformy Azure

System Azure oferuje wiele narzędzi do monitorowania, zapobiegania, wykrywania i reagowania na zdarzenia zabezpieczeń sieci. Oto niektóre z najbardziej zaawansowanych narzędzi dostępny w tym zakresie:

-   Network Watcher

-   Poziom monitorowania zasobów sieciowych

-   Log Analytics

### <a name="network-watcher"></a>Obserwator sieci

[Monitor sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) — na podstawie scenariusza monitorowania jest dostarczana z funkcji obserwatora sieciowego. Ta usługa obejmuje przechwytywania pakietów, następnego przeskoku, przepływ IP Sprawdź widok grupy zabezpieczeń, dzienniki przepływu NSG. Scenariusz poziomu monitorowania udostępnia widok pełnego zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Monitor sieci jest regionalnych usługa, która umożliwia monitorowanie i diagnozowanie warunki na poziomie sieci scenariusz w, do i z platformy Azure. Diagnostyka sieci i narzędzi wizualizacji dostępnych z obserwatora sieciowego pomagają zrozumieć, diagnozowanie i Uzyskaj wgląd do sieci na platformie Azure.

Obserwatora sieciowego ma obecnie następujące możliwości:

#### <a name="topology"></a>Topologia

[Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) zwraca wykres zasobów sieciowych w sieci wirtualnej. Wykres przedstawia połączenia między zasobami do reprezentowania łączność sieciową pełnego. W portalu topologii zwraca obiekty zasobów zgodnie z harmonogramem podstawę sieci wirtualnej. Relacje są określone przez linie między zasobami poza region obserwatora sieciowego, nawet jeśli w zasobie grupy nie zostaną wyświetlone. Zasoby zwracane w widoku portalu są podzbiorem składników sieciowych, które są wyświetlone na wykresie. Aby wyświetlić pełną listę zasobów sieciowych, można użyć [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) lub [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Ponieważ zasoby są zwracane połączenie między one są modelowane w obszarze dwie relacje.

- **Zawieranie** -sieć wirtualna zawiera podsieci, która zawiera jedną kartą sieciową.

- **Skojarzone** -A kart interfejsu Sieciowego jest skojarzona z maszyną Wirtualną.

#### <a name="variable-packet-capture"></a>Przechwytywania pakietów zmiennej

Monitor sieci [przechwytywania pakietów zmiennej](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) służy do tworzenia sesji przechwytywania pakietów, aby śledzić ruch do i z maszyny wirtualnej. Pomaga przechwytywania pakietów do diagnozowania sieci anomalii zarówno rozbudowy i proactivity. Innych celów obejmują zbieranie statystyk sieciowych, uzyskanie informacji na temat wtargnięcia sieci, aby debugować komunikacja klient serwer i o wiele więcej.

Przechwytywania pakietów jest uruchamiana zdalnie za pomocą Monitora sieci rozszerzenia maszyny wirtualnej. Funkcja ta ułatwia obciążeń uruchomionych przechwytywania pakietów ręcznego na odpowiednią maszynę wirtualną, która zapisuje cenny czas. Przechwytywania pakietów mogą być wyzwalane za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Przykład sposobu przechwytywania pakietów mogą być wyzwalane jest z alertami maszyny wirtualnej.

#### <a name="ip-flow-verify"></a>Sprawdź przepływ IP

[Sprawdź przepływów IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) sprawdza, czy pakiet jest dozwolony lub odmowa dostępu do lub z maszyny wirtualnej na podstawie informacji 5-elementowej. Te informacje składa się z kierunku, protokół lokalny adres IP, zdalny adres IP, portu lokalnego i portu zdalnego. Jeśli pakiet jest zabroniony przez grupę zabezpieczeń, zwracana jest nazwa reguły, z którego pakiet. Podczas gdy można wybrać dowolny źródłowy lub docelowy adres IP, funkcja ta ułatwia ona administratorom szybkie diagnozowanie problemów z łącznością z lub do Internetu i z lub do środowiska lokalnego.

Sprawdź przepływów IP jest przeznaczony dla interfejsu sieciowego maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie ustawień skonfigurowanych do lub z tym interfejs sieciowy. Ta funkcja jest przydatna w potwierdzaniu blokuje ruch przychodzący lub wychodzący z maszyny wirtualnej lub jeśli reguły do grupy zabezpieczeń sieci.

#### <a name="next-hop"></a>Następny przeskok

Określa [następnego przeskoku](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) pakiety przesyłane w sieci szkieletowej Azure, dzięki któremu można zdiagnozować wszelkie błędnie skonfigurowane trasy zdefiniowane przez użytkownika. Ruch z maszyny Wirtualnej są wysyłane do lokalizacji docelowej, na podstawie tras skuteczne skojarzony z jedną kartą sieciową. Następny przeskok pobiera typ następnego przeskoku i adres IP pakietu z określonej maszyny wirtualnej i karty sieciowej. Dzięki temu do określenia, czy pakiet jest być kierowany do lokalizacji docelowej i jest holed ruchu jest czarny.

Następny przeskok zwraca również wartość tabeli tras skojarzone z następnego przeskoku. Podczas wykonywania zapytania następnego przeskoku, jeśli trasa jest zdefiniowana jako trasy zdefiniowane przez użytkownika, zostanie zwrócony tej trasy. W przeciwnym razie wartość następnego skoku zwraca "Trasa systemowa".

#### <a name="security-group-view"></a>Widok grupy zabezpieczeń

Pobiera reguły skuteczne i zastosowane zabezpieczeń, które są stosowane na maszynie Wirtualnej. Na poziomie podsieci lub na poziomie karty Sieciowej skojarzonych grup zabezpieczeń sieci. Gdy skojarzony na poziomie podsieci, dotyczy wszystkich wystąpień maszyn wirtualnych w podsieci. Sieci [widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) zwraca wszystkich skonfigurowanych grup NSG i reguł, które są skojarzone na poziomie podsieci i karty na maszynie wirtualnej, zapewniając wgląd w konfiguracji. Ponadto zasady efektywnym elementem systemu zabezpieczeń są zwracane dla każdej z kart sieciowych w maszynie Wirtualnej. Widok przy użyciu grup zabezpieczeń sieci można ocenić maszyny Wirtualnej dla luk w zabezpieczeniach sieci takich jak otwartych portów. Można również sprawdzić czy grupy zabezpieczeń sieci działa zgodnie z oczekiwaniami, na podstawie [porównanie skonfigurowanego i reguły zabezpieczeń skuteczne](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Rejestrowanie przepływu NSG

 Dzienniki przepływu dla grup zabezpieczeń sieci umożliwiają przechwytywanie dzienniki związane z ruchu, który ma być dozwolony lub odrzucany przez zasady zabezpieczeń w grupie. Przepływ został zdefiniowany przez informacji 5-elementowej — źródłowy adres IP, docelowy adres IP, Port źródłowy, Port docelowy i protokołu.

[Grupy zabezpieczeń sieci przepływu dzienniki](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) są funkcją obserwatora sieciowego, który służy do wyświetlania informacji na temat przychodzące i wychodzące ruchu IP za pośrednictwem grupy zabezpieczeń sieci.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Brama sieci wirtualnej i rozwiązywanie problemów z połączenia

Obserwatora sieciowego zawiera wiele funkcji w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jeden z tych funkcji jest zasobem rozwiązywania problemów. [Rozwiązywanie problemów z zasobów](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) może być wywoływany przez programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu obserwatora sieciowego sprawdza kondycję bramy sieci wirtualnej lub połączenie i zwraca jego wyniki.

W tej sekcji przedstawia zadań różnych zarządzania, które są obecnie dostępne dla zasobu rozwiązywania problemów.

-   [Rozwiązywanie problemów z bramy sieci wirtualnej](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Rozwiązywanie problemów z połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limity subskrypcji sieci

[Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) udostępniają szczegółowe informacje dotyczące użycia każdego z zasobów sieciowych w ramach subskrypcji w regionie przed maksymalną liczbę dostępnych zasobów.

#### <a name="configuring-diagnostics-log"></a>Konfigurowanie dziennika diagnostyki

Zawiera obserwatora sieciowego [dzienników diagnostycznych](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) widoku. Ten widok zawiera wszystkich zasobów sieciowych, które obsługują rejestrowania diagnostycznego. Z tego widoku można włączyć i wyłączyć zasobów sieciowych, łatwo i szybko.

### <a name="network-resource-level-monitoring"></a>Poziom monitorowania zasobów sieciowych

Następujące funkcje są dostępne dla poziomu monitorowania zasobów:

#### <a name="audit-log"></a>Dziennik inspekcji

Operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Te dzienniki inspekcji są niezbędne do ustanowienia różnych compliances. Dzienniki te mogą być wyświetlane w portalu Azure lub pobrany przy użyciu narzędzi firmy Microsoft, takich jak usługi Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API Rest.

> [!Note]
> Aby uzyskać więcej informacji na dziennikach inspekcji, zobacz [inspekcji operacji za pomocą Menedżera zasobów](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Dzienniki inspekcji są dostępne dla operacje wykonywane na wszystkich zasobów sieciowych.


#### <a name="metrics"></a>Metryki

Metryki są miar wydajności i liczniki zebrane w okresie. Metryki są obecnie dostępne dla bramy aplikacji. Metryki może służyć do wyzwolenia alerty oparte na wartości progowej. Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w jej puli zaplecza i automatycznie usuwa dowolnego zasobu z puli określana jako zła. Brama aplikacji w kontynuuje monitorowanie wystąpień złej kondycji i dodaje je do dobrej kondycji puli zaplecza, gdy staną się dostępne i odpowiadać na zdarzenie sondy kondycji. Brama aplikacji w wysyła sondy kondycji z tego samego portu, który jest zdefiniowany w ustawieniach protokołu HTTP zaplecza. Taka konfiguracja powoduje, że sondy testuje tego samego portu, którego klienci będą stosowane do nawiązania połączenia z zapleczem.

> [!Note]
> Zobacz [diagnostyki bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) do wyświetlania, jak metryki może służyć do tworzenia alertów.

#### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Okresowe i spontanicznych zdarzenia są tworzone przez zasobów sieciowych i zarejestrowane na kontach magazynu, wysyłane do Centrum zdarzeń lub analizy dzienników. Te dzienniki wgląd w kondycję zasobu. Te dzienniki można wyświetlać w narzędzi, takich jak Power BI i analizy dzienników. Aby dowiedzieć się wyświetlić dzienniki diagnostyczne, odwiedź stronę [analizy dzienników](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Dzienniki diagnostyczne są dostępne dla [modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [grup zabezpieczeń sieci](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), trasy i [brama aplikacji w](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Wyświetl dzienniki diagnostyczne zawiera obserwatora sieciowego. Ten widok zawiera wszystkich zasobów sieciowych, które obsługują rejestrowania diagnostycznego. Z tego widoku można włączyć i wyłączyć zasobów sieciowych, łatwo i szybko.

### <a name="log-analytics"></a>Log Analytics

[Zaloguj się Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) jest usługą platformy Azure, który monitoruje chmurze i lokalnych środowiskach utrzymywać ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł.

Analiza dzienników oferuje następujące rozwiązania do monitorowania sieci:

-   Monitor wydajności sieci (NPM)

-   Azure analytics bramy aplikacji

-   Grupy zabezpieczeń sieci Azure analityka

#### <a name="network-performance-monitor-npm"></a>Monitor wydajności sieci (NPM)
[Monitora wydajności sieci](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) rozwiązanie do zarządzania jest rozwiązania, który monitoruje kondycję, dostępności i uzyskiwanie sieci do monitorowania sieci.

Służy do monitorowania łączność między:

-   Chmura publiczna i lokalnych

-   centra danych i lokalizacje użytkownika (biurach oddziałów)

-   podsieci hosting różnych warstw aplikacji wielowarstwowych.


#### <a name="azure-application-gateway-analytics-in-log-analytics"></a>Analiza bramy aplikacji Azure w analizy dzienników

Dla bramy aplikacji obsługiwane są następujące dzienniki:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Obsługiwane są następujące metryki dla bramy aplikacji:

-   Przepływność 5 minut

#### <a name="azure-network-security-group-analytics-in-log-analytics"></a>Analiza grupy zabezpieczeń sieci platformy Azure w analizy dzienników

Następujące dzienniki są obsługiwane w przypadku [sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** zawiera wpisy, dla których NSG reguły są stosowane do maszyn wirtualnych i wystąpień ról na podstawie adresu MAC. Stan zasady te są gromadzone co 60 sekund.

- **NetworkSecurityGroupRuleCounter:** reguł zawiera wpisy dla ile razy każda grupa NSG jest stosowana do odmowy lub zezwolić na ruch.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o zabezpieczeń, odczytując niektóre nasze tematy szczegółowe zabezpieczeń:

-   [Analizy dzienników dla grup zabezpieczeń sieci (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Innowacje sieci, którzy sterują przerwy w chmurze](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Sieci Przełącz oprogramowania tym uprawnień globalnych firmy Microsoft w chmurze](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Jak Microsoft kompilacje jego szybkie i niezawodne sieci globalnej](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Oświetlenie zapasowej innowacji sieci](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
