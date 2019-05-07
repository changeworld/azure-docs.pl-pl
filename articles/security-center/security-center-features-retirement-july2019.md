---
title: Wycofanie usługi Security Center oferuje (2019 lipca) | Dokumentacja firmy Microsoft
description: Ten artykuł szczegółowo opisuje funkcje w usłudze Security Center, która zostanie wycofana 31 lipca 2019 r.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: ac677a7f9586dbe893cbcbc480564aceac0149a0
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65147928"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Wycofanie funkcje usługi Security Center (lipiec 2019 r.)

Wprowadziliśmy kilka [ulepszenia](https://azure.microsoft.com/updates/?product=security-center) do usługi Azure Security Center w ciągu ostatnich 6 miesięcy.  
Ulepszone funkcje liczbę nadmiarowych funkcji, a także powiązanych interfejsów API, zostaną usunięte z usługi Security Center na 31 lipca 2019 r.  

Większość funkcji wycofane, można zastąpić nowe funkcje w usłudze Azure Security Center lub usługi Log Analytics; i kilka funkcji można zaimplementować przy użyciu [Azure przez wartownika (wersja zapoznawcza)](https://azure.microsoft.com/services/azure-sentinel/), niedawno zapowiadane.

Lista funkcji zostanie wycofana w usłudze Security Center obejmuje:

- [Pulpit nawigacyjny zdarzeń](#menu_events)
- [Wpis menu wyszukiwania](#menu_search)
- [Widok klasyczny tożsamość i dostęp łącze na tożsamość i dostęp (wersja zapoznawcza)](#menu_classicidentity)
- [Zdarzenia zabezpieczeń mapy przycisku na mapie alerty zabezpieczeń (wersja zapoznawcza)](#menu_securityeventsmap)
- [Niestandardowe reguły alertów (wersja zapoznawcza)](#menu_customalerts)
- [Badanie przycisku w alertach zabezpieczeń, ochrony przed zagrożeniami](#menu_investigate)
- [Podzbiór rozwiązania w zakresie zabezpieczeń](#menu_solutions)
- [Edytowanie konfiguracji zabezpieczeń dla zasad zabezpieczeń](#menu_securityconfigurations)
- [Zabezpieczenia i inspekcja pulpit nawigacyjny (pierwotnie używany w portalu pakietu OMS) w przypadku obszarów roboczych usługi Log Analytics.](#menu_securityomsdashboard)

Poniżej zawiera szczegółowe informacje dla każdej funkcji wycofane kroki należy wykonać, korzystając z funkcji zastępowania.

## Pulpit nawigacyjny zdarzeń<a name="menu_events"></a>
Usługa Security Center używa programu Microsoft Monitoring Agent do gromadzenia zabezpieczeń różnych konfiguracji związanych z i zdarzeń z maszyn i przechowuje te zdarzenia w obszarach roboczych usługi. [Pulpit nawigacyjny zdarzeń](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umożliwia wyświetlanie tych danych i zasadniczo zawiera inny punkt wejścia do usługi Log Analytics.

Idąc dalej pulpit nawigacyjny zdarzeń zostanie wycofana z użycia:

![Ekran wyboru obszar roboczy zdarzenia][1]

Pulpit nawigacyjny zdarzeń, który pojawia się po użytkownik kliknie w obszarze roboczym, zostanie również wycofana z użycia:

![Pulpit nawigacyjny zdarzeń][2]

### <a name="events-dashboard---new-experience"></a>Pulpit nawigacyjny zdarzeń — nowe środowisko

Zachęcamy klientów do natywnych możliwości Log Analytics umożliwia wyświetlanie istotnych zdarzeń na swoich obszarów roboczych.
Jeśli już utworzono niestandardowe istotnych zdarzeń z usługi Security Center, będą one dostępne za pośrednictwem Log analytics -> Wybierz obszar roboczy -> zapisane wyszukiwania. Dane nie spowoduje utraty ani zmodyfikowane. Natywne istotnych zdarzeń są również dostępne w tym samym ekranie.

![Zapisane wyszukiwania obszaru roboczego][3]

## Wpis menu wyszukiwania<a name="menu_search"></a>
Usługi Azure Security Center aktualnie używa wyszukiwania dzienników usługi Azure Monitor, aby pobrać i przeanalizować danych zabezpieczeń. Tego ekranu, zasadniczo służy jako fasada do usługi Log Analytics "[wyszukiwania](https://docs.microsoft.com/azure/security-center/security-center-search)" strony — dzięki czemu użytkownicy mogą uruchamiać zapytania wyszukiwania w ich wybranego obszaru roboczego. Idąc dalej to okno wyszukiwania zostanie wycofana z użycia:

![Strona wyszukiwania][4]

### <a name="search-menu-entry---new-experience"></a>Wyszukaj wpis menu — nowe środowisko

Zachęcamy klientów do użycia **usługi Log Analytics** natywne możliwości w celu wykonywania zapytań przeszukiwania na swoich obszarów roboczych. Aby to zrobić, ich przejdź do usługi Log analytics na platformie Azure i wybierz: “Logs”:

![Usługa log Analytics rejestruje strony][5]

## Klasyczne tożsamość i dostęp (wersja zapoznawcza)<a name="menu_classicidentity"></a>
"Klasyczny" środowisko tożsamości i dostępu w usłudze Security Center ma pod warunkiem sposób klienci będą mogli wyświetlać ich tożsamość i dostęp do powiązanych informacji w usłudze log analytics. Zostało to zrobione, postępując zgodnie z poniższym kliknięć:

Kliknij pozycję "Wyświetl klasyczny tożsamość i dostęp"

![Strona tożsamości][6]

Wraz z ekranem, czy otworzyć tę stronę "Pulpit nawigacyjny tożsamości i dostępu":

![Strona tożsamości — wybór obszaru roboczego][7]

Kliknięcie w obszarze roboczym otworzy "Tożsamościami i dostępem" log analytics pulpit nawigacyjny których klientów można wyświetlać tożsamość i dostęp do informacji w ich obszarze roboczym:

![Strona tożsamości — pulpit nawigacyjny][8]

Idąc dalej trzy ekrany powyżej zostanie wycofana. Dane pozostaną dostępne w rozwiązania w zakresie zabezpieczeń analizy dzienników i zostaną nie zmodyfikowane lub usunięte.

### <a name="classic-identity--access-preview---new-experience"></a>Klasyczny obszar tożsamość i dostęp (wersja zapoznawcza) — nowe środowisko
Oferują wgląd we wszystkie subskrypcje i wszystkich obszarów roboczych skojarzonych z nimi, w łatwy, gdy pulpit nawigacyjny analizy dziennika dostarcza szczegółowe informacje w danym obszarze roboczym tylko natywne możliwości usługi Security Center — w celu użycia widoku, który pozwala skupić się na co firmy ważne, zgodnie z bezpiecznego wynik Twojej tożsamości i dostępu — liczba zaleceń.
Wszystkie funkcje tożsamości i dostępu do pulpitu nawigacyjnego analizy dzienników jest osiągalna, wybierając pozycję "Tożsamość i dostęp (wersja zapoznawcza)" w usłudze Security Center:

![Strona tożsamości — klasyczne środowisko wycofywania][9]

## Mapy zdarzeń związanych z zabezpieczeniami<a name="menu_securityeventsmap"></a>
Usługa Security Center zapewnia [mapy](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) pomaga identyfikować zagrożenia bezpieczeństwa środowiska. Przycisk "Przejdź do mapy zdarzeń zabezpieczeń" na tej mapie prowadzi do pulpitu nawigacyjnego, który umożliwia wyświetlanie zdarzeń związanych z zabezpieczeniami pierwotnych w wybranym obszarze roboczym.
Po kliknięciu przycisku wraz z pulpitu nawigacyjnego na obszar roboczy, zostaną usunięte po zakończeniu obsługi.

![Przycisk Mapa - alertów zabezpieczeń][10]

Już dziś, kliknij pozycję "Idź do mapy zdarzeń zabezpieczeń" pulpitu nawigacyjnego analizy zagrożeń jest otwierany. Pulpit nawigacyjny analizy zagrożeń zostaną wycofane.  

![Pulpit nawigacyjny analizy zagrożeń][11]

Wybierając obszar roboczy, aby wyświetlić jego pulpit nawigacyjny analizy zagrożeń bezpieczeństwa alerty ekranu map(Preview) *w usłudze Log Analytics* jest otwarty. Ten ekran zostanie wycofana.

![Mapa alertów zabezpieczeń w usłudze Log Analytics][12]

Istniejące dane pozostaną dostępne w rozwiązania w zakresie zabezpieczeń analizy dzienników i będzie nie można modyfikować ani usuwać.

### <a name="security-events-map---new-experience"></a>Mapy zdarzeń zabezpieczeń — nowe środowisko
Firma Microsoft zachęca klienci mogą korzystać z funkcji mapy alerty, które są wbudowane w usłudze Security Center — "alertów zabezpieczeń mapy (wersja zapoznawcza)". To zapewnia zoptymalizowane środowisko i działa we wszystkich subskrypcji i obszarów roboczych skojarzone, dzięki czemu makro wyświetlać w całym środowisku i koncentruje się na jeden obszar roboczy.

## Niestandardowe reguły alertów (wersja zapoznawcza)<a name="menu_customalerts"></a>
Środowisko niestandardowe alerty będzie [wycofane](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) się 30 czerwca 2019 r ze względu na wycofanie podstawowej infrastruktury jest on oparty na. W przedziale czasu, aż do zakończenia obsługi użytkownicy będą mogli edytować istniejące niestandardowych reguł alertów, ale nie będzie można dodawać nowe. Użytkownicy doradza się włączanie [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/) alerty dzienników, uruchamiane jednym kliknięciem dołączania automatycznie migrować istniejące alerty i tworzenie nowych lub też ponownie utworzyć alerty z usługą Azure Monitor.

Aby zachować istniejące alerty i przeprowadzić ich migrację do platformy Azure przez wartownika, uruchom przez wartownika platformy Azure. Jako pierwszy krok wybierz obszar roboczy, w którym przechowywane są Twoje niestandardowe alerty, a następnie wybierz element menu "Analytics", aby automatycznie migrować alerty.

![Alerty niestandardowe][13]

Zachęcamy klientów nie interesuje Cię dołączanie do usługi Azure przez wartownika Aby ponownie utworzyć alerty za pomocą alertów dzienników usługi Azure Monitor. Aby uzyskać instrukcje, zobacz: [tworzenie, wyświetlanie i zarządzanie nimi za pomocą usługi Azure Monitor alertów usługi log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log). Aby uzyskać instrukcje dotyczące sposobu tworzenia alertów dzienników Zobacz: [Alerty dzienników w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Aby uzyskać więcej informacji na temat wycofania niestandardowe alerty, zobacz [Centrum zabezpieczeń niestandardowe alerty dokumentacji](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Badanie alertów zabezpieczeń<a name="menu_investigate"></a>
[Funkcja badanie](https://docs.microsoft.com/azure/security-center/security-center-investigation) w usłudze Security Center umożliwia klasyfikowanie, zrozumieć zakres i śledzenie głównej przyczyny potencjalnego zdarzenie naruszenia zabezpieczeń. Ta funkcja zostanie usunięta z usługi Security Center, jak jest zastępowany przy użyciu udoskonalony interfejs użytkownika w [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/).

![Zdarzenie naruszenia zabezpieczeń][14]

Po kliknięciu przycisku "Zbadaj" powyżej "badań (wersja zapoznawcza)" w usłudze Log Analytics zostanie otwarty pulpit nawigacyjny. Pulpit nawigacyjny badań zostaną wycofane.  
Istniejące dane pozostaną dostępne w rozwiązaniu zabezpieczenia usługi Log Analytics i będzie nie można modyfikować ani usuwać.

![Pulpit nawigacyjny funkcji badanie w usłudze Log Analytics][15]

### <a name="investigation---new-experience"></a>Badanie — nowe środowisko

Zachęcamy klientów do dołączenia [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/) dla zaawansowanych badać obsługiwane przez alerty myślistwo funkcji. Wartownik platformy Azure udostępnia zaawansowane myślistwo Przeszukuj i Badaj narzędzia możliwe pod kątem zagrożeń bezpieczeństwa w źródłach danych Twojej organizacji.  

## Podzbiór rozwiązania w zakresie zabezpieczeń<a name="menu_solutions"></a>

Security Center oferuje możliwość włączenia [zintegrowanych rozwiązań zabezpieczeń na platformie Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Następujące rozwiązania partnerskie będą usunięte, a obsługiwane w [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/), wraz z jeszcze większą liczbą źródeł danych.

- Następnej generacji zapory i rozwiązania zapory aplikacji internetowych (Azure przez wartownika [dokumentacji](https://docs.microsoft.com/azure/sentinel/connect-data-sources))
- Integracja rozwiązań zabezpieczeń, które obsługują typowego formatu zdarzeń (CEF) (Azure przez wartownika [dokumentacji](https://docs.microsoft.com/azure/sentinel/connect-common-event-format))
- Microsoft Advanced Threat Analytics (Azure wartownik [dokumentacji](https://docs.microsoft.com/azure/sentinel/connect-azure-atp))
- Azure AD Identity Protection (Azure wartownik [dokumentacji](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection))

Po zakończeniu obsługi użytkownicy nie będą mogli dodać nowe lub modyfikować istniejące rozwiązania połączone z typów wymienionych powyżej, zarówno w Interfejsie użytkownika, jak i interfejsu API.
Istniejące rozwiązania połączone zachęca można przenieść do platformy Azure przez wartownika do końca tego okresu.

![Bezpieczeństwo centrów rozwiązania][16]

## Edytowanie konfiguracji zabezpieczeń dla zasad zabezpieczeń<a name="menu_securityconfigurations"></a>
Usługa Azure Security Center monitoruje konfiguracji zabezpieczeń, stosując zestaw [ponad 150 zalecanych reguł](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) dotyczących wzmacniania ochrony systemu operacyjnego, w tym reguł dotyczących zapór, inspekcji, zasady dotyczące haseł i inne. Maszyny w przypadku konfiguracji na ataki, usługa Security Center generuje zalecenie dotyczące zabezpieczeń. [Ekran konfiguracji zabezpieczeń edycji](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) pozwala klientom dostosowywać domyślna konfiguracja zabezpieczeń systemu operacyjnego w usłudze Security Center.

Ta funkcja jest w wersji zapoznawczej i zostaną wycofane.

![Edytowanie konfiguracji zabezpieczeń][17]

### <a name="edit-security-configurations---new-experience"></a>Edytowanie konfiguracji zabezpieczeń — nowe środowisko

Usługa Security Center będzie obsługiwać [agenta konfiguracji gościa](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) w najbliższej przyszłości, co znacznie bogatszy zestaw funkcji — Obsługa dodatkowych systemów operacyjnych i integracja z usługą konfiguracji gościa za pomocą zasad platformy Azure (w tym zasady gościa). Zapewni to także możliwość kontrolowania na dużą skalę i automatycznie stosować na nowych zasobów.

## Zabezpieczenia i inspekcja pulpitu nawigacyjnego (pierwotnie używany w portalu pakietu OMS) w przypadku obszarów roboczych usługi Log Analytics<a name="menu_securityomsdashboard"></a>

Pulpit nawigacyjny zabezpieczeń w usłudze Log analytics zawiera omówienie-workspace zdarzenia istotnych zabezpieczeń i zagrożeń, mapa analizy zagrożeń i tożsamość i dostęp do oceny zdarzeń związanych z zabezpieczeniami, zapisane w obszarze roboczym. Pulpit nawigacyjny zostanie usunięte w przyszłości. Jak zaleca się już na pulpicie nawigacyjnym interfejsu użytkownika, naszych użytkowników są zalecane jest użycie usługi Azure Security Center przyszłości.

![Pulpit nawigacyjny usługi log analytics security][18]

### <a name="security--audit-dashboard---new-experience"></a>Pulpit nawigacyjny zabezpieczenia i inspekcja — nowe środowisko
Naszym klientom są zalecane jest użycie usługi Azure Security Center, która zawiera przegląd zabezpieczeń w tym samym w wielu subskrypcji i obszarów roboczych skojarzonych z nimi, wraz z bogatszego zestawu funkcji.

## <a name="next-steps"></a>Kolejne kroki
- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/)
- Dowiedz się więcej o [Azure przez wartownika](https://docs.microsoft.com/azure/sentinel)

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
