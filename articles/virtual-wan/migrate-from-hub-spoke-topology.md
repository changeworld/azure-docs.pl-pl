---
title: 'Architektura: migracja do wirtualnej sieci WAN platformy Azure'
description: Dowiedz się, jak przeprowadzić migrację do wirtualnej sieci WAN platformy Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 8aa4fe143c78d2053ce8c48e4866a5522057aa0c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063025"
---
# <a name="migrate-to-azure-virtual-wan"></a>Migrowanie do wirtualnej sieci WAN platformy Azure

Wirtualna sieć WAN platformy Azure umożliwia firmom uproszczenie globalnej łączności w celu skorzystania ze skali globalnej sieci firmy Microsoft. Ten artykuł zawiera szczegóły techniczne dla firm, które chcą przeprowadzić migrację z istniejącej topologii koncentratora i szprychy zarządzanej przez klienta do projektu, który wykorzystuje zarządzane przez firmę Microsoft wirtualne centra sieci WAN.

Aby uzyskać informacje na temat korzyści, jakie wirtualna sieć WAN platformy Azure umożliwia przedsiębiorstwom przyjęcie nowoczesnej globalnej sieci przedsiębiorstwa zorientowanej na chmurę, zobacz [Architektura globalnej sieci tranzytowej i wirtualna sieć WAN.](virtual-wan-global-transit-network-architecture.md)

![centrum i](./media/migrate-from-hub-spoke-topology/hub-spoke.png)
szprycha**Rysunek: Wirtualna sieć WAN platformy Azure**

Model łączności koncentratoru i szprychy wirtualnego centrum danych platformy Azure (VDC) został przyjęty przez tysiące naszych klientów w celu wykorzystania domyślnego zachowania routingu przechodniego usługi Azure Networking w celu tworzenia prostych i skalowalnych sieci w chmurze. Wirtualna sieć WAN platformy Azure opiera się na tych koncepcjach i wprowadza nowe funkcje, które umożliwiają globalną topologię łączności, nie tylko między lokalizacjami lokalnymi a platformą Azure, ale także umożliwiają klientom wykorzystanie skali sieci Firmy Microsoft w celu zwiększenia ich istniejących sieci globalnych.

W tym artykule pokazano, jak przeprowadzić migrację istniejącego środowiska hybrydowego do wirtualnej sieci WAN.

## <a name="scenario"></a>Scenariusz

Contoso to globalna organizacja finansowa z biurami w Europie i Azji. Planują przenieść swoje istniejące aplikacje z lokalnego centrum danych na platformę Azure i stworzyli projekt fundamentu oparty na architekturze VDC, w tym regionalne sieci wirtualne centrum zarządzane przez klienta dla łączności hybrydowej. W ramach przejścia na technologie oparte na chmurze zespół sieci ma za zadanie zapewnić, że ich łączność jest zoptymalizowana pod kątem rozwoju firmy.

Na poniższej ilustracji przedstawiono widok wysokiego poziomu istniejącej sieci globalnej, w tym łączność z wieloma regionami platformy Azure.

![Contoso istniejąca](./media/migrate-from-hub-spoke-topology/contoso-pre-migration.png)
topologia sieci**Rysunek: Contoso istniejąca topologia sieci**

Z istniejącej topologii sieci można zrozumieć następujące punkty:

- Topologia koncentratora i szprychy jest używana w wielu regionach, w tym w obwodach usługi ExpressRoute Premium, aby uzyskać łączność z powrotem do wspólnej prywatnej sieci WAN.

- Niektóre z tych witryn mają również tunele sieci VPN bezpośrednio na platformie Azure, aby dotrzeć do aplikacji hostowanych w chmurze firmy Microsoft.

## <a name="requirements"></a>Wymagania

Zespół sieciowy ma za zadanie dostarczyć globalny model sieci, który może obsługiwać migrację Contoso do chmury i musi optymalizować w obszarach kosztów, skali i wydajności. Podsumowując, należy spełnić następujące wymagania:

