---
title: Usługa DNS platformy Azure — często zadawane pytania
description: Często zadawane pytania dotyczące usługi Azure DNS
services: dns
author: vhorne
ms.service: dns
ms.topic: article
ms.date: 3/21/2019
ms.author: victorh
ms.openlocfilehash: 4f0800dfd264059e1dc8aac32a54f216f777647f
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "58905719"
---
# <a name="azure-dns-faq"></a>Usługa DNS platformy Azure — często zadawane pytania

## <a name="about-azure-dns"></a>O usłudze Azure DNS

### <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

System nazw domen (DNS) tłumaczy lub usuwa nazwę witryny sieci Web lub usługi na jej adres IP. Usługa DNS platformy Azure to usługa hostingowa przeznaczona dla domen DNS. Umożliwia rozpoznawanie nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Domen DNS w usłudze Azure DNS znajdują się w usłudze Azure globalna sieć serwerów nazw DNS. Ten system korzysta z najbliższych tak, aby każde zapytanie DNS jest odbierane przez najbliższego dostępnego serwera DNS. Usługa Azure DNS zapewnia wysoką wydajność i wysoką dostępność domeny.

Usługa system DNS Azure jest oparta na usłudze Resource Manager. Usługa Azure DNS korzysta z funkcji Menedżera zasobów, takich jak kontrola dostępu oparta na rolach, dzienniki inspekcji i blokowanie zasobów. Możesz zarządzać domenach i rekordach za pośrednictwem witryny Azure portal, poleceń cmdlet programu Azure PowerShell i wiersza polecenia platformy Azure dla wielu platform. Aplikacje wymagające automatycznego zarządzania usługą DNS można zintegrować z usługi za pośrednictwem interfejsu API REST i zestawów SDK.

### <a name="how-much-does-azure-dns-cost"></a>Ile kosztuje usługa Azure DNS

Model rozliczeń za usługę Azure DNS jest na podstawie liczby stref DNS hostowanych w usłudze Azure DNS. Opiera się również na liczby zapytań DNS, które otrzyma. Rabaty znajdują się na podstawie użycia.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi system DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaka jest umowa SLA dla usługi Azure DNS?

Platforma Azure gwarantuje, że prawidłowe zapytania DNS odpowiedzi zwracane z co najmniej jeden serwer nazw DNS platformy Azure 100% czasu.

Aby uzyskać więcej informacji, zobacz [strony umowy SLA platformy Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co to jest strefa DNS? Czy to jest to samo co „domena DNS”? 

Domena jest unikatową nazwą w systemie nazw domen. Przykładowa domena to contoso.com.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Na przykład domena contoso.com może zawierać wiele rekordów DNS. Rekordy mogą obejmować mail.contoso.com dla serwera poczty oraz www\.contoso.com dla witryny sieci Web. Te rekordy są hostowane w strefie DNS contoso.com.

Nazwa domeny jest *tylko nazwa*. Strefa DNS jest zasób danych, który zawiera rekordy DNS dla nazwy domeny. Usługa Azure DNS umożliwia hostowanie strefy DNS i zarządzanie rekordami DNS dla domeny na platformie Azure. Umożliwia także serwery DNS do odpowiadania na kwerendy DNS z Internetu.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Czy muszę kupić nazwy domeny DNS, aby używać usługi Azure DNS? 

Niekoniecznie.

Nie musisz kupować domeny, aby hostować strefę DNS w usłudze Azure DNS. Strefę DNS można utworzyć w dowolnej chwili bez konieczności posiadania nazwy domeny. Zapytania DNS dla tej strefy rozpoznać tylko, jeśli jest skierowany do serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Aby połączyć strefę DNS z globalną hierarchią systemu, musisz kupić nazwy domeny. Następnie, zapytania DNS z dowolnego miejsca na świecie znajdowanie strefy DNS i odpowiedzi z rekordami systemu DNS.

## <a name="azure-dns-features"></a>Funkcje usługi Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Czy istnieją jakieś ograniczenia w przypadku używania rekordów aliasów dla wierzchołku nazwy domeny usługi Traffic Manager?

Tak. Statyczne publiczne adresy IP należy użyć z usługą Azure Traffic Manager. Konfigurowanie **zewnętrzny punkt końcowy** docelowej przy użyciu statycznego adresu IP. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Usługa Azure DNS obsługuje trybu failover routingu lub punktu końcowego ruchu oparte na systemie DNS?

Tryb failover routingu i punktu końcowego ruchu oparte na systemie DNS są dostarczane przez usługę Traffic Manager. Traffic Manager to osobna usługa Azure mogą być używane z usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Traffic Manager — omówienie](../traffic-manager/traffic-manager-overview.md).

