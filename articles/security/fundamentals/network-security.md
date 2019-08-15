---
title: Zabezpieczenia sieci platformy Azure | Microsoft Docs
description: Dowiedz się więcej na temat usług obliczeniowych opartych na chmurze, które obejmują szeroki wybór wystąpień obliczeniowych & usług, które mogą być automatycznie skalowane w górę i w dół w celu spełnienia potrzeb aplikacji lub przedsiębiorstwa.
services: security
documentationcenter: na
author: UnifyCloud
manager: barbkess
editor: TomSh
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: TomSh
ms.openlocfilehash: 3c4bdb3d46e4630257c2567cf2003ebede00b71e
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934786"
---
# <a name="azure-network-security"></a>Zabezpieczenia sieci na platformie Azure

Wiemy, że zabezpieczenia to zadanie jedno w chmurze i jak ważne jest, aby znaleźć dokładne i aktualne informacje o zabezpieczeniach platformy Azure. Jednym z najlepszych powodów używania platformy Azure dla aplikacji i usług jest skorzystanie z zalet szerokiej gamy narzędzi i możliwości zabezpieczeń platformy Azure. Te narzędzia i funkcje ułatwiają tworzenie bezpiecznych rozwiązań na platformie Azure.

Microsoft Azure zapewnia poufność, integralność i dostępność danych klientów, a także umożliwia przejrzyste odpowiedzialności. Aby lepiej zrozumieć zbieranie kontroli zabezpieczeń sieci wdrożonych w ramach Microsoft Azure z perspektywy klienta, ten artykuł "zabezpieczenia sieci platformy Azure" jest zapisywana w celu zapewnienia kompleksowego wglądu w kontrolę zabezpieczeń sieci dostępne dla Microsoft Azure.