- Zapewnij zarówno centralę (HQ), jak i oddziały z zoptymalizowaną ścieżką do aplikacji hostowanych w chmurze.
- Usuń poleganie na istniejących lokalnych centrach danych (DC) w celu zakończenia sieci VPN, zachowując następujące ścieżki łączności:
  - **Branch -to- VNet:** Biura połączone z siecią VPN muszą mieć dostęp do aplikacji zmigrowanych do chmury w lokalnym regionie platformy Azure.
  - **Oddział -to- Hub -to- Hub -to- Sieć wirtualna:** biura połączone z siecią VPN muszą mieć dostęp do aplikacji zmigrowanych do chmury w zdalnym regionie platformy Azure.
  - **Oddział -to- oddział:** Regionalne biura połączone vpn muszą być w stanie komunikować się ze sobą i usługi ExpressRoute połączone z centralą/kontrolerem domeny.
  - **Oddział -to- Hub -to- Hub -to- oddział:** Globalnie oddzielone biura sieci VPN muszą być w stanie komunikować się ze sobą i dowolnymi witrynami połączonymi z centralą/kontrolerem domeny połączonymi z usługą ExpressRoute.
  - **Oddział -to- Internet:** Połączone witryny muszą być w stanie komunikować się z Internetem. Ten ruch musi być filtrowany i rejestrowany.
  - **Sieć wirtualna -to- Sieć wirtualna:** Sieci wirtualne szprychowe w tym samym regionie muszą być w stanie komunikować się ze sobą.
  - **Sieć wirtualna -to- Hub -to- Hub -to- Sieć wirtualna:** Sieci wirtualne szprychowe w różnych regionach muszą być w stanie komunikować się ze sobą.
- Zapewnij użytkownikom mobilnym contoso dostęp do zasobów firmy, a nie w sieci firmowej, użytkownicy mobilni (laptop i telefon) mogą uzyskać dostęp do zasobów firmy.

## <a name="azure-virtual-wan-architecture"></a><a name="architecture"></a>Architektura wirtualnej sieci WAN platformy Azure

Na poniższej ilustracji przedstawiono widok wysokiego poziomu zaktualizowanej topologii docelowej przy użyciu wirtualnej sieci WAN platformy Azure w celu spełnienia wymagań opisanych w poprzedniej sekcji.

![Contoso wirtualna](./media/migrate-from-hub-spoke-topology/vwan-architecture.png)
architektura sieci WAN**Rysunek: Architektura wirtualnej sieci WAN platformy Azure**

Podsumowanie:

- Centrala w Europie pozostaje połączona z usługą ExpressRoute, lokalny kontroler domeny w Europie jest w pełni migrowany na platformę Azure i obecnie likwidowany.
- Azja DC i HQ pozostają połączone z prywatną siecią WAN. Wirtualna sieć WAN platformy Azure jest teraz używana do rozszerzania sieci lokalnych operatorów i zapewniania globalnej łączności.
- Centra wirtualnej sieci WAN platformy Azure wdrożone zarówno w europie zachodniej, jak i w azji południowo-wschodniej w celu zapewnienia centrum łączności dla urządzeń połączonych z usługą ExpressRoute i VPN.
- Koncentratory zapewniają również zakończenie sieci VPN dla użytkowników korzystających z roamingu w wielu typach klientów przy użyciu łączności OpenVPN z globalną siecią siatki, umożliwiając dostęp nie tylko do aplikacji zmigrowanych na platformę Azure, ale także do wszelkich zasobów pozostałych lokalnie.
- Łączność z Internetem dla zasobów w sieci wirtualnej udostępnianej przez wirtualną sieć WAN platformy Azure.

Łączność z Internetem dla witryn zdalnych również świadczona przez wirtualną sieć WAN platformy Azure. Lokalny internet breakout obsługiwany przez integrację z partnerami w celu optymalizacji dostępu do usług SaaS, takich jak Office 365.

## <a name="migrate-to-virtual-wan"></a>Migrowanie do usługi Virtual WAN

W tej sekcji przedstawiono różne kroki migracji do wirtualnej sieci WAN platformy Azure.

### <a name="step-1-vdc-hub-and-spoke-single-region"></a>Krok 1: VDC hub-and-spoke pojedynczy region

Przejrzyj architekturę. Na poniższej ilustracji przedstawiono topologię pojedynczego regionu dla firmy Contoso przed wdrożeniem wirtualnej sieci WAN platformy Azure:

![Topologia](./media/migrate-from-hub-spoke-topology/figure1.png)
pojedynczego regionu**Rysunek 1: VDC hub-and-spoke pojedynczy region**

