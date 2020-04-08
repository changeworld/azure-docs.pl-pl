---
title: Pojęcia i wymagania dotyczące zabezpieczeń sieci na platformie Azure | Dokumenty firmy Microsoft
description: Ten artykuł zawiera podstawowe wyjaśnienia dotyczące pojęć i wymagań dotyczących zabezpieczeń sieci podstawowej oraz informacje o tym, co platforma Azure oferuje w każdym z tych obszarów.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 496ee1bc97f6b72e09a62ae3491af7ccc7328583
ms.sourcegitcommit: 98e79b359c4c6df2d8f9a47e0dbe93f3158be629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80811080"
---
# <a name="azure-network-security-overview"></a>Omówienie zabezpieczeń sieci platformy Azure

Zabezpieczenia sieci można zdefiniować jako proces ochrony zasobów przed nieautoryzowanym dostępem lub atakiem przez zastosowanie formantów do ruchu sieciowego. Celem jest zapewnienie, że dozwolony jest tylko legalny ruch. Platforma Azure zawiera niezawodną infrastrukturę sieciową do obsługi wymagań dotyczących łączności aplikacji i usług. Łączność sieciowa jest możliwa między zasobami znajdującymi się na platformie Azure, między zasobami hostowanymi w środowisku lokalnym i platformy Azure oraz z Internetem i platformą Azure.

W tym artykule opisano niektóre opcje, które oferuje platforma Azure w obszarze zabezpieczeń sieci. Możesz dowiedzieć się więcej o:

* Sieć platformy Azure
* Kontrola dostępu do sieci
* Azure Firewall
* Bezpieczny dostęp zdalny i łączność między lokalizacjami
* Dostępność
* Rozpoznawanie nazw
* Architektura sieci obwodowej (DMZ)
* Azure DDoS Protection
* Azure Front Door
* Usługa Traffic Manager
* Monitorowanie i wykrywanie zagrożeń

## <a name="azure-networking"></a>Sieć platformy Azure

Platforma Azure wymaga, aby maszyny wirtualne były połączone z siecią wirtualną platformy Azure. Sieć wirtualna jest logiczną konstrukcją opartą na fizycznej sieci szkieletowej platformy Azure. Każda sieć wirtualna jest odizolowana od wszystkich innych sieci wirtualnych. Pomaga to zapewnić, że ruch sieciowy w wdrożeniach nie jest dostępny dla innych klientów platformy Azure.

Więcej informacji:

