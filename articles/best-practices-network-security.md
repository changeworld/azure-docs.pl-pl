---
title: Najlepsze rozwiązania sieci platformy Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, niektóre najważniejsze funkcje dostępne w systemie Azure w celu tworzenia bezpiecznego w środowiskach sieci
services: virtual-network
documentationcenter: na
author: tracsman
manager: rossort
editor: ''
ms.assetid: d169387a-1243-4867-a602-01d6f2d8a2a1
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/03/2017
ms.author: jonor
ms.openlocfilehash: cf015f4857a22b755813d0be1af5a55a8b7b6535
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34360476"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Chmury Microsoft bezpieczeństwa usług i sieci
Usługi w chmurze firmy Microsoft świadczenia usług hiperskali i infrastruktury, funkcje klasy korporacyjnej oraz wiele opcji na potrzeby łączności hybrydowej. Klienci mogą wybrać dostępu do tych usług za pośrednictwem Internetu lub z usługi Azure ExpressRoute, który zapewnia łączność w sieci prywatnej. Platforma Microsoft Azure umożliwia klientom bezproblemowo rozszerzają infrastruktury do chmury i tworzenia wielu architektur. Ponadto stron trzecich można włączyć udoskonalone funkcje oferty usług zabezpieczeń oraz urządzenia wirtualnego. Ten dokument zawiera omówienie architektury problemów, które klientów należy wziąć pod uwagę podczas korzystania z usługi w chmurze firmy Microsoft dostępne za pośrednictwem usługi ExpressRoute i zabezpieczeń. Obejmuje ona również tworzenia bardziej bezpieczne usług w sieci wirtualnych platformy Azure.

## <a name="fast-start"></a>Szybki start
Na poniższym wykresie logiki można kierować do określonych przykład wielu metody zabezpieczeń dostępne z platformą Azure. Krótki przewodnik można znaleźć na przykład, który najlepiej odpowiada Twoim przypadku. Rozwinięte wyjaśnień nadal odczytywania za pośrednictwem papieru.
[![0]][0]