Usługa DNS platformy Azure obsługuje tylko hostowania statycznych domen DNS, w którym każde zapytanie DNS dla danego rekordu DNS zawsze będzie otrzymywał tę samą odpowiedź DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Usługa DNS platformy Azure obsługuje rejestracji nazwy jej domeny?

Nie. Usługa Azure DNS nie obsługuje obecnie możliwość zakupienia nazw domen. Aby kupić domen, należy użyć Rejestratora nazw domen innej firmy. Rejestrator zwykle opłaty za niewielką opłatą roczną. Domeny następnie mogą być hostowane w usłudze Azure DNS do zarządzania rekordami DNS. Więcej informacji można znaleźć w temacie [Delegowanie domeny do usługi DNS platformy Azure](dns-domain-delegation.md).

Funkcja zakupu nazw domen jest śledzona w zaległości platformy Azure. Użyj witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Usługa Azure DNS obsługuje rozszerzenia DNSSEC?

Nie. Usługa Azure DNS nie obsługuje obecnie Domain Name System Security Extensions (DNSSEC).

Funkcja rozszerzenia DNSSEC jest śledzona w zaległości w usłudze Azure DNS. Użyj witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Usługa DNS platformy Azure obsługuje transfery strefy (AXFR/IXFR)?

Nie. Usługa Azure DNS nie obsługuje obecnie transferów stref. Strefy DNS mogą być [importowane do usługi Azure DNS przy użyciu wiersza polecenia platformy Azure](dns-import-export.md). Rekordy DNS są zarządzane za pośrednictwem [portalu zarządzania usługi Azure DNS](dns-operations-recordsets-portal.md), [interfejsu API REST](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md), lub [ Narzędzie interfejsu wiersza polecenia](dns-operations-recordsets-cli.md).

Funkcja transfer strefy jest śledzona w zaległości w usłudze Azure DNS. Użyj witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Usługa Azure DNS obsługuje adres URL przekierowania?

Nie. Adres URL przekierowania usług nie są usługi DNS. Działają na poziomie protokołu HTTP, a nie na poziomie usługi DNS. Niektórzy dostawcy DNS pakietu usługi Przekierowywanie adresu URL jako część swojej oferty ogólne. Ta usługa nie jest obecnie obsługiwane przez usługę Azure DNS.

Funkcja Przekierowywanie adresu URL jest śledzona w zaległości w usłudze Azure DNS. Użyj witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Usługa Azure DNS obsługuje rozszerzone ASCII (8-bitowa) zestawu dla zestawów rekordów TXT kodowania?

Tak. Usługa DNS platformy Azure obsługuje ASCII rozszerzonego zestawu dla zestawów rekordów TXT kodowania. Ale musisz użyć najnowszej wersji interfejsów API REST platformy Azure, SDK, programu PowerShell i interfejsu wiersza polecenia. Wersje starsze niż 1 października 2017 r. lub zestawu SDK 2.1 nie obsługują rozszerzonego zestawu ASCII. 

Na przykład Podaj ciąg jako wartość dla rekordu TXT, który ma rozszerzone \128 znaków ASCII. Przykładem jest "abcd\128efgh." Usługa Azure DNS korzysta z tego znaku, który jest 128, wartość bajtu w wewnętrznej reprezentacji. W czasie rozpoznawania nazw DNS ta wartość bajtu jest zwracany w odpowiedzi. Należy również zauważyć, "abc" i "\097\098\099" czy zamienne chodzi rozwiązania jest. 

