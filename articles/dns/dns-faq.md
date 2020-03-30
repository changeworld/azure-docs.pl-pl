---
title: Często zadawane pytania — Usługa Azure DNS
description: W tym artykule dowiedz się więcej o często zadawanych pytaniach dotyczących usługi Azure DNS
services: dns
author: rohinkoul
ms.service: dns
ms.topic: article
ms.date: 6/15/2019
ms.author: rohink
ms.openlocfilehash: 76b19cfb3c00a26d81eab81f67d8e156a520f377
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77121718"
---
# <a name="azure-dns-faq"></a>Często zadawane pytania dotyczące usługi Azure DNS

## <a name="about-azure-dns"></a>Informacje o usłudze Azure DNS

### <a name="what-is-azure-dns"></a>Co to jest system DNS platformy Azure?

System nazw domen (DNS) tłumaczy lub rozwiązuje nazwę witryny sieci Web lub usługi na jego adres IP. Usługa Azure DNS jest usługą hostingu dla domen DNS. Zapewnia rozpoznawanie nazw przy użyciu infrastruktury platformy Microsoft Azure. Dzięki hostowaniu swoich domen na platformie Azure możesz zarządzać rekordami DNS z zastosowaniem tych samych poświadczeń, interfejsów API, narzędzi i rozliczeń co w przypadku innych usług platformy Azure.

Domeny DNS w usłudze Azure DNS są hostowane w globalnej sieci serwerów nazw DNS platformy Azure. Ten system używa sieci Anycast, dzięki czemu każda kwerenda DNS jest odbierana przez najbliższy dostępny serwer DNS. Usługa Azure DNS zapewnia wysoką wydajność i wysoką dostępność dla twojej domeny.

Usługa Azure DNS jest oparta na usłudze Azure Resource Manager. Usługa Azure DNS korzysta z funkcji Usługi Resource Manager, takich jak kontrola dostępu oparta na rolach, dzienniki inspekcji i blokowanie zasobów. Domeny i rekordy można zarządzać za pośrednictwem witryny Azure portal, poleceń cmdlet programu Azure PowerShell i wieloplatformowego interfejsu wiersza polecenia platformy Azure. Aplikacje wymagające automatycznego zarządzania systemem DNS mogą integrować się z usługą za pośrednictwem interfejsu API REST i sdków.

### <a name="how-much-does-azure-dns-cost"></a>Ile kosztuje usługa Azure DNS?

Model rozliczeń usługi Azure DNS jest oparty na liczbie stref DNS hostowanych w usłudze Azure DNS. Jest również na podstawie liczby otrzymywanych zapytań DNS. Zniżki są udzielane na podstawie użycia.

