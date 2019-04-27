---
title: Najlepsze rozwiązania dotyczące zabezpieczeń sieci platformy Azure | Dokumentacja firmy Microsoft
description: Zapoznaj się z kluczowymi funkcjami dostępnych na platformie Azure w celu tworzenia bezpiecznych środowisk sieciowych
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
ms.openlocfilehash: 74a46c7788b0a0dc729ab977489ed6d97a387a6e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60619531"
---
# <a name="microsoft-cloud-services-and-network-security"></a>Usługa Microsoft cloud services i network security
Usługi w chmurze Microsoft dostarczać usługi w hiperskali i infrastruktury, funkcji przeznaczonych dla przedsiębiorstw i wiele możliwości na potrzeby łączności hybrydowej. Klienci mogą wybrać uzyskać dostęp do tych usług za pośrednictwem Internetu lub z usługą Azure ExpressRoute, która zapewnia łączność sieci prywatnej. Platforma Microsoft Azure umożliwia klientom bezproblemowo rozszerzyć swoją infrastrukturę do chmury i tworzenie architektury wielowarstwowej. Ponadto innych firm można włączyć rozszerzone możliwości, oferując bezpieczeństwa usług i urządzeń wirtualnych. Ten dokument zawiera omówienie zabezpieczeń i architektury problemy, które klienci powinni rozważyć w przypadku korzystania z usług chmurowych firmy Microsoft dostępne za pośrednictwem usługi ExpressRoute. Poruszono w nim także tworzenie bardziej bezpiecznych usług w sieciach wirtualnych platformy Azure.

## <a name="fast-start"></a>Szybki start
Na poniższym wykresie logiki można kierować do konkretnemu przykładowi wiele technik zabezpieczeń dostępne dzięki platformie Azure. Krótki przewodnik można znaleźć w przykładzie, który najlepiej odpowiada Twoim przypadku. Rozwinięty wyjaśnień nadal lektura papieru.
[![0]][0]

