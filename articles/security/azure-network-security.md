---
title: Zabezpieczenia sieci platformy Azure | Dokumentacja firmy Microsoft
description: Więcej informacji na temat obliczeń usług w chmurze, które zawierają szeroką gamę wystąpień obliczeniowych i usług, które można skalować w górę i w dół automatycznie do potrzeb aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: f684a9d7bca77a8aa3aa60f5079dda0ce3b58a1c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60587467"
---
# <a name="azure-network-security"></a>Zabezpieczenia sieci na platformie Azure

Wiemy, że bezpieczeństwo to zadanie, jeden w chmurze i jak ważne jest, że możesz znaleźć dokładne i aktualne informacje o zabezpieczeniach platformy Azure. Jedną z najważniejszych powodów, dla aplikacji i usług za pomocą platformy Azure jest korzystanie z zalet platformy Azure szeroką gamę narzędzi zabezpieczeń i możliwości. Te narzędzia i możliwości pomocy, umożliwiają tworzenie bezpiecznych rozwiązań na platformie Azure.

Microsoft Azure oferuje poufność, integralność i dostępność danych klientów, pozwalając również przezroczyste odpowiedzialności. Aby umożliwić użytkownikom lepsze rozumienie zbiór zaimplementowane w systemie Microsoft Azure z perspektywy klienta kontroli zabezpieczeń sieci, w tym artykule "Zabezpieczenia sieci platformy Azure", są zapisywane w zapewnienie kompleksowych informacji o sieci kontroli zabezpieczeń dostępne z platformą Microsoft Azure.