Wykonamy [ze standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) strefa reguły ucieczki wzorca format pliku dla rekordów TXT. Na przykład `\` teraz faktycznie wszystko, czego zgodnie z RFC specjalne. Jeśli określisz `A\B` jako wartość rekordu TXT, jest reprezentowane i rozpoznane jako `AB`. Jeśli na pewno rekord TXT, aby `A\B` w rozdzielczości, musisz wyjść `\` ponownie. Na przykład określić `A\\B`.

Obecnie ta funkcja jest niedostępna dla rekordy TXT, utworzone w witrynie Azure portal.

## <a name="alias-records"></a>Rekordy aliasu

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Co to są sytuacje, w którym rekordów aliasów są przydatne?

Zobacz scenariusze w temacie [Azure alias DNS rekordów — omówienie](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jakie typy rekordów są obsługiwane dla zestawów rekordów alias?

Zestawy rekordów aliasów są obsługiwane następujące typy rekordów w strefie usługi Azure DNS:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Jakie zasoby są obsługiwane jako elementy docelowe dla aliasu zestawy rekordów?

- **Wskaż publicznego zasobu adresu IP z usługi DNS A/AAAA zestawu rekordów.** Można utworzyć zestawu rekordów A/AAAA i ułatwiają zestawie aliasu rekordu, aby wskazywał publiczny zasób adresu IP.
- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME.** Możesz wskazać CNAME profilu usługi Traffic Manager, z zestawu rekordów CNAME w systemie DNS. Przykładem jest contoso.trafficmanager.net. Teraz możesz też wskazać profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe z rekordu A lub AAAA ustawiana w strefie DNS.
- **Wskaż punktu końcowego usługi Azure Content Delivery Network (CDN)**. Jest to przydatne podczas tworzenia statycznych witryn internetowych przy użyciu usługi Azure storage i Azure CDN.
- **Wskaż inny zestaw rekordów DNS w ramach tej samej strefie.** Alias rekordy mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów DNS CNAME może być aliasem dla innego zestawu rekordów CNAME tego samego typu. To rozwiązanie jest przydatne w przypadku niektórych zestawów rekordów aliasów się i niektórych innych aliasów.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Czy mogę tworzyć i aktualizować alias rekordów w witrynie Azure portal?

Tak. Można utworzyć lub zarządzać rekordów aliasów w witrynie Azure portal, wraz z interfejsów API REST platformy Azure, programu PowerShell, interfejsu wiersza polecenia i zestawy SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Rekordów aliasów pomoże upewnij się, że mojego zestawu rekordów DNS zostanie usunięty po usunięciu podstawowej publiczny adres IP?

Tak. Ta funkcja jest jednym z podstawowych możliwości rekordów aliasów. Ułatwia on uniknięcia potencjalnych przestojów dla użytkowników aplikacji.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Będzie alias rejestruje pomoc, aby upewnić się, że mojego zestawu rekordów DNS zostały zaktualizowane do prawidłowego adresu IP po zmianie podstawowy publiczny adres IP?

Tak. Ta funkcja jest jednym z podstawowych możliwości rekordów aliasów. Pomaga unikać potencjalnych awarii lub zagrożenia dla bezpieczeństwa aplikacji.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Czy istnieją jakieś ograniczenia podczas przy użyciu rekordu aliasu ustawia for A lub AAAA rejestruje, aby wskazywały do usługi Traffic Manager?

Tak. Wskaż profil usługi Traffic Manager jako alias z A lub AAAA zestawu rekordów, usługi Traffic Manager profil korzystać tylko zewnętrzne punkty końcowe. Tworząc zewnętrzne punkty końcowe w usłudze Traffic Manager, podaj rzeczywiste adresy IP punktów końcowych.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Czy istnieje dodatkowe opłaty za używanie rekordów aliasów?

Alias rekordy są kwalifikacji na prawidłowy zestaw rekordów DNS. Nie istnieje żadne dodatkowe opłaty rekordów aliasów.

## <a name="use-azure-dns"></a>Używanie systemu Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Czy mogę współprowadzącym domeny przy użyciu usługi Azure DNS i innego dostawcy DNS?

Tak. Usługa DNS platformy Azure obsługuje wspólnej hosting domeny z innymi usługami DNS.

Aby skonfigurować wspólnej hostingu, należy zmodyfikować rekordy NS dla domeny wskazywał serwery nazw obu dostawców. The name server (NS) records control which providers receive DNS queries for the domain. Możesz zmodyfikować te rekordy NS w usłudze Azure DNS, u innego dostawcy i w strefie nadrzędnej. Strefy nadrzędnej jest zazwyczaj skonfigurowany, za pomocą rejestratora nazw domen. Aby uzyskać więcej informacji na temat delegowania usługi DNS, zobacz [Delegowanie domeny DNS](dns-domain-delegation.md).

Upewnij się również, czy rekordy DNS dla domeny są zsynchronizowane między obu dostawców usługi DNS. Usługa Azure DNS nie obsługuje obecnie transferu strefy DNS. Rekordy DNS muszą być synchronizowane za pomocą [portalu zarządzania usługi Azure DNS](dns-operations-recordsets-portal.md), [interfejsu API REST](https://docs.microsoft.com/powershell/module/az.dns), [SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md), lub [Narzędzie interfejsu wiersza polecenia](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Czy muszę delegować mojej domeny na wszystkich czterech serwerów nazw usługi Azure DNS?

Tak. Usługa system DNS Azure przypisuje czterech serwerów nazw do każdej strefy DNS. Taki układ jest izolacji błędów i zapewnić większą elastyczność. Aby zakwalifikować się do umowy SLA DNS platformy Azure, należy delegować domenę do wszystkich czterech serwerów nazw.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jakie są limity użycia dla usługi Azure DNS?

Następujące domyślne limity mają zastosowanie, gdy używasz usługi Azure DNS.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Strefy DNS platformy Azure można przenosić między grupami zasobów lub między subskrypcjami?

Tak. Strefy DNS można przenosić między grupami zasobów lub subskrypcji.

Gdy przeniesiesz strefy DNS jest nie wpływa na zapytania DNS. Serwery nazw przypisane do strefy pozostają takie same. Zapytania DNS są przetwarzane w zwykły w całym.

Aby uzyskać więcej informacji oraz instrukcje dotyczące przenoszenia stref DNS, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak długo trwa dla zmian systemu DNS zastosować zmiany?

Nowych stref DNS i rekordów DNS zazwyczaj znajdują się w usłudze Azure DNS serwery nazw szybko. Czas jest kilka sekund.

Zmiany w istniejących rekordach DNS może trwać nieco dłużej. Zwykle pojawiają się w usłudze Azure DNS serwery nazw w ciągu 60 sekund. DNS w pamięci podręcznej przez klientów DNS i DNS cyklicznego rozpoznawania nazw poza system DNS Azure również może mieć wpływ na czas. Aby kontrolować ten czas trwania pamięci podręcznej, należy użyć właściwości Time To Live (TTL) każdego zestawu rekordów.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak chronić Moje stref DNS przed przypadkowym usunięciem?

Usługa DNS platformy Azure odbywa się za pomocą usługi Azure Resource Manager. Usługa Azure DNS korzysta z funkcji kontroli dostępu, udostępnianych przez usługi Azure Resource Manager. Kontrola dostępu oparta na rolach określa użytkowników, którzy mają odczytu lub zapisu do strefy DNS i zestawami rekordów. Blokad zasobów uniemożliwiają przypadkowe modyfikacja lub usunięcie strefy DNS i zestawami rekordów.

Aby uzyskać więcej informacji, zobacz [strefy i rekordy DNS chronić](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak skonfigurować rekordy SPF w usłudze Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Czy serwery nazw usługi Azure DNS można rozwiązać za pośrednictwem protokołu IPv6? 

Tak. Serwery nazw w usłudze Azure DNS są podwójnego stosu. Podwójnego stosu oznacza, że mają one IPv4 i IPv6 adresów. Aby znaleźć adres IPv6 serwerów nazw usługi Azure DNS, które są przypisane do strefy DNS, użyj narzędzia takiego jak nslookup. Może to być na przykład `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Jak skonfigurować międzynarodową nazwą domeny w usłudze Azure DNS?

