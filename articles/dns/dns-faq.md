---
title: Usługa DNS platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure DNS
services: dns
documentationcenter: na
author: vhorne
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 66e04e7f0b272f19788e79805ef06d11e2eda572
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948030"
---
# <a name="azure-dns-faq"></a>Usługa DNS platformy Azure — często zadawane pytania

## <a name="about-azure-dns"></a>O usłudze Azure DNS

### <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

System nazw domen lub DNS, odpowiada za tłumaczenia (lub rozpoznawanie) nazwę witryny sieci Web lub usługi na jej adres IP. Azure DNS to usługa hostingowa przeznaczona dla domen DNS, która umożliwia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

W usłudze Azure DNS domeny DNS są hostowane w globalnej sieci serwerów nazw DNS na platformie Azure. Ta metoda korzysta najbliższych tak, aby każde zapytanie DNS jest odbierane przez najbliższego dostępnego serwera DNS. Usługa DNS platformy Azure zapewnia wysoką wydajność i wysoką dostępność domeny.

Usługa Azure DNS jest oparta na usłudze Azure Resource Manager. W efekcie firma korzysta z zalet funkcji Menedżera zasobów, takich jak kontrola dostępu oparta na rolach, dzienniki inspekcji i blokowanie zasobów. Twoich domen i rekordów mogą być zarządzane za pośrednictwem witryny Azure portal, poleceń cmdlet programu Azure PowerShell i wiersza polecenia platformy Azure dla wielu platform. Aplikacji wymagających automatycznego zarządzania usługą DNS można zintegrować z usługą za pomocą interfejsu API REST i zestawów SDK.

### <a name="how-much-does-azure-dns-cost"></a>Ile kosztuje usługa Azure DNS

Model rozliczeń za usługę Azure DNS zależy od liczby stref DNS hostowanych w usłudze Azure DNS i liczby zapytań DNS, które otrzyma. Rabaty znajdują się na podstawie użycia.