Ten dokument ma na celu poinformowanie o szerokim zakresie kontroli sieci, które można skonfigurować w celu zwiększenia bezpieczeństwa rozwiązań wdrażanych na platformie Azure. Jeśli chcesz, aby firma Microsoft zabezpieczeni sieci szkieletowej platformy Azure, zapoznaj się z sekcją zabezpieczeń platformy Azure w [Centrum zaufania firmy Microsoft](https://microsoft.com/en-us/trustcenter/cloudservices/azure).

## <a name="azure-platform"></a>Platforma Azure

Azure to platforma usług w chmurze publicznej, która obsługuje szeroką gamę systemów operacyjnych, języków programowania, struktur, narzędzi, baz danych i urządzeń.  Może uruchamiać kontenery systemu Linux z integracją platformy Docker; Twórz aplikacje w językach JavaScript, Python, .NET, PHP, Java i Node. js; Twórz zaplecza dla urządzeń z systemem iOS, Android i Windows. Usługi w chmurze platformy Azure obsługują te same technologie, które miliony deweloperów i specjalistów IT są już zależne i ufają.

Podczas kompilowania lub migrowania zasobów IT do usługi w chmurze publicznej można polegać na możliwościach tej organizacji, aby chronić aplikacje i dane za pomocą usług i kontroli, które zapewniają Zarządzanie zabezpieczeniami zasobów opartych na chmurze.

Infrastruktura platformy Azure została zaprojektowana od funkcji do aplikacji do hostowania milionów klientów jednocześnie i zapewnia godną zaufania podstawę, w której firmy mogą spełniać wymagania dotyczące zabezpieczeń. Ponadto platforma Azure zapewnia obszerną kolekcję konfigurowalnych opcji zabezpieczeń oraz możliwość sterowania nimi, aby można było dostosować zabezpieczenia w taki sposób, aby spełniały unikatowe wymagania wdrożeń w organizacji.

## <a name="abstract"></a>Abstrakcyjny

Usługi w chmurze publicznej firmy Microsoft zapewniają usługi i infrastrukturę skalowania, możliwości klasy korporacyjnej oraz wiele opcji łączności hybrydowej. Możesz uzyskiwać dostęp do tych usług za pośrednictwem Internetu lub z usługi Azure ExpressRoute, która zapewnia łączność sieci prywatnej. Platforma Microsoft Azure pozwala bezproblemowo rozbudować infrastrukturę do chmury i utworzyć architektury wielowarstwowe. Ponadto firmy trzecie mogą włączyć Ulepszone możliwości, oferując usługi zabezpieczeń i urządzenia wirtualne.

Usługi sieciowe platformy Azure maksymalizują elastyczność, dostępność, odporność, bezpieczeństwo i integralność przez projektowanie. Ten oficjalny dokument zawiera szczegółowe informacje dotyczące funkcji sieciowych platformy Azure oraz informacje na temat sposobu korzystania przez klientów z natywnych funkcji zabezpieczeń platformy Azure w celu ochrony zasobów informacji.

Zaznaczeni odbiorcy tego dokumentu obejmują:

- Kierownicy techniczne, Administratorzy sieci i deweloperzy szukający rozwiązań zabezpieczeń dostępnych i obsługiwanych na platformie Azure.

-   MŚP lub dyrektorzy procesów roboczych, którzy chcą uzyskać ogólne omówienie technologii i usług sieciowych platformy Azure, które są istotne w dyskusjach dotyczących zabezpieczeń sieci w chmurze publicznej platformy Azure.

## <a name="azure-networking-big-picture"></a>Duże zdjęcie w sieci platformy Azure
Microsoft Azure obejmuje niezawodną infrastrukturę sieciową do obsługi wymagań dotyczących łączności aplikacji i usług. Możliwe jest połączenie sieciowe między zasobami znajdującymi się na platformie Azure, między zasobami lokalnymi i hostowanymi przez platformę Azure oraz z Internetu i platformy Azure.

![Duże zdjęcie w sieci platformy Azure](./media/network-security/azure-network-security-fig-1.png)

[Infrastruktura sieci platformy Azure](../../virtual-machines/windows/infrastructure-example.md) umożliwia bezpieczne łączenie zasobów platformy Azure ze sobą przy użyciu sieci wirtualnych (sieci wirtualnych). Sieć wirtualna jest reprezentacją własnej sieci w chmurze. Sieć wirtualna jest logiczną izolacją sieci w chmurze platformy Azure dedykowaną dla Twojej subskrypcji. Możesz połączyć sieci wirtualnych z sieciami lokalnymi.

Platforma Azure obsługuje dedykowane połączenia WAN z siecią lokalną i Virtual Network platformy Azure z usługą [ExpressRoute](../../expressroute/expressroute-introduction.md). Link między platformą Azure a lokacją korzysta z dedykowanego połączenia, które nie przechodzi przez publiczny Internet. Jeśli aplikacja platformy Azure działa w wielu centrach danych, możesz użyć [usługi azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) , aby skierować żądania od użytkowników w sposób inteligentny między wystąpieniami aplikacji. Możesz również kierować ruch do usług nieuruchomionych na platformie Azure, jeśli są one dostępne z Internetu.

## <a name="enterprise-view-of-azure-networking-components"></a>Widok przedsiębiorstwa składników sieciowych platformy Azure
Platforma Azure ma wiele składników sieciowych, które są istotne dla dyskusji o zabezpieczeniach sieci. Firma Microsoft opisuje te składniki sieciowe i koncentruje się na występujących w nich problemach dotyczących zabezpieczeń.

> [!Note]
> Nie wszystkie aspekty sieci platformy Azure są opisane — omawiamy tylko te, które są uważane za planowane i projektowanie bezpiecznej infrastruktury sieciowej otaczającej usługi i aplikacje wdrażane na platformie Azure.

W tym dokumencie przedstawiono następujące możliwości sieci platformy Azure dla przedsiębiorstw:

-   Podstawowa łączność sieciowa

-   Łączność hybrydowa

-   Kontrolki zabezpieczeń

-   Sprawdzanie poprawności sieci

### <a name="basic-network-connectivity"></a>Podstawowa łączność sieciowa

Usługa [azure Virtual Network](../../virtual-network/virtual-networks-overview.md) umożliwia bezpieczne łączenie zasobów platformy Azure ze sobą za pomocą sieci wirtualnych (VNET). Sieć wirtualna jest reprezentacją własnej sieci w chmurze. Sieć wirtualna jest logiczną izolacją infrastruktury sieci platformy Azure dedykowanej dla Twojej subskrypcji. Można także łączyć sieci wirtualnych ze sobą i z sieciami lokalnymi przy użyciu sieci VPN typu lokacja-lokacja i dedykowanych [łączy sieci WAN](../../expressroute/expressroute-introduction.md).

![Podstawowa łączność sieciowa](./media/network-security/azure-network-security-fig-2.png)

W przypadku korzystania z maszyn wirtualnych do hostowania serwerów na platformie Azure pytanie to sposób, w jaki te maszyny wirtualne łączą się z siecią. Odpowiedź polega na tym, że maszyny wirtualne łączą się z [Virtual Network platformy Azure](../../virtual-network/virtual-networks-overview.md).

Sieci wirtualne platformy Azure są podobne do sieci wirtualnych używanych lokalnie z własnymi rozwiązaniami platformy wirtualizacji, takimi jak Microsoft Hyper-V lub VMware.

#### <a name="intra-vnet-connectivity"></a>Łączność między sieciami wirtualnymi

Możesz połączyć sieci wirtualnych ze sobą, włączając zasoby połączone z sieciami wirtualnymi, aby komunikować się ze sobą w sieci wirtualnych. Aby połączyć sieci wirtualnych ze sobą, można użyć jednej lub obu poniższych opcji:

- **Komunikacji równorzędnej** Włącza zasoby połączone z różnymi sieci wirtualnychami platformy Azure w ramach tej samej lokalizacji platformy Azure, aby komunikować się ze sobą. Przepustowość i opóźnienie w sieci wirtualnej są takie same, jak w przypadku, gdy zasoby były połączone z tą samą siecią wirtualną. Aby dowiedzieć się więcej o komunikacji równorzędnej, Odczytaj [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md).

  ![Komunikacja równorzędna](./media/network-security/azure-network-security-fig-3.png)

- **Połączenie między sieciami wirtualnymi:** Włącza zasoby połączone z różnymi sieciami wirtualnymi platformy Azure w ramach tych samych lub różnych lokalizacji platformy Azure. W przeciwieństwie do komunikacji równorzędnej przepustowość jest ograniczona między sieci wirtualnych, ponieważ ruch musi przepływać przez VPN Gateway platformy Azure.

![Połączenie między sieciami wirtualnymi](./media/network-security/azure-network-security-fig-4.png)


Aby dowiedzieć się więcej na temat łączenia sieci wirtualnych z połączeniem między sieciami wirtualnymi, zapoznaj się z [artykułem Konfigurowanie połączenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json)między sieciami wirtualnymi.

#### <a name="azure-virtual-network-capabilities"></a>Możliwości usługi Azure Virtual Network:

Jak widać, usługa Azure Virtual Network udostępnia maszyny wirtualne do łączenia się z siecią, dzięki czemu mogą łączyć się z innymi zasobami sieci w bezpieczny sposób. Jednak podstawowa łączność jest już na początku. Następujące możliwości usługi Virtual Network platformy Azure uwidaczniają charakterystykę zabezpieczeń Virtual Network platformy Azure:

-   Izolacja

-   Łączność z Internetem

-   Łączność z zasobami platformy Azure

-   Łączność z siecią wirtualną

-   Łączność lokalna

-   Filtrowanie ruchu

-   Routing

**Izolacji**

Sieci wirtualnych są [](../../virtual-network/virtual-networks-overview.md) odizolowane od siebie nawzajem. Można utworzyć osobne sieci wirtualnych do projektowania, testowania i produkcji, które używają tych samych bloków adresów [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Z drugiej strony można utworzyć wiele sieci wirtualnych, które używają różnych bloków adresów CIDR i łączą się ze sobą. Sieć wirtualną można podzielić na wiele podsieci.

Platforma Azure udostępnia wewnętrzne rozpoznawanie nazw dla maszyn wirtualnych i [Cloud Services](https://azure.microsoft.com/services/cloud-services/) wystąpień roli podłączonych do sieci wirtualnej. Opcjonalnie można skonfigurować sieć wirtualną tak, aby korzystała z własnych serwerów DNS, a nie przy użyciu wewnętrznego rozpoznawania nazw platformy Azure.

Możesz zaimplementować wiele sieci wirtualnych w ramach każdej [subskrypcji](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure i [regionu](https://docs.microsoft.com/azure/azure-glossary-cloud-terminology?toc=%2fazure%2fvirtual-network%2ftoc.json)świadczenia usługi Azure. Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. Dla każdej sieci wirtualnej można:

-   Określenie niestandardowej prywatnej przestrzeni adresowej IP przy użyciu adresów publicznych i prywatnych (RFC 1918). Platforma Azure przypisuje zasoby połączone z siecią wirtualną prywatnym adresem IP z przestrzeni adresowej.

-   Należy podzielić sieć wirtualną na co najmniej jedną podsieć i przydzielić część przestrzeni adresowej sieci wirtualnej do każdej podsieci.

-   Użyj rozpoznawania nazw udostępnianej przez platformę Azure lub określ własny serwer DNS do użycia przez zasoby połączone z siecią wirtualną. Aby dowiedzieć się więcej na temat rozpoznawania nazw w sieci wirtualnych, przeczytaj temat [rozpoznawanie nazw dla maszyn wirtualnych i Cloud Services](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**Łączność z Internetem**

Wszystkie wystąpienia roli [usługi Azure Virtual Machines (VM)](../../virtual-machines/windows/index.yml) i Cloud Services role połączone z siecią wirtualną domyślnie mają dostęp do Internetu. W razie potrzeby można również włączyć dostęp przychodzący do określonych zasobów. (VM) i wystąpienia roli Cloud Services połączone z siecią wirtualną domyślnie mają dostęp do Internetu. W razie potrzeby można również włączyć dostęp przychodzący do określonych zasobów.

Wszystkie zasoby połączone z siecią wirtualną domyślnie mają połączenie wychodzące z Internetem. Prywatny adres IP zasobu jest przetłumaczony na publiczny adres IP w ramach infrastruktury platformy Azure. Można zmienić domyślną łączność przez implementację niestandardowego filtrowania routingu i ruchu. Aby dowiedzieć się więcej o wychodzącej łączności z Internetem, Przeczytaj [Opis połączeń wychodzących na platformie Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections?toc=%2fazure%2fvirtual-network%2ftoc.json).

Aby można było komunikować ruch przychodzący do zasobów platformy Azure z Internetu lub do komunikacji wychodzącej z Internetem bez niego, należy przypisać do zasobu publiczny adres IP. Aby dowiedzieć się więcej o publicznych adresach IP, zapoznaj się z [publicznymi adresami IP](../../virtual-network/virtual-network-public-ip-address.md).

**Łączność z zasobami platformy Azure**

[Zasoby platformy Azure](../../virtual-network/virtual-networks-overview.md) , takie jak Cloud Services i maszyny wirtualne, mogą być połączone z tą samą siecią wirtualną. Zasoby mogą łączyć się ze sobą przy użyciu prywatnych adresów IP, nawet jeśli znajdują się w różnych podsieciach. Platforma Azure udostępnia domyślny Routing między podsieciami, sieci wirtualnych i sieciami lokalnymi, dlatego nie trzeba konfigurować tras i zarządzać nimi.

Do sieci wirtualnej można podłączyć kilka zasobów platformy Azure, takich jak Virtual Machines (VM), Cloud Services, środowiska App Service i Virtual Machine Scale Sets. Maszyny wirtualne łączą się z podsiecią w sieci wirtualnej za pośrednictwem interfejsu sieciowego (NIC). Aby dowiedzieć się więcej na temat kart [](../../virtual-network/virtual-network-network-interface.md)sieciowych, zapoznaj się z interfejsami sieciowymi.

**Łączność z siecią wirtualną**

[Sieci wirtualnych](../../virtual-network/virtual-networks-overview.md) można połączyć ze sobą, włączając zasoby połączone z dowolną siecią wirtualną, aby komunikować się z dowolnym zasobem w innej sieci wirtualnej.

Możesz połączyć sieci wirtualnych ze sobą, włączając zasoby połączone z sieciami wirtualnymi, aby komunikować się ze sobą w sieci wirtualnych. Aby połączyć sieci wirtualnych ze sobą, można użyć jednej lub obu poniższych opcji:

- **Komunikacji równorzędnej** Włącza zasoby połączone z różnymi sieci wirtualnychami platformy Azure w ramach tej samej lokalizacji platformy Azure, aby komunikować się ze sobą. Przepustowość i opóźnienie w sieci wirtualnych są takie same, jak w przypadku, gdy zasoby były połączone z tym samym VNet.To Dowiedz się więcej o komunikacji równorzędnej, Odczytaj [wirtualne sieci równorzędne](../../virtual-network/virtual-network-peering-overview.md).

- **Połączenie między sieciami wirtualnymi:** Włącza zasoby połączone z różnymi sieciami wirtualnymi platformy Azure w ramach tych samych lub różnych lokalizacji platformy Azure. W przeciwieństwie do komunikacji równorzędnej przepustowość jest ograniczona między sieci wirtualnych, ponieważ ruch musi przepływać przez VPN Gateway platformy Azure. Aby dowiedzieć się więcej na temat łączenia sieci wirtualnych z połączeniem między sieciami wirtualnymi. Aby dowiedzieć się więcej, zapoznaj się z tematem [Konfigurowanie połączenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal?toc=%2fazure%2fvirtual-network%2ftoc.json) między sieciami wirtualnymi.

**Łączność lokalna**

Sieci wirtualnych można połączyć z sieciami [lokalnymi](../../virtual-network/virtual-networks-overview.md) za pośrednictwem prywatnych połączeń sieciowych między siecią i platformą Azure albo za pośrednictwem połączenia sieci VPN typu lokacja-lokacja za pośrednictwem Internetu.

Możesz połączyć sieć lokalną z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

- **Wirtualna sieć prywatna typu punkt-lokacja:** Ustanowiono między pojedynczym komputerem podłączonym do sieci i siecią wirtualną. Ten typ połączenia jest świetny, jeśli dopiero rozpoczynasz pracę z platformą Azure. Jest też odpowiedni dla deweloperów, ponieważ wymaga niewielkich zmian w istniejącej sieci lub nie wymaga ich wcale. Połączenie używa protokołu SSTP w celu zapewnienia zaszyfrowanej komunikacji przez Internet między komputerem i siecią wirtualną. Opóźnienie dla sieci VPN typu punkt-lokacja jest nieprzewidywalne, ponieważ ruch przechodzi przez Internet.

- **Sieć VPN typu lokacja-lokacja:** Ustanowiono między urządzeniem sieci VPN a VPN Gatewayem platformy Azure. Ten typ połączenia umożliwia dostęp do sieci wirtualnej w dowolnym z zasobów lokalnych. Połączenie to sieci VPN IPsec/IKE, która zapewnia zaszyfrowaną komunikację przez Internet między urządzeniem lokalnym i bramą sieci VPN platformy Azure. Opóźnienie dla połączenia typu lokacja-lokacja jest nieprzewidywalne, ponieważ ruch przechodzi przez Internet.

- **Azure ExpressRoute:** połączenie nawiązywane pomiędzy siecią i platformą Azure za pośrednictwem partnera usługi ExpressRoute. To połączenie jest prywatne. Ruch nie przechodzi przez Internet. Opóźnienie dla połączenia ExpressRoute jest przewidywalne, ponieważ ruch nie przechodzi przez Internet. Aby dowiedzieć się więcej na temat wszystkich poprzednich opcji połączenia, zapoznaj się z [diagramami topologii połączenia](../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Filtrowanie ruchu**

[Ruch sieciowy](../../virtual-network/virtual-networks-overview.md) dla wystąpień roli maszyny wirtualnej i Cloud Services może być filtrowany w ruchu przychodzącym i wychodzącym przez źródłowy adres IP i port, docelowy adres IP i port oraz protokół.

Ruch sieciowy pomiędzy podsieciami możesz filtrować przy użyciu jednej lub obu poniższych opcji:

- **Sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń):** Każdy sieciowej grupy zabezpieczeń może zawierać wiele reguł zabezpieczeń dla ruchu przychodzącego i wychodzącego, które umożliwiają filtrowanie ruchu przez źródłowy i docelowy adres IP, port i protokół. Można zastosować sieciowej grupy zabezpieczeń do każdej karty sieciowej na maszynie wirtualnej. Można również zastosować sieciowej grupy zabezpieczeń do podsieci karty sieciowej lub innego zasobu platformy Azure, z którą jest połączony. Aby dowiedzieć się więcej na temat sieciowych grup zabezpieczeń, Przeczytaj [sieciowe grupy zabezpieczeń](../../virtual-network/virtual-network-vnet-plan-design-arm.md).

- **Urządzenia Virtual Networkowe:** Urządzenie sieci wirtualnej to maszyna wirtualna, na której działa oprogramowanie, które wykonuje funkcję sieciową, taką jak Zapora. Wyświetl listę dostępnych urządzeń WUS w portalu Azure Marketplace. Dostępne są również urządzeń WUS zapewniające optymalizację sieci WAN i inne funkcje ruchu sieciowego. Urządzeń WUS są zwykle używane z trasami zdefiniowanymi przez użytkownika lub BGP. Można również użyć urządzenie WUS do filtrowania ruchu między sieci wirtualnych.

**Routing**

Opcjonalnie można zastąpić domyślny Routing platformy Azure przez skonfigurowanie własnych tras lub użycie tras BGP za pośrednictwem bramy sieci.

Na platformie Azure tworzone są tabele tras, które umożliwiają zasobom połączone z dowolną podsiecią w dowolnej wirtualnej komunikacji. Możesz zaimplementować jedną lub obie poniższe opcje, aby zastąpić domyślne trasy tworzone przez platformę Azure:

- **Trasy zdefiniowane przez użytkownika:** możesz utworzyć niestandardowe tabele tras z trasami kontrolującymi przekierowywanie ruchu do każdej podsieci. Aby dowiedzieć się więcej o trasach zdefiniowanych przez użytkownika, Przeczytaj [trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md).

- **Trasy protokołu BGP:** W przypadku połączenia sieci wirtualnej z siecią lokalną za pomocą usługi Azure VPN Gateway lub połączenia ExpressRoute można propagować trasy BGP do sieci wirtualnych.

### <a name="hybrid-internet-connectivity-connect-to-an-on-premises-network"></a>Hybrydowa łączność z Internetem: Nawiązywanie połączenia z siecią lokalną
Możesz połączyć sieć lokalną z siecią wirtualną przy użyciu dowolnej kombinacji następujących opcji:

-   Łączność z Internetem

-   Sieć VPN typu punkt-lokacja (P2S VPN)

-   Sieć VPN typu lokacja-lokacja (VPN S2S)

-   ExpressRoute

#### <a name="internet-connectivity"></a>Łączność z Internetem

Jako że nazwa sugeruje, połączenie z Internetem zapewnia dostęp do obciążeń z Internetu przez udostępnienie różnych publicznych punktów końcowych do obciążeń, które znajdują się w sieci wirtualnej. Te obciążenia mogą być uwidocznione przy użyciu [Load Balancer internetowego](../../load-balancer/load-balancer-overview.md#publicloadbalancer) lub po prostu przypisywania publicznego adresu IP do maszyny wirtualnej. W ten sposób będzie możliwe dotarcie do tej maszyny wirtualnej za pomocą zapory hosta, [sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń)](../../virtual-network/virtual-network-vnet-plan-design-arm.md)i [tras zdefiniowanych przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) w taki sposób.

W tym scenariuszu można uwidocznić aplikację, która musi być publiczna w Internecie i mieć możliwość łączenia się z nią z dowolnego miejsca lub z określonych lokalizacji w zależności od konfiguracji obciążeń.

#### <a name="point-to-site-vpn-or-site-to-site-vpn"></a>Sieć VPN typu punkt-lokacja lub sieć VPN typu lokacja-lokacja
Te dwa mieszczą się w tej samej kategorii. Obie te osoby potrzebują sieci wirtualnej, aby mieć VPN Gateway i można z nią łączyć się za pomocą klienta sieci VPN dla stacji roboczej w ramach [konfiguracji typu punkt-lokacja](../../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md) lub można skonfigurować lokalne [urządzenie sieci VPN](../../vpn-gateway/vpn-gateway-about-vpn-devices.md) , aby można było zakończyć działanie lokacja-lokacja. Połączenie. Dzięki temu urządzenia lokalne mogą łączyć się z zasobami w sieci wirtualnej.

Konfiguracja typu punkt-lokacja pozwala utworzyć indywidualne bezpieczne połączenie z siecią wirtualną z poziomu komputera klienckiego. Połączenie punkt-lokacja (P2S) to połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol).

![Sieci VPN typu punkt lokacja](./media/network-security/azure-network-security-fig-5.png)

Połączenia typu punkt-lokacja są przydatne, gdy chcesz połączyć się z siecią wirtualną z lokalizacji zdalnej, np. z domu lub z poziomu konferencji lub gdy masz tylko kilku klientów, którzy muszą łączyć się z sieciami wirtualnymi.

Połączenia typu punkt-lokacja nie wymagają do prawidłowego działania urządzenia sieci VPN ani publicznego adresu IP. Połączenie z siecią VPN jest nawiązywane z komputera klienckiego. W związku z tym P2S nie zaleca się nawiązywania połączenia z platformą Azure w przypadku, gdy potrzebne jest stałe połączenie z wielu lokalnych urządzeń i komputerów do sieci platformy Azure.

![Sieci VPN typu lokacja lokacja](./media/network-security/azure-network-security-fig-6.png)

> [!Note]
> Aby uzyskać więcej informacji na temat połączeń punkt-lokacja, zobacz [punkt do lokacji FA v Q](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md).

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2).

Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. To połączenie odbywa się za pośrednictwem Internetu i umożliwia "tunelowanie" w ramach zaszyfrowanego połączenia między siecią i platformą Azure. Sieci VPN typu lokacja-lokacja to bezpieczna, dojrzała technologia, która została wdrożona przez przedsiębiorstwa wszystkich rozmiarów dla dekad. Szyfrowanie tunelowe jest realizowane przy użyciu [trybu tunelowania IPSec](https://technet.microsoft.com/library/cc786385.aspx).

Gdy sieć VPN typu lokacja-lokacja jest zaufaną, niezawodną i ustanowioną technologią, ruch w ramach tunelu przechodzi przez Internet. Dodatkowo przepustowość jest stosunkowo ograniczona do maksymalnie 200 MB/s.

Jeśli potrzebujesz wyjątkowego poziomu zabezpieczeń lub wydajności dla połączeń obejmujących wiele lokalizacji, zalecamy użycie usługi Azure ExpressRoute na potrzeby łączności między lokalizacjami. ExpressRoute to dedykowany link sieci WAN między lokalizacją lokalną lub dostawcą hostingu programu Exchange. Ponieważ jest to połączenie odpływ, dane nie podróżują przez Internet i w związku z tym nie są narażone na potencjalne zagrożenia związane z komunikacją internetową.

> [!Note]
> Aby uzyskać więcej informacji na temat bram sieci VPN, zobacz temat About [VPN Gateway](../../vpn-gateway/vpn-gateway-about-vpngateways.md).

#### <a name="dedicated-wan-link"></a>Dedykowany link sieci WAN
Microsoft Azure ExpressRoute umożliwia rozbudowa sieci lokalnych na platformę Azure przez dedykowane połączenie prywatne obsługiwane przez dostawcę połączenia.

Połączenia ExpressRoute nie odbywają się za pośrednictwem publicznego Internetu. Dzięki temu oferują one większą niezawodność i szybkość oraz mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet.

![ Dedykowany link sieci WAN](./media/network-security/azure-network-security-fig-7.png)

> [!Note]
> Aby uzyskać informacje na temat sposobu łączenia sieci z firmą Microsoft przy użyciu usługi ExpressRoute, zobacz [modele łączności ExpressRoute](../../vpn-gateway/vpn-gateway-about-vpngateways.md) i [Omówienie techniczne ExpressRoute](../../expressroute/expressroute-introduction.md).

Podobnie jak w przypadku opcji sieci VPN typu lokacja-lokacja, ExpressRoute umożliwia również łączenie się z zasobami, które nie muszą należeć tylko do jednej z nich. W rzeczywistości, w zależności od jednostki SKU, można nawiązać połączenie z 10 sieci wirtualnych. Jeśli masz [dodatek Premium](../../expressroute/expressroute-faqs.md), możliwe jest połączenie z maksymalnie 100 sieci wirtualnych, w zależności od przepustowości. Aby dowiedzieć się więcej o tym, jak wyglądają te typy połączeń, należy odczytać [diagramy topologii połączeń](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json).

### <a name="security-controls"></a>Kontrolki zabezpieczeń
Usługa Azure Virtual Network zapewnia bezpieczną, sieci logicznej, która jest odizolowana od innych sieci wirtualnych i obsługuje wiele mechanizmów kontroli zabezpieczeń używanych w sieciach lokalnych. Klienci tworzą własną strukturę przy użyciu: podsieci — używają własnego zakresu prywatnych adresów IP, konfigurowania tabel tras, sieciowych grup zabezpieczeń, list kontroli dostępu (ACL), bram i urządzeń wirtualnych do uruchamiania ich obciążeń w chmurze.

Poniżej znajdują się mechanizmy kontroli zabezpieczeń, których można używać w sieciach wirtualnych platformy Azure:

-   Kontrola dostępu do sieci

-   Trasy zdefiniowane przez użytkownika

-   Urządzenie zabezpieczeń sieci

-   Application Gateway

-   Zapora aplikacji sieci Web platformy Azure

-   Kontrola dostępności sieci

#### <a name="network-access-controls"></a>Kontrola dostępu do sieci
Chociaż usługa Azure Virtual Network (VNet) jest podstawą modelu sieci platformy Azure i zapewnia izolację i ochronę, [sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](https://blogs.msdn.microsoft.com/igorpag/2016/05/14/azure-network-security-groups-nsg-best-practices-and-lessons-learned/) są głównym narzędziem używanym do wymuszania i kontrolowania reguł ruchu sieciowego na poziomie sieci.

![ Kontrola dostępu do sieci](./media/network-security/azure-network-security-fig-8.png)


Można kontrolować dostęp przez umożliwienie lub odmowę komunikacji między obciążeniami w ramach sieci wirtualnej, z systemów w sieci klienta za pośrednictwem łączności między lokalizacjami lub bezpośredniej komunikacji internetowej.

Na diagramie zarówno sieci wirtualnych, jak i sieciowych grup zabezpieczeń znajdują się w określonej warstwie w ogólnym stosie zabezpieczeń platformy Azure, w której urządzenia wirtualne sieciowych grup zabezpieczeń, UDR i Network mogą służyć do tworzenia granic zabezpieczeń w celu ochrony wdrożeń aplikacji w chronionej sieci.

Sieciowych grup zabezpieczeń użyć krotki 5 do oszacowania ruchu (i są używane w regułach skonfigurowanych dla sieciowej grupy zabezpieczeń):

-   [Źródłowy i docelowy adres IP](https://support.microsoft.com/help/969029/the-functionality-for-source-ip-address-selection-in-windows-server-2008-and-in-windows-vista-differs-from-the-corresponding-functionality-in-earlier-versions-of-windows)

-   [Port źródłowy i docelowy](https://technet.microsoft.com/library/dd197515)

-   Protokół: [Transmission Control Protocol (TCP)](https://technet.microsoft.com/library/cc940037.aspx) lub [User Datagram Protocol (UDP)](https://technet.microsoft.com/library/cc940034.aspx)

Oznacza to, że można kontrolować dostęp między pojedynczą maszyną wirtualną i grupą maszyn wirtualnych lub pojedynczą maszyną wirtualną do innej maszyny wirtualnej lub między całymi podsieciami. Należy pamiętać, że jest to proste filtrowanie pakietów stanowych, a nie pełna inspekcja pakietów. W sieciowej grupie zabezpieczeń nie ma możliwości weryfikacji protokołu ani identyfikatorów lub adresów IP na poziomie sieci.

SIECIOWEJ grupy zabezpieczeń zawiera pewne wbudowane reguły, których należy wiedzieć. Są to:

-   **Zezwalaj na cały ruch w ramach określonej sieci wirtualnej:** Wszystkie maszyny wirtualne w tej samej Virtual Network platformy Azure mogą komunikować się ze sobą.

-   **Zezwalaj na ruch przychodzący do usługi Azure Load Balancing:**  ta reguła umożliwia przesyłanie danych z dowolnego adresu źródłowego do dowolnego adresu docelowego dla modułu równoważenia obciążenia platformy Azure.

-   **Odrzuć wszystkie przychodzące:**  ta reguła blokuje wszystkie źródła ruchu z Internetu, które zostały jawnie dozwolone.

-   **Zezwalaj na cały ruch wychodzący do Internetu:** Ta reguła umożliwia maszynom wirtualnym Inicjowanie połączeń z Internetem. Jeśli nie chcesz, aby te połączenia zostały zainicjowane, należy utworzyć regułę, która będzie blokować te połączenia lub wymuszać Wymuszone tunelowanie.

#### <a name="system-routes-and-user-defined-routes"></a>Trasy systemowe i trasy zdefiniowane przez użytkownika

Podczas dodawania maszyn wirtualnych do sieci wirtualnej na platformie Azure można zauważyć, że maszyny wirtualne mogą komunikować się ze sobą za pośrednictwem sieci, automatycznie. Nie ma potrzeby określania bramy, nawet gdy maszyny wirtualne znajdują się w różnych podsieciach.

Dotyczy to także komunikacji z maszyn wirtualnych do publicznej sieci Internet, a nawet do sieci lokalnej, gdy obecne jest połączenie hybrydowe z platformy Azure do własnego centrum danych.

![Trasy systemowe](./media/network-security/azure-network-security-fig-9.png)

Taki przepływ komunikacji jest możliwy, ponieważ platforma Azure korzysta z szeregu tras systemowych do definiowania przepływu ruchu w sieci IP. Trasy systemowe sterują przepływem komunikacji według następujących scenariuszy:

-   Z tej samej podsieci.

-   Z jednej podsieci do drugiej w ramach sieci wirtualnej.

-   Z maszyny wirtualnej do sieci Internet.

-   Z sieci wirtualnej do innej sieci wirtualnej za pośrednictwem bramy sieci VPN.

-   Z sieci wirtualnej z inną siecią wirtualną za pośrednictwem komunikacji równorzędnej sieci wirtualnej ([łańcuch usług](../../virtual-network/virtual-network-peering-overview.md)).

-   Z sieci wirtualnej do sieci lokalnej za pośrednictwem bramy sieci VPN.

Wiele przedsiębiorstw ma rygorystyczne wymagania dotyczące zabezpieczeń i zgodności, które wymagają lokalnej inspekcji wszystkich pakietów sieciowych w celu wymuszenia określonych zasad. Platforma Azure zapewnia mechanizm o [](../../vpn-gateway/vpn-gateway-about-forced-tunneling.md) nazwie Wymuszone tunelowanie, który kieruje ruch z maszyn wirtualnych do lokalnego przez utworzenie trasy niestandardowej lub anonsów [Border Gateway Protocol (BGP)](https://docs.microsoft.com/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) za pośrednictwem usługi ExpressRoute lub sieci VPN.

Wymuszone tunelowanie w systemie Azure jest skonfigurowany za pośrednictwem sieci wirtualnej trasy zdefiniowane przez użytkownika (UDR). Przekierowywanie ruchu do lokacji lokalnej jest wyrażona jako domyślną trasę do bramy sieci VPN platformy Azure.

W poniższej sekcji przedstawiono bieżące ograniczenie tabeli routingu i trasy dla usługi Azure Virtual Network:

- Każda podsieć sieci wirtualnej ma wbudowane i tabelę routingu systemu. Tabela routingu system ma trzy następujące grupy trasy:

  -  **Lokalne trasy sieci wirtualnej:** Bezpośrednio do docelowych maszyn wirtualnych w tej samej sieci wirtualnej

  - **Lokalne trasy:** Do usługi Azure VPN Gateway

  -  **Trasa domyślna:** Bezpośrednio z Internetem. Pakiety przeznaczone dla prywatnych adresów IP, które nie są objęte poprzednimi dwiema trasami, są usuwane.

- Dzięki wykorzystaniu tras zdefiniowanych przez użytkownika można utworzyć tabelę routingu w celu dodania trasy domyślnej, a następnie skojarzyć tabelę routingu z podsiecią sieci wirtualnej w celu włączenia wymuszonego tunelowania w tych podsieciach.

- Należy ustawić "Domyślna witryna" wśród wielu lokacji lokalnych podłączone do sieci wirtualnej.

- Wymuszone tunelowanie muszą być skojarzone z siecią wirtualną, która ma sieci VPN bramy o dynamicznym routingu (nie brama statyczna).

- Usługa ExpressRoute wymuszonego tunelowania nie jest skonfigurowany za pomocą tego mechanizmu, ale zamiast tego można włączyć, konfigurując anonsuje trasę domyślną za pośrednictwem sesje komunikacji równorzędnej BGP usługi ExpressRoute.

> [!Note]
> Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) , aby uzyskać więcej informacji.

#### <a name="network-security-appliances"></a>Urządzenia zabezpieczające sieci
Mimo że sieciowe grupy zabezpieczeń i trasy zdefiniowane przez użytkownika mogą zapewnić pewne środki bezpieczeństwa sieci w sieci i warstwy transportu [modelu osi](https://en.wikipedia.org/wiki/OSI_model), zdarza się sytuacje, w których chcesz lub trzeba włączyć zabezpieczenia na wyższym poziomie stos sieciowy. W takich sytuacjach zalecamy wdrożenie urządzeń zabezpieczeń sieci wirtualnej udostępnianych przez partnerów platformy Azure.

![Urządzenia zabezpieczające sieci](./media/network-security/azure-network-security-fig-10.png)

Urządzenia zabezpieczeń sieci platformy Azure ulepszają zabezpieczenia sieci wirtualnej i funkcje sieciowe i są dostępne dla wielu dostawców za pośrednictwem [portalu Azure Marketplace](https://azuremarketplace.microsoft.com). Te wirtualne urządzenia zabezpieczeń można wdrożyć w celu zapewnienia:

-   Zapory o wysokiej dostępności

-   Zapobieganie włamaniom

-   Wykrywanie wtargnięcia

-   Zapory aplikacji sieci Web (sieciowi)

-   Optymalizacja sieci WAN

-   Routing

-   Równoważenie obciążenia

-   VPN

-   Zarządzanie certyfikatami

-   Usługa Active Directory

-   Uwierzytelnianie wieloskładnikowe

#### <a name="application-gateway"></a>Brama aplikacji

[Microsoft Azure Application Gateway](../../application-gateway/overview.md) to dedykowane urządzenie wirtualne udostępniające kontroler dostarczania aplikacji (ADC) jako usługę.

 ![Application Gateway](./media/network-security/azure-network-security-fig-11.png)

Application Gateway pozwala zoptymalizować wydajność i dostępność kolektywu serwerów sieci Web przez odciążenie zakończenia użycia procesora CPU przez protokół SSL do bramy aplikacji (odciążania protokołu SSL). Zapewnia także inne możliwości routingu warstwy 7, takie jak:

-   Rozkład okrężny ruchu przychodzącego

-   Koligacja sesji oparta na plikach cookie

-   Routing oparty na ścieżkach URL

-   Możliwość hostowania wielu witryn sieci Web za pojedynczym Application Gateway


[Zapora aplikacji sieci Web (WAF)](../../application-gateway/waf-overview.md) jest również dostępna jako część bramy aplikacji. Zapewnia to ochronę aplikacji sieci Web przed typowymi lukami w zabezpieczeniach sieci Web. Application Gateway można skonfigurować jako bramę mającą dostęp do Internetu, tylko wewnętrzną bramę lub kombinację obu tych elementów.

Application Gateway WAF można uruchomić w trybie wykrywania lub zapobiegania. Typowy przypadek użycia jest przeznaczony dla administratorów do działania w trybie wykrywania, aby obserwować ruch dla złośliwych wzorców. Po wykryciu potencjalnych luk w zabezpieczeniach tryb zapobiegania blokuje podejrzany ruch przychodzący.

 ![Application Gateway](./media/network-security/azure-network-security-fig-12.png)

Ponadto Application Gateway WAF pomaga monitorować aplikacje sieci Web przed atakami przy użyciu dziennika WAF w czasie rzeczywistym, który jest zintegrowany z [Azure monitor](../../azure-monitor/overview.md) i [Azure Security Center](https://azure.microsoft.com/services/security-center/) do śledzenia alertów WAF i łatwego monitorowania trendów.

Dziennik sformatowany w formacie JSON przechodzi bezpośrednio do konta magazynu klienta. Masz pełną kontrolę nad tymi dziennikami i można zastosować własne zasady przechowywania.

Możesz również pozyskiwać te dzienniki do własnego systemu analitycznego za pomocą [Azure log Integration](https://aka.ms/AzLog). Dzienniki WAF są również zintegrowane z [dziennikami Azure monitor](/azure/log-analytics/log-analytics-overview) , dzięki czemu można używać dzienników Azure monitor do wykonywania zaawansowanych zapytań szczegółowych.

#### <a name="azure-web-application-firewall-waf"></a>Zapora aplikacji sieci Web platformy Azure (WAF)

Aplikacje sieci Web są coraz bardziej ukierunkowane na złośliwe ataki wykorzystujące typowe znane luki w zabezpieczeniach, takie jak iniekcja SQL, ataki skryptów między lokacjami i inne ataki, które pojawiają się w [OWASP 10 pierwszych](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project). Uniemożliwianie takich ataków w aplikacji wymaga rygorystycznej konserwacji, poprawek i monitorowania na wielu warstwach topologii aplikacji.

 ![Zapora aplikacji sieci Web platformy Azure (WAF)](./media/network-security/azure-network-security-fig-13.png)

Scentralizowana [Zapora aplikacji sieci Web (WAF)](../../application-gateway/waf-overview.md) umożliwia ochronę przed atakami sieci Web i upraszcza zarządzanie zabezpieczeniami bez konieczności wprowadzania zmian w aplikacjach.

Zapora aplikacji internetowej może reagować na zagrożenia bezpieczeństwa szybciej — poprzez wdrażanie poprawek zapobiegających wykorzystaniu znanych luk w zabezpieczeniach w centralnej lokalizacji zamiast w poszczególnych aplikacjach internetowych. Istniejące bramy Application Gateway można łatwo przekonwertować na bramę Application Gateway obsługującą zaporę aplikacji internetowej.

#### <a name="network-availability-controls"></a>Kontrola dostępności sieci

Istnieją różne opcje umożliwiające dystrybucję ruchu sieciowego za pomocą platformy Microsoft Azure. Działanie tych opcji różni się między sobą, a same opcje wykorzystują różny zbiór funkcji i obsługują różne scenariusze. Można korzystać z nich osobno lub używać ich łącznie.

Poniżej przedstawiono kontrolę dostępności sieci:

-   Azure Load Balancer

-   Application Gateway

-   Traffic Manager

**Moduł równoważenia obciążenia platformy Azure**

Zapewnia wysoką dostępność i wydajność sieci dla aplikacji. Jest to moduł równoważenia obciążenia warstwy 4 (TCP, UDP), który dystrybuuje ruch przychodzący między prawidłowymi wystąpieniami usług zdefiniowanymi w zestawie o zrównoważonym obciążeniu.

 ![Azure Load Balancer](./media/network-security/azure-network-security-fig-14.png)


Azure Load Balancer można skonfigurować w taki sposób, aby:

-   Równoważenie obciążenia przychodzącego ruchu internetowego do maszyn wirtualnych. Ta konfiguracja jest znana jako [Usługa równoważenia obciążenia](../../load-balancer/load-balancer-overview.md#publicloadbalancer)dostępnego z Internetu.

-   Równoważyć obciążenie ruchu między maszynami wirtualnymi w sieci wirtualnej, między maszynami wirtualnymi w usługach w chmurze lub między komputerami lokalnymi i maszynami wirtualnymi w sieci wirtualnej obejmującej wiele lokalizacji. Ta konfiguracja jest znana jako [wewnętrzna usługa równoważenia obciążenia](../../load-balancer/load-balancer-overview.md#internalloadbalancer).

-   Przekazuj ruch zewnętrzny do określonej maszyny wirtualnej.

Wszystkie zasoby w chmurze muszą mieć publiczny adres IP, aby można było uzyskać dostęp z Internetu. Infrastruktura chmurowa na platformie Azure używa adresów IP bez obsługi routingu do zasobów. Platforma Azure używa translatora adresów sieciowych (NAT) z publicznymi adresami IP, aby komunikować się z Internetem.

 **Brama aplikacji**

 Application Gateway działa w warstwie aplikacji (warstwa 7 w stosie odwołań sieci OSI). Działa jako usługa zwrotnego serwera proxy, kończy połączenie klienta i przekazuje żądania do punktów końcowych zaplecza.

 **Traffic Manager**

Microsoft Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu użytkowników dla punktów końcowych usługi w różnych centrach danych. Punkty końcowe usługi obsługiwane przez Traffic Manager obejmują maszyny wirtualne platformy Azure, Web Apps i usługi w chmurze. Usługi Traffic Manager można również używać z zewnętrznymi punktami końcowymi poza platformą Azure.

Traffic Manager używa systemu nazw domen (DNS) do kierowania żądań klientów do najbardziej odpowiedniego punktu końcowego na podstawie [metody routingu ruchu](../../traffic-manager/traffic-manager-routing-methods.md) i kondycji punktów końcowych. Traffic Manager zapewnia szereg metod routingu ruchu w celu dopasowania do różnych potrzeb aplikacji, [monitorowania](../../traffic-manager/traffic-manager-monitoring.md)kondycji punktu końcowego i automatycznej pracy awaryjnej. Usługa Traffic Manager jest odporna na awarie, w tym awarię całego regionu platformy Azure.

Usługa Azure Traffic Manager umożliwia kontrolowanie dystrybucji ruchu między punktami końcowymi aplikacji. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią.

Traffic Manager zapewnia dwie kluczowe korzyści:

-   Rozkład ruchu zgodnie z jedną z kilku [metod routingu ruchu sieciowego](../../traffic-manager/traffic-manager-routing-methods.md).

-   [Ciągłe monitorowanie kondycji punktów końcowych](../../traffic-manager/traffic-manager-monitoring.md) i automatyczne przełączanie w tryb failover w przypadku niepowodzenia punktów końcowych.

Gdy klient próbuje nawiązać połączenie z usługą, musi najpierw rozpoznać nazwę DNS usługi na adres IP. Klient następnie łączy się z tym adresem IP w celu uzyskania dostępu do usługi. Traffic Manager używa systemu DNS do kierowania klientów do określonych punktów końcowych usługi na podstawie reguł metody routingu ruchu. Klienci łączą się bezpośrednio z wybranym punktem końcowym. Traffic Manager nie jest serwerem proxy ani bramą. Traffic Manager nie widzi ruchu przechodzącego między klientem a usługą.

### <a name="azure-network-validation"></a>Sprawdzanie poprawności sieci platformy Azure

Sprawdzanie poprawności sieci platformy Azure polega na zapewnieniu, że usługa Azure Network działa tak, jakby została skonfigurowana, a sprawdzanie poprawności można przeprowadzić za pomocą usług i funkcji dostępnych do monitorowania sieci. Dzięki usłudze Azure Network Watcher można uzyskać dostęp do mnóstwo funkcji rejestrowania i diagnostyki, które umożliwiają wgląd w szczegółowe informacje o wydajności i kondycji sieci. Te funkcje są dostępne za pośrednictwem portalu, narzędzia PowerShell, interfejsu wiersza polecenia i zestawu SDK Rest.

Zabezpieczenia operacyjne platformy Azure odnoszą się do usług, formantów i funkcji dostępnych dla użytkowników w celu ochrony danych, aplikacji i innych zasobów w Microsoft Azure. Usługa Azure Operational Security jest oparta na architekturze, która obejmuje wiedzę uzyskaną za pomocą różnych możliwości, które są unikatowe dla firmy Microsoft, w tym Microsoft Security Development (SDL), programu Microsoft Security Response Centre i dogłębną świadomością na poziomie zagrożenia bezpieczeństwa cybernetycznymi.

-   [Azure Security Center](../../security-center/security-center-intro.md)

-   [Azure Monitor](../../azure-monitor/overview.md)

-   [Obserwator sieci platformy Azure](../../network-watcher/network-watcher-monitoring-overview.md)

-   [Analiza usługi Azure Storage](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)

-   Azure Resource Manager

#### <a name="azure-resource-manager"></a>Usługa Azure Resource Manager

Osoby i procesy, które działają Microsoft Azure, są prawdopodobnie najważniejszymi funkcjami zabezpieczeń platformy. W tej sekcji opisano funkcje globalnej infrastruktury centrów danych firmy Microsoft, która pomaga w ulepszaniu i utrzymaniu zabezpieczeń, ciągłości i prywatności.

Infrastruktura aplikacji zwykle obejmuje wiele składników — może to być maszyna wirtualna, konto magazynu i sieć wirtualna albo aplikacja internetowa, baza danych, serwer bazy danych i usługi zewnętrzne. Te składniki nie są widoczne jako osobne jednostki, tylko jako powiązane i zależne od siebie nawzajem części jednej całości. Dlatego najlepiej wdrażać i monitorować je oraz zarządzać nimi grupowo. Usługa Azure Resource Manager umożliwia pracę z zasobami tworzącymi rozwiązanie w formie grupy.

Wszystkie zasoby danego rozwiązania można wdrożyć, zaktualizować lub usunąć w ramach jednej skoordynowanej operacji. Wdrażanie wykonuje się przy użyciu szablonu, którego można następnie używać w różnych środowiskach (testowanie, etap przejściowy i produkcja). Usługa Resource Manager zapewnia funkcje zabezpieczeń, inspekcji i znakowania ułatwiające zarządzanie zasobami po wdrożeniu.

**Korzyści wynikające z używania Menedżer zasobów**

Usługa Resource Manager zapewnia kilka korzyści:

-   Możliwość grupowego wdrożenia i monitorowania wszystkich zasobów w ramach rozwiązania oraz zarządzania nimi (zamiast obsługiwania zasobów pojedynczo).

-   Możliwość wielokrotnego wdrażania rozwiązania w całym cyklu programistycznym z gwarancją spójnego stanu zasobów po każdym wdrożeniu.

-   Możliwość zarządzania infrastrukturą przy użyciu szablonów deklaratywnych zamiast skryptów.

-   Można zdefiniować zależności między zasobami, aby zostały wdrożone w odpowiedniej kolejności.

-   Możliwość stosowania kontroli dostępu do wszystkich usług w grupie zasobów dzięki natywnej integracji funkcji kontroli dostępu na podstawie ról z platformą zarządzania.

-   Możliwość dodawania tagów do zasobów w celu logicznego uporządkowania wszystkich zasobów w ramach subskrypcji.

-   Aby wyjaśnić rozliczenia w organizacji, można wyświetlić koszty dla grupy znaczników udostępniania zasobów.

> [!Note]
> Usługa Resource Manager udostępnia nową metodę wdrażania rozwiązań i zarządzania nimi. Jeśli znasz wcześniejszy model wdrażania i chcesz dowiedzieć się więcej o zmianach, zobacz artykuł [Understanding Resource Manager deployment and classic deployment](../../azure-resource-manager/resource-manager-deployment-model.md) (Opis wdrażania za pomocą usługi Resource Manager oraz wdrażania klasycznego).

## <a name="azure-network-logging-and-monitoring"></a>Rejestrowanie i monitorowanie sieci platformy Azure

Platforma Azure oferuje wiele narzędzi do monitorowania, zapobiegania, wykrywania i reagowania na zdarzenia zabezpieczeń sieci. Niektóre z najbardziej zaawansowanych narzędzi dostępnych dla Ciebie w tym obszarze obejmują:

-   Network Watcher

-   Monitorowanie poziomu zasobów sieciowych

-   Dzienniki usługi Azure Monitor

### <a name="network-watcher"></a>Obserwator sieci

Monitorowanie oparte na scenariuszu [Network Watcher](../../network-watcher/network-watcher-monitoring-overview.md) jest dostarczane z funkcjami w Network Watcher. Ta usługa obejmuje funkcję przechwytywania pakietów, następny przeskok, sprawdzenie przepływu IP, widok grupy zabezpieczeń, dzienniki przepływu sieciowej grupy zabezpieczeń. Monitorowanie poziomu scenariusza zapewnia kompleksowy wgląd w zasoby sieciowe w przeciwieństwie do monitorowania poszczególnych zasobów sieciowych.

 ![Network Watcher](./media/network-security/azure-network-security-fig-15.png)

Network Watcher to usługa regionalna, która umożliwia monitorowanie i diagnozowanie warunków na poziomie scenariusza sieci w, do i z platformy Azure. Narzędzia do diagnostyki i wizualizacji sieci dostępne w Network Watcher pomagają zrozumieć, zdiagnozować i uzyskiwać wgląd w sieć na platformie Azure.

Network Watcher obecnie ma następujące możliwości:

#### <a name="topology"></a>Topologia

[Topologia](../../network-watcher/view-network-topology.md) zwraca Graf zasobów sieciowych w sieci wirtualnej. Wykres przedstawia połączenie między zasobami w celu reprezentowania kompleksowej łączności sieciowej. W portalu topologia zwraca obiekty zasobów w odniesieniu do każdej sieci wirtualnej. Relacje są przedstawiane według wierszy między zasobami spoza regionu Network Watcher, nawet jeśli w grupie zasobów nie będzie wyświetlana. Zasoby zwracane w widoku portalu są podzbiorem składników sieciowych, które są przedstawiane na wykresie. Aby zapoznać się z pełną listą zasobów sieciowych, można użyć [programu PowerShell](../../network-watcher/view-network-topology.md) lub [rest](../../network-watcher/view-network-topology.md).

Po zwróceniu zasobów połączenie między nimi jest modelowane w dwóch relacjach.

- Zawiera-Virtual Network zawierają podsieć, która zawiera kartę sieciową.

- **Skojarzone** — karta sieciowa jest skojarzona z maszyną wirtualną.

#### <a name="variable-packet-capture"></a>Przechwytywanie pakietów zmiennych

Network Watcher [przechwytywanie pakietów zmiennych](../../network-watcher/network-watcher-packet-capture-overview.md) umożliwia tworzenie sesji przechwytywania pakietów do śledzenia ruchu do i z maszyny wirtualnej. Przechwytywanie pakietów pomaga zdiagnozować anomalie w sieci zarówno w sposób aktywny, jak i w razie potrzeby. Inne zastosowania obejmują gromadzenie statystyk sieci, uzyskiwanie informacji o atakach sieci, debugowanie komunikacji klient-serwer i wiele więcej.

Przechwytywanie pakietów to rozszerzenie maszyny wirtualnej, które jest zdalnie uruchamiane za pomocą Network Watcher. Ta funkcja ułatwia obciążenie przechwycenia pakietu ręcznie na wybranej maszynie wirtualnej, co pozwala zaoszczędzić cenny czas. Przechwytywanie pakietów może być wyzwalane za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia lub API REST. Przykładem możliwości wyzwolenia funkcji przechwytywania pakietów są alerty maszyn wirtualnych.

#### <a name="ip-flow-verify"></a>Weryfikacja przepływu dla adresu IP

[Przepływy IP](../../network-watcher/network-watcher-ip-flow-verify-overview.md) sprawdzają, czy pakiet jest dozwolony, czy odrzucany do lub z maszyny wirtualnej na podstawie informacji o kolekcji 5. Te informacje obejmują kierunek, protokół, lokalny adres IP, zdalny adres IP, port lokalny i Port zdalny. Jeśli pakiet zostanie odrzucony przez grupę zabezpieczeń, zostanie zwrócona nazwa reguły, która odrzuciła pakiet. Gdy można wybrać dowolny źródłowy lub docelowy adres IP, ta funkcja ułatwia administratorom Szybkie diagnozowanie problemów z łącznością z lub do Internetu oraz z lub do środowiska lokalnego.

Przepływy IP weryfikują cel interfejsu sieciowego maszyny wirtualnej. Przepływ ruchu jest następnie weryfikowany na podstawie skonfigurowanych ustawień do lub z tego interfejsu sieciowego. Ta funkcja jest przydatna w przypadku potwierdzenia, czy reguła w sieciowej grupie zabezpieczeń blokuje ruch przychodzący lub wyjściowy do lub z maszyny wirtualnej.

#### <a name="next-hop"></a>Następny przeskok

Określa [Następny przeskok](../../network-watcher/network-watcher-next-hop-overview.md) dla pakietów przesyłanych w sieci szkieletowej platformy Azure, dzięki czemu można zdiagnozować wszelkie nieprawidłowo skonfigurowane trasy zdefiniowane przez użytkownika. Ruch z maszyny wirtualnej jest wysyłany do miejsca docelowego na podstawie efektywnych tras skojarzonych z kartą sieciową. Następny przeskok Pobiera typ następnego przeskoku i adres IP pakietu z określonej maszyny wirtualnej i karty sieciowej. Dzięki temu można określić, czy pakiet jest kierowany do miejsca docelowego, czy też czy ruch jest w czerni.

Następny przeskok zwraca również tabelę tras skojarzoną z następnym przeskokiem. Podczas wykonywania zapytania dotyczącego następnego przeskoku, jeśli trasa jest zdefiniowana jako trasa zdefiniowana przez użytkownika, ta trasa zostanie zwrócona. W przeciwnym wypadku następnym przeskokiem zwracany jest "trasa systemowa".

#### <a name="security-group-view"></a>Widok grup zabezpieczeń

Pobiera obowiązujące i stosowane reguły zabezpieczeń stosowane na maszynie wirtualnej. Sieciowe grupy zabezpieczeń są skojarzone na poziomie podsieci lub na poziomie karty sieciowej. Gdy jest on skojarzony z poziomem podsieci, ma zastosowanie do wszystkich wystąpień maszyn wirtualnych w podsieci. [Widok grup zabezpieczeń](../../network-watcher/network-watcher-security-group-view-overview.md) sieci zwraca wszystkie skonfigurowane sieciowych grup zabezpieczeń i reguły, które są skojarzone na karcie sieciowej i poziomie podsieci dla maszyny wirtualnej, która zapewnia wgląd w konfigurację. Ponadto obowiązujące reguły zabezpieczeń są zwracane dla każdej karty interfejsu sieciowego w maszynie wirtualnej. Za pomocą widoku sieciowej grupy zabezpieczeń można ocenić maszynę wirtualną pod kątem luk w zabezpieczeniach, takich jak otwarte porty. Możesz również sprawdzić, czy sieciowa Grupa zabezpieczeń działa zgodnie z oczekiwaniami na podstawie [porównania między skonfigurowanymi i obowiązującymi regułami zabezpieczeń](../../network-watcher/network-watcher-nsg-auditing-powershell.md).

#### <a name="nsg-flow-logging"></a>Rejestrowanie przepływu sieciowej grupy zabezpieczeń

 Dzienniki przepływu dla sieciowych grup zabezpieczeń umożliwiają przechwytywanie dzienników związanych z ruchem, które są dozwolone lub odrzucane przez reguły zabezpieczeń w grupie. Przepływ jest definiowany przy użyciu 5-informacje o spójnej kolekcji — źródłowy adres IP, docelowy adres IP, port źródłowy, port docelowy i protokół.

[Dzienniki przepływu sieciowych grup zabezpieczeń](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) są funkcją Network Watcher, która umożliwia wyświetlanie informacji dotyczących ruchu przychodzącego i wychodzącego IP za pomocą sieciowej grupy zabezpieczeń.

#### <a name="virtual-network-gateway-and-connection-troubleshooting"></a>Rozwiązywanie problemów z bramą sieci wirtualnej i połączeniem

Network Watcher udostępnia wiele funkcji, które odnoszą się do poznania zasobów sieciowych na platformie Azure. Jedną z tych możliwości jest rozwiązywanie problemów z zasobami. [Rozwiązywanie problemów z zasobami](../../network-watcher/network-watcher-troubleshoot-manage-rest.md) może być wywołane przez program PowerShell, interfejs wiersza polecenia lub interfejsu API REST. Gdy jest wywoływana, Network Watcher sprawdza kondycję Virtual Network bramy lub połączenia i zwraca swoje wyniki.

Ta sekcja przeprowadzi Cię przez różne zadania zarządzania, które są obecnie dostępne do rozwiązywania problemów z zasobami.

-   [Rozwiązywanie problemów z bramą Virtual Network](../../network-watcher/network-watcher-troubleshoot-manage-rest.md)

-   [Rozwiązywanie problemów z połączeniem](../../network-watcher/network-watcher-troubleshoot-manage-rest.md)

#### <a name="network-subscription-limits"></a>Limity dotyczące subskrypcji sieci

[Limity dotyczące subskrypcji sieci](../../network-watcher/network-watcher-monitoring-overview.md) zapewniają szczegółowe informacje o użyciu poszczególnych zasobów sieciowych w ramach subskrypcji w regionie w porównaniu z maksymalną liczbą dostępnych zasobów.

#### <a name="configuring-diagnostics-log"></a>Konfigurowanie dziennika diagnostycznego

Network Watcher udostępnia widok [dzienników diagnostycznych](../../network-watcher/network-watcher-monitoring-overview.md) . Ten widok zawiera wszystkie zasoby sieciowe, które obsługują rejestrowanie diagnostyczne. Z tego widoku możesz wygodnie i szybko włączać i wyłączać zasoby sieciowe.

### <a name="network-resource-level-monitoring"></a>Monitorowanie poziomu zasobów sieciowych

Do monitorowania na poziomie zasobów dostępne są następujące funkcje:

#### <a name="audit-log"></a>Dziennik inspekcji

Operacje wykonywane w ramach konfiguracji sieci są rejestrowane. Te dzienniki inspekcji są niezbędne do ustanowienia różnych zgodności. Te dzienniki można wyświetlać w Azure Portal lub pobrać przy użyciu narzędzi firmy Microsoft, takich jak Power BI lub narzędzi innych firm. Dzienniki inspekcji są dostępne za pomocą portalu, programu PowerShell, interfejsu wiersza polecenia i API REST.

> [!Note]
> Aby uzyskać więcej informacji na temat dzienników inspekcji, zobacz [operacje inspekcji z Menedżer zasobów](../../azure-resource-manager/resource-group-audit.md).
Dzienniki inspekcji są dostępne dla operacji wykonywanych na wszystkich zasobach sieciowych.


#### <a name="metrics"></a>Metryki

Metryki to pomiary wydajności i liczniki zbierane w danym okresie. Metryki są obecnie dostępne dla Application Gateway. Metryki mogą służyć do wyzwalania alertów na podstawie wartości progowej. Usługa Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa wszelkie zasoby uznawane za złej kondycji z puli. Application Gateway nadal monitoruje wystąpienia w złej kondycji i dodaje je z powrotem do odpowiedniej puli zaplecza, gdy staną się dostępne i reagują na sondy kondycji. Brama aplikacji wysyła sondy kondycji z tym samym portem, który jest zdefiniowany w ustawieniach protokołu HTTP zaplecza. Ta konfiguracja gwarantuje, że sonda testuje ten sam port, który będzie używany przez klientów do łączenia się z zapleczem.

> [!Note]
> Zobacz [Application Gateway Diagnostics](../../application-gateway/application-gateway-probe-overview.md) , aby zobaczyć, jak można używać metryk do tworzenia alertów.

#### <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

Zdarzenia okresowe i spontaniczne są tworzone przez zasoby sieciowe i rejestrowane na kontach magazynu, wysyłane do centrum zdarzeń lub dzienników Azure Monitor. Te dzienniki zapewniają wgląd w kondycję zasobu. Te dzienniki mogą być wyświetlane w narzędziach, takich jak dzienniki Power BI i Azure Monitor. Aby dowiedzieć się, jak wyświetlać dzienniki diagnostyczne, odwiedź [Azure monitor dzienników](../../azure-monitor/insights/azure-networking-analytics.md).

Dzienniki diagnostyczne są dostępne dla [Load Balancer](../../load-balancer/load-balancer-monitor-log.md), [sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md), tras i [Application Gateway](../../application-gateway/application-gateway-diagnostics.md).

Network Watcher udostępnia widok dzienników diagnostycznych. Ten widok zawiera wszystkie zasoby sieciowe, które obsługują rejestrowanie diagnostyczne. Z tego widoku możesz wygodnie i szybko włączać i wyłączać zasoby sieciowe.

### <a name="azure-monitor-logs"></a>Dzienniki usługi Azure Monitor

[Azure monitor Logs](../../log-analytics/log-analytics-queries.md) to usługa platformy Azure, która monitoruje środowiska chmurowe i lokalne w celu utrzymania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł.

Dzienniki Azure Monitor oferują następujące rozwiązania do monitorowania sieci:

-   Network Performance Monitor (NPM)

-   Analiza Application Gateway platformy Azure

-   Analiza grup zabezpieczeń sieci platformy Azure

#### <a name="network-performance-monitor-npm"></a>Monitor wydajności sieci (NPM)
Rozwiązanie do zarządzania [Network Performance Monitor](../../azure-monitor/insights/network-performance-monitor.md) to rozwiązanie do monitorowania sieci monitorujące kondycję, dostępność i osiągalność sieci.

Służy do monitorowania łączności między:

-   Chmura publiczna i lokalna

-   Centra danych i lokalizacje użytkowników (biura oddziałów)

-   podsieci obsługujące różne warstwy wielowarstwowej aplikacji.


#### <a name="azure-application-gateway-analytics-in-azure-monitor-logs"></a>Analiza usługi Azure Application Gateway w dziennikach Azure Monitor

W przypadku bram aplikacji obsługiwane są następujące dzienniki:

-   ApplicationGatewayAccessLog

-   ApplicationGatewayPerformanceLog

-   ApplicationGatewayFirewallLog

Następujące metryki są obsługiwane w przypadku bram aplikacji:

-   przepustowość 5 minut

#### <a name="azure-network-security-group-analytics-in-azure-monitor-logs"></a>Analiza grup zabezpieczeń sieci platformy Azure w dziennikach Azure Monitor

Następujące dzienniki są obsługiwane dla [sieciowych grup zabezpieczeń](../../virtual-network/virtual-network-nsg-manage-log.md):

- **NetworkSecurityGroupEvent:** Zawiera wpisy, dla których reguły sieciowej grupy zabezpieczeń są stosowane do maszyn wirtualnych i ról wystąpień opartych na adresie MAC. Stan tych reguł jest zbierany co 60 sekund.

- **NetworkSecurityGroupRuleCounter:** Zawiera wpisy dotyczące liczby przypadków zastosowania każdej reguły sieciowej grupy zabezpieczeń do odmowy lub zezwolenia na ruch.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o zabezpieczeniach, odczytując niektóre szczegółowe tematy dotyczące zabezpieczeń:

-   [Dzienniki Azure Monitor dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../../virtual-network/virtual-network-nsg-manage-log.md)

-   [Innowacje w sieci, które zwiększają zakłócenia w chmurze](https://azure.microsoft.com/blog/networking-innovations-that-drive-the-cloud-disruption/)

-   [SONiC: Oprogramowanie przełącznika sieci, które ma uprawnienia do chmury globalnej firmy Microsoft](https://azure.microsoft.com/blog/sonic-the-networking-switch-software-that-powers-the-microsoft-global-cloud/)

-   [Jak firma Microsoft kompiluje swoją szybką i niezawodną sieć globalną](https://azure.microsoft.com/blog/how-microsoft-builds-its-fast-and-reliable-global-network/)

-   [Oświetlenie innowacji sieciowych](https://azure.microsoft.com/blog/lighting-up-network-innovation/)