Międzynarodowe nazwy domen (IDN) kodowanie nazwy DNS przy użyciu [punycode](https://en.wikipedia.org/wiki/Punycode). Zapytania DNS są wykonywane za pomocą tych nazw zakodowane w formacie punycode.

Aby skonfigurować międzynarodowych nazw domen w usłudze Azure DNS, należy przekonwertować nazwy strefy lub nazwa zestawu rekordów na ciąg punycode. Usługa Azure DNS nie obsługuje obecnie wbudowanej konwersji do i z punycode.

## <a name="private-dns"></a>Prywatna strefa DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Usługa DNS platformy Azure obsługuje domen prywatnych?

Obsługa domen prywatnych jest zaimplementowana za pomocą funkcji stref prywatnych. Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej. Strefami prywatnymi odbywa się przy użyciu tych samych narzędzi jako strefy DNS platformy Azure dostępnego z Internetu. Są one możliwej do rozpoznania tylko z w ramach określonej sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Przegląd](private-dns-overview.md).

Obecnie stref prywatnych nie są obsługiwane w witrynie Azure portal.

Aby uzyskać informacje na temat innych wewnętrznych DNS na platformie Azure, zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="whats-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Jaka jest różnica między sieć wirtualną rejestracji i sieć wirtualną rozpoznawania w kontekście strefami prywatnymi?