Zgodnie z podejściem Virtual Data Center (VDC) sieć wirtualna centrum zarządzanego przez klienta zawiera kilka bloków funkcji:

- Usługi współdzielone (każda wspólna funkcja wymagana przez wiele szprych). Przykład: Contoso używa kontrolerów domeny systemu Windows Server na maszynach wirtualnych Infrastruktury jako usługi (IaaS).
- Usługi zapory IP/Routing są świadczone przez wirtualne urządzenie sieciowe innej firmy, umożliwiając routing IP typu "szprycha do szprychy".
- Usługi transferu ruchu internetowego/wychodzące, w tym usługa Azure Application Gateway dla przychodzących żądań HTTPS i usługi proxy innych firm uruchomione na maszynach wirtualnych w celu filtrowania wychodzącego dostępu do zasobów internetowych.
- Brama sieci wirtualnej usługi ExpressRoute i VPN do łączności z sieciami lokalnymi.

### <a name="step-2-deploy-virtual-wan-hubs"></a>Krok 2: Wdrażanie wirtualnych koncentratorów sieci WAN

Wdrażanie centrum wirtualnej sieci WAN w każdym regionie. Skonfiguruj centrum wirtualnej sieci WAN z bramą sieci VPN i bramą usługi ExpressRoute zgodnie z opisem w następujących artykułach:

- [Samouczek: tworzenie połączenia lokacja-lokacja przy użyciu usługi Azure Virtual WAN](virtual-wan-site-to-site-portal.md)
- [Samouczek: Tworzenie skojarzenia usługi ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure](virtual-wan-expressroute-portal.md)

> [!NOTE]
> Wirtualna sieć WAN platformy Azure musi używać standardowej jednostki SKU, aby włączyć niektóre ścieżki ruchu pokazane w tym artykule.

![Wdrażanie wirtualnych](./media/migrate-from-hub-spoke-topology/figure2.png)
koncentratorów sieci WAN**Rysunek 2: koncentrator i rozmowa z wirtualną siecią WAN**

### <a name="step-3-connect-remote-sites-expressroute-and-vpn-to-virtual-wan"></a>Krok 3: Łączenie witryn zdalnych (Usługi ExpressRoute i VPN) z wirtualną siecią WAN

Podłącz koncentrator wirtualnej sieci WAN do istniejących obwodów usługi ExpressRoute i skonfiguruj sieci VPN lokacja lokacja przez Internet do dowolnych oddziałów zdalnych.

> [!NOTE]
> Obwody tras ekspresowych muszą zostać uaktualnione do typu jednostki SKU premium, aby połączyć się z koncentratorem wirtualnej sieci WAN.

![Łączenie lokacji zdalnych](./media/migrate-from-hub-spoke-topology/figure3.png)
z wirtualną siecią WAN**Rysunek 3: koncentrator i rozmowa z wirtualną siecią WAN**

W tym momencie lokalne urządzenia sieciowe zaczną odbierać trasy odzwierciedlające przestrzeń adresową IP przypisaną do wirtualnej sieci wirtualnej w sieci wirtualnej w sieci wirtualnej zarządzanej przez sieć wirtualną WAN. Zdalne oddziały połączone z siecią VPN na tym etapie zobaczą dwie ścieżki do istniejących aplikacji w sieciach wirtualnych szprych. Urządzenia te powinny być skonfigurowane tak, aby nadal korzystać z tunelu do centrum VDC, aby zapewnić routing symetryczny w fazie przejścia.

### <a name="step-4-test-hybrid-connectivity-via-virtual-wan"></a>Krok 4: Testowanie łączności hybrydowej za pośrednictwem wirtualnej sieci WAN

Przed użyciem zarządzanego koncentratora wirtualnej sieci WAN dla łączności produkcyjnej zaleca się skonfigurowanie sieci wirtualnej szprychy testowej i połączenia wirtualnej sieci wirtualnej WAN. Sprawdź, czy połączenia z tym środowiskiem testowym działają za pośrednictwem usługi ExpressRoute i sieci VPN lokacji do witryny przed kontynuowaniem kolejnych kroków.

![Testowanie łączności hybrydowej](./media/migrate-from-hub-spoke-topology/figure4.png)
za pośrednictwem wirtualnej sieci WAN**Rysunek 4: VDC hub-and-spoke do wirtualnej migracji WAN**