Ten dokument jest przeznaczony do informujące o szerokiej gamy kontrolek sieci, które można skonfigurować tak, aby zwiększyć bezpieczeństwo rozwiązania, które można wdrożyć na platformie Azure. Jeśli interesuje Cię do zabezpieczania sieci szkieletowej platformy Azure, sama działania firmy Microsoft, zobacz sekcję zabezpieczeń platformy Azure w [Microsoft Trust Center](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Platforma Azure

Platforma Azure to platforma usługi w chmurze publicznej obsługuje szeroki zakres systemów operacyjnych, języków programowania, struktur, narzędzia, baz danych i urządzeń.  Jej uruchamianie kontenerów systemu Linux z integracją Docker; Tworzenie aplikacji w językach JavaScript, Python, .NET, PHP, Java i Node.js; Tworzenie zapleczy dla systemów iOS, Android i Windows urządzenia. Usług Azure cloud services obsługują te same technologie przez miliony deweloperów i specjalistów IT, już używane i wypróbowane.

Podczas tworzenia na lub migracji zasobów informatycznych do dostawcy usług w chmurze publicznej, polegasz na możliwości w organizacji, aby chronić aplikacje i dane za pomocą usług i kontrolek, zapewniają Zarządzanie zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana kompleksowo, począwszy od aplikacje hostujące jednocześnie miliony klientów, a jego zapewnia wiarygodną podstawę firm mogą spełnienie rządowych wymagań dotyczących zabezpieczeń. Ponadto platforma Azure udostępnia obszernej kolekcji konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, dzięki czemu możesz dostosować zabezpieczenia, aby spełniały unikatowe wymagania wdrożenia w organizacji.

## <a name="abstract"></a>Abstrakcyjny

Usługi w chmurze publicznej firmy Microsoft dostarczać usługi w hiperskali i infrastruktury, funkcji przeznaczonych dla przedsiębiorstw i wiele możliwości na potrzeby łączności hybrydowej. Można uzyskać do nich dostępu za pośrednictwem Internetu lub z usługą Azure ExpressRoute, która zapewnia łączność sieci prywatnej. Platforma Microsoft Azure pozwala na bezproblemowe rozszerzenie infrastruktury w chmurze i tworzenie architektury wielowarstwowej. Ponadto innych firm można włączyć rozszerzone możliwości, oferując bezpieczeństwa usług i urządzeń wirtualnych.

Usługi platformy Azure, sieci Maksymalizuj elastyczność, dostępności, odporności, bezpieczeństwa i integralności zgodnie z projektem. Ten dokument zawiera szczegółowe informacje dotyczące funkcji sieciowych platformy Azure i informacji na temat jak klienci mogą używać funkcji natywnych zabezpieczeń platformy Azure w celu ochrony swoimi zasobami informacyjnymi.

Zamierzonych odbiorców dla tym oficjalnym dokumencie obejmują:

- Menedżerów technicznego, Administratorzy sieci i deweloperów poszukujących rozwiązań zabezpieczeń dostępnych i obsługiwanych na platformie Azure.

-   MSP lub firm procesu dyrektorzy, którzy chcesz aby uzyskać ogólne omówienie technologii sieci platformy Azure i usług, które mają zastosowanie w dyskusjach dotyczących zabezpieczeń sieci w chmurze publicznej Azure.

## <a name="azure-networking-big-picture"></a>Azure networking w szerszej perspektywie
Microsoft Azure obejmuje niezawodną infrastrukturę sieci do obsługi aplikacji i wymagań dotyczących łączności usługi. Łączność sieciowa jest możliwe między zasobami znajdującymi się na platformie Azure, między lokalnych i zasobów, hostowane w systemie Azure oraz do i z Internetu i platformy Azure.

![Azure Networking w szerszej perspektywie](media/azure-network-security/azure-network-security-fig-1.png)

[Infrastruktury sieci platformy Azure](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-networking-guidelines) umożliwia bezpieczne łączenie zasobów platformy Azure ze sobą z sieciami wirtualnymi (Vnet). Sieć wirtualna jest reprezentacją Twojej własnej sieci w chmurze. Sieć wirtualna jest to logiczna izolacja sieci chmury platformy Azure dedykowanej dla Twojej subskrypcji. Do sieci lokalnej można połączyć sieci wirtualne.

Obsługiwane przez platformę Azure w wersji dedykowanej łącza sieci WAN łączność z siecią lokalną i siecią wirtualną platformy Azure przy użyciu [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction). Łącze między platformą Azure i witryna korzysta z dedykowanego połączenia, które nie przechodzi przez publiczny Internet. Jeśli aplikacja Azure działa w wielu centrach danych, możesz użyć [usługi Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) na kierowaniu żądań od użytkowników inteligentnie między wystąpieniami aplikacji. Może również kierować ruch do usługi nie są uruchomione na platformie Azure, jeśli są one dostępne z Internetu.

## <a name="enterprise-view-of-azure-networking-components"></a>Wyświetl Enterprise składników sieciowych platformy Azure
Platforma Azure oferuje wiele składników sieciowych, które mają zastosowanie w dyskusjach dotyczących zabezpieczeń sieci. Firma Microsoft opisano te składniki sieci i skoncentrować się na problemy z zabezpieczeniami z nimi związane.

> [!Note]
> Nie wszystkie aspekty sieci platformy Azure są opisane — omówimy tylko te, które uznawany za pivotal w planowaniu i projektowaniu bezpiecznej infrastruktury sieciowej wokół usługi i aplikacje, które wdrażasz na platformie Azure.

W tym dokumencie będzie cover następujące enterprise możliwości sieciowe platformy Azure:

-   Podstawowe połączenie sieciowe

-   Łączność hybrydowa

-   Opcje zabezpieczeń

-   Weryfikacja sieci

### <a name="basic-network-connectivity"></a>Podstawowe połączenie sieciowe

[Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) service pozwala bezpiecznie połączyć ze sobą zasobów platformy Azure z sieciami wirtualnymi (VNet). Sieć wirtualna jest reprezentacją Twojej własnej sieci w chmurze. Sieć wirtualna jest izolacja logiczna infrastruktury sieci platformy Azure w wersji dedykowanej do Twojej subskrypcji. Można też połączyć sieci wirtualne oraz przy użyciu sieci VPN typu lokacja lokacja sieci lokalnej i w wersji dedykowanej [łącza sieci WAN](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

![Podstawowe połączenie sieciowe](media/azure-network-security/azure-network-security-fig-2.png)

Przy założeniu, używanie maszyn wirtualnych do serwerów hostów na platformie Azure, pytaniem jest, jak połączyć tych maszyn wirtualnych z siecią. Odpowiedzią jest, że maszyny wirtualne nawiązywać połączenie [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

Sieci wirtualne platformy Azure są podobne wirtualnej sieci lokalnej za pomocą własnych rozwiązań platformy wirtualizacji, takie jak Microsoft Hyper-V lub VMware.

#### <a name="intra-vnet-connectivity"></a>Połączenie między sieciami wewnątrz

Łączenie sieci wirtualnych między sobą Włączanie zasobów podłączonej do jednej sieci wirtualnej do komunikowania się ze sobą w ramach sieci wirtualnych. Łączenie sieci wirtualnych między sobą, można użyć jednego lub obu z następujących opcji:

- **Komunikacja równorzędna:** Dzięki zasoby podłączone do różnych sieci wirtualnych platformy Azure w ramach tej samej lokalizacji platformy Azure do komunikowania się ze sobą. Przepustowość i opóźnienie między siecią wirtualną jest taki sam, jakby zasoby były podłączone do tej samej sieci wirtualnej. Aby dowiedzieć się więcej na temat komunikacji równorzędnej, przeczytaj [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

  ![Komunikacja równorzędna](media/azure-network-security/azure-network-security-fig-3.png)

- **Połączenie sieć wirtualna-sieć wirtualna:** Dzięki zasoby podłączony do innej sieci wirtualnej platformy Azure w ramach tej samej lub innej lokalizacji platformy Azure. W przeciwieństwie do komunikacji równorzędnej, przepustowość jest ograniczona między sieciami wirtualnymi, ponieważ ruch musi przepływać za pośrednictwem bramy sieci VPN Azure.

![Połączenia sieć wirtualna-sieć wirtualna](media/azure-network-security/azure-network-security-fig-4.png)


Aby dowiedzieć się więcej na temat łączenia sieci wirtualnych za pomocą połączenia sieć wirtualna-sieć wirtualna, przeczytaj [skonfigurować artykułu połączenia sieć wirtualna-sieć wirtualna](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json).

#### <a name="azure-virtual-network-capabilities"></a>Możliwości w sieci wirtualnej platformy Azure:

Jak widać, usługi Azure Virtual Network zapewnia maszyn wirtualnych w celu połączenia z siecią, tak aby umożliwić im połączenie z innymi zasobami sieciowymi w bezpieczny sposób. Jednak podstawowej łączności to dopiero początek. Następujące funkcje usługi Azure Virtual Network ujawnić właściwości zabezpieczeń usługi Azure Virtual Network:

-   Izolacja

-   Łączność z Internetem

-   Połączenia zasobów platformy Azure

-   Połączenie między sieciami

-   Łączność lokalna

-   Filtrowanie ruchu

-   Routing

**Izolacja**

Sieci wirtualne znajdują się [izolowane](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) od siebie nawzajem. Możesz utworzyć oddzielne sieci wirtualnych dla rozwoju, testowania i produkcji, które używały tych samych [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) bloki adresów. Z drugiej strony można utworzyć wiele sieci wirtualnych, użyj różnych bloków adresów CIDR i połączyć ze sobą sieci. Sieć wirtualną można podzielić na wiele podsieci.

Platforma Azure udostępnia rozpoznawania nazw wewnętrznych dla maszyn wirtualnych i [usług w chmurze](https://azure.microsoft.com/services/cloud-services/) wystąpień roli połączone z siecią wirtualną. Opcjonalnie można skonfigurować sieci wirtualnej do użycia własnych serwerów DNS, zamiast korzystać z rozpoznawania nazw wewnętrznych platformy Azure.

Można zaimplementować wiele sieci wirtualnych w ramach każdej platformy Azure [subskrypcji](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) i na platformie Azure [region](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json). Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. W każdej sieci wirtualnej możesz wykonywać następujące czynności:

-   Określenie niestandardowej prywatnej przestrzeni adresowej IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Przypisuje Azure zasoby są połączone z sieci wirtualnej prywatny adres IP z przestrzeni adresowej, możesz przypisać.

-   Segment sieci wirtualnej do co najmniej jednej podsieci i przydzielić części przestrzeni adresowej sieci wirtualnej w każdej podsieci.

-   Korzystanie z rozpoznawania nazw platformy Azure lub określenie własnego serwera DNS do użytku przez zasoby połączone z siecią wirtualną. Aby dowiedzieć się więcej na temat rozpoznawania nazw w sieciach wirtualnych, przeczytaj [rozpoznawanie nazw dla maszyn wirtualnych i usług w chmurze](https://docs.microsoft.com/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances).

**Łączność z Internetem**

Wszystkie [Azure Virtual Machines (VM)](https://docs.microsoft.com/azure/virtual-machines/windows/) i wystąpień roli usług Cloud Services, połączone z siecią wirtualną domyślnie mają dostęp do Internetu. Można również włączyć dostępu przychodzącego do określonych zasobów, zgodnie z potrzebami. (VM) i wystąpień roli usług Cloud Services, połączone z siecią wirtualną domyślnie mają dostęp do Internetu. Można również włączyć dostępu przychodzącego do określonych zasobów, zgodnie z potrzebami.

Domyślnie wszystkie zasoby połączone z siecią wirtualną mają łączność wychodząca z Internetem. Prywatny adres IP zasobu jest adres sieci źródłowej translacji (SNAT) na publiczny adres IP w ramach infrastruktury platformy Azure. Łączność domyślne można zmienić poprzez implementację niestandardowy routing i filtrowanie ruchu. Aby uzyskać więcej informacji na temat wychodzące połączenie z Internetem, przeczytaj [informacje o połączeniach wychodzących na platformie Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Do komunikacji z Internetu dla ruchu przychodzącego z zasobami platformy Azure lub do komunikowania się ruch wychodzący do Internetu bez SNAT, zasób musi posiadać publiczny adres IP. Aby dowiedzieć się więcej na temat publicznych adresów IP, przeczytaj [publiczne adresy IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address).

**Połączenia zasobów platformy Azure**

[Zasoby platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) takie jak maszyny wirtualne i usługi w chmurze, które mogą być połączone z tej samej sieci wirtualnej. Zasoby mogą łączyć się ze sobą przy użyciu prywatnych adresów IP, nawet jeśli leżą one w różnych podsieciach. Platforma Azure udostępnia domyślny routing między podsieciami, sieciami wirtualnymi i sieciami lokalnymi, dzięki czemu nie trzeba konfigurować i zarządzać nimi trasy.

Kilka zasobów platformy Azure można połączyć z siecią wirtualną, takie jak maszyny wirtualne (VM), usług w chmurze, środowiskach usługi App Service i zestawy skalowania maszyn wirtualnych. Maszyny wirtualne połączone z podsiecią w sieci wirtualnej przy użyciu interfejsu sieciowego (NIC). Aby dowiedzieć się więcej na temat kart sieciowych, przeczytaj [interfejsy sieciowe](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface).

**Połączenie między sieciami**

[Sieci wirtualne](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) mogą być połączone ze sobą, umożliwiając zasobów podłączonej do dowolnej sieci wirtualnej do komunikowania się z dowolnym zasobem w innej sieci wirtualnej.

Łączenie sieci wirtualnych między sobą Włączanie zasobów podłączonej do jednej sieci wirtualnej do komunikowania się ze sobą w ramach sieci wirtualnych. Łączenie sieci wirtualnych między sobą, można użyć jednego lub obu z następujących opcji:

- **Komunikacja równorzędna:** Dzięki zasoby podłączone do różnych sieci wirtualnych platformy Azure w ramach tej samej lokalizacji platformy Azure do komunikowania się ze sobą. Przepustowość i opóźnienie między sieciami wirtualnymi jest taka sama tak, jakby zasoby były podłączone do tej samej VNet.To więcej informacji na temat komunikacji równorzędnej, przeczytaj [komunikacja równorzędna sieci wirtualnych](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).

- **Połączenie sieć wirtualna-sieć wirtualna:** Dzięki zasoby podłączony do innej sieci wirtualnej platformy Azure w ramach tej samej lub innej lokalizacji platformy Azure. W przeciwieństwie do komunikacji równorzędnej, przepustowość jest ograniczona między sieciami wirtualnymi, ponieważ ruch musi przepływać za pośrednictwem bramy sieci VPN Azure. Aby dowiedzieć się więcej na temat łączenia sieci wirtualnych za pomocą połączenia sieć wirtualna-sieć wirtualna. Aby dowiedzieć się więcej, przeczytaj [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) .

**Łączność lokalna**

Sieci wirtualne mogą być połączone z [lokalnych](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) sieci za pośrednictwem sieci prywatnych połączeń między siecią a Azure lub za pośrednictwem połączenia sieci VPN typu lokacja lokacja za pośrednictwem Internetu.

Można połączyć sieć lokalną z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

- **Wirtualna sieć prywatna typu punkt-lokacja:** Ustanowić między jeden komputer połączony z siecią i siecią wirtualną. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Połączenie korzysta z protokołu SSTP, zapewnienie szyfrowaną komunikację przez Internet między Komputerem a siecią wirtualną. Opóźnienie w sieci VPN punkt lokacja jest nieprzewidywalne, ponieważ ruch przechodzi przez Internet.

- **Sieć VPN typu lokacja-lokacja:** Ustanowić między urządzeniem sieci VPN i bramą sieci VPN platformy Azure. Ten typ połączenia umożliwia dowolnego zasobu w środowisku lokalnym, zezwalają na dostęp do sieci wirtualnej. Połączenie jest VPN protokołu IPsec/IKE, który zapewnia szyfrowaną komunikację przez Internet między urządzeniem lokalnej i bramy sieci VPN platformy Azure. Czas oczekiwania na połączenie lokacja lokacja jest nieprzewidywalne, ponieważ ruch przechodzi przez Internet.

- **Azure ExpressRoute:** połączenie nawiązywane pomiędzy siecią i platformą Azure za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Czas oczekiwania na połączenie usługi ExpressRoute jest przewidywalne, ponieważ ruch nie przechodzi przez Internet. Aby dowiedzieć się więcej na temat wszystkich poprzednich opcji połączenia, przeczytaj [diagramy topologii połączeń](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrowanie ruchu**

Wystąpienia roli maszyn wirtualnych i usług w chmurze [ruch sieciowy](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) można filtrować ruchu przychodzącego i wychodzącego przez źródłowy adres IP i port, docelowy adres IP i portu i protokołu.

Ruch sieciowy pomiędzy podsieciami możesz filtrować przy użyciu jednej lub obu poniższych opcji:

- **Sieciowe grupy zabezpieczeń (NSG):** Poszczególnych sieciowych grupach zabezpieczeń może zawierać wiele reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego, które pozwalają do filtrowania ruchu przez źródłowy i docelowy adres IP, portu i protokołu. Sieciowa grupa zabezpieczeń można stosować do każdej karty Sieciowej na maszynie wirtualnej. Można także zastosować sieciową grupę zabezpieczeń do podsieci karty Sieciowej lub innych zasobów platformy Azure jest połączona. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, przeczytaj [sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).

- **Urządzenia sieci wirtualnej:** Urządzenie sieci wirtualnej jest uruchomione oprogramowanie, która realizuje funkcje sieci, takimi jak Zapora maszyny Wirtualnej. Wyświetl listę dostępnych urządzeń WUS w witrynie Azure Marketplace. Urządzenia WUS są również dostępne, zapewniające Optymalizacja sieci WAN i innych sieci ruchu funkcji. Urządzenia WUS są zwykle używane przy użyciu zdefiniowanych przez użytkownika lub tras BGP. Urządzenie WUS można również użyć do filtrowania ruchu między sieciami wirtualnymi.

**Routing**

Opcjonalnie można zastąpić domyślne platformy Azure routing, dbając o Konfigurowanie własnego tras lub przy użyciu trasy protokołu BGP za pośrednictwem bramy sieci.

Platforma Azure tworzy tabel tras, które umożliwiają zasobów podłączone do żadnej podsieci w dowolnej sieci wirtualnej do komunikowania się ze sobą, domyślnie. Możesz zaimplementować jedną lub obie poniższe opcje, aby zastąpić domyślne trasy tworzone przez platformę Azure:

- **Trasy zdefiniowane przez użytkownika:** możesz utworzyć niestandardowe tabele tras z trasami kontrolującymi przekierowywanie ruchu do każdej podsieci. Aby dowiedzieć się więcej o trasach definiowanych przez użytkownika, przeczytaj [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview).

- **Trasy protokołu BGP:** Jeśli łączysz sieć wirtualną z siecią lokalną przy użyciu usługi Azure VPN Gateway lub połączenia ExpressRoute, możesz propagować trasy protokołu BGP w Twoich sieciach wirtualnych.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybrydowe połączenie z Internetem: Łączenie z siecią lokalną
Można połączyć sieć lokalną z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

-   Łączność z Internetem

-   Sieci VPN typu punkt lokacja (P2S sieci VPN)

-   Sieci VPN typu lokacja lokacja (S2S sieci VPN)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Łączność z Internetem

Jak sugeruje nazwa, łączność z Internetem sprawia, że obciążenia dostępny z Internetu, konfigurując potrzebne różne publicznych punktów końcowych do obciążeń znajdujące się wewnątrz sieci wirtualnej. Te obciążenia mogą zostać ujawnione pewne przy użyciu [modułu równoważenia obciążenia dostępnego z Internetu](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview) lub po prostu przypisanie publicznego adresu IP do maszyny Wirtualnej. W ten sposób stanie się możliwe dla wszystkich elementów w Internecie, aby mieć dostęp do tej maszyny wirtualnej, pod warunkiem Zapora hosta [sieciowe grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg), i [trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview) , aby umożliwić mieć miejsce.

W tym scenariuszu może narazić aplikację, która wymaga, aby widoczne publicznie w Internecie i mieć możliwość nawiązania połączenia z dowolnego miejsca i z określonych lokalizacji w zależności od konfigurację Twoich obciążeń.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Sieci VPN typu punkt lokacja lub sieci VPN typu lokacja lokacja
Te dwa mieści się w tej samej kategorii. Muszą mieć bramę sieci VPN sieci wirtualnej i możesz połączyć do niego przy użyciu klienta sieci VPN dla stacji roboczej w ramach [Point-to-Site configuration](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal) możesz też skonfigurować lokalną [urządzenia sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpn-devices)aby można było zakończyć VPN lokacja lokacja. W ten sposób lokalnych urządzeń można połączyć się z zasobami w sieci wirtualnej.

Konfiguracja typu punkt-lokacja pozwala utworzyć indywidualne bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenie punkt-lokacja (P2S) to połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol).

![Sieci VPN typu punkt lokacja](media/azure-network-security/azure-network-security-fig-5.png)

Połączenia punkt-lokacja są przydatne, jeśli chcesz połączyć się z siecią wirtualną z lokalizacji zdalnej, takich jak z domu lub Centrum konferencji, lub gdy jedynie niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną.

Połączenia typu punkt-lokacja nie wymagają do prawidłowego działania urządzenia sieci VPN ani publicznego adresu IP. Połączenie z siecią VPN jest nawiązywane z komputera klienckiego. W związku z tym P2S nie zaleca się sposobem na łączenie z platformą Azure, w razie potrzeby trwałe połączenie z wielu lokalnych urządzeń i komputerów do sieci platformy Azure.

![Sieci VPN typu lokacja lokacja](media/azure-network-security/azure-network-security-fig-6.png)

> [!Note]
> Aby uzyskać więcej informacji na temat połączeń typu punkt-lokacja, zobacz [Point-to-Site FA v Q](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal).

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2).

Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. To połączenie odbywa się za pośrednictwem Internetu i pozwala na informacji "tuneli" wewnątrz zaszyfrowanych łącza między siecią i platformy Azure. Sieci VPN typu lokacja lokacja to bezpieczne, dojrzała technologia, która został wdrożony w przedsiębiorstwach każdej wielkości używana od dziesięcioleci. Tunel szyfrowanie odbywa się przy użyciu [trybu tunelowania IPsec](https://technet.microsoft.com/library/cc786385.aspx).

Sieci VPN typu lokacja lokacja jest technologia zaufanej, niezawodnej i ustanowionych, ruch sieciowy w ramach tunelu przechodzi przez Internet. Ponadto przepustowość stosunkowo jest ograniczone do maksymalnie około 200 MB/s.

Jeśli potrzebujesz wyjątkowych poziom zabezpieczeń i wydajności dla połączeń obejmujących wiele lokalizacji, zaleca się używać usługi Azure ExpressRoute dla łączności między środowiskami lokalnymi. Usługa ExpressRoute jest dedykowany sieci WAN łącze między Twojej lokalizacji lokalnej lub dostawcy usług hosta programu Exchange. Ponieważ jest to połączenie telco, dane nie przesyłane za pośrednictwem Internetu i w związku z tym nie jest narażony na potencjalne ryzyko związane z komunikację z Internetem.

> [!Note]
> Aby uzyskać więcej informacji na temat bram sieci VPN, zobacz temat [bramy sieci VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

#### <a name="dedicated-wan-link"></a>Łącza sieci WAN w wersji dedykowanej
Usługa Microsoft Azure ExpressRoute umożliwia rozszerzanie sieci lokalnych na platformę Azure za pośrednictwem specjalnego połączenia prywatnego wspieranego przez dostawcę połączenia.

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

![ Dedicated WAN Link](media/azure-network-security/azure-network-security-fig-7.png)

> [!Note]
> Aby uzyskać informacje na temat łączenia sieci z firmą Microsoft, przy użyciu usługi ExpressRoute, zobacz [modele połączeń usługi ExpressRoute](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) i [ExpressRoute — opis techniczny](https://docs.microsoft.com/azure/expressroute/expressroute-introduction).

Jako przy użyciu opcji sieci VPN typu lokacja lokacja, usługa ExpressRoute również pozwala łączyć się z zasobami, które nie są zawsze tylko jednej sieci wirtualnej. W rzeczywistości w zależności od jednostki SKU, możesz połączyć z 10 sieciami wirtualnymi. Jeśli masz [dodatek premium](https://docs.microsoft.com/azure/expressroute/expressroute-faqs), połączenia z maksymalnie 100 sieci wirtualne są możliwe, w zależności od przepustowości. Aby dowiedzieć się więcej na temat tych typów połączeń, zobacz jak, przeczytaj [diagramy topologii połączeń](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Opcje zabezpieczeń
Usługi Azure Virtual Network oferuje bezpieczne sieci logicznych, jest odizolowana od innych sieci wirtualnych oraz obsługę wielu kontroli zabezpieczeń, których używasz w sieciach lokalnych. Klientom tworzenie własnych struktury za pomocą: podsieci — ich używać własnych zakresu prywatnych adresów IP, konfigurowanie tabel tras, sieciowe grupy zabezpieczeń i listy kontroli dostępu (ACL), bramy i urządzeń wirtualnych, aby uruchamiać swoje obciążenia w chmurze.

Kontrole zabezpieczeń można użyć w sieciach wirtualnych platformy Azure są następujące:

-   Kontrolę dostępu do sieci

-   Trasy zdefiniowane przez użytkownika

-   Urządzenie zabezpieczeń sieciowych

-   Application Gateway

-   Zapora aplikacji sieci Web platformy Azure

-   Kontrola dostępności sieci

#### <a name="network-access-controls"></a>Kontrolę dostępu do sieci
Azure Virtual Network (VNet) stanowi podstawę model sieci platformy Azure i zapewnia izolację i ochronę, [grup zabezpieczeń sieci (NSG)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) to główne narzędzie służy do wymuszania i kontrolować reguły ruchu sieciowego na poziom sieci.

![ Kontrolę dostępu do sieci](media/azure-network-security/azure-network-security-fig-8.png)


Można kontrolować dostęp przez zezwalanie na dostęp lub odmawianie komunikacji między obciążeniami usług w ramach sieci wirtualnej, z systemów w sieciach klienta za pośrednictwem łączności między środowiskami lokalnymi lub bezpośrednia komunikacja z Internetem.

Na diagramie zarówno sieci wirtualne, jak i sieciowymi grupami zabezpieczeń znajdują się w określonej warstwie Azure ogólną zabezpieczeń stosu, gdzie sieciowych grup zabezpieczeń, Routing zdefiniowany przez użytkownika i wirtualnych urządzeń sieciowych może służyć do tworzenia granic zabezpieczeń, aby chronić wdrożenia aplikacji w sieci chronionej.

Sieciowe grupy zabezpieczeń służą 5-elementowych do oceny ruchu (i są używane w regułach skonfigurowanych dla sieciowej grupy zabezpieczeń):

-   [Źródłowy i docelowy adres IP](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Port źródłowy i docelowy](https://technet.microsoft.com/library/dd197515)

-   Protokół: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) lub [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Oznacza to, że można kontrolować dostęp między jedną maszynę Wirtualną i grupę maszyn wirtualnych lub jedną maszynę Wirtualną do innej maszyny Wirtualnej z jednego lub między podsieciami całego. Ponownie należy pamiętać, że jest to prosty pakietów filtrowania, inspekcja pakietów nie pełnemu. Brak, sprawdzanie poprawności protokołu lub identyfikatorów poziomu sieci i możliwości adresów IP w sieciowej grupie zabezpieczeń.

Sieciowa grupa zabezpieczeń zawiera kilka wbudowanych zasad, które trzeba wiedzieć. Są to:

-   **Zezwalaj na cały ruch w ramach określonej sieci wirtualnej:** Wszystkie maszyny wirtualne w tej samej sieci wirtualnej platformy Azure mogą komunikować się ze sobą.

-   **Zezwalaj na przychodzący Równoważenie obciążenia platformy Azure:**  ta reguła zezwala na ruch z dowolnego źródłowego adresu do dowolnego adresu docelowego dla modułu równoważenia obciążenia platformy Azure.

-   **Odmów wszystkich przychodzących:**  ta reguła blokuje cały ruch, określania źródła z Internetu, który ma być jawnie dozwolone.

-   **Zezwalaj na ruch wychodzący do Internetu cały ruch:** Ta zasada umożliwia maszyn wirtualnych do zainicjowania połączeń z Internetu. Jeśli użytkownik nie chce tych połączeń inicjowanych, należy utworzyć zasadę, aby zablokować te połączenia lub wymuszanie tunelowania.

#### <a name="system-routes-and-user-defined-routes"></a>Trasy systemowe i trasy zdefiniowane przez użytkownika

Po dodaniu maszyny wirtualne (VM) z siecią wirtualną (VNet) na platformie Azure, można zauważyć, że maszyny wirtualne będą mogli komunikować się ze sobą za pośrednictwem sieci, automatycznie. Nie ma potrzeby określania bramy, nawet gdy maszyny wirtualne znajdują się w różnych podsieciach.

Dotyczy to także komunikacji z maszyn wirtualnych do publicznej sieci Internet, a nawet do sieci lokalnej, gdy obecne jest połączenie hybrydowe z platformy Azure do własnego centrum danych.

![Trasy systemowe](media/azure-network-security/azure-network-security-fig-9.png)

Taki przepływ komunikacji jest możliwy, ponieważ platforma Azure korzysta z szeregu tras systemowych do definiowania przepływu ruchu w sieci IP. Trasy systemowe sterują przepływem komunikacji według następujących scenariuszy:

-   Z tej samej podsieci.

-   Z jednej podsieci do drugiej w ramach sieci wirtualnej.

-   Z maszyny wirtualnej do sieci Internet.

-   Z sieci wirtualnej do innej sieci wirtualnej za pośrednictwem bramy sieci VPN.

-   Z sieci wirtualnej z inną siecią wirtualną za pośrednictwem komunikacji równorzędnej sieci wirtualnych ([tworzenie łańcucha usług](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)).

-   Z sieci wirtualnej do sieci lokalnej za pośrednictwem bramy sieci VPN.

W wielu przedsiębiorstwach istnieją ścisłymi zasadami kontrolnymi zabezpieczeń i wymagań dotyczących zgodności, które wymagają lokalnej inspekcji wszystkich pakietów sieciowych w celu wymuszenia określonych zasad. Platforma Azure udostępnia do tego mechanizm nazywany [wymuszonego tunelowania](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-forced-tunneling) która kieruje ruch z maszyn wirtualnych do środowiska lokalnego, tworząc trasy niestandardowej lub [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) anonse za pośrednictwem usługi ExpressRoute lub sieci VPN.

Wymuszone tunelowanie w systemie Azure jest skonfigurowany za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika (UDR). Przekierowywanie ruchu do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure.

W poniższej sekcji przedstawiono bieżące ograniczenie tabeli routingu i trasy dla usługi Azure Virtual Network:

- Każda podsieć sieci wirtualnej ma wbudowane i tabelę routingu systemu. Tabela routingu system ma trzy następujące grupy trasy:

  -  **Lokalne trasy sieci wirtualnej:** Bezpośrednio do lokalizacji docelowej maszyny wirtualne w tej samej sieci wirtualnej

  - **W lokalnej trasy:** Do bramy sieci VPN platformy Azure

  -  **Trasa domyślna:** Bezpośrednio do Internetu. Pakiety przeznaczone do prywatnych adresów IP nieuwzględnionych przez poprzednie dwie trasy są porzucane.

- Wraz z wydaniem trasy zdefiniowane przez użytkownika można utworzyć tabeli routingu, aby dodać trasę domyślną i skojarz tabeli routingu do podsieci sieci wirtualnej można włączyć tunelowania wymuszonego tych podsieci.

- Należy ustawić "Domyślna witryna" wśród wielu lokacji lokalnych podłączone do sieci wirtualnej.

- Wymuszone tunelowanie muszą być skojarzone z siecią wirtualną, która ma sieci VPN bramy o dynamicznym routingu (nie brama statyczna).

- Usługa ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą tego mechanizmu, ale zamiast tego można włączyć, konfigurując anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute.

> [!Note]
> Aby uzyskać więcej informacji, zobacz [dokumentacja usługi ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) Aby uzyskać więcej informacji.

#### <a name="network-security-appliances"></a>Sieciowe urządzenia zabezpieczające
Gdy sieciowych grup zabezpieczeń i trasy zdefiniowane przez użytkownika może zapewnić miary zabezpieczenia sieci w sieci i mechanizm transportu warstw [OSI model](https://en.wikipedia.org/wiki/OSI_model), mają być sytuacje, gdzie ma lub należy włączyć zabezpieczenia na wyższe poziomy stosu sieciowego. W takich sytuacjach zalecamy wdrażania wirtualnego sieciowe urządzenia zabezpieczające udostępniane przez partnerów platformy Azure.

![Sieciowe urządzenia zabezpieczające](./media/azure-network-security/azure-network-security-fig-10.png)

Azure sieciowe urządzenia zabezpieczające poprawę zabezpieczeń sieci wirtualnej i funkcje sieciowe i są one dostępne od wielu dostawców za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Aby zapewnić można wdrożyć te urządzenia wirtualne zabezpieczeń:

-   Zapory o wysokiej dostępności

-   Zapobiegania włamaniom

-   Wykrywanie włamań

-   Zapory aplikacji sieci Web (Waf)

-   Optymalizacja sieci WAN

-   Routing

-   Równoważenie obciążenia

-   Sieć VPN

-   Zarządzanie certyfikatami

-   Usługa Active Directory

-   Uwierzytelnianie wieloskładnikowe

#### <a name="application-gateway"></a>Brama aplikacji

[Microsoft Azure Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-introduction) to dedykowane urządzenie wirtualne, która zapewnia kontroler dostarczania aplikacji (ADC) jako usługę.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-11.png)

Usługa Application Gateway umożliwia optymalizowanie wydajności farmy sieci web i dostępność dzięki przeniesieniu procesor CPU o znacznym wykorzystaniu kończenia żądań SSL na bramie aplikacji (odciążanie protokołu SSL). Zapewnia także inne możliwości routingu warstwy 7, takie jak:

-   Rozdzielanie ruchu przychodzącego

-   Koligacja sesji na podstawie plików cookie

-   Routing oparty na ścieżkach URL

-   Możliwość hostowania wielu witryn sieci Web za pojedynczą usługą Application Gateway


A [zapory aplikacji sieci web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) jest również udostępniany w ramach usługi application gateway. To zapewnia ochronę do aplikacji sieci web z typowych internetowymi wykorzystującymi luki w zabezpieczeniach. Usługa Application Gateway można skonfigurować jako kontakt z Internetem bramy, bramę tylko wewnętrzną lub jako kombinację obu tych.

Brama aplikacji zapory aplikacji internetowych mogą być uruchamiane w trybie wykrywania i zapobiegania. Typowy przypadek użycia jest dla administratorów do pracy w trybie wykrywania do obserwowania szkodliwe wzorce ruchu. Po wykryciu potencjalne luki w zabezpieczeniach, włączając tryb zapobiegania blokuje podejrzany ruch przychodzący.

 ![Application Gateway](./media/azure-network-security/azure-network-security-fig-12.png)

Ponadto, brama aplikacji zapory aplikacji internetowych ułatwia monitorowanie aplikacji sieci web przed atakami przy użyciu w czasie rzeczywistym dziennika zapory aplikacji sieci Web, które jest zintegrowane z [usługi Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview) i [usługi Azure Security Center](https://azure.microsoft.com/services/security-center/) śledzić alerty zapory aplikacji sieci Web oraz łatwego monitorowania trendów.

W dzienniku sformatowanego JSON przechodzi bezpośrednio do konta magazynu klienta. Mają pełną kontrolę nad tych dzienników i można zastosować zasad przechowywania.

Te dzienniki mogą również pozyskiwanie, do własnego analizę systemu przy użyciu [Azure Log Integration](https://aka.ms/AzLog). Dzienniki zapory aplikacji sieci Web jest również zintegrowana z [dzienniki usługi Azure Monitor](../log-analytics/log-analytics-overview.md) tak dzienniki usługi Azure Monitor umożliwia wykonywanie zaawansowanych zapytań szczegółowych.

#### <a name="azure-web-application-firewall-waf"></a>Zapora aplikacji sieci web platformy Azure (WAF)

Aplikacje sieci Web coraz cele złośliwych ataków wykorzystujących najpopularniejszych znanych luk w zabezpieczeniach, takie jak iniekcja SQL, ataki z użyciem skryptów cross-site i innych ataków, które pojawiają się w [OWASP top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Zapobieganie takie luki w zabezpieczeniach w aplikacji wymaga rygorystycznego przestrzegania harmonogramu konserwacji, poprawek i monitorowania na poziomie wielu warstw topologii aplikacji.

 ![Zapora aplikacji sieci Web platformy Azure (WAF)](./media/azure-network-security/azure-network-security-fig-13.png)

Scentralizowana [zapory aplikacji sieci web (WAF)](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-application-firewall-overview) można zabezpieczać się przed atakami z sieci web i upraszcza zarządzanie zabezpieczeniami bez konieczności wprowadzania zmian w aplikacjach.

Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

#### <a name="network-availability-controls"></a>Formanty dostępności sieci

Istnieją różne opcje umożliwiające dystrybucję ruchu sieciowego za pomocą platformy Microsoft Azure. Działanie tych opcji różni się między sobą, a same opcje wykorzystują różny zbiór funkcji i obsługują różne scenariusze. Można korzystać z nich osobno lub używać ich łącznie.

Poniżej przedstawiono kontroli dostępności sieci:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Usługa Azure Load balancer**

Zapewnia wysoką dostępność i wydajność sieci do aplikacji. Jest rozkładający ruch przychodzący między dobrej kondycji wystąpień usługi zdefiniowane w zestawie z równoważeniem obciążenia modułu równoważenia obciążenia warstwy 4 (TCP, UDP).

 ![Azure Load Balancer](media/azure-network-security/azure-network-security-fig-14.png)


Usługa Azure Load Balancer można skonfigurować w celu:

-   Obciążenia równoważenia ruchu internetowego przychodzącego do maszyn wirtualnych. Ta konfiguracja jest określana jako [równoważenia obciążenia dostępnego z Internetu](https://docs.microsoft.com/azure/load-balancer/load-balancer-internet-overview).

-   Równoważenie obciążenia ruchu między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługach w chmurze lub między komputerami w środowisku lokalnym i maszyn wirtualnych w sieci wirtualnej między lokalizacjami. Ta konfiguracja jest określana jako [wewnętrzne Równoważenie obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-internal-overview).

-   Przekazywanie ruchu zewnętrznego do określonej maszyny wirtualnej.

Wszystkie zasoby w chmurze muszą publiczny adres IP, który ma być dostępny z Internetu. Infrastruktury chmury na platformie Azure używa bez obsługi routingu adresów IP dla zasobów. Platforma Azure używa translatora adresów sieciowych (NAT) przy użyciu publicznych adresów IP do komunikowania się z Internetem.

 **Usługa Application gateway**

 Usługa Application Gateway działa w warstwie aplikacji (warstwa 7 w modelu odniesienia osi). Działa jako usługa zwrotnego serwera proxy, kończy połączenie klienta i przekazuje żądania do punktów końcowych zaplecza.

 **Usługa Traffic manager**

Microsoft Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu użytkowników do punktów końcowych usługi w różnych centrach danych. Punkty końcowe usługi obsługiwane przez usługę Traffic Manager obejmują maszyny wirtualne platformy Azure, aplikacje sieci Web i usług w chmurze. Usługi Traffic Manager można również używać z zewnętrznymi punktami końcowymi poza platformą Azure.

Usługa Traffic Manager przy użyciu systemu nazw domen (DNS) kieruje żądania klientów do najbardziej odpowiednich punktów końcowych, na podstawie [metody routingu ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods) i kondycji punktów końcowych. Usługa Traffic Manager udostępnia szereg metod routingu ruchu do potrzeb różnych aplikacji, punkt końcowy kondycji [monitorowania](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring)i automatycznej pracy awaryjnej. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi usługi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Usługa Traffic Manager zapewnia dwie kluczowe korzyści:

-   Dystrybucja ruchu sieciowym pogrupowane według jedną z kilku [metody routingu ruchu](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-routing-methods).

-   [Ciągłe monitorowanie kondycji punktu końcowego](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-monitoring) i automatycznej pracy awaryjnej, gdy nie punktów końcowych.

Gdy klient próbuje nawiązać połączenie z usługą, jego rozpoznać nazwy DNS usługi na adres IP. Klient następnie łączy się z tego adresu IP, aby uzyskać dostęp do usługi. Przy użyciu DNS usługi Traffic Manager kieruje klientów do punktów końcowych określonej usługi na podstawie reguł metody routingu ruchu. Klienci łączą się z wybranego punktu końcowego bezpośrednio. Usługa Traffic Manager nie jest serwer proxy lub bramą. Usługa Traffic Manager nie widzi ruch przekazywanie między klientem a usługą.

### <a name="azure-network-validation"></a>Sprawdzanie poprawności sieci platformy Azure

Weryfikacja sieci platformy Azure jest zapewnienie, że sieci platformy Azure działa jak jest skonfigurowany i może odbywać się sprawdzanie poprawności przy użyciu usług i funkcji dostępnych w przypadku monitorowania sieci. Azure Network Watcher umożliwia dostęp do mnóstwo rejestrowania i diagnostykę, które dają szczegółowe informacje umożliwiające zrozumienie Twojej wydajności i kondycji sieci. Te możliwości są dostępne za pośrednictwem portalu, powłoki Power Shell, interfejs wiersza polecenia, interfejs API Rest i zestawu SDK.

Zabezpieczenia usługi Azure Operational odnosi się do usługi, formanty i funkcje dostępne dla użytkowników na potrzeby ochrony danych, aplikacji i innych zasobów na platformie Microsoft Azure. Bezpieczeństwa platformy Azure działa w oparciu o strukturę, która zawiera wiedzy uzyskanej dzięki różnych funkcji, które są unikatowe w firmie Microsoft, w tym Microsoft cykl projektowania zabezpieczeń (SDL), program Microsoft Security odpowiedzi Centrum oraz głębokiej świadomości bezpieczeństwa cybernetycznego zagrożeniach.

-   [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-intro)

-   [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview)

-   [Azure Network watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview)

-   [Analizy usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Usługi Azure resource manager

Ludzi i procesy, które działają Microsoft Azure są najważniejszych funkcji zabezpieczeń platformy. W tej sekcji opisano funkcje, które pomagają poprawić Obsługa zabezpieczeń, ciągłości działania i ochrony prywatności firmy Microsoft w globalną infrastrukturę centrów danych.

Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja internetowa, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. Usługa Azure Resource Manager umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy.

Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu.

**Korzyści z używania usługi Resource Manager**

Usługa Resource Manager zapewnia kilka korzyści:

-   Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

-   Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

-   Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

-   Możliwość definiowania zależności między zasobami, dzięki czemu są one wdrażane w odpowiedniej kolejności.

-   Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.

-   Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

-   Możliwość wyjaśniania rozliczeń w organizacji przez wyświetlanie kosztów dla grupy zasobów, udostępnianie tagu.

> [!Note]
> Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli znasz wcześniejszy model wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz artykuł [Understanding Resource Manager deployment and classic deployment](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-model) (Opis wdrażania za pomocą usługi Resource Manager oraz wdrażania klasycznego).

## <a name="azure-network-logging-and-monitoring"></a>Monitorowanie i rejestrowanie sieci platformy Azure

Platforma Azure oferuje wiele narzędzi do monitorowania, zapobieganie, wykrywanie ich i odpowiadanie na zdarzenia zabezpieczeń sieci. Oto niektóre z najbardziej zaawansowanych narzędzi dostępnych w tym obszarze:

-   Network Watcher

-   Network Resource Level Monitoring

-   Dzienniki usługi Azure Monitor

### <a name="network-watcher"></a>Usługa Network watcher

[Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) — oparte na scenariuszach monitorowania jest dostarczana z funkcji usługi Network Watcher. Ta usługa obejmuje przechwytywanie pakietów, następny przeskok, przepływu dla adresu IP Sprawdź widok grup zabezpieczeń, dzienników przepływu sieciowych grup zabezpieczeń. Scenariusz poziomu monitorowania udostępnia widok typu end to end zasobów sieciowych, w przeciwieństwie do monitorowania zasobów poszczególnych sieci.

 ![Network Watcher](./media/azure-network-security/azure-network-security-fig-15.png)

Network Watcher to regionalna usługa, która pozwala na monitorowanie i diagnozowanie warunków na poziomie sieci, do i z platformy Azure. Diagnostyka sieci i narzędzi do wizualizacji dostępne w usłudze Network Watcher pomagają zrozumieć, diagnozowanie i uzyskiwanie szczegółowych informacji do sieci na platformie Azure.

Usługa Network Watcher jest obecnie ma następujące możliwości:

#### <a name="topology"></a>Topologia

[Topologia](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-overview) zwraca wykres zasobów sieciowych w sieci wirtualnej. Wykres przedstawia wzajemne połączenie między zasobami do reprezentowania połączenie sieciowe typu end to end. W portalu topologii zwraca obiekty zasobów zgodnie z poszczególnych sieci wirtualnej. Relacje są przedstawione przez linie między zasobami poza regionem usługi Network Watcher, nawet jeśli w zasobie grupy nie będą wyświetlane. Zasoby zwracane w widoku portalu są podzbiorem składników sieciowych, które wykresie przedstawia możliwościom. Aby wyświetlić pełną listę zasobów sieciowych, można użyć [PowerShell](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-powershell) lub [REST](https://docs.microsoft.com/azure/network-watcher/network-watcher-topology-rest).

Ponieważ zasoby są zwracane połączenie między one są modelowane w dwie relacje.

- **Zawieranie** -sieć wirtualna zawiera podsieci, która zawiera jedną kartą sieciową.

- **Skojarzone** -karta sieciowa jest skojarzony z maszyną Wirtualną.

#### <a name="variable-packet-capture"></a>Przechwytywanie pakietów zmiennych

Network Watcher [Przechwytywanie pakietów zmiennych](https://docs.microsoft.com/azure/network-watcher/network-watcher-packet-capture-overview) pozwala na tworzenie sesji przechwytywania pakietów, aby śledzić ruch do i z maszyny wirtualnej. Ułatwia Przechwytywanie pakietów do diagnozowania sieci anomalie zarówno w sposób reaktywny i proactivity. Inne zastosowania obejmują zbierania statystyk sieciowych, uzyskiwanie informacji na temat włamań sieci, debugowanie komunikacja klient serwer i wiele więcej.

Przechwytywanie pakietów jest uruchamiana zdalnie przy użyciu usługi Network Watcher rozszerzenie maszyny wirtualnej. Ta możliwość ułatwia obciążenia ręczne uruchomienie przechwytywania pakietów na odpowiednią maszynę wirtualną, co pozwoli zaoszczędzić cenny czas. Przechwytywanie pakietów mogą być wywoływane za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Jest jednym z przykładów jak przechwytywanie pakietów mogą być wywoływane z alertami maszyny wirtualnej.

#### <a name="ip-flow-verify"></a>Weryfikowanie przepływu adresów IP

[Sprawdź przepływów adresów IP](https://docs.microsoft.com/azure/network-watcher/network-watcher-ip-flow-verify-overview) sprawdza, czy pakiet jest dozwolony lub wychodzić do / z maszyny wirtualnej na podstawie 5-elementowych informacji. Te informacje składa się z kierunek, protokół, lokalny adres IP zdalnego adresu IP, port lokalny i port zdalny. Jeśli pakiet zostanie odrzucona przez grupę zabezpieczeń, zwracana jest nazwa reguły, która odrzuciła pakiet. Chociaż można wybrać dowolny źródłowy lub docelowy adres IP, ta funkcja pomaga administratorom szybkie diagnozowanie problemów z połączeniem z lub z Internetem i z lub do środowiska lokalnego.

Sprawdź przepływów adresów IP, jest przeznaczony dla interfejsu sieciowego maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie ustawień skonfigurowanych do lub z tym interfejsem sieciowym. Ta możliwość jest przydatna w potwierdzenie, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch przychodzący lub wychodzący ruch do lub z maszyny wirtualnej.

#### <a name="next-hop"></a>Następny przeskok

Określa [miejsca docelowego następnego skoku](https://docs.microsoft.com/azure/network-watcher/network-watcher-next-hop-overview) na pakiety przesyłane w sieci szkieletowej platformy Azure, umożliwiając diagnozowanie dowolne błędnie skonfigurowane trasy zdefiniowane przez użytkownika. Ruch z maszyny Wirtualnej jest wysyłany do miejsca docelowego na podstawie skutecznych tras skojarzonej z kartą sieciową. Następny przeskok pobiera typ następnego skoku i adres IP pakietu z konkretnej maszyny wirtualnej i karty sieciowej. Pomaga to w celu określenia, czy pakiet jest kierowanie do miejsca docelowego i jest holed czarny ruchu.

Następny przeskok zwraca również wartość tabeli tras skojarzonej z następnego przeskoku. Podczas wykonywania zapytań dotyczących miejsca docelowego następnego skoku, jeśli trasa jest zdefiniowana jako trasy zdefiniowane przez użytkownika, zostanie zwrócony tej trasy. W przeciwnym razie miejsca docelowego następnego skoku zwraca "Trasa systemowa".

#### <a name="security-group-view"></a>Widok grup zabezpieczeń

Pobiera reguły zabezpieczeń efektywny i zastosowane, które są stosowane na maszynie Wirtualnej. Sieciowe grupy zabezpieczeń są skojarzone na poziomie podsieci lub na poziomie karty Sieciowej. Gdy skojarzone na poziomie podsieci, ma zastosowanie do wszystkich wystąpień maszyn wirtualnych w podsieci. Sieć [widok grupy zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-security-group-view-overview) zwraca wszystkie skonfigurowane sieciowe grupy zabezpieczeń i reguły skojarzone na poziomie karty Sieciowej i podsieci dla maszyny wirtualnej, zapewniając wgląd w konfiguracji. Ponadto zwracane są obowiązujących reguł zabezpieczeń dla każdej z kart sieciowych na maszynie wirtualnej. Widok przy użyciu sieciowej grupy zabezpieczeń można ocenić maszyny Wirtualnej dla luk w zabezpieczeniach sieci, takich jak otwieranie portów. Możesz również walidować Jeśli sieciowej grupy zabezpieczeń działają zgodnie z oczekiwaniami, na podstawie [porównanie skonfigurowanych i efektywne reguły zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-auditing-powershell).

#### <a name="nsg-flow-logging"></a>Rejestrowanie przepływu sieciowych grup zabezpieczeń

 Dzienniki przepływów dla sieciowych grup zabezpieczeń umożliwiają przechwytywanie dzienniki dotyczące ruchu, które są dozwolone lub odmowa dostępu przez zasady zabezpieczeń w grupie. Przepływ jest definiowany przez informacji krotki 5: źródłowy adres IP, docelowy adres IP, portu źródłowego, Port docelowy i protokołu.

[Dzienniki przepływu sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/network-watcher/network-watcher-nsg-flow-logging-overview) są funkcją usługi Network Watcher, który służy do wyświetlania informacji na temat przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Brama sieci wirtualnej i rozwiązywanie problemów z połączeniem

Usługa Network Watcher udostępnia wiele możliwości, w odniesieniu do zrozumienia zasobów sieciowych na platformie Azure. Jedną z tych funkcji jest zasobem rozwiązywania problemów. [Rozwiązywanie problemów z zasobu](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest) można wywoływać za pomocą programu PowerShell, interfejsu wiersza polecenia lub interfejsu API REST. Po wywołaniu usługi Network Watcher sprawdza kondycję połączenia lub bramy sieci wirtualnej i zwraca jego wyniki.

Ta sekcja przeprowadzi Cię przez zadania zarządzania różnych, które są obecnie dostępne dla zasobów rozwiązywania problemów.

-   [Rozwiązywanie problemów z bramą sieci wirtualnej](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

-   [Rozwiązywanie problemów z połączeniem](https://docs.microsoft.com/azure/network-watcher/network-watcher-troubleshoot-manage-rest)

#### <a name="network-subscription-limits"></a>Limity subskrypcji sieci

[Limity subskrypcji sieci](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) umożliwiają ze szczegółami użycia każdego z zasobów sieciowych w ramach subskrypcji w regionie na maksymalnej liczbie dostępnych zasobów.

#### <a name="configuring-diagnostics-log"></a>Konfigurowanie diagnostyki dzienników

Usługa Network Watcher oferuje [dzienniki diagnostyczne](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview) widoku. Ten widok zawiera wszystkie zasoby sieciowe, które obsługują rejestrowania diagnostycznego. W tym widoku można włączać i wyłączać zasobami sieciowymi w przypadku, szybko i wygodnie.

### <a name="network-resource-level-monitoring"></a>Poziom monitorowania zasobów sieciowych

Następujące funkcje są dostępne dla monitorowania na poziomie zasobów:

#### <a name="audit-log"></a>Dziennik inspekcji

Operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Te dzienniki inspekcji są niezbędne do ustanowienia różne zachowania zgodności z prawnymi. Te dzienniki mogą być wyświetlane w witrynie Azure portal lub pobrać przy użyciu narzędzi firmy Microsoft, takich jak usługa Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API Rest.

> [!Note]
> Aby uzyskać więcej informacji na temat dzienników inspekcji, zobacz [inspekcji operacji przy użyciu usługi Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
Dzienniki inspekcji są dostępne dla operacje wykonywane na wszystkich zasobów sieciowych.


#### <a name="metrics"></a>Metryki

Metryki są pomiarów wydajności i liczniki zbierane w przedziale. Metryki są obecnie dostępne w usłudze Application Gateway. Metryki może służyć do wyzwolenia alertów na podstawie progu. Usługa Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa dowolnego zasobu z puli uznawana za złą. Usługa Application Gateway w dalszym ciągu monitorować złej kondycji wystąpień i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i odpowiadać na sondy kondycji. Usługa Application gateway wysyła sondy kondycji za pomocą tego samego portu, który jest zdefiniowany w ustawieniach HTTP zaplecza. Ta konfiguracja gwarantuje, że sonda testuje tego samego portu, którego klienci będą stosowane do łączenia się z zapleczem.

> [!Note]
> Zobacz [diagnostyki bramy aplikacji](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview) do wyświetlania, jak metryki może służyć do tworzenia alertów.

#### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Okresowe i spontanicznej zdarzenia są tworzone przez zasoby sieciowe i rejestrowane w ramach kont magazynu, wysłane do Centrum zdarzeń lub dzienniki usługi Azure Monitor. Te dzienniki zawierają szczegółowe informacje na temat kondycji zasobu. Te dzienniki mogą być wyświetlane w narzędzia, takie jak dzienniki usługi Power BI i usługi Azure Monitor. Aby dowiedzieć się, jak wyświetlić dzienniki diagnostyczne, odwiedź stronę [dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-networking-analytics).

Dzienniki diagnostyczne są dostępne dla [modułu równoważenia obciążenia](https://docs.microsoft.com/azure/load-balancer/load-balancer-monitor-log), [sieciowych grup zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log), tras i [Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics).

Usługa Network Watcher oferuje widok dla dzienników diagnostycznych. Ten widok zawiera wszystkie zasoby sieciowe, które obsługują rejestrowania diagnostycznego. W tym widoku można włączać i wyłączać zasobami sieciowymi w przypadku, szybko i wygodnie.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Dzienniki platformy Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) to usługa platformy Azure, która monitoruje środowiska chmurowe lokalnych i w celu zachowania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł.

Dzienniki platformy Azure Monitor udostępnia następujące rozwiązania do monitorowania sieci:

-   Rozwiązanie Network Performance Monitor (NPM)

-   Funkcja analizy usługi Azure Application Gateway

-   Analiza sieciowej grupy zabezpieczeń platformy Azure

#### <a name="network-performance-monitor-npm"></a>Rozwiązanie Network performance monitor (NPM)
[Rozwiązania Network Performance Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor) rozwiązanie do zarządzania jest rozwiązanie, które monitoruje kondycję, dostępności i osiągalności sieci do monitorowania sieci.

Służy do monitorowania łączności między:

-   Chmura publiczna i lokalnych

-   Centra danych i lokalizacji użytkownika (biurach oddziałów)

-   Podsieci, które hostują różne warstwy aplikacji wielowarstwowych.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Analiza bramy aplikacji platformy Azure w dziennikach w usłudze Azure Monitor

Bramy Application Gateway obsługuje następujące dzienniki:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Następujące metryki są obsługiwane w przypadku bram Application Gateway:

-   5-minutowych przepływności

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Analizy grupy zabezpieczeń sieci platformy Azure w dziennikach w usłudze Azure Monitor

Następujące dzienniki są obsługiwane w przypadku [sieciowe grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log):

- **NetworkSecurityGroupEvent:** Zawiera wpisy, dla których sieciowej grupy zabezpieczeń reguły są stosowane do maszyn wirtualnych i ról wystąpień na podstawie adresu MAC. Stan reguły te są gromadzone co 60 sekund.

- **NetworkSecurityGroupRuleCounter:** Zawiera wpisy dla tyle razy, każda reguła sieciowej grupy zabezpieczeń są stosowane do odmowy lub zezwolić na ruch.

## <a name="next-steps"></a>Kolejne kroki
Dowiedz się więcej o zabezpieczeń, zapoznając się niektóre z naszych tematy szczegółowe zabezpieczeń:

-   [Dzienniki monitora platformy Azure dla sieciowych grup zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-network-nsg-manage-log)

-   [Innowacje w sieci, które dysku przerw w działaniu chmury](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Sieci Przełącz oprogramowania tego uprawnienia globalne chmury firmy Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [W jaki sposób Microsoft kompiluje jego szybkie i niezawodne globalnej sieci](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Uruchamiamy innowacji sieci](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
