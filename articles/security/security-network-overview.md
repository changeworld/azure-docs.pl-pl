---
title: Pojęcia dotyczące zabezpieczeń i wymagań na platformie Azure sieci | Dokumentacja firmy Microsoft
description: Ten artykuł zawiera podstawowe objaśnienia dotyczące podstawowych pojęć związanych z zabezpieczeniami sieci i wymagania i informacje dotyczące ofert platformy Azure w każdym z tych obszarów.
services: security
documentationcenter: na
author: TomShinder
manager: barbkess
editor: TomSh
ms.assetid: bedf411a-0781-47b9-9742-d524cf3dbfc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/29/2018
ms.author: terrylan
ms.openlocfilehash: 2aabe3d1fa8a6034c2dab38c8d6fa6da4b00ac1b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60444269"
---
# <a name="azure-network-security-overview"></a>Omówienie zabezpieczeń sieci platformy Azure

Zabezpieczenia sieciowe można zdefiniować jako procesu ochrony zasobów przed nieautoryzowanym dostępem lub ataku, stosując kontrolki do ruchu sieciowego. Celem jest zapewnienie, że tylko uprawnionym ruch jest dozwolony. System Azure zawiera niezawodną infrastrukturę sieci do obsługi aplikacji i wymagań dotyczących łączności usługi. Łączność sieciowa jest możliwe między zasobami znajdującymi się na platformie Azure, między lokalnych i zasobów, hostowane w systemie Azure oraz do i z Internetu i platformy Azure.

W tym artykule omówiono niektóre z opcji oferowanych przez platformę Azure w obszarze zabezpieczeń sieci. Informacje na temat:

* Sieci platformy Azure
* Kontrola dostępu do sieci
* Azure Firewall
* Zabezpieczanie połączenia zdalnego dostępu i między środowiskami lokalnymi
* Dostępność
* Rozpoznawanie nazw
* Architektura sieci (DMZ) obwodowej
* Azure DDoS Protection
* Azure Front Door
* Usługa Traffic Manager
* Monitorowanie i wykrywanie zagrożeń

## <a name="azure-networking"></a>Sieci platformy Azure

Platforma Azure wymaga połączenia z siecią wirtualną Azure maszyny wirtualne. Sieć wirtualna jest konstrukcją logiczną, zbudowany na podstawie sieci szkieletowej sieci fizycznej platformy Azure. Każda sieć wirtualna jest odizolowana od innych sieci wirtualnych. Pozwala to zagwarantować, że ruch sieciowy we wdrożeniach nie jest dostępna dla innych klientów platformy Azure.

Więcej informacji:

* [Omówienie usługi Virtual network](../virtual-network/virtual-networks-overview.md)

## <a name="network-access-control"></a>Kontrola dostępu do sieci

Kontrola dostępu do sieci jest czynnością ograniczenia łączności do i z określonymi urządzeniami lub podsieci w sieci wirtualnej. Celem kontroli dostępu do sieci jest ograniczenie dostępu do maszyn wirtualnych i usług dla zatwierdzonych użytkowników i urządzeń. Mechanizmy kontroli dostępu są oparte na decyzje, aby udzielić lub odmówić połączenia do i z maszyny wirtualnej lub usługi.

Platforma Azure obsługuje kilka typów kontroli dostępu do sieci, takich jak:

* Kontrola warstwy sieci
* Kontrola kierowania i wymuszonego tunelowania
* Urządzenia zabezpieczeń w sieci wirtualnej

### <a name="network-layer-control"></a>Kontrola warstwy sieci

Wszelkie bezpieczne wdrożenie wymaga niektóre miary kontroli dostępu do sieci. Celem kontroli dostępu do sieci jest ograniczenia komunikacji maszyny wirtualnej na potrzeby systemów. Inne próby komunikacji są blokowane.

> [!NOTE]
> Zapór usługi Storage są objęte [Omówienie zabezpieczeń usługi Azure storage](security-storage-overview.md) artykułu

#### <a name="network-security-rules-nsgs"></a>Reguły zabezpieczeń sieci (NSG)