### <a name="step-5-transition-connectivity-to-virtual-wan-hub"></a>Krok 5: Przejście łączności do wirtualnego koncentratora sieci WAN

![Łączność przejściowa z](./media/migrate-from-hub-spoke-topology/figure5.png)
wirtualnym koncentratorem sieci WAN**Rysunek 5: koncentrator i rozmowa z wirtualną siecią WAN**

**a**. Usuń istniejące połączenia komunikacji równorzędnej z sieci wirtualnych Szprychy do starego centrum VDC. Dostęp do aplikacji w sieciach wirtualnych szprych jest niedostępny do momentu zakończenia czynności a-c.

**b**. Połącz sieci wirtualne szprychy z koncentratorem wirtualnej sieci WAN za pośrednictwem połączeń sieci wirtualnej.

**c**. Usuń wszystkie trasy zdefiniowane przez użytkownika (UDR) wcześniej używane w sieciach wirtualnych szprych do komunikacji szprychowej. Ta ścieżka jest teraz włączona przez routing dynamiczny dostępny w centrum wirtualnej sieci WAN.

**d**. Istniejące bramy usługi ExpressRoute i SIECI VPN w centrum VDC są teraz likwidowane, aby umożliwić następny krok (e).

**e**. Połącz stary koncentrator VDC (hub virtual network) z koncentratorem wirtualnej sieci WAN za pośrednictwem nowego połączenia sieci wirtualnej.

### <a name="step-6-old-hub-becomes-shared-services-spoke"></a>Krok 6: Stare centrum staje się współdzielone usługi mówił

Teraz przeprojektowaliśmy naszą sieć platformy Azure, aby wirtualne centrum sieci WAN stało się centralnym punktem naszej nowej topologii.

![Stary koncentrator staje](./media/migrate-from-hub-spoke-topology/figure6.png)
się shared services mówił**Rysunek 6: VDC hub-and-spoke do wirtualnej migracji WAN**

Ponieważ centrum wirtualnej sieci WAN jest jednostką zarządzaną i nie zezwala na wdrażanie zasobów niestandardowych, takich jak maszyny wirtualne, blok usług udostępnionych istnieje teraz jako sieć wirtualna szprychowa i obsługuje funkcje, takie jak przychodzące dane przychodzące do Internetu za pośrednictwem usługi Azure Application Gateway lub zwirtualizowanego urządzenia sieciowego. Ruch między środowiskiem usług udostępnionych i maszynami wirtualnymi zaplecza przechodzi teraz przez centrum zarządzane przez wirtualną sieć WAN.

### <a name="step-7-optimize-on-premises-connectivity-to-fully-utilize-virtual-wan"></a>Krok 7: Optymalizacja łączności lokalnej w celu pełnego wykorzystania wirtualnej sieci WAN

Na tym etapie contoso najczęściej zakończył migracje aplikacji biznesowych w chmurze Microsoft Cloud, tylko kilka starszych aplikacji pozostałych w lokalnym kontrolerze domeny.

![Optymalizacja łączności lokalnej w celu pełnego wykorzystania](./media/migrate-from-hub-spoke-topology/figure7.png)
wirtualnej sieci WAN**Rysunek 7: VDC hub-and-spoke do wirtualnej migracji WAN**

Aby wykorzystać pełną funkcjonalność wirtualnej sieci WAN platformy Azure, firma Contoso postanawia zlikwidować ich starsze połączenia sieci VPN. Wszystkie gałęzie kontynuujące dostęp do sieci HQ lub DC mogą przesyłać dane z globalnej sieci firmy Microsoft przy użyciu wbudowanego routingu tranzytowego wirtualnej sieci WAN platformy Azure.

> [!NOTE]
> Usługa ExpressRoute Global Reach to alternatywny wybór dla klientów, którzy chcą wykorzystać szkielet firmy Microsoft w celu uzupełnienia istniejących prywatnych sieci WAN.

## <a name="end-state-architecture-and-traffic-paths"></a>Architektura stanu końcowego i ścieżki ruchu

![Architektura stanu końcowego](./media/migrate-from-hub-spoke-topology/figure8.png)
i ścieżki ruchu**Rysunek: Dwuregionowa wirtualna sieć WAN**

