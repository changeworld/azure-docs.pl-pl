---
title: Zaufany internetowy połączenia wskazówki dotyczące platformy Azure
description: Zaufany wskazówki połączenia internetowe dla platformy Azure i usług SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: cf24810c0aa414e751e55df163563f013c1a0081
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969949"
---
# <a name="trusted-internet-connection-guidance"></a>Wskazówki dotyczące połączenia zaufany internetowy

## <a name="background"></a>Tło

Inicjatywy zaufanych Internet połączenia (tablica) ma na celu optymalizacji i ujednolicenie zabezpieczeń poszczególnych zewnętrznych połączeń sieciowych aktualnie w użyciu przez agencje federalne. Zasady jest opisany w OMB (Office programu Management and Budget) [protokołu M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

W listopadzie 2007 OMB ustanowione program Tablica, aby poprawić zabezpieczenia brzegowe sieci federalnych i reagowania na zdarzenia funkcji. Tablica został zaprojektowany w celu przeprowadzenia analizy sieci całego ruchu przychodzącego i wychodzącego .gov do identyfikowania określonych podpisów i danych na podstawie wzorca i wykrycia anomalii zachowań, takich jak działania botnetu. Agencje zostały upoważnione do konsolidacji ich połączeń sieci zewnętrznej i mają cały ruch kierowany przez nieautoryzowanego dostępu wykrywania i zapobiegania im oraz urządzeniami (znana jako EINSTEIN) hostowanych w ograniczonej liczbie punktów końcowych sieci (określane jako zaufane Internet Połączenia).

Krótko mówiąc, celem tablica jest dla Agencji wiedzieć:
- Kto jest w mojej sieci (autoryzacji lub brak autoryzacji)?
- Jeśli Moja sieć uzyskiwany jest i dlaczego?
- Jakie zasoby są używane?

Obecnie wszystkie połączenia zewnętrzne agencji ma być kierowany przez zatwierdzone OMB tablica. Agencje federalne są wymagane do uczestnictwa w programie Tablica jako tablica dostępu do dostawcy (TICAP) lub przez instytucje usługi z jednym z głównych warstwy 1 usługodawców internetowych, określonych jako zarządzane zaufanych Internet Protocol usługi (MTIPS) dostawców.  Tablica zawiera obowiązkowe krytyczne możliwości, które są wykonywane już dziś, agencji i MTIPS dostawcy. W bieżącej wersji tablica, wykrywanie włamań w wersji 2 EINSTEIN i EINSTEIN w wersji 3 accelerated (3A) nieautoryzowanego dostępu zapobiegania urządzeń są wdrażane na poszczególnych TICAP oraz MTIPS i agencji ustanawia ustaleń z dział Zwycięstwa zabezpieczeń (DHS) rozmieszczania możliwości EINSTEIN federalne.

W ramach jego odpowiedzialność za ochronę sieci .gov DHS wymaga źródła danych pierwotnych danych Netflow agencji korelowanie zdarzeń w instytucji rządowych i wykonywania analiz przy użyciu specjalistycznych narzędzi. Routery DHS zapewniają możliwość zbierania ruchu sieciowego adresu IP, ponieważ wprowadza lub kończy działanie interfejsu. Analizując dane przepływu netto, administrator sieci można określić elementy, takie jak źródło i miejsce docelowe ruchu, klasa usługi itp. NET przepływu danych jest uznawany za "data-content" (na przykład nagłówek źródłowego adresu IP, docelowy adres IP, itp.) i umożliwia DHS poznawać informacje wokół zawartości; oznacza to, kto wykonywanych co się stanie, jak i na jak długo.

Inicjatywy zawiera również zasady zabezpieczeń, wskazówek i struktur, które zakładają infrastruktury lokalnej. Agencje rządowe przeniesieniu do chmury do osiągnięcia oszczędności kosztów, wydajność operacyjną i innowacji, wymagania wdrożenia tablica są w niektórych przypadkach zmniejszają ruch sieciowy i ograniczanie szybkości i elastyczności, dzięki której dla instytucji rządowych użytkownicy mogą dostęp do swoich danych w chmurze.

W tym artykule opisano, agencje rządowe użycia platformy Microsoft Azure Government do zapewnienia zgodności z zasadami tablica usługach PaaS i IaaS.

## <a name="summary-of-azure-networking-options"></a>Podsumowanie opcji sieci platformy Azure

Podczas nawiązywania połączenia z usługami platformy Azure, istnieją trzy główne opcje:

1. Bezpośrednie połączenie z Internetem: łączenie się z usługami platformy Azure bezpośrednio za pomocą otwartego połączenia internetowego. Nośnik jest publiczny, a także połączenia. Aplikacji i szyfrowanie na poziomie transportu są stosowane w celu zapewnienia zachowania poufności. Przepustowość jest ograniczona przez witrynę łączności z Internetem i wiele aktywnych dostawców może służyć do zapewnienia odporności
1. Wirtualna sieć prywatna: Łączenie się z siecią wirtualną platformy Azure przez użytkowników za pomocą bramy sieci VPN.
Nośnik jest publiczny, go przechodzi przez witrynę standardowego połączenia internetowego, ale połączenie jest szyfrowane w tunelu, aby zapewnić prywatność. Przepustowość jest ograniczona w zależności od urządzenia sieci VPN i konfiguracji wybrany. Połączenia Azure Point-to-Site są zwykle maksymalnie 100 MB/s, podczas gdy połączenia lokacja-lokacja są ograniczone do 1,25 GB/s.
1. Microsoft ExpressRoute: ExpressRoute to bezpośrednie połączenie z usługami firmy Microsoft. Ponieważ łączność za pośrednictwem izolowane protokołem fiber channel, połączenie może być publicznym lub prywatnym w zależności od konfiguracji używane. Przepustowość jest zazwyczaj ograniczone do maksymalnie 10 GB/s.

Istnieje kilka sposobów wymagań zaufanych Internet połączenia załącznik H (zagadnienia dotyczące chmury), podczas gdy znaleziono "Zaufanych Internet połączenia (tablica) odwołanie do architektury dokument, w wersji 2.0" w dziale z bezpieczeństwa wewnętrznego Stanów Zjednoczonych firmy. W tym dokumencie wskazówki tablica DHS zostanie określone jako tablica w wersji 2.0.

Aby włączyć połączenie z działu lub agencji (D/A) na platformie Azure lub usługi Office 365 bez konieczności routingu ruchu za pośrednictwem tablica D/A, D/A należy użyć szyfrowany tunel i/lub dedykowanego połączenia do dostawcy usług chmury (CSP). Usług dostawcy usług Kryptograficznych zapewnić łączność z zasobami w chmurze D/nie są dostępne do publicznego Internetu, uzyskać dostęp do personelu Agencji bezpośrednie.

Usługi Office 365 jest zgodne z tablica 2.0 załącznik H, za pomocą obu Express Route o [Peering firmy Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings#expressroute-routing-domains) włączone lub połączenia internetowego, który szyfruje cały ruch przy użyciu protokołu TLS 1.2.  Użytkownicy końcowi D/A w sieci D/A można nawiązywać za pośrednictwem ich agencji sieci i infrastruktury tablica za pośrednictwem Internetu. Wszystkie zdalnego dostępu do Internetu dla usługi Office 365 jest zablokowane i przesyłane za pośrednictwem agencji. D/A też nawiązać połączenie usługi Office 365 za pośrednictwem połączenia Expressroute za pomocą komunikacji równorzędnej firmy Microsoft, który jest typem publicznej komunikacji równorzędnej włączone.  

Platformy Azure tylko opcje 2 (VPN) i 3 (ExpressRoute) można spełniają te wymagania, gdy są one używane w połączeniu z usługami, które ograniczają dostęp do Internetu.

![Microsoft zaufanych Internet Diagram połączenia (tablica)](media/tic-diagram-a.png)

## <a name="how-azure-infrastructure-as-a-service-offerings-can-help-with-tic-compliance"></a>Jak infrastruktura platformy Azure jako oferty usługi może pomóc w tablica zgodności

Zgodne z zasadami tablica za pomocą rozwiązania IaaS jest stosunkowo prosta, ponieważ klienci platformy Azure zarządzać ich własnych routing w sieci wirtualnej.

Główne konieczność zapewnienia zgodności z architektury referencyjnej tablica jest zapewnienie, że sieci wirtualnej (VNet) staje się rozszerzeniem prywatnej sieci działu lub agencji. Stanie się _prywatnej_ rozszerzenia zasady wymagają, żaden ruch pozostaw sieci, z wyjątkiem za pośrednictwem połączenia sieciowego między lokalnymi tablica. Proces ten jest znany jako "Tunelowania wymuszonego", który, gdy używana tablica zgodności, to proces routingu cały ruch z dowolnego systemu w środowisku dostawcy usług Kryptograficznych do pracy za pomocą bramy lokalnej sieci organizacji się z Internetem za pośrednictwem tablica.

Zgodności tablica IaaS platformy Azure może można podzielić na dwie główne kroki:

1. Konfigurowanie
1. Inspekcja

### <a name="azure-iaas-tic-compliance-configuration"></a>Konfiguracja zgodności tablica IaaS platformy Azure

Aby skonfigurować zgodne tablica architektury za pomocą platformy Azure, trzeba będzie najpierw uniemożliwić bezpośredni dostęp do Internetu do sieci wirtualnej, a następnie wymuszają ruch internetowy za pośrednictwem sieci lokalnej.

#### <a name="prevent-direct-internet-access"></a>Uniemożliwić dostęp do Internetu bezpośrednie

Sieci IaaS platformy Azure odbywa się za pośrednictwem sieci wirtualnych składającej się z podsieci, z którą są skojarzone kontrolery interfejsu sieciowego (NIC) maszyn wirtualnych.

Najprostszy scenariusz do obsługi zgodności tablica jest zapewnienie, że maszynę wirtualną lub jego kolekcji nie można połączyć z dowolnych zasobów zewnętrznych. Rozłączenie z sieci zewnętrznych mogą mieć pewność, przy użyciu sieciowych grup zabezpieczeń (NSG), która może służyć do kontrolowania ruchu do kart sieciowych lub podsieci w sieci wirtualnej. Sieciowa grupa zabezpieczeń zawiera reguły kontroli dostępu, które zezwalają na ruch lub go blokują w oparciu o kierunek ruchu, protokół, adres źródłowy i docelowy oraz port. Reguły sieciowej grupy zabezpieczeń można zmienić w dowolnym momencie, a zmiany są stosowane do wszystkich skojarzonych wystąpień.  Aby dowiedzieć się więcej o tym, jak utworzyć sieciową grupę zabezpieczeń, znajduje się w artykule [tworzenie sieciowej grupy zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-on-premises-network"></a>Wymuszają ruch internetowy za pośrednictwem sieci lokalnej

Platforma Azure automatycznie tworzy trasy systemowe i przypisuje trasy do każdej podsieci w sieci wirtualnej. Nie można tworzyć tras systemowych ani nie można usunąć trasy systemowe, ale możesz zastąpić niektóre trasy systemowe trasami niestandardowymi. Azure tworzy domyślne trasy systemowe dla każdej podsieci, a następnie dodaje dodatkowe opcjonalne trasy domyślne do określonych podsieci lub każdej podsieci, korzystając z określonych funkcji platformy Azure. Marszruty gwarantuje, że ruch kierowany do sieci wirtualnej, pozostaje w obrębie sieci wirtualnej, IANA wyznaczony prywatnych przestrzeni adresowych, takich jak 10.0.0.0/8 są odrzucane (chyba że zawarte w przestrzeni adresowej sieci wirtualnej) i routing "ostatnia czynność" 0.0.0.0/0 do punktu końcowego Internet sieci wirtualnej.

![Tablica wymuszanie tunelowania](media/tic-diagram-c.png)

Aby upewnić się, że cały ruch przechodzi przez tablica D/A, cały ruch, pozostawiając sieć wirtualna musi być kierowane za pośrednictwem połączenia między lokalnymi.  Tworzenie niestandardowych tras przez albo tworzenia tras definiowanych przez użytkownika lub przez wymianę border gateway protocol (BGP) tras między swojej bramy sieci lokalnej i bramą sieci wirtualnej platformy Azure. Więcej informacji na temat trasy zdefiniowane przez użytkownika, można znaleźć w folderze https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined. Można także znaleźć więcej informacji na temat protokołu Border Gateway Protocol na https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol.

#### <a name="user-defined-routes"></a>Trasy zdefiniowane przez użytkownika

Jeśli używane są oparte na trasach bramy sieci wirtualnej, tunelowanie wymuszone można osiągnąć w obrębie platformy Azure, dodając ruchu 0.0.0.0/0 ustawienie zdefiniowaną przez użytkownika (UDR), można je skierować na "przeskok" bramy sieci wirtualnej. Azure priorytetem trasy zdefiniowany przez użytkownika za pośrednictwem trasy zdefiniowane przez System, więc spowodowałoby to cały ruch-sieć wirtualna są wysyłane do usługi bramy sieci wirtualnej, której następnie można kierować do środowiska lokalnego. Po zdefiniowaniu tej trasy zdefiniowane przez użytkownika musi być skojarzone z podsieciami wszystkie istniejące lub nowo utworzone w ramach wszystkie sieci wirtualne w środowisku platformy Azure.

![trasy zdefiniowane przez użytkownika i Tablica](media/tic-diagram-d.png)

#### <a name="border-gateway-protocol"></a>Protokół Border Gateway Protocol

Jeśli używasz usługi ExpressRoute lub Border Gateway Protocol (BGP) włączone bramy sieci wirtualnej, Protokół BGP jest to preferowany anonsowanie tras. Za pomocą protokołu BGP trasy anonsowany 0.0.0.0/0 oraz połączeń usługi ExpressRoute i protokołu BGP bramy sieci wirtualnej pamiętać zapewni, że ta trasa domyślna jest stosowane do wszystkich podsieci w sieci wirtualnej.

### <a name="azure-iaas-tic-compliance-auditing"></a>Tablica IaaS platformy Azure, inspekcja zgodności

Platforma Azure oferuje wiele sposobów, aby inspekcja zgodności tablica.

#### <a name="effective-routes"></a>Obowiązujące trasy

Aby upewnić się, rozpropagowane trasy domyślne, można zaobserwować "Skuteczne trasy" określonej maszyny Wirtualnej, określonej karty Sieciowej lub tabelę tras zdefiniowane przez użytkownika. Można to zrobić za pomocą witryny Azure portal zgodnie z opisem w https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal, lub za pośrednictwem programu PowerShell, zgodnie z opisem w https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell. Blok obowiązujące trasy pozwoli zobaczyć, że odpowiednie trasy zdefiniowane przez użytkownika, Protokół BGP anonsowane tras i trasy systemowe, które są stosowane do odpowiednich jednostek, jak pokazano poniżej.

![Zrzut ekranu kieruje](media/tic-screen-1.png)

**Uwaga**: nie można wyświetlić obowiązujące trasy dla karty Sieciowej, chyba że jest ona skojarzona z uruchomionej maszyny Wirtualnej.

#### <a name="network-watcher"></a>Network Watcher

Usługa Azure Network Watcher oferuje wiele narzędzi, których można użyć do inspekcji tablica zgodności.  Dowiedz się więcej o usłudze Network Watcher w https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview.

##### <a name="network-security-groups-flow-logs"></a>Dzienniki przepływów grup zabezpieczeń sieci 

Usługa Azure Network Watcher oferuje możliwość przechwytywania dzienników przepływu sieciowych wskazujący metadanych związanych z ruchem IP. Oprócz innych danych dzienników przepływów sieciowych zawierać źródło i lokalizacje docelowe adresy elementów docelowych. W efekcie w połączeniu z dzienników z bramy sieci wirtualnej, na urządzenia brzegowe w środowisku lokalnym i/lub tablica, umożliwia monitorowanie, cały ruch rzeczywiście jest rozsyłany w środowisku lokalnym. 

## <a name="how-azure-platform-as-a-service-offerings-can-help-with-tic-compliance"></a>Jak platforma Azure jako oferty usługi może pomóc w tablica zgodności

Usługi PaaS platformy Azure, takich jak Azure Storage są dostępne za pośrednictwem adresu URL dostępnego w Internecie. Każda osoba mająca poświadczenia zatwierdzonych można uzyskać dostęp do zasobu, np. Konto magazynu z dowolnego miejsca, bez przechodzenie tablica. Z tego powodu wielu klientom będącym instytucjami niepoprawnie zawrzeć usług PaaS platformy Azure nie są zgodne z zasadami tablica. W rzeczywistości wielu usług PaaS platformy Azure może być zgodne z zasadami tablica przy użyciu takiej samej architekturze jako środowiska IaaS CLS tablica opisanych powyżej, jeśli ich może zostać dołączony do sieci wirtualnej (VNet). Integrowanie usług PaaS platformy Azure z siecią wirtualną platformy Azure umożliwia usłudze prywatnie dostępności tej sieci wirtualnej i pozwala niestandardowy routing 0.0.0.0/0, które mają być stosowane za pomocą trasy zdefiniowane przez użytkownika lub protokołu BGP, zapewnia, że całego ruchu skierowanego do Internetu jest rozsyłany środowiska lokalnego do Przechodzenie przez tablica.  Niektóre usługi platformy Azure można zintegrować sieci wirtualnych za pomocą następujących wzorców:

- **Wdrażanie dedykowane wystąpienie usługi**: coraz więcej usług może być wdrożony jako dedykowane wystąpienia z sieciami wirtualnymi w modelu paas dołączone punktów końcowych. Na przykład App Service Environment (ASE) można wdrożyć w trybie "Izolowane", dzięki czemu jej punkt końcowy sieci będą ograniczane do sieci wirtualnej. To środowisko ASE następnie może obsługiwać wiele usług Azure PaaS, takich jak aplikacje sieci Web, interfejsów API i funkcje.
- **Punkty końcowe usługi sieci wirtualnej**: coraz więcej usług PaaS, Zezwalaj na możliwość skorzystania z ich punktu końcowego do sieci wirtualnej prywatny adres IP zamiast adresów publicznych.

Poniżej wymieniono usługi, które obsługują wdrażanie wystąpień dedykowanych do sieci wirtualnej lub punktów końcowych usługi, począwszy od maja 2018 r.: * (dostępność reprezentuje wersji Azure Commercial, platformy Azure dla instytucji rządowych dostępności do czasu).

### <a name="service-endpoints"></a>Punkty końcowe usługi

|Usługa                   |Stan            |
|--------------------------|------------------|
|Usługa Azure KeyVault            | Prywatna wersja zapoznawcza  |
|Cosmos DB                 | Prywatna wersja zapoznawcza  |
|Tożsamość                  | Prywatna wersja zapoznawcza  |
|Azure Data Lake           | Prywatna wersja zapoznawcza  |
|SQL Postgress/Mysql       | Prywatna wersja zapoznawcza  |
|Azure SQL Data Warehouse  | Publiczna wersja zapoznawcza   |
|Azure SQL                 | Ogólna dostępność               |
|Magazyn                   | Ogólna dostępność               |

### <a name="vnet-injection"></a>Iniekcja sieci wirtualnej

|Usługa                            |Stan            |
|-----------------------------------|------------------|
|Wystąpienie zarządzane SQL               | Publiczna wersja zapoznawcza   |
|Service(AKS) kontenerów platformy Azure       | Publiczna wersja zapoznawcza   |
|Service Fabric                     | Ogólna dostępność               |
|API Management                     | Ogólna dostępność               |
|Usługa Azure Active Directory             | Ogólna dostępność               |
|Azure Batch                        | Ogólna dostępność               |
|ŚRODOWISKA ASE                                | Ogólna dostępność               |
|Redis                              | Ogólna dostępność               |
|USŁUGI HDI                                | Ogólna dostępność               |
|Obliczenia zestawu skalowania maszyn wirtualnych  | Ogólna dostępność               |
|Obliczenia usługi w chmurze              | Ogólna dostępność               |

### <a name="vnet-integration-details"></a>Szczegóły integracji sieci wirtualnej

Poniższym diagramie przedstawiono ogólne sieciowego dla dostępu do usług PaaS przy użyciu iniekcji sieci wirtualnej i tunelowanie usługi sieci wirtualnej.  Dodatkowe informacje na temat bram usług sieciowych, sieci wirtualnej i tagi usługi można znaleźć tutaj https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags.

![Opcje łączności PaaS tablica](media/tic-diagram-e.png)

1. Prywatnego połączenia z platformą Azure przy użyciu usługi ExpressRoute. Prywatnej komunikacji równorzędnej usługi ExpressRoute przy wymuszonego tunelowania, można wymusić cały ruch z sieci wirtualnej klienta za pośrednictwem usługi ExpressRoute do środowiska lokalnego. Peering firmy Microsoft nie jest wymagana.
2. Usługa Azure VPN Gateway, używany w połączeniu z komunikacji równorzędnej firmy Microsoft usługi ExpressRoute może służyć do nakładki end-to-end szyfrowania IPSec między sieci wirtualnej klienta i urządzenia brzegowe w środowisku lokalnym. 
3. Połączenie sieciowe z sieci wirtualnej klienta steruje się za pomocą sieciowych grup zabezpieczeń co umożliwia klientom w celu zezwolenia/blokowania na podstawie IP, portu i protokołu.
4. Sieci wirtualnej klienta jest rozszerzany do usługi PaaS, tworząc punkt końcowy usługi dla usługi klienta.
5. Punkt końcowy usługi PaaS jest zabezpieczony na domyślny Odmawiaj wszystkim i tylko zezwolenie na dostęp z określonej podsieci w sieci wirtualnej klienta.  Domyślnie odmawiać również zawiera połączeń pochodzących z Internetu.
6. Wszelkich innych usług platformy Azure, które muszą uzyskać dostęp do zasobów w ramach sieci wirtualnej klienta powinny być:  
  a. Wdrażać bezpośrednio w sieci wirtualnej  
  b. Povolen selektywnie w oparciu o wskazówki z odpowiednich usług platformy Azure.

#### <a name="option-1-dedicated-instance-vnet-injection"></a>Opcja 1: Dedykowanego wystąpienia "Sieci wirtualnej iniekcji"

Przy użyciu iniekcji sieci wirtualnej klienci mogą wdrażać selektywnie wystąpień dedykowanych danej usługi systemu Azure, takich jak HDInsight, do ich własnych sieci wirtualnej. Wystąpienia usługi są wdrażane w dedykowanej podsieci w sieci wirtualnej klienta. Iniekcja sieci wirtualnej umożliwia dostęp za pośrednictwem rutowalne adresy-Internet do zasobów usługi.  Wystąpienia lokalne można uzyskać dostęp do tych wystąpień usługi za pośrednictwem przestrzeni adresowej sieci wirtualnej bezpośrednio za pośrednictwem usługi ExpressRoute lub sieci VPN lokacja-lokacja, zamiast otwierać zapór przestrzeń publicznych adresów internetowych. Przy użyciu dedykowanego wystąpienia dołączone do punktu końcowego ale opisane strategie używana tablica IaaS zgodności może być wykorzystywany, z domyślną routingu, zapewnienie, że ruchu skierowanego do Internetu jest przekierowywane do bramy sieci wirtualnej do środowiska lokalnego. Dla ruchu przychodzącego i wychodzącego dostępu do może dodatkowo kontrolowane za pomocą sieciowych grup zabezpieczeń (NSG) dla danej podsieci.

![Diagram ogólny iniekcji sieci wirtualnej](media/tic-diagram-f.png)

#### <a name="option-2-vnet-service-endpoints"></a>Opcja 2: Punkty końcowe usługi sieci wirtualnej 

Coraz więcej wielodostępnych usług platformy Azure oferują funkcja "Punkt końcowy usługi", alternatywna metoda umożliwia integrację z sieciami wirtualnymi platformy Azure. Punkty końcowe usługi sieci wirtualnej rozszerzają możliwości przestrzenią adresów IP sieci wirtualnej i tożsamość sieci wirtualnej do usługi za pośrednictwem bezpośredniego połączenia.  Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Azure. Po włączeniu punktu końcowego usługi dla usługi połączenia z usługą można ograniczyć do tej sieci wirtualnej za pomocą zasad udostępnianych przez usługę. Sprawdzanie uprawnień dostępu są wymuszane na platformie przez usługę Azure, a dostęp do zablokowanych zasobu jest udzielany tylko jeśli żądanie pochodzi z dozwolonych sieci wirtualnej/podsieci i/lub dwa adresy IP używane do identyfikowania ruchu opłacanie, jeśli korzystasz z usługi ExpressRoute. Może to służyć do skutecznie zapobiega Inbound/Outbound ruchu bezpośrednio opuszczania usługi PaaS.

![Diagram ogólny punkty końcowe usługi](media/tic-diagram-g.png)

## <a name="using-cloud-native-tools-for-network-situational-awareness"></a>Przy użyciu narzędzia natywnych w chmurze dla świadomości sytuacyjnej w sieci

System Azure oferuje chmurę natywnych narzędzi, aby mieć pewność, że masz świadomości sytuacyjnej, trzeba zrozumieć przepływy ruchu w sieci. Nie są wymagane do zapewnienia zgodności z zasadami tablica, ale ich może znacząco poprawić swoje funkcje zabezpieczeń.

### <a name="azure-policy"></a>Azure Policy

Usługa Azure Policy (https://azure.microsoft.com/services/azure-policy/) jest usługą platformy Azure, która zapewnia lepsze możliwości inspekcji i wymuszania zgodności Twojej organizacji.  Obecnie dostępne w publicznej wersji zapoznawczej w komercyjnych chmurach Azure, ale nie są jeszcze w systemie Microsoft Azure dla instytucji rządowych, klienci świadome tablica zacząć planowania i testowania ich reguły dla zapewnienia zgodności w przyszłości. Usługa Azure Policy jest przeznaczona na poziomie subskrypcji i zawiera scentralizowanego interfejsu zarządzania inicjatywy, definicje zasad, wyniki inspekcji i wymuszania i Zarządzanie wyjątkami. Oprócz wielu wbudowane definicje zasad platformy Azure Administratorzy mogą definiować własne niestandardowe definicje za pomocą szablonów json proste. Dla wielu klientów firma Microsoft zaleca priorytetyzacji inspekcji za pośrednictwem wymuszania, jeśli jest to możliwe.

Poniższy przykład zasad mogą być przydatne w przypadku scenariuszy zgodności tablica:

|Zasady  |Przykładowy scenariusz  |Począwszy od szablonu  |
|---------|---------|---------|
|Wymuszanie tabeli tras zdefiniowanych przez użytkownika |     Upewnij się, że trasy domyślne we wszystkich punktach sieci wirtualnych do zatwierdzonych bramy sieci wirtualnej dla routingu do lokalnego | https://docs.microsoft.com/azure/azure-policy/scripts/no-user-def-route-table |
|Przeprowadzaj inspekcję usługi Network Watcher nie jest włączona dla regionu  | Upewnij się, że usługi Network Watcher jest włączona dla wszystkich używanych regionów  | https://docs.microsoft.com/azure/azure-policy/scripts/net-watch-not-enabled |
|Sieciowa grupa zabezpieczeń X w każdej podsieci  | Upewnij się, że sieciowa grupa zabezpieczeń (lub zestaw zatwierdzonych sieciowych grup zabezpieczeń) przy użyciu zablokowany ruch z Internetu, jest stosowane do wszystkich podsieci w każdej sieci wirtualnej | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-subnet |
|Sieciowa grupa zabezpieczeń X na każdej karcie sieciowej | Upewnij się, że sieciowa grupa zabezpieczeń z Zablokowany ruch z Internetu jest stosowany do wszystkich kart sieciowych na wszystkich maszynach wirtualnych. | https://docs.microsoft.com/azure/azure-policy/scripts/nsg-on-nic |
|Użyj zatwierdzone sieci wirtualnej dla interfejsów sieciowych maszyny Wirtualnej  | Upewnij się, że wszystkie karty sieciowe w zatwierdzonej sieci wirtualnej | https://docs.microsoft.com/azure/azure-policy/scripts/use-approved-vnet-vm-nics |
|Dozwolone lokalizacje | Upewnij się, że wszystkie zasoby są wdrażane w regionach z zgodne z konfiguracją sieci wirtualne i usługi Network Watcher  | https://docs.microsoft.com/azure/azure-policy/scripts/allowed-locs |
|Niedozwolone typy zasobów, takich jak publicznymi  | Stanów Zjednoczonych zabraniają wdrożenia typy zasobów, które nie posiadają plan zgodności. Na przykład te zasady mogą służyć do Stanów Zjednoczonych zabraniają wdrożenie publicznego adresu IP adresowania zasobów. Reguły sieciowej grupy zabezpieczeń można zastosować, aby skutecznie zablokować ruch przychodzący z Internetu, co uniemożliwia dalsze korzystanie z publicznych adresów IP zmniejsza obszar narażony na ataki.    | https://docs.microsoft.com/azure/azure-policy/scripts/not-allowed-res-type  |

### <a name="azure-traffic-analyticshttpsazuremicrosoftcomen-inblogtraffic-analytics-in-preview"></a>Azure [analizy ruchu](https://azure.microsoft.com/en-in/blog/traffic-analytics-in-preview/)

Analiza ruchu platformy Azure usługi Network Watcher korzysta z danych dzienników przepływu i innych dzienników, aby zapewnić ogólny przegląd ruchu sieciowego. Może to być przydatne do inspekcji tablica zgodności i identyfikacji aktywnych problemów. Ogólny pulpit nawigacyjny może służyć do szybkiego ekranu, które komunikują się z maszyn wirtualnych z Internetem, które będzie dostarczać ukierunkowane listy dla routingu tablica.

![Zrzut ekranu z analizy ruchu](media/tic-traffic-analytics-1.png)

"Mapy geograficznej" można szybko zidentyfikować prawdopodobnie fizycznych miejsc docelowych ruchu internetowego dla maszyn wirtualnych, co umożliwia identyfikowanie i klasyfikowanie podejrzanych lokalizacji lub zmiany wzorca.

![Zrzut ekranu z analizy ruchu](media/tic-traffic-analytics-2.png)

Mapy topologii sieci, można szybko przeglądać istniejące sieci wirtualne:

![Zrzut ekranu z analizy ruchu](media/tic-traffic-analytics-3.png)

### <a name="azure-network-watcher-next-hop"></a>Usługa Azure Network Watcher miejsca docelowego następnego skoku

Sieci w regionach monitorowane przez usługę Network Watcher można przeprowadzać testy "Następnego przeskoku", umożliwiając łatwy dostęp oparte na portalu, wpisać źródłowego i docelowego przykładowy przepływ sieciowy, dla którego usługa Network Watcher rozwiąże docelowej "Następnego skoku". To może służyć przed maszyn wirtualnych i adresami internetowymi przykład do upewnij się, że "następnego skoku" rzeczywiście bramy sieci wirtualnej.

![Następny przeskok obserwatora sieciowego](media/tic-network-watcher.png)

## <a name="conclusions"></a>Wnioski

Microsoft Azure, Office 365 i Dynamics 365 dostępu można je łatwo konfigurować, aby zapewnić zgodność ze wskazówkami Tablica 2.0 załącznik H jako napisane i zdefiniowane w maju 2018 r.  Firma Microsoft jest pamiętać, że te wskazówki może ulec zmianie i będzie wszelkich starania, aby pomóc klientom w wydaniu nowych wytycznych spełniają ze wskazówkami zawartymi w sposób terminowy.

## <a name="appendix-tic-patterns-for-common-workloads"></a>Dodatek: Tablica wzorce dla typowych obciążeń

| Kategoria | Obciążenie | IaaS | PaaS w wersji dedykowanej / iniekcji sieci wirtualnej  | Punkty końcowe usługi  |
|---------|---------|---------|---------|--------|
| Wystąpienia obliczeniowe | Maszyny wirtualne z systemem Linux | Yes | | |
| Wystąpienia obliczeniowe | Maszyny wirtualne z systemem Windows | Yes | | |
| Wystąpienia obliczeniowe | Zestawy skali maszyn wirtualnych | Yes | | |
| Wystąpienia obliczeniowe | Azure Functions | | za pomocą środowiska App Service Environment (ASE) | |
| Sieć Web i aplikacje mobilne | Wewnętrzne aplikacje internetowe | | za pomocą środowiska App Service Environment (ASE) | |
| Sieć Web i aplikacje mobilne | Wewnętrzny aplikacji mobilnej | | za pomocą środowiska App Service Environment (ASE) | |
| Sieć Web i aplikacje mobilne | API Apps | | za pomocą środowiska App Service Environment (ASE) | |
| Containers | Usługa Azure Container Service (ACS) | | | Yes |
| Containers | Usługa Azure Container Service (AKS) * | | | Yes |
| Database (Baza danych) | SQL Database | | Usługi Azure SQL Database zarządzanego wystąpienia * | Azure SQL |
| Database (Baza danych) | Azure Database for MySQL | | | Yes |
| Database (Baza danych) | Azure Database for PostgreSQL | | | Yes |
| Database (Baza danych) | SQL Data Warehouse | | | Yes |
| Database (Baza danych) | Azure Cosmos DB | | | Yes |
| Database (Baza danych) | Pamięć podręczna Redis | | Yes | |
| Magazyn | Obiekty blob | Yes | | |
| Magazyn | Pliki | Yes | | |
| Magazyn | Kolejki | Yes | | |
| Magazyn | Tabele | Yes | | |
| Magazyn | Dyski | Yes | | |

*: Publicznej wersji zapoznawczej Azure Government od maja 2018 r.  
**: Prywatnej wersji zapoznawczej usługi Azure Government od maja 2018 r.

