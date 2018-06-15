---
title: Pojęcia dotyczące zabezpieczeń i wymagania dotyczące platformy Azure sieci | Dokumentacja firmy Microsoft
description: W tym artykule przedstawiono podstawowe wyjaśnienia dotyczące pojęcia dotyczące zabezpieczeń sieci podstawowej i wymagania i informacje na system Azure oferuje w każdym z tych obszarów.
services: security
documentationcenter: na
author: TomShinder
manager: MBaldwin
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: terrylan
ms.openlocfilehash: fbd589aedb955ee4bd61dc0ec754d8713a98179a
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34365631"
---
# <a name="azure-network-security-overview"></a>Przegląd zabezpieczeń sieci platformy Azure
Azure obejmuje to niezawodna infrastruktura sieci do obsługi aplikacji i wymaganiami dotyczącymi łączności usługi. Łączność sieciowa będzie możliwe między zasobami znajdującymi się na platformie Azure, między lokalnymi i Azure hostowanych zasobów oraz do i z Internetu i Azure.

Celem tego artykułu jest wyjaśnienie Azure oferuje w zakresie zabezpieczeń sieci. Oprócz podstawowych objaśnienia dotyczące pojęcia dotyczące zabezpieczeń sieci podstawowej i wymagania informacje na temat:

* Sieć platformy Azure
* Kontrola dostępu do sieci
* Bezpiecznego połączenia zdalnego dostępu i między lokalizacjami
* Dostępność
* Rozpoznawanie nazw
* Architektura (DMZ) sieci obwodowej
* Monitorowanie i wykrywania zagrożeń

## <a name="azure-networking"></a>Sieć platformy Azure
Maszyny wirtualne muszą łączność sieciową. W celu spełnienia tego wymagania, platforma Azure wymaga maszyn wirtualnych, które będą podłączone do sieci wirtualnej platformy Azure. Sieć wirtualna jest konstrukcją logiczną, rozszerzający fizycznej Azure sieci szkieletowej. Każdy logicznej sieć wirtualna jest odizolowana od wszystkich sieci wirtualnych. Pomaga to zapewnić, że ruchu sieciowego w ramach wdrożeń nie jest dostępna dla innych klientów platformy Azure.

Więcej informacji:

* [Omówienie sieci wirtualnej](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Kontrola dostępu do sieci
Kontrola dostępu do sieci jest czynnością ograniczenia łączności do i z określonymi urządzeniami lub podsieci w sieci wirtualnej. Celem kontroli dostępu do sieci jest ograniczenie dostępu do maszyn wirtualnych i usług dla zatwierdzonych użytkowników i urządzeń. Kontroli dostępu są oparte na decyzje, aby akceptować lub odrzucać połączenia do i z sieci maszyny wirtualnej lub usługi.

Azure obsługuje kilka typów kontroli dostępu do sieci, takich jak:

* Kontrola warstwy sieci
* Trasy kontroli oraz wymuszonego tunelowania
* Urządzenia zabezpieczeń sieci wirtualnej

### <a name="network-layer-control"></a>Kontrola warstwy sieci
Wszystkie wdrożenia bezpiecznego wymaga niektóre miary kontroli dostępu do sieci. Celem kontroli dostępu do sieci jest ograniczyć komunikacji maszyny wirtualnej na potrzeby systemów. Inne próby komunikacji są zablokowane.

Jeśli potrzebujesz kontroli dostępu na poziomie sieci podstawowej (na podstawie adresu IP i protokoły TCP lub UDP), można użyć grup zabezpieczeń sieci (NSG). Grupa NSG jest basic, stateful, filtrowania zapory pakietów i umożliwia kontrolowanie dostępu na podstawie [5-elementowej](https://www.techopedia.com/definition/28190/5-tuple). Grupy NSG nie udostępniają kontroli warstwy aplikacji lub uwierzytelnionego kontroli dostępu.

Więcej informacji:

* [Grupy zabezpieczeń sieci](../virtual-network/security-overview.md)

### <a name="route-control-and-forced-tunneling"></a>Trasy kontroli oraz wymuszonego tunelowania
Możliwość kontrolowania zachowania routingu w sieci wirtualnej jest krytyczna. Jeśli routing jest niepoprawnie skonfigurowana, aplikacji i usług hostowanych na maszynie wirtualnej może się połączyć nieautoryzowanego urządzenia, w tym systemów i jest przez potencjalnymi atakami.

Sieć platformy Azure obsługuje możliwość dostosowania zachowania routingu ruchu w sieci dla sieci wirtualnej. Dzięki temu można zmienić domyślny routing wpisów tabeli w Twojej sieci wirtualnej. Kontroli zachowania routingu pomaga upewnij się, że cały ruch z niektórych urządzeń lub grupy urządzeń wprowadza lub pozostawia sieci wirtualnej do określonej lokalizacji.

Na przykład może być urządzenie zabezpieczeń sieci wirtualnych w sieci wirtualnej. Chcesz upewnij się, że cały ruch do i z sieci wirtualnej przechodzi przez tego urządzenia wirtualnego zabezpieczeń. Można to zrobić przez skonfigurowanie [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) (Udr) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) mechanizm służy do zapewnienia usług nie są dozwolone do nawiązania połączenia z urządzeniami przez Internet. Należy pamiętać, że różni się to od przyjmowanie połączeń przychodzących, a następnie odpowiada do nich. Serwerów frontonu sieci web muszą odpowiadać na żądania z hostami w Internecie, a więc internet-powierzając jej ich konserwację ruch jest dozwolony przychodzące do tych serwerów sieci web i serwerów sieci web mogą odpowiadać.

Co nie chcesz umożliwić to serwer frontonu sieci web do zainicjowania żądania wychodzącego. Takich żądań może reprezentować zagrożenie bezpieczeństwa, ponieważ te połączenia służy do pobierania złośliwego oprogramowania. Nawet jeśli chcesz, aby te serwery frontonu do inicjowania żądań wychodzące z Internetem, można wymusić przechodzić przez serwer proxy sieci web w sieci lokalnej. Dzięki temu można korzystać z adresu URL filtrowanie i rejestrowania.

Czy chcesz zamiast tego użyj tunelowania wymuszonego, aby zapobiec takiej sytuacji. Włączenie tunelowania wymuszonego, wszystkie połączenia z Internetem będą obowiązkowo przenoszone za pośrednictwem bramy sieci lokalnej. Można skonfigurować wymuszanie tunelowania dzięki wykorzystaniu Udr.

Więcej informacji:

* [Co to są trasy zdefiniowane przez użytkownika i przesyłania dalej IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń sieci wirtualnej
Gdy grup NSG, Udr i wymuszanie tunelowania zapewniają poziom zabezpieczeń na poziomie warstwy sieci i transportu [OSI model](https://en.wikipedia.org/wiki/OSI_model), można także włączyć zabezpieczenia na poziomie wyższym niż sieć.

Na przykład wymagań dotyczących zabezpieczeń mogą być następujące:

* Uwierzytelnianie i autoryzacja przed zezwoleniem na dostęp do aplikacji
* Wykrywania nieautoryzowanego dostępu i odpowiedzi nieautoryzowanego dostępu
* Kontrolę warstwy aplikacji protokołów wysokiego poziomu
* Filtrowanie adresów URL
* Oprogramowanie antywirusowe poziomu sieci i ochrony przed złośliwym oprogramowaniem
* Ochrona przed bot
* Kontrola dostępu aplikacji
* Dodatkowa ochrona przed atakami DDoS (powyżej ochrony przed atakami DDoS zapewnianej przez sieci szkieletowej Azure, sam)

Te funkcje zabezpieczeń rozszerzonych sieci mogą korzystać za pomocą rozwiązania Azure partnera. Można znaleźć najbardziej aktualne sieci Azure partnerów rozwiązań zabezpieczeń poprzez wizytę [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/)i wyszukując "zabezpieczenia" i "zabezpieczenia sieciowe".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Bezpiecznego połączenia zdalnego dostępu i między lokalizacjami
Instalacji, konfiguracji i zarządzania potrzeb zasobów platformy Azure, można to robić zdalnie. Ponadto może być konieczne wdrożenie [hybrydowego IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) rozwiązania, które ma składniki lokalnej i w chmurze publicznej Azure. Te scenariusze wymaga bezpiecznego dostępu zdalnego.

Sieć platformy Azure obsługuje następujące scenariusze bezpieczny dostęp zdalny:

* Połącz stacjach roboczych do sieci wirtualnej
* Łączenie sieci lokalnej sieci wirtualnej z sieci VPN
* Połączyć sieć lokalną sieć wirtualną z dedykowanym łącza sieci WAN
* Sieci wirtualne się ze sobą łączyć

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Połącz stacjach roboczych do sieci wirtualnej
Można włączyć indywidualnych deweloperów lub personel do zarządzania maszynami wirtualnymi i usługami na platformie Azure. Załóżmy na przykład, że chcesz uzyskać dostęp do maszyny wirtualnej w sieci wirtualnej. Ale zasady zabezpieczeń nie zezwala na połączenie RDP lub SSH zdalny dostęp do poszczególnych maszyn wirtualnych. W takim przypadku można użyć połączenia sieci VPN punkt lokacja.

Połączenie VPN punkt lokacja używa [sieć VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) protokołu umożliwiają konfigurowanie prywatnego i bezpieczne połączenie między użytkownikiem i sieci wirtualnej. Po nawiązaniu połączenia sieci VPN, użytkownik może RDP lub SSH za pośrednictwem łącza sieci VPN do maszyn wirtualnych w sieci wirtualnej. (Przy założeniu, że użytkownik może uwierzytelnić i daje uprawnienia).

Więcej informacji:

* [Skonfiguruj połączenie punkt lokacja sieci wirtualnej przy użyciu programu PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Łączenie sieci lokalnej sieci wirtualnej z sieci VPN
Możesz połączyć całej sieci firmowej, lub w części, sieć wirtualną. To jest typowe w przypadku hybrydowych IT scenariuszy, których organizacje [rozszerzenie ich lokalnych centrów danych na platformie Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). W wielu przypadkach organizacje hosta usługi w programie Azure i części lokalnymi. Na przykład one mogą robić, gdy rozwiązanie zawiera serwery frontonu sieci web na platformie Azure i wewnętrznej bazy danych lokalnych. Typy połączeń "między lokalizacjami" Upewnij się również zarządzania Azure znajduje się zasoby bardziej bezpieczny i Włącz scenariuszy, takich jak rozszerzanie kontrolerów domeny usługi Active Directory na platformie Azure.

Aby wykonać to jest użycie [sieci VPN typu lokacja lokacja](https://www.techopedia.com/definition/30747/site-to-site-vpn). Różnica między VPN lokacja lokacja i sieć VPN punkt lokacja jest, że jego pojedyncze urządzenie łączy się z sieci wirtualnej. Sieć VPN lokacja lokacja łączy całej sieci (na przykład sieci lokalnej) do sieci wirtualnej. Sieci VPN typu lokacja lokacja, do sieci wirtualnej Użyj wysokim poziomie zabezpieczeń trybu tunelowania IPsec protokołu sieci VPN.

Więcej informacji:

* [Tworzenie sieci wirtualnej Resource Manager za pomocą połączenia sieci VPN lokacja lokacja przy użyciu portalu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planowanie i projektowanie bramy sieci VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Połączyć sieć lokalną sieć wirtualną z dedykowanym łącza sieci WAN
Połączenia VPN punkt lokacja i lokacja lokacja obowiązują umożliwiających łączności między lokalizacjami. Jednak niektóre organizacje wziąć pod uwagę ich ma następujące wady:

* Połączenia sieci VPN przenoszenia danych za pośrednictwem Internetu. Udostępnia to tych połączeń potencjalne problemy związane z przenoszenia danych w sieci publicznej. Ponadto nie można zagwarantować niezawodność i dostępność dla połączenia z Internetem.
* Połączenia sieci VPN do sieci wirtualnych mogą nie mieć przepustowości dla niektórych aplikacji i celów, w miarę ich maksymalny limit na około 200 MB/s.

Organizacji, które zwykle potrzebują najwyższy poziom zabezpieczeń i dostępności do ustanawiania połączeń między różnymi lokalizacjami Użyj dedykowane łącza sieci WAN, aby połączyć się z lokacjami zdalnymi. Platforma Azure udostępnia możliwość używania wydzielonego łącza sieci WAN, można użyć, aby połączyć sieć lokalną z siecią wirtualną. Usługa Azure ExpressRoute umożliwia to.

Więcej informacji:

* [Opis techniczny ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-virtual-networks-to-each-other"></a>Sieci wirtualne się ze sobą łączyć
Istnieje możliwość wiele sieci wirtualnych na potrzeby wdrożeń. Istnieją różne przyczyny, dlaczego można to zrobić. Można uprościć zarządzanie, można też zwiększyć bezpieczeństwo. Niezależnie od motywacją umieszczanie zasobów w różnych sieciach wirtualnych może być godziny zużycia zasobów w każdej sieci, aby połączyć się ze sobą.

Jedną z opcji jest dla usług na jedną sieć wirtualną do łączenia się usługami w innej sieci wirtualnej, "pętli ponownie" za pośrednictwem Internetu. Połączenie rozpoczyna się w jednej wirtualnej sieci, odbywa się przez internet, a następnie wróci do docelowej sieci wirtualnej. Ta opcja udostępnia połączenie na problemy dotyczące zabezpieczeń związane z dowolnego komunikacji internetowego.

Lepszym rozwiązaniem może być można utworzyć sieci VPN lokacja lokacja, która nawiązuje połączenie między dwiema sieciami wirtualnymi. Ta metoda używa takie same [trybu tunelowania IPsec](https://technet.microsoft.com/library/cc786385.aspx) protokołem wymienione powyżej połączenia sieci VPN między lokalizacjami lokacja lokacja.

Zaletą tej metody jest ustanowienie połączenia sieci VPN za pośrednictwem sieci szkieletowej sieć platformy Azure, a nie połączenie za pośrednictwem Internetu. Zapewnia dodatkową warstwę zabezpieczeń, w porównaniu z sieciami VPN lokacja lokacja, które łączą się przez internet.

Więcej informacji:

* [Konfigurowanie połączenia do wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Dostępność
Dostępność jest kluczowym składnikiem programu zabezpieczeń. Jeśli muszą uzyskiwać dostęp za pośrednictwem sieci nie dostępu użytkowników i systemów, usługa jest uznawana za naruszenia zabezpieczeń. Platforma Azure ma technologii sieciowych, które obsługują następujące mechanizmy wysokiej dostępności:

* Równoważenie obciążenia oparte na protokole HTTP
* Równoważenie obciążenia poziomu sieci
* Globalnego równoważenia obciążenia

Równoważenie obciążenia sieciowego jest mechanizm przeznaczony do jednakowo dystrybucji połączeń między wieloma urządzeniami. Cele równoważenia obciążenia są:

* Aby zwiększyć dostępność. Podczas ładowania saldo połączeń na wielu urządzeniach, co najmniej jedno urządzenie może staną się niedostępne bez naruszania usługi. Usługi działające na pozostałych urządzeń online można nadal udostępniać zawartość z usługi.
* W celu zwiększenia wydajności. Podczas ładowania saldo połączeń na wielu urządzeniach, pojedyncze urządzenie nie ma do obsługi przetwarzania. Zamiast tego żądania przetwarzania i pamięci do obsługi zawartości zostanie rozmieszczona na wielu urządzeniach.

### <a name="http-based-load-balancing"></a>Równoważenie obciążenia oparte na protokole HTTP
Organizacje, które działa usług sieci web często chcesz mieć usługi równoważenia obciążenia opartą na protokole HTTP przed tych usług sieci web. Pomaga to zapewnić odpowiednie poziomy wydajności i wysokiej dostępności. Moduły równoważenia obciążenia tradycyjnych, opartych na sieci korzystają z protokołami warstwy transportu i sieci. Równoważenia obciążenia opartą na protokole HTTP, z drugiej strony, decyzje na podstawie charakterystyk protokołu HTTP.

Brama aplikacji Azure zapewnia oparte na protokole HTTP równoważenia obciążenia dla usług opartych na sieci web. Brama aplikacji w obsługuje:

* Koligacji na podstawie plików cookie sesji. Ta funkcja zapewnia, że połączeń ustanowionych z jednym z serwerów za ten moduł równoważenia obciążenia pozostaje niezmieniona między klientem i serwerem. Dzięki temu stabilności transakcji.
* Odciążanie protokołu SSL. Gdy klient nawiąże połączenie z modułem równoważenia obciążenia, czy sesja jest szyfrowana przy użyciu protokołu HTTPS (SSL). Jednak aby zwiększyć wydajność, można użyć protokołu HTTP (bez szyfrowania) nawiązywania połączenia między usługi równoważenia obciążenia i serwer sieci web związanej z modułem równoważenia obciążenia. Jest to określane jako "Odciążanie protokołu SSL", ponieważ serwery sieci web związanej z modułem równoważenia obciążenia nie występuje obciążenie procesora związane z szyfrowaniem. Serwery sieci web w związku z tym można szybciej obsługi żądań.
* Adres URL na podstawie zawartości routingu. Ta funkcja umożliwia podjęcie decyzji o gdzie należy do przodu połączenia, w oparciu o docelowy adres URL usługi równoważenia obciążenia. Zapewnia to znacznie większą elastyczność niż rozwiązania, które należy załadować równoważenia decyzje na podstawie adresów IP.

Więcej informacji:

* [Omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Równoważenie obciążenia poziomu sieci
W przeciwieństwie do równoważenia obciążenia oparty na protokole HTTP Równoważenie obciążenia poziomu sprawia, że decyzje oparte na adres i port (TCP lub UDP) numery IP.
Aby uzyskać korzyści wynikające z poziomu równoważenia obciążenia sieciowego w Azure przy użyciu usługi równoważenia obciążenia Azure. Niektóre właściwości klucza usługi równoważenia obciążenia obejmują:

* Równoważenie obciążenia poziomu na podstawie liczby adres i port IP.
* Obsługa protokołu warstwy żadnych aplikacji.
* Równoważy obciążenia na maszynach wirtualnych platformy Azure i wystąpień roli usług w chmurze.
* Można użyć zarówno internetowy (zewnętrzne Równoważenie obciążenia sieciowego), jak i z systemem innym niż internet ukierunkowane maszyn wirtualnych i aplikacji (równoważenia obciążenia wewnętrznego).
* Punkt końcowy monitorowania, który służy do określania, jeśli którakolwiek z nich za usługą równoważenia obciążenia stały się niedostępne.

Więcej informacji:

* [Moduł równoważenia obciążenia internetowy między wielu maszyn wirtualnych lub usług](../load-balancer/load-balancer-internet-overview.md)
* [Omówienie usługi równoważenia obciążenia wewnętrznego](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globalnego równoważenia obciążenia
Niektóre organizacje mają możliwe najwyższy poziom dostępności. Jednym ze sposobów osiągnięcia tego celu jest umożliwia obsługę aplikacji w rozproszonych globalnie centrach danych. Gdy aplikacja jest obsługiwana w centrach danych znajdujących się na całym świecie, jest możliwe całego regionu geograficznymi staną się niedostępne, a jeszcze aplikacji w i działa.

Ta strategia równoważenia obciążenia można również uzyskanie zwiększenia wydajności. Można przekierować żądania dotyczące usługi do centrum danych, który znajduje się najbliżej urządzenia, do której wysłano żądanie.

Na platformie Azure Aby uzyskać korzyści wynikające z globalnego równoważenia obciążenia za pomocą usługi Azure Traffic Manager.

Więcej informacji:

* [Co to jest usługa Traffic Manager?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Rozpoznawanie nazw
Rozpoznawanie nazw jest funkcją krytyczne dla wszystkich usług, które są hostowane na platformie Azure. Z punktu widzenia zabezpieczeń naruszenia funkcja rozpoznawania nazw może prowadzić do przekierowywania żądań z lokacji do lokacji osoba atakująca osoba atakująca. Rozpoznawanie nazw bezpiecznego jest wymagane dla wszystkich usług w chmurze hostowanej.

Istnieją dwa typy rozpoznawania nazw, które należy rozwiązać:

* Rozpoznawania nazw wewnętrznych. To jest używany przez usługi w sieci wirtualnej i sieci lokalnej. Nazwy używane do rozpoznawania nazw wewnętrznych nie są dostępne za pośrednictwem Internetu. Optymalne zabezpieczeń ważne jest schematem rozpoznawania nazw wewnętrznych nie jest dostępna dla użytkowników zewnętrznych.
* Rozpoznawanie nazw zewnętrznych. To jest używany przez osoby i urządzenia poza sieci lokalnej i sieci wirtualnych. Są to nazwy, które są widoczne w Internecie i umożliwia bezpośrednie połączenie z usługami w chmurze.

Do rozpoznawania nazw wewnętrznych dostępne są dwie opcje:

* Serwer DNS sieci wirtualnej. Podczas tworzenia nowej sieci wirtualnej, serwer DNS jest tworzony automatycznie. Ten serwer DNS może rozpoznawania nazw komputerów znajdujących się w tej sieci wirtualnej. Ten serwer DNS nie jest konfigurowany, jest zarządzany przez Menedżera sieci szkieletowej Azure i w związku z tym pomoże Ci zabezpieczyć rozwiązanie sieci rozpoznawania nazwy.
* Przełącz serwer DNS. Istnieje możliwość wprowadzenia serwer DNS wybranej przez użytkownika w sieci wirtualnej. Ten serwer DNS może być zintegrowane usługi Active Directory, serwer DNS lub dedykowane rozwiązania serwera DNS podany przez partnera Azure, który można uzyskać w witrynie Azure Marketplace.

Więcej informacji:

* [Omówienie sieci wirtualnej](../virtual-network/virtual-networks-overview.md)
* [Zarządzanie serwerami DNS używanymi przez sieć wirtualną](../virtual-network/manage-virtual-network.md#change-dns-servers)

Aby rozpoznawanie nazw zewnętrznych dostępne są dwie opcje:

* Host własne zewnętrznych DNS serwera lokalnego.
* Host własne zewnętrznego serwera DNS z dostawcą usług.

W wielu organizacjach dużych obsługiwać własne DNS serwerów lokalnych. Mogą one to robić ponieważ mają one doświadczenia z sieci i globalnych obecności w tym celu.

W większości przypadków najlepiej hosta z usługi rozpoznawania nazw DNS z dostawcą usług. Ci dostawcy usług mają doświadczenia z sieci i globalne obecności w celu zapewnienia bardzo wysoka dostępność dla Twojej usługi rozpoznawania nazw. Dostępność jest istotne dla usługi DNS, ponieważ w przypadku awarii z usługi rozpoznawania nazw, nie będzie mogła nawiązać połączenia z internetowy usług.

Platforma Azure udostępnia o wysokiej dostępności i wydajności zewnętrznych DNS rozwiązanie w formie usługi Azure DNS. To rozwiązanie rozpoznawania nazw zewnętrznych korzysta z infrastruktury usługi Azure DNS na całym świecie. Umożliwia hostowanie domenę na platformie Azure przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń jak innymi usługami Azure. W ramach platformy Azure również dziedziczy formanty silne zabezpieczenie wbudowanych w platformy.

Więcej informacji:

* [Omówienie usługi Azure DNS](../dns/dns-overview.md)

## <a name="perimeter-network-architecture"></a>Architektura sieci obwodowej
W wielu organizacjach dużych segmentów swoich sieci przy użyciu sieci obwodowej, a utworzyć strefę buforu od Internetu, a ich usług. Część sieci obwodowej jest uznawany za strefy niskim poziomie zabezpieczeń, a nie wartościowe zasoby są umieszczane w tym segmencie sieci. Zwykle zobaczysz urządzeniach zabezpieczeń sieciowych z karty sieciowej w segmencie sieci obwodowej. Inny interfejs sieciowy jest połączony z siecią mającą maszyn wirtualnych i usług, które akceptują połączenia przychodzące z Internetu.

Na kilka różnych sposobów, można zaprojektować sieci obwodowej. Decyzja o wdrożeniu sieci obwodowej, a następnie sieci obwodowej jakiego rodzaju do użycia, jeśli zdecydujesz się używać jednego, zależy od wymagań dotyczących zabezpieczeń sieci.

Więcej informacji:

* [Usługi w chmurze firmy Microsoft i zabezpieczenia sieci](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Monitorowanie i wykrywania zagrożeń

Platforma Azure udostępnia funkcje ułatwiające w tym obszarze klucza z wczesnego wykrywania, monitorowanie, i zbierania i przeglądania ruch sieciowy.

### <a name="azure-network-watcher"></a>Azure Network Watcher
Azure obserwatora sieciowego może pomóc rozwiązać i udostępnia zupełnie nowy zestaw narzędzi, aby pomóc w identyfikacji problemów z zabezpieczeniami.

[Widok grupy zabezpieczeń ](../network-watcher/network-watcher-security-group-view-overview.md) ułatwia utrzymanie zgodności inspekcji i zabezpieczeń maszyn wirtualnych. Ta funkcja służy do wykonywania inspekcji programowe, porównanie zasad linii bazowej zdefiniowanych przez organizację do wprowadzenia reguł dla poszczególnych maszyn wirtualnych. Może to pomóc w identyfikacji dowolnego odejście konfiguracji.

[Przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-overview.md) służy do przechwytywania ruchu sieciowego do i z maszyny wirtualnej. Można zbierać statystyk sieciowych i rozwiązywanie problemów aplikacji, które mogą być cenne w badaniu wtargnięcia sieci. Umożliwia także tej funkcji, wraz z usługi Azure Functions można uruchomić przechwytywanie sieci w odpowiedzi na konkretnych alertów platformy Azure.

Aby uzyskać więcej informacji o obserwatora sieciowego i rozpocząć testowanie niektóre funkcje w sieci laboratorium, zobacz [obserwatora sieciowego Azure omówienie monitorowania](../network-watcher/network-watcher-monitoring-overview.md).

>[!NOTE]
Najbardziej aktualne powiadomień o dostępności i stan tej usługi, sprawdź [strona aktualizacje Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center
Centrum zabezpieczeń Azure ułatwia zapobieganie, wykrywania i reagowania na zagrożenia i zapewnia zwiększyć widoczność i kontrolę nad, zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z dużym zestawem rozwiązań zabezpieczających.

Centrum zabezpieczeń ułatwia optymalizacji i monitorowania zabezpieczeń sieci przez:

* Udostępnia zalecenia dotyczące zabezpieczeń sieci.
* Monitorowanie stanu konfiguracji zabezpieczeń sieci.
* Wysyłać alerty do sieci na podstawie zagrożenia, zarówno na poziomie punktu końcowego i sieci.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Rejestrowanie
Rejestrowanie na poziomie sieci jest funkcją klucza w żadnym scenariuszu zabezpieczeń sieci. Na platformie Azure można rejestrować informacje uzyskane dla grupy NSG można pobrać sieci poziom rejestrowania informacji. Z rejestrowaniem grupy NSG można uzyskać informacji o:

* [Dzienniki aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md). Użyj te dzienniki, aby wyświetlić wszystkie operacje przesłane do Twojej subskrypcji platformy Azure. Te dzienniki są domyślnie włączone i może być używana w portalu Azure. Zostały one wcześniej znana jako inspekcji lub operacyjne dzienniki.
* Dzienniki zdarzeń. Te dzienniki zawierają informacje dotyczące reguły NSG, jakie zostały zastosowane.
* Dzienniki liczników. Te dzienniki pozwalają wiedzieć, ile razy każdej reguły NSG została zastosowana do odmowy lub zezwolić na ruch.

Można również użyć [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), narzędzie wizualizacji zaawansowanych danych, aby przeglądać i analizować te dzienniki.

Więcej informacji:

* [Analizy dzienników dla grup zabezpieczeń sieci (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)