Ta sekcja zawiera podsumowanie, jak ta topologia spełnia oryginalne wymagania, patrząc na niektóre przykładowe przepływy ruchu.

### <a name="path-1"></a>Ścieżka 1

Ścieżka 1 pokazuje przepływ ruchu z połączonej gałęzi sieci VPN S2S w Azji do sieci wirtualnej platformy Azure w regionie Azji Południowo-Wschodniej.

Ruch jest kierowany w następujący sposób:

- Oddział w Azji jest połączony za pomocą odpornych tuneli obsługujących protokół S2S BGP do wirtualnego centrum sieci WAN w Azji Południowo-Wschodniej.

- Węzeł Azji Wirtualna sieć WAN kieruje ruch lokalnie do połączonej sieci wirtualnej.

![Przepływ 1](./media/migrate-from-hub-spoke-topology/flow1.png)

### <a name="path-2"></a>Ścieżka 2

Ścieżka 2 pokazuje przepływ ruchu z usługi ExpressRoute połączone europejskiej centrali do sieci wirtualnej platformy Azure w regionie Azji Południowo-Wschodniej.

Ruch jest kierowany w następujący sposób:

- Europejska centrala centrala jest połączona za pośrednictwem obwodu premium ExpressRoute do wirtualnego centrum WAN w Europie Zachodniej.

- Wirtualna globalna łączność w sieci WAN z koncentratorem do koncentratora umożliwia przesyłanie ruchu do sieci wirtualnej połączonej w regionie zdalnym.

![Przepływ 2](./media/migrate-from-hub-spoke-topology/flow2.png)

### <a name="path-3"></a>Ścieżka 3

Ścieżka 3 pokazuje przepływ ruchu z lokalnego kontrolera domeny w Azji podłączonego do prywatnej sieci WAN do oddziału połączonego z europejskimi usługami S2S.

Ruch jest kierowany w następujący sposób:

- Firma Asia DC jest podłączona do lokalnego prywatnego przewoźnika WAN.

- Obwód usługi ExpressRoute lokalnie kończy się w prywatnej sieci WAN łączy się z centrum sieci WAN Wirtualnej Azji Południowo-Wschodniej.

- Wirtualna globalna łączność w sieci WAN z koncentratorem umożliwia przesyłanie ruchu.

![Przepływ 3](./media/migrate-from-hub-spoke-topology/flow3.png)

### <a name="path-4"></a>Ścieżka 4

Ścieżka 4 pokazuje przepływ ruchu z sieci wirtualnej platformy Azure w regionie Azji Południowo-Wschodniej do sieci wirtualnej platformy Azure w regionie Europy Zachodniej.

Ruch jest kierowany w następujący sposób:

- Wirtualna łączność globalna w sieci WAN hub-to-hub umożliwia natywny tranzyt wszystkich połączonych sieci wirtualnych platformy Azure bez dalszej konfiguracji użytkownika.

![Przepływ 4](./media/migrate-from-hub-spoke-topology/flow4.png)

### <a name="path-5"></a>Ścieżka 5

Ścieżka 5 pokazuje przepływ ruchu z użytkowników mobilnej sieci VPN (P2S) do sieci wirtualnej platformy Azure w regionie Europy Zachodniej.

Ruch jest kierowany w następujący sposób:

- Użytkownicy laptopów i urządzeń mobilnych korzystają z klienta OpenVPN w celu uzyskania przejrzystej łączności z bramą sieci VPN P2S w Europie Zachodniej.

- Centrum wirtualnej sieci WAN w Europie Zachodniej kieruje ruch lokalnie do połączonej sieci wirtualnej.

![Przepływ 5](./media/migrate-from-hub-spoke-topology/flow5.png)

## <a name="security-and-policy-control-via-azure-firewall"></a>Kontrola zabezpieczeń i zasad za pośrednictwem zapory platformy Azure

Firma Contoso potwierdziła teraz łączność między wszystkimi gałęziami i sieciami wirtualnymi zgodnie z wymaganiami omówione wcześniej w tym artykule. Aby spełnić ich wymagania dotyczące kontroli zabezpieczeń i izolacji sieci, muszą nadal oddzielać i rejestrować ruch za pośrednictwem sieci koncentratora. Poprzednio ta funkcja była wykonywana przez wirtualne urządzenie sieciowe (NVA). Contoso chce również zlikwidować istniejące usługi proxy i korzystać z natywnych usług platformy Azure do wychodzącego filtrowania Internetu.