Jeśli potrzebujesz kontroli dostępu na poziomie basic sieci (na podstawie adresu IP i protokołów TCP lub UDP), można użyć grup zabezpieczeń sieci (NSG). Sieciowa grupa zabezpieczeń jest podstawowy, stanowa, filtrowania zapory pakietów i pozwala na kontrolowanie dostępu na podstawie [5-elementowe spójne kolekcje](https://www.techopedia.com/definition/28190/5-tuple). Sieciowe grupy zabezpieczeń obejmują funkcje w celu uproszczenia zarządzania i zmniejszyć prawdopodobieństwo błędów konfiguracji:

* **Rozszerzone reguły zabezpieczeń** upraszczają definicję reguły sieciowej grupy zabezpieczeń i umożliwiają tworzenie złożonych reguł zamiast konieczności tworzenia wielu proste zasady, aby osiągnąć ten sam wynik.
* **Tagów usług** Microsoft tworzonych etykiet, które reprezentują grupy adresów IP. Dynamiczne zaktualizowanie obejmujący zakresy adresów IP, które spełniają warunki, które definiują włączenia w etykiecie. Na przykład jeśli chcesz utworzyć regułę, która ma zastosowanie do wszystkich magazynu platformy Azure w regionie wschodnie stany służy Storage.EastUS
* **Grupy zabezpieczeń aplikacji** umożliwiają wdrażanie zasobów na grupy aplikacji i kontrolować dostęp do tych zasobów, tworząc reguły o korzystających z tych grup aplikacji. Na przykład jeśli masz wdrożenie w grupie aplikacji "Webservers" serwery sieci Web można utworzyć regułę, która ma zastosowanie sieciowa grupa zabezpieczeń zezwala na ruch 443 z Internetu do wszystkich systemów w grupie aplikacji "Serwery sieci Web".

Sieciowe grupy zabezpieczeń nie są oferowane kontroli warstwy aplikacji lub uwierzytelniony za pomocą kontroli dostępu.

Więcej informacji:

* [Sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md)

#### <a name="asc-just-in-time-vm-access"></a>ASC dostęp dokładnie na czas maszyny Wirtualnej

[Centrum zabezpieczeń Azure](../security-center/security-center-intro.md) zarządzać sieciowe grupy zabezpieczeń na maszynach wirtualnych i zablokować dostęp do maszyny Wirtualnej do użytkownika za pomocą kontroli dostępu opartej na rolach odpowiednie [RBAC](../role-based-access-control/overview.md) uprawnienia żądania dostępu. Po użytkownik pomyślnie autoryzowanych ASC sprawia, że zmiany do sieciowych grup zabezpieczeń, aby umożliwić dostęp do wybranych portów przez czas określony. Po upływie czasu sieciowe grupy zabezpieczeń zostaną przywrócone do ich poprzedniego stanu zabezpieczone.

Więcej informacji:

* [Usługa Azure Security Center dostęp dokładnie na czas](../security-center/security-center-just-in-time.md)

#### <a name="service-endpoints"></a>Punkty końcowe usługi

Punkty końcowe usługi są innym sposobem zastosowania kontroli nad ruchem. Można ograniczyć komunikację z obsługiwanych usług można tylko sieci wirtualne, za pośrednictwem bezpośredniego połączenia. Ruch z sieci wirtualnej do określonej usługi platformy Azure pozostaje w sieci szkieletowej platformy Microsoft Azure.  

Więcej informacji:

* [Punkty końcowe usługi](../virtual-network/virtual-network-service-endpoints-overview.md#securing-azure-services-to-virtual-networks)

### <a name="route-control-and-forced-tunneling"></a>Kontrola kierowania i wymuszonego tunelowania

Ważne jest możliwość kontrolowania zachowania routingu w sieciach wirtualnych. Jeśli routingu jest skonfigurowana niepoprawnie, aplikacji i usług hostowanych na maszynie wirtualnej może nawiązywać połączenie z nieautoryzowanego urządzenia, w tym systemów i jest przez potencjalnych ataków.

Sieć platformy Azure obsługuje możliwość dostosowywania zachowania routingu ruchu w sieci dla sieci wirtualnych. Dzięki temu można zmienić domyślny routing wpisów tabeli w Twojej sieci wirtualnej. Kontroli zachowania routingu pomaga upewnić się, że cały ruch z niektórych urządzeń lub grupy urządzeń wprowadza lub go opuszcza sieci wirtualnej do określonej lokalizacji.

Na przykład Niewykluczone, że wirtualne urządzenie zabezpieczeń sieciowych w sieci wirtualnej. Chcesz upewnić się, że cały ruch do i z sieci wirtualnej przechodzi przez urządzenia wirtualne zabezpieczeń. Można to zrobić, konfigurując [tras zdefiniowanych przez użytkownika](../virtual-network/virtual-networks-udr-overview.md) (Udr) na platformie Azure.

[Wymuszone tunelowanie](https://www.petri.com/azure-forced-tunneling) to mechanizm, można użyć, aby upewnić się, że usługi nie mogą inicjować połączenie w celu urządzeń w Internecie. Należy pamiętać, że to różni się od akceptuje połączenia przychodzące i następnie odpowiada do nich. Serwery frontonu sieci web muszą odpowiadać na żądania z hostami w Internecie, a więc źródło internet ruch jest dozwolony dla ruchu przychodzącego na tych serwerach sieci web i serwerów sieci web mogą odpowiadać.

Czego nie chcesz zezwolić na to serwera frontonu sieci web, aby zainicjować żądanie ruchu wychodzącego. Takie żądania może reprezentować zagrożenie bezpieczeństwa, ponieważ te połączenia może służyć do pobierania złośliwego oprogramowania. Nawet jeśli użytkownik chce tych serwerów frontonu w celu zainicjowania żądania wychodzące z Internetem, możesz chcieć wymusić na nich za pośrednictwem usługi serwerów proxy sieci web w środowisku lokalnym. Dzięki temu można korzystać z adresu URL, filtrowanie i rejestrowania.

Zamiast tego należy użyć wymuszonego tunelowania, aby zapobiec takiej sytuacji. Po włączeniu wymuszonym tunelowaniu muszą wszystkie połączenia z Internetem za pośrednictwem bramy sieci lokalnej. Można skonfigurować wymuszone tunelowanie, wykorzystując tras zdefiniowanych przez użytkownika.

Więcej informacji:

* [Co to są trasy zdefiniowane przez użytkownika i przekazywanie adresów IP](../virtual-network/virtual-networks-udr-overview.md)

### <a name="virtual-network-security-appliances"></a>Urządzenia zabezpieczeń w sieci wirtualnej

Gdy sieciowych grup zabezpieczeń, tras zdefiniowanych przez użytkownika i tunelowania zapewniają poziom zabezpieczeń na poziomie warstwy sieci i mechanizm transportu [OSI model](https://en.wikipedia.org/wiki/OSI_model), można również włączyć zabezpieczenia na poziomie wyższym niż sieć.

Na przykład wymagań dotyczących zabezpieczeń mogą obejmować:

* Uwierzytelnianie i autoryzacja przed zezwoleniem na dostęp do aplikacji
* Wykrywanie włamań i odpowiedzi do nieautoryzowanego dostępu
* Inspekcja warstwy aplikacji dla protokołów wysokiego poziomu
* Filtrowanie adresów URL
* Oprogramowanie antywirusowe z poziomu sieci i ochrony przed złośliwym kodem
* Ochrona antybotowe
* Kontrola dostępu do aplikacji
* Dodatkowa ochrona przed atakami DDoS (powyżej ochrony przed atakami DDoS zapewnianej przez sieci szkieletowej platformy Azure, sam)

Te funkcje zabezpieczeń sieci rozszerzone dostęp za pomocą rozwiązania partnerów platformy Azure. Można znaleźć najbardziej aktualne sieci partnerów platformy Azure rozwiązania w zakresie bezpieczeństwa, odwiedzając [portalu Azure Marketplace](https://azure.microsoft.com/marketplace/)i wyszukując "zabezpieczenia" i "" zabezpieczenia sieciowe.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall to zarządzana, sieciowa usługa zabezpieczeń oparta na chmurze, która zabezpiecza zasoby usługi Azure Virtual Network. Jest to w pełni stanowa zapora oferowana jako usługa, z wbudowaną wysoką dostępnością i możliwością nieograniczonego skalowania w chmurze. Niektóre funkcje:

* Wysoka dostępność
* Skalowalność w chmurze
* Reguły filtrowania w pełni kwalifikowanych nazw domen aplikacji
* Reguły filtrowania ruchu sieciowego

Więcej informacji:

* [Omówienie zapory platformy Azure](../firewall/overview.md)

## <a name="secure-remote-access-and-cross-premises-connectivity"></a>Zabezpieczanie połączenia zdalnego dostępu i między środowiskami lokalnymi

Instalacji, konfiguracji i zarządzania Twoje potrzeby zasobów platformy Azure, można to robić zdalnie. Ponadto, możesz chcieć wdrażać [hybrydowego IT](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) rozwiązania mające składników lokalnych i w chmurze publicznej Azure. Scenariusze te wymagają bezpiecznego dostępu zdalnego.

Sieci platformy Azure obsługuje następujące scenariusze bezpieczny dostęp zdalny:

* Połącz poszczególnych stacji roboczych z siecią wirtualną
* Łączenie sieci lokalnej z siecią wirtualną przy użyciu sieci VPN
* Łączenie sieci lokalnej z siecią wirtualną za pomocą dedykowanego łącza sieci WAN
* Łączenie sieci wirtualnych między sobą

### <a name="connect-individual-workstations-to-a-virtual-network"></a>Połącz poszczególnych stacji roboczych z siecią wirtualną

Możesz chcieć umożliwić indywidualnych deweloperów i pracowników operacyjnych. Ci do zarządzania maszynami wirtualnymi i usługami na platformie Azure. Załóżmy na przykład, że musisz mieć dostęp do maszyny wirtualnej w sieci wirtualnej. Jednak zasady zabezpieczeń nie zezwala na połączenie RDP lub SSH dostępu zdalnego z poszczególnymi maszynami wirtualnymi. W takim przypadku można użyć [sieci VPN typu punkt lokacja](../vpn-gateway/point-to-site-about.md) połączenia.

Połączenia sieci VPN typu punkt lokacja można skonfigurować prywatność i bezpieczeństwo połączenie między użytkownikiem i sieci wirtualnej. Po nawiązaniu połączenia sieci VPN, użytkownik może nawiązać połączenie RDP lub SSH za pośrednictwem łącza sieci VPN do maszyn wirtualnych w sieci wirtualnej. (Przy założeniu, że użytkownik uwierzytelni i jest autoryzowany). Obsługuje sieci VPN typu punkt lokacja:

* Zabezpiecz SSTP Socket Tunneling Protocol (), protokołem opartym na protokole SSL sieci VPN. Rozwiązanie SSL sieci VPN może przechodzić przez zapory, ponieważ większość zapór otwiera port 443 protokołu TCP, który używa protokołu SSL. Protokół SSTP jest obsługiwana tylko na urządzeniach Windows. Platforma Azure obsługuje wszystkich wersji systemu Windows, który ma protokołu SSTP (Windows 7 i nowsze).

* Sieć VPN z protokołem IKEv2 to oparte na standardach rozwiązanie sieci VPN korzystające z protokołu IPsec. Sieci VPN z protokołem IKEv2 można używać do łączenia z urządzeniami Mac (z systemem OSX 10.11 lub nowszym).

* [OpenVPN](https://azure.microsoft.com/updates/openvpn-support-for-azure-vpn-gateways/)

Więcej informacji:

* [Konfigurowanie połączenia punkt lokacja z siecią wirtualną przy użyciu programu PowerShell](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-vpn"></a>Łączenie sieci lokalnej z siecią wirtualną przy użyciu sieci VPN

Można nawiązać połączenia z całej sieci firmowej lub jego części, sieć wirtualną. To jest typowe w hybrydowym IT scenariuszy, których organizacje [rozszerzają swoje centra danych lokalnych na platformę Azure](https://gallery.technet.microsoft.com/Datacenter-extension-687b1d84). W wielu przypadkach organizacjom hostować części usługi na platformie Azure i części lokalnego. Na przykład może to zrobią, gdy rozwiązanie zawiera serwery frontonu sieci web na platformie Azure i lokalne bazy danych zaplecza. Tego rodzaju połączenia "obejmującego", upewnij się również zarządzania platformy Azure znajduje się zasoby bardziej bezpieczny i umożliwia realizację scenariuszy, takich jak rozszerzanie kontrolery domeny usługi Active Directory na platformę Azure.

Jednym ze sposobów, aby wykonać to jest użycie [sieci VPN typu lokacja lokacja](https://www.techopedia.com/definition/30747/site-to-site-vpn). Różnica między sieci VPN lokacja lokacja i punkt lokacja sieci VPN jest, że jego pojedyncze urządzenie łączy się z sieci wirtualnej. Sieć VPN lokacja lokacja łączy całej sieci (na przykład z siecią lokalną) do sieci wirtualnej. Site-to-site VPN do sieci wirtualnej Użyj zabezpieczonych trybu tunelowania IPsec VPN protokołu.

Więcej informacji:

* [Tworzenie sieci wirtualnej usługi Resource Manager za pomocą połączenia sieci VPN lokacja lokacja przy użyciu witryny Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [VPN Gateway — informacje](../vpn-gateway/vpn-gateway-about-vpngateways.md)

### <a name="connect-your-on-premises-network-to-a-virtual-network-with-a-dedicated-wan-link"></a>Łączenie sieci lokalnej z siecią wirtualną za pomocą dedykowanego łącza sieci WAN

Połączenia sieci VPN typu punkt lokacja i lokacja lokacja są skuteczne umożliwiające łączność między wieloma lokalizacjami. Jednak w niektórych organizacjach należy wziąć pod uwagę, aby miał następujące wady:

* Połączenia sieci VPN przenoszenie danych za pośrednictwem Internetu. To udostępnia te połączenia na potencjalne problemy związane z przenoszeniem danych przez sieć publiczną. Ponadto nie można zagwarantować niezawodności i dostępności dla połączeń internetowych.
* Połączenia sieci VPN do sieci wirtualnych mogą nie mieć przepustowości dla niektórych aplikacji i celów, w miarę ich maksymalna się na około 200 MB/s.

Organizacje, które zazwyczaj należy najwyższy poziom bezpieczeństwa i dostępności do ustanawiania połączeń obejmujących wiele lokalizacji za pomocą dedykowanego łącza sieci WAN połączyć z lokacjami zdalnymi. Azure oferuje możliwość korzystania z dedykowanych łącza sieci WAN, która umożliwia łączenie sieci lokalnej z siecią wirtualną. Usługa Azure ExpressRoute Express route bezpośrednie i Express route globalny zasięg włączyć tę opcję.

Więcej informacji:

* [ExpressRoute — opis techniczny](../expressroute/expressroute-introduction.md)
* [Bezpośrednie z usługi ExpressRoute](../expressroute/expressroute-erdirect-about.md)
* [Express route globalny zasięg](..//expressroute/expressroute-global-reach.md)

### <a name="connect-virtual-networks-to-each-other"></a>Łączenie sieci wirtualnych między sobą

Istnieje możliwość wiele sieci wirtualnych na potrzeby wdrożenia. Istnieją różne powody, dlaczego można to zrobić. Można uproszczenie zarządzania lub może być większe bezpieczeństwo. Niezależnie od tego, motywy umieszczenie zasobów w różnych sieciach wirtualnych może być konieczny zasoby w każdej sieci, aby połączyć ze sobą.

Jedną z opcji jest dla usług w jednej sieci wirtualnej na łączenie się z usługami w innej sieci wirtualnej, "ponownie pętli" za pośrednictwem Internetu. Połączenie jest uruchamiany w jednej sieci wirtualnej, przechodzi przez internet, a następnie wróci do docelowej sieci wirtualnej. Ta opcja udostępnia połączenie problemy z zabezpieczeniami, nieprzerwaną pracę w jakiejkolwiek korespondencji oparty na Internecie.

Lepszym rozwiązaniem może być tworzenie sieci VPN lokacja lokacja, który nawiązuje połączenie między dwiema sieciami wirtualnymi. Ta metoda używa tych samych [trybu tunelowania IPSec](https://technet.microsoft.com/library/cc786385.aspx) protokołu jako połączenie sieci VPN lokacja lokacja między środowiskami lokalnymi wymienionych powyżej.

Zaletą tego podejścia jest to, że połączenie sieci VPN odbywają się za pośrednictwem sieci szkieletowej sieci platformy Azure, a nie połączenie za pośrednictwem Internetu. Zapewnia dodatkową warstwę zabezpieczeń, w porównaniu do sieci VPN typu lokacja lokacja, łączących się za pośrednictwem Internetu.

Więcej informacji:

* [Konfigurowanie połączenia sieć wirtualna-sieć wirtualna za pomocą usługi Azure Resource Manager i programu PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

Innym sposobem łączenia sieci wirtualnych jest [komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md). Ta funkcja umożliwia łączenie dwóch sieci platformy Azure, dzięki czemu komunikację między nimi wykonuje się za pośrednictwem infrastruktury sieci szkieletowej firmy Microsoft bez go przekażesz je przez Internet. Komunikacja równorzędna sieci wirtualnych można połączyć dwie sieci wirtualne w tym samym regionie lub dwiema sieciami wirtualnymi w regionach platformy Azure. Sieciowe grupy zabezpieczeń, można ograniczyć łączność między różnych podsieci lub systemów.

## <a name="availability"></a>Dostępność

Dostępność jest kluczowym składnikiem programu zabezpieczeń. Jeśli muszą uzyskiwać dostęp za pośrednictwem sieci nie dostępu użytkowników i systemów, usługa jest uznawana za naruszenia zabezpieczeń. Platforma Azure oferuje technologii sieciowych, które obsługują następujące mechanizmy wysokiej dostępności:

* Równoważenie obciążenia oparte na protokole HTTP
* Poziom Usługa równoważenia obciążenia sieci
* Globalne równoważenia obciążenia

Równoważenie obciążenia jest mechanizm umożliwiający równe rozłożenie połączeń między wieloma urządzeniami. Dostępne są następujące cele równoważenia obciążenia:

* Aby zwiększyć dostępność. Podczas ładowania równoważenia połączenia na wielu urządzeniach, co najmniej jedno z urządzeń może stać się niedostępne bez uszczerbku dla usługi. Usług działających na pozostałe urządzenia w trybie online, mogą nadal obsługiwać zawartość z usługi.
* Aby zwiększyć wydajność. Gdy załadujesz saldo połączeń na wielu urządzeniach pojedynczego urządzenia nie ma do obsługi całego procesu przetwarzania. Zamiast tego żądania przetwarzania i pamięci do obsługi zawartości jest rozłożona się między wieloma urządzeniami.

### <a name="http-based-load-balancing"></a>Równoważenie obciążenia oparte na protokole HTTP

Organizacje, które uruchamiania usług opartych na sieci web, często pozwalają mieć modułu równoważenia obciążenia oparty na protokole HTTP, przed tych usług sieci web. Pomaga to zapewnić odpowiednie poziomy wydajności i wysokiej dostępności. Moduły równoważenia obciążenia tradycyjnych, oparte na sieci zależy od sieci i mechanizm transportu protokołów warstwy. Moduły równoważenia obciążenia oparty na protokole HTTP, z drugiej strony, decyzje na podstawie charakterystyki protokołu HTTP.

Usługa Azure Application Gateway zapewnia oparte na protokole HTTP równoważenia obciążenia dla usług sieci web. Usługa Application Gateway obsługuje:

* Koligacja sesji na podstawie pliku cookie. Ta funkcja zwiększa się, że połączeń ustanowionych z jednym z serwerów za modułem równoważenia obciążenia, że pozostaje niezmienione między klientem i serwerem. Dzięki temu stabilności transakcji.
* Odciążanie protokołu SSL. Gdy klient nawiąże połączenie z modułem równoważenia obciążenia, czy sesja jest szyfrowana przy użyciu protokołu HTTPS (SSL). Jednak aby zwiększyć wydajność, służy protokół HTTP (bez szyfrowania) do połączenia między równoważenia obciążenia i serwerem internetowym za modułem równoważenia obciążenia. To nazywa się "Odciążanie protokołu SSL", ponieważ serwery sieci web za modułem równoważenia obciążenia nie występuje obciążenie procesora związane z szyfrowaniem. Serwery sieci web w związku z tym można szybciej obsługi żądań.
* Adres URL routingu opartego na zawartości. Ta funkcja umożliwia równoważenia obciążenia do podejmowania decyzji o, gdzie należy połączeń do przodu, w oparciu o docelowy adres URL. To zapewnia znacznie większą elastyczność niż rozwiązań, dzięki którym obciążenia równoważenia decyzje na podstawie adresów IP.

Więcej informacji:

* [Application Gateway — omówienie](../application-gateway/application-gateway-introduction.md)

### <a name="network-level-load-balancing"></a>Poziom Usługa równoważenia obciążenia sieci

W przeciwieństwie do równoważenia obciążenia oparty na protokole HTTP Równoważenie obciążenia poziomu sprawia, że decyzje oparte na IP adres i port (TCP lub UDP) liczby.
Aby uzyskać korzyści wynikające z poziomu równoważenia obciążenia sieciowego w platformie Azure przy użyciu usługi Azure Load Balancer. Zawiera kilka kluczowych charakterystyk modułu równoważenia obciążenia:

* Równoważenie obciążenia poziomu na podstawie liczby adresów IP adres i port.
* Wsparcie dla każdego protokołu warstwy aplikacji.
* Równoważenie obciążenia maszyn wirtualnych platformy Azure i wystąpień ról usługi w chmurze.
* Może służyć do Internetu (Równoważenie obciążenia zewnętrznych) i -internet dostępny z maszyny wirtualne i aplikacje (wewnętrzne Równoważenie obciążenia).
* Punkt końcowy monitorowania, która służy do określania, jeśli dowolnej usługi za modułem równoważenia obciążenia stały się niedostępne.

Więcej informacji:

* [Moduł równoważenia obciążenia dostępnego z Internetu między wieloma maszynami wirtualnymi lub usługami](../load-balancer/load-balancer-internet-overview.md)
* [Omówienie modułu równoważenia obciążenia wewnętrznego](../load-balancer/load-balancer-internal-overview.md)

### <a name="global-load-balancing"></a>Globalne równoważenia obciążenia

Niektóre organizacje mają najwyższy poziom dostępności to możliwe. Jednym ze sposobów osiągnięcia tego celu jest do hostowania aplikacji w globalnie rozproszonych centrach danych. Gdy aplikacja jest hostowana w centrach danych znajdujących się na całym świecie, jest możliwe dla całego regionu geopolitycznego staną się niedostępne i nadal mieć aplikację i uruchomione.

Ta strategia równoważenia obciążenia może również przynieść korzyści wydajności. Można kierować żądania do centrum danych, które jest najbardziej zbliżona do urządzenia, które żądanie zostało wysłane przez usługę.

Na platformie Azure możesz uzyskać korzyści wynikające z globalnego równoważenia obciążenia za pomocą usługi Azure Traffic Manager.

Więcej informacji:

* [Co to jest usługa Traffic Manager?](../traffic-manager/traffic-manager-overview.md)

## <a name="name-resolution"></a>Rozpoznawanie nazw

Rozpoznawanie nazw jest funkcją krytyczne dla wszystkich usług, które są hostowane na platformie Azure. Z punktu widzenia zabezpieczeń naruszenia funkcja rozpoznawania nazw może prowadzić do osoba atakująca Przekierowywanie żądań z witryny do witryny. Rozpoznawanie nazw bezpieczne jest wymagana dla wszystkich usług hostowane w chmurze.

Istnieją dwa rodzaje rozpoznawania nazw, które należy spełnić:

* Rozpoznawania nazw wewnętrznych. Jest on używany przez usługi w sieciach wirtualnych i/lub sieci lokalnej. Nazwy używane do rozpoznawania nazw wewnętrznych nie są dostępne za pośrednictwem Internetu. Zapewnienie optymalnego poziomu bezpieczeństwa ważne jest schemat rozpoznawania nazw wewnętrznych nie jest dostępny dla użytkowników zewnętrznych.
* Rozpoznawanie zewnętrznych. Jest on używany przez osobom i urządzeniem poza z sieciami lokalnymi i sieciami wirtualnymi. Są to nazwy, które są widoczne w Internecie i umożliwia bezpośrednie połączenie do usług w chmurze.

Rozpoznawanie nazw wewnętrznych masz dwie opcje:

* Serwer DNS sieci wirtualnej. Podczas tworzenia nowej sieci wirtualnej, serwer DNS jest tworzone. Tego serwera DNS można rozwiązać nazwy maszyn znajdujących się w tej sieci wirtualnej. Ten serwer DNS nie jest konfigurowalne, jest zarządzane przez Menedżera sieci szkieletowej platformy Azure i w związku z tym może pomóc zabezpieczyć swoje rozwiązanie rozpoznawania nazw.
* Przenieś własnego serwera DNS. Istnieje możliwość wprowadzenia serwer DNS, wybranej przez użytkownika w sieci wirtualnej. Ten serwer DNS może być zintegrowane usługi Active Directory, serwer DNS lub dedykowane rozwiązania serwera DNS, udostępniane przez partnerów platformy Azure, który można uzyskać w portalu Azure Marketplace.

Więcej informacji:

* [Omówienie usługi Virtual network](../virtual-network/virtual-networks-overview.md)
* [Zarządzanie serwerami DNS używanymi przez sieć wirtualną](../virtual-network/manage-virtual-network.md#change-dns-servers)

Rozpoznawanie nazw zewnętrznych masz dwie opcje:

* Hostowanie własnych zewnętrznych DNS serwera w środowisku lokalnym.
* Hostowanie własnego zewnętrznego serwera DNS u dostawcy usług.

W wielu dużych organizacjach hostować własne DNS serwerów w środowisku lokalnym. Mogą one to robić, ponieważ mają one sieci wiedzę i całym świecie, aby to zrobić.

W większości przypadków zaleca się udostępnić swoje usługi rozpoznawania nazw DNS u dostawcy usług. Ci dostawcy usług mają doświadczenie sieci i całym świecie, aby zapewnić bardzo wysoką dostępność dla Twojej usługi rozpoznawania nazw. Dostępność jest niezbędne dla usługi DNS, ponieważ w przypadku awarii usługi rozpoznawania nazw, nie będą mogli nawiązać połączenie z Internetem usług.

Platforma Azure zapewnia wysoce dostępnych i o wysokiej wydajności zewnętrznych DNS rozwiązanie w formie usługi Azure DNS. To rozwiązanie rozpoznawania nazw zewnętrznych korzysta z infrastruktury DNS platformy Azure na całym świecie. Umożliwia ona Hostuj swoją domenę na platformie Azure przy użyciu tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co inne usługi platformy Azure. W ramach platformy Azure również dziedziczy formantów silnych zabezpieczeń wbudowaną w platformę.

Więcej informacji:

* [Omówienie usługi Azure DNS](../dns/dns-overview.md)
* [Strefami prywatnymi usługi Azure DNS](../dns/private-dns-overview.md) umożliwia skonfigurowanie prywatnego nazwy DNS dla zasobów platformy Azure, a nie nazwy przypisany automatycznie, bez konieczności dodawania niestandardowego rozwiązania DNS.

## <a name="perimeter-network-architecture"></a>Architektura sieci obwodowej

W wielu dużych organizacjach umożliwia segmentu ich sieci w sieci obwodowej, a następnie utworzyć strefę bufor między Internetem a swoich usług. Obwodowej część sieci jest uważany za strefę na niskim poziomie zabezpieczeń, a żadne zasoby o wysokiej wartości są umieszczane w tym segmencie sieci. Zazwyczaj zobaczysz urządzeń zabezpieczeń sieciowych, mających interfejs sieciowy w segmencie sieci obwodowej. Inny interfejs sieciowy jest podłączony do sieci maszyn wirtualnych i usług, które akceptują połączenia przychodzące z Internetu.

Można zaprojektować sieci obwodowych na wiele różnych sposobów. Decyzja o wdrożeniu sieci obwodowej, a następnie jakiego rodzaju obwód sieci do użycia, jeśli zdecydujesz się go użyć, zależy od wymagań dotyczących zabezpieczeń sieci.

Więcej informacji:

* [Zabezpieczenia usług firmy Microsoft w chmurze i sieci](../best-practices-network-security.md)

## <a name="azure-ddos-protection"></a>Azure DDoS Protection

Ataki typu „rozproszona odmowa usługi” (Distributed Denial of Service, DDoS) należą do największych obaw związanych z dostępnością i zabezpieczeniami wśród klientów, którzy planują przeniesienie swoich aplikacji do chmury. Próby ataków DDoS na wyczerpanie zasobów aplikacji, że aplikacja staje się niedostępny dla uprawnionych użytkowników. Celem ataku DDoS może być dowolny punkt końcowy publicznie dostępny za pośrednictwem Internetu.
Firma Microsoft zapewnia ochronę przed atakami DDoS, znane jako **podstawowe** w ramach platformy Azure. To jest dostępna bez dodatkowych opłat i zawsze obejmuje środki zaradcze monitorowania i w czasie rzeczywistym w typowych ataków w poziomie sieci. Oprócz zabezpieczenia dołączone do ochrony przed atakami DDoS **podstawowe** można włączyć **standardowa** opcji. Usługa DDoS Protection standardowe funkcje obejmują:

* **Integracja platformy natywnej:** Natywnie zintegrowana z platformą Azure. Obejmuje konfigurację za pomocą witryny Azure portal. Standard ochrony przed atakami DDoS uwzględnia zasoby i konfiguracji zasobów.
* **Kompleksowa ochrona:** Uproszczona konfiguracja chroni wszystkie zasoby w sieci wirtualnej tak szybko, jak standardowy ochrony przed atakami DDoS jest włączona. Brak definicji interwencji lub użytkownika jest wymagana. Standard ochrony przed atakami DDoS natychmiast i automatycznie minimalizuje skuteczność ataku, po jego wykryciu.
* **Zawsze włączone monitorowanie ruchu:** Wzorców ruchu dla twojej aplikacji są monitorowane 24 godzin dziennie, 7 dni w tygodniu, wyszukiwanie wskaźników ataki DDoS. Środki zaradcze odbywa się po przekroczeniu zasady ochrony.
* **Raporty ograniczania ryzyka ataków** raporty ograniczania ryzyka ataków umożliwia uzyskanie szczegółowych informacji dotyczących ataków ukierunkowanych na Twoje zasoby sieci zagregowane dane przepływu.
* **Dzienniki przepływu ograniczania ryzyka ataków** dzienników przepływu ograniczania ryzyka ataków umożliwiają przeglądanie porzuconych ruchu, przekazane ruchu i inne dane ataku w niemal w czasie rzeczywistym podczas aktywnego ataków DDoS.
* **Dostrajanie adaptacyjne:** Profilowanie ruchu inteligentne uzyskuje informacje o ruchu danej aplikacji wraz z upływem czasu i wybiera i aktualizuje profil, który jest najbardziej odpowiedni dla Twojej usługi. Profil, który dostosowuje się zgodnie z ruchem zmienia się wraz z upływem czasu. Warstwa 3 warstwy 7 ochrony: Zapewnia ochronę przed atakami DDoS pełnego stosu, gdy jest używane z zapory aplikacji sieci web.
* **Skaluj rozbudowane środki zaradcze:** Ponad 60 ataku różnych typów można zminimalizować globalnego pojemność, aby zapewnić ochronę przed największych znane ataki DDoS.
* **Metryki ataku:** Skrócona metryki z każdego ataku są dostępne za pośrednictwem usługi Azure Monitor.
* **Alertowanie ataku:** Alerty można skonfigurować na początku i Zatrzymaj atak i czasie trwania ataku przy użyciu ataku wbudowanych metryk. Alerty zintegrować operacyjne oprogramowania, takie jak dzienniki, Splunk, usługi Azure Storage, poczty E-mail i witryny Azure portal Microsoft Azure Monitor.
* **Gwarancja kosztów:**  Transfer danych i aplikacji skalowalnego w poziomie zniżki w ramach udokumentowane ataki DDoS.
* **Dynamiczne Rapid przed atakami DDoS** klientom wersji Standard ochrony przed atakami DDoS mają teraz dostęp do zespołu Rapid Response podczas aktywnego ataku. DRR może ułatwić badanie ataku, niestandardowe środki zaradcze podczas ataku i analizy po ataku.


Więcej informacji:

* [Omówienie ochrony przed atakami DDOS](../virtual-network/ddos-protection-overview.md)

## <a name="azure-front-door"></a>Azure Front Door

Usługa drzwiami frontowymi umożliwia definiowanie, zarządzanie i monitorowanie globalnego routingu ruchu w sieci web. Optymalizuje Twój ruch routingu dla uzyskania najlepszej wydajności i wysokiej dostępności. Usługa Azure Front Door umożliwia tworzenie reguł zapory aplikacji internetowej w celu kontrolowania dostępu, aby chronić obciążenia protokołów HTTP/HTTPS przed wykorzystywaniem w oparciu o adresy IP, kod kraju i parametry protokołu HTTP klientów. Ponadto drzwiami frontowymi umożliwia również tworzenie reguł gruntownie złośliwym ruchem botów ograniczania szybkości, zawiera on odciążanie protokołu SSL i żądania dla protokołu HTTP/HTTPS przetwarzania w warstwie aplikacji.

Samą platformę drzwiami frontowymi jest chroniony przez usługę Azure DDoS Protection podstawowa. W celu zastosowania dalszej ochrony przez atakami w warstwie sieci (protokół TCP/UDP) za pośrednictwem funkcji automatycznego dostrajania i ograniczania ryzyka można włączyć usługę Azure DDoS Protection w warstwie Standardowa w sieciach wirtualnych i zasobach zabezpieczeń. Drzwiami frontowymi jest zwrotny serwer proxy warstwy 7, umożliwia tylko ruch internetowy do przejścia do tworzenia kopii zakończenie serwerów i domyślnego blokowania z innych typów ruchu.

Więcej informacji:

* Aby uzyskać więcej informacji na cały zestaw Azure frontonu drzwiczki możliwości można przejrzeć [drzwi wejściowe platformy Azure — omówienie](../frontdoor/front-door-overview.md)

## <a name="azure-traffic-manager"></a>Usługa Azure Traffic manager

Usługa Azure Traffic Manager to oparty na systemie DNS moduł równoważenia obciążenia ruchu, który umożliwia optymalną dystrybucję ruchu do usług w wielu regionach platformy Azure na świecie, przy jednoczesnym zapewnieniu wysokiej dostępności i krótkiego czasu odpowiedzi. Usługa Traffic Manager używa systemu DNS do kierowania żądań klientów do najodpowiedniejszego punktu końcowego usługi w oparciu o metodę routingu ruchu i kondycję punktów końcowych. Punkt końcowy to dowolna internetowa usługa hostowana wewnątrz platformy Azure lub poza nią. Usługa Traffic manager monitoruje punktów końcowych i nie kierować ruch do żadnych punktów końcowych, które są niedostępne.

Więcej informacji:

* [Azure Traffic manager — omówienie](../traffic-manager/traffic-manager-overview.md)

## <a name="monitoring-and-threat-detection"></a>Monitorowanie i wykrywanie zagrożeń

Platforma Azure udostępnia funkcje pomagające w tym obszarze klucza z wczesne wykrywanie, monitorowania i zbierania i przeglądania ruch sieciowy.

### <a name="azure-network-watcher"></a>Azure Network Watcher

Usługa Azure Network Watcher może pomóc w rozwiązaniu i oferuje zupełnie nowy zestaw narzędzi, która pomaga w identyfikacji problemów z zabezpieczeniami.

[Widok grupy zabezpieczeń](../network-watcher/network-watcher-security-group-view-overview.md) może ułatwić realizację zgodności inspekcji i zabezpieczeń maszyn wirtualnych. Ta funkcja służy do wykonywania inspekcji programowy, porównanie zasady linii bazowej zdefiniowany przez Twoją organizację do skutecznego reguł dla poszczególnych maszyn wirtualnych. Może to ułatwić identyfikację dowolne odstępstwo konfiguracji.

[Przechwytywanie pakietów](../network-watcher/network-watcher-packet-capture-overview.md) umożliwia przechwytywanie ruchu sieciowego do i z maszyny wirtualnej. Można zbierać statystykę sieci i rozwiązywanie problemów z aplikacjami, które mogą być cenne, ponieważ w badaniu sieci intruzami. Ta funkcja wraz z usługi Azure Functions umożliwia również uruchomić sieci w odpowiedzi na konkretne alerty platformy Azure.

Aby uzyskać więcej informacji na temat usługi Network Watcher i uruchamiania, niektóre funkcje testowania w laboratorium, zobacz [omówienie monitorowania w usłudze Azure network watcher](../network-watcher/network-watcher-monitoring-overview.md).

> [!NOTE]
> Aby uzyskać najbardziej aktualne powiadomienia dotyczące dostępności i stanu tej usługi, sprawdź [strony aktualizacje platformy Azure](https://azure.microsoft.com/updates/?product=network-watcher).

### <a name="azure-security-center"></a>Azure Security Center

Usługa Azure Security Center pomaga zapobiegać zagrożeniom, wykrywanie i reagowanie na zagrożenia i zapewnia większą widoczność i kontrolę nad, zabezpieczenia zasobów platformy Azure. Zapewnia zabezpieczenia zintegrowane monitorowanie i zarządzanie zasadami subskrypcji platformy Azure, pomaga wykrywać zagrożenia, które mogłyby w przeciwnym razie pozostać niezauważone, a także współpracuje z szerokiej gamy rozwiązań w zakresie bezpieczeństwa.

Usługa Security Center pomaga optymalizować i monitorować bezpieczeństwo sieci przez:

* Udostępnia zalecenia dotyczące zabezpieczeń sieci.
* Monitorowanie stanu konfiguracji zabezpieczeń sieci.
* Alerty o sieci na podstawie zagrożenia, zarówno na poziomie punktu końcowego i sieci.

Więcej informacji:

* [Wprowadzenie do usługi Azure Security Center](../security-center/security-center-intro.md)

### <a name="virtual-network-tap"></a>Virtual Network TAP

Usługa Azure virtual network TAP (Terminal punktem dostępu) umożliwia ciągłego strumienia do narzędzia sieci pakiet modułu zbierającego lub analizy ruchu sieciowego maszyny wirtualnej. Moduł zbierający lub narzędzia do analizy jest dostarczany przez partnera wirtualnego urządzenia sieciowego. Można użyć tej samej sieci wirtualnej zasobów wzorca TAP agregacji ruchu z wieloma interfejsami sieciowymi w tej samej lub różnych subskrypcji.

Więcej informacji:

* [PODSŁUCHU sieci wirtualnej](../virtual-network/virtual-network-tap-overview.md)

### <a name="logging"></a>Rejestrowanie

Rejestrowanie na poziomie sieci jest funkcją klucza dla dowolnych scenariuszy zabezpieczeń sieci. Na platformie Azure możesz rejestrować informacje uzyskane dla sieciowych grup zabezpieczeń, można pobrać na poziomie sieci, rejestrowanie informacji. Za pomocą funkcji rejestrowania sieciowej grupy zabezpieczeń, można uzyskać informacji o:

* [Dzienniki aktywności](../azure-monitor/platform/activity-logs-overview.md). Użyj tych dzienników, aby wyświetlić wszystkie operacje przesłane do subskrypcji platformy Azure. Te dzienniki są domyślnie włączone i mogą być używane w witrynie Azure portal. Zostały one wcześniej nazywane inspekcji lub dzienniki operacyjne.
* Dzienniki zdarzeń. Te dzienniki zawierają informacje dotyczące reguły sieciowej grupy zabezpieczeń, które zostały zastosowane.
* Dzienniki liczników. Te dzienniki umożliwiają wiedzieć, ile razy każdą regułę sieciowej grupy zabezpieczeń została zastosowana do odmowy lub zezwolić na ruch.

Można również użyć [Microsoft Power BI](https://powerbi.microsoft.com/what-is-power-bi/), narzędzi do wizualizacji danych zaawansowane, aby przeglądać i analizować te dzienniki.
Więcej informacji:

* [Dzienniki monitora platformy Azure dla sieciowych grup zabezpieczeń (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)