Aby uzyskać więcej informacji, zobacz [stronę z cennikiem usługi system DNS Azure](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaka jest umowa SLA dla usługi Azure DNS?

Platforma Azure gwarantuje, że prawidłowe zapytania DNS otrzyma odpowiedź z co najmniej jeden serwer nazw DNS platformy Azure co najmniej 99,99% czasu.

Aby uzyskać więcej informacji, zobacz [strony umowy SLA platformy Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co to jest "strefa DNS"? Czy to jest to samo co „domena DNS”? 

Domena jest unikatową nazwą w systemie nazw domen, na przykład "contoso.com".


Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Na przykład domena "contoso.com" może zawierać wiele rekordów DNS, takich jak "mail.contoso.com" (dla serwera poczty) i "www.contoso.com" (dla witryny sieci web). Te rekordy będzie hostowane w strefie DNS "contoso.com".

Nazwa domeny jest *tylko nazwa*, podczas gdy strefa DNS jest zasobem danych zawierającego rekordy DNS dla nazwy domeny. Usługa Azure DNS umożliwia hostowanie strefy DNS i zarządzanie rekordami DNS dla domeny na platformie Azure. Umożliwia także serwery DNS do odpowiadania na kwerendy DNS z Internetu.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Czy muszę zakupić nazwę domeny DNS, aby używać usługi System DNS Azure? 

Niekoniecznie.

Nie trzeba kupować domeny, aby hostować strefę DNS w usłudze System DNS Azure. Strefę DNS można utworzyć w dowolnej chwili bez konieczności posiadania nazwy domeny. Zapytania DNS dla tej strefy rozpozna tylko, jeśli są kierowane do serwerów nazw usługi Azure DNS, które są przypisane do strefy.

Należy zakupić nazwę domeny, jeśli chcesz połączyć swoją strefę DNS z globalną hierarchią systemu — to połączenie umożliwia zapytania DNS z dowolnego miejsca na świecie, aby odnaleźć daną strefę DNS i udzielić odpowiedzi z rekordami systemu DNS.

## <a name="azure-dns-features"></a>Funkcje usługi Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Czy istnieją jakieś ograniczenia w przypadku używania rekordów aliasów dla wierzchołku nazwy domeny usługi Traffic Manager?

Tak. Statyczne publiczne adresy IP należy użyć z usługą Traffic Manager. Konfigurowanie **zewnętrzny punkt końcowy** przy użyciu statycznego adresu IP. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Usługa Azure DNS obsługuje trybu failover routingu lub punktu końcowego ruchu oparte na systemie DNS?

Tryb failover routingu i punktu końcowego ruchu oparte na systemie DNS są dostarczane przez usługę Azure Traffic Manager. Usługa Azure Traffic Manager jest osobną usługą platformy Azure, który może być używane razem z usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Traffic Manager — omówienie](../traffic-manager/traffic-manager-overview.md).

Usługa DNS platformy Azure obsługuje tylko hostowanie domen DNS "static", w którym każde zapytanie DNS dla danego rekordu DNS zawsze będzie otrzymywał tę samą odpowiedź DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Usługa DNS platformy Azure obsługuje rejestracji nazwy jej domeny?

Nie. Usługa Azure DNS nie obsługuje aktualnie możliwości zakupu nazw domen. Aby zakupić domenę, należy użyć Rejestratora nazw domen innej firmy. Rejestrator zwykle opłaty za niewielką opłatą roczną. Następnie domeny mogą być hostowane w usłudze Azure DNS do zarządzania rekordami DNS. Aby uzyskać szczegółowe informacje, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

Kup domeny jest funkcją jest śledzony w zaległości platformy Azure. Można użyć witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Usługa Azure DNS obsługuje rozszerzenia DNSSEC?

Nie. Usługa Azure DNS aktualnie nie obsługuje rozszerzeń DNSSEC.

Rozszerzenia DNSSEC, to funkcja śledzony na liście prac w usłudze Azure DNS. Można użyć witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Usługa DNS platformy Azure obsługuje transfery strefy (AXFR/IXFR)?

Nie. Usługa Azure DNS nie obsługuje obecnie transferów stref. Strefy DNS mogą być [importowane do usługi Azure DNS przy użyciu wiersza polecenia platformy Azure](dns-import-export.md). Rekordy DNS mogą być zarządzane za pośrednictwem [portalu zarządzania usługi Azure DNS](dns-operations-recordsets-portal.md), nasze [interfejsu API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md), lub [Narzędzie interfejsu wiersza polecenia](dns-operations-recordsets-cli.md).

Transfer strefy jest funkcją, jest śledzony na liście prac w usłudze Azure DNS. Można użyć witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Usługa Azure DNS obsługuje adres URL przekierowania?

Nie. Adres URL przekierowania usług nie są faktycznie usługi DNS — działają na poziomie HTTP, a nie na poziomie usługi DNS. Niektórzy dostawcy DNS pakietów adresu URL przekierowania usługi w ramach swojej oferty ogólne. To nie jest obecnie obsługiwane przez usługę Azure DNS.

Funkcja Przekierowywanie adresu URL jest śledzony na liście prac w usłudze Azure DNS. Można użyć witrynę opinii do [zarejestrować działu pomocy technicznej dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset"></a>Usługa Azure DNS obsługuje rozszerzone ASCII (8-bitowa) zestawu dla zestawu rekordów TXT kodowania?

Tak. Usługa DNS platformy Azure obsługuje rozszerzonego zestawu kodowanie ASCII dla zestawów rekordów TXT, jeśli używasz najnowszej wersji interfejsów API REST platformy Azure, zestawy SDK, program PowerShell i interfejsu wiersza polecenia (wersje starsze niż 2017-10-01 lub czy SDK 2.1 obsługuje rozszerzonego zestawu ASCII). Na przykład, jeśli użytkownik udostępnia ciąg jako wartość dla rekordu TXT, który ma rozszerzone \128 znaków ASCII (na przykład: "abcd\128efgh"), system DNS Azure użyje wartości bajtu tego znaku (czyli 128) w wewnętrznej reprezentacji. W czasie rozpoznawania nazw DNS także tę wartość bajtu zostaną zwrócone w odpowiedzi. Należy również zauważyć, "abc" i "\097\098\099" czy zamienne chodzi rozwiązania jest. 

Wykonamy [ze standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) strefa reguły ucieczki wzorca format pliku dla rekordów TXT. Na przykład "\" teraz faktycznie wszystko, czego zgodnie z RFC specjalne. Jeśli określisz "A\B." jako wartości rekordu TXT będą reprezentowane i rozwiązać jak właśnie "AB". Jeśli naprawdę rekord TXT mieć "A\B.", rozdzielczością należy jako znak ucieczki "\" ponownie, tj. Określ jako "A\\B". 

Ta funkcja jest obecnie dostępna dla rekordów TXT utworzone w witrynie Azure portal. 

## <a name="alias-records"></a>Rekordów aliasów

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Co to są sytuacje, w którym rekordów aliasów są przydatne?
Zobacz scenariusze w temacie [Azure alias DNS rekordów — omówienie](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jakie typy rekordów są obsługiwane dla zestawów rekordów alias?
Alias zestawów rekordów są obsługiwane w przypadku następujących typów rekordów w strefie usługi Azure DNS: A i AAAA, CNAME. 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Jakie zasoby są obsługiwane jako elementy docelowe dla aliasu zestawy rekordów?
- **Wskazywać na zasób publicznego adresu IP z usługi DNS zestawu rekordów A/AAAA**. Tworzenie zestawu rekordów A/AAAA i ułatwiają alias zestawu rekordów, aby wskazywać na zasób publicznego adresu IP.
- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME**. Oprócz możliwości, aby wskazywał CNAME profilu usługi Traffic Manager (na przykład: contoso.trafficmanager.net) z zestawu rekordów CNAME w systemie DNS, można teraz również wskazać profilu usługi Traffic Manager, który ma zewnętrzne punkty końcowe z zestawu rekordów A lub AAAA w systemie DNS strefa.
- **Wskaż inny rekordów DNS w ramach tej samej strefie**. Alias rekordy mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład masz zestaw rekordów CNAME w systemie DNS jest aliasem innej rekordów CNAME tego samego typu. Jest to przydatne, jeśli chcesz mieć niektóre zestawy rekordów można aliasów, a niektóre jako innych aliasów pod kątem zachowania.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Czy mogę tworzyć i aktualizować alias rekordów w witrynie Azure portal?
Tak. Rekordów aliasów mogą być tworzone lub zarządzane w witrynie Azure portal, oprócz interfejsów API REST platformy Azure, programu Azure PowerShell, interfejsu wiersza polecenia i zestawy SDK.

### <a name="will-alias-records-help-ensure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Rekordów aliasów pomoże upewnij się, że mojego zestawu rekordów DNS zostanie usunięty po usunięciu podstawowej publiczny adres IP?
Tak. W rzeczywistości jest to jedna z podstawowymi możliwościami rekordów aliasów. Ułatwiają one uniknięcia potencjalnych przestojów dla użytkowników końcowych w aplikacji.

### <a name="will-alias-records-help-ensure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Alias rekordów pomoże upewnij się, że mojego zestawu rekordów DNS zostały zaktualizowane do prawidłowego adresu IP po zmianie podstawowy publiczny adres IP?
Tak. Jak poprzednie pytanie to jest jednym z podstawowych możliwości rekordów aliasów i pomaga uniknąć potencjalnych awarii lub zagrożenia dla bezpieczeństwa aplikacji.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-an-a-or-aaaa-records-to-point-to-traffic-manager"></a>Czy istnieją jakieś ograniczenia w przypadku zestawów za pomocą rekordu aliasu dla rekordów A lub AAAA wskazywały do usługi Traffic Manager?
Tak. Jeśli chcesz wskazać profilu usługi Traffic Manager jako alias z zestaw rekordów A lub AAAA, upewnij się, że profil usługi Traffic Manager używa tylko zewnętrzne punkty końcowe. Tworząc zewnętrzne punkty końcowe w usłudze Traffic Manager, upewnij się, że zapewnia rzeczywistego adresy IP punktów końcowych.

### <a name="is-there-an-additional-charge-for-using-alias-records"></a>Czy istnieje dodatkowa opłata za pomocą aliasu rekordów?
Rekordów aliasów są kwalifikacji na prawidłowe rekordów DNS, a nie ma żadnych dodatkowych rozliczeń dla rekordów aliasów.

## <a name="using-azure-dns"></a>Za pomocą usługi Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Czy mogę współprowadzącym domeny przy użyciu usługi Azure DNS i innego dostawcy DNS?

Tak. Usługa DNS platformy Azure obsługuje wspólnej hosting domeny z innymi usługami DNS.

Aby to zrobić, należy zmodyfikować rekordy NS w domenie, (które określają, w których dostawców otrzymywać DNS wysyła zapytanie do domeny) wskaż serwery nazw obu dostawców. Możesz zmodyfikować te rekordy NS w trzech miejscach: w usłudze Azure DNS, u innego dostawcy i w strefie nadrzędnej (zwykle konfigurowane za pomocą rejestratora nazw domen). Aby uzyskać więcej informacji na temat delegowania usługi DNS, zobacz [Delegowanie domeny DNS](dns-domain-delegation.md).

Ponadto należy się upewnić, że rekordy DNS dla domeny są zsynchronizowane między obu dostawców usługi DNS. Usługa Azure DNS nie obsługuje obecnie transferu strefy DNS. Rekordy DNS muszą być zsynchronizowane za pomocą [portalu zarządzania usługi Azure DNS](dns-operations-recordsets-portal.md), [interfejsu API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md), lub [Narzędzie interfejsu wiersza polecenia](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Czy muszę delegować mojej domeny na wszystkich czterech serwerów nazw usługi Azure DNS?

Tak. Usługa system DNS Azure przypisuje czterech serwerów nazw do każdej strefy DNS izolacji błędów i zapewnić większą elastyczność. Aby zakwalifikować się do umowy SLA DNS platformy Azure, należy delegować domenę do wszystkich czterech serwerów nazw.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jakie są limity użycia dla usługi Azure DNS?

Następujące limity domyślne są stosowane podczas korzystania z usługi Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Strefy DNS platformy Azure można przenosić między grupami zasobów lub między subskrypcjami?

Tak. Strefy DNS można przenosić między grupami zasobów lub subskrypcji.

Ma to żadnego wpływu na zapytania DNS, podczas przenoszenia strefę DNS. Serwery nazw przypisane do strefy pozostają takie same, a zapytania DNS są przetwarzane w zwykły w całym.

Aby uzyskać więcej informacji oraz instrukcje dotyczące przenoszenia stref DNS, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak długo trwa dla zmian systemu DNS zastosować zmiany?

Nowych stref DNS i rekordów DNS są zazwyczaj odzwierciedlane na serwerach nazw usługi system DNS Azure szybko, w ciągu kilku sekund.

Zmiany w istniejących rekordach DNS może trwać nieco dłużej, ale nadal powinno zostać odzwierciedlone na serwerach nazw usługi system DNS Azure w ciągu 60 sekund. W tym przypadku DNS buforowania poza usługi Azure DNS (przez klientów DNS i rozpoznawania nazw DNS cyklicznego) może także wpłynąć na czas potrzebny do zmiany DNS zaczęła obowiązywać. Ten czas trwania pamięci podręcznej mogą być kontrolowane za pomocą właściwości Time To Live (TTL) każdego zestawu rekordów.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak chronić Moje stref DNS przed przypadkowym usunięciem?

Usługa DNS platformy Azure odbywa się przy użyciu usługi Azure Resource Manager i korzyści z kontroli dostępu do funkcji tej usługi Azure Resource Manager zapewnia. Kontrola dostępu oparta na rolach może służyć do kontrolowania użytkowników, którzy mają odczytu lub zapisu do strefy DNS i zestawów rekordów. Aby zapobiec przypadkowemu modyfikacja lub usunięcie strefy DNS i zestawów rekordów można zastosować blokad zasobów.

Aby uzyskać więcej informacji, zobacz [ochrona stref i rekordów DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak skonfigurować rekordy SPF w usłudze Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Czy serwery nazw w usłudze Azure DNS można rozwiązać za pośrednictwem protokołu IPv6? 

Tak. Usługa Azure DNS serwery nazw są obsługującej podwójny stos (mają adresy IPv4 i IPv6). Aby znaleźć adres IPv6 dla usługi Azure DNS serwery nazw przypisane do strefy DNS, można użyć narzędzia, takiego jak nslookup (na przykład `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Jak ustawić się międzynarodowych nazw domen (IDN) w usłudze Azure DNS?

Międzynarodowe nazwy domen (IDN) pracy kodowania, w których używane są nazwy DNS "[punycode](https://en.wikipedia.org/wiki/Punycode)". Zapytania DNS są wykonywane przy użyciu tych nazw zakodowane w formacie punycode.

Międzynarodowe nazwy domen (IDN) można skonfigurować w usłudze Azure DNS, konwertując pierwszą nazwę strefy, lub nazwą zestawu rekordów do punycode. Usługa Azure DNS nie obsługuje obecnie wbudowanej konwersji do i z niej punycode.

## <a name="private-dns"></a>Prywatna strefa DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Usługa Azure DNS obsługuje domen "private"?
Obsługa domen "private" jest implementowane za pomocą funkcji stref prywatnych.  Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej.  Strefy prywatne są zarządzane jako strefy DNS platformy Azure dostępnego z Internetu przy użyciu tych samych narzędzi, ale są one tylko można ją rozpoznać z w ramach określonej sieci wirtualnej.  Zobacz [Przegląd](private-dns-overview.md) Aby uzyskać szczegółowe informacje.

W tej chwili stref prywatnych nie są obsługiwane w witrynie Azure portal. 

Aby uzyskać informacje na temat innych wewnętrznych DNS na platformie Azure, zobacz [rozpoznawania nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Jaka jest różnica między sieć wirtualną rejestracji i sieć wirtualną rozpoznawania w kontekście strefami prywatnymi? 
Można połączyć sieci wirtualne prywatnej strefy DNS na dwa sposoby — jako sieć wirtualną rejestracji lub sieć wirtualną rozpoznawania. W obu przypadkach maszyny wirtualne w sieci wirtualnej będzie można pomyślnie rozpoznawania rekordy w strefie prywatnych. Jednak jeśli określona sieć wirtualna jako sieć wirtualną rejestracji, platformy Azure są automatycznie rejestrowane rekordów DNS (dynamiczną rejestrację) do strefy dla maszyn wirtualnych w sieci wirtualnej. Ponadto podczas maszynę wirtualną podczas rejestracji, zostaje usunięty sieci wirtualnej Azure również automatycznie usunie odpowiedni rekord DNS z połączonych prywatnej strefy. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Usługa Azure DNS Private Zones zadziała regionami platformy Azure?
Tak. Private Zones jest obsługiwana dla rozpoznawania nazw DNS między sieciami wirtualnymi w różnych regionach platformy Azure, nawet bez jawnie tak długo, jak sieci wirtualne są określane jako sieciami wirtualnymi rozpoznawania dla strefy prywatnej komunikacji równorzędnej sieci wirtualnych. Klienci mogą muszą sieci wirtualne można równorzędne dla protokołu TCP/HTTP przepływ ruchu z jednego regionu do innego. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Jest łączność z Internetem z sieci wirtualne wymagane dla stref prywatnych?
Nie. Strefy prywatne działają w połączeniu z sieciami wirtualnymi i umożliwianie klientom zarządzanie domenami dla maszyn wirtualnych lub innych zasobów w ramach i między sieciami wirtualnymi. Nie łączności z Internetem jest wymagany do rozpoznawania nazw. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Może służyć do wielu sieci wirtualnych rozpoznawania tej samej strefy prywatnej? 
Tak. Klientów można skojarzyć maksymalnie 10 sieciami wirtualnymi rozpoznawania ze strefą prywatną jednego.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Sieć wirtualną należącą do innej subskrypcji dodaniem jako sieć wirtualną rozpoznawania do stref prywatnych? 
Tak, jak długo użytkownik ma uprawnienia do zapisu operacji na obydwu sieci wirtualnych, a także strefy prywatnej DNS. Uprawnienie do zapisu może być przydzielona do wielu ról RBAC. Na przykład rola klasycznego RBAC Współautor sieci ma uprawnienia do zapisu w sieciach wirtualnych. Aby uzyskać więcej informacji na temat ról RBAC, zobacz [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Rekordy DNS maszyny wirtualnej automatycznie zarejestrowane w prywatnej strefy automatycznie usunie usunięcie maszyn wirtualnych przez klienta?
Tak. Jeśli usuniesz maszynę wirtualną w ramach sieci wirtualnych rejestracji, firma Microsoft automatycznie spowoduje usunięcie rekordów DNS, które zostały zarejestrowane do strefy ze względu na tym, że sieć wirtualną rejestracji. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Rekord maszyny wirtualnej automatycznie zarejestrowane w prywatnej strefy (z sieci wirtualnej rejestracji) można usunąć ręcznie? 
Nie. W tej chwili rekordy DNS maszyny wirtualnej, które są automatycznie rejestrowane w prywatnej strefy z sieci wirtualnej rejestracji nie są widoczne lub edycji przez klientów. Można jednak zastąpić (Zastąp) takich automatycznie zarejestrowanych rekordów DNS przy użyciu systemu DNS utworzonych ręcznie zarejestrować w strefie. Zobacz następujące pytanie i odpowiedź, która rozwiązuje ten problem.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Co się stanie w przypadku próby ręcznego utworzenia nowego rekordu DNS do prywatnej strefy o tej samej nazwy hosta jako (automatycznie zarejestrowane) istniejącej maszyny wirtualnej w sieci wirtualnej rejestracji? 
Jeśli spróbujesz ręcznie utworzyć nowego rekordu DNS do strefy prywatnej, o tej samej nazwy hosta jako istniejącej maszyny wirtualnej (automatycznie zarejestrowane) w sieci wirtualnej rejestracji umożliwi możemy nowego rekordu DNS zastąpić automatycznie zarejestrowane rekord maszyny wirtualnej. Ponadto Jeśli spróbujesz później usunąć ten utworzone ręcznie rekord DNS w strefie, Usuń zakończy się powodzeniem i automatycznej rejestracji nastąpi ponownie (rekord DNS będzie ponownie tworzone automatycznie w strefie) tak długo, jak długo maszyny wirtualnej nadal istnieje i ma prywatny adres IP dołączony do niego. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone-would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Co się stanie, gdy firma Microsoft Odłącz sieć wirtualną rejestracji z prywatnej strefy? Ze strefy, jak również zostaną usunięte rekordy automatycznie zarejestrowane maszyny wirtualnej z sieci wirtualnej?
Tak. Sieć wirtualna rejestracji (Zaktualizuj strefę DNS, aby usunąć sieć wirtualną rejestracji skojarzony) Odłącz się od prywatnej strefy, Azure usunie wszystkie rekordy automatycznie zarejestrowane maszyny wirtualnej ze strefy. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone-do-we-have-to-manually-update-the-private-zone-to-unlink-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Co się stanie, gdy usuwamy rejestracji (bądź rozpoznawanie nazw) sieci wirtualnej, która jest połączona z prywatnej strefy? Czy mamy ręcznie zaktualizować prywatnej strefy można odłączyć sieci wirtualnej sieci wirtualnej co rejestracji (bądź rozpoznawanie nazw) ze strefy?
Tak. Po usunięciu sieć wirtualną rejestracji (bądź rozpoznawanie nazw) bez odłączenie go z prywatnej strefy najpierw, Azure umożliwi operacja usuwania została wykonana pomyślnie, ale sieć wirtualna nie jest automatycznie niepołączone z prywatnej strefy ewentualne. Musisz ręcznie Odłącz sieć wirtualną z prywatnej strefy. Z tego powodu zaleca się najpierw odłączyć sieci wirtualnej z prywatnej strefy przed jego usunięciem.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>Czy rozpoznawanie nazw DNS przy użyciu domyślnego FQDN (internal.cloudapp.net) nadal działać, nawet w przypadku stref prywatnych (na przykład: contoso.local) jest połączona z siecią wirtualną? 
Tak. Funkcja stref prywatnych nie zastępuje domyślne rozwiązania DNS przy użyciu strefy internal.cloudapp.net platformy Azure i jest oferowana jako dodatkowe możliwości lub rozszerzenie. Dla obu przypadków (czy polegania na internal.cloudapp.net platformy Azure lub na strefy prywatne), zalecane jest używana nazwa FQDN strefy mają być rozpoznane względem. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Aby w przypadku stref prywatnych zostaną zmienione sufiks DNS na maszynach wirtualnych w ramach połączonych sieci wirtualnej? 
Nie. W tej chwili sufiks DNS na maszynach wirtualnych w sieci wirtualnej połączonej pozostanie jako domyślny sufiks platformy Azure ("*. internal.cloudapp.net"). Można jednak ręcznie zmienić ten sufiks DNS na maszynach wirtualnych, na który prywatnej strefy. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Czy istnieją jakiekolwiek ograniczenia w przypadku stref prywatnych w tej wersji zapoznawczej?
Tak. W publicznej wersji zapoznawczej istnieją następujące ograniczenia:
* Jednej sieci wirtualnych rejestracji na stref prywatnych
* Sieci wirtualne maksymalnie 10 rozpoznawanie poszczególnych stref prywatnych
* Danej sieci wirtualnej mogą być łączone tylko z jednej prywatnej strefy jako sieć wirtualną rejestracji
* Danej sieci wirtualnej mogą być połączone z maksymalnie 10 Private Zones jako sieć wirtualną rozpoznawania
* Jeżeli określono sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do stref prywatnych nie będą widoczne lub pobieranie za pomocą programu Powershell/interfejsu wiersza polecenia/interfejsów API, ale rekordy maszyny Wirtualnej w rzeczywistości są zarejestrowane i zostanie rozwiązany pomyślnie
* Odwrotnym systemem DNS będzie działać tylko za miejsce do prywatnego adresu IP w sieci wirtualnej rejestracji
* Odwrotne DNS dla prywatnego adresu IP, który nie jest zarejestrowany w strefie prywatny (na przykład: prywatny adres IP dla maszyny wirtualnej w sieci wirtualnej, która zostanie połączona jako sieć wirtualną rozpoznawania do strefy prywatnej) zwróci "internal.cloudapp.net" jako sufiks DNS, jednak Ten sufiks, nie będzie można rozpoznać.   
* Sieć wirtualna musi być pusta (tj.) Brak maszyn wirtualnych przy użyciu z kartą Sieciową dołączoną) podczas początkowego (tj.) po raz pierwszy) łączenie strefę prywatną co sieć wirtualna rejestracji lub rozpoznawania. Jednak sieci wirtualnej następnie może być pusty dla przyszłych połączeń jako rejestracji lub rozpoznawania sieci wirtualnej, do innych stref prywatnych. 
* W tej chwili warunkowego przesyłania dalej nie jest obsługiwana, na przykład dotyczące włączania rozwiązania między sieciami Azure i lokalnego. Dokumentację, jak klienci mogą weź pod uwagę, w tym scenariuszu za pośrednictwem innych mechanizmów, zobacz [rozpoznawania nazw dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Istnieją limity przydziału ani limitów stref i rekordów dla strefy prywatne w usłudze?
Istnieją osobne nieograniczoną liczbę stref dozwolone na subskrypcję lub liczba zestawów rekordów dla strefy dla stref prywatnych. Zarówno publiczne i prywatne strefy są wliczane do ogólnej limity DNS zgodnie z opisem [tutaj](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Czy istnieje portalu pomocy technicznej w przypadku stref prywatnych?
Strefy prywatne, które zostały już utworzone za pomocą mechanizmów-Portal (/ PowerShell/interfejsu wiersza polecenia/zestawami SDK interfejsu API) będą widoczne w witrynie Azure portal, ale klienci nie będzie w stanie do tworzenia nowych stref prywatnych lub zarządzania skojarzenia z sieciami wirtualnymi. Ponadto dla sieci wirtualnych skojarzone jako sieci wirtualne rejestracji, automatycznie zarejestrowanych rekordów maszyn wirtualnych nie będą widoczne w portalu. 

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o usłudze Azure DNS](dns-overview.md)
<br>
[Dowiedz się więcej o korzystaniu z usługi Azure DNS dla domen prywatnych](private-dns-overview.md)
<br>
[Dowiedz się więcej na temat stref i rekordów DNS](dns-zones-records.md)
<br>
[Rozpoczynanie pracy z usługą Azure DNS](dns-getstarted-portal.md)