![Kontrola zabezpieczeń i zasad](./media/migrate-from-hub-spoke-topology/security-policy.png)
za pomocą figurki zapory**platformy Azure: Zapora azure w wirtualnej sieci WAN (zabezpieczony koncentrator wirtualny)**

Następujące kroki wysokiego poziomu są wymagane do wprowadzenia zapory azure do wirtualnych centrów sieci WAN, aby włączyć ujednoliconą kontrolę punktu zasad. Aby uzyskać więcej informacji na temat tego procesu i koncepcji bezpiecznych centrów wirtualnych, zobacz [Usługa Azure Firewall Manager](../firewall-manager/index.yml).

1. Utwórz zasady zapory platformy Azure.
2. Połącz zasady zapory z centrum wirtualnej sieci WAN platformy Azure. Ten krok umożliwia istniejącemu wirtualnemu centrum sieci WAN działanie jako zabezpieczone centrum wirtualne i wdraża wymagane zasoby zapory platformy Azure.

> [!NOTE]
> Jeśli zapora azure jest wdrażana w centrum standardowej wirtualnej sieci WAN (SKU : Standard): zasady V2V, B2V, V2I i B2I FW są wymuszane tylko w ruchu pochodzącym z sieci wirtualnych i gałęzi połączonych z określonym koncentratorem, w którym jest wdrażana usługa Azure FW (Centrum zabezpieczane). Ruch pochodzący ze zdalnych sieci wirtualnych i gałęzi, które są dołączone do innych koncentratorów wirtualnej sieci WAN w tej samej wirtualnej sieci WAN, nie będzie "zaporowy", nawet jeśli zdalne gałęzie i sieć wirtualna są połączone za pośrednictwem koncentratora wirtualnej sieci WAN z łączami koncentratorowymi. Obsługa zapory między centrum znajduje się na mapie drogowej usługi Azure Virtual WAN i Firewall Manager.

Następujące ścieżki pokazują ścieżki łączności włączone przy użyciu usług wirtualnych zabezpieczonych platformy Azure:

### <a name="path-6"></a>Ścieżka 6

Ścieżka 6 pokazuje bezpieczny przepływ ruchu między sieciami wirtualnymi w tym samym regionie.

Ruch jest kierowany w następujący sposób:

- Sieci wirtualne połączone z tym samym zabezpieczonym centrum wirtualnym kierują teraz ruch za pośrednictwem zapory azure.

- Zapora azure można zastosować zasady do tych przepływów.

![Przepływ 6](./media/migrate-from-hub-spoke-topology/flow6.png)

### <a name="path-7"></a>Ścieżka 7

Ścieżka 7 pokazuje przepływ ruchu z sieci wirtualnej platformy Azure do Internetu lub usługi zabezpieczeń innej firmy.

Ruch jest kierowany w następujący sposób:

- Sieci wirtualne połączone z bezpiecznym centrum wirtualnym mogą wysyłać ruch do publicznych miejsc docelowych w Internecie, używając bezpiecznego centrum jako centralnego punktu dostępu do Internetu.

- Ten ruch można filtrować lokalnie przy użyciu reguł FQDN zapory azure lub wysyłane do usługi zabezpieczeń innej firmy w celu sprawdzenia.

![Przepływ 7](./media/migrate-from-hub-spoke-topology/flow7.png)

### <a name="path-8"></a>Ścieżka 8

Ścieżka 8 pokazuje przepływ ruchu z gałęzi do Internetu lub usługi zabezpieczeń innych firm.

Ruch jest kierowany w następujący sposób:

- Gałęzie połączone z centrum bezpiecznego wirtualnego mogą wysyłać ruch do miejsc docelowych publicznych w Internecie za pomocą Bezpiecznego centrum jako centralnego punktu dostępu do Internetu.

- Ten ruch można filtrować lokalnie przy użyciu reguł FQDN zapory azure lub wysyłane do usługi zabezpieczeń innej firmy w celu sprawdzenia.

![Przepływ 8](./media/migrate-from-hub-spoke-topology/flow8.png) 

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [wirtualnej sieci WAN platformy Azure](virtual-wan-about.md)
