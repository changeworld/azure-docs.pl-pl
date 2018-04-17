---
title: Plan sieci wirtualnej platformy Azure (VNet) oraz przewodnik dotyczący projektowania | Dokumentacja firmy Microsoft
description: Dowiedz się, jak zaplanować i zaprojektować sieci wirtualnych na platformie Azure, w zależności od wymagań izolacji, łączności i lokalizacji.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: tysonn
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2016
ms.author: jdial
ms.openlocfilehash: 6e41dae2f4e93fe2e3cef689596612a6a192c844
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="plan-and-design-azure-virtual-networks"></a>Planowanie i projektowanie sieci wirtualnych Azure
Tworzenie sieci wirtualnej do eksperymentów z jest dość proste, ale prawdopodobnie, wdrażania wielu sieci wirtualnych w czasie, aby obsługiwały produkcyjnym wymagania organizacji. Z pewnego planowania i projektowania będzie mógł wdrożyć sieci wirtualnych i połączyć zasoby, których potrzebujesz bardziej efektywnie. Jeśli nie masz doświadczenia z sieciami wirtualnymi, jest zalecane możesz [Dowiedz się więcej o sieci wirtualnych](virtual-networks-overview.md) i [wdrażanie](quick-create-portal.md) jeden przed kontynuowaniem.

## <a name="plan"></a>Planowanie
Dokładne zrozumienie subskrypcji platformy Azure, regiony i zasobów sieciowych jest szczególnie ważne w przypadku powodzenia. Lista zagadnień poniżej służy jako punkt początkowy. Po zrozumieniu tych zagadnień można zdefiniować wymagania dla projektu sieci.

### <a name="considerations"></a>Zagadnienia do rozważenia
Przed udzielenie odpowiedzi na planowanie pytania poniżej, należy rozważyć następujące kwestie:

* Wszystkie obiekty, które można utworzyć na platformie Azure składa się z co najmniej jeden zasób. Maszyna wirtualna (VM) jest zasobem, karty interfejsu sieciowego (NIC) używany przez maszynę Wirtualną jest zasobem zasobu jest publiczny adres IP używany przez kartę Sieciową, kartę Sieciową podłączoną do sieci wirtualnej jest zasobem.
* Utwórz zasoby w [regionu Azure](https://azure.microsoft.com/regions/#services) i subskrypcji. Zasoby mogą być połączone tylko z sieci wirtualnej, który istnieje w tym samym regionie i jest subskrypcji zasobu.
* Sieci wirtualne można połączyć ze sobą przy użyciu:
    * **[Sieci wirtualnej komunikacji równorzędnej](virtual-network-peering-overview.md)**: w tym samym regionie Azure musi znajdować się sieci wirtualnych. Przepustowość między zasobami w sieciach wirtualnych połączyć za pomocą jest taka sama, jak gdyby zasoby były podłączone do tej samej sieci wirtualnej.
    * **Azure [bramy sieci VPN](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)**: sieci wirtualnej może istnieć w tych samych lub różnych regionach platformy Azure. Przepustowość między zasobami w sieciach wirtualnych połączone za pośrednictwem bramy sieci VPN jest ograniczona przepustowość bramy sieci VPN.
* Sieci wirtualne mogą łączyć się z siecią lokalną, przy użyciu jednej z [opcji łączności](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) dostępnej na platformie Azure.
* Różne zasoby można grupować w [grup zasobów](../azure-resource-manager/resource-group-overview.md#resource-groups), co ułatwia zarządzanie zasobu jako jednostka. Grupa zasobów może zawierać zasobów z wielu regionach, tak długo, jak zasoby należą do tej samej subskrypcji.

### <a name="define-requirements"></a>Określenie wymagań w zakresie
Użycie tych pytań poniżej jako punkt początkowy dla projektu sieci platformy Azure.    

1. Ich lokalizacje Azure zostanie użyty do hosta sieci wirtualnych?
2. Potrzebujesz do zapewnienia komunikacji między tymi lokalizacjami Azure?
3. Czy trzeba zapewniają komunikację między VNet(s) Twojego Azure i datacenter(s) sieci lokalnej?
4. Ile infrastruktura jako usługa (IaaS) maszyn wirtualnych, usług w chmurze role i aplikacji sieci web potrzebne do rozwiązania?
5. Potrzebujesz do izolacji ruchu w oparciu o grupy maszyn wirtualnych (tj. fronton sieci web serwerów i serwerów bazy danych zaplecza)?
6. Czy trzeba sterowaniu przepływem ruchu przy użyciu wirtualnych urządzeń?
7. Czy użytkownicy mają różne zestawy uprawnień do różnych zasobów platformy Azure?

### <a name="understand-vnet-and-subnet-properties"></a>Zrozumienie właściwości sieci wirtualnej i podsieci
Zasoby sieci wirtualnej i podsieci pomocy, zdefiniuj granicę zabezpieczeń dla obciążeń działających na platformie Azure. Kolekcja przestrzeni adresów, zdefiniowany jako bloków CIDR charakteryzuje się sieci wirtualnej.

> [!NOTE]
> Administratorzy sieci znają notacji CIDR. Jeśli nie znasz CIDR, [Dowiedz się więcej o](http://whatismyipaddress.com/cidr).
>
>

Sieci wirtualne obejmują następujące właściwości.

| Właściwość | Opis | Ograniczenia |
| --- | --- | --- |
| **Nazwa** |Nazwa sieci wirtualnej |Ciąg do 80 znaków. Może zawierać litery, cyfry, podkreślenia, kropki i łączniki. Musi zaczynać się literą lub cyfrą. Musi kończyć się literą, cyfrą lub podkreśleniem. Można zawiera wielkich i małych liter. |
| **location** |Lokalizacja platformy Azure (zwaną także regionu). |Musi mieć jedną z prawidłowych lokalizacji platformy Azure. |
| **addressSpace** |Kolekcja prefiksów adresów, które tworzą sieci wirtualnej w notacji CIDR. |Musi być tablicą prawidłowy bloków adresów CIDR, w tym zakresy publicznych adresów IP. |
| **subnets** |Kolekcja podsieci, które tworzą sieci wirtualnej |znajdują się w poniższej tabeli właściwości podsieci. |
| **dhcpOptions** |Obiekt, który zawiera jednej wymaganej właściwości o nazwie **dnsServers**. | |
| **dnsServers** |Tablica serwery DNS używane przez sieci wirtualnej. Jeśli nie zostanie podana, rozpoznawania nazw wewnętrznych Azure jest używana. |Musi być tablicą maksymalnie 10 serwerów DNS, za pomocą adresu IP. |

Podsieć jest zasobem podrzędnych sieci wirtualnej i pomaga zdefiniować segmenty przestrzeni adresów w obrębie blok CIDR, przy użyciu prefiksów adresów IP. Karty sieciowe mogą być dodawane do podsieci lub podłączone do maszyn wirtualnych, zapewniają łączność dla różnych obciążeń.

Podsieci obejmują następujące właściwości.

| Właściwość | Opis | Ograniczenia |
| --- | --- | --- |
| **Nazwa** |Nazwa podsieci |Ciąg do 80 znaków. Może zawierać litery, cyfry, podkreślenia, kropki i łączniki. Musi zaczynać się literą lub cyfrą. Musi kończyć się literą, cyfrą lub podkreśleniem. Można zawiera wielkich i małych liter. |
| **location** |Lokalizacja platformy Azure (zwaną także regionu). |Musi mieć jedną z prawidłowych lokalizacji platformy Azure. |
| **addressPrefix** |Prefiks pojedynczy adres, który tworzą podsieci w notacji CIDR |Musi być jeden blok CIDR, który wchodzi w skład jednej z przestrzeni adresów sieci wirtualnej. |
| **networkSecurityGroup** |Grupa NSG stosowana do podsieci | |
| **routeTable** |Tabela tras stosowane do podsieci | |
| **elementy Ipconfiguration** |Kolekcja obiektów konfiguracji IP używane przez karty sieciowe podłączone do podsieci | |

### <a name="name-resolution"></a>Rozpoznawanie nazw
Domyślnie korzysta z sieci wirtualnej [rozpoznawania nazw platformy Azure](virtual-networks-name-resolution-for-vms-and-role-instances.md) do rozpoznawania nazw w sieci wirtualnej, a w publicznej sieci Internet. Jednak jeśli łączysz się z sieciami wirtualnymi w centrach danych w sieci lokalnej, należy podać [serwer DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md) do rozpoznawania nazw między sieci.  

### <a name="limits"></a>Limity
Przejrzyj ograniczenia sieci [Azure ogranicza](../azure-subscription-service-limits.md#networking-limits) artykuł, aby upewnić się, że projektu nie koliduje to z tych limitów. Niektóre limity można zwiększyć przez otwarcie biletu pomocy technicznej.

### <a name="role-based-access-control-rbac"></a>Kontrola dostępu oparta na rolach (RBAC)
Można użyć [Azure RBAC](../role-based-access-control/built-in-roles.md) kontrolować poziom dostępu różni użytkownicy mogą używać do różnych zasobów platformy Azure. W ten sposób może też oddzielić pracy przez zespół na podstawie swoich potrzeb.

Jeżeli sieci wirtualne są dane użytkowników w **współautora sieci** roli mają pełną kontrolę nad zasobami sieci wirtualnej Azure Resource Manager. Podobnie, użytkownicy w **klasycznego współautora sieci** rola ma pełną kontrolę nad zasoby klasyczne sieci wirtualnej.

> [!NOTE]
> Możesz również [tworzyć własne role](../role-based-access-control/role-assignments-portal.md) do oddzielania potrzeb administracyjnych.
>
>

## <a name="design"></a>Projekt
Jeśli znasz już odpowiedzi na pytania w [Planowanie](#Plan) Przejrzyj następujące przed zdefiniowaniem Twojej sieci wirtualnych.

### <a name="number-of-subscriptions-and-vnets"></a>Liczba subskrypcji i sieci wirtualnych
Należy rozważyć utworzenie wielu sieci wirtualnych w następujących scenariuszach:

* **Maszyny wirtualne, które muszą być umieszczone w różnych lokalizacjach Azure**. Sieci wirtualne na platformie Azure są regionalne. Nie obejmują one lokalizacji. W związku z tym należy co najmniej jedną sieć wirtualną dla każdej lokalizacji platformy Azure, do maszyn wirtualnych hosta w.
* **Obciążeń, które muszą być całkowicie odizolowane od siebie**. Można utworzyć oddzielne sieci wirtualnych, nawet używające tej samej przestrzeni adresów IP do izolowania różnych obciążeń od siebie nawzajem.

Należy pamiętać o tym, które są wyświetlone powyżej limitu na region na subskrypcję. Oznacza to, że wiele subskrypcji można użyć, aby zwiększyć limit zasobów, które można zachować na platformie Azure. Sieć VPN lokacja lokacja lub obwodu usługi ExpressRoute, umożliwia połączyć sieci wirtualnych w ramach różnych subskrypcji.

### <a name="subscription-and-vnet-design-patterns"></a>Subskrypcja i wzorce projektowe sieci wirtualnej
W poniższej tabeli przedstawiono niektóre typowe wzorce projektowe dotyczące korzystania z subskrypcji i sieci wirtualnych.

| Scenariusz | Diagram | Specjaliści | Wady |
| --- | --- | --- | --- |
| Jedną subskrypcją, dwie sieci wirtualne dla aplikacji |![Pojedyncza subskrypcja](./media/virtual-network-vnet-plan-design-arm/figure1.png) |Tylko jedna Subskrypcja do zarządzania. |Maksymalna liczba sieci wirtualnych na region platformy Azure. Potrzebujesz więcej subskrypcji po tym. Przegląd [Azure ogranicza](../azure-subscription-service-limits.md#networking-limits) artykułu, aby uzyskać szczegółowe informacje. |
| Z jedną subskrypcją jednej aplikacji, dwie sieci wirtualne dla aplikacji |![Pojedyncza subskrypcja](./media/virtual-network-vnet-plan-design-arm/figure2.png) |Używa tylko dwie sieci wirtualne na subskrypcję. |Trudne do zarządzania, gdy istnieją zbyt wiele aplikacji. |
| Z jedną subskrypcją jednej jednostki biznesowej, dwie sieci wirtualne dla aplikacji. |![Pojedyncza subskrypcja](./media/virtual-network-vnet-plan-design-arm/figure3.png) |Równowaga między liczbę subskrypcji i sieci wirtualnych. |Maksymalna liczba sieci wirtualnych na jednostki biznesowej (subscription). Przegląd [Azure ogranicza](../azure-subscription-service-limits.md#networking-limits) artykułu, aby uzyskać szczegółowe informacje. |
| Z jedną subskrypcją jednej jednostki biznesowej, dwie sieci wirtualne dla każdej grupy aplikacji. |![Pojedyncza subskrypcja](./media/virtual-network-vnet-plan-design-arm/figure4.png) |Równowaga między liczbę subskrypcji i sieci wirtualnych. |Aplikacje muszą występować samodzielnie za pomocą podsieci i grup NSG. |

### <a name="number-of-subnets"></a>Liczba podsieci
Należy rozważyć wiele podsieci w sieci wirtualnej w następujących scenariuszach:

* **Za mało prywatnych adresów IP dla wszystkich kart sieciowych w podsieci**. Przestrzeń adresowa podsieci nie zawiera za mało adresów IP dla wielu kart sieciowych w podsieci, należy utworzyć wiele podsieci. Należy pamiętać, że Azure rezerwuje 5 prywatnych adresów IP z każdej podsieci, której nie można użyć: imię i nazwisko adresami przestrzeni adresowej (dla adresów podsieci oraz multiemisji) i 3 umożliwia wewnętrznie (na potrzeby DHCP i DNS).
* **Bezpieczeństwo** Podsieci służy do oddzielania grupy maszyn wirtualnych od siebie nawzajem dla obciążenia, które mają wielowarstwową struktury i zastosowania różnych [sieciowej grupy zabezpieczeń (NSG)](virtual-networks-nsg.md#subnets) dla tych podsieci.
* **Połączenia hybrydowe**. Można użyć bramy sieci VPN i obwody usługi ExpressRoute, aby [połączyć](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti) Twojej sieci wirtualnych ze sobą oraz z center(s) danych lokalnych. Bramy sieci VPN i obwody usługi ExpressRoute wymagają podsieci we własnym ma zostać utworzony.
* **Urządzenie wirtualne**. Urządzenie wirtualne, takie jak zapory, sieci WAN akceleratora lub brama sieci VPN można użyć w sieć wirtualną platformy Azure. Po wykonaniu tej czynności należy [kierować ruchem](virtual-networks-udr-overview.md) do tych urządzeń i odizolowane w ich własnych podsieci.

### <a name="subnet-and-nsg-design-patterns"></a>Wzorce projektowe podsieci i grupy NSG
W poniższej tabeli przedstawiono niektóre typowe wzorce projektowe dotyczące korzystania z podsieci.

| Scenariusz | Diagram | Specjaliści | Wady |
| --- | --- | --- | --- |
| Jednej podsieci, grupy NSG na warstwie aplikacji dla aplikacji |![Pojedyncza podsieć](./media/virtual-network-vnet-plan-design-arm/figure5.png) |Tylko jednej podsieci do zarządzania. |Wiele grup NSG niezbędne w celu odizolowania każdej aplikacji. |
| Podsieć każdej aplikacji, grupy NSG na warstwie aplikacji |![Podsieć każdej aplikacji](./media/virtual-network-vnet-plan-design-arm/figure6.png) |Mniejszą liczbę grup NSG do zarządzania. |Wiele podsieci do zarządzania. |
| Jednej podsieci na warstwie aplikacji, grup NSG dla aplikacji. |![Podsieci w warstwie](./media/virtual-network-vnet-plan-design-arm/figure7.png) |Równowaga między wiele podsieci i grup NSG. |Maksymalna liczba grup NSG na subskrypcję. Przegląd [Azure ogranicza](../azure-subscription-service-limits.md#networking-limits) artykułu, aby uzyskać szczegółowe informacje. |
| W jednej podsieci na warstwie aplikacji dla aplikacji, grup NSG dla podsieci |![Podsieci w warstwie aplikacji](./media/virtual-network-vnet-plan-design-arm/figure8.png) |Prawdopodobnie mniejszą liczbę grup NSG. |Wiele podsieci do zarządzania. |

## <a name="sample-design"></a>Przykładowy projekt
Aby zilustrować aplikacji informacje przedstawione w tym artykule, należy rozważyć następujący scenariusz.

Pracujesz dla firmy, która ma 2 centrów danych w Ameryce Północnej i centrów danych dwa Europy. Zidentyfikowano 6 aplikacji połączonej różnych klientów obsługiwana przez 2 różnych jednostek biznesowych, które chcesz migrować na platformie Azure jako pilotażu. Podstawowa architektura aplikacji są następujące:

* App1, App2 App3 i App4 są hostowane na serwerach Linux z systemem Ubuntu aplikacji sieci web. Każda aplikacja nawiązuje połączenie z serwerem aplikacji oddzielne obsługującego usługi RESTful na serwerach Linux. Usługi RESTful nawiązać wewnętrzna baza danych MySQL.
* App5 i App6 są aplikacji sieci web na serwerach z systemem Windows z systemem Windows Server 2012 R2. Każda aplikacja łączy się do wewnętrznej bazy danych programu SQL Server.
* Wszystkie aplikacje są obecnie hostowane w jednym z centrów danych firmy w Ameryce Północnej.
* Lokalnymi centrami danych, użyj przestrzeni adresowej 10.0.0.0/8.

Należy zaprojektować rozwiązanie sieci wirtualnej, która spełnia następujące wymagania:

* Zużycie zasobów w innych jednostek biznesowych nie powinny mieć wpływ na poszczególnych jednostek biznesowych.
* Należy zminimalizować ilość sieci wirtualnych i podsieci, aby ułatwić zarządzanie.
* Poszczególnych jednostek biznesowych powinien mieć pojedynczy test/programowanie używane dla wszystkich aplikacji w sieci wirtualnej.
* Każda aplikacja znajduje się w 2 różnych Azure centrach danych na kontynencie (Ameryce Północnej i Europie).
* Każda aplikacja jest całkowicie odizolowane od siebie nawzajem.
* Każdej aplikacji są dostępne przez klientów w Internecie przy użyciu protokołu HTTP.
* Każda aplikacja może uzyskiwać użytkowników połączonych centrów danych lokalnych przy użyciu tunelu zaszyfrowane.
* Połączenie z lokalnymi centrami danych, należy używać istniejącego urządzenia sieci VPN.
* Grupa sieci firmowej powinna mieć pełną kontrolę nad konfigurację sieci wirtualnej.
* Deweloperzy w poszczególnych jednostek biznesowych tylko powinna być mógł wdrożyć maszyn wirtualnych do istniejących podsieci.
* Wszystkie aplikacje zostaną poddane migracji, ponieważ są one na platformie Azure (przyrostu i zmiany).
* Bazy danych w każdej lokalizacji powinny replikowane do innych lokalizacji Azure raz dziennie.
* Każdej aplikacji należy używać 5 serwerów sieci web frontonu, 2 serwery aplikacji (w razie potrzeby) i 2 serwery bazy danych.

### <a name="plan"></a>Planowanie
Należy rozpocząć planowanie o udzielenie odpowiedzi na pytania w projekcie [określenie wymagań w zakresie](#Define-requirements) sekcji, jak pokazano poniżej.

1. Ich lokalizacje Azure zostanie użyty do hosta sieci wirtualnych?

    2 lokalizacje w Ameryce Północnej i 2 lokalizacji w Europie. Należy wybrać te, na podstawie fizycznej lokalizacji centrami danych lokalnych. W ten sposób połączenia z Twojej lokalizacji fizycznych do platformy Azure będą mieć lepszą opóźnienia.
2. Potrzebujesz do zapewnienia komunikacji między tymi lokalizacjami Azure?

    Tak. Ponieważ bazy danych muszą zostać zreplikowane do wszystkich lokalizacji.
3. Czy trzeba zapewniają komunikację między VNet(s) Twojego Azure i lokalnymi center(s) danych?

    Tak. Ponieważ użytkowników połączonych z lokalnymi centrami danych musi mieć możliwość dostępu do aplikacji za pośrednictwem tunelu zaszyfrowane.
4. Jak wiele maszyn wirtualnych IaaS należy użyć dla rozwiązania?

    200 maszyn wirtualnych IaaS. App1, App2 App3 i App4 wymagają 5 serwerów sieci web, każdy, 2 aplikacji na każdym serwerze i serwerami baz danych 2 każdego. To suma 9 maszyn wirtualnych IaaS na aplikację lub 36 maszyn wirtualnych IaaS. App5 i App6 wymagają 5 serwerów sieci web, jak i 2 serwery baz danych. To suma 7 maszyn wirtualnych IaaS na aplikację lub 14 maszyn wirtualnych IaaS. W związku z tym należy 50 maszyn wirtualnych IaaS dla wszystkich aplikacji w każdym regionie Azure. Ponieważ należy użyć 4 regionów, będzie 200 maszyn wirtualnych IaaS.

    Należy również podać serwerów DNS w każdej sieci wirtualnej lub w centrach danych z lokalnego do rozpoznawania nazw między maszyn wirtualnych IaaS platformy Azure i siecią lokalną.
5. Potrzebujesz do izolacji ruchu w oparciu o grupy maszyn wirtualnych (tj. fronton sieci web serwerów i serwerów bazy danych zaplecza)?

    Tak. Każda aplikacja powinna być całkowicie odizolowane od siebie, a także każdej warstwy aplikacji powinna zostać odizolowana.
6. Czy trzeba sterowaniu przepływem ruchu przy użyciu wirtualnych urządzeń?

    Nie. Urządzenie wirtualne może służyć do zapewnienia większą kontrolę nad przepływ ruchu, tym bardziej szczegółowe rejestrowanie płaszczyzna danych.
7. Czy użytkownicy mają różne zestawy uprawnień do różnych zasobów platformy Azure?

    Tak. Zespół sieci musi pełnej kontroli dla ustawień sieci wirtualnej, gdy deweloperzy tylko powinien mieć możliwość wdrażania ich maszyn wirtualnych do istniejącego podsieci.

### <a name="design"></a>Projekt
Należy stosować projektu określenie subskrypcji, sieci wirtualnych, podsieci i grup NSG. W tym miejscu omówimy grup NSG, ale powinien więcej informacji o [grup NSG](virtual-networks-nsg.md) przed zakończeniem projektu.

**Liczba subskrypcji i sieci wirtualnych**

Następujące wymagania odnoszą się do subskrypcji i sieci wirtualnych:

* Zużycie zasobów w innych jednostek biznesowych nie powinny mieć wpływ na poszczególnych jednostek biznesowych.
* Należy zminimalizować ilość sieci wirtualnych i podsieci.
* Poszczególnych jednostek biznesowych powinien mieć pojedynczy test/programowanie używane dla wszystkich aplikacji w sieci wirtualnej.
* Każda aplikacja znajduje się w 2 różnych Azure centrach danych na kontynencie (Ameryce Północnej i Europie).

Na podstawie tych wymagań, musisz mieć subskrypcję dla poszczególnych jednostek biznesowych. W ten sposób zużycia zasobów przy użyciu jednostki biznesowe zostaną nie wchodzą w skład limity dla innych jednostek biznesowych. I ponieważ chcesz ograniczyć liczbę sieci wirtualnych, należy rozważyć użycie **z jedną subskrypcją jednej jednostki biznesowej, dwie sieci wirtualne dla każdej grupy aplikacji** wzorca, jak pokazano poniżej.

![Pojedyncza subskrypcja](./media/virtual-network-vnet-plan-design-arm/figure9.png)

Należy również określić dla każdej sieci wirtualnej przestrzeni adresowej. Ponieważ należy połączenie między danych lokalnych w centrach regiony platformy Azure, przestrzeni adresowej używane dla sieci wirtualnych platformy Azure nie mogą powodować konfliktów z sieci lokalnej i używane przez każdego sieci wirtualnej przestrzeni adresowej powinien nie mogą powodować konfliktów z innych istniejących sieci wirtualnych. Przestrzenie adresowe w poniższej tabeli można użyć do spełnienia tych wymagań.  

| **Subskrypcja** | **Sieć wirtualna** | **Region platformy Azure** | **Przestrzeń adresowa** |
| --- | --- | --- | --- |
| BU1 |ProdBU1US1 |Zachodnie stany USA |172.16.0.0/16 |
| BU1 |ProdBU1US2 |Wschodnie stany USA |172.17.0.0/16 |
| BU1 |ProdBU1EU1 |Europa Północna |172.18.0.0/16 |
| BU1 |ProdBU1EU2 |Europa Zachodnia |172.19.0.0/16 |
| BU1 |TestDevBU1 |Zachodnie stany USA |172.20.0.0/16 |
| BU2 |TestDevBU2 |Zachodnie stany USA |172.21.0.0/16 |
| BU2 |ProdBU2US1 |Zachodnie stany USA |172.22.0.0/16 |
| BU2 |ProdBU2US2 |Wschodnie stany USA |172.23.0.0/16 |
| BU2 |ProdBU2EU1 |Europa Północna |172.24.0.0/16 |
| BU2 |ProdBU2EU2 |Europa Zachodnia |172.25.0.0/16 |

**Liczba podsieci i grupy NSG**

Następujące wymagania odnoszą się do podsieci i grup NSG:

* Należy zminimalizować ilość sieci wirtualnych i podsieci.
* Każda aplikacja jest całkowicie odizolowane od siebie nawzajem.
* Każdej aplikacji są dostępne przez klientów w Internecie przy użyciu protokołu HTTP.
* Każda aplikacja może uzyskiwać użytkowników połączonych centrów danych lokalnych przy użyciu tunelu zaszyfrowane.
* Połączenie z lokalnymi centrami danych, należy używać istniejącego urządzenia sieci VPN.
* Bazy danych w każdej lokalizacji powinny replikowane do innych lokalizacji Azure raz dziennie.

Na podstawie tych wymagań, można użyć jednej podsieci na warstwie aplikacji i użycia grup NSG do filtrowania ruchu na aplikację. Dzięki temu wystarczy tylko 3 podsieci w każdej sieci wirtualnej (frontonu, warstwy aplikacji i warstwą danych) i jeden NSG na aplikację w każdej podsieci. W takim przypadku należy rozważyć przy użyciu **jednej podsieci na warstwie aplikacji, grup NSG dla aplikacji** wzorzec projektowy. Na poniższej ilustracji pokazano sposób użycia reprezentująca wzorzec projektowania **ProdBU1US1** sieci wirtualnej.

![W jednej podsieci na warstwę, co grupa NSG dla aplikacji w warstwie](./media/virtual-network-vnet-plan-design-arm/figure11.png)

Jednak należy utworzyć dodatkowe podsieci dla połączenia sieci VPN między sieciami wirtualnymi i centrów danych z lokalnego. I należy określić przestrzeń adresów dla każdej podsieci. Na poniższym rysunku przedstawiono przykładowe rozwiązanie dla **ProdBU1US1** sieci wirtualnej. W tym scenariuszu zostanie zreplikowany dla każdej sieci wirtualnej. Każdy kolor reprezentuje inną aplikację.

![Przykład sieci wirtualnej](./media/virtual-network-vnet-plan-design-arm/figure10.png)

**Kontrola dostępu**

Poniższe wymagania dotyczą kontrola dostępu:

* Grupa sieci firmowej powinna mieć pełną kontrolę nad konfigurację sieci wirtualnej.
* Deweloperzy w poszczególnych jednostek biznesowych tylko powinna być mógł wdrożyć maszyn wirtualnych do istniejących podsieci.

Na podstawie tych wymagań, można dodać użytkowników z sieci zespołu do wbudowanych **współautora sieci** roli w każdej subskrypcji; i utworzyć niestandardową rolę dla deweloperów aplikacji w każdej subskrypcji, zapewniając im prawa do dodawania maszyn wirtualnych do istniejących podsieci.

## <a name="next-steps"></a>Kolejne kroki
* [Wdrażanie sieci wirtualnej](quick-create-portal.md).
* Zrozumienie sposobu [równoważenia obciążenia](../load-balancer/load-balancer-overview.md) maszyny wirtualne IaaS i [Zarządzanie routingu w wielu regionach platformy Azure](../traffic-manager/traffic-manager-overview.md).
* Dowiedz się więcej o [sieciowej grupy zabezpieczeń](security-overview.md) rozwiązania NSG.
* Dowiedz się więcej o sieci [między lokalizacjami oraz opcji łączności sieci wirtualnej](../vpn-gateway/vpn-gateway-about-vpngateways.md#s2smulti).
