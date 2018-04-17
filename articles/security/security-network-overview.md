---
title: Sieci pojęcia dotyczące zabezpieczeń i wymagania dotyczące platformy Azure | Dokumentacja firmy Microsoft
description: " Ten artykuł ułatwia zrozumienie Microsoft Azure ma oferować w zakresie zabezpieczeń sieci. Udostępniamy wyjaśnienia podstawowe pojęcia dotyczące zabezpieczeń sieci podstawowej i wymagania i informacje na platforma Azure ma oferować w każdym z tych obszarów. "
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
ms.openlocfilehash: 0e32cd1f4267b9ae0825fbb34ed00e25dd1b63b5
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="azure-network-security-overview"></a>Przegląd zabezpieczeń sieci platformy Azure
Microsoft Azure obejmuje to niezawodna infrastruktura sieci do obsługi aplikacji i wymaganiami dotyczącymi łączności usługi. Łączność sieciowa będzie możliwe między zasobami znajdującymi się na platformie Azure, między lokalnymi i Azure hostowanych zasobów oraz do i z Internetu i Azure.

Celem tego artykułu jest w celu ułatwienia zrozumienia Microsoft Azure ma oferować w zakresie zabezpieczeń sieci. W tym miejscu udostępniamy wyjaśnienia podstawowe pojęcia dotyczące zabezpieczeń sieci podstawowej i wymagań. Firma Microsoft udostępnia również można uzyskać informacje na platforma Azure ma oferować w każdym z tych obszarów, a także łącza do pomagają lepiej zrozumieć interesujące obszary.

W tym artykule Przegląd zabezpieczeń sieci Azure skupia się wokół następujących obszarów:

* Sieć platformy Azure
* Kontrola dostępu do sieci
* Bezpiecznego połączenia zdalnego dostępu i między lokalizacjami
* Dostępność
* Rozpoznawanie nazw
* Architektura sieci obwodowej
* Monitorowanie i wykrywania zagrożeń


## <a name="azure-networking"></a>Sieci systemu Azure
Maszyny wirtualne muszą łączność sieciową. W celu spełnienia tego wymagania, platforma Azure wymaga maszyn wirtualnych, które będą podłączone do sieci wirtualnej platformy Azure. Sieci wirtualnej platformy Azure jest konstrukcją logiczną, rozszerzający fizycznej Azure sieci szkieletowej. Każdy logicznej sieci wirtualnej platformy Azure jest odizolowana od wszystkich innych sieciach wirtualnych platformy Azure. Pomaga to zapewnić, że ruch sieciowy we wdrożeniach nie jest dostępna dla innych klientów firmy Microsoft Azure.

Więcej informacji:

* [Omówienie usługi Virtual Network](../virtual-network/virtual-networks-overview.md)


## <a name="network-access-control"></a>Kontrola dostępu do sieci
Kontrola dostępu do sieci jest czynnością ograniczenia łączności do i z określonymi urządzeniami lub podsieci w sieci wirtualnej platformy Azure. Celem kontroli dostępu do sieci jest ograniczenie dostępu do maszyn wirtualnych i usług dla zatwierdzonych użytkowników i urządzeń. Kontroli dostępu są oparte na akceptować lub odrzucać decyzje dotyczące połączeń do i z sieci maszyny wirtualnej lub usługi.

Azure obsługuje kilka typów kontroli dostępu do sieci, takich jak:

* Kontrola warstwy sieci
* Trasy kontroli oraz wymuszonego tunelowania
* Urządzenia zabezpieczeń sieci wirtualnej

### <a name="network-layer-control"></a>Kontrola warstwy sieci
Wszystkie wdrożenia bezpiecznego wymaga niektóre miary kontroli dostępu do sieci. Celem kontroli dostępu do sieci jest ograniczanie komunikacji maszyny wirtualnej na potrzeby systemów i że inne próby komunikacji są blokowane.

