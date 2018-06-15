---
title: Usługa DNS platformy Azure — często zadawane pytania | Dokumentacja firmy Microsoft
description: Często zadawane pytania dotyczące usługi Azure DNS
services: dns
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: kumud
ms.openlocfilehash: e0eb39ced1d88d2e0b6128493304f112f9c685fa
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31395234"
---
# <a name="azure-dns-faq"></a>Usługa DNS platformy Azure — często zadawane pytania

## <a name="about-azure-dns"></a>Usługa DNS platformy Azure — informacje

### <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

System nazw domen lub DNS, jest odpowiedzialny za tłumaczenia (lub rozpoznawania) nazwę witryny sieci Web lub usługi na adres IP. Usługa DNS platformy Azure to Usługa hostingu domen DNS, rozpoznawania nazw przy użyciu infrastruktury Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Domeny DNS w usłudze Azure DNS są hostowane w globalnej sieci platformy Azure serwerów nazw DNS. Ta metoda korzysta emisji dowolnej sieci, dzięki czemu każdy zapytanie DNS zostanie odebrane przez najbliższy dostępny serwer DNS. Usługa DNS platformy Azure zapewnia duża wydajność i wysoka dostępność dla domeny.

Usługa Azure DNS jest oparta na usłudze Azure Resource Manager. W efekcie korzysta z funkcji Menedżera zasobów, takich jak kontrola dostępu oparta na rolach, dzienniki inspekcji i blokowanie zasobów. Twoje domenach i rekordach mogą być zarządzane za pośrednictwem portalu Azure, poleceń cmdlet programu PowerShell Azure i wiersza polecenia platformy Azure i platform. Aplikacje wymagające automatycznego zarządzania DNS można zintegrować z usługą za pośrednictwem interfejsu API REST i zestawy SDK.

### <a name="how-much-does-azure-dns-cost"></a>Ile kosztuje usługi Azure DNS

Modelu rozliczeń usługi Azure DNS opiera się na liczbę stref DNS udostępnianych w usłudze Azure DNS oraz liczby zapytań DNS, które otrzymują. Zniżki znajdują się na podstawie użycia.