Możesz połączyć sieci wirtualne strefę prywatną DNS jako sieć wirtualną rejestracji lub sieć wirtualną rozpoznawania. W obu przypadkach maszyny wirtualne w sieci wirtualnej pomyślnie rozpoznawania rekordy w strefie prywatnych. Z siecią wirtualną rejestracji rekordy DNS są automatycznie rejestrowane do strefy dla maszyn wirtualnych w sieci wirtualnej. Jeśli maszynę wirtualną rejestracji z sieci wirtualnej zostanie usunięta w odpowiedni rekord DNS z połączonych strefy prywatnej zostanie automatycznie usunięty. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Usługa Azure DNS Private Zones zadziała regionami platformy Azure?

Tak. Private Zones jest obsługiwana dla rozpoznawania nazw DNS między sieciami wirtualnymi w różnych regionach platformy Azure. Strefy prywatne działa nawet bez jawnie komunikacji równorzędnej sieci wirtualnych. Wszystkie sieci wirtualne muszą być określone jako sieciami wirtualnymi rozpoznawania prywatnej strefy. Klienci, może być konieczne sieci wirtualne można równorzędne dla protokołu TCP/HTTP przepływ ruchu z jednego regionu do innego.

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Jest łączność z Internetem z wymagane w przypadku stref prywatnych sieci wirtualnych?

Nie. Prywatnych stref pracy wraz z sieciami wirtualnymi. Klienci ich używać do zarządzania domenami dla maszyn wirtualnych lub innych zasobów w ramach i między sieciami wirtualnymi. Łączność z Internetem nie jest wymagana do rozpoznawania nazw. 

### <a name="can-the-same-private-zone-be-used-for-several-virtual-networks-for-resolution"></a>Można użyć tej samej prywatnej strefy dla wielu sieci wirtualnych rozpoznawania?

Tak. Klientów można skojarzyć maksymalnie 10 sieciami wirtualnymi rozpoznawania ze strefą prywatną jednego.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Sieć wirtualną należącą do innej subskrypcji dodaniem jako sieć wirtualną rozpoznawania do prywatnej strefy?

Tak. Musi mieć uprawnienia do operacji zapisu w sieci wirtualnych i prywatnej strefy DNS. Do wielu ról RBAC można udzielić uprawnienia do zapisu. Na przykład rola klasycznego RBAC Współautor sieci ma uprawnienia do zapisu w sieciach wirtualnych. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md).

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Rekordy DNS maszyny wirtualnej automatycznie zarejestrowane w prywatnej strefy automatycznie usunie usunięcie maszyn wirtualnych przez klienta?