Aby uzyskać więcej informacji, zobacz [stronę cennika usługi Azure DNS](https://azure.microsoft.com/pricing/details/dns/).

### <a name="what-is-the-sla-for-azure-dns"></a>Jaki jest poziom umowy SLA dla usługi Azure DNS?

Platforma Azure gwarantuje, że prawidłowe żądania DNS otrzymują odpowiedź od co najmniej jednego serwera nazw DNS platformy Azure w 100% czasu.

Aby uzyskać więcej informacji, zobacz [stronę Umowy SLA usługi Azure DNS](https://azure.microsoft.com/support/legal/sla/dns).

### <a name="what-is-a-dns-zone-is-it-the-same-as-a-dns-domain"></a>Co to jest strefa DNS? Czy jest taka sama jak domena DNS? 

Domena jest unikatową nazwą w systemie nazw domen. Przykładowa domena to contoso.com.

Strefa DNS jest używana do hostowania rekordów DNS dla określonej domeny. Na przykład contoso.com domeny może zawierać kilka rekordów DNS. Rekordy mogą zawierać mail.contoso.com dla serwera poczty\.i contoso.com www dla witryny sieci Web. Te rekordy są hostowane w strefie DNS contoso.com.

Nazwa domeny to *tylko nazwa*. Strefa DNS to zasób danych zawierający rekordy DNS dla nazwy domeny. Usługa Azure DNS umożliwia hostowanie strefy DNS i zarządzanie rekordami DNS dla domeny na platformie Azure. Udostępnia również serwery nazw DNS do odpowiadania na zapytania DNS z Internetu.

### <a name="do-i-need-to-buy-a-dns-domain-name-to-use-azure-dns"></a>Czy muszę kupić nazwę domeny DNS, aby korzystać z usługi Azure DNS? 

Niekoniecznie.

Nie musisz kupować domeny, aby hostować strefę DNS w usłudze Azure DNS. Strefę DNS można utworzyć w dowolnym momencie bez posiadania nazwy domeny. Kwerendy DNS dla tej strefy są rozpoznawane tylko wtedy, gdy są kierowane do serwerów nazw DNS platformy Azure przypisanych do strefy.

Aby połączyć strefę DNS z globalną hierarchią DNS, należy ją kupić. Następnie zapytania DNS z dowolnego miejsca na świecie znajdują twoją strefę DNS i odpowiadają za pomocą rekordów DNS.

## <a name="azure-dns-features"></a>Funkcje usługi Azure DNS

### <a name="are-there-any-restrictions-when-using-alias-records-for-a-domain-name-apex-with-traffic-manager"></a>Czy istnieją jakieś ograniczenia dotyczące używania rekordów aliasu dla wierzchołka nazwy domeny z usługą Traffic Manager?

Tak. Należy użyć statycznych publicznych adresów IP z usługą Azure Traffic Manager. Skonfiguruj **cel zewnętrzny punkt końcowy** przy użyciu statycznego adresu IP. 

### <a name="does-azure-dns-support-dns-based-traffic-routing-or-endpoint-failover"></a>Czy usługa Azure DNS obsługuje routing ruchu oparty na systemie DNS lub przebłaję pracy awaryjnej punktu końcowego?

Routing ruchu oparty na systemie DNS i usługa failover punktu końcowego są dostarczane przez usługę Traffic Manager. Traffic Manager to oddzielna usługa platformy Azure, która może być używana z usługą Azure DNS. Aby uzyskać więcej informacji, zobacz [omówienie usługi Traffic Manager](../traffic-manager/traffic-manager-overview.md).

Usługa Azure DNS obsługuje tylko hostowanie statycznych domen DNS, w których każde zapytanie DNS dla danego rekordu DNS zawsze otrzymuje tę samą odpowiedź DNS.

### <a name="does-azure-dns-support-domain-name-registration"></a>Czy usługa Azure DNS obsługuje rejestrację nazw domen?

Nie. Usługa Azure DNS obecnie nie obsługuje opcji kupowania nazw domen. Aby kupować domeny, musisz użyć zewnętrznego rejestratora nazw domen. Rejestrator zazwyczaj pobiera niewielką opłatę roczną. Domeny następnie mogą być hostowane w usłudze Azure DNS do zarządzania rekordami DNS. Aby uzyskać więcej informacji, zobacz [Delegowanie domeny do usługi Azure DNS](dns-domain-delegation.md).

Funkcja zakupu nazw domen jest śledzona w zaległościach platformy Azure. Użyj witryny z opiniami, aby [zarejestrować swoją obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/4996615-azure-should-be-its-own-domain-registrar).

### <a name="does-azure-dns-support-dnssec"></a>Czy usługa Azure DNS obsługuje usługę DNSSEC?

Nie. Usługa Azure DNS nie obsługuje obecnie rozszerzeń zabezpieczeń systemu nazw domen (DNSSEC).

Funkcja DNSSEC jest śledzona w zaległościach usługi Azure DNS. Użyj witryny z opiniami, aby [zarejestrować swoją obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/13284393-azure-dns-needs-dnssec-support).

### <a name="does-azure-dns-support-zone-transfers-axfrixfr"></a>Czy usługa Azure DNS obsługuje transfery stref (AXFR/IXFR)?

Nie. Usługa Azure DNS obecnie nie obsługuje transferów stref. Strefy DNS można [importować do usługi Azure DNS przy użyciu interfejsu wiersza polecenia platformy Azure](dns-import-export.md). Rekordy DNS są zarządzane za pośrednictwem [portalu zarządzania usługi Azure DNS, interfejsu](dns-operations-recordsets-portal.md) [API REST,](https://docs.microsoft.com/powershell/module/az.dns) [zestawu SDK,](dns-sdk.md) [poleceń cmdlet programu PowerShell](dns-operations-recordsets.md)lub [narzędzia interfejsu wiersza polecenia.](dns-operations-recordsets-cli.md)

Funkcja transferu strefy jest śledzona w zaległościach usługi Azure DNS. Użyj witryny z opiniami, aby [zarejestrować swoją obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/12925503-extend-azure-dns-to-support-zone-transfers-so-it-c).

### <a name="does-azure-dns-support-url-redirects"></a>Czy usługa Azure DNS obsługuje przekierowania adresów URL?

Nie. Usługi przekierowania adresów URL nie są usługą DNS. Działają one na poziomie HTTP, a nie na poziomie DNS. Niektórzy dostawcy DNS łączą usługę przekierowania adresu URL jako część swojej ogólnej oferty. Ta usługa nie jest obecnie obsługiwana przez usługę Azure DNS.

Funkcja przekierowania adresu URL jest śledzona w zaległościach usługi Azure DNS. Użyj witryny z opiniami, aby [zarejestrować swoją obsługę tej funkcji](https://feedback.azure.com/forums/217313-networking/suggestions/10109736-provide-a-301-permanent-redirect-service-for-ape).

### <a name="does-azure-dns-support-the-extended-ascii-encoding-8-bit-set-for-txt-record-sets"></a>Czy usługa Azure DNS obsługuje rozszerzony zestaw kodowania ASCII (8-bitowy) dla zestawów rekordów TXT?

Tak. Usługa Azure DNS obsługuje rozszerzony zestaw kodowania ASCII dla zestawów rekordów TXT. Ale należy użyć najnowszej wersji interfejsów API rest platformy Azure, zestawów SDK, programu PowerShell i interfejsu wiersza polecenia. Wersje starsze niż 1 października 2017 r. lub SDK 2.1 nie obsługują rozszerzonego zestawu ASCII. 

Na przykład można podać ciąg jako wartość dla rekordu TXT, który ma rozszerzony znak ASCII \128. Przykładem jest "abcd\128efgh". Usługa Azure DNS używa wartości bajtów tego znaku, który jest 128, w reprezentacji wewnętrznej. W czasie rozpoznawania DNS ta wartość bajtu jest zwracana w odpowiedzi. Należy również zauważyć, że "abc" i "\097\098\099" są wymienne, jeśli chodzi o rozdzielczość. 

Przestrzegamy reguły ucieczki formatu głównego pliku strefy [RFC 1035](https://www.ietf.org/rfc/rfc1035.txt) dla rekordów TXT. Na przykład `\` teraz faktycznie ucieka wszystko na RFC. Jeśli określisz `A\B` jako wartość rekordu TXT, jest ona reprezentowana i rozpoznawana jako tylko `AB`. Jeśli naprawdę chcesz, aby rekord `A\B` TXT miał rozdzielczość, `\` musisz uciec ponownie. Na przykład określ `A\\B`.

Ta obsługa nie jest obecnie dostępna dla rekordów TXT utworzonych z witryny Azure portal.

## <a name="alias-records"></a>Rekordy aliasu

### <a name="what-are-some-scenarios-where-alias-records-are-useful"></a>Jakie są scenariusze, w których rekordy aliasu są przydatne?

Zobacz sekcję scenariuszy w [omówie rekordów aliasu DNS platformy Azure](dns-alias.md).

### <a name="what-record-types-are-supported-for-alias-record-sets"></a>Jakie typy rekordów są obsługiwane dla zestawów rekordów aliasu?

Zestawy rekordów aliasu są obsługiwane dla następujących typów rekordów w strefie DNS platformy Azure:
 
- A 
- AAAA
- CNAME 

### <a name="what-resources-are-supported-as-targets-for-alias-record-sets"></a>Jakie zasoby są obsługiwane jako obiekty docelowe dla zestawów rekordów aliasu?

- **Wskaż publiczny zasób IP z zestawu rekordów DNS A/AAAA.** Można utworzyć zestaw rekordów A/AAAA i ustawić rekord aliasu wskazujący publiczny zasób IP.
- **Wskaż profil usługi Traffic Manager z zestawu rekordów DNS A/AAAA/CNAME.** Można wskazać CNAME profilu usługi Traffic Manager z zestawu rekordów CNAME DNS. Przykładem jest contoso.trafficmanager.net. Teraz można również wskazać profil usługi Traffic Manager, który ma zewnętrzne punkty końcowe z rekordu A lub AAAA ustawionego w strefie DNS.
- **Wskaż punkt końcowy sieci dostarczania zawartości platformy Azure (CDN).** Jest to przydatne podczas tworzenia statycznych witryn sieci Web przy użyciu usługi Azure Storage i usługi Azure CDN.
- **Wskaż inny rekord DNS ustawiony w tej samej strefie.** Rekordy aliasów mogą odwoływać się do innych zestawów rekordów tego samego typu. Na przykład zestaw rekordów DNS CNAME może być aliasem dla innego zestawu rekordów CNAME tego samego typu. Ten układ jest przydatny, jeśli niektóre zestawy rekordów mają być aliasami, a niektóre niealiasje.

### <a name="can-i-create-and-update-alias-records-from-the-azure-portal"></a>Czy mogę tworzyć i aktualizować rekordy aliasów z witryny Azure portal?

Tak. Rekordy aliasów można tworzyć i zarządzać nimi w witrynie Azure portal wraz z interfejsami API rest platformy Azure, programem PowerShell, interfejsem wiersza polecenia i zestawami SDK.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-deleted-when-the-underlying-public-ip-is-deleted"></a>Czy rekordy aliasu pomogą upewnić się, że mój zestaw rekordów DNS zostanie usunięty po usunięciu podstawowego publicznego adresu IP?

Tak. Ta funkcja jest jedną z podstawowych możliwości rekordów aliasu. Pomaga uniknąć potencjalnych awarii dla użytkowników aplikacji.

### <a name="will-alias-records-help-to-make-sure-my-dns-record-set-is-updated-to-the-correct-ip-address-when-the-underlying-public-ip-address-changes"></a>Czy rekordy aliasu pomogą upewnić się, że mój zestaw rekordów DNS jest aktualizowany na poprawny adres IP po zmianie podstawowego publicznego adresu IP?

Tak. Ta funkcja jest jedną z podstawowych możliwości rekordów aliasu. Pomaga uniknąć potencjalnych awarii lub zagrożeń bezpieczeństwa dla aplikacji.

### <a name="are-there-any-restrictions-when-using-alias-record-sets-for-a-or-aaaa-records-to-point-to-traffic-manager"></a>Czy istnieją jakieś ograniczenia dotyczące używania zestawów rekordów aliasów dla rekordów A lub AAAA do wskazywać menedżera ruchu?

Tak. Aby wskazać profil usługi Traffic Manager jako alias z zestawu rekordów A lub AAAA, profil usługi Traffic Manager musi używać tylko zewnętrznych punktów końcowych. Podczas tworzenia zewnętrznych punktów końcowych w usłudze Traffic Manager podaj rzeczywiste adresy IP punktów końcowych.

### <a name="is-there-an-additional-charge-to-use-alias-records"></a>Czy istnieje dodatkowa opłata za używanie rekordów aliasu?

Rekordy aliasów to kwalifikacja w prawidłowym zestawie rekordów DNS. Nie ma żadnych dodatkowych rozliczeń za rekordy aliasu.

## <a name="use-azure-dns"></a>Korzystanie z usługi Azure DNS

### <a name="can-i-co-host-a-domain-by-using-azure-dns-and-another-dns-provider"></a>Czy mogę współorganizować domenę przy użyciu usługi Azure DNS i innego dostawcy DNS?

Tak. Usługa Azure DNS obsługuje domeny współorganizacyjne z innymi usługami DNS.

Aby skonfigurować współ hosting, zmodyfikuj rekordy NS, aby domena wskazywała serwery nazw obu dostawców. Rekordy serwera nazw (NS) określają, którzy dostawcy otrzymują zapytania DNS dla domeny. Można zmodyfikować te rekordy NS w usłudze Azure DNS, w innym dostawcy i w strefie nadrzędnej. Strefa nadrzędna jest zazwyczaj konfigurowana za pośrednictwem rejestratora nazw domen. Aby uzyskać więcej informacji na temat delegowania DNS, zobacz [Delegowanie domeny DNS](dns-domain-delegation.md).

Upewnij się również, że rekordy DNS domeny są zsynchronizowane między obydwoma dostawcami dns. Usługa Azure DNS obecnie nie obsługuje transferów stref DNS. Rekordy DNS muszą być synchronizowane przy użyciu [portalu zarządzania usługi Azure DNS, interfejsu](dns-operations-recordsets-portal.md) [API REST,](https://docs.microsoft.com/rest/api/dns/) [zestawu SDK,](dns-sdk.md) [polecenia cmdlet programu PowerShell](dns-operations-recordsets.md)lub [narzędzia interfejsu wiersza polecenia.](dns-operations-recordsets-cli.md)

### <a name="do-i-have-to-delegate-my-domain-to-all-four-azure-dns-name-servers"></a>Czy muszę delegować domenę na wszystkie cztery serwery nazw DNS platformy Azure?

Tak. Usługa Azure DNS przypisuje do każdej strefy DNS cztery serwery nazw. Takie rozwiązanie dotyczy izolacji winy i zwiększonej odporności. Aby zakwalifikować się do umowy SLA DNS platformy Azure, delegoj domenę na wszystkie cztery serwery nazw.

### <a name="what-are-the-usage-limits-for-azure-dns"></a>Jakie są limity użycia usługi Azure DNS?

Podczas korzystania z usługi Azure DNS obowiązują następujące limity domyślne.

[!INCLUDE [dns-limits](../../includes/dns-limits.md)]

### <a name="can-i-move-an-azure-dns-zone-between-resource-groups-or-between-subscriptions"></a>Czy mogę przenieść strefę DNS platformy Azure między grupami zasobów lub między subskrypcjami?

Tak. Strefy DNS można przenosić między grupami zasobów lub między subskrypcjami.

Nie ma wpływu na kwerendy DNS podczas przenoszenia strefy DNS. Serwery nazw przypisane do strefy pozostają takie same. Kwerendy DNS są przetwarzane normalnie w całym.

Aby uzyskać więcej informacji i instrukcji dotyczących przenoszenia stref DNS, zobacz [Przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

### <a name="how-long-does-it-take-for-dns-changes-to-take-effect"></a>Jak długo trwa wprowadzanie zmian w systemie DNS?

Nowe strefy DNS i rekordy DNS są zazwyczaj szybko wyświetlane na serwerach nazw DNS platformy Azure. Czas wynosi kilka sekund.

Zmiany w istniejących rekordach DNS mogą potrwać nieco dłużej. Zazwyczaj pojawiają się one na serwerach nazw DNS platformy Azure w ciągu 60 sekund. Buforowanie DNS przez klientów DNS i cykliczne programy rozpoznawania nazw DNS poza usługą Azure DNS również może mieć wpływ na czas. Aby kontrolować czas trwania pamięci podręcznej, należy użyć właściwości Time-To-Live (TTL) każdego zestawu rekordów.

### <a name="how-can-i-protect-my-dns-zones-against-accidental-deletion"></a>Jak chronić strefy DNS przed przypadkowym usunięciem?

Usługa Azure DNS jest zarządzana przy użyciu usługi Azure Resource Manager. Usługa Azure DNS korzysta z funkcji kontroli dostępu udostępniane przez usługę Azure Resource Manager. Oparte na rolach kontroli dostępu, które użytkownicy mają dostęp do odczytu lub zapisu do stref DNS i zestawów rekordów. Blokady zasobów zapobiegają przypadkowej modyfikacji lub usunięciu stref DNS i zestawów rekordów.

Aby uzyskać więcej informacji, zobacz [Ochrona stref i rekordów DNS](dns-protect-zones-recordsets.md).

### <a name="how-do-i-set-up-spf-records-in-azure-dns"></a>Jak skonfigurować rekordy SPF w usłudze Azure DNS?

[!INCLUDE [dns-spf-include](../../includes/dns-spf-include.md)]

### <a name="do-azure-dns-name-servers-resolve-over-ipv6"></a>Czy serwery nazw DNS platformy Azure są rozpoznawane za pośrednictwem usługi IPv6? 

Tak. Serwery nazw DNS platformy Azure są dwa stosy. Podwójny stos oznacza, że mają adresy IPv4 i IPv6. Aby znaleźć adres IPv6 dla serwerów nazw DNS platformy Azure przypisanych do strefy DNS, użyj narzędzia, takiego jak nslookup. Może to być na przykład `nslookup -q=aaaa <Azure DNS Nameserver>`.

### <a name="how-do-i-set-up-an-idn-in-azure-dns"></a>Jak skonfigurować usługę IDN w usłudze Azure DNS?

Umięsione nazwy domen (IDN) kodują każdą nazwę DNS przy użyciu [punycode](https://en.wikipedia.org/wiki/Punycode). Zapytania DNS są dokonywane przy użyciu tych nazw zakodowanych w kodach.

Aby skonfigurować nazwy IDN w usłudze Azure DNS, przekonwertuj nazwę strefy lub nazwę zestawu rekordów na punycode. Usługa Azure DNS nie obsługuje obecnie wbudowanej konwersji do lub z punycode.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej o usłudze Azure DNS](dns-overview.md).

- [Dowiedz się więcej o używaniu usługi Azure DNS dla domen prywatnych](private-dns-overview.md).

- [Dowiedz się więcej o strefach i rekordach DNS](dns-zones-records.md).

- [Wprowadzenie do usługi Azure DNS](dns-getstarted-portal.md).