Jeśli potrzebujesz kontroli dostępu na poziomie sieci podstawowej (na podstawie adresu IP i protokoły TCP lub UDP), można użyć grup zabezpieczeń sieci. Grupy zabezpieczeń sieci (NSG) jest filtrowanie Zapora podstawowa pakietów stanowe i umożliwia kontrolowanie dostępu na podstawie [5-elementowej](https://www.techopedia.com/definition/28190/5-tuple). Grupy NSG nie udostępniają kontroli warstwy aplikacji lub uwierzytelnionego kontroli dostępu.

Więcej informacji:

* [Grupy zabezpieczeń sieci](../virtual-network/virtual-networks-nsg.md)

### <a name="route-control-and-forced-tunneling"></a>Formant trasy i wymuszanie tunelowania
Możliwość kontrolowania zachowania routingu w sieci wirtualnej platformy Azure jest krytyczny sieci możliwości kontroli dostępu i zabezpieczeń. Jeśli routing jest niepoprawnie skonfigurowana, aplikacji i usług hostowanych na maszynie wirtualnej może połączyć się nieautoryzowanego urządzeń, takich jak systemy i jest przez potencjalnymi atakami.

Sieć platformy Azure obsługuje możliwość dostosowania zachowania routingu ruchu w sieci dla sieci wirtualne platformy Azure. Dzięki temu zmieniania domyślnych wpisów tabeli routingu w sieci wirtualnej platformy Azure. Kontroli zachowania routingu pomaga upewnij się, że cały ruch z niektórych urządzeń lub grupy urządzeń wprowadza lub pozostawia sieci wirtualnej do określonej lokalizacji.

Na przykład może być urządzenie zabezpieczeń sieci wirtualnych w sieci wirtualnej platformy Azure. Chcesz upewnij się, że cały ruch do i z sieci wirtualnej Azure przechodzi przez tego urządzenia wirtualnego zabezpieczeń. Można to zrobić przez skonfigurowanie [trasy zdefiniowane przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) mechanizm służy do zapewnienia usług nie są dozwolone do nawiązania połączenia z urządzeniami przez Internet. Należy pamiętać, że różni się to od przyjmowanie połączeń przychodzących, a następnie odpowiada do nich. Serwerów frontonu sieci web muszą odpowiadać na żądania z hostami w Internecie, a więc Internet-powierzając jej ich konserwację ruch jest dozwolony przychodzące do tych serwerów sieci web i serwerów sieci web mogą odpowiadać.

Co nie chcesz umożliwić to serwer frontonu sieci web do zainicjowania żądania wychodzącego. Takich żądań może reprezentować zagrożenie bezpieczeństwa, ponieważ te połączenia może zostać użyty do pobrania złośliwego oprogramowania. Nawet jeśli chcesz, aby te serwery frontonu do inicjowania żądań wychodzące z Internetem, można wymusić przechodzić przez serwer proxy sieci web w sieci lokalnej, dzięki czemu możesz korzystać z adresu URL filtrowanie i rejestrowanie.

Czy chcesz zamiast tego użyj tunelowania wymuszonego, aby zapobiec takiej sytuacji. Włączenie tunelowania wymuszonego, wszystkie połączenia z Internetem będą obowiązkowo przenoszone za pośrednictwem bramy sieci lokalnej. Można skonfigurować wymuszanie tunelowania dzięki wykorzystaniu trasy zdefiniowane przez użytkownika.

Więcej informacji:

* [Co to są trasy zdefiniowane przez użytkownika i przesyłania dalej IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń sieci wirtualnej
Gdy grup zabezpieczeń sieci, trasy zdefiniowane przez użytkownika i wymuszanie tunelowania zapewniają poziom zabezpieczeń na poziomie warstwy sieci i transportu [OSI model](https://en.wikipedia.org/wiki/OSI_model), czasami, jeśli chcesz włączyć zabezpieczeń na poziomie wyższym niż sieć.

Na przykład wymagań dotyczących zabezpieczeń mogą być następujące:

* Uwierzytelnianie i autoryzacja przed zezwoleniem na dostęp do aplikacji
* Wykrywania nieautoryzowanego dostępu i odpowiedzi nieautoryzowanego dostępu
* Kontrolę warstwy aplikacji protokołów wysokiego poziomu
* Filtrowanie adresów URL
* Oprogramowanie antywirusowe poziomu sieci i ochrony przed złośliwym oprogramowaniem
* Ochrona przed bot
* Kontrola dostępu aplikacji
* Dodatkowa ochrona przed atakami DDoS (powyżej DDoS, zapewnić ochronę sieci szkieletowej Azure, sam)

Te funkcje zabezpieczeń rozszerzonych sieci mogą korzystać za pomocą rozwiązania Azure partnera. Można znaleźć najbardziej aktualne sieci Azure partnerów rozwiązań zabezpieczeń poprzez wizytę [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/) i wyszukując "zabezpieczenia" i "zabezpieczenia sieciowe".

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Bezpieczny dostęp zdalny i łączność między różnymi lokalizacjami
Instalacji, konfiguracji i zarządzania potrzeb zasobów platformy Azure, można to robić zdalnie. Ponadto warto wdrożyć [hybrydowego IT](http://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) rozwiązania, które ma składniki lokalnej i w chmurze publicznej Azure. Te scenariusze wymaga bezpiecznego dostępu zdalnego.

Sieć platformy Azure obsługuje następujące scenariusze bezpieczny dostęp zdalny:

* Połącz stacjach roboczych do sieci wirtualnej platformy Azure
* Łączenie sieci lokalnej sieci wirtualnej platformy Azure, korzystając z sieci VPN
* Połączyć sieć lokalną sieć wirtualną platformy Azure dedykowane łącza sieci WAN
* Połączenia sieci wirtualnych platformy Azure

### <a name="connect-individual-workstations-to-an-azure-virtual-network"></a>Połącz stacjach roboczych do sieci wirtualnej platformy Azure
Może to być razy, jeśli chcesz włączyć indywidualnych deweloperów i operacji personelu do zarządzania maszynami wirtualnymi i usługami na platformie Azure. Na przykład potrzebny jest dostęp do maszyny wirtualnej w sieci wirtualnej platformy Azure i zasady zabezpieczeń nie zezwalaj na RDP lub SSH zdalny dostęp do poszczególnych maszyn wirtualnych. W takim przypadku można użyć połączenia sieci VPN punkt lokacja.

Połączenie VPN punkt lokacja używa [sieć VPN SSTP](https://technet.microsoft.com/library/cc731352.aspx) protokołu umożliwiają konfigurowanie prywatne i bezpiecznego połączenia między użytkownikiem i sieci wirtualnej platformy Azure. Po ustanowieniu połączenia sieci VPN, użytkownik będzie nawiązać połączenie RDP lub SSH za pośrednictwem łącza sieci VPN do żadnej maszyny wirtualnej w sieci wirtualnej platformy Azure (przy założeniu, że jest autoryzowany i może uwierzytelnić użytkownika).

Więcej informacji:

* [Skonfiguruj połączenie punkt-lokacja sieci wirtualnej przy użyciu programu PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-vpn"></a>Połączyć sieć lokalną sieć wirtualną platformy Azure, korzystając z sieci VPN
Możesz połączyć całej sieci firmowej, lub w części, sieć wirtualną platformy Azure. To jest typowe w przypadku hybrydowych IT scenariusze gdzie firmy [rozszerzenie ich lokalnych centrów danych na platformie Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). W wielu przypadkach firmy będą hosta usługi w programie Azure i w częściach lokalnymi, np. gdy rozwiązanie zawiera serwerów frontonu sieci web w Azure i bazy danych zaplecza lokalnie. Typy połączeń "między lokalizacjami" Upewnij się również zarządzania Azure znajduje się zasoby bardziej bezpieczny i włączyć scenariuszy, takich jak rozszerzanie kontrolerów domeny usługi Active Directory na platformie Azure.

Aby wykonać to jest użycie [sieci VPN typu lokacja lokacja](https://www.techopedia.com/definition/30747/site-to-site-vpn). Różnica między VPN lokacja lokacja i sieć VPN punkt lokacja jest, że VPN punkt lokacja pojedyncze urządzenie łączy się z sieci wirtualnej platformy Azure, gdy sieć VPN lokacja lokacja łączy całej sieci (na przykład sieci lokalnej) do sieci wirtualnej platformy Azure. Sieci VPN typu lokacja lokacja, do sieci wirtualnej platformy Azure, użyj wysokim poziomie zabezpieczeń trybu tunelowania IPsec protokołu sieci VPN.

Więcej informacji:

* [Tworzenie sieci wirtualnej Resource Manager za pomocą połączenia sieci VPN lokacja lokacja przy użyciu portalu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Planowanie i projektowanie bramy sieci VPN](../vpn-gateway/vpn-gateway-plan-design.md)

### <a name="connect-your-on-premises-network-to-an-azure-virtual-network-with-a-dedicated-wan-link"></a>Połączyć sieć lokalną sieć wirtualną Azure z dedykowanym łącza sieci WAN
Połączenia VPN punkt lokacja i lokacja lokacja obowiązują umożliwiających łączności między lokalizacjami. Jednak niektóre organizacje wziąć pod uwagę ich ma następujące wady:

* Połączenia sieci VPN przenoszenia danych za pośrednictwem Internetu — to udostępnia te połączenia na potencjalne problemy związane z przenoszenia danych w sieci publicznej. Ponadto nie można zagwarantować niezawodność i dostępność dla połączenia z Internetem.
* Połączenia sieci VPN do sieci wirtualnych Azure można uznać się, że przepustowość jest ograniczona do niektóre aplikacje i celów, w miarę ich maksymalny limit na około 200 MB/s.

Organizacji, które zwykle potrzebują najwyższy poziom zabezpieczeń i dostępności do ustanawiania połączeń między różnymi lokalizacjami Użyj dedykowane łącza sieci WAN, aby połączyć się z lokacjami zdalnymi. Platforma Azure udostępnia możliwość używania wydzielonego łącza sieci WAN, można użyć, aby połączyć sieć lokalną sieć wirtualną platformy Azure. Ta opcja jest włączona za pomocą usługi Azure ExpressRoute.

Więcej informacji:

* [Opis techniczny ExpressRoute](../expressroute/expressroute-introduction.md)

### <a name="connect-azure-virtual-networks-to-each-other"></a>Połączenia sieci wirtualnych platformy Azure
Istnieje możliwość użycia wielu sieci wirtualnych Azure wdrożeń. Istnieje wiele przyczyn, dlaczego można to zrobić. Przyczyny mogą być uproszczenie zarządzania; inny może być ze względów bezpieczeństwa. Niezależnie od tego, czy motywacją lub uzasadnienie umieszczanie zasobów w różnych sieciach wirtualnych platformy Azure może być godziny zużycia zasobów w każdej sieci, aby połączyć się ze sobą.

Jedną z opcji byłoby dla usług w jednej sieci wirtualnych Azure nawiązać połączenia z usługami w innej sieci wirtualnej platformy Azure "pętli ponownie" za pośrednictwem Internetu. Połączenie będzie uruchomić na jedną sieć wirtualną platformy Azure, przejdź przez Internet i następnie wróć do miejsca docelowego sieci wirtualnej platformy Azure. Ta opcja udostępnia połączenie do komunikacji między internetowym związane problemy dotyczące zabezpieczeń.

Lepszym rozwiązaniem może być utworzyć Azure wirtualnego VPN lokacja lokacja sieci wirtualnej sieci do platformy Azure. Używa tego Azure wirtualnego VPN lokacja lokacja sieci wirtualnej sieci do platformy Azure, takie same [trybu tunelowania IPsec](https://technet.microsoft.com/library/cc786385.aspx) protokołem wymienione powyżej połączenia sieci VPN między lokalizacjami lokacja lokacja.

Zaletą używania Azure wirtualnego VPN lokacja lokacja sieci wirtualnej sieci do platformy Azure jest ustanowienie połączenia sieci VPN za pośrednictwem sieci szkieletowej sieć platformy Azure, a nie połączenie za pośrednictwem Internetu. Zapewnia dodatkową warstwę zabezpieczeń w porównaniu z sieciami VPN lokacja lokacja, które łączą się przez Internet.

Więcej informacji:

* [Konfigurowanie połączenia do wirtualnymi przy użyciu usługi Azure Resource Manager i programu PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="availability"></a>Dostępność
Dostępność jest kluczowym składnikiem programu zabezpieczeń. Jeśli muszą uzyskiwać dostęp za pośrednictwem sieci nie dostępu użytkowników i systemów, usługa jest uznawana za naruszenia zabezpieczeń. Platforma Azure ma technologii sieciowych, które obsługują następujące mechanizmy wysokiej dostępności:

* Równoważenie obciążenia oparte na protokole HTTP
* Równoważenie obciążenia poziomu sieci
* Globalnego równoważenia obciążenia

Równoważenie obciążenia sieciowego jest mechanizm przeznaczony do jednakowo dystrybucji połączeń między wieloma urządzeniami. Cele równoważenia obciążenia są:

* Zwiększ dostępność — podczas ładowania saldo połączenia między wieloma urządzeniami, co najmniej jedno urządzenie może stać się niedostępne i usługi działające na pozostałych urządzeń online mogą w dalszym ciągu udostępniać zawartość z usługi
* Zwiększyć wydajność — podczas ładowania saldo połączeń na wielu urządzeniach, pojedyncze urządzenie nie trzeba wykonywać trafień procesora. Zamiast tego żądania przetwarzania i pamięci do obsługi zawartości zostanie rozmieszczona na wielu urządzeniach.

### <a name="http-based-load-balancing"></a>Równoważenie obciążenia oparte na protokole HTTP
Organizacje, które działa usług sieci web często chcesz mieć usługi równoważenia obciążenia opartą na protokole HTTP przed tych usług sieci web, aby upewnić się, odpowiednich poziomów wydajności i wysokiej dostępności. W przeciwieństwie do równoważenia obciążenia opartego na sieci tradycyjnych decyzje podjęte przez oparte na protokole HTTP równoważenia obciążenia usługi równoważenia obciążenia są oparte na właściwości protokołu HTTP, a nie na protokołami warstwy transportu i sieci.

Aby przygotować oparty na protokole HTTP równoważenia obciążenia dla usług opartych na sieci web, platforma Azure udostępnia Azure Application Gateway. Brama aplikacji w usłudze Azure obsługuje:

* Oparte na protokole HTTP Równoważenie obciążenia — decyzje równoważenia obciążenia są wykonywane na podstawie cech specjalne protokołu HTTP
* Koligacji na podstawie plików cookie sesji — ta funkcja zapewnia, że połączeń ustanowionych z jednym z serwerów za ten moduł równoważenia obciążenia pozostaje niezmieniona między klientem i serwerem. Dzięki temu stabilności transakcji.
* Odciążanie protokołu SSL — gdy klient nawiązaniu połączenia z modułem równoważenia obciążenia, czy sesji między klientem a usługą równoważenia obciążenia jest szyfrowana przy użyciu protokołu HTTPS (SSL /) protokołu. Jednak aby zwiększyć wydajność, istnieje możliwość połączenia między usługi równoważenia obciążenia i serwer sieci web za użycie protokołu HTTP (bez szyfrowania) usługi równoważenia obciążenia. To jest określane jako "Odciążanie protokołu SSL", ponieważ serwery sieci web związanej z modułem równoważenia obciążenia nie występuje obciążenie procesora związane z szyfrowaniem i w związku z tym powinno być możliwe do obsługi żądań szybciej.
* Adres URL routingu opartego na protokole zawartości — ta funkcja umożliwia równoważenia obciążenia do podejmowaniu decyzji, dokąd należy przekazywać połączeń, w oparciu o docelowy adres URL. Zapewnia to znacznie większą elastyczność niż rozwiązania, które należy załadować równoważenia decyzje na podstawie adresów IP.

Więcej informacji:

* [Omówienie bramy aplikacji](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Równoważenie obciążenia poziomu sieci
W przeciwieństwie do równoważenia obciążenia opartą na protokole HTTP, równoważenie obciążenia poziomu sprawia, że decyzje równoważenia obciążenia oparte na adres i port (TCP lub UDP) numery IP.
Aby uzyskać korzyści wynikające z poziomu równoważenia obciążenia sieciowego w Azure przy użyciu usługi równoważenia obciążenia Azure. Niektóre właściwości klucza usługi równoważenia obciążenia Azure obejmują:

* Równoważenie obciążenia poziomu oparte na numery adres i port IP
* Obsługa protokołu warstwy żadnych aplikacji
* Równoważy obciążenia na maszynach wirtualnych platformy Azure i wystąpień roli usług w chmurze
* Można użyć zarówno internetowy (zewnętrzne Równoważenie obciążenia sieciowego), jak i z systemem innym niż Internet skierowane w aplikacji (równoważenia obciążenia wewnętrznego) i maszyny wirtualne
* Punkt końcowy monitorowania, który służy do określania, jeśli którakolwiek z nich za usługą równoważenia obciążenia stały się niedostępne

Więcej informacji:

* [Moduł równoważenia obciążenia nakierowany na Internet między maszynami wirtualnymi lub usługami](../load-balancer/load-balancer-internet-overview.md)
* [Omówienie usługi równoważenia obciążenia wewnętrznego](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globalnego równoważenia obciążenia
Niektóre organizacje będzie możliwe najwyższy poziom dostępności. Jednym ze sposobów osiągnięcia tego celu jest umożliwia obsługę aplikacji w rozproszonych globalnie centrach danych. Gdy aplikacja jest obsługiwana w centrach danych znajdujących się na całym świecie, jest możliwe całego regionu geograficznymi może stać się niedostępne, a nadal aplikacji uruchomionych.

Oprócz korzyści dostępności Ci się obsługi aplikacji w rozproszonych globalnie centrach danych możesz również uzyskać zwiększenia wydajności. Te korzyści wydajności można uzyskać za pomocą mechanizmu, który kieruje żądania usługi do centrum danych, który znajduje się najbliżej urządzenia, do której wysłano żądanie.

Równoważenie obciążenia globalny może umożliwić obu tych korzyści. Na platformie Azure Aby uzyskać korzyści wynikające z globalnego równoważenia obciążenia za pomocą usługi Azure Traffic Manager.

Więcej informacji:

* [Co to jest usługa Traffic Manager?](../traffic-manager/traffic-manager-overview.md)


## <a name="name-resolution"></a>Rozpoznawanie nazw
Rozpoznawanie nazw jest funkcją krytyczne dla wszystkich usług, które są hostowane na platformie Azure. Z punktu widzenia zabezpieczeń naruszenia funkcja rozpoznawania nazw może prowadzić do przekierowywania żądań z lokacji do lokacji osoba atakująca osoba atakująca. Rozpoznawanie nazw bezpiecznego jest wymagane dla wszystkich usług w chmurze hostowanej.

Istnieją dwa typy rozpoznawania nazw, które należy rozwiązać:

* Rozpoznawania nazw wewnętrznych — rozpoznawania nazw wewnętrznych jest używany przez usługi w sieci wirtualne platformy Azure i/lub sieci lokalnej. Nazwy używane do rozpoznawania nazw wewnętrznych nie są dostępne za pośrednictwem Internetu. Optymalne zabezpieczeń ważne jest schematem rozpoznawania nazw wewnętrznych nie jest dostępna dla użytkowników zewnętrznych.
* Rozpoznawanie nazw zewnętrznych — rozpoznawanie nazw zewnętrznych jest używany przez osoby i urządzenia poza sieci lokalnej i sieci wirtualnych Azure. Są to nazwy, które są widoczne z Internetem i umożliwia bezpośrednie połączenie z usługami w chmurze.

Do rozpoznawania nazw wewnętrznych dostępne są dwie opcje:

* Serwer DNS sieci wirtualnej platformy Azure — podczas tworzenia nowej sieci wirtualnej Azure, serwer DNS jest tworzony automatycznie. Ten serwer DNS może rozpoznawania nazw komputerów znajdujących się w tej sieci wirtualnej platformy Azure. Ten serwer DNS nie jest konfigurowalne i jest zarządzany przez Menedżera sieci szkieletowej Azure, co czyni go rozwiązania rozpoznawania nazwy bezpieczne.
* Przełącz serwer DNS — istnieje możliwość wprowadzenia serwer DNS wybranej przez użytkownika w sieci wirtualnej platformy Azure. Ten serwer DNS może być zintegrowane usługi Active Directory, serwer DNS lub dedykowane rozwiązania serwera DNS podany przez partnera Azure, który można uzyskać w witrynie Azure Marketplace.

Więcej informacji:

* [Omówienie usługi Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Zarządzanie serwerami DNS używanymi przez sieć wirtualną (VNet)](../virtual-network/manage-virtual-network.md#change-dns-servers)

Dla zewnętrznych rozpoznawanie nazw DNS dostępne są dwie opcje:

* Host własne zewnętrznych DNS serwera lokalnego
* Host własne zewnętrznego serwera DNS z dostawcą usług

W wielu organizacjach dużych będzie obsługiwać własne DNS serwerów lokalnych. Mogą one to robić ponieważ mają one doświadczenia z sieci i globalnych obecności w tym celu.

W większości przypadków najlepiej hosta z usługi rozpoznawania nazw DNS z dostawcą usług. Ci dostawcy usług mają doświadczenia z sieci i globalne obecności w celu zapewnienia bardzo wysoka dostępność dla Twojej usługi rozpoznawania nazw. Dostępność jest istotne dla usługi DNS, ponieważ w przypadku awarii z usługi rozpoznawania nazw, nie będzie mogła nawiązać połączenia z internetowy usług.

Azure zapewnia wysoką dostępność i wydajność zewnętrznych DNS rozwiązanie w formie usługi Azure DNS. To rozwiązanie rozpoznawania nazw zewnętrznych korzysta z infrastruktury usługi Azure DNS na całym świecie. Umożliwia hostowanie domeny platformy Azure przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń jak innymi usługami Azure. W ramach platformy Azure również dziedziczy formanty silne zabezpieczenie wbudowanych w platformy.

Więcej informacji:

* [Omówienie usługi Azure DNS](../dns/dns-overview.md)

## <a name="dmz-architecture"></a>Architektura sieci obwodowej
W wielu organizacjach enterprise umożliwia segment ich sieci, aby utworzyć strefę buforu od Internetu, a ich usług sieci obwodowej. Sieć obwodowa część sieci jest uznawany za strefy niskim poziomie zabezpieczeń i nie zasoby wysokiej wartości są umieszczane w tym segmencie sieci. Zwykle zobaczysz urządzeń zabezpieczeń sieciowych, karty sieciowej w segmencie DMZ i inny interfejs sieciowy podłączony do sieci z maszyn wirtualnych i usług, które akceptują połączenia przychodzące z Internetu.

Istnieje wiele zmian projektu sieci obwodowej i decyzja o wdrożeniu sieci obwodowej i następnie jakiego rodzaju sieć obwodowa do użycia, jeśli zdecydujesz się używać jednego, opiera się na określonych wymagań dotyczących zabezpieczeń sieci.

Więcej informacji:

* [Usługi w chmurze firmy Microsoft i zabezpieczenia sieci](../best-practices-network-security.md)


## <a name="monitoring-and-threat-detection"></a>Monitorowanie i wykrywania zagrożeń

Platforma Azure udostępnia funkcje ułatwiające w tym obszarze klucza z wczesnego wykrywania, monitorowania i możliwość zbierania i przejrzyj ruch sieciowy.

### <a name="azure-network-watcher"></a>Azure Network Watcher
Azure obserwatora sieciowego zawiera wiele funkcji, które pomóc w rozwiązywaniu problemów, a także zapewnić zupełnie nowy zestaw narzędzi ułatwiających identyfikację problemów z zabezpieczeniami.

[Widok grupy zabezpieczeń ](../network-watcher/network-watcher-security-group-view-overview.md) ułatwia utrzymanie zgodności inspekcji i zabezpieczeń maszyn wirtualnych i może służyć do wykonywania inspekcji programowe porównanie zasad linii bazowych zdefiniowanych przez organizację do wprowadzenia reguł dla poszczególnych maszyn wirtualnych. Może to pomóc w identyfikacji dowolnego odejście konfiguracji.

[Przechwytywania pakietów](../network-watcher/network-watcher-packet-capture-overview.md) służy do przechwytywania ruchu sieciowego do i z maszyny wirtualnej. Oprócz myśl, umożliwiając zbieranie statystyk sieci oraz rozwiązywanie problemów aplikacji przechwytywania pakietów mogą być cenne w badaniu wtargnięcia sieci. Umożliwia także tę funkcję, wraz z usługi Azure Functions można uruchomić przechwytywanie sieci w odpowiedzi na konkretnych alertów platformy Azure.

Aby uzyskać więcej informacji na obserwatora sieciowego Azure i uruchamiania testów niektórych funkcji w Twojej labs Spójrz na [obserwatora sieciowego Azure monitorowania — omówienie](../network-watcher/network-watcher-monitoring-overview.md)

>[!NOTE]
Azure obserwatora sieciowego jest nadal w publicznej wersji zapoznawczej, więc może nie dysponować taki sam poziom dostępności i niezawodności jako wersji usług, które są zwykle dostępności. Niektóre funkcje mogą nie być obsługiwane, mogą mieć ograniczone możliwości i mogą nie być dostępne we wszystkich lokalizacjach Azure. Najbardziej aktualne powiadomień o dostępności i stan tej usługi, sprawdź [strony aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=network-watcher)

### <a name="azure-security-center"></a>Azure Security Center
Centrum zabezpieczeń ułatwia zapobieganie, wykrywania i reagowania na zagrożenia i zapewnia zwiększyć widoczność i kontrolę nad, zabezpieczeń zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z dużym zestawem rozwiązań zabezpieczających.

Centrum zabezpieczeń Azure ułatwia optymalizacji i monitorować przez zabezpieczenia sieci:

* Udostępnia zalecenia dotyczące zabezpieczeń sieci
* Monitorowanie stanu konfiguracji zabezpieczeń sieci
* Wysyłać alerty o podstawie zagrożenia sieciowe zarówno na poziomie punktu końcowego i sieci

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)


### <a name="logging"></a>Rejestrowanie
Rejestrowanie na poziomie sieci jest funkcją klucza w żadnym scenariuszu zabezpieczeń sieci. Na platformie Azure można rejestrować informacje uzyskane dla grup zabezpieczeń sieci można pobrać sieci poziom rejestrowania informacji. Z rejestrowaniem grupy NSG można uzyskać informacji o:

* [Dzienniki aktywności](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) — te dzienniki służą do wyświetlania wszystkich operacji przesłane do Twojej subskrypcji platformy Azure. Te dzienniki są domyślnie włączone i może być używana w portalu Azure. Zostały one wcześniej znana jako "Dzienników inspekcji" lub "Operacyjne dzienniki".
* Dzienniki zdarzeń — te dzienniki zawierają informacje dotyczące reguły NSG, jakie zostały zastosowane.
* Licznik dzienników — te dzienniki pozwalają wiedzieć, ile razy każdej reguły NSG została zastosowana do odmowy lub zezwolić na ruch.

Można również użyć [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), narzędzie wizualizacji zaawansowanych danych, aby przeglądać i analizować te dzienniki.

Więcej informacji:

* [Analizy dzienników dla grup zabezpieczeń sieci (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)