Tak. Jeśli usuniesz maszynę wirtualną w ramach sieci wirtualnych rejestracji, rekordy DNS, które zostały zarejestrowane do strefy są automatycznie usuwane. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Rekord maszyny wirtualnej automatycznie zarejestrowane w prywatnej strefy z sieci wirtualnej rejestracji można usunąć ręcznie?

Nie. Rekordy DNS maszyny wirtualnej, które są automatycznie rejestrowane w prywatnej strefy z sieci wirtualnej rejestracji nie są widoczne lub nie można edytować przez klientów. Może spowodować zastąpienie automatycznie zarejestrowanych rekordów DNS przy użyciu ręcznie utworzonego rekordu DNS w strefie. Poniższe pytania i odpowiedzi dotyczą w tym temacie.

### <a name="what-happens-when-we-try-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Co się stanie, gdy podejmowane są próby ręcznie utworzyć nowego rekordu DNS do strefy prywatnej, o tej samej nazwy hosta jako automatycznie zarejestrowane istniejącej maszyny wirtualnej w sieci wirtualnej rejestracji?

Spróbuj ręcznie utworzyć nowego rekordu DNS do strefy prywatnej, o tej samej nazwy hosta jako maszyna wirtualna istniejących, automatycznie zarejestrowane w sieci wirtualnej rejestracji. Po wykonaniu nowego rekordu DNS zastępuje rekord automatycznie zarejestrowane maszyny wirtualnej. Jeśli spróbujesz ponownie usunąć to ręczne tworzenie rekordu DNS w strefie, Usuń zakończy się pomyślnie. Automatyczna rejestracja odbywa się ponownie, tak długo, jak maszyna wirtualna nadal istnieje i ma dołączoną prywatny adres IP. Rekord DNS jest ponownie tworzone automatycznie w strefie.

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-will-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-too"></a>Co się stanie, gdy firma Microsoft Odłącz sieć wirtualną rejestracji z prywatnej strefy? Zostaną rekordy automatycznie zarejestrowane maszyny wirtualnej z sieci wirtualnej usunięte ze strefy zbyt?

Tak. Aby odłączyć ze strefą prywatną sieć wirtualną rejestracji, należy zaktualizować strefę DNS, aby usunąć skojarzone sieć wirtualną rejestracji. W ramach tego procesu rekordy maszyn wirtualnych, które zostały automatycznie rejestrowane są usuwane ze strefy. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-thats-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Co się stanie, gdy usuwamy rejestracji lub rozpoznawania sieci wirtualnej, która jest połączona z prywatnej strefy? Czy mamy ręcznie zaktualizować prywatnej strefy można odłączyć sieci wirtualnej jako rejestracja lub sieć wirtualną rozpoznawania ze strefy?

Tak. Po usunięciu sieć wirtualną rejestracji lub rozpoznawania bez najpierw odłączanie ze strefą prywatną operacja usuwania powiedzie się. Jednak sieci wirtualnej nie jest automatycznie odłączone z prywatnej strefy, jeśli istnieje. Należy ręcznie Odłącz sieć wirtualną z prywatnej strefy. Z tego powodu Odłącz sieć wirtualną z prywatnej strefy, zanim usuniesz je.

### <a name="will-dns-resolution-by-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-privatecontosocom-is-linked-to-a-virtual-network"></a>Rozpoznawanie nazw DNS przy użyciu domyślnej nazwy FQDN (internal.cloudapp.net) wciąż działają nawet wtedy, gdy strefę prywatną (na przykład private.contoso.com) jest połączona z siecią wirtualną?

Tak. Strefy prywatne w usłudze nie zastępuje domyślne rozwiązania DNS przy użyciu strefy internal.cloudapp.net platformy Azure. Jest dostępna jako dodatkową funkcję lub ulepszenie. Czy możesz polegać na internal.cloudapp.net platformy Azure lub w prywatnej strefy, użyj nazwy FQDN strefy, którą chcesz usunąć względem. 