Aby uzyskać więcej informacji, zobacz [usługi Azure DNS cennikiem](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaka jest umowa SLA dla usługi Azure DNS?

Azure gwarantuje, że prawidłowe żądania DNS otrzyma odpowiedź z co najmniej jedną usługę Azure DNS nazwy serwera co najmniej 99,99% czasu.

Aby uzyskać więcej informacji, zobacz [strony umowy SLA platformy Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co to jest strefy DNS? Czy to jest to samo co „domena DNS”? 

Domena jest unikatową nazwę w systemie nazw domen, na przykład "contoso.com".


Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Na przykład domena "contoso.com" może zawierać wiele rekordów DNS, takie jak "mail.contoso.com" (dla serwera poczty) i "www.contoso.com" (dla witryny sieci web). Może być hostowana te rekordy w strefie DNS "contoso.com".

Nazwa domeny jest *tylko nazwa*, podczas gdy strefa DNS jest zasobem danych zawierającego rekordy DNS dla nazwy domeny. Usługa Azure DNS umożliwia hostowanie strefy DNS i zarządzanie rekordami DNS dla domeny na platformie Azure. Umożliwia także serwery DNS odpowiada na zapytania DNS z Internetu.

### <a name="do-i-need-to-purchase-a-dns-domain-name-to-use-azure-dns"></a>Czy muszę zakupić nazwę domeny DNS, aby używać usługi System DNS Azure? 

Niekoniecznie.

Nie trzeba kupować domeny, aby hostować strefę DNS w usłudze System DNS Azure. Strefę DNS można utworzyć w dowolnej chwili bez konieczności posiadania nazwy domeny. Zapytania DNS dla tej strefy rozwiąże tylko, jeśli są kierowane do usługi Azure DNS serwery nazw przypisane do strefy.

Musisz kupić nazwę domeny, jeśli chcesz połączyć swoją strefę DNS w hierarchii DNS globalnej — to połączenie umożliwia zapytania DNS z dowolnego miejsca na świecie można znaleźć strefy DNS i udzielić odpowiedzi z rekordów DNS.

## <a name="azure-dns-features"></a>Funkcje usługi Azure DNS

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Usługi Azure DNS obsługuje trybu failover routingu lub punkt końcowy usługi DNS ruchu?

Trybu failover routingu i punktu końcowego usługi DNS ruchu są dostarczane przez usługę Azure Traffic Manager. Menedżer ruchu Azure jest oddzielny usługa Azure, która może być używany razem z usługi Azure DNS. Aby uzyskać więcej informacji, zobacz [Omówienie Menedżera ruchu](../traffic-manager/traffic-manager-overview.md).

Usługa DNS platformy Azure obsługuje tylko hosting domeny DNS "static", gdy każdego zapytania DNS dotyczące rekordu DNS danego otrzyma zawsze tej samej odpowiedzi DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Usługi Azure DNS obsługuje rejestracji nazwy jej domeny?

Nie. Usługa Azure DNS nie obsługuje obecnie zakupów nazw domen. Jeśli chcesz kupić domen, należy użyć Rejestratora nazw domen innych firm. Zazwyczaj rejestratora pobiera małych opłata. Następnie domeny może być hostowana w usłudze Azure DNS zarządzania rekordów DNS. Zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md) szczegółowe informacje.

Zakupu domeny jest funkcją śledzone zaległe platformy Azure. Można użyć witrynę opinii do [rejestrowania programu obsługi dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Usługa Azure DNS obsługuje rozszerzenia DNSSEC?

Nie. Usługa DNS platformy Azure aktualnie nie obsługuje rozszerzeń DNSSEC.

Rozszerzenia DNSSEC to funkcja śledzenia na liście prac usługi Azure DNS. Można użyć witrynę opinii do [rejestrowania programu obsługi dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Usługa DNS platformy Azure obsługuje transferów stref (AXFR/IXFR)?

Nie. Usługa Azure DNS nie obsługuje obecnie transferów stref. Strefy DNS może być [zaimportować do usługi Azure DNS przy użyciu interfejsu wiersza polecenia Azure](dns-import-export.md). Zarządzanie rekordami DNS za pomocą [portalu zarządzania usługi Azure DNS](dns-operations-recordsets-portal.md), naszych [interfejsu API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md), lub [interfejsu wiersza polecenia narzędzia](dns-operations-recordsets-cli.md).

Transfer strefy to funkcja śledzenia na liście prac usługi Azure DNS. Można użyć witrynę opinii do [rejestrowania programu obsługi dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Usługi DNS platformy Azure obsługuje adres URL przekierowania?

Nie. Adres URL przekierowania usługi nie są faktycznie usługi DNS — działają na poziomie protokołu HTTP, a nie poziom DNS. Niektórzy dostawcy DNS pakietów adres URL przekierowania usługi w ramach ich ogólną oferty. Jest to obecnie nieobsługiwane przez usługę Azure DNS.

Funkcja Przekierowywanie adresu URL jest śledzony na liście prac usługi Azure DNS. Można użyć witrynę opinii do [rejestrowania programu obsługi dla tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-extended-ascii-encoding-8-bit-set-for-txt-recordset-"></a>Usługi Azure DNS obsługuje rozszerzonej ASCII (8-bitowych) zestawu dla zestawu rekordów TXT kodowania?

Tak. Usługa DNS platformy Azure obsługuje rozszerzonego zestawu kodowanie ASCII dla zestawów rekordów TXT, korzystając z najnowszej wersji interfejsów API REST usługi Azure, SDK, programu PowerShell i interfejsu wiersza polecenia (w wersji wcześniejszej niż 2017-10-01 lub zestawu SDK 2.1 obsługuje rozszerzonego zestawu ASCII). Na przykład, jeśli użytkownik udostępnia ciąg jako wartość rekordu TXT mający rozszerzonej \128 znaków ASCII (np: "abcd\128efgh"), usługi Azure DNS będzie używać wartości bajtu tego znaku (czyli 128) w reprezentacji wewnętrznej. W tym czasie rozpoznawania nazw DNS również tej wartości bajtu zostanie zwrócony w odpowiedzi. Należy również zauważyć, że "abc" i "\097\098\099" są wymienne chodzi rozpoznawania jest. 

Firma Microsoft wykonaj [ze standardem RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) strefy reguły specjalne wzorca format pliku rekordów TXT. Na przykład "\" teraz faktycznie specjalne wszystko na dokumenty RFC. Jeśli określisz "A\B" jako wartości rekordu TXT będą reprezentowane i rozpoznać jako właśnie "AB". Rekord TXT mają "A\B" rozdzielczością na pewno chcesz, należy wprowadzić "\" ponownie, tj. Określ jako "A\\B". 

Należy pamiętać, że ta obsługa jest obecnie niedostępna dla rekordów TXT utworzone w portalu Azure. 

## <a name="using-azure-dns"></a>Za pomocą usługi Azure DNS

### <a name="can-i-co-host-a-domain-using-azure-dns-and-another-dns-provider"></a>Czy mogę hostowane przy użyciu usługi Azure DNS i dostawcy usługi DNS innej domeny?

Tak. Usługa DNS platformy Azure obsługuje wspólnej hostingu domeny z innymi usługami DNS.

Aby to zrobić, należy zmodyfikować rekordy NS dla domeny (które kontroli, których dostawców odbierania DNS wysyła zapytanie do domeny) aby wskazywał serwery nazw zarówno dostawców. Możesz zmodyfikować te rekordy NS w trzech miejscach: w usłudze Azure DNS, inny dostawca i w strefie nadrzędnej (zazwyczaj skonfigurowany za pomocą rejestratora nazw domen). Aby uzyskać więcej informacji dotyczących delegowania usługi DNS, zobacz [Delegowanie domeny DNS](dns-domain-delegation.md).

Ponadto należy się upewnić, że rekordy DNS dla domeny są zsynchronizowane między zarówno dostawców DNS. Usługa Azure DNS nie obsługuje obecnie transferów stref DNS. Rekordy DNS muszą zostać zsynchronizowane za pomocą [portalu zarządzania usługi Azure DNS](dns-operations-recordsets-portal.md), [interfejsu API REST](https://docs.microsoft.com/powershell/module/azurerm.dns), [SDK](dns-sdk.md), [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md), lub [Interfejsu wiersza polecenia narzędzia](dns-operations-recordsets-cli.md).

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Czy muszę delegować mojej domeny, aby wszystkie cztery serwery DNS platformy Azure?

Tak. Usługa Azure DNS przypisuje cztery serwery nazw do każdej strefy DNS izolacji błędów i zwiększonej odporności. Aby zakwalifikować się do umowy SLA platformy Azure DNS, należy delegować domenę do wszystkie cztery serwery nazw.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jakie są limity użycia dla usługi Azure DNS?

Następujące domyślne limity stosowane podczas korzystania z usługi Azure DNS:

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Strefy DNS platformy Azure można przenosić między grupami zasobów lub subskrypcji?

Tak. Strefy DNS można przenosić między grupami zasobów lub subskrypcji.

Nie ma żadnych wpływu na zapytania DNS podczas przenoszenia strefy DNS. Serwery nazw przypisane do strefy pozostają bez zmian i zapytań DNS są przetwarzane w zwykły w całym.

Aby uzyskać więcej informacji oraz instrukcje dotyczące przenoszenia stref DNS, zobacz [przenoszenia zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/resource-group-move-resources.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak długo trwa DNS zmiany zaczęły obowiązywać?

Nowych stref DNS i rekordy DNS są zwykle odzwierciedlone na serwery nazw usługi Azure DNS szybkiego, w ciągu kilku sekund.

Zmiany w istniejących rekordów DNS może zająć nieco dłużej, ale nadal może mieć odzwierciedlenie w serwerów nazw DNS platformy Azure w ciągu 60 sekund. W takim przypadku DNS buforowania poza usługi Azure DNS (przez klientów DNS i rozpoznawania nazw DNS cykliczne) może również wpływ na czas poświęcony na zmiany w systemie DNS były skuteczne. Ten czas trwania pamięci podręcznej mogą być kontrolowane za pomocą właściwości Time To Live (TTL) każdego zestawu rekordów.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak chronić mój stref DNS przed przypadkowym usunięciem?

Usługa Azure DNS jest zarządzana przy użyciu usługi Azure Resource Manager i korzyści z kontroli dostępu do funkcji tego usługi Azure Resource Manager udostępnia. Kontrola dostępu oparta na rolach umożliwia kontrolowanie użytkowników, którzy mają odczytu lub zapisu do strefy DNS i zestawy rekordów. Aby zapobiec przypadkowemu modyfikacja lub usunięcie strefy DNS i zestawy rekordów można zastosować blokowania zasobów.

Aby uzyskać więcej informacji, zobacz [ochrona strefy DNS i rekordy](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak skonfigurować rekordy SPF w usłudze Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-nameservers-resolve-over-ipv6"></a>Czy Azure DNS Nameservers rozwiązać za pośrednictwem protokołu IPv6? 

Tak. Podwójny stos są Nameservers DNS platformy Azure (mają adresy IPv4 i IPv6). Aby znaleźć adres IPv6 nameservers usługi Azure DNS, przypisane do strefy DNS, można użyć narzędzia, takiego jak nslookup (na przykład `nslookup -q=aaaa <Azure DNS Nameserver>`).

### <a name="how-do-i-set-up-an-international-domain-name-idn-in-azure-dns"></a>Jak ustawić zapasowej międzynarodowej domeny nazwę (IDN) w usłudze Azure DNS?

Międzynarodowe nazwy domen (IDN) działają przez kodowanie, których używane są nazwa DNS "[punycode](https://en.wikipedia.org/wiki/Punycode)". Zapytania DNS są wykonywane przy użyciu tych nazw kodowany w formacie punycode.

Międzynarodowe nazwy domen (IDN) można skonfigurować w usłudze Azure DNS, konwertując pierwszy Nazwa strefy lub nazwy zestawu rekordów na ciąg punycode. Usługa Azure DNS nie obsługuje obecnie wbudowanej konwersję z/na ciąg punycode.

## <a name="private-dns"></a>Prywatne DNS

[!INCLUDE [private-dns-public-preview-notice](../../includes/private-dns-public-preview-notice.md)]

### <a name="does-azure-dns-support-private-domains"></a>Usługi Azure DNS obsługuje domen "private"?
Obsługa domen "private" jest implementowane za pomocą funkcji prywatnego strefy.  Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej.  Prywatne strefy są zarządzane przy użyciu tych samych narzędzi jako internetowy strefy DNS platformy Azure, ale są one tylko rozpoznawana z w określonej sieci wirtualne.  Zobacz [omówienie](private-dns-overview.md) szczegółowe informacje.

W tym momencie stref prywatne nie są obsługiwane w portalu Azure. 

Informacje na temat innych wewnętrzny DNS na platformie Azure, zobacz [rozpoznawanie nazwy dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

### <a name="what-is-the-difference-between-registration-virtual-network-and-resolution-virtual-network-in-the-context-of-private-zones"></a>Jaka jest różnica między siecią wirtualną rejestracji i siecią wirtualną rozpoznawania w kontekście stref prywatnej? 
Sieci wirtualne można połączyć prywatnej strefy DNS na dwa sposoby — co sieć wirtualna rejestracji lub jako rozwiązania sieci wirtualnej. W obu przypadkach maszyn wirtualnych w sieci wirtualnej będzie można pomyślnie fd3e rekordy w strefie prywatnych. Jednak jeśli określisz sieci wirtualnej co sieć wirtualna rejestracji Azure automatycznie zarejestruje rekordy DNS (rejestracja dynamicznych) do strefy dla maszyn wirtualnych w sieci wirtualnej. Ponadto podczas maszynę wirtualną w sieci wirtualnej zostaje usunięta rejestracji Azure również automatycznie usunie odpowiedniego rekordu DNS z połączonego strefy prywatnych. 

### <a name="will-azure-dns-private-zones-work-across-azure-regions"></a>Strefy DNS prywatnego Azure będzie działać w regionach platformy Azure?
Tak. Strefy prywatnego jest obsługiwana dla rozpoznawania nazw DNS między sieciami wirtualnymi w regionach platformy Azure, nawet jeśli nie zostanie jawnie równorzędna sieci wirtualnych, pod warunkiem, że sieci wirtualne są określone jako rozpoznawania wirtualnych sieci prywatnych strefy. Klienci mogą muszą sieci wirtualne, można połączyć za pomocą dla ruchu TCP/HTTP przepływać z jednego regionu. 

### <a name="is-connectivity-to-the-internet-from-virtual-networks-required-for-private-zones"></a>Jest połączenie z Internetem z sieci wirtualne wymagane dla stref prywatnej?
Nie. Strefy prywatnego działają w połączeniu z sieciami wirtualnymi i poinformuj klientów, Zarządzanie domenami dla maszyn wirtualnych lub innych zasobów w ramach i między sieciami wirtualnymi. Bez łączności z Internetem jest wymagany do rozpoznawania nazw. 

### <a name="can-the-same-private-zone-be-used-for-multiple-virtual-networks-for-resolution"></a>Może służyć do wielu sieci wirtualnych do rozpoznawania tej samej strefie prywatnej? 
Tak. Klientów można skojarzyć maksymalnie 10 sieciami wirtualnymi rozwiązania z jednej strefie prywatnych.

### <a name="can-a-virtual-network-that-belongs-to-a-different-subscription-be-added-as-a-resolution-virtual-network-to-a-private-zone"></a>Można sieci wirtualnej, który należy do innej subskrypcji można dodać jako rozwiązania wirtualnej sieci do strefy prywatnej? 
Tak, jak długo użytkownik ma uprawnienia do zapisu operacji na zarówno sieci wirtualnych, jak i strefy DNS prywatnych. Należy pamiętać, że uprawnienia do zapisu mogą zostać przypisane do wielu ról RBAC. Na przykład rola klasycznego RBAC współautora sieci ma uprawnienia zapisu do sieci wirtualnych. Aby uzyskać więcej informacji na role RBAC, zobacz [kontroli dostępu opartej na rolach](../role-based-access-control/overview.md)

### <a name="will-the-automatically-registered-virtual-machine-dns-records-in-a-private-zone-be-automatically-deleted-when-the-virtual-machines-are-deleted-by-the-customer"></a>Zostaną automatycznie zarejestrowane maszyny wirtualnej rekordów DNS w strefie prywatnej automatycznie usunięte po maszyn wirtualnych zostaną usunięte przez klienta?
Tak. Po usunięciu maszyny wirtualnej w ramach sieci wirtualnej rejestracji, firma Microsoft automatycznie usunąć rekordy DNS, które zostały zarejestrowane w strefie ze względu na to jest sieć wirtualną rejestracji. 

### <a name="can-an-automatically-registered-virtual-machine-record-in-a-private-zone-from-a-registration-virtual-network-be-deleted-manually"></a>Rekord maszyny wirtualnej automatycznie rejestrowane w prywatnej strefy (od rejestracji sieci wirtualnej) można usunąć ręcznie? 
Nie. W tej chwili rekordy DNS maszyny wirtualnej, które są automatycznie rejestrowane w strefie prywatnych z rejestracji sieci wirtualnej nie są widoczne lub edytowany przez klientów. Można jednak zastąpić (Zastąp) takie automatycznie zarejestrowane rekordy DNS z ręcznie utworzonych DNS rekordów w strefie. Zobacz poniższe pytanie i odpowiedź, która rozwiązuje ten problem.

### <a name="what-happens-when-we-attempt-to-manually-create-a-new-dns-record-into-a-private-zone-that-has-the-same-hostname-as-an-automatically-registered-existing-virtual-machine-in-a-registration-virtual-network"></a>Co się stanie, gdy firma Microsoft próbują ręcznie utworzyć nowy rekord DNS do strefy prywatnej o tej samej nazwy hosta jako (automatycznie zarejestrowane) istniejącej maszyny wirtualnej w sieci wirtualnej rejestracji? 
Jeśli spróbujesz ręcznie utworzyć nowy rekord DNS do strefy prywatnej o tej samej nazwy hosta jako istniejącej maszyny wirtualnej (automatycznie zarejestrowane) w sieci wirtualnej rejestracji, możliwe będzie nowy rekord DNS zastąpić automatycznie zarejestrowane rekord maszyny wirtualnej. Ponadto, następnie podejmie próbę usunięcia utworzone ręcznie rekord DNS ze strefy, powiedzie się, Usuń i ponownie nastąpi automatyczna rejestracja (rekord DNS zostanie ponownie utworzony automatycznie w strefie) tak długo, jak maszyny wirtualne nadal istnieje i ma prywatnego adresu IP do niego dołączony. 

### <a name="what-happens-when-we-unlink-a-registration-virtual-network-from-a-private-zone--would-the-automatically-registered-virtual-machine-records-from-the-virtual-network-be-removed-from-the-zone-as-well"></a>Co się stanie, gdy firma Microsoft odłączyć rejestracji wirtualnej sieci prywatnej strefy? Ze strefy również zostaną usunięte rekordy automatycznie zarejestrowane maszyny wirtualnej z sieci wirtualnej?
Tak. Odłączenie rejestracji sieci wirtualnej (aktualizacji strefy DNS, aby usunąć skojarzone sieci wirtualnej rejestracji) ze strefy prywatne, Azure usunie wszystkie rekordy automatycznie zarejestrowane maszyny wirtualnej ze strefy. 

### <a name="what-happens-when-we-delete-a-registration-or-resolution-virtual-network-that-is-linked-to-a-private-zone--do-we-have-to-manually-update-the-private-zone-to-un-link-the-virtual-network-as-a-registration-or-resolution--virtual-network-from-the-zone"></a>Co się stanie po usunięciu możemy rejestracji (bądź rozpoznawanie nazw) siecią wirtualną, która jest połączony z prywatnej strefy? Czy mamy ręcznie zaktualizuj strefę prywatnej un-łącza sieci wirtualnej sieci wirtualnej co rejestracji (bądź rozpoznawanie nazw) ze strefy?
Tak. Usunięcie sieci wirtualnej rejestracji (bądź rozpoznawanie nazw) bez odłączenie go od prywatnej najpierw strefy, Azure umożliwią usunięcia operacja powiodła się, ale sieci wirtualnej nie jest automatycznie odłączyć od prywatnego strefy ewentualne. Należy ręcznie Odłącz wirtualnej sieci prywatnej strefy. Z tego powodu zaleca się najpierw odłączyć sieci wirtualnej sieci prywatnej strefy przed jego usunięciem.

### <a name="would-dns-resolution-using-the-default-fqdn-internalcloudappnet-still-work-even-when-a-private-zone-for-example-contosolocal-is-linked-to-a-virtual-network"></a>Czy rozpoznawanie nazw DNS przy użyciu domyślnego nazwy FQDN (internal.cloudapp.net) nadal działa nawet wtedy, gdy strefa prywatne (na przykład: contoso.local) jest połączona z siecią wirtualną? 
Tak. Funkcja prywatnego strefy nie zastępuje domyślną rozdzielczości DNS przy użyciu strefy internal.cloudapp.net platformy Azure i jest oferowany jako dodatkowe możliwości lub rozszerzenie. W obu przypadkach (czy jednostki uzależnionej na internal.cloudapp.net platformy Azure lub strefy prywatne) zaleca się Użyj nazwy FQDN, która ma zostać rozwiązany dla strefy. 

### <a name="would-the-dns-suffix-on-virtual-machines-within-a-linked-virtual-network-be-changed-to-that-of-the-private-zone"></a>Sufiks DNS na maszynach wirtualnych w ramach połączonych sieci wirtualnej zostałaby zmieniona do tej strefy prywatnej? 
Nie. W tej chwili sufiks DNS na maszynach wirtualnych w sieci wirtualnej połączonego pozostanie jako domyślny sufiks platformy Azure ("*. internal.cloudapp.net"). Można jednak ręcznie zmienić ten sufiks DNS na maszynach wirtualnych do tej strefy prywatnych. 

### <a name="are-there-any-limitations-for-private-zones-during-this-preview"></a>Czy istnieją jakiekolwiek ograniczenia dla stref prywatnego tej wersji zapoznawczej?
Tak. W publicznej wersji zapoznawczej istnieją następujące ograniczenia:
* Sieci wirtualne 1 rejestracji dla stref prywatnych
* Sieci wirtualne maksymalnie 10 rozpoznawania dla stref prywatnych
* Danej sieci wirtualnej może odnosić się tylko do jednej strefie prywatnego co sieć wirtualna rejestracji
* Danej sieci wirtualnej może odnosić się do maksymalnie 10 stref prywatnego jako rozwiązania sieci wirtualnej
* Jeżeli określono sieć wirtualną rejestracji, rekordy DNS dla maszyn wirtualnych z tej sieci wirtualnej, które są zarejestrowane do strefy prywatnych nie będą widoczne lub pobieranie z programu Powershell/CLI/interfejsów API, ale rekordów maszyny Wirtualnej w rzeczywistości są rejestrowane i zostanie rozwiązany pomyślnie
* Wstecznego DNS działa tylko dla miejsca prywatnego adresu IP w sieci wirtualnej rejestracji
* Reverse DNS dla prywatnego adresu IP, który nie jest zarejestrowany w strefie prywatny (na przykład: prywatnego adresu IP dla maszyny wirtualnej w sieci wirtualnej połączonego jako rozwiązania wirtualnej sieci prywatnej strefę) zwróci "internal.cloudapp.net" jako sufiks DNS, jednak Ten sufiks nie jest rozpoznawana.   
* Sieć wirtualna musi być pusta (tzn. nie maszyn wirtualnych z karty Sieciowej podłączony) podczas początkowego (tj. po raz pierwszy) łączenia ze strefy prywatnej, co sieć wirtualna rejestracji bądź rozpoznawanie nazw. Jednak sieci wirtualnej można następnie niepustym przyszłych łączenia jako rejestracji bądź rozpoznawanie nazw sieci wirtualnej, do innych stref prywatnych. 
* W tej chwili warunkowego przesyłania dalej nie jest obsługiwana, na przykład dotyczące włączania rozpoznawania między sieciami Azure i lokalnego programu. Dokumentację dotyczącą sposobu klientów można uzyskać w tym scenariuszu za pośrednictwem innych mechanizmów, zobacz [rozpoznawanie nazwy dla maszyn wirtualnych i wystąpień roli](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)

### <a name="are-there-any-quotas-or-limits-on-zones-or-records-for-private-zones"></a>Czy istnieją przydziałów lub limitów strefy lub rekordów dla strefy prywatnej?
Nie ma nie oddzielnych limity liczby stref dozwolonych dla jednej subskrypcji lub liczba zestawów rekordów dla strefy dla stref prywatnych. Zarówno publiczne i prywatne strefy są wliczane do ogólnej limity DNS zgodnie z opisem [tutaj](../azure-subscription-service-limits.md#dns-limits)

### <a name="is-there-portal-support-for-private-zones"></a>Istnieje już Portal obsługę strefy prywatnej?
Strefy prywatne, które zostały już utworzone za pomocą mechanizmów-Portal (interfejs API/programu PowerShell/interfejsu wiersza polecenia/SDK) będą widoczne w portalu Azure, ale klienci nie będzie w stanie do tworzenia nowych stref prywatnych lub zarządzania skojarzenia z sieciami wirtualnymi. Ponadto dla sieci wirtualnych skojarzone jako sieci wirtualne rejestracji, automatycznie zarejestrowane rekordy maszyny Wirtualnej nie będą widoczne w portalu. 

## <a name="next-steps"></a>Kolejne kroki

[Dowiedz się więcej o usłudze Azure DNS](dns-overview.md)
<br>
[Dowiedz się więcej o korzystaniu z usługi Azure DNS dla domen prywatnych](private-dns-overview.md)
<br>
[Więcej informacji na temat stref DNS i rekordów](dns-zones-records.md)
<br>
[Wprowadzenie do usługi Azure DNS](dns-getstarted-portal.md)

