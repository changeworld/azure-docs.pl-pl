---
title: Kontrolowanie ruchu związanego z ruchem przychodzącym w Australii platformy Azure
description: Przewodnik dotyczący kontrolowania ruchu związanego z ruchem przychodzącym w Australii platformy Azure w celu spełnienia wymogów australijskiego rządu dla bezpiecznych bram internetowych
author: galey801
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: grgale
ms.openlocfilehash: 808a615885129af1be9b7fdcdb64d5a8c5a25e40
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68571655"
---
# <a name="controlling-ingress-traffic-in-azure-australia"></a>Kontrolowanie ruchu związanego z ruchem przychodzącym w Australii platformy Azure

Podstawowym elementem zabezpieczania systemów ICT jest kontrolowanie ruchu w sieci. Ruch powinien być ograniczony tylko do tego, co jest niezbędne, aby system mógł działać, aby zmniejszyć ryzyko złamania.

Ten przewodnik zawiera szczegółowe informacje o sposobie działania ruchu sieciowego ruchu przychodzącego na platformie Azure oraz Zalecenia dotyczące implementowania kontroli zabezpieczeń sieci dla systemu połączonego z Internetem.

Kontrolki sieci są wyrównane do wskazówek dotyczących konsumentów cybernetycznymi Security Centre (ACSC) oraz zamiaru podręcznika dotyczącego bezpieczeństwa informacji ACSC (ISM).

## <a name="requirements"></a>Wymagania

