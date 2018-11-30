---
title: Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center | Dokumentacja firmy Microsoft
description: W tym dokumencie przedstawiono sposób zalecenia w usłudze Azure Security Center ułatwiają ochronę zasobów platformy Azure i pozostają zgodne z zasadami zabezpieczeń.
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: 86c50c9f-eb6b-4d97-acb3-6d599c06133e
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/18/2018
ms.author: rkarlin
ms.openlocfilehash: 3e8333b521832579a942d3fffb06103ad0431acc
ms.sourcegitcommit: a08d1236f737915817815da299984461cc2ab07e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2018
ms.locfileid: "52316621"
---
# <a name="managing-security-recommendations-in-azure-security-center"></a>Zarządzanie zaleceniami dotyczącymi zabezpieczeń w usłudze Azure Security Center
W tym dokumencie przedstawiono sposób korzystania z zaleceń Centrum zabezpieczeń Azure, aby pomóc w ochronie Twoich zasobów platformy Azure.

> [!NOTE]
> Informacje na temat usługi przedstawiono w tym dokumencie za pomocą przykładowego wdrożenia.  Ten dokument nie jest przewodnik krok po kroku.
>
>

## <a name="what-are-security-recommendations"></a>Co to są zalecenia dotyczące zabezpieczeń?
Usługa Security Center okresowo analizuje stan zabezpieczeń zasobów platformy Azure. Po znalezieniu potencjalnych luk w zabezpieczeniach usługa Security Center tworzy odpowiednie zalecenia. Przewodnik dotyczący zaleceń prowadzi użytkownika przez proces konfigurowania wymaganych kontrolek.

## <a name="implementing-security-recommendations"></a>Wdrażanie zaleceń dotyczących zabezpieczeń
### <a name="set-recommendations"></a>Zestaw zaleceń
W [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md), Dowiedz się, jak:

* Podczas konfigurowania zasad zabezpieczeń.
* Włącz zbieranie danych.
* Wybierz, które zalecenia, aby zobaczyć jako część zasad zabezpieczeń.

Bieżący Centrum zalecenia dotyczące zasad wokół aktualizacji systemu, reguły linii bazowej, programy chroniące przed złośliwym kodem [sieciowe grupy zabezpieczeń](../virtual-network/security-overview.md) podsieci i interfejsów sieciowych, inspekcji usługi SQL database, SQL database technologii transparent data encryption, i zapory aplikacji sieci web.  [Ustawianie zasad zabezpieczeń](security-center-azure-policy.md) zawiera opis każdej opcji zalecenia.

### <a name="monitor-recommendations"></a>Zalecenia dotyczące monitorowania
Po skonfigurowaniu zasad zabezpieczeń usługa Security Center analizuje stan zabezpieczeń zasobów w celu identyfikowania potencjalnych luk w zabezpieczeniach. **Zalecenia** kafelka w obszarze **Przegląd** informuje Cię łączną liczbę zaleceniami zostały zidentyfikowane przez usługę Security Center.

![Zalecenia dotyczące kafelków][1]

Aby wyświetlić szczegóły każde zalecenie, wybierz **Kafelek zalecenia** w obszarze **Przegląd**. **Zalecenia dotyczące** zostanie otwarty.

![Zalecenia dotyczące filtru][2]

Można filtrować zalecenia. Aby filtrować zalecenia, wybierz **filtru** na **zalecenia** bloku. **Filtru** zostanie otwarty blok, a następnie wybierz ważność i stan wartości, które chcesz wyświetlić.

Zalecenia są wyświetlane w postaci tabeli, gdzie każdy wiersz reprezentuje jedno zalecenie. Dostępne są następujące kolumny w tej tabeli:

* **Opis**: wyjaśniono zalecenia i co trzeba zrobić, aby rozwiązać problem.
* **ZASÓB**: Wyświetla listę zasobów, do których zostanie zastosowana tego zalecenia.
* **Stan**: Opisuje bieżący stan zalecenia:
  * **Otwórz**: zalecenie nie zostały jeszcze zarejestrowane.
  * **Trwającą**: zalecenie jest aktualnie stosowane do zasobów i jest wymagana żadna akcja.
  * **Rozwiązane**: zalecenie zostało już ukończone (w tym przypadku wiersza jest wyszarzony).