[Przykład 1: Tworzenie sieci obwodowej (znany także jako DMZ, strefą zdemilitaryzowaną lub podsiecią ekranowaną) w celu ochrony aplikacji za pomocą sieciowych grup zabezpieczeń (NSG).](#example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs)</br>
[Przykład 2: Tworzenie sieci obwodowej, aby pomóc w ochronie aplikacji z zaporą i sieciowymi grupami zabezpieczeń.](#example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs)</br>
[Przykład 3: Tworzenie sieci obwodowej, aby pomóc w ochronie sieci z zaporą, trasa zdefiniowana przez użytkownika (UDR) i sieciowej grupy zabezpieczeń.](#example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg)</br>
[Przykład 4: Dodawanie połączenia hybrydowego za pomocą site-to-site, wirtualne urządzenie wirtualnej sieci prywatnej (VPN).](#example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn)</br>
[Przykład 5: Dodaj połączenie hybrydowe przy użyciu bramy lokacja lokacja, sieci VPN platformy Azure.](#example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway)</br>
[Przykład 6: Dodaj połączenie hybrydowe przy użyciu usługi ExpressRoute.](#example-6-add-a-hybrid-connection-with-expressroute)</br>
Przykłady dodawania połączenia między sieciami wirtualnymi o wysokiej dostępności i tworzenie łańcucha usług zostaną dodane do tego dokumentu w ciągu następnych kilku miesięcy.

## <a name="microsoft-compliance-and-infrastructure-protection"></a>Zgodność i infrastruktury ochrony firmy Microsoft
Aby ułatwić organizacjom, które są zgodne z krajowe, regionalne i branżowe wymagania dotyczące zbierania i używania danych poszczególnych osób, firma Microsoft oferuje ponad 40 certyfikatami i aprobatami. Najbardziej wszechstronny zestaw wszystkich dostawców usług w chmurze.

Aby uzyskać więcej informacji, zobacz informacje o zgodności w [Microsoft Trust Center][TrustCenter].

Firma Microsoft oferuje kompleksowe podejście do ochrony infrastruktury chmury potrzebnych do uruchomienia usług globalnych ogromnej skali. Infrastruktura chmury firmy Microsoft zawiera sprzętu, oprogramowania, sieci i administracyjnych i operatorów, oprócz fizycznych centrów danych.

![2]

To podejście zapewnia bardziej bezpieczną podstawę dla klientów do wdrożenia usługi w chmurze firmy Microsoft. Następnym krokiem jest dla klientów do projektowania i tworzenia architektury zabezpieczeń w celu ochrony tych usług.

## <a name="traditional-security-architectures-and-perimeter-networks"></a>Tradycyjne zabezpieczenia architektury i sieci obwodowej
Mimo że Microsoft intensywnie inwestuje w zakresie ochrony infrastruktury chmury, musisz również chronić klientów swoich usług w chmurze i grupy zasobów. Wielowarstwowego podejścia do zabezpieczeń zapewnia najlepszą ochronę. Strefy zabezpieczeń sieci obwodowej chroni wewnętrznych zasobów sieciowych z niezaufaną siecią. Sieci obwodowej odnosi się do krawędzi lub części sieci, które znajdują się między Internetem a chronionej przedsiębiorstwa infrastrukturę IT.

W sieciach typowego przedsiębiorstwa infrastrukturze podstawowej jest silnie wzmocnione na strefy, dzięki wielu wbudowanym warstwom zabezpieczeń urządzeń. Granicę każda warstwa składa się z urządzeniami i punkty wymuszania zasad. Każda warstwa może zawierać kombinację następujących urządzeń zabezpieczeń sieciowych: zapory, zapobiegania przeprowadzenie ataku typu "odmowa usługi" (DoS), Wykrywanie nieautoryzowanego dostępu lub systemy ochrony (IDS/IPS) i urządzenia sieci VPN. Wymuszanie zasad zostaje wyłączone, może mieć postać zasad zapory, listy kontroli dostępu (ACL) lub specyficznego routingu. Pierwszy wiersz obrony w sieci, bezpośrednio akceptować ruch przychodzący z Internetu, składa się z tych mechanizmów blokowanie ataków i szkodliwy ruch zezwalając dalsze uzasadnione żądania do sieci. Kieruje ruch bezpośrednio z innymi zasobami w sieci obwodowej. Ten zasób może następnie "rozmawiać" ze zasobów głębiej w sieci, użyciem przejścia dalej granic do sprawdzania poprawności pierwszy. Warstwa najbardziej zewnętrznej jest nazywany sieci obwodowej, ponieważ ta część sieci jest połączone z Internetem, zwykle za pomocą jakiejś formy ochrony po obu stronach. Poniższej ilustracji przedstawiono przykład sieci obwodowej jednej podsieci w sieci firmowej, z dwoma granic zabezpieczeń.

![3]

Istnieje wiele architektur używaną do zaimplementowania sieci obwodowej. Te architektury można w zakresie od modułu równoważenia obciążenia prosty do sieci obwodowej z wieloma podsieciami zróżnicowane mechanizmów na każdej granicy do blokuje ruch i chronić głębszych warstw sieci firmowej. Jak zbudowane jest w sieci obwodowej zależy od konkretnych potrzeb organizacji i jej ogólną tolerancji ryzyka.

Jak klienci przenieść swoje obciążenia do chmur publicznych, bardzo ważne jest obsługa podobne możliwości architektury sieci obwodowej na platformie Azure, aby zaspokoić potrzeby dotyczące zgodności i wymagań dotyczących zabezpieczeń. Ten dokument zawiera wskazówki, w jaki sposób klienci mogą tworzyć środowiska bezpiecznej sieci na platformie Azure. Skupiono się w sieci obwodowej, ale również zawiera kompleksowe omówienie wiele aspektów zabezpieczeń sieci. Poniższe pytania o tej dyskusji:

* Jak można utworzyć sieci obwodowej na platformie Azure
* Co to są funkcje systemu Azure dostępnych do tworzenia sieci obwodowej?
* Jak można chronić obciążeń zaplecza
* Jak są kontrolowane obciążeń na platformie Azure komunikację z Internetem?
* Jak sieciami lokalnymi mogą być chronione od wdrożenia na platformie Azure?
* Gdy funkcje natywne zabezpieczeń platformy Azure powinny być używane w stosunku do urządzeń innych firm lub usługi?

Na poniższym diagramie przedstawiono różne warstwy zabezpieczeń, które platforma Azure oferuje klientom. Te warstwy są natywne w samą platformę Azure i funkcji zdefiniowanych przez klienta:

![4]

Dla ruchu przychodzącego z Internetu — Azure pomaga chronić przed atakami na dużą skalę na platformie Azure przed atakami DDoS. Kolejna warstwa jest zdefiniowaną przez użytkownika publiczne adresy IP (punkty końcowe.), które są używane do określenia, który ruch danych można przekazać za pośrednictwem usługi w chmurze do sieci wirtualnej. Natywne usługa Azure virtual, izolacja sieci zapewnia pełną izolację od innych sieci i czy ruch przepływa tylko za pośrednictwem ścieżki skonfigurowane przez użytkownika i metody. Te ścieżki i metody jest kolejna warstwa, gdzie sieciowych grup zabezpieczeń, Routing zdefiniowany przez użytkownika i wirtualnych urządzeń sieciowych może służyć do tworzenia granic zabezpieczeń, aby chronić wdrożenia aplikacji w sieci chronionej.

Następna sekcja zawiera omówienie sieci wirtualnych platformy Azure. Te sieci wirtualne są tworzone przez klientów i co ich wdrożonych obciążeń są połączone. Sieci wirtualne są podstawą wszystkie funkcje zabezpieczeń sieci, wymagane do ustanowienia sieci obwodowej, aby chronić wdrożenia klienta na platformie Azure.

## <a name="overview-of-azure-virtual-networks"></a>Omówienie sieci wirtualnych platformy Azure
Zanim ruch internetowy może nawiązać połączenia z sieciami wirtualnymi platformy Azure, istnieją dwie warstwy zabezpieczeń wbudowane w platformę Azure:

1.    **Ochrona przed atakami DDoS**: Ochrona przed atakami DDoS to warstwa Azure sieci fizycznej, która chroni samą platformę Azure przed atakami internetowymi na dużą skalę. Te ataki używać wielu węzłów "robota" zostanie podjęta próba przeciąży usługi internetowe. Platforma Azure oferuje niezawodną siatki ochrony przed atakami DDoS na wszystkie połączenia dla ruchu przychodzącego, ruchu wychodzącego i platformy Azure dla wielu regionów. Ta warstwa ochrony przed atakami DDoS nie ma żadnych konfigurowalnych atrybutów użytkownika i nie jest dostępna dla klienta. Warstwę ochrony przed atakami DDoS chroni Azure jako platformy przed atakami na dużą skalę, również monitoruje ruchu wyjściowego powiązane i regionu platformy Azure między ruchu. Przy użyciu wirtualnych urządzeń sieciowych w sieci wirtualnej, dodatkowe warstwy odporności można skonfigurować klienta względem mniejszych ataku skalowania, który nie przełączył ochrony na poziomie platformy. Przykładem przed atakami DDoS w akcji; Jeśli adres IP nakierowanego na internet zostało zaatakowane przez na dużą skalę ataków DDoS, platforma Azure będzie wykrywanie źródeł rodzajów ataków i czyszczenie naruszającym ruchu przed osiągnięciem jego przeznaczenia. W większości przypadków zaatakowane punkt końcowy nie ma wpływu na atak. W rzadkich przypadkach, że punkt końcowy ma wpływ żaden ruch nie ma wpływ na inne punkty końcowe, tylko zaatakowane punktu końcowego. Ten sposób innych klientów i usług będą w nim wyświetlane nie wpływu na tego ataku. Koniecznie pamiętać tylko poszukuje Azure przed atakami DDoS ataków na dużą skalę. Istnieje możliwość, że określone usługi może zostać przeciążona, zanim przekroczeniu progów ochrony na poziomie platformy. Na przykład witryny sieci web na jednym serwerze A0 IIS, można podjąć w trybie offline ataków DDoS zanim poziomu platformy Azure DDoS protection zarejestrowane zagrożenie.

2.  **Publiczne adresy IP**: Publiczny adres IP adresów (włączone za pośrednictwem punktów końcowych usługi, publiczny adres IP adresów, usługa Application Gateway i innych funkcji platformy Azure, które przedstawiają publiczny adres IP w Internecie kierowane do zasobu usługi) Zezwalaj na chmurze usług lub grupy zasobów ma publiczny adres IP w Internecie adresów i portów. Punkt końcowy korzysta z translacji adresów sieciowych (NAT) do kierowania ruchu do wewnętrznych adresów i portów w sieci wirtualnej platformy Azure. Ta ścieżka jest podstawowym sposobem dla ruchu zewnętrznego do przekazania do sieci wirtualnej. Publiczne adresy IP są konfigurowane w celu ustalenia, jaki ruch jest przekazywany w i jak i gdzie jest tłumaczony na sieć wirtualną.

Gdy ruch trafia sieci wirtualnej, istnieje wiele funkcji, które dochodzą do głosu. Sieci wirtualne platformy Azure są podstawą dla klientów dołączyć swoje obciążenia i gdzie ma zastosowanie podstawowych zabezpieczeń na poziomie sieci. Jest siecią prywatną (w nakładce sieci wirtualnej) na platformie Azure dla klientów korzystających z następujących funkcji i właściwości:

* **Izolacji ruchu**: Sieć wirtualna jest granicę izolacji ruchu na platformie Azure. Maszyn wirtualnych (VM) w jednej sieci wirtualnej nie może komunikować się bezpośrednio do maszyn wirtualnych w innej sieci wirtualnej, nawet jeśli obie sieci wirtualne są tworzone przez tego samego klienta. Izolacja jest krytyczne właściwości, które gwarantuje, że maszyny wirtualne klientów i komunikacja pozostanie w prywatnej sieci wirtualnej.

>[!NOTE]
>Izolacja ruchu, który odwołuje się tylko do ruchu *dla ruchu przychodzącego* do sieci wirtualnej. Domyślnie ruch wychodzący z sieci wirtualnej z Internetem jest dozwolone, ale można zapobiec, jeśli to konieczne, sieciowe grupy zabezpieczeń.
>
>

* **Topologia wielowarstwowa**: Sieci wirtualne umożliwiają klientom Definiowanie topologii wielowarstwowej usługi przydzielając podsieci i wyznaczanie oddzielnych przestrzeni adresowych dla różnych elementów lub "warstwy" ich obciążeń. Te logiczne grupowanie i topologii pozwala klientom Definiowanie zasad dostępu różne na podstawie typów obciążeń, a także kontrolować przepływy ruchu między warstwami.
* **Łączność między wieloma lokalizacjami**: Klientów można ustanowić łączności między środowiskami lokalnymi między siecią wirtualną i wieloma lokacjami lokalnymi lub innymi sieciami wirtualnymi na platformie Azure. Do utworzenia połączenia, klienci mogą używać wirtualnej sieci równorzędnej, bram Azure VPN Gateway, wirtualne urządzenia sieciowe innych firm lub usługi ExpressRoute. Platforma Azure obsługuje za pomocą standardowych protokołów IPsec/IKE i prywatne połączenia usługi ExpressRoute sieci VPN typu lokacja lokacja (S2S).
* **Sieciowa grupa zabezpieczeń** umożliwia klientom tworzenie reguł (kontroli dostępu ACL) na odpowiednim poziomie szczegółowości: sieci interfejsów, poszczególnych maszyn wirtualnych lub podsieci wirtualnych. Klientów można kontrolować dostęp przez zezwalanie na dostęp lub odmawianie komunikacji między obciążeniami usług w ramach sieci wirtualnej, z systemów w sieciach klienta za pośrednictwem łączności między środowiskami lokalnymi lub bezpośrednia komunikacja z Internetem.
* **Trasa zdefiniowana przez użytkownika** i **przesyłania dalej protokołu IP** umożliwiają klientom definiują ścieżki komunikacji między różnych warstw w ramach sieci wirtualnej. Klienci mogą wdrożenie zapory, IDS/IPS i innych urządzeń wirtualnych i kierować ruchem sieciowym za pomocą tych urządzeń zabezpieczeń do wymuszania zasad granicy zabezpieczeń, inspekcji i kontroli.
* **Sieciowych urządzeń wirtualnych** w witrynie Azure Marketplace: Urządzenia zabezpieczeń, takich jak zapory i moduły równoważenia obciążenia, IDS/IPS są dostępne w portalu Azure Marketplace i galerii obrazów maszyn wirtualnych. Klienci mogą wdrożyć te urządzenia w swoich sieciach wirtualnych, a w szczególności w ich granic zabezpieczeń (w tym podsieci sieci obwodowej), aby ukończyć bezpieczne środowisko wielopoziomowe sieci.

Z tych funkcji i możliwości jednym z przykładów jak architektura sieci obwodowej należy zbudować na platformie Azure jest poniższym diagramie:

![5]

## <a name="perimeter-network-characteristics-and-requirements"></a>Właściwości sieci obwodowej i wymagania
W sieci obwodowej jest fronton sieci komunikacji z Internetu komunikuje się bezpośrednio. Pakiety przychodzące powinna przepływać za pośrednictwem urządzenia zabezpieczeń, takie jak zapora, Identyfikatory i adresy IP, przed dotarciem do serwerów zaplecza. Pakiety z Internetu od obciążenia również może przepływać za pośrednictwem urządzenia zabezpieczeń w sieci obwodowej, wymuszanie zasad zostaje wyłączone, inspekcji i celów inspekcji przed opuszczeniem sieci. Ponadto w sieci obwodowej może obsługiwać między środowiskami lokalnymi bramami sieci VPN między sieciami wirtualnymi klienta i sieciami lokalnymi.

### <a name="perimeter-network-characteristics"></a>Właściwości sieci obwodowej
Odwoływanie się do poprzedniej ilustracji, niektóre właściwości sieci obwodowej dobre są następujące:

* Internet-facing:
  * Samej podsieci sieci obwodowej jest dostępnym z Internetu, bezpośrednio podczas komunikowania się z Internetem.
  * Publiczne adresy IP, adresy VIP i/lub punktów końcowych usługi przekazywać ruch internetowy do urządzeń i sieć frontonu.
  * Ruch przychodzący z Internetu przechodzą przez urządzeń zabezpieczających przed innymi zasobami w sieci frontonu.
  * Włączenie zabezpieczeń dla ruchu wychodzącego ruch przechodzi przez urządzenia zabezpieczeń w ostatnim kroku przed przekazaniem do Internetu.
* Sieć chroniona:
  * Nie ma bezpośredniej ścieżki z Internetu w infrastrukturze podstawowej.
  * Kanały w infrastrukturze podstawowej muszą przejść za pomocą urządzenia zabezpieczeń, takich jak sieciowe grupy zabezpieczeń, zapory lub urządzenia sieci VPN.
  * Inne urządzenia nie musi stanowić pomost Internet i infrastrukturze podstawowej.
  * Urządzenia zabezpieczeń zarówno z Internetu, jak i sieci chronionej połączonego z granicami sieci obwodowej (na przykład dwa zapory ikony zostało pokazane na poprzedniej ilustracji) faktycznie może być pojedynczego urządzenia wirtualnego za pomocą zróżnicowanych reguły lub interfejsów dla każdej granicy. Na przykład jedno urządzenie fizyczne, logicznie oddzielone obsługi obciążenia dla obu granice sieci obwodowej.
* Inne wspólne praktyki i ograniczenia:
  * Obciążenia nie mogą przechowywać krytyczne informacje biznesowe.
  * Dostęp i aktualizacje konfiguracji sieci obwodowej i wdrożenia są ograniczone do tylko autoryzowani administratorzy.

### <a name="perimeter-network-requirements"></a>Wymagania dotyczące sieci obwodowej
Aby włączyć te właściwości, należy przestrzegać następujących wytycznych wymagań sieci wirtualnej do sieci obwodowej pomyślnego wdrożenia:

* **Architektura podsieci:** Określ sieci wirtualnej w taki sposób, że w całej podsieci jest przeznaczony jako sieci obwodowej, oddzielone od innych podsieci w tej samej sieci wirtualnej. Ten rozdział zapewnia ruch między siecią obwodową i innych podsieci wewnętrznego lub prywatnego warstw odbywa się za pośrednictwem zapory lub urządzenia wirtualnego IDS/IPS.  Trasy zdefiniowane przez użytkownika w podsieciach granic należy przekazywać ten ruch do urządzenia wirtualnego.
* **NSG:** Samej podsieci sieci obwodowej powinny być otwarty, aby zezwalać na komunikację z Internetem, ale nie oznacza to, że klienci powinien pomijanie sieciowych grup zabezpieczeń. Postępuj zgodnie z typowe rozwiązania w zakresie zabezpieczeń zminimalizować powierzchnie sieci, połączenie z Internetem. Zablokowanie zakresy adresów zdalnych, mogą uzyskiwać dostęp do wdrożenia lub określoną aplikacją, protokoły i porty, które są otwarte. Mogą zaistnieć okoliczności, jednak, w których pełną blokowanie nie jest możliwe. Na przykład jeśli klienci mają zewnętrzną witrynę sieci Web na platformie Azure, w sieci obwodowej powinien zezwalać na przychodzące żądania sieci web z publicznych adresów IP, ale należy otwierać tylko te porty aplikacji sieci web: Ruch TCP na porcie 80 i/lub ruch TCP na porcie 443.
* **Tabela routingu:** Samej podsieci sieci obwodowej powinien mieć możliwość komunikacji z Internetem bezpośrednio, ale nie powinien zezwalać na bezpośrednią komunikację do i z tyłu zakończenia lub lokalnej sieci bez pośrednictwa urządzenia zapory lub zabezpieczeń.
* **Konfiguracja urządzenia zabezpieczeń:** Trasy i sprawdzanie pakietów między siecią obwodową a pozostała część chronionej sieci, urządzenia zabezpieczeń, takie jak zapora, Identyfikatory i adresy IP urządzeń mogą być wieloadresowych. Mogą mieć oddzielnych kart sieciowych dla sieci obwodowej i podsieci zaplecza. Karty sieciowe w sieci obwodowej komunikują się bezpośrednio do i z Internetu, przy użyciu odpowiednich grup zabezpieczeń sieci i tabelę routingu w sieci obwodowej. Karty sieciowe, nawiązywania połączenia z podsieci zaplecza więcej ograniczył sieciowe grupy zabezpieczeń i tabel routingu odpowiednie podsieci zaplecza.
* **Funkcje urządzenia zabezpieczeń:** Urządzenia zabezpieczeń, wdrożone w sieci obwodowej, zwykle wykonują następujące funkcje:
  * Zapora: Wymuszanie reguły zapory lub zasady kontroli dostępu dla żądań przychodzących.
  * Wykrywanie zagrożeń i zapobieganie im: Wykrywanie i eliminowanie złośliwe ataki z Internetu.
  * Inspekcja i rejestrowanie: Obsługa szczegółowych dzienników inspekcji i analizy.
  * Zwrotny serwer proxy: Przekierowywanie żądań przychodzących do odpowiednich serwerów zaplecza. Przekierowanie obejmuje mapowania i zazwyczaj translacji adresów docelowych urządzeniach frontonu zapory adresów serwerów zaplecza.
  * Serwer proxy przesyłania dalej: Zapewnianie translatora adresów Sieciowych i przeprowadzania inspekcji dla komunikacji inicjowane z sieci wirtualnej do Internetu.
  * Router: Przekazywanie ruchu przychodzącego i między podsieciami w sieci wirtualnej.
  * Urządzenie sieci VPN: Pełni rolę bram sieci VPN między środowiskami lokalnymi dla połączenia sieci VPN między lokalizacjami między sieciami lokalnymi klienta i sieciami wirtualnymi platformy Azure.
  * Serwer sieci VPN: Akceptuje klientów sieci VPN, łączenie z sieciami wirtualnymi platformy Azure.

> [!TIP]
> Zachowania ich odrębności następujących dwóch grup: osobom uprawnień dostępu do narzędzi zabezpieczeń w sieci obwodowej i osoby autoryzowane jako administratorzy rozwoju, wdrożenia i działania aplikacji. Oddzieleniu tych grup umożliwia podział obowiązków i uniemożliwia jedna osoba z pominięciem kontroli bezpieczeństwa sieci i zabezpieczeń aplikacji.
>
>

### <a name="questions-to-be-asked-when-building-network-boundaries"></a>Pytania należy zadać, podczas tworzenia granic sieci
W tej sekcji chyba że wyraźnie wymienionych termin "sieci" odnosi się do prywatnych sieciach wirtualnych platformy Azure utworzone przez administratora subskrypcji. Termin nie odwołuje się do podstawowej sieci fizycznej w ramach platformy Azure.

Ponadto sieciami wirtualnymi platformy Azure są często używane do rozszerzenia tradycyjnego dla sieci lokalnych. Istnieje możliwość dołączyć lokacja lokacja lub ExpressRoute hybrydowych rozwiązań sieciowych przy użyciu architektury sieci obwodowej. To łącze hybrydowego jest ważną kwestią tworzenia granic zabezpieczeń sieci.

Następujące trzy pytania są kluczowe do odpowiedzi w przypadku tworzenia sieci z sieci obwodowej oraz wielu granic zabezpieczeń.

#### <a name="1-how-many-boundaries-are-needed"></a>(1) jak dużo granice są potrzebne?
Pierwszy punkt decyzyjny ma się do określania, ile granic zabezpieczeń są wymagane w danym scenariuszu:

* Pojedyncza granica: Jeden w sieci obwodowej frontonu, między siecią wirtualną a Internetem.
* Dwa granic: Jeden po stronie Internetu w sieci obwodowej, a drugi między podsieci sieci obwodowej i podsieci zaplecza w sieciach wirtualnych platformy Azure.
* Trzy granic: Na stronie Internetu w sieci obwodowej, po jednym między siecią obwodową a podsieci zaplecza i jednego między podsieciami zaplecza i siecią lokalną.
* Granice N: Numer zmiennej. W zależności od wymagań dotyczących zabezpieczeń nie ma żadnego limitu liczby granic zabezpieczeń, które mogą być stosowane w danej sieci.

Liczba i typ potrzebne różnią się w granicach na podstawie tolerancja ryzyka firmy i konkretnego scenariusza, które są wdrażane. Ta decyzja często składa się z wielu grup w organizacji, często w tym zespół ryzyka i zgodności, sieci i zespołu platform oraz zespół deweloperów aplikacji. Osoby posiadające wiedzę na temat zabezpieczeń, dane, które są zaangażowane i technologie używane powinna mieć say w tej decyzji, aby upewnić się, wystąpienie zabezpieczeń odpowiednich dla każdego wdrożenia.

> [!TIP]
> Użyj najmniejszą liczbę granic, które spełniają wymagania dotyczące zabezpieczeń dla danej sytuacji. Z granicami więcej operacji i rozwiązywania problemów może być trudniejsze, a także koszty zarządzania związane z zarządzaniem wieloma zasadami granic wraz z upływem czasu. Jednak za mało granic zwiększyć ryzyko. Ważne jest znalezienie równowagi.
>
>

![6]

Na powyższej ilustracji przedstawiono ogólny widok sieci granicznej zabezpieczeń trzy. Granice należą do zakresu od sieci obwodowej i Internet, Azure frontonu i zaplecza prywatne podsieci i podsieci zaplecza platformy Azure i lokalnej sieci firmowej.

#### <a name="2-where-are-the-boundaries-located"></a>(2) gdzie znajdują się granic
Liczba granic są ustalane, miejsce ich wdrażania po następnego punktu decyzji. Zazwyczaj są trzy opcje:

* Za pomocą pośredniczącej usługi internetowej (na przykład, oparte na chmurze zapory aplikacji sieci Web, która nie została omówiona w tym dokumencie)
* Za pomocą natywne funkcje i/lub wirtualnych urządzeń sieciowych na platformie Azure
* Za pomocą urządzeń fizycznych w sieci lokalnej

W sieciach czysto platformy Azure opcje są natywne funkcje platformy Azure (na przykład równoważenia obciążenia platformy Azure) lub wirtualne urządzenia sieciowe z bogaty ekosystem partnerów platformy Azure (na przykład zapory Check Point).

Razie granicę między platformą Azure i siecią lokalną, urządzenia zabezpieczeń mogą znajdować się po obu stronach połączenia (lub obu stron). Związku z tym decyzja należy do lokalizacji do umieszczenia koła zębatego zabezpieczeń.

Na poprzednim rysunku sieci Internet obwodowej granice przodu do tyłu zakończenia są całkowicie zawarty w obrębie platformy Azure i musi być natywne funkcje platformy Azure lub sieci wirtualnych urządzeń sieciowych. Na stronie platformy Azure lub stronie w środowisku lokalnym lub nawet kombinację urządzeń po obu stronach, może być urządzeń zabezpieczających na granicy między platformą Azure (podsieci zaplecza) i sieci firmowej. Może to być istotne zalety i wady na jedną z opcji, które muszą być rozważone w naszych użytkowników bardzo poważnie.

Na przykład na stronie sieci w środowisku lokalnym przy użyciu istniejących narzędzi zabezpieczeń fizycznych ma tę zaletę, że nowego biegu jest potrzebna. Po prostu potrzeba ponownej konfiguracji. Wadą jest to jednak, że cały ruch musi wróć na platformie Azure do sieci lokalnej, aby była widoczna koła zębatego zabezpieczeń. Ten sposób ruch do platformy może pociągnąć za sobą znaczne opóźnienia i mogą wpłynąć na wydajność aplikacji i środowisko użytkowników, jeśli nastąpiło wymuszenie do sieci lokalnej do wymuszania zasad zabezpieczeń.

#### <a name="3-how-are-the-boundaries-implemented"></a>(3) sposobu implementacji granice
Każda granica zabezpieczeń będzie prawdopodobnie wymagań związanych z możliwościami inny (na przykład, Identyfikatory i reguł zapory sieci obwodowej, ale tylko listy ACL między siecią obwodową a podsieci zaplecza po stronie Internetu). Przy wyborze rozwiązania, w którym urządzenie (lub ile urządzeń) do użycia zależy od wymagań scenariusza i zabezpieczeń. W poniższej sekcji Przykłady 1, 2 i 3 omówiono niektóre opcje, które mogłyby zostać użyte. Przegląd funkcji sieci natywnego platformy Azure i urządzeń, które są dostępne na platformie Azure z ekosystemem partnerów zawiera wiele opcji do rozwiązania praktycznie dowolnego scenariusza.

Przy podejmowaniu decyzji w innej implementacji klucza jest jak połączyć sieć lokalną z platformą Azure. Należy użyć platformy Azure Brama wirtualnej lub wirtualnego urządzenia sieciowego? Te opcje są omówione bardziej szczegółowo w następnej sekcji (przykłady 4, 5 i 6).

Ponadto ruch sieciowy między sieciami wirtualnymi na platformie Azure mogą być potrzebne. Scenariusze te zostaną dodane w przyszłości.

Jeśli znasz już odpowiedzi na pytania Wstecz [szybkiego startu](#fast-start) sekcji mogą pomóc zidentyfikować, przykłady, które są najbardziej odpowiednie dla danego scenariusza.

## <a name="examples-building-security-boundaries-with-azure-virtual-networks"></a>Przykłady: Tworzenie granic zabezpieczeń z sieciami wirtualnymi platformy Azure
### <a name="example-1-build-a-perimeter-network-to-help-protect-applications-with-nsgs"></a>Przykład 1 Tworzenie sieci obwodowej, aby chronić aplikacje z sieciowymi grupami zabezpieczeń
[Powrót do szybkiego rozpoczęcia](#fast-start) | [szczegółowe kompilacji instrukcje w tym przykładzie][Example1]

[![7]][7]

#### <a name="environment-description"></a>Opis środowiska
W tym przykładzie ma subskrypcję, która zawiera następujące zasoby:

- Pojedyncza grupa zasobów
- Sieć wirtualna z dwiema podsieciami: "Fronton" i "Wewnętrzna"
- Sieciowa grupa zabezpieczeń, która jest stosowana do obu podsieci
- Serwer Windows, który reprezentuje serwer aplikacji sieci web ("IIS01")
- Dwa serwery Windows, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
- Serwer Windows, który reprezentuje serwer DNS ("DNS01")
- Publiczny adres IP skojarzony z serwerem sieci web aplikacji

Skrypty i szablonu usługi Azure Resource Manager, zobacz [szczegółowe instrukcje kompilacji][Example1].

#### <a name="nsg-description"></a>Opis sieciowej grupy zabezpieczeń
W tym przykładzie grupa sieciowej grupy zabezpieczeń jest utworzone i następnie ładowany za pomocą sześciu reguł.

> [!TIP]
> Ogólnie rzecz biorąc należy utworzyć określone reguły "Zezwalaj" najpierw następuje bardziej ogólnymi zasadami "Deny". Podana wartość priorytetu decyduje o tym, jakie reguły są obliczane jako pierwsze. Nie dodatkowe reguły są oceniane, po znalezieniu ruchu do zastosowania do określonej reguły. Reguły sieciowej grupy zabezpieczeń można stosować w żadnym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
>
>

Deklaratywne są tworzone następujące reguły dla ruchu przychodzącego:

1. Ruch DNS wewnętrzny (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do dowolnej maszyny wirtualnej jest dozwolony.
3. Ruch HTTP (port 80) z Internetu do serwera sieci web (IIS01) jest dozwolone.
4. Cały ruch (wszystkich portów) z IIS01 do AppVM1 jest dozwolone.
5. Cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (zarówno podsieci) zostanie odrzucone.
6. Cały ruch (wszystkich portów) z podsieci frontonu do podsieci zaplecza jest odrzucane.

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało ruch przychodzący z Internetu do serwera sieci web, obie reguły 3 (Zezwalaj na) i 5 (odmowa) będzie miało zastosowanie. Jednak ponieważ reguła 3 ma wyższy priorytet, tylko będzie zastosowanie i reguły 5 nie przybyły, aby wpływające na. Dlatego żądania HTTP dozwolone będzie serwer sieci web. Jeśli ten sam ruch próbował uzyskać dostęp do serwera DNS01, zasada 5 (odmowa) może być pierwszy do zastosowania, a ruch będzie nie można przekazać do serwera. Reguła 6 (odmowa) blokuje podsieci frontonu w rozmowie z podsieci zaplecza (z wyjątkiem dozwolonego ruchu w regułach 1 i 4). Ten zestaw reguł zapewnia ochronę sieci wewnętrznej, w przypadku, gdy osoba atakująca obniża aplikacji sieci web na frontonie. Osoba atakująca może mają ograniczony dostęp do wewnętrznej sieci "chroniony" (tylko do zasobów udostępnianych na serwerze AppVM01).

Brak domyślną regułę ruchu wychodzącego, która umożliwia ruchu wychodzącego z Internetem. W tym przykładzie firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie żadnych reguł dla ruchu wychodzącego. Aby zablokować ruch w obu kierunkach, routingu zdefiniowanego przez użytkownika jest wymagany (Zobacz przykład 3).

#### <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest stosunkowo prosta i prosty sposób odizolowanie podsieci zaplecza z ruchu przychodzącego. Aby uzyskać więcej informacji, zobacz [szczegółowe instrukcje kompilacji][Example1]. Instrukcje te obejmują:

* Jak utworzyć tej sieci obwodowej za pomocą klasycznego skryptów programu PowerShell.
* Jak utworzyć tej sieci obwodowej przy użyciu szablonu usługi Azure Resource Manager.
* Szczegółowy opis każdego polecenia sieciowej grupy zabezpieczeń.
* Scenariusze przepływu ruchu szczegółowe, pokazujący, jak ruch jest dozwolony lub zablokowany w każdej warstwie.


### <a name="example-2-build-a-perimeter-network-to-help-protect-applications-with-a-firewall-and-nsgs"></a>Przykład 2 Tworzenie sieci obwodowej, aby pomóc w ochronie aplikacji z zaporą i sieciowymi grupami zabezpieczeń
[Powrót do szybkiego rozpoczęcia](#fast-start) | [szczegółowe kompilacji instrukcje w tym przykładzie][Example2]

[![8]][8]

#### <a name="environment-description"></a>Opis środowiska
W tym przykładzie ma subskrypcję, która zawiera następujące zasoby:

* Pojedyncza grupa zasobów
* Sieć wirtualna z dwiema podsieciami: "Fronton" i "Wewnętrzna"
* Sieciowa grupa zabezpieczeń, która jest stosowana do obu podsieci
* Wirtualne urządzenie sieciowe, w tym przypadku w zaporze podłączone do podsieci frontonu
* Serwer Windows, który reprezentuje serwer aplikacji sieci web ("IIS01")
* Dwa serwery Windows, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Serwer Windows, który reprezentuje serwer DNS ("DNS01")

Skrypty i szablonu usługi Azure Resource Manager, zobacz [szczegółowe instrukcje kompilacji][Example2].

#### <a name="nsg-description"></a>Opis sieciowej grupy zabezpieczeń
W tym przykładzie grupa sieciowej grupy zabezpieczeń jest utworzone i następnie ładowany za pomocą sześciu reguł.

> [!TIP]
> Ogólnie rzecz biorąc należy utworzyć określone reguły "Zezwalaj" najpierw następuje bardziej ogólnymi zasadami "Deny". Podana wartość priorytetu decyduje o tym, jakie reguły są obliczane jako pierwsze. Nie dodatkowe reguły są oceniane, po znalezieniu ruchu do zastosowania do określonej reguły. Reguły sieciowej grupy zabezpieczeń można stosować w żadnym kierunku ruchu przychodzącego lub wychodzącego (z punktu widzenia podsieci).
>
>

Deklaratywne są tworzone następujące reguły dla ruchu przychodzącego:

1. Ruch DNS wewnętrzny (port 53) jest dozwolone.
2. Ruch protokołu RDP (port 3389) z Internetu do dowolnej maszyny wirtualnej jest dozwolony.
3. Wszelkie ruchu internetowego (wszystkich portów) do wirtualnego urządzenia sieciowego (zapora) jest dozwolone.
4. Cały ruch (wszystkich portów) z IIS01 do AppVM1 jest dozwolone.
5. Cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (zarówno podsieci) zostanie odrzucone.
6. Cały ruch (wszystkich portów) z podsieci frontonu do podsieci zaplecza jest odrzucane.

Przy użyciu tych reguł powiązany z każdej podsieci, jeśli żądanie HTTP zostało ruch przychodzący z Internetu do zapory, obie reguły 3 (Zezwalaj na) i 5 (odmowa) będzie miało zastosowanie. Jednak ponieważ reguła 3 ma wyższy priorytet, tylko będzie zastosowanie i reguły 5 nie przybyły, aby wpływające na. Ten sposób żądania HTTP będzie mogła zapory. Jeśli ten sam ruch próbował uzyskać dostęp do serwera IIS01 mimo że jest w tej podsieci frontonu, zasada 5 (odmowa) będzie miało zastosowanie, a ruch będzie nie można przekazać do serwera. Reguła 6 (odmowa) blokuje podsieci frontonu w rozmowie z podsieci zaplecza (z wyjątkiem dozwolonego ruchu w regułach 1 i 4). Ten zestaw reguł zapewnia ochronę sieci wewnętrznej, w przypadku, gdy osoba atakująca obniża aplikacji sieci web na frontonie. Osoba atakująca może mają ograniczony dostęp do wewnętrznej sieci "chroniony" (tylko do zasobów udostępnianych na serwerze AppVM01).

Brak domyślną regułę ruchu wychodzącego, która umożliwia ruchu wychodzącego z Internetem. W tym przykładzie firma Microsoft zezwala na ruch wychodzący i nie modyfikowanie żadnych reguł dla ruchu wychodzącego. Aby zablokować ruch w obu kierunkach, routingu zdefiniowanego przez użytkownika jest wymagany (Zobacz przykład 3).

#### <a name="firewall-rule-description"></a>Opis reguły zapory
Na zaporze powinny być tworzone reguły przekazywania. Reguła jest wymagana, ponieważ w tym przykładzie tylko trasy ruchu internetowego przychodzącego do zapory, a następnie z serwerem sieci web tylko jednego przekazującego translacja adresów (NAT).

Reguły przekazywania akceptuje dowolny adres źródła dla ruchu przychodzącego, która osiąga zapory próba nawiązania połączenia HTTP (port 80 i 443 dla protokołu HTTPS). Ma wysyłane z lokalnego interfejsu zapory i Przekierowanie do serwera sieci web o adresie IP 10.0.1.5.

#### <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest stosunkowo łatwe ochrony aplikacji z zaporą i izolowanie podsieci zaplecza z ruchu przychodzącego. Aby uzyskać więcej informacji, zobacz [szczegółowe instrukcje kompilacji][Example2]. Instrukcje te obejmują:

* Jak utworzyć tej sieci obwodowej za pomocą klasycznego skryptów programu PowerShell.
* Jak skompilować ten przykład przy użyciu szablonu usługi Azure Resource Manager.
* Szczegółowy opis każdego polecenia i zapory reguły sieciowej grupy zabezpieczeń.
* Scenariusze przepływu ruchu szczegółowe, pokazujący, jak ruch jest dozwolony lub zablokowany w każdej warstwie.

### <a name="example-3-build-a-perimeter-network-to-help-protect-networks-with-a-firewall-and-udr-and-nsg"></a>Przykład 3 Tworzenie sieci obwodowej, aby pomóc w ochronie sieci z zaporą i trasa zdefiniowana przez użytkownika i sieciowej grupy zabezpieczeń
[Powrót do szybkiego rozpoczęcia](#fast-start) | [szczegółowe kompilacji instrukcje w tym przykładzie][Example3]

[![9]][9]

#### <a name="environment-description"></a>Opis środowiska
W tym przykładzie ma subskrypcję, która zawiera następujące zasoby:

* Pojedyncza grupa zasobów
* Sieć wirtualna z trzema podsieciami: "SecNet", "Fronton" i "Wewnętrzna"
* Wirtualnego urządzenia sieciowego, w tym przypadku, a Zapora połączona z podsiecią SecNet
* Serwer Windows, który reprezentuje serwer aplikacji sieci web ("IIS01")
* Dwa serwery Windows, które reprezentują serwery zaplecza aplikacji ("AppVM01", "AppVM02")
* Serwer Windows, który reprezentuje serwer DNS ("DNS01")

Skrypty i szablonu usługi Azure Resource Manager, zobacz [szczegółowe instrukcje kompilacji][Example3].

#### <a name="udr-description"></a>Trasa zdefiniowana przez użytkownika opis
Domyślnie następujące trasy systemowe są definiowane jako:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.0.0/16}     VNETLocal                            Active   Default    
         {0.0.0.0/0}       Internet                             Active   Default    
         {10.0.0.0/8}      Null                                 Active   Default    
         {100.64.0.0/10}   Null                                 Active   Default    
         {172.16.0.0/12}   Null                                 Active   Default    
         {192.168.0.0/16}  Null                                 Active   Default

VNETLocal jest zawsze co najmniej jeden prefiksy adresów zdefiniowanych, które tworzą sieci wirtualnej dla tej określonej sieci (czyli zmienia z sieci wirtualnej do sieci wirtualnej, w zależności od tego, jak jest zdefiniowany każdej określonej sieci wirtualnej). Pozostałe tras systemowych są statyczne i domyślne określonych w tabeli.

W tym przykładzie dwie tabele routingu są tworzone, jeden dla podsieci frontonu i zaplecza. Każda tabela jest załadowana trasy statyczne, które są odpowiednie dla danej podsieci. W tym przykładzie każda tabela zawiera trzy trasy, które kierować cały ruch (0.0.0.0/0) za pośrednictwem zapory (następnego przeskoku = adres wirtualny adres IP urządzenia):

1. Ruchu w podsieci lokalnej za pomocą następnego skoku określone, aby zezwolić na ruch z podsieci lokalnej ominąć zaporę.
2. Ruchu w sieci wirtualnej za pomocą następnego skoku zdefiniowany jako zapora. To następny przeskok zastępuje regułę domyślną, która umożliwia lokalne ruchu w sieci wirtualnej do rozsyłania bezpośrednio.
3. Cały ruch pozostałe (0/0) za pomocą następnego skoku zdefiniowany jako zapora.

> [!TIP]
> Nie ma wpisu w podsieci lokalnej podczas komunikacji podsieci lokalnej podziały trasy zdefiniowanej przez użytkownika.
>
> * W naszym przykładzie bezwzględnie 10.0.1.0/24 wskazujący VNETLocal! Bez tego pakietów, pozostawiając serwera sieci Web (10.0.1.4), przeznaczone na inny serwer lokalny (na przykład) 10.0.1.25 zakończy się niepowodzeniem, ponieważ zostaną wysłane do urządzenia WUS. Urządzenie WUS będzie wysyłać je do podsieci i podsieć ponownie wyśle je do urządzenia NVA w pętli nieskończonej.
> * Prawdopodobieństwo Pętla routingu są zazwyczaj wyższe na urządzeniach z wieloma kartami sieciowymi, które są podłączone do oddzielnych podsieci, co ma często miejsce tradycyjny, urządzeń w środowisku lokalnym.
>
>

Po utworzeniu tabele routingu musi być powiązany z ich podsieciami. Podsieci frontonu tabeli routingu, jeden raz utworzona i powiązana z podsiecią, będzie wyglądać następujące dane wyjściowe:

        Effective routes :
         Address Prefix    Next hop type    Next hop IP address Status   Source     
         --------------    -------------    ------------------- ------   ------     
         {10.0.1.0/24}     VNETLocal                            Active
         {10.0.0.0/16}     VirtualAppliance 10.0.0.4            Active    
         {0.0.0.0/0}       VirtualAppliance 10.0.0.4            Active

> [!NOTE]
> Trasa zdefiniowana przez użytkownika można teraz stosować do podsieci bramy, na którym jest połączony z obwodem usługi ExpressRoute.
>
> W przykładach 3 i 4 przedstawiono przykładowe włączania tej samej sieci obwodowej przy użyciu usługi ExpressRoute lub sieci lokacja lokacja.
>
>

#### <a name="ip-forwarding-description"></a>Opis usługi przesyłania dalej protokołu IP
Przekazywanie adresów IP to funkcja pomocnika trasy zdefiniowanej przez użytkownika. Przekazywanie adresów IP to ustawienie na urządzenie wirtualne, które umożliwia odbieranie ruchu nie jest skierowana do urządzenia, a następnie prześlij je dalej ruchu do ostatecznego miejsca docelowego.

Na przykład jeśli AppVM01 kieruje żądanie do serwera DNS01, trasę zdefiniowaną przez użytkownika będzie kierować ruch do zapory. Z włączoną funkcją przekazywania adresów IP ruch dla miejsca docelowego DNS01 (10.0.2.4) zostanie zaakceptowany przez urządzenie (10.0.0.4) i następnie przekazywane do ostatecznego miejsca docelowego (10.0.2.4). Bez przekazywania adresów IP jest włączona Zapora ruch nie była akceptowana przez urządzenie, mimo że tabela tras ma zapory w następnym skoku. Aby korzystać z urządzenia wirtualnego, koniecznie należy pamiętać umożliwić przekazywanie adresów IP oraz Routing zdefiniowany przez użytkownika.

#### <a name="nsg-description"></a>Opis sieciowej grupy zabezpieczeń
W tym przykładzie grupa sieciowej grupy zabezpieczeń jest utworzone i następnie ładowane przy użyciu jednej reguły. Ta grupa jest następnie powiązany tylko do podsieci frontonu i zaplecza (nie SecNet). Deklaratywne budowanego następującą regułę:

* Cały ruch (wszystkich portów) z Internetu do całej sieci wirtualnej (wszystkie podsieci) zostanie odrzucone.

Mimo że sieciowe grupy zabezpieczeń są używane w tym przykładzie, ich głównym celem jest jako dodatkowej warstwy chroniącej przed ręczne błędnej konfiguracji. Celem jest, aby zablokować cały ruch przychodzący z Internetu, albo z podsieciami frontonu i zaplecza. Ruch tylko powinna przepływać za pośrednictwem podsieci SecNet do zapory (a następnie, jeśli to stosowne, się do podsieci frontonu lub zaplecza). Ponadto za pomocą reguł trasy zdefiniowanej przez użytkownika w miejscu, cały ruch, która została wprowadzona do podsieci frontonu lub zaplecza będzie nastąpi przekierowanie się do zapory (dzięki trasy zdefiniowanej przez użytkownika). Zapora widział ten ruch jako przepływ asymetryczne i będzie pomijać ruch wychodzący. Dlatego istnieją trzy warstwy zabezpieczeń, ochrony podsieci:

* Nie publicznych adresów IP na wszystkich frontonu lub zaplecza kart sieciowych.
* Sieciowe grupy zabezpieczeń odmawianie ruch z Internetu.
* Ruch asymetrycznego usuwanie zapory.

Jeden punkt interesujące dotyczące sieciowej grupy zabezpieczeń, w tym przykładzie jest zawieranie przez nią tylko jedną regułę, która umożliwia odmawiać ruchu internetowego do całej sieci wirtualnej, w tym podsieć zabezpieczeń. Jednak ponieważ sieciowa grupa zabezpieczeń jest powiązana tylko z podsieciami frontonu i zaplecza, reguła nie jest przetwarzane na ruch przychodzący do podsieci zabezpieczeń. W rezultacie ruch odbywa się do podsieci zabezpieczeń.

#### <a name="firewall-rules"></a>Reguły zapory
Na zaporze powinny być tworzone reguły przekazywania. Wiele reguł zapory są wymagane, ponieważ blokowanie lub funkcji przekazywania, wszystkie przychodzące, wychodzące, ruchu w sieci wirtualnej w obrębie zapory. Ponadto cały ruch przychodzący trafienia usługi zabezpieczeń publiczny adres IP (w innych portów), do przetworzenia przez zaporę. Najlepszym rozwiązaniem jest diagram logiczny przepływów, przed rozpoczęciem konfigurowania podsieci i reguł zapory, aby uniknąć przerabiać później. Poniższa ilustracja jest widok logiczny reguł zapory, w tym przykładzie:

![10]

> [!NOTE]
> Oparte na wirtualne urządzenie sieciowe używane, porty zarządzania różnią się. W tym przykładzie zapora Barracuda NextGen odwołuje się do, która korzysta z portów 22, 801 i 807. Zapoznaj się z dokumentacją dostawcy urządzenia, aby znaleźć dokładnie portów używanych do zarządzania urządzeniami używana.
>
>

#### <a name="firewall-rules-description"></a>Opis reguły zapory
Na powyższym diagramie logicznej podsieci zabezpieczeń nie jest wyświetlany, ponieważ Zapora jest tylko zasobów tej podsieci. Diagram jest wyświetlane reguły zapory i jak one logicznie blokują lub zezwalają na przepływy ruchu, a nie rzeczywiste ścieżkę routingu. Ponadto portów zewnętrznych wybrany z ruchem RDP są wyższe zakres portów (8014 — 8026) i wybrano luźno wyrównać ostatnie dwa oktety adresu IP lokalnego czytelność (na przykład serwer lokalny adres 10.0.1.4 jest skojarzony z zewnętrzny port 8014). Wszystkie porty wyższe bezkonfliktowe jednak można użyć.

W tym przykładzie należy siedem typy reguł:

* Reguły zewnętrzne (dla ruchu przychodzącego):
  1. Reguły zarządzania zapory: Ta reguła przekierowania aplikacji zezwala na ruch do przekazania do portów zarządzania wirtualnego urządzenia sieciowego.
  2. Reguły protokołu RDP (na każdym serwerze Windows): Te cztery reguły (po jednym dla każdego serwera) umożliwiają zarządzanie poszczególnych serwerów za pośrednictwem protokołu RDP. Cztery reguły protokołu RDP może być również zwijane w jednej reguły, w zależności od możliwości sieciowe urządzenie wirtualne używane.
  3. Reguły ruchu dla aplikacji: Istnieją dwa z tych reguł pierwszy ruchu frontonu sieci web, a drugi dla ruchu w sieci wewnętrznej (na przykład serwer sieci web do warstwy danych). Konfiguracja tych zasad jest zależna od architektury sieci (w którym są umieszczane serwerów) i ruchu przepływów (kierunku przepływu ruchu, a które porty są używane).
     * Pierwsza reguła zezwala na ruch rzeczywiste aplikacje do osiągnięcia serwera aplikacji. Inne zasady umożliwiające zabezpieczeniom i operacjom zarządzania, reguły ruchu aplikacji są co umożliwia użytkownikom zewnętrznym lub usługi w celu dostępu do aplikacji. Ten przykład jest jednym serwerze sieci web na porcie 80. Ten sposób pojedynczą aplikację regułę zapory przekierowuje ruch przychodzący na zewnętrzny adres IP jako wewnętrzny adres IP w sieci web serwerów. Sesja przekierowanego ruchu będzie translacji za pośrednictwem translatora adresów Sieciowych, do wewnętrznego serwera.
     * Druga reguła jest reguła zaplecza, aby zezwolić na serwerze sieci web na komunikowanie się z serwerem AppVM01 (ale nie AppVM02) za pośrednictwem dowolnego portu.
* Wewnętrzne zasady (dla ruchu w sieci wirtualnej w obrębie)
  1. Wychodzące reguły Internet: Ta reguła zezwala na ruch z żadną siecią do przekazania do wybranych sieci. Ta reguła jest zazwyczaj domyślna reguła już w zaporze, ale w stanie wyłączenia. Ta reguła powinna być włączona w tym przykładzie.
  2. Reguła DNS: Ta reguła zezwala tylko na ruch DNS (port 53) do przekazania do serwera DNS. Dla tego środowiska większość ruchu z frontonu do zaplecza jest zablokowany. Ta zasada umożliwia specjalnie DNS z dowolnej podsieci lokalnej.
  3. Podsieć do podsieci reguły: Ta reguła jest umożliwienie dowolnego serwera w tej podsieci zaplecza, połączyć się z dowolnego serwera w podsieci frontonu (ale nie odwrotnie).
* Reguła odporne na uszkodzenia (dla ruchu, który nie spełnia żadnego z poprzedniego):
  1. Odmów wszystkie reguły ruchu: Tę regułę Odmów powinna zawsze być końcowa reguła (w kategoriach priority) i w związku z tym Jeśli przepływ ruchu, które nie pasuje do żadnej z powyższych zasad zostanie usunięte przez tę regułę. Ta reguła ma reguły domyślnej i zwykle w miejscu i aktywne. Żadnych modyfikacji zazwyczaj są potrzebne do tej reguły.

> [!TIP]
> Na drugiej reguły ruchu aplikacji aby uprościć przykład dowolnego portu jest dozwolone. W przypadku rzeczywistych bardziej konkretny od pozostałych zakresy portów i adresów należy zmniejszyć obszar ataków tej reguły.
>
>

Po utworzeniu poprzednich reguł jest ważne zapoznać się z priorytetem każdej reguły, aby upewnić się, ruch jest dozwolony lub zablokowany zgodnie z potrzebami. W tym przykładzie reguły są w kolejności priorytetów.

#### <a name="conclusion"></a>Podsumowanie
W tym przykładzie jest bardziej złożone ale ukończenia sposób ochrony i izolowania sieci niż w poprzednich przykładach. (Przykład 2 chroni tylko aplikacji, a przykład 1, po prostu wyodrębnia podsieci). Ten projekt umożliwia monitorowanie ruchu w obu kierunkach i chroni nie tylko na serwerze aplikacji dla ruchu przychodzącego, ale wymusza zasady zabezpieczeń sieci dla wszystkich serwerów w tej sieci. Ponadto w zależności od urządzenia używane pełnego ruchu inspekcji i rozpoznawanie można osiągnąć. Aby uzyskać więcej informacji, zobacz [szczegółowe instrukcje kompilacji][Example3]. Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej, za pomocą klasycznego skryptów programu PowerShell.
* Jak skompilować ten przykład przy użyciu szablonu usługi Azure Resource Manager.
* Szczegółowy opis każdej trasy zdefiniowanej przez użytkownika, sieciowej grupy zabezpieczeń polecenia i reguły zapory.
* Scenariusze przepływu ruchu szczegółowe, pokazujący, jak ruch jest dozwolony lub zablokowany w każdej warstwie.

### <a name="example-4-add-a-hybrid-connection-with-a-site-to-site-virtual-appliance-vpn"></a>Przykład 4 Dodaj połączenie hybrydowe z site-to-site, wirtualne urządzenie sieci VPN
[Powrót do szybkiego rozpoczęcia](#fast-start) | Szczegółowe instrukcje kompilacji dostępne wkrótce

[![11]][11]

#### <a name="environment-description"></a>Opis środowiska
Sieci hybrydowe przy użyciu wirtualnego urządzenia sieciowego (WUS) można dodać do dowolnego typu sieci obwodowej, opisanego w przykładach 1, 2 lub 3.

Jak pokazano na poprzedniej ilustracji, połączenie sieci VPN za pośrednictwem Internetu (site-to-site) umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem urządzenia WUS.

> [!NOTE]
> Jeśli korzystasz z usługi ExpressRoute z włączoną opcją publicznej komunikacji równorzędnej Azure, należy utworzyć trasę statyczną. Tej trasy statycznej należy kierować na adres IP sieci VPN urządzenia WUS się sieci firmowej Internet, a nie za pośrednictwem połączenia usługi ExpressRoute. Translator adresów Sieciowych wymaganych opcji Azure publicznej komunikacji równorzędnej usługi ExpressRoute może przerwać sesję sieci VPN.
>
>

Gdy sieć VPN znajduje się w miejscu, urządzenie WUS staje się Centrum dla wszystkich podsieci i sieci. Zasady przekazywania zapory określają przepływy ruchu, które są dozwolone, są tłumaczone za pośrednictwem translatora adresów Sieciowych, są przekierowywane lub są porzucane (nawet w przypadku widok przepływów ruchu sieciowego między siecią lokalną a platformą Azure).

Widok przepływów ruchu sieciowego należy dokładnie rozważyć, jak można zoptymalizować, lub ograniczone w tym wzorcu projektowym w zależności od określonego przypadek użycia.

Przy użyciu środowiska utworzone w przykładzie 3, a następnie dodanie połączenia sieciowego hybrydowego sieci VPN typu lokacja lokacja, generuje następujący wzór:

[![12]][12]

Router lokalny lub innego urządzenia sieciowego, który jest zgodny z Twojego urządzenia WUS dla sieci VPN, byłoby klienta sieci VPN. To fizyczne urządzenie będzie odpowiedzialny za inicjowanie i utrzymywania połączenia sieci VPN z Twojego urządzenia WUS.

Logicznie do urządzenia WUS, sieci wygląda cztery oddzielne "strefy zabezpieczeń" z zasadami na urządzeniu WUS są podstawowym Dyrektor ds. ruch sieciowy między tych stref:

![13]

#### <a name="conclusion"></a>Podsumowanie
Dodawanie połączenia sieciowego hybrydowej sieci VPN lokacja lokacja z siecią wirtualną platformy Azure można rozszerzyć sieć lokalną na platformę Azure w bezpieczny sposób. Korzystając z połączenia sieci VPN, ruch jest szyfrowany i kieruje za pośrednictwem Internetu. Urządzenie WUS w tym przykładzie stanowi centralną lokalizację, aby wymusić i zarządzanie zasadami zabezpieczeń. Aby uzyskać więcej informacji zobacz instrukcje szczegółowe kompilacji (nadchodzących). Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej, za pomocą skryptów programu PowerShell.
* Jak skompilować ten przykład przy użyciu szablonu usługi Azure Resource Manager.
* Scenariuszy przepływów ruchu szczegółowe, pokazujący, jak ruch przechodzi przez ten projekt.

### <a name="example-5-add-a-hybrid-connection-with-a-site-to-site-azure-vpn-gateway"></a>Przykład 5 Dodaj połączenie hybrydowe z bramą Azure VPN lokacja lokacja,
[Powrót do szybkiego rozpoczęcia](#fast-start) | Szczegółowe instrukcje kompilacji dostępne wkrótce

[![14]][14]

#### <a name="environment-description"></a>Opis środowiska
Sieci hybrydowe za pomocą bramy sieci VPN platformy Azure można dodać do dowolnego typu sieci obwodowej, opisanego w przykładach 1 lub 2.

Jak pokazano na poprzednim rysunku, połączenie sieci VPN za pośrednictwem Internetu (site-to-site) umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem bramy sieci VPN platformy Azure.

> [!NOTE]
> Jeśli korzystasz z usługi ExpressRoute z włączoną opcją publicznej komunikacji równorzędnej Azure, należy utworzyć trasę statyczną. Tej trasy statycznej należy kierować na adres IP sieci VPN urządzenia WUS, limit sieci firmowej Internet, a nie za pomocą usługi ExpressRoute WAN. Translator adresów Sieciowych wymaganych opcji Azure publicznej komunikacji równorzędnej usługi ExpressRoute może przerwać sesję sieci VPN.
>
>

Na poniższej ilustracji przedstawiono dwie krawędzi sieci, w tym przykładzie. Na krawędzi pierwszego urządzenia WUS i sieciowymi grupami zabezpieczeń kontrolowania przepływu ruchu dla sieci w obrębie platformy Azure i między platformą Azure i Internetem. Drugi edge jest bramy sieci VPN platformy Azure, która jest przewagę oddzielnych sieci między lokalną i platformą Azure.

Widok przepływów ruchu sieciowego należy dokładnie rozważyć, jak można zoptymalizować, lub ograniczone w tym wzorcu projektowym w zależności od określonego przypadek użycia.

Przy użyciu środowiska utworzone w przykładzie 1, a następnie dodanie połączenia sieciowego hybrydowego sieci VPN typu lokacja lokacja, generuje następujący wzór:

[![15]][15]

#### <a name="conclusion"></a>Podsumowanie
Dodawanie połączenia sieciowego hybrydowej sieci VPN lokacja lokacja z siecią wirtualną platformy Azure można rozszerzyć sieć lokalną na platformę Azure w bezpieczny sposób. Za pomocą natywnego bramy sieci VPN platformy Azure, ruch sieciowy jest szyfrowane protokołu IPSec i kieruje za pośrednictwem Internetu. Ponadto przy użyciu bramy sieci VPN platformy Azure może zapewnić to opcja tańszych (nie dodatkowych licencji kosztów, podobnie jak w przypadku urządzeń WUS innych firm). Ta opcja jest najbardziej ekonomiczne w przykładzie 1, gdy jest używana nie urządzenia WUS. Aby uzyskać więcej informacji zobacz instrukcje szczegółowe kompilacji (nadchodzących). Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej, za pomocą skryptów programu PowerShell.
* Jak skompilować ten przykład przy użyciu szablonu usługi Azure Resource Manager.
* Scenariuszy przepływów ruchu szczegółowe, pokazujący, jak ruch przechodzi przez ten projekt.

### <a name="example-6-add-a-hybrid-connection-with-expressroute"></a>Przykład 6 Dodawanie połączenia hybrydowego za pomocą usługi ExpressRoute
[Powrót do szybkiego rozpoczęcia](#fast-start) | Szczegółowe instrukcje kompilacji dostępne wkrótce

[![16]][16]

#### <a name="environment-description"></a>Opis środowiska
Sieci hybrydowe przy użyciu połączenia prywatnej komunikacji równorzędnej usługi ExpressRoute można dodać do dowolnego typu sieci obwodowej, opisanego w przykładach 1 lub 2.

Jak pokazano na poprzednim rysunku, prywatnej komunikacji równorzędnej usługi ExpressRoute zapewnia bezpośrednie połączenie między siecią lokalną i sieci wirtualnej platformy Azure. Ruch transits sieci dostawcy usług i sieci Microsoft Azure nigdy nie dotknięcie z Internetem.

> [!TIP]
> Przy użyciu usługi ExpressRoute zachowuje firmowej ruch sieciowy poza Internetem. Umożliwia ona również umów dotyczących poziomu usług od dostawcy usługi ExpressRoute. Brama platformy Azure można przekazać maksymalnie 10 GB/s przy użyciu usługi ExpressRoute, za pomocą sieci VPN typu lokacja lokacja, maksymalna przepływność bramy platformy Azure jest 200 MB/s.
>
>

Jak widać na poniższym diagramie, z tą opcją środowiska ma teraz dwie krawędzi sieci. Urządzenie WUS i sieciowej grupy zabezpieczeń kontrolować przepływy ruchu dla sieci w obrębie platformy Azure i między platformą Azure i Internet, podczas gdy brama jest przewagę oddzielnych sieci między lokalną i platformą Azure.

Widok przepływów ruchu sieciowego należy dokładnie rozważyć, jak można zoptymalizować, lub ograniczone w tym wzorcu projektowym w zależności od określonego przypadek użycia.

Przy użyciu środowiska utworzone w przykładzie 1, a następnie dodanie połączenia sieci hybrydowych usługi ExpressRoute generuje następujący wzór:

[![17]][17]

#### <a name="conclusion"></a>Podsumowanie
Dodawanie komunikacji równorzędnej usługi ExpressRoute, prywatne połączenie sieciowe można rozszerzyć sieć lokalną na platformę Azure w bezpieczny, niższe opóźnienia wyższej wykonywania sposób. Ponadto za pomocą natywnego bramy platformy Azure, jak w poniższym przykładzie, udostępnia opcję tańszych (Brak dodatkowych licencji, podobnie jak w przypadku urządzeń WUS innych firm). Aby uzyskać więcej informacji zobacz instrukcje szczegółowe kompilacji (nadchodzących). Instrukcje te obejmują:

* Jak utworzyć ten przykład sieci obwodowej, za pomocą skryptów programu PowerShell.
* Jak skompilować ten przykład przy użyciu szablonu usługi Azure Resource Manager.
* Scenariuszy przepływów ruchu szczegółowe, pokazujący, jak ruch przechodzi przez ten projekt.

## <a name="references"></a>Dokumentacja
### <a name="helpful-websites-and-documentation"></a>Przydatne witryn sieci Web i dokumentacji
* Dostęp do platformy Azure za pomocą usługi Azure Resource Manager:
* Uzyskiwanie dostępu do platformy Azure przy użyciu programu PowerShell: [https://docs.microsoft.com/powershell/azureps-cmdlets-docs/](/powershell/azure/overview)
* Wirtualne sieci dokumentacji: [https://docs.microsoft.com/azure/virtual-network/](https://docs.microsoft.com/azure/virtual-network/)
* Dokumentacja grupy zabezpieczeń sieci: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg](virtual-network/security-overview.md)
* Dokumentacja routing zdefiniowany przez użytkownika: [https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview](virtual-network/virtual-networks-udr-overview.md)
* Bramy wirtualnej platformy Azure: [https://docs.microsoft.com/azure/vpn-gateway/](https://docs.microsoft.com/azure/vpn-gateway/)
* Sieci VPN lokacja lokacja: [https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell](vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* Dokumentacja usługi ExpressRoute (koniecznie zapoznaj się z sekcji "Wprowadzenie" i "How to"): [https://docs.microsoft.com/azure/expressroute/](https://docs.microsoft.com/azure/expressroute/)

<!--Image References-->
[0]: ./media/best-practices-network-security/flowchart.png "schemat blokowy opcje zabezpieczeń"
[2]: ./media/best-practices-network-security/azuresecurityfeatures.png "funkcji zabezpieczeń platformy azure"
[3]: ./media/best-practices-network-security/dmzcorporate.png "Sieć obwodowa A w sieci firmowej"
[4]: ./media/best-practices-network-security/azuresecurityarchitecture.png "architektury zabezpieczeń platformy azure"
[5]: ./media/best-practices-network-security/dmzazure.png "Sieć obwodowa w sieci wirtualnej platformy Azure"
[6]: ./media/best-practices-network-security/dmzhybrid.png "sieci hybrydowej za pomocą trzech granic zabezpieczeń"
[7]: ./media/best-practices-network-security/example1design.png "ruchu przychodzącego sieci obwodowej z grupy NSG"
[8]: ./media/best-practices-network-security/example2design.png "ruchu przychodzącego sieci obwodowej NVA i grupy NSG"
[9]: ./media/best-practices-network-security/example3design.png "Dwukierunkowa sieć obwodowa z NVA, NSG i przez"
[10]: ./media/best-practices-network-security/example3firewalllogical.png "widok logiczny reguł zapory"
[11]: ./media/best-practices-network-security/example3designoptions.png "Sieć obwodowa z NVA połączonych sieci hybrydowe"
[12]: ./media/best-practices-network-security/example4designs2s.png "Sieć obwodowa z NVA nawiązano połączenie przy użyciu sieci VPN lokacja lokacja"
[13]: ./media/best-practices-network-security/example4networklogical.png "sieci logicznej z perspektywy urządzenia WUS"
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