Ogólne wymagania dotyczące zabezpieczeń systemów Wspólnoty są zdefiniowane w ISM. Aby pomóc podmiotom Wspólnoty w wdrażaniu zabezpieczeń sieci, ACSC opublikowała [ochronę ACSC: Implementacja segmentacji i](https://www.acsc.gov.au/publications/protect/network_segmentation_segregation.htm)rozdzielania sieci oraz ułatwianie zabezpieczania systemów w środowiskach w chmurze ACSC opublikowała [zabezpieczenia chmury obliczeniowej dla dzierżawców](https://www.acsc.gov.au/publications/protect/Cloud_Computing_Security_for_Tenants.pdf).

Te przewodniki przedstawiają kontekst implementowania zabezpieczeń sieci i kontrolowania ruchu oraz obejmują praktyczne zalecenia dotyczące projektowania i konfigurowania sieci.

Zabezpieczenia w [chmurze obliczeniowej](https://aka.ms/au-irap) firmy Microsoft dla dzierżawców przewodnika Microsoft Azure na stronie australijskiej portalu zaufania usługi wyróżniają konkretne technologie firmy Microsoft, które umożliwiają spełnienie porad w publikacjach ACSC.

Następujące kluczowe wymagania, które zostały zidentyfikowane w publikacjach z ACSC, są ważne w przypadku kontrolowania ruchu przychodzącego na platformie Azure:

|Opis|Source|
|---|---|
|**Implementacja segmentacji sieci i rozdzielenia, na przykład architektury n-warstwowej, przy użyciu zapór opartych na hoście i kontroli dostępu do sieci dostawcy usług kryptograficznych, aby ograniczyć przychodzące i wychodzące połączenia sieciowe maszyny wirtualnej tylko do wymaganych portów/protokołów.**| _Przetwarzanie w chmurze dla dzierżawców_|
|**Implementowanie odpowiednio dużej przepustowości, małych opóźnień i niezawodnej łączności sieciowej** między dzierżawcą (łącznie z użytkownikami zdalnymi dzierżawy) i usługą w chmurze w celu spełnienia wymagań dotyczących dostępności dzierżawy  | _Przetwarzanie w chmurze dla dzierżawców_|
|**Zastosuj technologie tylko w warstwie sieciowej**. Każdy host i sieć powinny być poddzielone i segregowane, tam gdzie to możliwe, na najniższym poziomie, który może być praktycznie zarządzany. W większości przypadków segmentacja i rozdzielenie stosują się od warstwy linku danych do warstwy aplikacji i wraz z nią. Jednak w środowiskach wrażliwych może być odpowiednia izolacja fizyczna. Miary oparte na hoście i całej sieci należy wdrażać w sposób uzupełniający i monitorowany centralnie. Używanie zapory lub urządzenia zabezpieczeń jako jedynego środka zabezpieczeń nie jest wystarczające. |_ACSC ochronę: Implementowanie segmentacji sieci i segregowania_|
|**Skorzystaj z zasad o najniższych uprawnieniach i potrzebujesz do lokalnych do do lokalnych**. Jeśli host, usługa lub sieć nie musi komunikować się z innym hostem, usługą lub siecią, nie powinno być dozwolone. Jeśli host, usługa lub Sieć musi komunikować się tylko z innym hostem, usługą lub siecią przy użyciu określonych portów lub protokołów, należy wyłączyć wszystkie inne porty lub protokoły. Zastosowanie tych zasad w sieci spowoduje uzupełnienie minimalnych uprawnień użytkowników i znacząco zwiększy ogólną stan bezpieczeństwa środowiska. |_ACSC ochronę: Implementowanie segmentacji sieci i segregowania_|
|**Oddzielne hosty i sieci na podstawie ich czułości lub znaczenia dla operacji działania biznesowego**. Rozdzielenie można uzyskać przy użyciu innego sprzętu lub platform w zależności od różnych klasyfikacji zabezpieczeń, domen zabezpieczeń lub wymagań dotyczących dostępności/integralności dla określonych hostów lub sieci. W szczególności oddziel sieci zarządzania i rozważ fizyczne izolowanie sieci zarządzania poza pasmem w środowiskach poufnych. |_ACSC ochronę: Implementowanie segmentacji sieci i segregowania_|
|**Identyfikuj, uwierzytelniaj i Autoryzuj dostęp przez wszystkie jednostki do wszystkich innych jednostek**. Dostęp do wszystkich użytkowników, hostów i usług powinien być ograniczony tylko do innych użytkowników, hostów i usług wymaganych do wyznaczonych obowiązków lub funkcji. Wszystkie starsze lub lokalne usługi, które pomijają lub obniżają siłę identyfikacji, uwierzytelniania i autoryzacji, powinny być wyłączone, a ich użycie powinna być ściśle monitorowana. |_ACSC ochronę: Implementowanie segmentacji sieci i segregowania_|
|**Zaimplementuj opcję Zezwól na listę ruchu sieciowego zamiast listy Odmów**. Zezwalaj na dostęp tylko dla znanego dobrego ruchu sieciowego (tzn. identyfikowanego, uwierzytelnianego i autoryzowanego), a nie Odmawiaj dostępu do znanego nieprawidłowego ruchu sieciowego (na przykład blokującego określony adres lub usługę). Listy dozwolonych skutkuje wyższymi zasadami zabezpieczeń w celu odlistowania i znacznego zwiększenia zdolności organizacji do wykrywania i oceniania potencjalnych intruzów w sieci. |_ACSC ochronę: Implementowanie segmentacji sieci i segregowania_|
|

Ten artykuł zawiera informacje i zalecenia dotyczące tego, jak te wymagania mogą być spełnione w przypadku systemów wdrożonych na platformie Azure przy użyciu zarówno infrastruktury jako usługi (IaaS), jak i platformy jako usługi (PaaS). Należy również zapoznać się z artykułem dotyczącym [kontrolowania ruchu wychodzącego w Australii platformy Azure](gateway-egress-traffic.md) , aby w pełni zrozumieć kontrolę nad ruchem sieciowym na platformie Azure.

## <a name="architecture"></a>Architektura

Jeśli chcesz zaprojektować lub zaimplementować kontrolę ruchu sieciowego i transferu danych przychodzących, należy najpierw zrozumieć, w jaki sposób ruch sieciowy odbywa się na platformie Azure zarówno w IaaS, jak i w PaaS. Ta sekcja zawiera omówienie możliwych punktów wejścia, w których ruch sieciowy może nawiązać połączenie z zasobem hostowanym na platformie Azure, oraz kontrolami zabezpieczeń dostępnymi w celu ograniczenia i kontrolowania tego ruchu. Każdy z tych składników został szczegółowo omówiony w pozostałych sekcjach tego przewodnika.

### <a name="architecture-components"></a>Składniki architektury

Przedstawiony tutaj diagram architektury przedstawia możliwe ścieżki, które mogą być wykonywane przez ruch sieciowy w celu nawiązania połączenia z usługą hostowaną na platformie Azure. Te składniki są podzielone na platformę Azure, IaaS ruch przychodzący, PaaSy i kontroli zabezpieczeń, w zależności od funkcji zapewnianej dla ruchu przychodzącego.

![Architektura](media/ingress-traffic.png)

### <a name="azure-components"></a>Składniki platformy Azure

|Składnik | Opis|
|---|---|
|**DDoS Protection** | Ataki rozproszonego typu "odmowa usługi" (DDoS) podejmują próbę wyczerpania zasobów aplikacji, co sprawia, że aplikacja jest niedostępna dla uprawnionych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu. Platforma Azure oferuje DDoS Protection automatycznie za pomocą platformy Azure i zapewnia dodatkowe możliwości ograniczenia, które mogą być włączone dla określonych aplikacji w celu zapewnienia bardziej szczegółowej kontroli.|
| **Traffic Manager** | Usługa Azure Traffic Manager to moduł równoważenia obciążenia oparty na systemie nazw domen (DNS), który umożliwia optymalne dystrybuowanie ruchu do usług w różnych regionach platformy Azure, zapewniając wysoką dostępność i czas odpowiedzi. Traffic Manager używa systemu DNS do kierowania żądań klientów do najbardziej odpowiedniego punktu końcowego na podstawie metody routingu ruchu i kondycji punktów końcowych.|
| **ExpressRoute** | ExpressRoute to dedykowane połączenie sieciowe do korzystania z usług w chmurze firmy Microsoft. Jest ona obsługiwana przez dostawcę połączenia i oferuje większą niezawodność, większe szybkości, mniejsze opóźnienia i lepsze zabezpieczenia niż typowe połączenia przez Internet. Obwód usługi ExpressRoute reprezentuje połączenie logiczne między infrastrukturą lokalną i usługami w chmurze firmy Microsoft przez dostawcę połączenia.|
| **Prywatna Komunikacja równorzędna ExpressRoute** | Prywatna Komunikacja równorzędna ExpressRoute to połączenie między środowiskiem lokalnym i prywatnymi sieciami wirtualnymi platformy Azure. Prywatna Komunikacja równorzędna umożliwia dostęp do usług platformy Azure, takich jak Virtual Machines, które są wdrażane w ramach sieci wirtualnej. Zasoby i sieci wirtualne dostępne za pośrednictwem prywatnej komunikacji równorzędnej są traktowane jako rozszerzenie sieci podstawowej organizacji. Prywatna Komunikacja równorzędna zapewnia dwukierunkową łączność między siecią lokalną i sieciami wirtualnymi platformy Azure przy użyciu prywatnych adresów IP.|
| **ExpressRoute komunikacji równorzędnej firmy Microsoft** | ExpressRoute Komunikacja równorzędna firmy Microsoft to połączenie między środowiskiem lokalnym a usługami publicznymi firmy Microsoft i platformy Azure. Obejmuje to łączność z pakietami Office 365, Dynamics 365 i Azure PaaS Services. Komunikacja równorzędna jest ustanawiana za pośrednictwem publicznych adresów IP należących do organizacji lub dostawcy łączności. Żadne usługi nie są dostępne za pośrednictwem usługi ExpressRoute Komunikacja równorzędna firmy Microsoft, a organizacja musi zrezygnować z wymaganych usług. Ten proces zapewnia następnie łączność z tymi samymi punktami końcowymi, które są dostępne w Internecie.|
|

### <a name="iaas-ingress-components"></a>IaaS składniki transferu danych przychodzących

|Składnik | Opis|
|---|---|
|**Interfejs sieciowy** | Interfejs sieciowy jest zasobem, który istnieje na platformie Azure. Jest on dołączony do maszyny wirtualnej i ma przypisany prywatny, nieinternetowy adres IP z podsieci, z którą jest skojarzona. Ten adres IP jest dynamicznie lub statycznie przypisywany przez Azure Resource Manager.|
|**Podsieć** | Podsieć jest zakresem adresów IP, który jest tworzony w sieci wirtualnej. Można utworzyć wiele podsieci w sieci wirtualnej dla segmentacji sieci.|
| **Virtual Network (Sieć wirtualna)** | Sieć wirtualna to podstawowe zasoby platformy Azure, które udostępniają platformę i granicę na potrzeby wdrażania zasobów i włączania komunikacji. Sieć wirtualna istnieje w regionie świadczenia usługi Azure i definiuje przestrzeń adresów IP oraz granice routingu dla zintegrowanych zasobów sieci wirtualnej, takich jak Virtual Machines.|
| **Komunikacja równorzędna sieci wirtualnych** | Komunikacja równorzędna sieci wirtualnych jest opcją konfiguracji platformy Azure, która umożliwia bezpośrednią komunikację między dwoma sieci wirtualnych bez konieczności stosowania bramy Virtual Network. Po połączeniu komunikacji równorzędnej dwie sieci wirtualnych mogą komunikować się bezpośrednio, a dodatkowa konfiguracja może kontrolować korzystanie z bram Virtual Network i innych opcji tranzytu.|
| **Publiczny adres IP** | Publiczny adres IP jest zasobem, który rezerwuje jeden z publicznych adresów IP należących do firmy Microsoft w określonym regionie do użycia w sieci wirtualnej. Można go skojarzyć z określonym interfejsem sieciowym, co umożliwia dostęp do tego zasobu z systemów internetowych, ExpressRoute i PaaS.|
| **Brama ExpressRoute** | Brama ExpressRoute jest obiektem w Virtual Network zapewnia łączność i routing z Virtual Network do sieci lokalnych za pośrednictwem prywatnej komunikacji równorzędnej w obwodzie usługi ExpressRoute.|
| **VPN Gateway** | VPN Gateway to obiekt w Virtual Network, który udostępnia zaszyfrowany tunel z Virtual Network do sieci zewnętrznej. Zaszyfrowany tunel może być typu lokacja-lokacja w przypadku komunikacji dwukierunkowej z środowiskiem lokalnym, inną siecią wirtualną lub środowiskiem chmury lub punktem-lokacją w celu komunikacji z jednym punktem końcowym.|
| **Integracja z siecią wirtualną PaaS** | Wiele możliwości PaaS można wdrożyć w programie lub zintegrować z Virtual Network. Niektóre funkcje PaaS mogą być w pełni zintegrowane z siecią wirtualną i być dostępne tylko za pośrednictwem prywatnych adresów IP. Inne, takie jak Azure Load Balancer i Application Gateway platformy Azure, mogą mieć interfejs zewnętrzny z publicznym adresem IP i interfejsem wewnętrznym z prywatnym adresem IP w sieci wirtualnej. W tym wystąpieniu ruch może przetransferować do Virtual Network za pośrednictwem możliwości PaaS.|
|

### <a name="paas-ingress-components"></a>PaaS składniki transferu danych przychodzących

|Składnik | Opis|
|---|---|
|**Nazwa hosta** | Możliwości usługi Azure PaaS są identyfikowane przez unikatową publiczną nazwę hosta, która jest przypisana podczas tworzenia zasobu. Ta nazwa hosta jest następnie zarejestrowana w publicznej domenie DNS, w którym można ją rozpoznać jako publiczny adres IP.|
|**Publiczny adres IP** | O ile nie zostanie wdrożona w konfiguracji zintegrowanej sieci wirtualnej, dostęp do funkcji platformy Azure PaaS odbywa się za pośrednictwem publicznego adresu IP z obsługą Internetu. Ten adres może być przeznaczony dla określonych zasobów, takich jak publiczna Load Balancer, lub może być skojarzony z konkretną możliwością, która ma współużytkowany punkt wejścia dla wielu wystąpień, takich jak Storage lub SQL. Dostęp do tego publicznego adresu IP można uzyskać z Internetu, ExpressRoute lub z publicznych adresów IP IaaS za pośrednictwem sieci szkieletowej platformy Azure.|
|**Punkty końcowe usługi** | Punkty końcowe usługi zapewniają bezpośrednie, prywatne połączenie z Virtual Network do konkretnej możliwości PaaS. Punkty końcowe usługi, które są dostępne tylko dla podzbioru możliwości PaaS, zapewniają zwiększoną wydajność i bezpieczeństwo zasobów w sieci wirtualnej uzyskujących dostęp do PaaS.|
|

### <a name="security-controls"></a>Kontrolki zabezpieczeń

|Składnik | Opis|
|---|---|
|**Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)** | Sieciowych grup zabezpieczeń kontrolować ruch do i z zasobów sieci wirtualnej na platformie Azure. Sieciowych grup zabezpieczeń stosują reguły dla przepływów ruchu, które są dozwolone lub odrzucane, które obejmują ruch na platformie Azure oraz między systemem Azure i sieciami zewnętrznymi, takimi jak lokalne lub internetowe. Sieciowych grup zabezpieczeń są stosowane do podsieci w obrębie sieci wirtualnej lub do poszczególnych interfejsów sieciowych.|
|**Zapora PaaS** | Wiele funkcji PaaS, takich jak Storage i SQL, ma wbudowaną zaporę służącą do kontrolowania ruchu sieciowego związanego z ruchem przychodzącym do określonego zasobu. Można tworzyć reguły zezwalające na połączenia z określonych adresów IP i/lub sieci wirtualnych lub odmawiające tych połączeń.|
|**PaaS uwierzytelniania i Access Control** | W ramach podejścia warstwowego do zabezpieczeń PaaS funkcje zapewniają wiele mechanizmów uwierzytelniania użytkowników i kontrolowania uprawnień i dostępu.|
|**Azure Policy** | Azure Policy to usługa platformy Azure do tworzenia i przypisywania zasad oraz zarządzania nimi. Zasady te służą do kontrolowania typów zasobów, które można wdrożyć, oraz konfiguracji tych zasobów. Zasady mogą służyć do wymuszania zgodności, uniemożliwiając wdrożenie zasobów, jeśli nie spełniają wymagań lub mogą być używane do monitorowania w celu raportowania stanu zgodności.|
|

## <a name="general-guidance"></a>Ogólne wytyczne

Aby zaprojektować i utworzyć bezpieczne rozwiązania na platformie Azure, należy zrozumieć i kontrolować ruch sieciowy w taki sposób, że może wystąpić tylko zidentyfikowana i autoryzowana komunikacja. Celem niniejszych wskazówek i wskazówek dotyczących określonego składnika w kolejnych sekcjach jest opisanie narzędzi i usług, które mogą być wykorzystywane do zastosowania zasad opisanych w _artykule ACSC Protect: Implementowanie segmentacji sieci i podziału_ między obciążeniami platformy Azure. Obejmuje to szczegółowo, jak utworzyć architekturę wirtualną w celu zabezpieczania zasobów, gdy nie jest możliwe stosowanie tych samych tradycyjnych kontroli fizycznych i sieci, które są możliwe w środowisku lokalnym.

### <a name="specific-focus-areas"></a>Określone obszary fokusu

* Ogranicz liczbę punktów wejścia do sieci wirtualnych
* Ogranicz liczbę publicznych adresów IP
* Rozważ użycie sieci typu Hub i szprych dla sieci wirtualnych, zgodnie z opisem w dokumentacji Microsoft Virtual Data Centre (VDC).
* Wykorzystywanie produktów z wbudowanymi funkcjami zabezpieczeń dla połączeń przychodzących z Internetu (na przykład Application Gateway, bramy interfejsu API, sieciowych urządzeń wirtualnych)
* Ogranicz przepływy komunikacji, aby PaaS możliwości tylko do tych, które są niezbędne do funkcjonowania systemu
* Wdrażanie PaaS w konfiguracji zintegrowanej sieci wirtualnej na potrzeby szerszego segregowania i kontroli
* Konfigurowanie systemów do korzystania z mechanizmów szyfrowania zgodnie z zaleceniami konsumentów ACSC i ISM
* Korzystanie z ochrony opartej na tożsamościach, takich jak uwierzytelnianie i kontrola dostępu oparta na rolach, poza tradycyjnymi kontrolkami sieci
* Implementowanie ExpressRoute na potrzeby łączności z sieciami lokalnymi
* Implementowanie sieci VPN na potrzeby ruchu administracyjnego i integracji z sieciami zewnętrznymi
* Wykorzystanie Azure Policy, aby ograniczyć regiony i zasoby tylko do tych, które są niezbędne do korzystania z funkcji systemu
* Wykorzystanie Azure Policy do wymuszenia konfiguracji zabezpieczeń linii bazowej dla zasobów dostępnych w Internecie

### <a name="additional-resources"></a>Dodatkowe zasoby

|Resource | Łącze|
|---|---|
|Udokumentowane dokumenty dotyczące zgodności z przepisami i zasad, w tym wskazówki dla użytkowników|[https://aka.ms/au-irap](https://aka.ms/au-irap)|
|Wirtualne centrum danych Azure|[https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)|
|Segmentacja sieci ACSC|[https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm](https://acsc.gov.au/publications/protect/network_segmentation_segregation.htm)|
|ACSC zabezpieczenia chmury dla dzierżawców| [https://acsc.gov.au/publications/protect/cloud-security-tenants.htm](https://acsc.gov.au/publications/protect/cloud-security-tenants.htm)|
|Podręcznik zabezpieczeń informacji ACSC|[https://acsc.gov.au/infosec/ism/index.htm](https://acsc.gov.au/infosec/ism/index.htm)|

## <a name="component-guidance"></a>Wskazówki dotyczące składników

Ta sekcja zawiera dalsze wskazówki dotyczące poszczególnych składników związanych z ruchem przychodzącym do systemów wdrożonych na platformie Azure. W każdej sekcji opisano intencję określonego składnika z łączami do dokumentacji i przewodników konfiguracji, których można użyć do ułatwienia projektowania i tworzenia działań.

## <a name="azure"></a>Azure

Cała komunikacja z zasobami w ramach platformy Azure odbywa się za pośrednictwem infrastruktury sieci utrzymywanej przez firmę Microsoft, która zapewnia łączność i funkcje zabezpieczeń. Zakres ochrony jest wdrażany automatycznie przez firmę Microsoft w celu ochrony platformy Azure i infrastruktury sieciowej oraz dodatkowych funkcji dostępnych jako usługi na platformie Azure w celu kontrolowania ruchu sieciowego i ustanawiania segmentacji sieci. podział.

### <a name="ddos-protection"></a>DDoS Protection

Zasoby dostępne dla Internetu są podatne na ataki DDoS. Aby chronić przed atakami, platforma Azure zapewnia ochronę DDoS na poziomie podstawowym i standardowym.

Funkcja Basic jest automatycznie włączana w ramach platformy Azure, w tym zawsze włączone monitorowanie ruchu, oraz łagodzenie typowych ataków na poziomie sieci w czasie rzeczywistym, zapewniając te same obronność, które zostały wykorzystane przez Usługi online firmy Microsoft. Cała Skala sieci globalnej platformy Azure może służyć do dystrybucji i ograniczania ruchu ataków między regionami. Zapewniono ochronę dla publicznych adresów IP systemu IPv4 i IPv6

Standard oferuje dodatkowe możliwości ograniczania funkcjonalności w ramach podstawowej warstwy usług, które są dostosowane specjalnie do zasobów usługi Azure Virtual Network. Zasady ochrony są dostosowane przez dedykowane algorytmy monitorowania ruchu i uczenia maszynowego. Podano ochronę dla publicznych adresów IP platformy Azure IPv4.

|Resource|Łącze|
|---|---|
|Przegląd Azure DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview](https://docs.microsoft.com/azure/virtual-network/ddos-protection-overview)|
|Najlepsze rozwiązania dotyczące usługi Azure DDoS|[https://docs.microsoft.com/azure/security/azure-ddos-best-practices](https://docs.microsoft.com/azure/security/azure-ddos-best-practices)|
|Zarządzanie DDoS Protection|[https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection](https://docs.microsoft.com/azure/virtual-network/manage-ddos-protection)|
|

### <a name="traffic-manager"></a>Traffic Manager

Traffic Manager służy do zarządzania ruchem przychodzącym przez kontrolowanie, które punkty końcowe aplikacji odbierają połączenia. Aby chronić przed utratą dostępności systemów lub aplikacji z powodu ataku cybernetycznymi zabezpieczenia lub odzyskiwania usług po naruszeniu systemu, Traffic Manager może służyć do przekierowywania ruchu do działającego, dostępnego wystąpienia aplikacji.

|Resource|Łącze|
|---|---|
|Traffic Manager — omówienie | [https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview)|
|Odzyskiwanie po awarii przy użyciu podręczników Azure DNS i Traffic Manager | [https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager](https://docs.microsoft.com/azure/networking/disaster-recovery-dns-traffic-manager)|
|

### <a name="expressroute"></a>ExpressRoute

ExpressRoute można użyć do nawiązania prywatnej ścieżki ze środowiska lokalnego z systemami hostowanymi na platformie Azure. To połączenie może zapewnić większą niezawodność i gwarantowaną wydajność dzięki zwiększonej prywatności komunikacji sieciowej. Funkcja Express Route umożliwia podmiotom Wspólnoty sterowanie ruchem przychodzącym z środowiska lokalnego i Definiowanie dedykowanych adresów specyficznych dla organizacji na potrzeby reguł zapory dla ruchu przychodzącego i list kontroli dostępu.

|Resource | Łącze|
|---|---|
|Omówienie usługi ExpressRoute | [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)|
|Modele łączności ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models](https://docs.microsoft.com/azure/expressroute/expressroute-connectivity-models)|
|

### <a name="expressroute-private-peering"></a>Prywatna Komunikacja równorzędna ExpressRoute

Prywatna Komunikacja równorzędna zapewnia mechanizm rozszerzania środowiska lokalnego na platformę Azure przy użyciu tylko prywatnych adresów IP. Umożliwia to jednostkom Wspólnoty integrację sieci wirtualnych platformy Azure i zakresów adresów z istniejącymi systemami i usługami lokalnymi. Prywatna Komunikacja równorzędna zapewnia gwarancję, że komunikację między ExpressRouteami jest tylko sieci wirtualne autoryzowane przez organizację. W przypadku korzystania z prywatnej komunikacji równorzędnej jednostki Wspólnoty muszą zaimplementować wirtualne urządzenia sieciowe (urządzenie WUS), a nie VPN Gateway platformy Azure, aby nawiązać bezpieczną komunikację sieci VPN z sieciami lokalnymi zgodnie z wymaganiami klienta ACSC.

|Resource | Łącze|
|---|---|
|Omówienie prywatnej komunikacji równorzędnej ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|Przewodnik po prywatnej komunikacji równorzędnej ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#private)|
|

### <a name="expressroute-microsoft-peering"></a>ExpressRoute komunikacji równorzędnej firmy Microsoft

Komunikacja równorzędna firmy Microsoft zapewnia szybkie i małe opóźnienia w połączeniu z usługami publicznymi firmy Microsoft bez konieczności przechodzenia przez Internet. Zapewnia to większą niezawodność, wydajność i prywatność połączeń. Za pomocą filtrów tras, jednostki Wspólnoty mogą ograniczyć komunikację tylko do regionów świadczenia usługi Azure, których wymagają, ale obejmuje usługi hostowane przez inne organizacje i mogą wymagać dodatkowych funkcji filtrowania lub inspekcji między środowisko lokalne i Microsoft.

Jednostki Wspólnoty mogą korzystać z dedykowanych publicznych adresów IP ustanowionych za pomocą relacji komunikacji równorzędnej w celu jednoznacznego zidentyfikowania środowiska lokalnego do użycia na zaporach i listach kontroli dostępu w ramach możliwości PaaS.

Alternatywnie jednostki Wspólnoty mogą używać komunikacji równorzędnej firmy Microsoft ExpressRoute jako sieci underlay do ustanawiania łączności sieci VPN za pośrednictwem platformy Azure VPN Gateway. W tym modelu nie ma aktywnej komunikacji od wewnętrznej sieci lokalnej do usług publicznych platformy Azure za pośrednictwem usługi ExpressRoute, ale bezpieczne połączenie z prywatnymi sieciami wirtualnymi odbywa się zgodnie ze wskazówkami konsumenta ACSC.

|Resource | Łącze|
|---|---|
|ExpressRoute omówienie komunikacji równorzędnej firmy Microsoft | [https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#routingdomains)|
|ExpressRoute komunikacji równorzędnej firmy Microsoft | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft](https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager#msft)|
|

## <a name="iaas-ingress"></a>IaaS ruch przychodzący

Ta sekcja zawiera wskazówki dotyczące składników służących do kontrolowania ruchu przychodzącego do składników IaaS. IaaS zawiera Virtual Machines i innych zasobów obliczeniowych, które mogą być wdrażane i zarządzane w ramach Virtual Network na platformie Azure. W przypadku ruchu, który ma zostać osiągnięty w systemach wdrożonych przy użyciu IaaS, musi on mieć punkt wejścia do Virtual Network, który można ustanowić za pośrednictwem publicznego adresu IP, bramy Virtual Network lub relacji komunikacji równorzędnej Virtual Network.

### <a name="network-interface"></a>Interfejs sieciowy

Interfejsy sieciowe to punkty wejściowe dla całego ruchu kierowanego do maszyny wirtualnej. Interfejsy sieciowe umożliwiają konfigurację adresowania IP i mogą być używane do stosowania sieciowych grup zabezpieczeń lub do routingu ruchu za pomocą sieciowego urządzenia wirtualnego. Interfejsy sieciowe dla Virtual Machines powinny być planowane i skonfigurowane odpowiednio do dopasowania z ogólną segmentacją sieci i celami podziału.

|Resource | Łącze|
|---|---|
|Tworzenie, zmienianie lub usuwanie interfejsu sieciowego | [https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface)|
|Adresowanie IP interfejsu sieciowego | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)|
|

### <a name="subnet"></a>Subnet

Podsieci to kluczowy składnik służący do segmentacji sieci i podziału na platformę Azure. Podsieci mogą być używane w podobny sposób, aby zapewnić rozdzielenie między systemami. Sieciowych grup zabezpieczeń można zastosować do podsieci w celu ograniczenia przepływów komunikacji przychodzącej tylko do tych, które są niezbędne do funkcjonowania systemu. Podsieci mogą być używane jako adresy źródłowe i docelowe dla reguł zapory oraz list kontroli dostępu i można je skonfigurować dla punktów końcowych usługi, aby zapewnić łączność z możliwościami PaaS.

|Resource | Łącze|
|---|---|
|Dodawanie, zmienianie lub usuwanie podsieci sieci wirtualnej | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)|
|

### <a name="virtual-network-vnet"></a>Sieć wirtualna (VNet)

Sieci wirtualnych to jeden z podstawowych bloków konstrukcyjnych dla sieci na platformie Azure. Sieci wirtualne definiują przestrzeń adresów IP i granicę routingu, które mają być używane w różnych systemach. Sieci wirtualne są podzielone na podsieci, a wszystkie podsieci w ramach Virtual Network mają bezpośrednią trasę sieciową. Za pomocą bram Virtual Network (ExpressRoute lub VPN) systemy w ramach Virtual Network można udostępnić środowisku lokalnym i zewnętrznym. Zrozumienie sieci wirtualnych i skojarzonych z nimi parametrów konfiguracji i routingu ma kluczowe znaczenie w zrozumieniu i kontrolowaniu ruchu sieciowego związanego z ruchem przychodzącym.

|Resource | Łącze|
|---|---|
|Omówienie sieci wirtualnych | [https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)|
|Planowanie sieci wirtualnych — Przewodnik | [https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm)|
Tworzenie Virtual Network szybkiego startu | [https://docs.microsoft.com/azure/virtual-network/quick-create-portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)|
|

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Komunikacja równorzędna sieci wirtualnej jest używana w celu zapewnienia bezpośredniej ścieżki komunikacji między dwiema sieciami wirtualnymi. Po ustanowieniu komunikacji równorzędnej hosty w jednym Virtual Network mają ścieżkę routingu o dużej szybkości bezpośrednio do hostów w innym Virtual Network. Sieciowych grup zabezpieczeń nadal stosuje się do ruchu jako normalne i zaawansowane parametry konfiguracji mogą służyć do określenia, czy jest dozwolona komunikacja za pośrednictwem bram Virtual Network lub z innych systemów zewnętrznych.

|Resource | Łącze|
|---|---|
|Omówienie Virtual Network komunikacji równorzędnej |  [https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)|
|Tworzenie, zmienianie lub usuwanie komunikacji równorzędnej sieci wirtualnej | [https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering)|
|

### <a name="public-ip-on-vnet"></a>Publiczny adres IP w sieci wirtualnej

Publiczne adresy IP są używane do dostarczania ścieżki komunikacji przychodzącej do usług wdrożonych w Virtual Network. Jednostki Wspólnoty powinny dokładnie zaplanować przydział publicznych adresów IP i przypisać je tylko do zasobów, w których istnieje oryginalne wymaganie. Ogólnie rzecz biorąc, publiczne adresy IP powinny być przydzieloną do zasobów z wbudowanymi możliwościami zabezpieczeń, takimi jak Application Gateway lub wirtualne urządzenia sieciowe, aby zapewnić bezpieczny, kontrolowany publiczny punkt wejścia do Virtual Network.

|Resource | Łącze|
|---|---|
|Przegląd Publiczne adresy IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)|
|Tworzenie, zmienianie lub usuwanie publicznego adresu IP | [https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)|
|

### <a name="expressroute-gateway"></a>Brama ExpressRoute

Bramy ExpressRoute zapewniają punkt transferu danych przychodzących ze środowiska lokalnego i powinny być wdrażane w celu spełnienia wymagań dotyczących zabezpieczeń, dostępności, finansów i wydajności. Bramy ExpressRoute zapewniają określoną przepustowość sieci i obciążają koszty użycia po wdrożeniu. Sieci wirtualne mogą mieć tylko jedną bramę ExpressRoute, ale może to być połączone z wieloma obwodami usługi ExpressRoute i mogą być używane przez wiele sieci wirtualnych za pośrednictwem komunikacji równorzędnej sieci wirtualnych, co pozwala wielu wirtualnym współużytkować przepustowość i łączność. Należy zachować ostrożność konfigurowania routingu między środowiskami lokalnymi i sieciami wirtualnymi przy użyciu bram ExpressRoute, aby zapewnić kompleksowe połączenie przy użyciu znanych, kontrolowanych punktów ruchu przychodzącego sieci. Jednostki Wspólnoty korzystające z bramy ExpressRoute muszą również wdrożyć sieciowe urządzenia wirtualne w celu ustanowienia łączności sieci VPN z środowiskiem lokalnym w celu zapewnienia zgodności ze wskazówkami konsumenta ACSC.

|Resource | Łącze|
|---|---|
|ExpressRoute Gateway — Omówienie | [https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways)|
|Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute | [https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager](https://docs.microsoft.com/azure/expressroute/expressroute-howto-add-gateway-portal-resource-manager)|
|

### <a name="vpn-gateway"></a>VPN Gateway

Usługa Azure VPN Gateway udostępnia punkt sieci ruchu przychodzącego z sieci zewnętrznej w celu zapewnienia bezpiecznych połączeń między lokacjami lub punkt-lokacja. Bramy sieci VPN zapewniają zdefiniowaną przepustowość sieci i obciążają koszty użycia po wdrożeniu. Jednostki Wspólnoty wykorzystujące VPN Gateway powinny mieć pewność, że zostały skonfigurowane zgodnie z zaleceniami konsumentów ACSC. Sieci wirtualne mogą mieć tylko jeden VPN Gateway, ale można je skonfigurować z wieloma tunelami i mogą być używane przez wiele sieci wirtualnych za pośrednictwem komunikacji równorzędnej sieci wirtualnych, co pozwala wielu wirtualnym współużytkować przepustowości i łączności. Bramy sieci VPN można ustanawiać za pośrednictwem Internetu lub przez ExpressRoute za pośrednictwem komunikacji równorzędnej firmy Microsoft.

|Resource | Łącze|
|---|---|
|Przegląd VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)|
|Planowanie i projektowanie dla usługi VPN Gateway | [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)|
|VPN Gateway konfiguracja dla instytucji rządowych Australii|[Wymagana jest konfiguracja protokołu IPSEC dla instytucji rządowych Australii](vpn-gateway.md)|
|

### <a name="paas-vnet-integration"></a>Integracja z siecią wirtualną PaaS

Korzystanie z PaaS może zapewnić lepszą funkcjonalność i dostępność oraz zmniejszyć koszty związane z zarządzaniem, ale muszą być odpowiednio zabezpieczone. W celu zwiększenia kontroli, wymuszania segmentacji sieci lub zapewnienia bezpiecznego punktu wejścia dla aplikacji i usług wiele możliwości PaaS można zintegrować z Virtual Network.

Aby zapewnić bezpieczny punkt wejścia, PaaS możliwości, takie jak Application Gateway, można skonfigurować przy użyciu zewnętrznego, publicznego interfejsu i wewnętrznego interfejsu prywatnego do komunikacji z usługami aplikacji. Zapobiega to konieczności konfigurowania serwerów aplikacji z publicznymi adresami IP i udostępniania ich w sieciach zewnętrznych.

Aby użyć PaaS jako zintegrowanej części architektury systemu lub aplikacji, firma Microsoft udostępnia wiele mechanizmów wdrażania PaaS w Virtual Network. Metodologia wdrażania ogranicza dostęp przychodzący z sieci zewnętrznych, takich jak Internet, przy jednoczesnym zapewnianiu łączności i integracji z wewnętrznymi systemami i aplikacjami. Przykłady obejmują środowiska App Service, wystąpienia zarządzane SQL i wiele innych.

|Resource | Łącze|
|---|---|
|Integracja sieci wirtualnej dla usług platformy Azure | [https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services)|
|Integrowanie aplikacji z usługą Azure Virtual Network Przewodnik po poradniku | [https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)|
|

## <a name="paas-ingress"></a>PaaS ruch przychodzący

Możliwości PaaS zapewniają możliwości w celu zwiększenia możliwości i uproszczonego zarządzania, ale wprowadzają złożoność w zakresie adresów dla segmentacji sieci i rozdzielenia. Możliwości PaaS są zwykle konfigurowane przy użyciu publicznych adresów IP i są dostępne z Internetu.  Podczas kompilowania systemów przy użyciu funkcji PaaS należy zwrócić uwagę, aby zidentyfikować wszystkie wymagane przepływy komunikacji między składnikami w systemie i regułami zabezpieczeń sieci utworzonymi w celu zezwolenia tylko na tę komunikację. W ramach kompleksowego podejścia do obrony należy skonfigurować funkcje PaaS z szyfrowaniem, uwierzytelnianiem i odpowiednimi kontrolami dostępu i uprawnieniami.  

### <a name="hostname"></a>Nazwa hosta

Możliwości PaaS są jednoznacznie identyfikowane przez nazwy hostów, aby umożliwić hostowanie wielu wystąpień tej samej usługi przy użyciu tego samego publicznego adresu IP. Unikatowe nazwy hostów są określane podczas tworzenia zasobów i istnieją w ramach domen DNS należących do firmy Microsoft. Określone nazwy hostów dla dozwolonych usług mogą być używane w narzędziach zabezpieczeń z możliwościami filtrowania na poziomie aplikacji. Niektóre usługi można także skonfigurować przy użyciu domen niestandardowych zgodnie z potrzebami.

|Resource | Łącze|
|---|---|
|Wiele publicznych przestrzeni nazw używanych przez usługi platformy Azure można uzyskać za pomocą programu PowerShell, uruchamiając polecenie Get-AzureRMEnvironment | [https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment](https://docs.microsoft.com/powershell/module/azurerm.profile/get-azurermenvironment)|
|Konfigurowanie niestandardowej nazwy domeny dla usługi w chmurze platformy Azure | App Services i inne mogą mieć domeny niestandardowe[https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal](https://docs.microsoft.com/azure/cloud-services/cloud-services-custom-domain-name-portal)|
|

### <a name="public-ip-for-paas"></a>Publiczny adres IP dla PaaS

Publiczne adresy IP dla możliwości PaaS są przypisywane w oparciu o region, w którym usługa jest hostowana lub wdrożona. Do tworzenia odpowiednich reguł zabezpieczeń sieci i topologii routingu na potrzeby segmentacji sieci i segregowania obejmujących sieci wirtualne platformy Azure, PaaS i ExpressRoute oraz łączność internetową, zrozumienie przydziałów i regionów publicznego adresu IP Wymagane. Platforma Azure przydziela adresy IP z puli przydzielonej do każdego regionu platformy Azure. Firma Microsoft udostępnia adresy używane w poszczególnych regionach do pobrania, które są aktualizowane w regularnych i kontrolowany sposób. Usługi, które są dostępne w poszczególnych regionach, również często zmieniają się po udostępnieniu nowych usług lub wdrożenia usług. Jednostki Wspólnoty powinny regularnie przeglądać te materiały i mogą korzystać z automatyzacji, aby utrzymywać systemy zgodnie z potrzebami. Poszczególne adresy IP dla niektórych usług hostowanych w każdym regionie można uzyskać, kontaktując się z pomocą techniczną firmy Microsoft.

|Resource | Łącze|
|---|---|
|Zakresy adresów IP centrum danych Microsoft Azure | [https://www.microsoft.com/download/details.aspx?id=41653](https://www.microsoft.com/download/details.aspx?id=41653)|
|Usługi platformy Azure na region | [https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional Australia-środkowe, Australia-środkowe-2, Australia-Wschodnia, Australia-Południowo-Wschodnia & produkty = wszystkie](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast&products=all)|
|

### <a name="service-endpoints"></a>Punkty końcowe usługi

Virtual Network punkty końcowe usługi zapewniają szybkie, prywatne połączenie sieciowe w ramach Virtual Network do korzystania z określonych możliwości PaaS. Aby zapewnić pełną segmentację sieci i rozdzielenie możliwości PaaS, możliwość PaaS musi być skonfigurowana w taki sposób, aby akceptowała połączenia tylko z niezbędnych sieci wirtualnych. Nie wszystkie możliwości PaaS obsługują kombinację reguł zapory, które obejmują punkty końcowe usługi i tradycyjne reguły oparte na adresach IP, dlatego należy zwrócić uwagę, aby zrozumieć przepływ komunikacji wymagane przez funkcje i administrowanie aplikacjami. Dzięki temu implementacja tych kontroli zabezpieczeń nie ma wpływu na dostępność usługi.

|Resource | Łącze|
|---|---|
|Przegląd punktów końcowych usługi | [https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)
|Samouczek |[https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)|
|

## <a name="security"></a>Bezpieczeństwo

Implementowanie segmentacji sieci i kontroli podziału na możliwościach IaaS i PaaS jest realizowane za pośrednictwem zabezpieczania możliwości i implementowania kontrolowane ścieżki komunikacyjne z systemów, które będą komunikować się z pozwala.

Projektowanie i kompilowanie rozwiązań na platformie Azure to proces tworzenia architektury logicznej do zrozumienia, kontrolowania i monitorowania zasobów sieciowych w całej obecności platformy Azure. Ta architektura logiczna jest oprogramowaniem zdefiniowanym na platformie Azure i ma miejsce topologii sieci fizycznej wdrożonej w tradycyjnych środowiskach sieciowych.

Utworzona architektura logiczna musi zapewniać funkcjonalność potrzebną dla użyteczności, ale musi także zapewnić widoczność i kontrolę potrzebną w zakresie zabezpieczeń i integralności.

Osiągnięcie tego wyniku jest uzależnione od implementacji niezbędnego segmentacji sieci i narzędzi do segregowania, ale również w ochronie i wymuszanie topologii sieci i implementacji tych narzędzi.

Informacje zawarte w tym przewodniku mogą ułatwić zidentyfikowanie źródeł ruchu przychodzącego, które muszą być dozwolone, oraz sposobów, w jaki ruch może być bardziej kontrolowany lub ograniczony.

### <a name="network-security-groups-nsgs"></a>Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń)

Sieciowych grup zabezpieczeń są używane do określania ruchu przychodzącego i wychodzącego, który jest dozwolony dla podsieci lub określonego interfejsu sieciowego. Podczas konfigurowania sieciowych grup zabezpieczeń jednostki Wspólnoty powinny używać podejścia listy dozwolonych, w którym reguły są skonfigurowane tak, aby zezwalały na ruch wymagany z regułą domyślną skonfigurowaną tak, aby odrzucać cały ruch, który nie jest zgodny z konkretną instrukcją zezwolenia. Należy zachować ostrożność podczas planowania i konfigurowania sieciowych grup zabezpieczeń, aby upewnić się, że cały ruch przychodzący i wychodzący jest odpowiednio przechwytywany. Obejmuje to identyfikowanie i zrozumienie wszystkich zakresów prywatnych adresów IP wykorzystywanych w ramach sieci wirtualnych platformy Azure i środowiska lokalnego oraz określonych usług firmy Microsoft, takich jak Azure Load Balancer i wymagania dotyczące zarządzania PaaS. Osoby mające na celu projektowanie i implementację sieciowych grup zabezpieczeń powinny również zrozumieć użycie tagów usługi i grup zabezpieczeń aplikacji do tworzenia szczegółowych reguł zabezpieczeń, usług i aplikacji.

|Resource | Łącze|
|---|---|
|Omówienie zabezpieczeń sieci | [https://docs.microsoft.com/azure/virtual-network/security-overview](https://docs.microsoft.com/azure/virtual-network/security-overview)
|Tworzenie, zmienianie lub usuwanie sieciowej grupy zabezpieczeń | [https://docs.microsoft.com/azure/virtual-network/manage-network-security-group](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)|
|

## <a name="paas-firewall"></a>Zapora PaaS

Zapora PaaS to funkcja kontroli dostępu do sieci, która może być stosowana do niektórych usług PaaS. Umożliwia filtrowanie adresów IP lub filtrowanie z określonych sieci wirtualnych w celu ograniczenia ruchu przychodzącego do określonego wystąpienia usługi PaaS. W przypadku funkcji PaaS, które obejmują zaporę, należy skonfigurować zasady kontroli dostępu do sieci, aby zezwalały na wymaganie ruchu przychodzącego na podstawie wymagań aplikacji.  

|Resource | Łącze|
|---|---|
|Reguły zapory Azure SQL Database i SQL Data Warehouse IP | [https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure)|
|Zabezpieczenia sieci magazynu | [https://docs.microsoft.com/azure/storage/common/storage-network-security](https://docs.microsoft.com/azure/storage/common/storage-network-security)|
|

## <a name="paas-authentication-and-access-control"></a>PaaS uwierzytelniania i kontroli dostępu

W zależności od możliwości PaaS i jej przeznaczenia użycie formantów sieciowych w celu ograniczenia dostępu może nie być możliwe ani praktyczne. W ramach modelu zabezpieczeń warstwowych dla PaaS platforma Azure zapewnia różnorodne mechanizmy uwierzytelniania i kontroli dostępu, aby ograniczyć dostęp do usługi, nawet jeśli ruch sieciowy jest dozwolony. Typowe mechanizmy uwierzytelniania dla możliwości PaaS obejmują Azure Active Directory, uwierzytelnianie na poziomie aplikacji oraz klucze udostępnione lub sygnatury dostępu. Gdy użytkownik zostanie bezpiecznie zidentyfikowany, role mogą być wykorzystywane do kontrolowania działań, które użytkownik może wykonywać. Te narzędzia mogą być wykorzystane jako alternatywny lub jako miarowy środek ograniczający dostęp do usług.

|Resource | Łącze|
|---|---|
|Kontrolowanie i udzielanie dostępu do bazy danych do SQL Database i SQL Data Warehouse | [https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins](https://docs.microsoft.com/azure/sql-database/sql-database-manage-logins)|
|Autoryzacja dla usług Azure Storage | [https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-Azure-Storage-Services)|
|

## <a name="azure-policy"></a>Azure Policy

Azure Policy to kluczowy składnik służący do wymuszania i utrzymywania integralności logicznej architektury środowiska platformy Azure. Mając na uwadze różne usługi i ścieżki ruchu przychodzącego w sieci dostępne za pomocą usług platformy Azure, ważne jest, aby jednostki Wspólnoty znały zasoby, które istnieją w ich środowisku i dostępne punkty transferu sieciowego. Aby zapewnić, że nieautoryzowane punkty transferu w sieci nie są tworzone w środowisku platformy Azure, jednostki Wspólnoty powinny korzystać Azure Policy w celu kontrolowania typów zasobów, które można wdrożyć, oraz konfiguracji tych zasobów. Praktyczne przykłady obejmują ograniczanie zasobów tylko do tych, które są autoryzowane i zatwierdzone do użycia, wymuszają szyfrowanie HTTPS w magazynie i wymagają dodania sieciowych grup zabezpieczeń do podsieci.

|Resource | Łącze|
|---|---|
|Przegląd Azure Policy | [https://docs.microsoft.com/azure/governance/policy/overview](https://docs.microsoft.com/azure/governance/policy/overview)|
|Przykładowe zasady dla dozwolonych typów zasobów | [https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types](https://docs.microsoft.com/azure/governance/policy/samples/allowed-resource-types)
|Upewnij się, że zasady przykładowe konta magazynu HTTPS|[https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account](https://docs.microsoft.com/azure/governance/policy/samples/ensure-https-storage-account)_
|Wymuś sieciowej grupy zabezpieczeń na zasadzie przykładowej podsieci| [https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet](https://docs.microsoft.com/azure/governance/policy/samples/nsg-on-subnet)|
|

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z artykułem dotyczącym [zarządzania ruchem wychodzącym bramy i kontroli](gateway-egress-traffic.md) w celu uzyskania szczegółowych informacji na temat zarządzania przepływami ruchu ze środowiska platformy Azure do innych sieci przy użyciu składników bramy na platformie Azure.