* **WAŻNOŚĆ**: opisuje ważność określonego zalecenia:
  * **Wysoka**: luki w zabezpieczeniach istnieje, która dotyczy istotnego zasobu (np. aplikacji, maszyny Wirtualnej lub sieciowej grupy zabezpieczeń) i wymaga uwagi.
  * **Średnia**: istnieje luka w zabezpieczeniach i niekrytyczne lub dodatkowe kroki są wymagane, aby wyeliminować go lub do ukończenia procesu.
  * **Niska**: istnieje luka w zabezpieczeniach, które powinny być kierowane, ale nie wymaga natychmiastowej uwagi. (Domyślnie zalecenia o niskiej ważności nie są prezentowane, ale można filtrować według zalecenia o niskiej ważności, jeśli chcesz je zobaczyć).

Użyj poniższej tabeli jako odwołanie ułatwiające zrozumienie dostępnych zaleceń i każdy z nich działanie w przypadku zastosowania.

> [!NOTE]
> Należy zrozumieć [klasycznej sieci wirtualnej i modelem wdrażania usługi Resource Manager](../azure-classic-rm.md) dla zasobów platformy Azure.
>
>

| Zalecenie | Opis |
| --- | --- |
| [Włącz zbieranie danych dla subskrypcji](security-center-enable-data-collection.md) |Zaleca się włączenie funkcji zbierania danych w ramach zasad zabezpieczeń dla każdej subskrypcji i wszystkich maszyn wirtualnych (VM) i komputerów spoza platformy Azure. |
| [Korygowanie konfiguracji zabezpieczeń](security-center-remediate-os-vulnerabilities.md) |Zaleca się, że wyrównywanie konfiguracji systemu operacyjnego za pomocą reguł konfiguracji zabezpieczeń, na przykład, nie zezwalaj na zapisywanie haseł. |
| [Zastosuj aktualizacje systemu](security-center-apply-system-updates.md) |Zaleca się wdrażanie brakujących zabezpieczeń systemu i aktualizacji krytycznych do Windows i maszyn wirtualnych systemu Linux i komputerów. |
| [Zastosuj Just-In-Time kontrolę dostępu do sieci](security-center-just-in-time.md) | Zaleca się zastosowanie dostęp dokładnie na czas maszyny Wirtualnej. Tylko w czasie funkcja jest dostępna w warstwie standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. |
| [Uruchom ponownie po zaktualizowaniu systemu](security-center-apply-system-updates.md#reboot-after-system-updates) |Zaleca ponowne uruchomienie maszyny wirtualnej w celu ukończenia procesu stosowania aktualizacji systemu. |
| [Dodawanie zapory aplikacji internetowej](security-center-add-web-application-firewall.md) |Zaleca wdrożenie zapory aplikacji sieci web (WAF) dla punktów końcowych sieci web. Dla wszelkich publicznych umożliwiający dostęp do Internetu adresu IP (adres IP na poziomie wystąpienia lub adres IP ze zrównoważonym obciążeniem) zawierającej sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzącego ruchu internetowego (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web. </br>Usługa Security Center zaleca obsługi administracyjnej zapory aplikacji sieci Web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web na maszynach wirtualnych i w środowisku App Service Environment. App Service Environment (ASE) jest [Premium](https://azure.microsoft.com/pricing/details/app-service/) usługi opcja planu usługi Azure App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji w usłudze Azure App Service. Aby dowiedzieć się więcej na temat środowiska ASE, zobacz [dokumentacja środowiska usługi App Service](../app-service/environment/intro.md).</br>Możesz chronić wiele aplikacji sieci web w usłudze Security Center, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web. |
| [Finalizowanie ochrony aplikacji](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby ukończyć konfigurację zapory aplikacji sieci Web, ruch musi zostać skierowany do urządzenia zapory aplikacji sieci Web. Tym zaleceniem kończy zmiany wymagane ustawienia. |
| [Dodawanie zapory nowej generacji](security-center-add-next-generation-firewall.md) |Zaleca się, możesz dodać następnej generacji zapory nowej (NGFW) z partnerem firmy Microsoft, aby zwiększyć Twoje zabezpieczenia. |
| [Kierowanie ruchu sieciowego tylko za pośrednictwem zapory następnej generacji](security-center-add-next-generation-firewall.md#route-traffic-through-ngfw-only) |Zaleca się, aby skonfigurować reguły Sieciowej grupy zabezpieczeń sieci, które wymuszają ruch przychodzący do maszyny Wirtualnej za pośrednictwem swojej zapory nowej generacji. |
| [Zainstaluj punkt końcowy](security-center-install-endpoint-protection.md) |Zaleca aprowizację programów chroniących przed złośliwym oprogramowaniem na maszynach wirtualnych (dotyczy tylko maszyn wirtualnych z systemem Windows). |
| [Włącz sieciowe grupy zabezpieczeń w podsieci lub maszyn wirtualnych](security-center-enable-network-security-groups.md) |Zaleca się włączenie sieciowe grupy zabezpieczeń dla podsieci lub maszyn wirtualnych. |
| [Ogranicz dostęp za pośrednictwem punktu końcowego z Internetem](security-center-restrict-access-through-internet-facing-endpoints.md) |Zaleca się, aby skonfigurować reguły ruchu przychodzącego dla sieciowych grup zabezpieczeń. |
| [Włączanie inspekcji i wykrywania zagrożeń na serwerach SQL](security-center-enable-auditing-on-sql-servers.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla serwerów SQL platformy Azure. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych). |
| [Włączanie inspekcji i wykrywania zagrożeń w bazach danych SQL](security-center-enable-auditing-on-sql-databases.md) |Zaleca się włączenie inspekcji i wykrywania zagrożeń dla baz danych Azure SQL. (Tylko w przypadku usługi azure SQL. Nie obejmuje SQL uruchomionych na maszynach wirtualnych). |
| [Włączanie funkcji Transparent Data Encryption w bazach danych SQL](security-center-enable-transparent-data-encryption.md) |Zaleca włączenie szyfrowania baz danych SQL. (Tylko usługa azure SQL). |
| [Włącz agenta maszyny wirtualnej](security-center-enable-vm-agent.md) |Umożliwia zobaczenie, które maszyny wirtualne wymagają agenta maszyny wirtualnej. Musi być zainstalowany Agent maszyny Wirtualnej na maszynach wirtualnych, do aprowizacji poprawek, skanowania, skanowania linii bazowej i programy chroniące przed złośliwym kodem. Agent maszyny wirtualnej jest instalowany domyślnie w przypadku maszyn wirtualnych wdrażanych z poziomu portalu Azure Marketplace. Informacje na temat instalowania agenta maszyny wirtualnej można znaleźć w artykule [Agent maszyny wirtualnej i rozszerzenia — część 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/). |
| [Zastosuj szyfrowanie dysków](security-center-apply-disk-encryption.md) |Zaleca szyfrowanie dysków maszyny wirtualnej przy użyciu usługi Azure Disk Encryption (maszyny wirtualne z systemami Windows i Linux). Szyfrowanie jest zalecane zarówno w przypadku woluminów z systemem operacyjnym, jak i z danymi maszyny wirtualnej. |
| [Podawanie szczegółów dotyczących kontaktu ds. zabezpieczeń](security-center-provide-security-contact-details.md) |Informacje kontaktowe, zaleca, aby podać zabezpieczeń dla każdej subskrypcji. Informacje kontaktowe są e-mail adres i numer telefonu. Informacje są używane z Tobą, jeśli nasz zespół ds. zabezpieczeń wykryje, że Twoje zasoby zostały naruszone. |
| [Aktualizacja wersji systemu operacyjnego](security-center-update-os-version.md) |Zaleca się, zaktualizować wersję systemu operacyjnego (OS) dla usługi w chmurze do najnowszej wersji dostępnych dla rodziny systemów operacyjnych.  Aby dowiedzieć się więcej o usługach Cloud Services, zobacz [Omówienie usług w chmurze](../cloud-services/cloud-services-choose-me.md). |
| [Funkcja oceny luk w zabezpieczeniach nie jest zainstalowana](security-center-vulnerability-assessment-recommendations.md) |Zaleca się zainstalowanie na maszynie wirtualnej rozwiązania do oceny luk w zabezpieczeniach. |
| [Korygowanie luk w zabezpieczeniach](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Umożliwia wyświetlenie luk w zabezpieczeniach systemu i aplikacji wykrytych przez rozwiązanie do oceny luk w zabezpieczeniach zainstalowane na maszynie wirtualnej. |
| [Włączanie szyfrowania dla konta usługi Azure Storage](security-center-enable-encryption-for-storage-account.md) | Zaleca się, zostanie włączone szyfrowanie usługi Azure Storage dla danych magazynowanych. Szyfrowanie usługi Storage (SSE) działa przez szyfrowanie danych podczas są zapisywane do magazynu platformy Azure i odszyfrowuje przed pobierania. SSE jest obecnie dostępna tylko w przypadku usługi Azure Blob service i może służyć do blokowych obiektów blob, stronicowe obiekty BLOB i uzupełnialnych obiektów blob. Aby dowiedzieć się więcej, zobacz [szyfrowanie usługi Storage dla danych magazynowanych](../storage/common/storage-service-encryption.md).</br>Funkcja SSE jest obsługiwana tylko na kontach magazynu usługi Resource Manager. |
| [Włączyć kontrolki adaptacyjne aplikacji](security-center-adaptive-application.md) | Zaleca się zastosowanie adaptacyjne kontrole aplikacji na maszynach wirtualnych Windows. Ta funkcja jest dostępna w warstwie standardowa usługi Security Center. Zobacz [cennik](security-center-pricing.md), aby dowiedzieć się więcej na temat warstw cenowych usługi Security Center. |
| Usługa App Service powinny być dostępne tylko za pośrednictwem protokołu HTTPS | Zaleca się ograniczenie dostępu do usługi App Service przy użyciu protokołu HTTPS tylko. |
| Gniazda sieci Web powinny być wyłączone dla aplikacji sieci Web| Zaleca się dokładnie przejrzyj użycie gniazda sieci Web w aplikacjach sieci web.  Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa. |
| Użyj domen niestandardowych dla aplikacji sieci Web | Zaleca się, użyj domen niestandardowych do ochrony aplikacji sieci web przed typowymi atakami, takimi jak wyłudzanie informacji i inne ataki związane z usługą DNS. |
| Konfiguruj ograniczenia adresów IP dla aplikacji sieci Web | Zaleca się, że definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji.  Użyj ograniczeń adresów IP chroni aplikację internetową przed typowymi atakami. |
| Nie zezwalaj na wszystkie ("*") zasobów uzyskiwanie dostępu do aplikacji | Zaleca się, że nie należy ustawiać parametru WEBSITE_LOAD_CERTIFICATES na "*". Ustawienie dla parametru "*" oznacza, że wszystkie certyfikaty zostaną załadowane do magazynu certyfikatów osobiste aplikacje sieci web.  Może to prowadzić do naruszenia zasady najmniejszych uprawnień, ponieważ jest mało prawdopodobne, że witryna musi mieć dostęp do wszystkich certyfikatów w czasie wykonywania. |
| Mechanizm CORS nie powinien zezwalać na każdy zasób, uzyskiwanie dostępu do aplikacji | Zaleca się, aby zezwolić tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe pochodzenia zasobów między źródłami (cors) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do sieci web aplikacji. |
| Użyj najnowszej obsługiwanej wersji systemu .NET Framework dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji .NET Framework dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Użyj najnowszej obsługiwanej wersji środowiska Java dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji języka Java dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Użyj najnowszej obsługiwanej wersji środowiska PHP dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji języka PHP dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| [Dodawanie zapory aplikacji internetowej](security-center-add-web-application-firewall.md) |Zaleca wdrożenie zapory aplikacji sieci web (WAF) dla punktów końcowych sieci web. Dla wszelkich publicznych umożliwiający dostęp do Internetu adresu IP (adres IP na poziomie wystąpienia lub adres IP ze zrównoważonym obciążeniem) zawierającej sieciową grupę zabezpieczeń skojarzoną z portami Otwórz przychodzącego ruchu internetowego (80,443) jest wyświetlane zalecenie zapory aplikacji sieci Web.</br></br>Usługa Security Center zaleca obsługi administracyjnej zapory aplikacji sieci Web, aby pomóc Ci chronić przed atakami przeznaczone dla aplikacji sieci web na maszynach wirtualnych i w środowisku App Service Environment. App Service Environment (ASE) jest [Premium](https://azure.microsoft.com/pricing/details/app-service/) usługi opcja planu usługi Azure App Service udostępniająca w pełni izolowane, dedykowane środowisko do bezpiecznego uruchamiania aplikacji w usłudze Azure App Service. Aby dowiedzieć się więcej na temat środowiska ASE, zobacz [dokumentacja środowiska usługi App Service](../app-service/environment/intro.md).</br></br>Możesz chronić wiele aplikacji sieci web w usłudze Security Center, dodając te aplikacje do istniejących wdrożeń zapory aplikacji sieci Web. |
| [Finalizowanie ochrony aplikacji](security-center-add-web-application-firewall.md#finalize-application-protection) |Aby ukończyć konfigurację zapory aplikacji sieci Web, ruch musi zostać skierowany do urządzenia zapory aplikacji sieci Web. Tym zaleceniem kończy zmiany wymagane ustawienia. |
| Użyj najnowszej obsługiwanej wersji środowiska Node.js dla aplikacji sieci Web | Zaleca się, że używasz najnowszej wersji środowiska Node.js dla najnowszych klas zabezpieczeń. Używanie starszych klas i typów może narazić aplikację. |
| Mechanizm CORS nie powinien zezwalać na każdy zasób, dostęp do aplikacji funkcji | Zaleca się, aby zezwolić tylko wymagane domen do interakcji z aplikacją sieci web. Krzyżowe origin resource sharing (CORS) nie powinien zezwalać na wszystkie domeny uzyskiwanie dostępu do funkcji aplikacji. |
| Użyj domen niestandardowych dla aplikacji funkcji | Zaleca się, użyj domen niestandardowych do ochrony aplikacji funkcji przed typowymi atakami, takimi jak wyłudzanie informacji i inne ataki związane z usługą DNS. |
| Konfiguruj ograniczenia adresów IP dla aplikacji funkcji | Zaleca się, że definiowanie listy adresów IP, które mogą uzyskać dostęp do Twojej aplikacji. Użyj ograniczeń adresów IP chroni aplikację funkcji przed typowymi atakami. |
| Funkcja aplikacji powinny być dostępne tylko za pośrednictwem protokołu HTTPS | Zaleca się ograniczenie dostępu do aplikacji funkcji przy użyciu protokołu HTTPS tylko. |
| Zdalne debugowanie powinno zostać wyłączone dla aplikacji funkcji | Zaleca się wyłączyć debugowania dla aplikacji funkcji, jeśli nie potrzebujesz już go używać. Zdalne debugowanie wymaga porty wejściowe były otwarte w aplikacji funkcji. |
| Gniazda sieci Web powinny być wyłączone dla aplikacji funkcji | Zaleca się dokładnie przejrzyj użycie gniazda sieci Web w obrębie aplikacji funkcji. Protokół Websocket jest narażony na różnego rodzaju zagrożenia bezpieczeństwa. |
| Wyznaczenie więcej niż jednego właściciela subskrypcji | Zaleca się wyznaczenie więcej niż jednego właściciela subskrypcji w celu posiadania nadmiarowości dostępu administratora. |
| Wyznacz maksymalnie 3 właścicieli w ramach subskrypcji | Zaleca się wyznaczenie mniejszej niż 3 właścicieli subskrypcji, aby zmniejszyć ryzyko naruszenia przez właściciela z naruszonymi zabezpieczeniami. |
| Włącz usługę MFA dla kont z uprawnieniami właściciela subskrypcji | Zaleca się włączenie uwierzytelniania wieloskładnikowego (MFA) dla wszystkich kont subskrypcji z uprawnieniami administratora w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów. |
| Włącz usługę MFA dla kont z uprawnieniami do zapisu w ramach subskrypcji | Zaleca się włączenie uwierzytelniania wieloskładnikowego (MFA) dla wszystkich kont subskrypcji z uprawnieniami do zapisu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów. |
| Włącz usługę MFA dla kont z uprawnieniami do odczytu w ramach subskrypcji | Zaleca się włączenie uwierzytelniania wieloskładnikowego (MFA) dla wszystkich kont subskrypcji z uprawnieniami do odczytu w celu zapobiegania naruszeniom zabezpieczeń kont lub zasobów. |
| Usuń konta zewnętrzne z uprawnieniami do odczytu z subskrypcji | Zaleca się, usuń konta zewnętrzne z uprawnieniami do odczytu z subskrypcji, aby uniknąć niemonitorowanego dostępu. |
| Usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji | Zaleca się, usuń konta zewnętrzne z uprawnieniami do zapisu z subskrypcji, aby uniknąć niemonitorowanego dostępu. |
| Usuń konta zewnętrzne z uprawnieniami właściciela z subskrypcji | Zaleca się, usuń konta zewnętrzne z uprawnieniami właściciela z subskrypcji, aby uniknąć niemonitorowanego dostępu. |
| Usuń przestarzałe konta z subskrypcji | Zaleca, które można usunąć przestarzałe konta z subskrypcji. |
| Usuń przestarzałe konta z uprawnieniami właściciela z subskrypcji | Zaleca, które można usunąć przestarzałe konta z uprawnieniami właściciela z subskrypcji. |

### <a name="apply-recommendations"></a>Stosowanie zalecenia
Po zapoznaniu się z wszystkich zaleceń decyzję, który co należy najpierw zastosować. Firma Microsoft zaleca używanie ocenę ważności, ponieważ główny parametr do oceny, zalecenia, które powinny być stosowane najpierw.

W tabeli powyższe zalecenia wybierz zalecenie i przeprowadzenie jej jako przykładu sposobu stosowania zalecenia.

## <a name="next-steps"></a>Kolejne kroki
W tym dokumencie Przedstawiliśmy zalecenia dotyczące zabezpieczeń w usłudze Security Center. Aby dowiedzieć się więcej na temat Centrum zabezpieczeń, zobacz następujące artykuły:

* [Ustawianie zasad zabezpieczeń w usłudze Azure Security Center](security-center-azure-policy.md) — informacje o sposobie konfigurowania zasad zabezpieczeń dla subskrypcji platformy Azure i grup zasobów.
* [Monitorowanie kondycji zabezpieczeń w usłudze Azure Security Center](security-center-monitoring.md) — informacje na temat monitorowania kondycji zasobów platformy Azure.
* [Reagowanie na alerty zabezpieczeń w Centrum zabezpieczeń Azure i zarządzanie nimi](security-center-managing-and-responding-alerts.md) — Dowiedz się, jak zarządzać i reagować na alerty zabezpieczeń.
* [Monitorowanie rozwiązań partnerskich w usłudze Azure Security Center](security-center-partner-solutions.md) — informacje na temat monitorowania stanu kondycji rozwiązań partnerskich.
* [Azure Security Center — często zadawane pytania](security-center-faq.md) — odpowiedzi na często zadawane pytania dotyczące korzystania z usługi.
* [Blog Azure Security](https://blogs.msdn.com/b/azuresecurity/) — wpisy na blogu dotyczące zabezpieczeń i zgodności platformy Azure.

<!--Image references-->
[1]: ./media/security-center-recommendations/recommendations-tile.png
[2]: ./media/security-center-recommendations/filter-recommendations.png
