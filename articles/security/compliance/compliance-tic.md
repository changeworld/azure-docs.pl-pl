---
title: Zaufany wskazówki połączenia z Internetem na platformie Azure
description: Zaufany wskazówki połączenia z Internetem na platformie Azure i usług SaaS
services: security
author: dlapiduz
ms.assetid: 09511e03-a862-4443-81ac-ede815bdaf25
ms.service: security
ms.topic: article
ms.date: 06/20/2018
ms.author: dlap
ms.openlocfilehash: bb186ab2700b147bee3a7dd81474409ccafb76fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60608088"
---
# <a name="trusted-internet-connections-guidance"></a>Zaufany wskazówki połączeń internetowych

W tym artykule opisano, jak użyć platformy Microsoft Azure Government do zapewnienia zgodności z inicjatywy zaufanych Internet połączenia (tablica) agencje rządowe. W artykule opisano sposób użycia platformy Azure dla instytucji rządowych w infrastrukturze platformy Azure jako usługa (IaaS) i platformy Azure jako oferty usługi (PaaS).

## <a name="trusted-internet-connections-overview"></a>Zaufanych połączeń internetowych — omówienie

Inicjatywy tablica ma na celu optymalizacji i ujednolicenie zabezpieczeń poszczególnych zewnętrznych połączeń sieciowych, które są używane przez agencje federalne. Zasady są opisane w temacie [Office zarządzania i budżetu OMB () protokołu M-08-05](https://georgewbush-whitehouse.archives.gov/omb/memoranda/fy2008/m08-05.pdf).

W listopadzie 2007 OMB ustanowione program Tablica, aby poprawić zabezpieczenia brzegowe sieci federalnych i reagowania na zdarzenia funkcji. Tablica jest przeznaczony do przeprowadzania analizy sieci całego ruchu przychodzącego i wychodzącego .gov do identyfikowania określonych podpisów i danych na podstawie wzorca. Tablica odkrywa anomalii zachowań, takich jak działania botnetu. Agencje upoważnionych do konsolidacji ich połączeń sieci zewnętrznej i kierować cały ruch za pomocą urządzeń wykrywania i zapobiegania włamaniom znane jako EINSTEIN. Urządzenia znajdują się w ograniczonej liczbie punktów końcowych sieci, które są określane jako _zaufanych połączeń internetowych_.

Celem tablica jest dla Agencji wiedzieć:
- Kto jest w mojej sieci (autoryzacji lub brak autoryzacji)?
- Jeśli sieć jest dostępny i dlaczego?
- Jakie zasoby są dostępne?

Wszystkie połączenia zewnętrzne agencji obecnie musi kierować za pośrednictwem zatwierdzone OMB tablica. Agencje federalne są wymagane do uczestnictwa w programie Tablica jako tablica dostępu do dostawcy (TICAP) lub przez instytucje usług przy użyciu jednego z dostawców usług internetowych głównych warstwy 1. Ci dostawcy są określane jako zarządzane zaufanych Internet Protocol usługi (MTIPS) dostawców. Tablica zawiera obowiązkowe krytyczne możliwości, które są wykonywane przez Agencję oraz MTIPS dostawcy. W bieżącej wersji tablica, wykrywanie włamań w wersji 2 EINSTEIN i EINSTEIN w wersji 3 accelerated (3A) nieautoryzowanego dostępu zapobiegania urządzeń są wdrażane w poszczególnych TICAP i MTIPS. Agencja ustanawia "Ustaleń" z działu z wewnętrznego Stanów Zjednoczonych zabezpieczeń (DHS) rozmieszczania możliwości EINSTEIN federalne.

W ramach jego odpowiedzialność za ochronę sieci .gov DHS wymaga źródła danych pierwotnych agencji netto przepływu danych w celu korelowanie zdarzeń w instytucji rządowych oraz przeprowadzanie analiz przy użyciu specjalistycznych narzędzi. Routery DHS zapewniają możliwość zbierania ruchu sieciowego adresu IP, ponieważ wprowadza lub kończy działanie interfejsu. Administratorzy sieci mogą analizować dane przepływu netto do ustalenia źródła i miejscem przeznaczenia ruchu, klasa usługi i tak dalej. NET przepływu danych jest uważany za "data-content" podobny do nagłówka, źródłowy adres IP, docelowy adres IP i tak dalej. Dane inne niż zawartości umożliwia DHS dowiedzieć się więcej na temat zawartości: kto wykonywanych co się stanie, jak i na jak długo.

Inicjatywy zawiera również zasady zabezpieczeń, wskazówek i struktur, które zakładają infrastruktury lokalnej. Agencje rządowe przeniesieniu do chmury do osiągnięcia oszczędności kosztów, wydajność operacyjną i innowacji, wymagania wdrożenia Tablica może spowolnić ruchu sieciowego. Szybkość i elastyczność, dzięki której dla instytucji rządowych użytkownicy mogą uzyskiwać dostęp ich danych w chmurze wynosi w wyniku.

## <a name="azure-networking-options"></a>Opcje sieci platformy Azure

Istnieją trzy główne opcje, aby połączyć się z usługami platformy Azure:

- Bezpośrednie połączenie z Internetem: Łączenie z usługami platformy Azure bezpośrednio za pomocą otwartego połączenia internetowego. Nośnik i połączenia są publiczne. Aplikacji i szyfrowanie na poziomie transportu są stosowane w celu zapewnienia zachowania poufności. Przepustowość jest ograniczona przez witrynę łączności z Internetem. Użyj więcej niż jednego dostawcę active w celu zapewnienia odporności.
- Wirtualnej sieci prywatnej (VPN): Połączyć się z siecią wirtualną platformy Azure prywatnie przy użyciu bramy sieci VPN.
Nośnik jest publiczny, ponieważ go przechodzi przez witrynę standardowego połączenia internetowego, ale połączenie jest szyfrowane w tunelu, aby zapewnić prywatność. Przepustowość jest ograniczona w zależności od urządzenia sieci VPN i konfiguracji wybrany. Połączenia z usługą Azure point-to-site są zazwyczaj ograniczone do 100 MB/s i połączenia lokacja lokacja są ograniczone do 1,25 GB/s.
- Azure ExpressRoute: ExpressRoute to bezpośrednie połączenie z usługami firmy Microsoft. Ponieważ łączność za pośrednictwem kanału fiber izolowane, połączenie może być publicznym lub prywatnym w zależności od konfiguracji, który jest używany. Przepustowość jest zazwyczaj ograniczone do maksymalnie 10 GB/s.

Istnieje kilka sposobów do spełnienia wymagań dotyczących tablica załącznik H (zagadnienia dotyczące chmury), jak określono w dziale zwycięstwa zabezpieczeń firmy, "Zaufanych Internet połączenia (tablica) odwołanie do architektury dokumentu, w wersji 2.0." Ten artykuł zawiera wskazówki dotyczące tablica DHS nazywa się **2.0 tablica**.

Aby włączyć połączenie z **dział (D/A) / Agencja kierująca** na platformie Azure lub usługi Office 365 bez konieczności routingu ruchu za pośrednictwem tablica D/A, D/A należy użyć szyfrowany tunel lub dedykowanego połączenia do dostawcy usług w chmurze (CSP). Z usług dostawcy usług Kryptograficznych upewnij się, że łączność z zasobami w chmurze D/nie jest oferowana do publicznej sieci internet, uzyskać dostęp do personelu bezpośrednie Agencji.

Usługi Office 365 jest zgodne z tablica 2.0 załącznik H przy użyciu jednej usługi ExpressRoute za pomocą [Peering firmy Microsoft](https://docs.microsoft.com/azure/expressroute/expressroute-circuit-peerings) włączone lub połączenia internetowego, który szyfruje cały ruch przy użyciu protokołu TLS 1.2. Użytkownicy końcowi D/A w sieci D/A można nawiązywać za pośrednictwem ich agencji sieci i infrastruktury tablica za pośrednictwem Internetu. Powoduje to zablokowanie wszystkich zdalnego dostępu do Internetu dla usługi Office 365 i kieruje za pośrednictwem agencji. D/A można też nawiązać połączenie usługi Office 365 za pośrednictwem połączenia usługi ExpressRoute za pomocą Peering firmy Microsoft (typ publicznej komunikacji równorzędnej) włączone.  

Platformy Azure tylko pierwszej opcji (VPN) i trzecią opcję (ExpressRoute) spełnić te wymagania, jeśli są używane w połączeniu z usługami, które ograniczają dostęp do Internetu.

![Microsoft zaufane połączenie z Internetem (tablica)](media/tic-diagram-a.png)

## <a name="azure-infrastructure-as-a-service-offerings"></a>Infrastruktura platformy Azure jako oferty usługi

Zgodność z zasadami tablica za pomocą modelu IaaS platformy Azure jest stosunkowo prosta, ponieważ klienci platformy Azure zarządza ich własnych routing w sieci wirtualnej.

Główne wymagania do zapewnienia zgodności z architektury referencyjnej tablica jest upewnij się, że Twoja sieć wirtualna jest rozszerzeniem prywatnej sieci D/A. Jako _prywatnej_ rozszerzenia zasady wymagają, żaden ruch nie opuści sieci firmy z wyjątkiem za pośrednictwem lokalnej z połączeniem sieciowym tablica. Ten proces jest nazywany _wymuszanie tunelowania_. Tablica zgodności proces przekierowuje cały ruch z dowolnego systemu w środowisku dostawcy usług Kryptograficznych za pośrednictwem bramy lokalnej sieci organizacji się z Internetem za pośrednictwem tablica.

Zgodności tablica IaaS platformy Azure jest podzielony na dwie główne kroki:

- Krok 1: Konfiguracja.
- Krok 2: Inspekcja.

### <a name="azure-iaas-tic-compliance-configuration"></a>Zgodności tablica IaaS platformy Azure: Konfigurowanie

Aby skonfigurować zgodne tablica architektury za pomocą platformy Azure, należy najpierw uniemożliwić bezpośredni dostęp do Internetu do sieci wirtualnej i następnie wymuszają ruch internetowy za pośrednictwem sieci lokalnej.

#### <a name="prevent-direct-internet-access"></a>Uniemożliwić bezpośredni dostęp do Internetu

Sieci IaaS platformy Azure odbywa się za pośrednictwem sieci wirtualne, które składają się z podsieci, z którymi są skojarzone kontrolery interfejsu sieciowego (NIC) maszyn wirtualnych.

Najprostszy scenariusz do obsługi zgodności tablica jest zapewnienie, że maszynę wirtualną lub kolekcji maszyn wirtualnych nie można połączyć z dowolnych zasobów zewnętrznych. Za pomocą sieciowych grup zabezpieczeń (NSG), należy zapewnić rozłączenia z sieci zewnętrznych. Użyj sieciowe grupy zabezpieczeń umożliwiają kontrolowanie ruchu do kart sieciowych lub podsieci w sieci wirtualnej. Sieciowa grupa zabezpieczeń zawiera reguły kontroli dostępu, które zezwalają na ruch lub go blokują w oparciu o kierunek ruchu, protokół, adres źródłowy i docelowy oraz port. Reguły sieciowej grupy zabezpieczeń można zmienić w dowolnym momencie, a zmiany są stosowane do wszystkich skojarzonych wystąpień. Aby dowiedzieć się więcej o tym, jak utworzyć sieciową grupę zabezpieczeń, zobacz [filtrowanie ruchu sieciowego z sieciową grupą zabezpieczeń](https://docs.microsoft.com/azure/virtual-network/virtual-networks-create-nsg-arm-pportal).

#### <a name="force-internet-traffic-through-an-on-premises-network"></a>Wymuszają ruch internetowy za pośrednictwem sieci lokalnej

Platforma Azure automatycznie tworzy trasy systemowe i przypisuje trasy do każdej podsieci w sieci wirtualnej. Nie można utworzyć lub usunąć trasy systemowe, ale możesz zastąpić niektóre trasy systemowe trasami niestandardowymi. Platforma Azure tworzy domyślne trasy systemowe dla każdej podsieci. Platforma Azure dodaje opcjonalne trasy domyślne do określonych podsieci lub każdej podsieci, korzystając z określonych funkcji platformy Azure. Zapewnia to typ routingu:
- Ruch skierowany do sieci wirtualnej, pozostaje w obrębie sieci wirtualnej.
- Wyznaczony przez organizację IANA prywatnych przestrzeni adresowych takich jak 10.0.0.0/8, są opuszczane, chyba że są one dołączone w przestrzeni adresowej sieci wirtualnej.
- Routing "Ostatnia czynność" 0.0.0.0/0 do punktu końcowego wirtualnej sieci internet.

![Tablica wymuszanie tunelowania](media/tic-diagram-c.png)

Cały ruch, które powodują, że sieć wirtualna musi kierowany przez połączenie lokalne, aby upewnić się, że cały ruch przechodzi przez tablica D/A. Tworzenie niestandardowych tras przez tworzenie tras zdefiniowanych przez użytkownika lub przez wymianę tras protokołu BGP (Border Gateway) między swojej bramy sieci lokalnej i bramą Azure VPN gateway. Aby uzyskać więcej informacji o trasach definiowanych przez użytkownika, zobacz [routing ruchu w sieci wirtualnej: Trasy zdefiniowane przez użytkownika](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#user-defined). Aby uzyskać więcej informacji na temat protokołu BGP, zobacz [routing ruchu w sieci wirtualnej: Border Gateway Protocol](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol).

#### <a name="add-user-defined-routes"></a>Dodawanie trasy zdefiniowane przez użytkownika

Jeśli używasz bramy sieci wirtualnej opartej na trasach, możesz wymusić tunelowania na platformie Azure. Dodawanie trasy zdefiniowane przez użytkownika (UDR) określająca ruchu 0.0.0.0/0 przekierowywać do **miejsca docelowego następnego skoku** bramy sieci wirtualnej. Azure priorytetem trasy zdefiniowane przez użytkownika za pośrednictwem trasy zdefiniowane przez system. Cały ruch sieciowy niewirtualną są wysyłane do bramy sieci wirtualnej, której następnie można kierować ruch do serwera lokalnego. Po zdefiniowaniu zdefiniowanej przez użytkownika, skojarzenia z trasą z podsieci istniejących lub nowych podsieci w obrębie wszystkich sieci wirtualnych w środowisku platformy Azure.

![trasy zdefiniowane przez użytkownika i Tablica](media/tic-diagram-d.png)

#### <a name="use-the-border-gateway-protocol"></a>Użyj protokołu Border Gateway Protocol

Jeśli używasz usługi ExpressRoute lub bram sieci wirtualnej z włączonym protokołem BGP, Protokół BGP jest to preferowany anonsowanie tras. Dla protokołu BGP trasy anonsowane 0.0.0.0/0 usługi ExpressRoute i obsługujących protokół BGP bramy sieci wirtualnej upewnij się, że domyślna trasa ma zastosowanie do wszystkich podsieci w sieci wirtualnej.

### <a name="azure-iaas-tic-compliance-auditing"></a>Zgodności tablica IaaS platformy Azure: Inspekcja

Platforma Azure oferuje kilka sposobów na inspekcję zgodności tablica.

#### <a name="view-effective-routes"></a>Wyświetlanie obowiązujących tras

Upewnij się, że trasy domyślne są propagowane przez obserwację _obowiązujące trasy_ dla konkretnej maszyny wirtualnej, określonej karty Sieciowej lub tabeli tras zdefiniowanych przez użytkownika w [witryny Azure portal](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-portal#diagnose-using-azure-portal) lub [ Program Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/virtual-network-routes-troubleshoot-powershell#diagnose-using-powershell). **Obowiązujące trasy** pokazują odpowiednie trasy zdefiniowane przez użytkownika anonsowania BGP tras i trasy systemowe, które są stosowane do odpowiednich jednostek, jak pokazano na poniższej ilustracji:

![Obowiązujące trasy](media/tic-screen-1.png)

> [!NOTE]
> Dla karty Sieciowej, nie można wyświetlić obowiązujące trasy, o ile nie jest skojarzony z uruchomionej maszyny wirtualnej karty Sieciowej.

#### <a name="use-azure-network-watcher"></a>Usługa Azure Network Watcher

Usługa Azure Network Watcher oferuje kilka narzędzi, aby przeprowadzić inspekcję zgodności tablica. Aby uzyskać więcej informacji, zobacz [w tym omówieniu o usłudze Network Watcher](https://docs.microsoft.com/azure/network-watcher/network-watcher-monitoring-overview).

##### <a name="capture-network-security-group-flow-logs"></a>Przechwytywanie dzienniki przepływu sieciowych grup zabezpieczeń 

Usługa Network Watcher umożliwia przechwytywania dzienników przepływu sieci, które wskazują metadanych, który otacza ruch IP. Dzienniki przepływu sieciowej zawierają źródłowych i docelowych adresów docelowych i inne dane. Można użyć tych danych za pomocą dzienników z bramy sieci wirtualnej, w środowisku lokalnym krawędzi urządzenia lub tablica, do monitorowania, że cały ruch trasy w środowisku lokalnym. 

## <a name="azure-platform-as-a-service-offerings"></a>Platforma Azure jako oferty usługi

Usług PaaS platformy Azure, takich jak Azure Storage są dostępne za pośrednictwem adresu URL dostępnego w Internecie. Każda osoba mająca poświadczenia zatwierdzonych można uzyskać dostęp do zasobu, np. Konto magazynu z dowolnego miejsca, bez przechodzenie tablica. Z tego powodu wielu klientom będącym instytucjami niepoprawnie stwierdzi, że usługi PaaS platformy Azure nie są zgodne z zasadami tablica. Wiele usług PaaS platformy Azure może być zgodne z zasadami tablica. Usługa jest zgodna w przypadku, gdy architektura jest taki sam jak środowiska IaaS tablica zgodne ([opisanej wcześniej](https://docs.microsoft.com/azure/security/compliance/compliance-tic#azure-infrastructure-as-a-service-offerings)), a usługa jest dołączona do sieci wirtualnej platformy Azure.

W przypadku usług PaaS platformy Azure są zintegrowane z siecią wirtualną, usługa jest prywatnie dostępne w tej sieci wirtualnej. Można zastosować, routing niestandardowe 0.0.0.0/0 za pomocą trasy zdefiniowane przez użytkownika lub protokołu BGP. Niestandardowy routing gwarantuje, że cały ruch z Internetu kieruje lokalnym w celu przechodzenia tablica. Integracja usług platformy Azure w sieciach wirtualnych, korzystając z następujących wzorców:

- **Wdrażanie dedykowane wystąpienie usługi**: Coraz więcej usług PaaS są możliwe do wdrożenia jako dedykowane wystąpienia za pomocą punktów końcowych dołączone do sieci wirtualnej. Środowisko usługi App Service w usłudze PowerApps można wdrożyć w trybie "Izolowany", aby zezwolić na punkt końcowy sieci będą ograniczane do sieci wirtualnej. Usługa App Service Environment następnie może obsługiwać wiele usług Azure PaaS, takich jak Azure Web Apps usługi Azure API Management i Azure Functions.
- **Korzystanie z punktów końcowych usługi sieci wirtualnej**: Coraz więcej usług PaaS umożliwiają możliwość skorzystania z ich punktu końcowego do sieci wirtualnej prywatny adres IP zamiast adresów publicznych.

Usług, które obsługują wdrażanie wystąpień dedykowanych w sieci wirtualnej lub użycie punktów końcowych usługi, od maja 2018 r. są wymienione w poniższych tabelach.

> [!Note]
> Stan dostępności odnosi się do usług platformy Azure, które są dostępne na rynku. Oczekuje stanu dostępności usług systemu Azure na platformie Azure Government.

### <a name="support-for-service-endpoints"></a>Obsługa punktów końcowych usługi

|Usługa                        |Dostępność      |
|-------------------------------|------------------|
|W usłudze Azure Key Vault                | Prywatna wersja zapoznawcza  |
|Azure Cosmos DB                | Prywatna wersja zapoznawcza  |
|Usługi zarządzania tożsamościami              | Prywatna wersja zapoznawcza  |
|Azure Data Lake                | Prywatna wersja zapoznawcza  |
|Azure Database for PostgreSQL  | Prywatna wersja zapoznawcza  |
|Azure Database for MySQL       | Prywatna wersja zapoznawcza  |
|Azure SQL Data Warehouse       | Publiczna wersja zapoznawcza   |
|Azure SQL Database             | Ogólna dostępność (GA) |
|Azure Storage                  | Ogólna dostępność               |

### <a name="support-for-virtual-network-injection"></a>Obsługa iniekcji sieci wirtualnej

|Usługa                               |Dostępność      |
|--------------------------------------|------------------|
|Wystąpienie zarządzane usługi Azure SQL Database   | Publiczna wersja zapoznawcza   |
|Azure Kubernetes Service (AKS)        | Publiczna wersja zapoznawcza   |
|Azure Service Fabric                  | Ogólna dostępność               |
|Usługa Azure API Management                  | Ogólna dostępność               |
|Usługa Azure Active Directory                | Ogólna dostępność               |
|Azure Batch                           | Ogólna dostępność               |
|Środowisko usługi App Service               | Ogólna dostępność               |
|Azure Cache for Redis                     | Ogólna dostępność               |
|Azure HDInsight                       | Ogólna dostępność               |
|Zestaw skalowania maszyn wirtualnych             | Ogólna dostępność               |
|usług Azure Cloud Services                  | Ogólna dostępność               |


### <a name="virtual-network-integration-details"></a>Szczegóły integracji sieci wirtualnej

Na poniższym diagramie przedstawiono przepływ ogólny sieci do uzyskiwania dostępu do usługi PaaS. Dostęp jest wyświetlany z iniekcji sieci wirtualnej i tunelowanie usługi sieci wirtualnej. Aby uzyskać więcej informacji dotyczących bramy usługi w sieci, sieci wirtualnych i tagi usługi, zobacz [sieciowych i grup zabezpieczeń aplikacji: Tagów usług](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

![Opcje łączności PaaS tablica](media/tic-diagram-e.png)

1. Połączenie prywatne dokonuje na platformie Azure przy użyciu usługi ExpressRoute. Prywatnej komunikacji równorzędnej usługi ExpressRoute przy wymuszonego tunelowania służy do wymuszenia wszystkich klientów ruchu w sieci wirtualnej za pośrednictwem usługi ExpressRoute i z powrotem do środowiska lokalnego. Peering firmy Microsoft nie jest wymagana.
2. Usługa Azure VPN Gateway, gdy jest używana w połączeniu z usługi ExpressRoute i Peering firmy Microsoft mogą nakładać end-to-end szyfrowania IPSec między sieci wirtualnej klienta i urządzenia brzegowe w środowisku lokalnym. 
3. Połączenie sieciowe z sieci wirtualnej klienta jest kontrolowany za pomocą sieciowych grup zabezpieczeń, które umożliwiają klientom w celu zezwolenia/blokowania na podstawie IP, portu i protokołu.
4. Sieci wirtualne klienta rozszerza usługa PaaS przez utworzenie punktu końcowego usługi dla usługi klienta.
5. Punkt końcowy usługi PaaS będzie zabezpieczony **domyślne Odmawiaj wszystkim** i aby zezwolić tylko na dostęp z określonej podsieci w sieci wirtualnej klienta. Domyślnie nie zezwala na również zawiera połączenia, które pochodzą z Internetu.
6. Innych usług platformy Azure, które muszą uzyskać dostęp do zasobów w ramach sieci wirtualnej klienta powinny być:  
   - Wdrażać bezpośrednio w sieci wirtualnej.
   - Selektywnie dozwolone, oparte na wskazówkach z odpowiednich usług platformy Azure.

#### <a name="option-a-deploy-a-dedicated-instance-of-a-service-virtual-network-injection"></a>Option A: Wdrażanie dedykowane wystąpienie usługi (iniekcji sieci wirtualnej)

Iniekcja sieci wirtualnej umożliwia klientom selektywne wdrażanie dedykowanych wystąpień usługi danej platformy Azure, takich jak HDInsight, w ich własnych sieci wirtualnej. Wystąpienia usługi są wdrażane w dedykowanej podsieci w sieci wirtualnej klienta. Iniekcja sieci wirtualnej umożliwia dostęp do zasobów usługi przy użyciu adresów Routing-internet. Wystąpienia lokalne umożliwia ExpressRoute lub sieci VPN lokacja lokacja uzyskiwania bezpośredniego dostępu do wystąpień usługi za pośrednictwem przestrzeni adresowej sieci wirtualnej, zamiast otwierać zapory w celu przestrzeń publicznych adresów internetowych. Gdy dedykowanego wystąpienia jest dołączony do punktu końcowego, możesz użyć tej samej strategii, jak w przypadku zgodności tablica IaaS. Domyślny routing gwarantuje, że ruchu skierowanego do Internetu jest przekierowywane do bramy sieci wirtualnej, który jest powiązany dla lokalnego. Dodatkowo można kontrolować wychodzący i przychodzący dostęp za pomocą sieciowych grup zabezpieczeń dla danej podsieci.

![Omówienie usługi Virtual network iniekcji](media/tic-diagram-f.png)

#### <a name="option-b-use-virtual-network-service-endpoints-service-tunnel"></a>Opcja B: Użyj punkty końcowe usługi sieci wirtualnej (tunel usługi)

Coraz więcej usług wielodostępnych systemu Azure oferują "punkty końcowe usługi." Punkty końcowe usługi są alternatywną metodę integracji z sieciami wirtualnymi platformy Azure. Punkty końcowe usługi sieci wirtualnej rozszerzają możliwości przestrzenią adresów IP sieci wirtualnej i tożsamość sieci wirtualnej do usługi za pośrednictwem bezpośredniego połączenia. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Azure. 

Po włączeniu punktu końcowego usługi dla usługi, użyj zasady udostępnianych przez usługę, aby ograniczyć połączenia usługi do tej sieci wirtualnej. Sprawdzanie uprawnień dostępu są wymuszane na platformie według usługi platformy Azure. Dostęp do zablokowanych zasobu otrzymuje tylko, jeśli żądanie pochodzi z dozwolonych sieci wirtualnej lub podsieci lub dwa adresy IP, które są używane do identyfikowania ruchu w środowisku lokalnym, jeśli korzystasz z usługi ExpressRoute. Ta metoda umożliwia skutecznie zapobiegać ruchu przychodzącego/wychodzącego bezpośrednio opuszczania usługi PaaS.

![Omówienie punktów końcowych usługi](media/tic-diagram-g.png)

## <a name="cloud-native-tools-for-network-situational-awareness"></a>Narzędzia natywnych dla chmury dla świadomości sytuacyjnej w sieci

System Azure oferuje narzędzia natywnych dla chmury, aby mieć pewność, że masz świadomości sytuacyjnej, które są wymagane, aby zrozumieć przepływy ruchu w sieci. Narzędzia nie są wymagane dla zgodności z zasadami tablica. Narzędzia może znacząco poprawić swoje funkcje zabezpieczeń.

### <a name="azure-policy"></a>Azure Policy

[Usługa Azure Policy](../../governance/policy/overview.md) jest usługą platformy Azure, która zapewnia lepsze możliwości inspekcji i wymuszania zgodności Twojej organizacji. Klientów można zaplanować i przetestować ich reguł usługi Azure Policy do zapewnienia przyszłych zgodności tablica.

Usługa Azure Policy jest przeznaczona na poziomie subskrypcji. Usługa zapewnia scentralizowane interfejs, gdzie można wykonać zadania zgodności, w tym:
- Zarządzanie inicjatywy
- Konfigurowanie definicji zasad
- Inspekcja zgodności
- Wymuszanie zgodności
- Zarządzanie wyjątkami

Wraz z wielu wbudowanych definicji Administratorzy mogą definiować własne niestandardowe definicje za pomocą prostych szablonów JSON. Firma Microsoft zaleca priorytetyzacji inspekcji za pośrednictwem wymuszania, jeśli jest to możliwe.

Poniższy przykład zasad może służyć do scenariuszy zgodności tablica:

|Zasady  |Przykładowy scenariusz  |Szablon  |
|---------|---------|---------|
|Wymuszanie tabeli tras zdefiniowanych przez użytkownika. | Upewnij się, że trasa domyślna na wszystkie sieci wirtualne wskazuje bramą zatwierdzonych sieci wirtualnej dla routingu do serwera lokalnego.    | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/no-user-defined-route-table.md). |
|Przeprowadzaj inspekcję usługi Network Watcher nie jest włączona dla regionu.  | Upewnij się, że usługi Network Watcher jest włączona dla wszystkich używanych regionów.  | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/network-watcher-not-enabled.md). |
|Sieciowa grupa zabezpieczeń x w każdej podsieci.  | Upewnij się, że sieciowa grupa zabezpieczeń (lub zestaw zatwierdzonych sieciowych grup zabezpieczeń) przy użyciu zablokowany ruch z Internetu, jest stosowane do wszystkich podsieci w każdej sieci wirtualnej. | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/nsg-on-subnet.md). |
|Sieciowa grupa zabezpieczeń x na każdej karcie sieciowej. | Upewnij się, że sieciowa grupa zabezpieczeń z Zablokowany ruch z Internetu jest stosowany do wszystkich kart sieciowych na wszystkich maszynach wirtualnych. | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/nsg-on-nic.md). |
|Zatwierdzone sieci wirtualnej na użytek interfejsy sieciowe maszyny wirtualnej.  | Upewnij się, że wszystkie karty sieciowe w sieci wirtualnej zatwierdzone. | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/use-approved-vnet-vm-nics.md). |
|Dozwolone lokalizacje. | Upewnij się, że wszystkie zasoby są wdrażane w regionach z sieciami wirtualnymi zgodne i konfiguracji usługi Network Watcher.  | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/allowed-locations.md). |
|Niedozwolone typy zasobów, takich jak **publicznymi**. | Stanów Zjednoczonych zabraniają wdrożenia typów zasobów, które nie mają planu zgodności. Aby uniemożliwić wdrażanie zasobów publicznych adresów IP, należy użyć tych zasad. Reguły sieciowej grupy zabezpieczeń można zastosować, aby skutecznie zablokować ruch przychodzący z Internetu, pozwalają na korzystanie z publicznych adresów IP dalsze zmniejsza obszar narażony na ataki.   | Rozpoczynanie pracy z tym [szablonu](../../governance/policy/samples/not-allowed-resource-types.md).  |

### <a name="network-watcher-traffic-analytics"></a>Network Watcher analizy ruchu

Network Watcher [traffic analytics](https://azure.microsoft.com/blog/traffic-analytics-in-preview/) korzystanie z danych dzienników przepływu i innych dzienników do stanowią ogólne omówienie ruchu sieciowego. Dane są przydatne do inspekcji tablica zgodności i do identyfikacji aktywnych problemów. Wysokiego poziomu pulpit nawigacyjny umożliwia szybkie ekranu maszyn wirtualnych, które komunikują się z Internetem i Uzyskaj listę wąsko zdefiniowany dla routingu tablica.

![Analiza ruchu](media/tic-traffic-analytics-1.png)

Użyj **mapy geograficznej** szybkie identyfikowanie prawdopodobne fizycznych miejsc docelowych ruchu internetowego dla maszyn wirtualnych. Można zidentyfikować i klasyfikowanie podejrzanych lokalizacji lub zmiany wzorca:

![Mapy geograficznej](media/tic-traffic-analytics-2.png)

Użyj **topologię sieci wirtualnej** w celu szybkiego badania istniejące sieci wirtualne:

![Mapy topologii sieci](media/tic-traffic-analytics-3.png)

### <a name="network-watcher-next-hop-tests"></a>Network Watcher następnego przeskoku testów

Sieci w regionach, które są monitorowane przy użyciu usługi Network Watcher można przeprowadzić następnego przeskoku testów. W witrynie Azure portal można wprowadzić źródłowego i docelowego przykładowy przepływ sieci usługi Network Watcher można rozpoznać miejsca docelowego następnego skoku. Uruchomić ten test przed maszyn wirtualnych i adresami internetowymi przykładowej w celu zapewnienia miejsca docelowego następnego skoku jest Brama wirtualnej sieci oczekiwane.

![Następny przeskok testów](media/tic-network-watcher.png)

## <a name="conclusions"></a>Wnioski

Można łatwo skonfigurować dostęp dla Microsoft Azure, Office 365 i Dynamics 365, aby zapewnić zgodność ze wskazówkami Tablica 2.0 załącznik H, jako napisane i zdefiniowane maja 2018 r. Firma Microsoft uznaje, że wskazówki tablica ulec zmianie. Microsoft usiłują pomóc klientom w wydaniu nowych wytycznych spełniają ze wskazówkami zawartymi w sposób terminowy.

## <a name="appendix-trusted-internet-connections-patterns-for-common-workloads"></a>Dodatek: Zaufany wzorce połączeń internetowych dla typowych obciążeń

| Category | Obciążenie | IaaS | PaaS w wersji dedykowanej / iniekcji sieciami wirtualnymi  | Punkty końcowe usługi  |
|---------|---------|---------|---------|--------|
| Wystąpienia obliczeniowe | Maszyny wirtualne systemu Linux platformy Azure | Yes | | |
| Wystąpienia obliczeniowe | Maszyny wirtualne platformy Azure Windows | Yes | | |
| Wystąpienia obliczeniowe | Zestawy skalowania maszyn wirtualnych | Yes | | |
| Wystąpienia obliczeniowe | Azure Functions | | Środowisko usługi App Service | |
| Sieci Web i mobilnych | Wewnętrzne aplikacje internetowe | | Środowisko usługi App Service| |
| Sieci Web i mobilnych | Wewnętrzny aplikacji mobilnej | | Środowisko usługi App Service | |
| Sieci Web i mobilnych | Aplikacje interfejsu API | | Środowisko usługi App Service | |
| Containers | Azure Container Service | | | Yes |
| Containers | Usługa Azure Kubernetes Service (AKS) \* | | | Yes |
| Database (Baza danych) | Azure SQL Database | | Wystąpienie zarządzane usługi Azure SQL Database \* | Azure SQL |
| Database (Baza danych) | Azure Database for MySQL | | | Yes |
| Database (Baza danych) | Azure Database for PostgreSQL | | | Yes |
| Database (Baza danych) | Azure SQL Data Warehouse | | | Yes |
| Database (Baza danych) | Azure Cosmos DB | | | Yes |
| Database (Baza danych) | Azure Cache for Redis | | Yes | |
| Magazyn | Azure Blob Storage | Yes | | |
| Magazyn | Azure Files | Yes | | |
| Magazyn | Usługa Azure Queue storage | Yes | | |
| Magazyn | Azure Table Storage | Yes | | |
| Magazyn | Usługa Azure Disk storage | Yes | | |

\* Publiczna wersja zapoznawcza na platformie Azure Government maja 2018 r.