* [Omówienie sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Kontrola dostępu do sieci

Kontrola dostępu do sieci jest działaniem ograniczającym łączność do i z określonych urządzeń lub podsieci w sieci wirtualnej. Celem kontroli dostępu do sieci jest ograniczenie dostępu do maszyn wirtualnych i usług do zatwierdzonych użytkowników i urządzeń. Formanty dostępu są oparte na decyzjach zezwalania lub odmówieniu połączeń do i z maszyny wirtualnej lub usługi.

Platforma Azure obsługuje kilka typów kontroli dostępu do sieci, takich jak:

* Kontrola warstwy sieciowej
* Sterowanie trasą i wymuszone tunelowanie
* Urządzenia zabezpieczające sieć wirtualną

### <a name="network-layer-control"></a>Kontrola warstwy sieciowej

Każde bezpieczne wdrożenie wymaga pewnej miary kontroli dostępu do sieci. Celem kontroli dostępu do sieci jest ograniczenie komunikacji maszyny wirtualnej do niezbędnych systemów. Inne próby komunikacji są blokowane.

> [!NOTE]
> Zapory magazynu są omówione w [artykule omówienie zabezpieczeń magazynu platformy Azure](storage-overview.md)

#### <a name="network-security-rules-nsgs"></a>Reguły bezpieczeństwa sieci (NSG)

Jeśli potrzebna jest podstawowa kontrola dostępu na poziomie sieci (oparta na adresie IP i protokołach TCP lub UDP), możesz użyć sieciowych grup zabezpieczeń (NSG). Sieciowej sieciowej sieciowej jest podstawowa, stanowa zapora filtrująca pakiety i umożliwia kontrolowanie dostępu na podstawie [5-krotki.](https://www.techopedia.com/definition/28190/5-tuple) Sieciowe sieciowe sieciowe obejmują funkcje upraszczające zarządzanie i zmniejszające ryzyko błędów w konfiguracji:

* **Rozszerzone reguły zabezpieczeń** upraszczają definicję reguł sieciowej grupy zabezpieczeń i umożliwiają tworzenie złożonych reguł, a nie tworzenie wielu prostych reguł w celu osiągnięcia tego samego rezultatu.
* **Tagi usługi** to etykiety utworzone przez firmę Microsoft, które reprezentują grupę adresów IP. Aktualizują się dynamicznie, aby uwzględnić zakresy adresów IP, które spełniają warunki definiujące włączenie do etykiety. Na przykład, jeśli chcesz utworzyć regułę, która ma zastosowanie do całego magazynu platformy Azure w regionie wschodnim, możesz użyć storage.EastUS
* **Grupy zabezpieczeń aplikacji** umożliwiają wdrażanie zasobów w grupach aplikacji i kontrolowanie dostępu do tych zasobów przez tworzenie reguł korzystających z tych grup aplikacji. Na przykład jeśli masz serwery www wdrożone w grupie aplikacji "Webservers", możesz utworzyć regułę, która stosuje grupę sieciowej sieciowej zezwalającą na ruch 443 z Internetu do wszystkich systemów w grupie aplikacji "Serwery sieci Web".

Sieciowe sieci zabezpieczeń nie zapewniają inspekcji warstwy aplikacji ani uwierzytelniania kontroli dostępu.

Więcej informacji:

* [Sieciowe grupy zabezpieczeń](../../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC just in time VM access ASC just in time VM access ASC just in time VM access ASC

[Centrum zabezpieczeń platformy Azure](../../security-center/security-center-intro.md) może zarządzać sieciami roboczymi na maszynach wirtualnych i zablokować dostęp do maszyny Wirtualnej, dopóki użytkownik z odpowiednimi uprawnieniami kontroli dostępu oparty na rolach [RBAC](/azure/role-based-access-control/overview) nie zażąda dostępu. Gdy użytkownik zostanie pomyślnie autoryzowany ASC wprowadza modyfikacje do grup NSG, aby umożliwić dostęp do wybranych portów w określonym czasie. Po upływie czasu nsgs są przywracane do poprzedniego stanu zabezpieczonego.

Więcej informacji:

* [Azure Security Center Just in Time Access](../../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi są inny sposób, aby zastosować kontrolę nad ruchem. Można ograniczyć komunikację z obsługiwanych usług tylko do sieci wirtualnych za pomocą bezpośredniego połączenia. Ruch z sieci wirtualnej do określonej usługi platformy Azure pozostaje w sieci szkieletowej platformy Microsoft Azure.  

Więcej informacji:

* [Punkty końcowe usługi](../../virtual-network/virtual-network-service-endpoints-overview.md#secure-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Sterowanie trasą i wymuszone tunelowanie

Możliwość kontrolowania zachowania routingu w sieciach wirtualnych ma kluczowe znaczenie. Jeśli routing jest skonfigurowany niepoprawnie, aplikacje i usługi hostowane na maszynie wirtualnej mogą łączyć się z nieautoryzowanymi urządzeniami, w tym z systemami należącymi do potencjalnych atakujących i obsługiwanymi przez nie.

Sieć platformy Azure obsługuje możliwość dostosowywania zachowania routingu dla ruchu sieciowego w sieciach wirtualnych. Dzięki temu można zmienić domyślne wpisy tabeli routingu w sieci wirtualnej. Kontrola zachowania routingu pomaga upewnić się, że cały ruch z określonego urządzenia lub grupy urządzeń wchodzi lub opuszcza sieć wirtualną za pośrednictwem określonej lokalizacji.

Na przykład w sieci wirtualnej może znajdować się urządzenie zabezpieczające sieci wirtualnej. Chcesz upewnić się, że cały ruch do i z sieci wirtualnej przechodzi przez to wirtualne urządzenie zabezpieczeń. Można to zrobić, konfigurując [trasy zdefiniowane przez użytkownika](../../virtual-network/virtual-networks-udr-overview.md) (UDR) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) to mechanizm, którego można użyć, aby upewnić się, że usługi nie mogą inicjować połączenia z urządzeniami w Internecie. Należy zauważyć, że różni się to od akceptowania połączeń przychodzących, a następnie odpowiadania na nie. Front-endowe serwery sieci web muszą odpowiadać na żądania od hostów internetowych, a więc ruch internetowy jest dozwolony przychodzący do tych serwerów sieci web, a serwery sieci web mogą odpowiadać.

Nie chcesz zezwalać na to serwer sieci web front-end do inicjowania żądania wychodzącego. Takie żądania mogą stanowić zagrożenie bezpieczeństwa, ponieważ te połączenia mogą być używane do pobierania złośliwego oprogramowania. Nawet jeśli chcesz, aby te serwery front-end inicjowały wychodzące żądania do Internetu, możesz wymusić na nich przechodzenie przez lokalne serwery proxy sieci Web. Dzięki temu można korzystać z filtrowania adresów URL i rejestrowania.

Zamiast tego należy użyć wymuszonego tunelowania, aby temu zapobiec. Po włączeniu wymuszonego tunelowania wszystkie połączenia z Internetem są wymuszane przez bramę lokalną. Można skonfigurować wymuszone tunelowanie, korzystając z UDR.

Więcej informacji:

* [Co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczające sieć wirtualną

Podczas gdy sieciowe sieciowe, UDR i wymuszone tunelowanie zapewniają poziom bezpieczeństwa w warstwach sieci i transportu [modelu OSI,](https://en.wikipedia.org/wiki/OSI_model)można również włączyć zabezpieczenia na poziomie wyższym niż sieć.

Na przykład wymagania dotyczące zabezpieczeń mogą obejmować:

* Uwierzytelnianie i autoryzacja przed zezwoleniem na dostęp do aplikacji
* Wykrywanie włamań i reagowanie na włamania
* Kontrola warstw aplikacji dla protokołów wysokiego poziomu
* Filtrowanie adresów URL
* Oprogramowanie antywirusowe i chroniące przed złośliwym oprogramowaniem na poziomie sieci
* Ochrona przed botami
* Kontrola dostępu do aplikacji
* Dodatkowa ochrona przed atakami DDoS (powyżej ochrony przed atakami DDoS zapewniana przez samą strukturę szkieletową platformy Azure)

Dostęp do tych ulepszonych funkcji zabezpieczeń sieci można uzyskać przy użyciu rozwiązania partnera platformy Azure. Najnowsze rozwiązania zabezpieczeń sieci partnerów platformy Azure można znaleźć, odwiedzając witrynę [Azure Marketplace](https://azure.microsoft.com/marketplace/)i wyszukując "zabezpieczenia" i "zabezpieczenia sieci".

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora oferowana jako usługa, z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. Niektóre funkcje obejmują:

* Wysoka dostępność
* Skalowalność chmury
* Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji
* Reguły filtrowania ruchu sieciowego

Więcej informacji:

* [Omówienie zapory platformy Azure](/azure/firewall/overview)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Bezpieczny dostęp zdalny i łączność między lokalizacjami

Konfiguracja, konfiguracja i zarządzanie zasobami platformy Azure muszą być wykonywane zdalnie. Ponadto można wdrożyć hybrydowe rozwiązania [IT,](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) które mają składniki lokalnie i w chmurze publicznej platformy Azure. Te scenariusze wymagają bezpiecznego dostępu zdalnego.

Sieć platformy Azure obsługuje następujące scenariusze bezpiecznego dostępu zdalnego:

* Łączenie poszczególnych stacji roboczych z siecią wirtualną
* Łączenie sieci lokalnej z siecią wirtualną za pomocą sieci VPN
* Podłączanie sieci lokalnej do sieci wirtualnej za pomocą dedykowanego łącza WAN
* Łączenie sieci wirtualnych ze sobą

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Łączenie poszczególnych stacji roboczych z siecią wirtualną

Można włączyć poszczególnych deweloperów lub pracowników operacyjnych do zarządzania maszynami wirtualnymi i usługami na platformie Azure. Załóżmy na przykład, że potrzebujesz dostępu do maszyny wirtualnej w sieci wirtualnej. Jednak zasady zabezpieczeń nie zezwalają na zdalny dostęp protokołu RDP lub SSH do poszczególnych maszyn wirtualnych. W takim przypadku można użyć połączenia [sieci VPN typu punkt-lokacja.](../../vpn-gateway/point-to-site-about.md)

Połączenie sieci VPN typu punkt-lokacja umożliwia skonfigurowanie prywatnego i bezpiecznego połączenia między użytkownikiem a siecią wirtualną. Po nawiązaniu połączenia sieci VPN użytkownik może łącze RDP lub SSH za pośrednictwem łącza VPN do dowolnej maszyny wirtualnej w sieci wirtualnej. (Zakłada się, że użytkownik może uwierzytelnić się i jest autoryzowany).) Sieć VPN typu punkt-lokacja obsługuje:

* Secure Socket Tunneling Protocol (SSTP), zastrzeżony protokół VPN oparty na SSL. Rozwiązanie SSL VPN może przenikać przez zapory, ponieważ większość zapór otwiera port TCP 443, którego używa TLS/SSL. Protokół SSTP jest obsługiwany tylko na urządzeniach z systemem Windows. Platforma Azure obsługuje wszystkie wersje systemu Windows z protokółem SSTP (Windows 7 lub nowszym).

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Więcej informacji:

* [Konfigurowanie połączenia typu "punkt-lokacja" z siecią wirtualną przy użyciu programu PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Łączenie sieci lokalnej z siecią wirtualną za pomocą sieci VPN

Można połączyć całą sieć firmową lub jej części z siecią wirtualną. Jest to powszechne w hybrydowych scenariuszach IT, w których organizacje [rozszerzają swoje lokalne centrum danych na platformę Azure.](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84) W wielu przypadkach organizacje hostują części usługi na platformie Azure i części lokalne. Na przykład mogą to zrobić, gdy rozwiązanie zawiera serwery sieci web front-end na platformie Azure i zaplecza bazy danych w środowisku lokalnym. Te typy połączeń "międzylokacowych" również uczynić zarządzanie zasobami zlokalizowanych platformy Azure bardziej bezpieczne i włączyć scenariusze, takie jak rozszerzenie kontrolerów domeny usługi Active Directory na platformie Azure.

Jednym ze sposobów osiągnięcia tego celu jest użycie [sieci VPN typu lokacja lokacja.](https://www.techopedia.com/definition/30747/site-to-site-vpn) Różnica między siecią VPN typu lokacja-lokacja a siecią VPN typu punkt-lokacja polega na tym, że łączy on jedno urządzenie z siecią wirtualną. Sieć VPN typu lokacja-lokacja łączy całą sieć (na przykład sieć lokalną) z siecią wirtualną. Sieci VPN typu lokacja-lokacja do sieci wirtualnej korzystają z wysoce bezpiecznego protokołu sieci VPN w trybie tunelowym IPsec.

Więcej informacji:

* [Tworzenie sieci wirtualnej Menedżera zasobów za pomocą połączenia sieci VPN między lokacjami za pomocą portalu Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN Gateway — informacje](../../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Podłączanie sieci lokalnej do sieci wirtualnej za pomocą dedykowanego łącza WAN

Połączenia sieci VPN typu "punkt-lokacja" i lokacja są skuteczne w umożliwianiu łączności międzylokacjowej. Jednak niektóre organizacje uważają je za następujące wady:

* Połączenia VPN przenoszą dane przez Internet. Uwidacznia to te połączenia do potencjalnych problemów z zabezpieczeniami związanych z przenoszeniem danych za pośrednictwem sieci publicznej. Ponadto nie można zagwarantować niezawodności i dostępności połączeń internetowych.
* Połączenia sieci VPN z sieciami wirtualnymi mogą nie mieć przepustowości dla niektórych aplikacji i celów, ponieważ mają maksymalną prędkość około 200 Mb/s.

Organizacje, które potrzebują najwyższego poziomu zabezpieczeń i dostępności połączeń międzylokacyjnych, zazwyczaj używają dedykowanych łączy WAN do łączenia się z lokacjami zdalnymi. Platforma Azure umożliwia korzystanie z dedykowanego łącza sieci WAN, którego można użyć do połączenia sieci lokalnej z siecią wirtualną. Włączają to globalny zasięg usługi Azure ExpressRoute, Express Route direct i Express Route.

Więcej informacji:

* [ExpressRoute — opis techniczny](../../expressroute/expressroute-introduction.md)
* [Bezpośrednio w ujrzej y wtrysku ekspresow](../../expressroute/expressroute-erdirect-about.md)
* [Trasa ekspresowa zasięg globalny](../../expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Łączenie sieci wirtualnych ze sobą

Istnieje możliwość korzystania z wielu sieci wirtualnych dla wdrożeń. Istnieje wiele powodów, dla których możesz to zrobić. Można uprościć zarządzanie lub zwiększyć bezpieczeństwo. Niezależnie od motywacji do umieszczania zasobów w różnych sieciach wirtualnych, może się okazać, że zasoby w każdej z sieci mają się ze sobą łączyć.

Jedną z opcji jest dla usług w jednej sieci wirtualnej, aby połączyć się z usługami w innej sieci wirtualnej, przez "zapętlanie z powrotem" przez Internet. Połączenie rozpoczyna się w jednej sieci wirtualnej, przechodzi przez Internet, a następnie wraca do docelowej sieci wirtualnej. Ta opcja udostępnia połączenie z problemami z zabezpieczeniami nieodłącznie związanymi z komunikacją internetową.

Lepszym rozwiązaniem może być utworzenie sieci VPN typu lokacja lokacja, która łączy się między dwiema sieciami wirtualnymi. Ta metoda używa tego samego protokołu [trybu tunelu IPSec,](https://technet.microsoft.com/library/cc786385.aspx) co wymienione powyżej połączenie sieci VPN między lokacjami.

Zaletą tego podejścia jest to, że połączenie sieci VPN jest ustanawiane za pośrednictwem sieci szkieletowej platformy Azure, zamiast łączenia się przez Internet. Zapewnia to dodatkową warstwę zabezpieczeń w porównaniu z sieciami VPN typu lokacja lokacja, które łączą się przez Internet.

Więcej informacji:

* [Konfigurowanie połączenia sieci wirtualnej z siecią wirtualną przy użyciu usługi Azure Resource Manager i programu PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Innym sposobem łączenia sieci wirtualnych jest [komunikacja równorzędna sieci wirtualnej](../../virtual-network/virtual-network-peering-overview.md). Ta funkcja umożliwia połączenie dwóch sieci platformy Azure, dzięki czemu komunikacja między nimi odbywa się za pośrednictwem infrastruktury szkieletowej firmy Microsoft bez konieczności przechodzenia przez Internet. Komunikacja równorzędna sieci wirtualnej może połączyć dwie sieci wirtualne w tym samym regionie lub dwie sieci wirtualne w regionach platformy Azure. Sieciowe sieciowe mogą być używane do ograniczania łączności między różnymi podsieciami lub systemami.

## <a name="availability"></a>Dostępność

Dostępność jest kluczowym składnikiem każdego programu zabezpieczeń. Jeśli użytkownicy i systemy nie mogą uzyskać dostępu do tego, czego potrzebują, aby uzyskać dostęp za pośrednictwem sieci, usługę można uznać za zagrożoną. Platforma Azure ma technologie sieciowe, które obsługują następujące mechanizmy wysokiej dostępności:

* Równoważenie obciążenia oparte na protoke http
* Równoważenie obciążenia na poziomie sieci
* Globalne równoważenie obciążenia

Równoważenie obciążenia to mechanizm przeznaczony do równomiernego rozdzielania połączeń między wiele urządzeń. Cele równoważenia obciążenia to:

* Aby zwiększyć dostępność. Podczas równoważenia obciążenia połączeń na wielu urządzeniach, co najmniej jedno z urządzeń może stać się niedostępne bez narażania na szwank usługi. Usługi uruchomione na pozostałych urządzeniach online mogą nadal obsługiwać zawartość z usługi.
* Aby zwiększyć wydajność. Podczas równoważenia obciążenia połączeń na wielu urządzeniach, jedno urządzenie nie musi obsługiwać wszystkich przetwarzania. Zamiast tego wymagania dotyczące przetwarzania i pamięci do obsługi zawartości są rozłożone na wiele urządzeń.

### <a name="http-based-load-balancing"></a>Równoważenie obciążenia oparte na protoke http

Organizacje, które uruchamiają usługi internetowe, często pragną mieć moduł równoważenia obciążenia oparte na protokoiści HTTP przed tymi usługami sieci web. Pomaga to zapewnić odpowiedni poziom wydajności i wysoką dostępność. Tradycyjne, sieciowe moduły równoważenia obciążenia opierają się na protokołach warstw sieciowych i transportowych. Z drugiej strony moduły równoważenia obciążenia oparte na protokole HTTP podejmują decyzje na podstawie cech protokołu HTTP.

Usługa Azure Application Gateway zapewnia równoważenie obciążenia oparte na protoke dla usług internetowych oparte na protokocz. Brama aplikacji obsługuje:

* Koligacja sesji oparta na plikach cookie. Ta funkcja zapewnia, że połączenia nawiązane do jednego z serwerów odpowiedzialnych za ten moduł równoważenia obciążenia pozostają nienaruszone między klientem a serwerem. Zapewnia to stabilność transakcji.
* Odciążanie TLS. Gdy klient łączy się z modułem równoważenia obciążenia, sesja ta jest szyfrowana przy użyciu protokołu HTTPS (TLS). Aby jednak zwiększyć wydajność, można użyć protokołu HTTP (niezaszyfrowanego) do łączenia się między modułem równoważenia obciążenia a serwerem sieci web za modułem równoważenia obciążenia. Jest to określane jako "odciążanie TLS", ponieważ serwery sieci web za moduł równoważenia obciążenia nie występują obciążenie procesora związane z szyfrowaniem. Serwery sieci web mogą zatem szybciej wysyłać żądania usługi.
* Routing zawartości oparty na adresie URL. Ta funkcja umożliwia modułowi równoważenia obciążenia podejmowanie decyzji o tym, gdzie można przesyłać dalej połączenia na podstawie docelowego adresu URL. Zapewnia to o wiele większą elastyczność niż rozwiązania, które podejmują decyzje dotyczące równoważenia obciążenia na podstawie adresów IP.

Więcej informacji:

* [Application Gateway — omówienie](/azure/application-gateway/application-gateway-introduction)

### <a name="network-level-load-balancing"></a>Równoważenie obciążenia na poziomie sieci

W przeciwieństwie do równoważenia obciążenia opartego na protoko> opartym na protoko> http, równoważenie obciążenia na poziomie sieci podejmuje decyzje na podstawie numerów adresu IP i portu (TCP lub UDP).
Możesz uzyskać korzyści z równoważenia obciążenia na poziomie sieci na platformie Azure przy użyciu modułu równoważenia obciążenia platformy Azure. Niektóre kluczowe cechy load balancer obejmują:

* Równoważenie obciążenia na poziomie sieci na podstawie adresu IP i numerów portów.
* Obsługa dowolnego protokołu warstwy aplikacji.
* Równoważenia obciążenia do maszyn wirtualnych platformy Azure i wystąpień roli usług w chmurze.
* Może być używany zarówno do aplikacji internetowych (zewnętrzne równoważenie obciążenia), jak i do komputerów wirtualnych nieprzejednanych z Internetu (równoważenie obciążenia wewnętrznego) i maszyn wirtualnych.
* Monitorowanie punktu końcowego, który jest używany do określenia, czy którakolwiek z usług za moduł równoważenia obciążenia stały się niedostępne.

Więcej informacji:

* [Internetowy moduł równoważenia obciążenia między wieloma maszynami wirtualnymi lub usługami](/azure/load-balancer/load-balancer-internet-overview)
* [Omówienie wewnętrznego modułu równoważenia obciążenia](/azure/load-balancer/load-balancer-internal-overview)

### <a name="global-load-balancing"></a>Globalne równoważenie obciążenia

Niektóre organizacje chcą najwyższego możliwego poziomu dostępności. Jednym ze sposobów osiągnięcia tego celu jest hostowania aplikacji w globalnie rozproszonych centrach danych. Gdy aplikacja jest hostowana w centrach danych zlokalizowanych na całym świecie, jest możliwe dla całego regionu geopolitycznego, aby stać się niedostępne i nadal mają aplikację i działa.

Ta strategia równoważenia obciążenia może również przynieść korzyści z wydajności. Można kierować żądania usługi do centrum danych, które znajduje się najbliżej urządzenia, które jest żądanie.

Na platformie Azure możesz uzyskać korzyści z globalnego równoważenia obciążenia przy użyciu usługi Azure Traffic Manager.

Więcej informacji:

* [Co to jest usługa Traffic Manager?](../../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Rozpoznawanie nazw

Rozpoznawanie nazw jest funkcją krytyczną dla wszystkich usług hostowania na platformie Azure. Z punktu widzenia zabezpieczeń naruszenie funkcji rozpoznawania nazw może prowadzić do przekierowania żądań przez osobę atakującą z witryn do witryny osoby atakującej. Bezpieczne rozpoznawanie nazw jest wymagane dla wszystkich usług hostowanych w chmurze.

Istnieją dwa typy rozpoznawania nazw, które należy rozwiązać:

* Wewnętrzna rozdzielczość nazw. Jest to używane przez usługi w sieciach wirtualnych, sieciach lokalnych lub obu tych sieciach. Nazwy używane do rozpoznawania nazw wewnętrznych nie są dostępne przez Internet. Dla optymalnego bezpieczeństwa ważne jest, aby wewnętrzny schemat rozpoznawania nazw nie był dostępny dla użytkowników zewnętrznych.
* Rozpoznawanie nazw zewnętrznych. Jest to używane przez osoby i urządzenia spoza sieci lokalnych i sieci wirtualnych. Są to nazwy, które są widoczne w Internecie i są używane do bezpośredniego połączenia z usługami w chmurze.

W przypadku wewnętrznego rozpoznawania nazw dostępne są dwie opcje:

* Serwer DNS sieci wirtualnej. Podczas tworzenia nowej sieci wirtualnej tworzony jest serwer DNS. Ten serwer DNS może rozpoznać nazwy komputerów znajdujących się w tej sieci wirtualnej. Ten serwer DNS nie jest konfigurowalny, jest zarządzany przez Menedżera sieci szkieletowej platformy Azure i dlatego może pomóc w zabezpieczeniu rozwiązania rozpoznawania nazw.
* Przynieś własny serwer DNS. Masz możliwość umieszczenia wybranego przez siebie serwera DNS w sieci wirtualnej. Ten serwer DNS może być zintegrowanym serwerem DNS usługi Active Directory lub dedykowanym rozwiązaniem serwera DNS dostarczonym przez partnera platformy Azure, które można uzyskać z portalu Azure Marketplace.

Więcej informacji:

* [Omówienie sieci wirtualnej](../../virtual-network/virtual-networks-overview.md)
* [Zarządzanie serwerami DNS używanymi przez sieć wirtualną](../../virtual-network/manage-virtual-network.md#change-dns-servers)

W przypadku rozpoznawania nazw zewnętrznych dostępne są dwie opcje:

* Hostuj własny zewnętrzny serwer DNS lokalnie.
* Hostuj własny zewnętrzny serwer DNS u usługodawcy.

Wiele dużych organizacji hostuje własne serwery DNS lokalnie. Mogą to zrobić, ponieważ mają wiedzę na temat sieci i globalną obecność, aby to zrobić.

W większości przypadków lepiej jest hostować usługi rozpoznawania nazw DNS u dostawcy usług. Ci dostawcy usług mają wiedzę specjalistyczną w sieci i globalną obecność, aby zapewnić bardzo wysoką dostępność usług rozpoznawania nazw. Dostępność jest niezbędna w przypadku usług DNS, ponieważ jeśli usługi rozpoznawania nazw nie powiodą się, nikt nie będzie mógł dotrzeć do twoich usług internetowych.

Platforma Azure zapewnia wysoce dostępne i wydajne zewnętrzne rozwiązanie DNS w postaci usługi Azure DNS. To rozwiązanie do rozpoznawania nazw zewnętrznych korzysta z infrastruktury dns platformy Azure na całym świecie. Umożliwia hostowanie domeny na platformie Azure przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń, co inne usługi platformy Azure. W ramach platformy Azure dziedziczy również silne mechanizmy kontroli zabezpieczeń wbudowane w platformę.

Więcej informacji:

* [Omówienie usługi Azure DNS](../../dns/dns-overview.md)
* [Strefy prywatne usługi Azure DNS](../../dns/private-dns-overview.md) umożliwiają konfigurowanie prywatnych nazw DNS dla zasobów platformy Azure, a nie automatycznie przypisywanych nazw bez konieczności dodawania niestandardowego rozwiązania DNS.

## <a name="perimeter-network-architecture"></a>Architektura sieci obwodowej

Wiele dużych organizacji używa sieci obwodowych do segmentowania swoich sieci i tworzenia strefy buforowej między Internetem a ich usługami. Część obwodowa sieci jest uważana za strefę niskiego poziomu zabezpieczeń i nie umieszcza się żadnych zasobów o wysokiej wartości w tym segmencie sieci. Zazwyczaj zobaczysz urządzenia zabezpieczeń sieciowych, które mają interfejs sieciowy w segmencie sieci obwodowej. Inny interfejs sieciowy jest połączony z siecią, która ma maszyny wirtualne i usługi, które akceptują połączenia przychodzące z Internetu.

Sieci obwodowe można projektować na wiele różnych sposobów. Decyzja o wdrożeniu sieci obwodowej, a następnie jakiego typu sieci obwodowej do użycia, jeśli zdecydujesz się jej używać, zależy od wymagań dotyczących zabezpieczeń sieci.

Więcej informacji:

* [Usługi w chmurze firmy Microsoft i zabezpieczenia sieci](network-best-practices.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Ataki typu „rozproszona odmowa usługi” (Distributed Denial of Service, DDoS) należą do największych obaw związanych z dostępnością i zabezpieczeniami wśród klientów, którzy planują przeniesienie swoich aplikacji do chmury. Atak DDoS próbuje wyczerpać zasoby aplikacji, dzięki czemu aplikacja jest niedostępna dla legalnych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.
Firma Microsoft zapewnia ochronę przed atakami DDoS znaną jako **Podstawowa** jako część platformy Azure. Jest to bezpłatne i obejmuje zawsze monitorowanie i łagodzenie w czasie rzeczywistym typowych ataków na poziomie sieci. Oprócz zabezpieczeń dołączonych do ochrony DDoS **Basic** można włączyć opcję **Standardowa.** Funkcje standardu ochrony przed atakami DDoS obejmują:

* **Natywna integracja platformy:** Natywnie zintegrowane z platformą Azure. Obejmuje konfigurację za pośrednictwem witryny Azure portal. Standard ochrony przed atakami DDoS rozumie twoje zasoby i konfigurację zasobów.
* **Ochrona pod klucz:** Uproszczona konfiguracja natychmiast chroni wszystkie zasoby w sieci wirtualnej, gdy tylko zostanie włączony standard ochrony przed atakami DDoS. Nie jest wymagana żadna interwencja ani definicja użytkownika. Standard ochrony przed atakami DDoS natychmiast i automatycznie zmniejsza atak po jego wykryciu.
* **Zawsze włączone monitorowanie ruchu:** Wzorce ruchu aplikacji są monitorowane 24 godziny na dobę, 7 dni w tygodniu, szukając wskaźników ataków DDoS. Łagodzenie jest wykonywane po przekroczeniu zasad ochrony.
* **Raporty łagodzenia ataków** Raporty ograniczania ataków używają zagregowanych danych przepływu sieci, aby dostarczyć szczegółowych informacji o atakach ukierunkowanych na zasoby.
* **Dzienniki przepływu ograniczania ataków** Dzienniki przepływu łagodzenia ataków umożliwiają przeglądanie porzuconego ruchu, przesyłanego dalej ruchu i innych danych ataku w czasie zbliżonym do rzeczywistego podczas aktywnego ataku DDoS.
* **Strojenie adaptacyjne:** Inteligentne profilowanie ruchu uczy się ruchu aplikacji w czasie i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla twojej usługi. Profil dostosowuje się wraz ze zmianami ruchu w czasie. Ochrona warstwy 3 do warstwy 7: zapewnia pełną ochronę przed atakami DDoS w stosie, gdy jest używana z zaporą aplikacji sieci web.
* **Rozbudowana skala łagodzenia:** Ponad 60 różnych typów ataków można złagodzić, z globalnym pojemność, aby chronić przed największymi znanymi atakami DDoS.
* **Wskaźniki ataku:** Podsumowane metryki z każdego ataku są dostępne za pośrednictwem usługi Azure Monitor.
* **Ostrzeganie o ataku:** Alerty można skonfigurować na początku i na zatrzymaniu ataku oraz w czasie trwania ataku, przy użyciu wbudowanych metryk ataku. Alerty są integrowane z oprogramowaniem operacyjnym, takim jak dzienniki monitorów platformy Microsoft Azure, splunk, usługa Azure Storage, poczta e-mail i portal Azure.
* **Gwarancja kosztów:**  Transfer danych i skalowanie aplikacji kredytów usługi dla udokumentowanych ataków DDoS.
* **Szybkość reakcji DDoS** Klienci DDoS Protection Standard mają teraz dostęp do zespołu rapid response podczas aktywnego ataku. DRR może pomóc w badaniu ataku, niestandardowe środki zaradcze podczas ataku i analizy po ataku.


Więcej informacji:

* [Omówienie ochrony DDOS](../../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Usługa Azure Front Door Service umożliwia definiowanie, zarządzanie i monitorowanie globalnego routingu ruchu internetowego. Optymalizuje routing ruchu, aby uzyskać najlepszą wydajność i wysoką dostępność. Usługa Azure Front Door umożliwia tworzenie reguł zapory aplikacji internetowej w celu kontrolowania dostępu, aby chronić obciążenia protokołów HTTP/HTTPS przed wykorzystywaniem w oparciu o adresy IP, kod kraju i parametry protokołu HTTP klientów. Ponadto drzwiami frontowymi umożliwiają również tworzenie reguł ograniczających szybkość do walki ze złośliwym ruchem botów, w tym odciążanie TLS i żądanie HTTP/HTTPS na przetwarzanie warstwy aplikacji.

Sama platforma Front Door jest chroniona przez usługę Azure DDoS Protection w warstwie Podstawowa. W celu zastosowania dalszej ochrony przez atakami w warstwie sieci (protokół TCP/UDP) za pośrednictwem funkcji automatycznego dostrajania i ograniczania ryzyka można włączyć usługę Azure DDoS Protection w warstwie Standardowa w sieciach wirtualnych i zasobach zabezpieczeń. Drzwi frontowe to odwrotny serwer proxy warstwy 7, który umożliwia tylko ruch internetowy, aby przechodzić przez serwery zaplecza i domyślnie blokować inne typy ruchu.

Więcej informacji:

* Aby uzyskać więcej informacji na temat całego zestawu funkcji drzwi y frontowej platformy Azure, można zapoznać się z [omówieniem usługi Azure Front Door](../../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Menedżer usługi Azure Traffic

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi. Usługa Traffic Manager używa systemu DNS do kierowania żądań klientów do najodpowiedniejszego punktu końcowego usługi w oparciu o metodę routingu ruchu i kondycję punktów końcowych. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Menedżer ruchu monitoruje punkty końcowe i nie kieruje ruchu do żadnych punktów końcowych, które są niedostępne.

Więcej informacji:

* [Omówienie usługi Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorowanie i wykrywanie zagrożeń

Platforma Azure oferuje funkcje ułatwiające dostęp do tego kluczowego obszaru dzięki wczesnemu wykrywaniu, monitorowaniu oraz gromadzonej i recenzowaniu ruchu sieciowego.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Usługa Azure Network Watcher może pomóc w rozwiązywaniu problemów i udostępnia zupełnie nowy zestaw narzędzi ułatwiających identyfikację problemów z zabezpieczeniami.

[Widok grupy zabezpieczeń](../../network-watcher/network-watcher-security-group-view-overview.md) pomaga w inspekcji i zgodności z zabezpieczeniami maszyn wirtualnych. Ta funkcja służy do przeprowadzania audytów programowych, porównując zasady linii bazowej zdefiniowane przez organizację z regułami skutecznymi dla każdej z maszyn wirtualnych. Może to pomóc w identyfikacji wszelkich dryfów konfiguracji.

[Przechwytywanie pakietów](../../network-watcher/network-watcher-packet-capture-overview.md) umożliwia przechwytywanie ruchu sieciowego do i z maszyny wirtualnej. Można zbierać statystyki sieci i rozwiązywać problemy z aplikacjami, które mogą być nieocenione w badaniu włamań do sieci. Tej funkcji można również użyć razem z usługami Azure Functions, aby uruchomić przechwytywanie sieci w odpowiedzi na określone alerty platformy Azure.

Aby uzyskać więcej informacji na temat funkcji Kontrola sieci i sposobu rozpoczęcia testowania niektórych funkcji w laboratoriach, zobacz [omówienie monitorowania obserwatora sieci platformy Azure.](../../network-watcher/network-watcher-monitoring-overview.md)

> [!NOTE]
> Aby uzyskać najbardziej aktualne powiadomienia o dostępności i stanie tej usługi, sprawdź [stronę aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center pomaga zapobiegać zagrożeniom, wykrywać i reagować na nie, a także zapewnia lepszy wgląd w zabezpieczenia zasobów platformy Azure i kontrolę nad nim. Zapewnia zintegrowane monitorowanie zabezpieczeń i zarządzanie zasadami w ramach subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które w przeciwnym razie mogą przejść niezauważone, i współpracuje z dużym zestawem rozwiązań zabezpieczających.

Usługa Security Center pomaga zoptymalizować i monitorować bezpieczeństwo sieci poprzez:

* Dostarczanie zaleceń dotyczących zabezpieczeń sieci.
* Monitorowanie stanu konfiguracji zabezpieczeń sieci.
* Ostrzeganie o zagrożeniach opartych na sieci, zarówno na poziomie punktu końcowego, jak i sieci.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Tap sieci wirtualnej

Usługa Azure virtual network TAP (Terminal Access Point) umożliwia ciągłe przesyłanie strumieniowe ruchu sieciowego maszyny wirtualnej do modułu zbierającego pakiety sieciowego lub narzędzia analitycznego. Narzędzie do tworzenia lub analizy jest dostarczane przez partnera urządzenia wirtualnego sieci. Za pomocą tego samego zasobu TAP sieci wirtualnej można agregować ruch z wielu interfejsów sieciowych w tej samej lub innej subskrypcji.

Więcej informacji:

* [Virtual Network TAP](../../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Rejestrowanie

Rejestrowanie na poziomie sieci jest kluczową funkcją dla każdego scenariusza zabezpieczeń sieci. Na platformie Azure można rejestrować informacje uzyskane dla sieciowych grup zabezpieczeń, aby uzyskać informacje dotyczące rejestrowania na poziomie sieci. Dzięki rejestrowaniu NSG otrzymujesz informacje od:

* [Dzienniki aktywności](../../azure-monitor/platform/platform-logs-overview.md). Użyj tych dzienników, aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure. Te dzienniki są domyślnie włączone i mogą być używane w witrynie Azure portal. Były one wcześniej znane jako dzienniki inspekcji lub operacyjne.
* Dzienniki zdarzeń. Te dzienniki zawierają informacje o tym, jakie reguły sieci owe zostały zastosowane.
* Dzienniki licznika. Te dzienniki pozwalają wiedzieć, ile razy każda reguła nsg została zastosowana do odmowy lub zezwolenia na ruch.

Do wyświetlania i analizowania tych dzienników można również użyć programu [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), zaawansowanego narzędzia do wizualizacji danych.
Więcej informacji:

* [Dzienniki usługi Azure Monitor dla sieciowych grup zabezpieczeń (nsg)](../../virtual-network/virtual-network-nsg-manage-log.md)