### <a name="will-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Sufiks DNS na maszynach wirtualnych w ramach połączonych sieci wirtualnej zostanie zmieniona na z prywatnej strefy?

Nie. Sufiks DNS na maszynach wirtualnych w sieci wirtualnej połączonej pozostaje jako domyślny sufiks platformy Azure ("*. internal.cloudapp.net"). Można ręcznie zmienić ten sufiks DNS na maszynach wirtualnych, na który strefa prywatna. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Czy istnieją jakiekolwiek ograniczenia w przypadku stref prywatnych w tej wersji zapoznawczej?

Tak. W publicznej wersji zapoznawczej istnieją następujące ograniczenia.
* Jedną sieć wirtualną rejestracji na strefę prywatną.
* Maksymalnie 10 sieciami wirtualnymi rozpoznawania na strefę prywatną.
* A udzielone tylko jedną strefę prywatnego jako sieć wirtualną rejestracji łączy sieci wirtualnej.
* A podanych linków sieci wirtualnej do maksymalnie 10 stref prywatnych jako sieć wirtualną rozpoznawania.
* Jeżeli określono sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do prywatnej strefy nie można wyświetlić lub pobrać z programu PowerShell, interfejsu wiersza polecenia lub interfejsów API. Rekordy maszyny Wirtualnej są zarejestrowane i rozwiązać pomyślnie.
* Odwrotnego DNS działa tylko w przypadku prywatnej przestrzeni adresów IP w sieci wirtualnej rejestracji.
* Odwrotnym systemem DNS, aby uzyskać prywatny adres IP, który nie jest zarejestrowany w prywatnej strefy zwraca "internal.cloudapp.net" jako sufiks DNS. Ten sufiks nelze rozpoznat. Przykładem jest prywatny adres IP dla maszyny wirtualnej w sieci wirtualnej, która zostanie połączona jako sieć wirtualną rozpoznawania na strefę prywatną.
* Sieć wirtualna musi być pusty, gdy łączy się on po raz pierwszy prywatnej strefy jako sieć wirtualną rejestracji lub rozpoznawania. Następnie sieci wirtualnej może być pusty dla przyszłych połączeń jako sieć wirtualną rejestracji lub rozpoznawania do innych stref prywatnych.
* Warunkowego przesyłania dalej nie jest obsługiwane, na przykład, aby włączyć rozpoznawanie między sieciami Azure i lokalnie. Dowiedz się, jak klienci mogą weź pod uwagę, w tym scenariuszu za pośrednictwem innych mechanizmów. Zobacz [rozpoznawanie nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Czy istnieją wszystkie przydziały i limity strefy lub rekordów w przypadku stref prywatnych?

Nie ma ograniczeń liczby stref dozwolone na subskrypcję w przypadku stref prywatnych. Nie ma ograniczeń liczby zestawów rekordów dla strefy dla stref prywatnych. Stref prywatnych i publicznych wliczają się do ogólnej limity DNS. Aby uzyskać więcej informacji, zobacz [limity usług i subskrypcji platformy Azure](../azure-subscription-service-limits.md#azure-dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Czy istnieje portalu pomocy technicznej w przypadku stref prywatnych?

Strefy prywatne, które zostały już utworzone za pomocą interfejsów API, PowerShell, interfejsu wiersza polecenia i zestawy SDK są widoczne w witrynie Azure portal. Jednak klienci nie można utworzyć nowych stref prywatnych ani zarządzać skojarzenia z sieciami wirtualnymi. Dla sieci wirtualnych skojarzone jako sieci wirtualne rejestracji automatycznie zarejestrowanych rekordów maszyny Wirtualnej nie są widoczne w portalu. 

## <a name="next-steps"></a>Kolejne kroki

- [Dowiedz się więcej o usłudze Azure DNS](dns-overview.md).

- [Dowiedz się więcej o tym, jak używać usługi Azure DNS dla domen prywatnych](private-dns-overview.md).

- [Dowiedz się więcej na temat stref DNS i rekordów](dns-zones-records.md).

- [Rozpoczynanie pracy z usługą Azure DNS](dns-getstarted-portal.md).