[Przykład 1: Tworzenie sieci obwodowej (znanej także jako strefa DMZ, strefą zdemilitaryzowaną lub podsiecią ekranowaną) do ochrony aplikacji przy użyciu grup zabezpieczeń sieci (NSG).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Przykład 2: Tworzenie sieci obwodowej, aby chronić aplikacje z zaporą i grup NSG.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Przykład 3: Tworzenie sieci obwodowej, aby zabezpieczyć sieć zapory, trasy zdefiniowane przez użytkownika (przez) i grupy NSG.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Przykład 4: Dodaj połączenie hybrydowe z urządzenia do lokacji, wirtualne wirtualnej sieci prywatnej (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Przykład 5: Dodaj połączenie hybrydowe lokacja lokacja, Brama sieci VPN platformy Azure.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Przykład 6: Dodaj połączenie hybrydowe z usługi ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Przykłady dodawania połączeń między sieciami wirtualnymi, wysokiej dostępności i łańcucha usługi zostanie dodany do tego dokumentu za pośrednictwem kilku kolejnych miesięcy.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Ochrona zgodności i infrastruktury firmy Microsoft
Aby ułatwić organizacjom zapewnienia zgodności z wymaganiami krajowych, regionalnych i branżowe dotyczące zbierania i używania danych poszczególnych osób, firma Microsoft oferuje ponad 40 certyfikaty oraz poświadczenia. Najbardziej kompleksowy zestaw dowolnego dostawcy usług w chmurze.

Aby uzyskać więcej informacji, zobacz informacje o zgodności na [Microsoft Trust Center][TrustCenter].

Firma Microsoft ma wyczerpującą chronić potrzebne do uruchomienia usług globalnych hiperskali infrastruktury chmury. Infrastruktury chmury firmy Microsoft obejmuje sprzętu, oprogramowania, sieci i administracyjnych i operatorów, oprócz centrów danych fizycznych.

![2]

To podejście zapewnia bardziej bezpieczną podstawę dla klientów do wdrożenia usług w chmurze firmy Microsoft. Następnym krokiem jest przeznaczona dla klientów do projektowania i tworzenia Architektura zabezpieczeń, aby chronić te usługi.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Tradycyjne zabezpieczenia architektury i sieci obwodowej
Mimo że Microsoft intensywnie inwestuje ochrony infrastruktury chmury, klienci muszą również ochronę ich usługi w chmurze i grup zasobów. Wielowarstwowe podejścia do zabezpieczeń zapewnia najlepszą ochroną. Strefy zabezpieczeń sieci obwodowej chroni wewnętrznych zasobów sieciowych z niezaufaną siecią. Sieć obwodowa odwołuje się do krawędzi lub części sieci, które znajdują się między Internet i chronionych enterprise infrastruktury IT.

W sieciach typowego przedsiębiorstwa w infrastrukturze podstawowej jest znacznie wzmocnione na strefy, z wielu warstw zabezpieczeń urządzeń. Granica każdej warstwy składa się z urządzeniami i punkty wymuszania zasad. Każda warstwa może zawierać kombinację następujących urządzeń zabezpieczeń sieciowych: zapór, zapobiegania przeprowadzenie ataku typu "odmowa usługi" (DoS), wykrywania nieautoryzowanego dostępu lub systemy ochrony (Identyfikatory/IP) i urządzenia sieci VPN. Wymuszanie zasad mogą mieć formę zasad zapory, listy kontroli dostępu (ACL) lub specyficznego routingu. Pierwszy wiersz obrony w sieci, bezpośrednio akceptować ruch przychodzący z Internetu, to kombinacja tych mechanizmów na ataki bloku i szkodliwy ruch umożliwiając dalsze uprawnionych żądań do sieci. Ten ruch trasy bezpośrednio do zasobów w sieci obwodowej. Ten zasób może następnie "skontaktować" zasobów więcej danych w sieci, przejeżdżających przez granicę dalej do sprawdzania poprawności pierwszy. Warstwa peryferyjnych jest nazywany sieci obwodowej, ponieważ ta część sieci jest połączenie z Internetem, zwykle z jakiegoś ochrony po obu stronach. Na poniższej ilustracji przedstawiono przykład sieci obwodowej pojedynczej podsieci w sieci firmowej, z dwóch granic zabezpieczeń.

![3]

Istnieje wiele architektur używaną do zaimplementowania sieci obwodowej. Te architektury mogą należeć do zakresu od modułu równoważenia obciążenia proste do sieci obwodowej wiele podsieci z różnych mechanizmów w każdej granicy bloku ruchu i ochronę głębiej warstwy sieci firmowej. Jak jest wbudowana w sieci obwodowej zależy od specyficznych potrzeb organizacji i jej ogólnej tolerancji ryzyka.

Jak klienci przenoszenie obciążeń do chmur publicznych, bardzo ważne jest Obsługa architektury sieci obwodowej na platformie Azure, aby spełnić wymagania dotyczące zabezpieczeń i zgodności podobne możliwości. Ten dokument zawiera wskazówki, w jaki sposób klienci mogą tworzyć środowisku bezpiecznej sieci na platformie Azure. Skupiono się w sieci obwodowej, ale również zawiera kompleksowe omówienie wiele aspektów zabezpieczeń sieci. Następujące pytania o tej dyskusji:

* Jak można wbudować sieci obwodowej na platformie Azure
* Co to są funkcje platformy Azure umożliwiających tworzenie sieci obwodowej?
* Jak można chronić obciążeń zaplecza
* Jak są kontrolowane komunikację z Internetem w przypadku obciążeń na platformie Azure?
* Jak sieci lokalnej mogą być chronione od wdrożenia na platformie Azure?
* Podczas funkcje zabezpieczeń platformy Azure natywnego powinny być używane lub innych urządzeń lub usług?

Na poniższym diagramie przedstawiono różnych warstw zabezpieczeń, które platforma Azure udostępnia klientom. Te warstwy są natywne w platformą Azure i funkcje zdefiniowane przez klientów:

![4]

Przychodzące z Internetu, przed atakami DDoS Azure pomaga chronić przed atakami na dużą skalę przed Azure. Kolejna warstwa jest zdefiniowany przez klienta publicznych adresów IP (punkty końcowe), które są używane do ustalenia, jaki ruch mogą przechodzić do sieci wirtualnej usługi w chmurze. Azure macierzystego wirtualnego izolacji sieci zapewnia pełnej izolacji od innych sieci i czy ruch przepływa tylko za pośrednictwem ścieżki użytkownika skonfigurowane i. Tych ścieżek i metody są następnej warstwy, których grup NSG, przez i wirtualnych urządzeń sieciowych może służyć do tworzenia granic zabezpieczeń do wdrożenia aplikacji w sieci chronionej ochrony.

Następna sekcja zawiera omówienie sieci wirtualnych platformy Azure. Te sieci wirtualne są tworzone przez klientów i są w ich wdrożonych obciążeń podłączonych do. Sieci wirtualne są podstawy wszystkich sieci funkcje zabezpieczeń wymagane do ustanowienia sieci obwodowej, aby chronić wdrożenia klienta na platformie Azure.

## <a name="overview-of-azure-virtual-networks"></a>Omówienie sieci wirtualnych platformy Azure
Zanim ruch internetowy może uzyskać dostęp do sieci wirtualnych platformy Azure, istnieją dwie warstwy zabezpieczeń związane z platformą Azure:

1.    **Ochrona przed atakami DDoS**: ochrona przed atakami DDoS to warstwa Azure sieci fizycznej, która chroni przed atakami na dużą skalę internetowego platformą Azure. Tego rodzaju ataki umożliwia wielu węzłów "bot" próbując przeciąży usługi internetowe. Platforma Azure ma niezawodne siatki ochrony przed atakami DDoS na wszystkie połączenia przychodzące, wychodzące i Azure między regionu. Ta warstwa ochrony przed atakami DDoS nie ma żadnych atrybutów można konfigurować użytkowników i nie jest dostępna dla klienta. Warstwę ochrony przed atakami DDoS chroni Azure jako platformę przed atakami na dużą skalę, również monitoruje ruchu wyjściowego powiązane i regionu Azure między ruchu. Przy użyciu sieci wirtualnych urządzeń w sieci wirtualnej, dodatkowe warstwy odporności można skonfigurować klienta przed mniejszych ataki skalowania, które nie rzeczy przed wyjazdem ochrony na poziomie platformy. Przykład DDoS w akcji; Jeśli internetowy adres IP zaatakowany przez na dużą skalę ataków DDoS, Azure może wykryć źródeł ataków i wyczyść ataku ruchu przed osiągnięciem jego przeznaczenia. W większości przypadków zaatakowane punkt końcowy nie ma wpływu na atak. W rzadkich przypadkach, że punkt końcowy ma wpływ żaden ruch ma wpływ na inne punkty końcowe, tylko zaatakowane punktu końcowego. W związku z tym innych klientów i usług zobaczy bez wpływu na tym ataki. Należy pamiętać tylko potrzebuje na dużą skalę ataków Azure DDoS. Istnieje możliwość, że określone usługi można przeciążony przed przekroczenia progów ochrony na poziomie platformy. Na przykład witryny sieci web na jednym serwerze A0 IIS, można podjąć w trybie offline takiego ataku przed zagrożeniem platformy Azure poziom ochrony przed atakami DDoS.

2.  **Publiczne adresy IP**: publicznego adresu IP adresów (włączonych za pomocą punktów końcowych usługi, publiczny adres IP adresy bramy aplikacji i innych funkcji platformy Azure, które są dostępne do publicznego adresu IP z Internetem kierowane do zasobu) Zezwalaj usługi w chmurze lub zasobu grupy mają publiczne adresy Internet IP i portów. Punkt końcowy używa translatora adresów sieciowych (NAT) do kierowania ruchem wewnętrzny adres i port na sieć wirtualna platformy Azure. Ta ścieżka jest podstawowy sposób dla zewnętrznego ruchu do przekazania do sieci wirtualnej. Publiczne adresy IP są konfigurowane w celu określenia, jaki ruch jest przekazywany w i jak i gdzie jest translacja do sieci wirtualnej.

Gdy ruch osiągnie sieci wirtualnej, istnieje wiele funkcji, które są dostarczane do odtwarzania. Sieci wirtualne Azure są podstawę dla klientów dołączyć ich obciążeń i których dotyczy podstawowych zabezpieczeń na poziomie sieci. Sieci prywatnej (w nakładce sieci wirtualnej) jest na platformie Azure dla klientów o następujące funkcje i właściwości:

* **Izolacja ruchu**: sieć wirtualna jest granica izolacji ruchu na platformie Azure. Maszynach wirtualnych (VM) w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta. Izolacja jest krytyczne właściwość, która zapewnia klienta maszyn wirtualnych i komunikacja pozostanie prywatnej sieci wirtualnej.

>[!NOTE]
>Izolacja ruchu odnosi się tylko do ruchu *przychodzących* do sieci wirtualnej. Domyślnie ruch wychodzący z sieci wirtualnej z Internetem jest dozwolona, ale w razie potrzeby przez grupy NSG można zapobiec.
>
>

* **Topologia wielowarstwowa**: sieci wirtualne umożliwiają klientom Definiowanie topologii wielowarstwowej przydzielając podsieci i wyznaczenie przestrzeni adresowych osobne dla różnych elementów lub "warstwy" ich obciążeń. Tych grup logicznych i topologii umożliwiają klientom Definiowanie zasad dostępu różnych na podstawie typów obciążenia, a także kontrolować ruch między warstwami.
* **Łączność między lokalizacjami**: klientów może nawiązać łączności między lokalizacjami między sieci wirtualnej i wieloma lokacjami lokalnymi lub innych sieci wirtualnych na platformie Azure. Do utworzenia połączenia, klienci mogą użyć sieci wirtualnej komunikacji równorzędnej, bram sieci VPN platformy Azure, wirtualnych urządzeń sieciowych innych firm lub ExpressRoute. Azure obsługuje za pomocą standardowych protokołów IPsec i IKE i prywatne połączenie ExpressRoute sieci VPN typu lokacja lokacja (S2S).
* **Grupa NSG** umożliwia klientom tworzenie zasady (kontroli dostępu ACL) na żądanym poziomie szczegółowości: sieci interfejsy poszczególnych maszyn wirtualnych i podsieci wirtualne. Klientów można kontrolować dostęp przez zezwalanie na dostęp lub odmawianie komunikacji między obciążeń w ramach sieci wirtualnej, od systemów w sieci klienta za pośrednictwem łączności między lokalizacjami lub bezpośrednia komunikacja z Internetem.
* **PRZEZ** i **przesyłania dalej protokołu IP** umożliwiają klientom Definiowanie ścieżki komunikacji między warstwami innej sieci wirtualnej. Klienci mogą wdrażać zaporą, Identyfikatory/adresów IP i inne urządzenia wirtualnego oraz kierować ruchem sieciowym za pomocą tych urządzeń zabezpieczeń do wymuszania zasad granic zabezpieczeń, inspekcji i inspekcji.
* **Sieci wirtualnych urządzeń** w portalu Azure Marketplace: urządzenia zabezpieczeń, takie jak zapory, moduły równoważenia obciążenia i Identyfikatory/adresów IP są dostępne w portalu Azure Marketplace i Galeria obrazów maszyny Wirtualnej. Klienci mogą wdrożyć te urządzenia w sieciach wirtualnych, a w szczególności na ich granic zabezpieczeń (w tym podsieci sieci obwodowej) do ukończenia bezpiecznym środowisku wielowarstwowych sieci.

Z tych funkcji i możliwości przykładem sposobu architektury sieci obwodowej mogła zostać zbudowana na platformie Azure jest poniższym diagramie:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Właściwości sieci obwodowej i wymagania
Sieci obwodowej jest to fronton sieci bezpośrednio relacje komunikacji z Internetu. Pakiety przychodzące powinny przepływać przez urządzenia zabezpieczeń, takie jak zapory, Identyfikatory oraz adresów IP, zanim dotrze do serwerów zaplecza. Pakiety powiązane z Internetu od obciążeń można również przepływać przez urządzenia zabezpieczeń w sieci obwodowej do wymuszania zasad inspekcji i celów inspekcji przed opuszczeniem sieci. Ponadto sieci obwodowej może obsługiwać bramy sieci VPN między lokalizacjami między sieci wirtualne klienta i sieci lokalnej.

### <a name="perimeter-network-characteristics"></a>Właściwości sieci obwodowej
Tworzenie odwołań na poprzedniej ilustracji, niektóre właściwości sieci obwodowej dobrej są następujące:

* Internetowy:
  * Samej podsieci sieci obwodowej jest skierowane do Internetu, bezpośrednio komunikacji z Internetem.
  * Publiczne adresy IP, adresy VIP i/lub punktów końcowych usługi przekazać ruch internetowy do sieci frontonu i urządzeń.
  * Ruch przychodzący z Internetu przechodzi przez urządzenia zabezpieczeń przed innymi zasobami w sieci frontonu.
  * Włączenie zabezpieczeń dla ruchu wychodzącego ruchu przechodzi przez urządzeń zabezpieczeń, co stanowi ostatni krok przed przekazaniem do Internetu.
* Sieć chroniona:
  * Brak bezpośrednią ścieżkę z Internetu do podstawowej infrastruktury.
  * Kanały w infrastrukturze podstawowej muszą przejść za pomocą urządzeń zabezpieczeń, takich jak grupy NSG, zapory lub urządzenia sieci VPN.
  * Inne urządzenia nie musi zestawiania Internet i infrastrukturze podstawowej.
  * Urządzenia zabezpieczeń na skierowane do Internetu i sieci chronionego ukierunkowane granic sieci obwodowej (na przykład dwa zapory ikony na poprzedniej ilustracji) może być faktycznie pojedynczego urządzenia wirtualnego przy użyciu reguł zróżnicowanych lub interfejsów dla każdej granicy. Na przykład jeden urządzenie fizyczne, logicznie rozdzielone, obsługi obciążenia dla obu granic sieci obwodowej.
* Inne typowe wskazówki i ograniczenia:
  * Obciążeń nie mogą przechowywać newralgicznych danych biznesowych.
  * Aktualizacje konfiguracji sieci obwodowej i wdrożeń i dostępu są ograniczone do tylko autoryzowani administratorzy.

### <a name="perimeter-network-requirements"></a>Wymagania dotyczące sieci obwodowej
Aby włączyć tę charakterystykę, zgodna z tymi wytycznymi na wymagania dotyczące sieci wirtualnej do zaimplementowania sieci obwodowej powiodło się:

* **Architektura podsieci:** Określ sieć wirtualną tak, aby w całej podsieci dedykowanego jako sieci obwodowej, oddzielona od innych podsieci w tej samej sieci wirtualnej. Ta separacja zapewnia ruchem między siecią obwodową i innych przepływów warstw wewnętrznego lub prywatnego podsieci za pośrednictwem zapory lub urządzenia wirtualnego Identyfikatory/adresów IP.  Trasy zdefiniowane przez użytkownika w podsieciach granic są wymagane do przesyłania tego ruchu do urządzenia wirtualnego.
* **Grupa NSG:** samej podsieci sieci obwodowej powinno być otwarte, aby umożliwić komunikację z Internetem, ale nie oznacza klientów należy pomijanie grup NSG. Wykonaj typowe wskazówki dotyczące zabezpieczeń, aby zminimalizować powierzchni sieci, połączenie z Internetem. Zablokowanie zakresów adresów zdalnych dozwolony dostęp do wdrożeń lub określonej aplikacji protokoły i porty, które są otwarte. Może to być sytuacji, w których pełną blokowanie nie jest możliwe. Na przykład, jeśli klienci mają zewnętrznej witryny sieci Web na platformie Azure, sieci obwodowej powinna zezwalać na przychodzące żądania sieci web z publicznych adresów IP, ale należy otwierać tylko porty aplikacji sieci web: ruch TCP na porcie 80 i/lub TCP na porcie 443.
* **Tabela routingu:** samej podsieci sieci obwodowej mogła komunikować się bezpośrednio z Internetem, ale nie należy zezwalać bezpośrednia komunikacja do i z tyłu zakończenia lub lokalnej sieci bez przechodzenia przez zaporę lub zabezpieczeń urządzenia.
* **Konfiguracja urządzenia zabezpieczeń:** do routingu i inspekcję pakietów między siecią obwodową i pozostałej części chronionej sieci, urządzenia zabezpieczeń, takie jak zapory, Identyfikatory, i adresy IP urządzeń mogą być wieloadresowych. Mogą mieć oddzielnych kart sieciowych dla sieci obwodowej i podsieci wewnętrznej. Karty sieciowe w sieci obwodowej komunikują się bezpośrednio do i z Internetu, z odpowiednich grup NSG i tabelę routingu w sieci obwodowej. Karty sieciowe, nawiązywanie połączeń z podsieciami zaplecza więcej ograniczonym grup NSG i tabelach routingu odpowiednie podsieci wewnętrznej.
* **Funkcje urządzenia zabezpieczeń:** urządzenia zabezpieczeń wdrożonego w sieci obwodowej zwykle wykonać następujące funkcje:
  * Zapora: Wymuszania reguł zapory lub zasady kontroli dostępu dla żądań przychodzących.
  * Zagrożenia i zapobiegania: wykrywanie i zmniejszenia złośliwych ataków z Internetu.
  * Inspekcja i rejestrowanie: Obsługa szczegółowe dzienniki inspekcji i analizy.
  * Odwrotny serwer proxy: przekierowywanie żądań przychodzących do odpowiednich serwerów zaplecza. Przekierowanie obejmuje mapowania i zwykle translacji adresów docelowych na urządzeniach z frontonu zapory na adresy serwerów zaplecza.
  * Przekazuj serwera proxy: zapewnianie NAT i przeprowadzania inspekcji dla komunikacji inicjowane przy użyciu sieci wirtualnej z Internetem.
  * Router: Przesyłania ruchu przychodzącego i różnych podsieci w sieci wirtualnej.
  * Urządzenia sieci VPN: działający jako bramy sieci VPN między lokalizacjami dla połączenia sieci VPN między lokalizacjami między sieciami lokalnymi klientów i sieci wirtualnych platformy Azure.
  * Serwer sieci VPN: akceptowanie klientów sieci VPN nawiązywania połączenia z sieciami wirtualnymi platformy Azure.

> [!TIP]
> Zachowaj oddzielne następujących dwóch grup: osób autoryzowany dostęp do narzędzi zabezpieczeń sieci obwodowej i osoby autoryzowane jako administratorzy programowanie, wdrożenia i działania aplikacji. Oddzieleniu tych grup umożliwia podział obowiązków i uniemożliwia jedna osoba z pominięciem aplikacji, zabezpieczeń i kontroli zabezpieczeń sieci.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Pytania pojawi się pytanie, podczas tworzenia granic sieci
W tej sekcji chyba że wyraźnie wymienionych termin "sieci" odwołuje się do prywatnej sieci wirtualnych platformy Azure utworzonych przez administratora subskrypcji. Termin nie odwołuje się do podstawowej sieci fizycznej w obrębie platformy Azure.

Ponadto sieci wirtualnych platformy Azure są często używane do rozszerzania sieci tradycyjnych, lokalnie. Istnieje możliwość uwzględnienia lokacja lokacja lub ExpressRoute hybrydowych rozwiązań sieciowych z architektury sieci obwodowej. To łącze hybrydowego jest ważną kwestią tworzenia granic zabezpieczeń sieci.

Następujące trzy pytania są krytyczne odpowiadać, gdy tworzysz sieci z siecią obwodową a wielu granic zabezpieczeń.

#### <a name="1-how-many-boundaries-are-needed"></a>1) ile granice są potrzebne?
Pierwszy punkt decyzji jest decyzji o tym, jak wiele granic zabezpieczeń są potrzebne w danej sytuacji:

* Pojedyncza granica: w sieci obwodowej frontonu sieci wirtualnej od Internetu.
* Granice dwóch: jeden po stronie Internetu sieci obwodowej, a drugi między podsieci sieci obwodowej i podsieci zaplecza w sieci wirtualnej platformy Azure.
* Trzy granice: na stronie Internetu w sieci obwodowej, między siecią obwodową a podsieci wewnętrznej, a drugi między podsieciami zaplecza i sieci lokalnej.
* Granice N: Numer zmiennej. W zależności od wymagań dotyczących zabezpieczeń jest nieograniczona liczba granic zabezpieczeń, które mogą być stosowane w danej sieci.

Liczba i typ potrzebne różnią się granic na podstawie tolerancji ryzyka firmy i danego scenariusza implementowana. Ta decyzja często staje się wraz z wielu grup w organizacji, często tym zespół ryzyka i zgodności, sieć i zespołu platformy i zespół deweloperów aplikacji. Osobom wiedzę na temat zabezpieczeń, dane związane i technologie używane powinny mieć powiedzieć w tej decyzji w celu zapewnienia zasobów odpowiednich ustawień zabezpieczeń dla poszczególnych implementacji.

> [!TIP]
> Użyj najmniejszą liczbę granice, które spełniają wymagania dotyczące zabezpieczeń w danej sytuacji. Z granice więcej operacji i rozwiązywanie problemów może być trudniejsze, a także zarządzania obciążenie związane z zarządzaniem wiele zasad granic wraz z upływem czasu. Jednak za mało granic zwiększenie ryzyka. Znajdowanie saldo jest krytyczna.
>
>

![6]

Powyższej ilustracji przedstawiono ogólny widok sieci granicznej zabezpieczeń trzy. Granice należą do zakresu od sieci obwodowej i Internetu, Azure frontonu i zaplecza prywatne podsieci i podsieci wewnętrznej platformy Azure i lokalnymi sieci firmowej.

#### <a name="2-where-are-the-boundaries-located"></a>2) granice lokalizację?
Liczba granic są decyzję, gdzie ich wdrażania po następnym podjęcie decyzji. Zazwyczaj są trzy opcje:

* Przy użyciu Internetowych usług pośredniczących (na przykład oparte na chmurze Zapora aplikacji sieci Web, który nie został omówiony w niniejszym dokumencie)
* Przy użyciu funkcji macierzystego i/lub wirtualnych urządzeń sieciowych na platformie Azure
* Za pomocą urządzeń fizycznych w sieci lokalnej

W sieciach czysto Azure opcje są natywnego funkcje platformy Azure (na przykład równoważenia obciążenia Azure) lub wirtualnych urządzeń sieciowych z ekosystemem partnerów sformatowanego platformy Azure (na przykład zapory Check Point).

Jeśli granica jest potrzebny platformy Azure i siecią lokalną, urządzenia zabezpieczeń może znajdować się po obu stronach połączenia (lub obu stronach). W związku z tym decyzji muszą być wprowadzane w lokalizacji do umieszczenia koło zębate zabezpieczeń.

Na poprzedniej ilustracji sieci Internet granicznej granice przodu do tyłu zakończenia całkowicie znajdują się w obrębie platformy Azure i musi być natywnego funkcje platformy Azure lub sieci wirtualnych urządzeń. Urządzenia zabezpieczeń w granicach platformy Azure (podsieci zaplecza) i sieci firmowej można Azure po stronie lub stronie lokalnymi lub nawet kombinację urządzeń po obu stronach. Może to być istotne zalety i wady do obu opcji, które należy rozważyć poważnie.

Na przykład przy użyciu istniejącego sprzętu fizycznego zabezpieczeń po stronie sieci lokalnej ma tę zaletę, że niezbędna jest żadnych nowych narzędzi. Wystarczy ponownej konfiguracji. Jednak wadą jest to, że cały ruch musi wrócić z platformy Azure do sieci lokalnej, aby była widoczna narzędzi zabezpieczeń. W związku z tym ruchu Azure do platformy Azure może pociągnąć za sobą znaczne opóźnienia i wpłynąć na wydajność aplikacji i środowisko, jeśli była powrotem wymuszenie sieci lokalnej w celu wymuszenia zasad zabezpieczeń.

#### <a name="3-how-are-the-boundaries-implemented"></a>3) granice implementowania?
Każdej granicy zabezpieczeń najprawdopodobniej będzie miała wymagania różne możliwości (na przykład identyfikatorów i reguły zapory po stronie Internetu sieci obwodowej, ale tylko listy ACL między siecią obwodową a podsieci wewnętrznej). Przy wyborze, na którym urządzenie (lub urządzenia, ile) do użycia zależy od wymagań scenariusza i zabezpieczeń. W poniższej sekcji Przykłady 1, 2 i 3 omówiono niektóre opcje, które mogłyby zostać użyte. Przegląd funkcji natywnego sieć platformy Azure i dostępnej na platformie Azure z ekosystemem partnerów urządzeń zawiera wiele opcji do rozwiązania praktycznie dowolne scenariusza.

Inną kwestią implementacji klucza jest sposób nawiązywania połączenia z siecią lokalną przy użyciu platformy Azure. Należy użyć Azure wirtualnej bramy lub urządzenie wirtualne sieci? Te opcje omówiono bardziej szczegółowo w następnej sekcji (przykłady 4, 5 i 6).

Ponadto ruchu między sieciami wirtualnymi w obrębie platformy Azure mogą być wymagane. Te scenariusze zostaną dodane w przyszłości.

Jeśli znasz już odpowiedzi na pytania Wstecz [szybkiego startu](#fast-start) sekcji mogą ułatwić identyfikację, przykłady, które są najbardziej odpowiednie dla danego scenariusza.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Przykłady: Tworzenie granic zabezpieczeń z sieci wirtualnych platformy Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Przykład 1 kompilacji sieci obwodowej, aby pomóc w ochronie aplikacji za pomocą grup NSG
[Powrót do szybki start](#fast-start) | [szczegółowy kompilacji instrukcje w tym przykładzie][Example1]

[![7]][7]

#### <a name="environment-description"></a>Opis elementu środowiska
W tym przykładzie jest subskrypcji, która zawiera następujące zasoby:

- Pojedyncza grupa zasobów
- Sieć wirtualną z dwiema podsieciami: "FrontEnd" i "Wewnętrzna"
- Grupy zabezpieczeń sieci jest stosowany do obu podsieci
- Windows server, który reprezentuje serwer sieci web aplikacji ("IIS01")
- Dwa serwery systemu Windows, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
- Windows server, który reprezentuje serwer DNS ("DNS01")
- Publicznego adresu IP skojarzonego z serwerem sieci web aplikacji

Skrypty i szablonu usługi Azure Resource Manager, zobacz [szczegółowe instrukcje kompilacji][Example1].

#### <a name="nsg-description"></a>Opis grupy NSG
W tym przykładzie grupy NSG jest wbudowana i następnie ładowane przy użyciu sześciu reguł.

> [!TIP]
> Ogólnie rzecz biorąc należy utworzyć określone zasady "Zezwalaj" najpierw, a następnie bardziej ogólnym reguły "Deny". Podana wartość priorytetu decyduje, jakie reguły są sprawdzane jako pierwsze. Po znalezieniu ruchu do zastosowania do określonej reguły nie dalsze reguły są sprawdzane. Reguły NSG można zastosować w ruchu przychodzącego lub wychodzącego kierunku (z punktu widzenia podsieci).
>
>

Deklaratywnie są tworzone następujące reguły dla ruchu przychodzącego:

1. Wewnętrzny ruch DNS (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do żadnej maszyny wirtualnej jest dozwolony.
3. Ruch HTTP (port 80) z Internetu do serwera sieci web (IIS01) jest dozwolony.
4. Cały ruch (wszystkie porty) z IIS01 do AppVM1 jest dozwolone.
5. Odmowa cały ruch (wszystkie porty) z Internetu do całej sieci wirtualnej (obie podsieci).
6. Odmowa cały ruch (wszystkie porty) z podsieci frontonu do podsieci wewnętrznej.

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało przychodzące z Internetu do serwera sieci web, obie zasady 3 (Zezwalaj) i 5 (odmówić go) powinna zostać zastosowana. Jednak ponieważ reguła 3 ma wyższy priorytet, tylko będą miały zastosowania i reguły 5 nie przybyły do odtwarzania. W związku z tym żądania HTTP będą dozwolone na serwerze sieci web. Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, zasada 5 (odmówić go) może być pierwszym do zastosowania, a ruch będzie niedozwolone do przekazania na serwer. Reguła 6 (odmówić go) — bloki podsieci frontonu z rozmowie z podsieci zaplecza (z wyjątkiem dozwolonego ruchu w regułach 1 i 4). Ten zestaw reguł chroni sieci wewnętrznej, w przypadku, gdy osoba atakująca obniża aplikacji sieci web na frontonie. Osoba atakująca może mieć ograniczony dostęp do sieci "chronionych" zaplecza (tylko dla zasobów udostępniane na serwerze AppVM01).

Brak domyślnej regule wychodzącej, która umożliwia ruchu wychodzącego do Internetu. Na przykład firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie reguł wychodzących. Do blokowania ruchu w obu kierunkach, routing zdefiniowane przez użytkownika jest wymagana (Zobacz przykład 3).

#### <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest względnie proste i bezpośrednie sposobem izolowanie podsieci wewnętrznej z ruchu przychodzącego. Aby uzyskać więcej informacji, zobacz [szczegółowe instrukcje kompilacji][Example1]. Instrukcje te obejmują:

* Sposób tworzenia tej sieci obwodowej z klasycznym skryptów programu PowerShell.
* Sposób tworzenia tej sieci obwodowej z szablonem usługi Azure Resource Manager.
* Szczegółowy opis każdego polecenia NSG.
* Scenariusze przepływu ruchu szczegółowe, przedstawiający sposób ruch jest dozwolony lub zabroniony w każdej warstwie.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Przykład 2 kompilacji sieci obwodowej, aby chronić aplikacje z zaporą i grupy NSG
[Powrót do szybki start](#fast-start) | [szczegółowy kompilacji instrukcje w tym przykładzie][Example2]

[![8]][8]

#### <a name="environment-description"></a>Opis elementu środowiska
W tym przykładzie jest subskrypcji, która zawiera następujące zasoby:

* Pojedyncza grupa zasobów
* Sieć wirtualną z dwiema podsieciami: "FrontEnd" i "Wewnętrzna"
* Grupy zabezpieczeń sieci jest stosowany do obu podsieci
* Urządzenie wirtualne sieci, w tym przypadku a zapory, podłączone do podsieci frontonu
* Windows server, który reprezentuje serwer sieci web aplikacji ("IIS01")
* Dwa serwery systemu Windows, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Windows server, który reprezentuje serwer DNS ("DNS01")

Skrypty i szablonu usługi Azure Resource Manager, zobacz [szczegółowe instrukcje kompilacji][Example2].

#### <a name="nsg-description"></a>Opis grupy NSG
W tym przykładzie grupy NSG jest wbudowana i następnie ładowane przy użyciu sześciu reguł.

> [!TIP]
> Ogólnie rzecz biorąc należy utworzyć określone zasady "Zezwalaj" najpierw, a następnie bardziej ogólnym reguły "Deny". Podana wartość priorytetu decyduje, jakie reguły są sprawdzane jako pierwsze. Po znalezieniu ruchu do zastosowania do określonej reguły nie dalsze reguły są sprawdzane. Reguły NSG można zastosować w ruchu przychodzącego lub wychodzącego kierunku (z punktu widzenia podsieci).
>
>

Deklaratywnie są tworzone następujące reguły dla ruchu przychodzącego:

1. Wewnętrzny ruch DNS (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do żadnej maszyny wirtualnej jest dozwolony.
3. Cały ruch Internet (wszystkie porty) do sieci (zapora) urządzenia wirtualnego jest dozwolone.
4. Cały ruch (wszystkie porty) z IIS01 do AppVM1 jest dozwolone.
5. Odmowa cały ruch (wszystkie porty) z Internetu do całej sieci wirtualnej (obie podsieci).
6. Odmowa cały ruch (wszystkie porty) z podsieci frontonu do podsieci wewnętrznej.

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało przychodzące z Internetu do zapory obie zasady 3 (Zezwalaj) i 5 (odmówić go) powinna zostać zastosowana. Jednak ponieważ reguła 3 ma wyższy priorytet, tylko będą miały zastosowania i reguły 5 nie przybyły do odtwarzania. W związku z tym żądania HTTP mogliby zapory. Jeśli ten sam ruch próbował uzyskać dostęp do serwera IIS01, nawet jeśli komputer jest w tej podsieci frontonu, zasada 5 (odmówić go) powinna zostać zastosowana, a ruch będzie niedozwolone do przekazania na serwer. Reguła 6 (odmówić go) — bloki podsieci frontonu z rozmowie z podsieci zaplecza (z wyjątkiem dozwolonego ruchu w regułach 1 i 4). Ten zestaw reguł chroni sieci wewnętrznej, w przypadku, gdy osoba atakująca obniża aplikacji sieci web na frontonie. Osoba atakująca może mieć ograniczony dostęp do sieci "chronionych" zaplecza (tylko dla zasobów udostępniane na serwerze AppVM01).

Brak domyślnej regule wychodzącej, która umożliwia ruchu wychodzącego do Internetu. Na przykład firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie reguł wychodzących. Do blokowania ruchu w obu kierunkach, routing zdefiniowane przez użytkownika jest wymagana (Zobacz przykład 3).

#### <a name="firewall-rule-description"></a>Opis reguły zapory
W zaporze powinny być tworzone zasady przekazywania. Ponieważ w tym przykładzie tylko kieruje ruch internetowy w powiązany z zapory, a następnie do serwera sieci web tylko jednego przekazującego sieci translacji adresów (NAT) jest wymagany regułę.

Reguła przekazywania akceptuje dowolny źródłowy dla ruchu przychodzącego adres, który trafienia zapory próba nawiązania połączenia HTTP (port 80 i 443 dla protokołu HTTPS). Ma ona wysyłane poza interfejsu lokalnego zapory i Przekierowanie do serwera sieci web z adresem IP 10.0.1.5.

#### <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest względnie łatwe ochronie aplikacji za pomocą zapory i izolowanie podsieci wewnętrznej z ruchu przychodzącego. Aby uzyskać więcej informacji, zobacz [szczegółowe instrukcje kompilacji][Example2]. Instrukcje te obejmują:

* Sposób tworzenia tej sieci obwodowej z klasycznym skryptów programu PowerShell.
* Jak utworzyć w tym przykładzie z szablonem usługi Azure Resource Manager.
* Szczegółowy opis każdego polecenia i zapory reguły NSG.
* Scenariusze przepływu ruchu szczegółowe, przedstawiający sposób ruch jest dozwolony lub zabroniony w każdej warstwie.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Przykład 3 kompilacji sieci obwodowej, aby zabezpieczyć sieć zapory i przez i grupy NSG
[Powrót do szybki start](#fast-start) | [szczegółowy kompilacji instrukcje w tym przykładzie][Example3]

[![9]][9]

#### <a name="environment-description"></a>Opis elementu środowiska
W tym przykładzie jest subskrypcji, która zawiera następujące zasoby:

* Pojedyncza grupa zasobów
* Sieć wirtualną przy użyciu trzech podsieci: "SecNet", "Fronton" i "Wewnętrzna"
* Urządzenie wirtualne sieci, w tym przypadku a zapory, połączony z podsiecią SecNet
* Windows server, który reprezentuje serwer sieci web aplikacji ("IIS01")
* Dwa serwery systemu Windows, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Windows server, który reprezentuje serwer DNS ("DNS01")

Skrypty i szablonu usługi Azure Resource Manager, zobacz [szczegółowe instrukcje kompilacji][Example3].

#### <a name="udr-description"></a>Opis elementu przez
Domyślnie następujące trasy systemowe są zdefiniowane jako:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal jest zawsze prefiksy adresów zdefiniowanych, wchodzące w skład sieci wirtualnej dla określonej sieci (czyli jego zmiany z sieci wirtualnej do sieci wirtualnej, w zależności od tego, jak zdefiniowano każdej określonej sieci wirtualnej). Pozostałe tras systemowych są statyczne i domyślne określonych w tabeli.

W tym przykładzie dwie tabele routingu są tworzone, jeden dla podsieci frontonu i zaplecza. Każda tabela została załadowana z tras statycznych, które są odpowiednie dla danej podsieci. W tym przykładzie każda tabela zawiera trzy tras, na których kierować cały ruch (0.0.0.0/0) za pośrednictwem zapory (następnego przeskoku = adres IP urządzenia wirtualnego):

1. Ruchu w podsieci lokalnej z następnego przeskoku określone, aby zezwolić na ruch podsieci lokalnej ominąć zaporę.
2. Ruchu w sieci wirtualnej z przeskoku dalej zdefiniowany jako zapory. Ta następnego przeskoku przesłania domyślna reguła, która umożliwia lokalne ruchu w sieci wirtualnej do routingu bezpośredniego.
3. Cały ruch pozostałych (0/0) z przeskoku dalej zdefiniowany jako zapory.

> [!TIP]
> W przypadku komunikacji podsieci lokalnej podziały przez nie ma wpisu podsieci lokalnej.
>
> * W naszym przykładzie 10.0.1.0/24 wskazujący VNETLocal jest krytyczny! Bez tego pakietu, pozostawiając serwera sieci Web (10.0.1.4) przeznaczone na inny serwer lokalny (na przykład) 10.0.1.25 wystąpi błąd, ponieważ będą wysyłane do analizę NVA. Analizę NVA wysyła go do podsieci, a podsieć zostanie ponownie do NVA w pętli nieskończonej.
> * Ryzyko Pętla routingu są zwykle wyższe, na urządzeniach z wieloma kartami sieciowymi podłączonymi do oddzielania podsieci, która jest często tradycyjnych, lokalnie urządzeń.
>
>

Po utworzeniu tabele routingu, musi być powiązana z ich podsieci. Podsieci frontonu tabeli routingu, raz utworzony i powiązany z podsiecią wyglądałyby tak jak te dane wyjściowe:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> Teraz można zastosować przez do podsieci bramy, na którym jest połączony z obwodem usługi ExpressRoute.
>
> Przykłady sposobu włączania sieci obwodowej za pomocą programu ExpressRoute lub lokacja lokacja sieci przedstawiono w przykładach 3 i 4.
>
>

#### <a name="ip-forwarding-description"></a>Opis usługi przesyłania dalej protokołu IP
Przekazywanie adresów IP jest przez funkcję pomocnika. Przekazywanie adresów IP to ustawienie na urządzenie wirtualne, które pozwala na odbieranie ruchu kierowanego nie specjalnie do urządzenia, a następnie przesyła ten ruch do końcowego miejsca docelowego.

Na przykład jeśli AppVM01 wysyła żądanie do serwera DNS01, przez może kierować ruchem tej zapory. Z przesyłania dalej protokołu IP włączone ruchu dla miejsca docelowego DNS01 (10.0.2.4) jest akceptowane przez urządzenia (10.0.0.4) i następnie przekazywane do końcowego miejsca docelowego (10.0.2.4). Bez przekazywania adresów IP jest włączona Zapora ruch może nie zostać zaakceptowane przez urządzenia, mimo że tabeli tras ma zapory w następnym skoku. Aby korzystać z urządzenia wirtualnego, bardzo ważne jest należy pamiętać o włączeniu wraz z przez przesyłania dalej protokołu IP.

#### <a name="nsg-description"></a>Opis grupy NSG
W tym przykładzie grupa NSG jest wbudowana i następnie ładowane przy użyciu jednej reguły. Ta grupa jest następnie powiązać tylko z podsieci frontonu i zaplecza (nie SecNet). Deklaratywnie budowanego następującą regułę:

* Odmowa cały ruch (wszystkie porty) z Internetu do całej sieci wirtualnej (wszystkie podsieci).

Mimo że grupy NSG są używane w tym przykładzie, głównym celem jest jako dodatkowej warstwy obrony przed ręcznej konfiguracji. Celem jest Blokuj cały ruch przychodzący z Internetu do frontonu lub zaplecza podsieci. Ruch powinny przepływać tylko za pośrednictwem podsieci SecNet do zapory (i następnie w razie potrzeby do podsieci frontonu lub zaplecza). Plus przy użyciu reguł przez w miejscu, dowolnego ruchu, który należy do podsieci frontonu lub zaplecza może być kierowane limit do zapory (dzięki użyciu przez). Zapora będzie wyświetlać ten ruch asymetrycznego przepływu i spowoduje pominięcie ruchu wychodzącego. W związku z tym są trzy warstwy zabezpieczeń, ochrony podsieci:

* Nie publiczny adres IP frontonu lub wewnętrznej bazy danych kart sieciowych.
* Grupy NSG zezwalających na ruch z Internetu.
* Ruch asymetrycznego usunięcie zapory.

Jeden punkt interesujące dotyczące grupy NSG, w tym przykładzie jest zawiera tylko jedną regułę, czyli odrzucanie ruch internetowy do całej sieci wirtualnej, w tym podsieci zabezpieczeń. Jednak ponieważ grupa NSG jest powiązany tylko z podsieci frontonu i zaplecza, reguła nie jest przetwarzana na ruch przychodzący z podsiecią zabezpieczeń. W związku z tym ruch przechodzi do podsieci zabezpieczeń.

#### <a name="firewall-rules"></a>Reguły zapory
W zaporze powinny być tworzone zasady przekazywania. Ponieważ Zapora jest blokowanie lub przekazywania wszystkich przychodzące, wychodzące i ruchu w sieci wirtualnej w obrębie, wiele reguł zapory są wymagane. Ponadto cały ruch przychodzący trafień usługi zabezpieczeń publicznego adresu IP (na różnych portów), do przetworzenia przez zaporę. Najlepszym rozwiązaniem jest diagram logiczny przepływów przed rozpoczęciem konfigurowania podsieci i reguły zapory, aby uniknąć zmian później. Poniższa ilustracja jest widok logiczny reguły zapory, w tym przykładzie:

![10]

> [!NOTE]
> Oparte na urządzenie wirtualne sieci używana, porty zarządzania różnią się. W tym przykładzie zapory NextGen Barracuda odwołuje się, który używa portów 22, 801 i 807. Zajrzyj do dokumentacji dostawcy urządzenia, aby znaleźć odpowiednie porty używane do zarządzania urządzeniami używana.
>
>

#### <a name="firewall-rules-description"></a>Opis reguł zapory
Na powyższym diagramie logiczne podsieci zabezpieczeń nie jest wyświetlane, ponieważ Zapora jest tylko zasobów tej podsieci. Diagram jest pokazywany reguły zapory i sposób ich logicznie akceptować lub odrzucać przepływów ruchu sieciowego, nie rzeczywistej ścieżce routingiem. Ponadto zewnętrzne porty wybrany na potrzeby ruchu protokołu RDP są wyższe ranged portów (8014 — 8026) i wybrano słabo uzgodnić z ostatnich dwóch oktety lokalny adres IP dla czytelności łatwiejsze (na przykład adres serwera lokalnego 10.0.1.4 jest skojarzony z portu zewnętrznego 8014). Wszystkie porty wyższej niekolidujących, mogą zostać wykorzystane.

W tym przykładzie potrzebne są nam siedem typów zasad:

* Reguły zewnętrzne (dla ruchu przychodzącego):
  1. Reguły zapory zarządzania: ten przekierowania aplikacji reguła zezwala na ruch do przekazania do portów zarządzania urządzenie wirtualne sieci.
  2. Zasady protokołu RDP (na każdym serwerze z systemem Windows): Zezwalaj na zarządzanie poszczególnych serwerów za pomocą protokołu RDP te cztery reguły (po jednym dla każdego serwera). Cztery reguły protokołu RDP może również zostać zwinięty w jednej reguły, w zależności od możliwości urządzenie wirtualne sieci używane.
  3. Reguły ruchu aplikacji: istnieją dwa z tych zasad, pierwszy dla ruchu frontonu sieci web, a drugi dla ruchu w sieci wewnętrznej (na przykład serwer sieci web do warstwy danych). Konfiguracja tych zasad jest zależna od architektury sieci (w którym są umieszczane serwerów) i przepływów ruchu (kierunku przepływów ruchu sieciowego i portów, do których są używane).
     * Pierwsza reguła zezwala na ruch rzeczywisty aplikacji do serwera aplikacji. Zezwalaj na inne zasady zabezpieczeń i zarządzania, reguły ruchu aplikacji są co Zezwalaj użytkowników zewnętrznych lub usług w celu uzyskania dostępu do aplikacji. Ten przykład jest jednym serwerze sieci web na porcie 80. W związku z tym pojedynczą aplikacji regułę zapory przekierowuje ruch przychodzący do zewnętrznego adresu IP do sieci web serwerów wewnętrzny adres IP. Sesja przekierowywania ruchu czy translacji za pośrednictwem translatora adresów Sieciowych do wewnętrznego serwera.
     * Drugą regułę jest zaplecza reguły zezwalającej na serwerze sieci web komunikował się z serwerem AppVM01 (ale nie AppVM02) za pomocą dowolnego portu.
* Reguły wewnętrzne (dla ruchu w sieci wirtualnej w obrębie)
  1. Ruch wychodzący do Internetu reguły: Ta reguła zezwala na ruch z żadną siecią do przekazania do wybranych sieci. Ta reguła jest zwykle domyślna reguła już w zaporze, ale w stanie wyłączenia. Ta reguła powinna być włączona w tym przykładzie.
  2. Reguła DNS: Ta reguła zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska większość ruchu z frontonu do wewnętrznej jest zablokowany. Ta zasada umożliwia w szczególności DNS z żadnych podsieci lokalnej.
  3. Podsieci do reguły podsieci: Ta reguła jest umożliwienie dowolnego serwera w podsieci wewnętrznej w celu połączenia z serwerem z podsieci frontonu (ale nie odwrotnie).
* Reguła awaryjnego (dla ruchu, który nie spełnia żadnego z poprzedniej):
  1. Odmów wszystkie reguły ruchu: Ta reguła odmowy zawsze powinna być końcowego reguły (pod względem priorytetu) oraz jako takie Jeśli przepływ ruchu, nie pasuje do żadnego z poprzedniego zasady zostanie usunięte przez tę regułę. Ta reguła jest regułą domyślnego i zwykle w miejscu i aktywne. Nie zmiany zwykle są niezbędne do tej reguły.

> [!TIP]
> Na drugą regułę ruchu aplikacji do uproszczenia w tym przykładzie każdy port jest dozwolone. W rzeczywistym scenariuszu najbardziej określonego portu i zakresy adresów należy zmniejszyć obszar ataków tej reguły.
>
>

Po utworzeniu poprzednich zasad, należy przejrzeć priorytet każdej reguły, aby upewnić się, ruch jest dozwolony lub zabroniony zgodnie z potrzebami. Na przykład reguły są w kolejności priorytetu.

#### <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest bardziej złożonych ale ukończyć sposób ochrony i izolowania sieci niż w poprzednich przykładach. (Przykład 2 chroni tylko aplikacji i przykład 1 po prostu wyodrębnia podsieci). Ten projekt umożliwia monitorowanie ruchu w obu kierunkach i chroni nie tylko serwer aplikacji dla ruchu przychodzącego, ale wymusza zasady zabezpieczeń sieci dla wszystkich serwerów w tej sieci. Ponadto w zależności od urządzenia używane pełne ruchu inspekcji i świadomości można osiągnąć. Aby uzyskać więcej informacji, zobacz [szczegółowe instrukcje kompilacji][Example3]. Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej z klasycznym skryptów programu PowerShell.
* Jak utworzyć w tym przykładzie z szablonem usługi Azure Resource Manager.
* Szczegółowe opisy każdej przez NSG polecenia, a reguły zapory.
* Scenariusze przepływu ruchu szczegółowe, przedstawiający sposób ruch jest dozwolony lub zabroniony w każdej warstwie.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Przykład 4 Dodaj połączenie hybrydowe z lokacja lokacja, wirtualne urządzenia sieci VPN
[Powrót do szybki start](#fast-start) | Szczegółowe instrukcje kompilacji jest dostępny wkrótce

[![11]][11]

#### <a name="environment-description"></a>Opis elementu środowiska
Sieci hybrydowe przy użyciu urządzenie wirtualne sieci (NVA) można dodać do dowolnego typu sieci obwodowej, opisane w przykładach 1, 2 lub 3.

Jak pokazano na poprzedniej ilustracji, połączenie sieci VPN za pośrednictwem Internetu (site-to-site) jest używana sieć wirtualna platformy Azure za pośrednictwem NVA nawiązać połączenia z siecią lokalną.

> [!NOTE]
> Jeśli używasz usługi ExpressRoute z włączoną opcją publicznej komunikacji równorzędnej platformy Azure, należy utworzyć tras statycznych. Tej trasy statyczne powinny kierować na adres IP sieci VPN NVA firmowej Internetu, a nie za pośrednictwem połączenia ExpressRoute. Translator adresów Sieciowych wymaganych opcji Azure publicznej komunikacji równorzędnej ExpressRoute mogą być dzielone sesji sieci VPN.
>
>

Po umieszczeniu sieć VPN w miejscu analizę NVA staje się Centrum dla wszystkich sieci i podsieci. Zasady przekazywania zapory określają przepływów ruchu sieciowego, które są dozwolone są tłumaczone za pośrednictwem translatora adresów Sieciowych, są przekierowywane albo są usuwane (nawet w przypadku przepływów ruchu sieciowego między siecią lokalną a platformą Azure).

Ruch należy rozważyć dokładnie, jak mogą być optymalizowane lub ograniczone przez ten wzorzec projektowania, w zależności od konkretnych przypadek użycia.

Przy użyciu wbudowanych w przykładzie 3 środowiska, a następnie dodanie połączenia sieciowego hybrydowego sieci VPN typu lokacja lokacja, tworzy następującego projektu:

[![12]][12]

Router lokalny lub innego urządzenia sieciowego, zgodny z Twojej NVA dla sieci VPN, byłoby klienta sieci VPN. To fizyczne urządzenie będzie odpowiedzialny za inicjowanie i utrzymywania połączenia sieci VPN, z Twojego NVA.

Logicznie do NVA sieci wygląda cztery oddzielne "strefy zabezpieczeń" z zasadami na NVA jest podstawowym Dyrektor ruchu między tych stref:

![13]

#### <a name="conclusion"></a>Podsumowanie
Dodanie połączenia sieciowego hybrydowego site-to-site VPN do sieci wirtualnej platformy Azure można rozszerzyć sieć lokalną na platformie Azure w bezpieczny sposób. Korzystając z połączenia sieci VPN, ruchu są szyfrowane i kieruje za pośrednictwem Internetu. Analizę NVA w tym przykładzie zapewnia centralnej lokalizacji, aby wymusić i zarządzania zasadami zabezpieczeń. Aby uzyskać więcej informacji zobacz instrukcje szczegółowe kompilacji (nadchodzącego). Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej za pomocą skryptów środowiska PowerShell.
* Jak utworzyć w tym przykładzie z szablonem usługi Azure Resource Manager.
* Scenariusze przepływu ruchu szczegółowe, przedstawiający sposób ruch przechodzi przez ten projekt.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Przykład 5 Dodaj połączenie hybrydowe z bramą sieci VPN platformy Azure do lokacji,
[Powrót do szybki start](#fast-start) | Szczegółowe instrukcje kompilacji jest dostępny wkrótce

[![14]][14]

#### <a name="environment-description"></a>Opis elementu środowiska
Sieci hybrydowe przy użyciu bramy sieci VPN platformy Azure można dodać do dowolnego typu sieci obwodowej opisanego w przykładach 1 lub 2.

Jak pokazano na poprzedniej ilustracji, połączenie sieci VPN za pośrednictwem Internetu (site-to-site) służy do połączyć sieć lokalną sieć wirtualną Azure za pośrednictwem bramy sieci VPN platformy Azure.

> [!NOTE]
> Jeśli używasz usługi ExpressRoute z włączoną opcją publicznej komunikacji równorzędnej platformy Azure, należy utworzyć tras statycznych. Tej trasy statyczne powinny kierować na adres IP sieci VPN NVA firmowej Internetu, a nie za pośrednictwem sieci WAN ExpressRoute. Translator adresów Sieciowych wymaganych opcji Azure publicznej komunikacji równorzędnej ExpressRoute mogą być dzielone sesji sieci VPN.
>
>

Na poniższej ilustracji przedstawiono dwie krawędzie sieci, w tym przykładzie. Na pierwszym krawędzi przepływów ruchu sieciowego dla sieci w obrębie platformy Azure i platformy Azure i Internet kontrolę NVA i grup NSG. Drugi krawędź jest brama sieci VPN platformy Azure krawędzi oddzielne sieci między lokalną i platformą Azure.

Ruch należy rozważyć dokładnie, jak mogą być optymalizowane lub ograniczone przez ten wzorzec projektowania, w zależności od konkretnych przypadek użycia.

Przy użyciu środowiska wbudowane w przykładzie 1, a następnie dodanie połączenia sieciowego hybrydowego sieci VPN typu lokacja lokacja, tworzy następującego projektu:

[![15]][15]

#### <a name="conclusion"></a>Podsumowanie
Dodanie połączenia sieciowego hybrydowego site-to-site VPN do sieci wirtualnej platformy Azure można rozszerzyć sieć lokalną na platformie Azure w bezpieczny sposób. Za pomocą natywnego bramy sieci VPN platformy Azure, ruchu jest IPSec szyfrowane i tras za pośrednictwem Internetu. Ponadto przy użyciu bramy sieci VPN platformy Azure zapewniają to opcja tanie (nie dodatkowych licencji koszt tak jak w przypadku innych firm NVAs). Ta opcja jest najbardziej ekonomiczne w przykładzie 1, gdzie są używane nie NVA. Aby uzyskać więcej informacji zobacz instrukcje szczegółowe kompilacji (nadchodzącego). Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej za pomocą skryptów środowiska PowerShell.
* Jak utworzyć w tym przykładzie z szablonem usługi Azure Resource Manager.
* Scenariusze przepływu ruchu szczegółowe, przedstawiający sposób ruch przechodzi przez ten projekt.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Przykład 6 Dodaj połączenie hybrydowe z usługi ExpressRoute
[Powrót do szybki start](#fast-start) | Szczegółowe instrukcje kompilacji jest dostępny wkrótce

[![16]][16]

#### <a name="environment-description"></a>Opis elementu środowiska
Sieci hybrydowe przy użyciu połączenia prywatnej komunikacji równorzędnej ExpressRoute można dodać do dowolnego typu sieci obwodowej opisanego w przykładach 1 lub 2.

Jak pokazano na poprzedniej ilustracji, prywatnej komunikacji równorzędnej ExpressRoute zawiera bezpośrednie połączenie między siecią lokalną a sieci wirtualnej platformy Azure. Ruch transits sieci dostawcy usług i sieci Microsoft Azure, nie ingerując w Internecie.

> [!TIP]
> Przy użyciu usługi ExpressRoute przechowuje ruchu w sieci firmowej z Internetu. Pozwala także umów dotyczących poziomu usług z dostawcy usługi ExpressRoute. Brama Azure można przekazać do 10 GB/s z usługi ExpressRoute, z sieci VPN typu lokacja lokacja, maksymalną przepływność bramy Azure jest 200 MB/s.
>
>

Jak pokazano na poniższym diagramie, po wybraniu tej opcji środowiska ma teraz dwie krawędzie sieci. NVA i NSG kontroli przepływów ruchu sieciowego dla sieci w obrębie platformy Azure i Azure od Internetu, gdy brama jest krawędzi oddzielne sieci między lokalną i platformą Azure.

Ruch należy rozważyć dokładnie, jak mogą być optymalizowane lub ograniczone przez ten wzorzec projektowania, w zależności od konkretnych przypadek użycia.

Przy użyciu środowiska wbudowane w przykładzie 1, a następnie dodanie połączenia sieciowego hybrydowego usługi ExpressRoute, tworzy następującego projektu:

[![17]][17]

#### <a name="conclusion"></a>Podsumowanie
Dodanie połączenia sieciowego w komunikacji równorzędnej ExpressRoute prywatnego można rozszerzyć sieć lokalną na platformie Azure w bezpieczny, dolna opóźnienia nowszego wykonywania sposób. Ponadto za pomocą natywnego bramy Azure, jak w poniższym przykładzie zapewnia opcję tanie (Brak dodatkowych licencjonowania, podobnie jak w przypadku innych firm NVAs). Aby uzyskać więcej informacji zobacz instrukcje szczegółowe kompilacji (nadchodzącego). Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej za pomocą skryptów środowiska PowerShell.
* Jak utworzyć w tym przykładzie z szablonem usługi Azure Resource Manager.
* Scenariusze przepływu ruchu szczegółowe, przedstawiający sposób ruch przechodzi przez ten projekt.

## <a name="references"></a>Dokumentacja
### <a name="helpful-websites-and-documentation"></a>Przydatne witryn sieci Web i dokumentacji
* Dostęp do platformy Azure z usługą Azure Resource Manager:
* Uzyskiwanie dostępu do platformy Azure przy użyciu programu PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Dokumentacja sieci wirtualnych: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Dokumentacja grupy zabezpieczeń sieci: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Dokumentacja tras zdefiniowanych przez użytkownika: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Azure bramy wirtualnej: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Sieci VPN typu lokacja lokacja: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Dokumentacja usługi ExpressRoute (należy sprawdzić w sekcji "Wprowadzenie" i "How"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "schemat blokowy opcji zabezpieczeń"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "funkcje zabezpieczeń platformy azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Sieć obwodowa A w sieci firmowej"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "architektury zabezpieczeń platformy azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Sieć obwodowa w sieci wirtualnej platformy Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "sieci hybrydowe za pomocą trzech granic zabezpieczeń"
[7]: ./media/best-practices-network-security/example1design.png "ruchu przychodzącego sieci obwodowej z grupy NSG"
[8]: ./media/best-practices-network-security/example2design.png "ruchu przychodzącego sieci obwodowej NVA i grupy NSG"
[9]: ./media/best-practices-network-security/example3design.png "Dwukierunkowa sieć obwodowa z NVA, NSG i przez"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "widok logiczny reguł zapory"
[11]: ./media/best-practices-network-security/example3designoptions.png "Sieć obwodowa z NVA połączonych sieci hybrydowe"
[12]: ./media/best-practices-network-security/example4designs2s.png "Sieć obwodowa z NVA nawiązano połączenie przy użyciu sieci VPN lokacja lokacja"
[13]: ./media/best-practices-network-security/example4networklogical.png "sieci logicznej z punktu widzenia NVA"
[14]: ./media/best-practices-network-security/example5designoptions.png "Sieć obwodowa na platformie Azure bramy połączonej sieci hybrydowe lokacja lokacja"
[15]: ./media/best-practices-network-security/example5designs2s.png "Sieć obwodowa na platformie Azure bramy przy użyciu sieci VPN lokacja lokacja"
[16]: ./media/best-practices-network-security/example6designoptions.png "Sieć obwodowa na platformie Azure bramy połączonej sieci hybrydowe usługi ExpressRoute"
[17]: ./media/best-practices-network-security/example6designexpressroute.png "Sieć obwodowa na platformie Azure bramy przy użyciu połączenia ExpressRoute"

<!--Link References-->
[TrustCenter]: https://azure.microsoft.com/support/trust-center/compliance/
[Example1]: ./virtual-network/virtual-networks-dmz-nsg.md
[Example2]: ./virtual-network/virtual-networks-dmz-nsg-fw-asm.md
[Example3]: ./virtual-network/virtual-networks-dmz-nsg-fw-udr-asm.md
[Example4]: ./virtual-network/virtual-networks-hybrid-s2s-nva-asm.md
[Example5]: ./virtual-network/virtual-networks-hybrid-s2s-agw-asm.md
[Example6]: ./virtual-network/virtual-networks-hybrid-expressroute-asm.md
[Example7]: ./virtual-network/virtual-networks-vnet2vnet-direct-asm.md
[Example8]: ./virtual-network/virtual-networks-vnet2vnet-transit-asm.md
