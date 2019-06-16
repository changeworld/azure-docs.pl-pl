---
title: Wycofanie usługi Security Center oferuje (2019 lipca) | Dokumentacja firmy Microsoft
description: W tym artykule opisano funkcje w usłudze Security Center, która zostanie wycofana 31 lipca 2019 r.
services: security-center
author: yoavfrancis
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.date: 4/16/2019
ms.author: yoafr
ms.openlocfilehash: 069345f9c2d0fff0b580365153d8be13bb4ba204
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "65952140"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Wycofanie funkcje usługi Security Center (lipiec 2019 r.)

Wprowadziliśmy kilka [ulepszenia](https://azure.microsoft.com/updates/?product=security-center) do usługi Azure Security Center w ciągu ostatnich sześciu miesięcy.
Dzięki takim funkcjom ulepszone firma Microsoft one usuwanie niektóre funkcje nadmiarowy i powiązanych interfejsów API w usłudze Security Center na 31 lipca 2019 r.  

Większość tych funkcji Ustępujący można zastąpić za pomocą nowych funkcji w usłudze Azure Security Center i Azure Log Analytics. Inne funkcje można zaimplementować przy użyciu [Azure przez wartownika (wersja zapoznawcza)](https://azure.microsoft.com/services/azure-sentinel/).

Funkcje usługi Security Center, które wkrótce zostaną wycofane, obejmują:

- [Pulpit nawigacyjny zdarzeń](#menu_events)
- [Wpis menu wyszukiwania](#menu_search)
- [Widok klasyczny tożsamość i dostęp łącze na tożsamość i dostęp (wersja zapoznawcza)](#menu_classicidentity)
- [Zdarzenia zabezpieczeń mapy przycisku na mapie alerty zabezpieczeń (wersja zapoznawcza)](#menu_securityeventsmap)
- [Niestandardowe reguły alertów (wersja zapoznawcza)](#menu_customalerts)
- [Badanie przycisku w alertach zabezpieczeń, ochrony przed zagrożeniami](#menu_investigate)
- [Podzbiór rozwiązania w zakresie zabezpieczeń](#menu_solutions)
- [Edytowanie konfiguracji zabezpieczeń dla zasad zabezpieczeń](#menu_securityconfigurations)
- [Zabezpieczenia i inspekcja w pulpicie nawigacyjnym (pierwotnie używanym w portalu pakietu OMS) dla obszarów roboczych usługi Log Analytics](#menu_securityomsdashboard)

Ten artykuł zawiera szczegółowe informacje dotyczące każdej funkcji wycofane i czynności, można wykonać w celu implementowania funkcji zastępowania.

## Pulpit nawigacyjny zdarzeń<a name="menu_events"></a>

Usługa Security Center używa programu Microsoft Monitoring Agent do zbierania różnych konfiguracji związanych z zabezpieczeniami i zdarzeń z maszyn. Przechowuje te zdarzenia w obszarach roboczych. [Pulpit nawigacyjny zdarzeń](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umożliwia wyświetlanie tych danych i zapewnia punkt wejścia do usługi Log Analytics.

Firma Microsoft przechodzisz na emeryturę pulpit nawigacyjny zdarzeń, który pojawia się po wybraniu obszaru roboczego:

![Pulpit nawigacyjny zdarzeń][2]

### <a name="events-dashboard---the-new-experience"></a>Pulpit nawigacyjny zdarzeń — nowe środowisko

Firma Microsoft zachęca do natywnych możliwości usługi Azure Log Analytics umożliwia wyświetlanie istotnych zdarzeń w obszarach roboczych.

Jeśli utworzono niestandardowe istotnych zdarzeń w usłudze Security Center, będzie dostępny. W usłudze Log Analytics, przejdź do **wybierz obszar roboczy** > **zapisane wyszukiwania**. Dane nie będą utracone lub zmodyfikowany. Natywne istotnych zdarzeń są również dostępne w tym samym ekranie w usłudze Log Analytics.

![Zapisane wyszukiwania obszaru roboczego][3]

## Wpis menu wyszukiwania<a name="menu_search"></a>

Usługi Azure Security Center aktualnie używa wyszukiwania dzienników usługi Azure Monitor, aby pobrać i przeanalizować danych zabezpieczeń. Ten ekran służy jako okna do strony wyszukiwania usługi Log Analytics i umożliwia użytkownikom uruchamianie zapytań wyszukiwania w ich wybranego obszaru roboczego. Aby uzyskać więcej informacji, zobacz [wyszukiwania w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Firma Microsoft przechodzisz na emeryturę to okno wyszukiwania:

![Strona wyszukiwania][4]

### <a name="search-menu-entry---the-new-experience"></a>Wpis menu wyszukiwania — nowe środowisko

Firma Microsoft zachęca do natywnych możliwości usługi Azure Log Analytics umożliwia wykonywania zapytań przeszukiwania w obszarach roboczych. Przejdź do usługi Azure Log Analytics i wybierz **dzienniki**.

![Usługa log Analytics rejestruje strony][5]

## Klasyczne tożsamość i dostęp (wersja zapoznawcza)<a name="menu_classicidentity"></a>

Środowisko klasycznego tożsamości i dostępu w usłudze Security Center przedstawia obecnie pulpit nawigacyjny tożsamości i dostępu do informacji w usłudze Log Analytics. Aby wyświetlić ten pulpit nawigacyjny:

1. Wybierz **Wyświetl klasyczny obszar tożsamość i dostęp**.

   ![Strona tożsamości][6]

1. Widok **pulpit nawigacyjny tożsamości i dostępu**.

    ![Strona tożsamości — wybór obszaru roboczego][7]

1. Wybierz obszar roboczy, aby otworzyć **tożsamość i dostęp** pulpitu nawigacyjnego w usłudze Log Analytics, aby wyświetlić tożsamość i dostęp do informacji w obszarze roboczym.

   ![Strona tożsamości — pulpit nawigacyjny][8]

Firma Microsoft przechodzisz na emeryturę wszystkie trzy ekrany wyświetlane w poprzednich krokach. Dane pozostaną dostępne w rozwiązaniu zabezpieczenia usługi Log Analytics i nie będzie modyfikację lub usunięta.

### <a name="classic-identity--access-preview---the-new-experience"></a>Klasyczny obszar tożsamość i dostęp (wersja zapoznawcza) — nowe środowisko

Pulpit nawigacyjny usługi Log Analytics wykazało szczegółowych informacji w jednym obszarze roboczym. Jednak natywnych możliwości usługi Security Center zapewniają wgląd we wszystkie subskrypcje i wszystkich obszarów roboczych skojarzonych z nimi. Możesz uzyskać dostęp łatwy — w celu użycia widoku, który pozwala skupić się na ważnych zagadnieniach wraz z zaleceniami określany zgodnie z ich bezpieczne wynik.

Wszystkie funkcje **tożsamość i dostęp** pulpitu nawigacyjnego w usłudze Log Analytics można osiągnąć przez wybranie **tożsamość i dostęp (wersja zapoznawcza)** w usłudze Security Center.

![Strona tożsamości — klasyczne środowisko wycofywania][9]

## Mapy zdarzeń związanych z zabezpieczeniami<a name="menu_securityeventsmap"></a>

Usługa Security Center zapewnia [Mapa alertów zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) ułatwiają identyfikację zagrożenia bezpieczeństwa. **Przejdź do mapy zdarzeń zabezpieczeń** przycisku na tej mapie zostanie otwarty pulpit nawigacyjny, który służy do wyświetlania zdarzeń związanych z zabezpieczeniami pierwotnych w wybranym obszarze roboczym.

Firma Microsoft jest usunięcie **przejdź do mapy zdarzeń zabezpieczeń** przycisk i pulpit nawigacyjny dla obszaru roboczego.

![Przycisk Mapa - alertów zabezpieczeń][10]

Po wybraniu **przejdź do mapy zdarzeń zabezpieczeń** przycisku, Otwórz pulpit nawigacyjny analizy zagrożeń. Firma Microsoft przechodzisz na emeryturę pulpit nawigacyjny analizy zagrożeń.  

![Pulpit nawigacyjny analizy zagrożeń][11]

Możesz wybrać obszar roboczy, aby wyświetlić jego pulpit nawigacyjny analizy zagrożeń, należy otworzyć ekran mapy (wersja zapoznawcza) alerty zabezpieczeń w usłudze Log Analytics. Firma Microsoft przechodzisz na emeryturę ten ekran.

![Mapa alertów zabezpieczeń w usłudze Log Analytics][12]

Istniejące dane pozostaną dostępne w rozwiązaniu zabezpieczenia usługi Log Analytics i nie będzie można modyfikować ani usuwać.

### <a name="security-events-map---the-new-experience"></a>Mapy zdarzeń zabezpieczeń — nowe środowisko

Zachęcamy do korzystania z funkcji mapy alerty utworzone w usłudze Security Center: **Mapa alertów zabezpieczeń (wersja zapoznawcza)** . Ta funkcja zapewnia zoptymalizowane środowisko i działa we wszystkich subskrypcji i obszarów roboczych skojarzone. Jego daje ogólny widok w środowisku i nie jest koncentruje się na jeden obszar roboczy.

## Niestandardowe reguły alertów (wersja zapoznawcza)<a name="menu_customalerts"></a>

Jesteśmy [wycofanie niestandardowe alerty środowisko](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) na 30 czerwca 2019 r, ponieważ Trwa wycofywanie jej podstawowej infrastruktury. W międzyczasie możesz edytować istniejące niestandardowe reguły alertów, ale nie ma możliwość dodawania nowych. Firma Microsoft zaleca, aby włączyć [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/) automatycznie migracji istniejących alertów i utworzyć nowe. Alternatywnie można utworzyć alerty za pomocą alertów dzienników usługi Azure Monitor.

Aby zachować istniejące alerty i przeprowadzić ich migrację do platformy Azure przez wartownika:

1. Otwórz przez wartownika platformy Azure, a następnie wybierz obszar roboczy, w którym przechowywane są Twoje niestandardowe alerty.
1. Wybierz **Analytics** z menu, aby automatycznie migrować alerty.

![Niestandardowe alerty][13]

Jeśli użytkownik nie chce się na platformie Azure przez wartownika, firma Microsoft zachęca do tworzenie alertów za pomocą alertów dzienników usługi Azure Monitor. Aby uzyskać instrukcje, zobacz [tworzenie, wyświetlanie i zarządzanie alerty dzienników przy użyciu usługi Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) i [alerty dzienników w usłudze Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

Aby uzyskać więcej informacji na temat wycofania niestandardowe alerty, zobacz [niestandardowe reguły alertów w usłudze Azure Security Center (wersja zapoznawcza)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Badanie alertów zabezpieczeń<a name="menu_investigate"></a>

[Funkcja badanie](https://docs.microsoft.com/azure/security-center/security-center-investigation) w Centrum zabezpieczeń ułatwia klasyfikowanie potencjalne zdarzenie naruszenia zabezpieczeń. Ta funkcja pozwala zrozumieć zakres incydentu i śledzenie głównej przyczyny problemu. Teraz możemy usuwania tej funkcji w usłudze Security Center, ponieważ jest zastępowany przy użyciu udoskonalony interfejs użytkownika w [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/).

![Zdarzenie naruszenia zabezpieczeń][14]

Po wybraniu **zbadaj** przycisk **zdarzenie naruszenia zabezpieczeń** ekranu, Otwórz pulpit nawigacyjny badań (wersja zapoznawcza) w usłudze Log Analytics. Firma Microsoft przechodzisz na emeryturę pulpitu nawigacyjnego badania.  

Istniejące dane pozostaną dostępne w rozwiązaniu zabezpieczenia usługi Log Analytics i nie będzie można modyfikować ani usuwać.

![Pulpit nawigacyjny funkcji badanie w usłudze Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Badanie — nowe środowisko

Firma Microsoft zachęca do którego nastąpi przejście [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/) środowisko Zaawansowane badania. Wartownik platformy Azure udostępnia zaawansowane Przeszukuj i Badaj narzędzia możliwe pod kątem zagrożeń bezpieczeństwa w źródłach danych Twojej organizacji.  

## Podzbiór rozwiązania w zakresie zabezpieczeń<a name="menu_solutions"></a>

Usługa Security Center można włączyć [zintegrowanych rozwiązań zabezpieczeń na platformie Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Firma Microsoft przechodzisz na emeryturę następujące rozwiązania partnerskie z usługi Security Center. Te rozwiązania są włączone w [Azure przez wartownika](https://azure.microsoft.com/services/azure-sentinel/) wraz z liczbą dodatkowych źródeł danych.

- [Następnej generacji zapory i sieci web zapory rozwiązania aplikacji](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integracja rozwiązań zabezpieczeń, które obsługują Common Event Format (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Po wycofaniu nie będzie mógł dodawać ani modyfikować żadnych typów rozwiązań wymienionych w powyższej liście, z poziomu interfejsu użytkownika lub interfejsu API.

Jeśli masz istniejące powiązanych ze sobą rozwiązań, firma Microsoft zachęca do przenieść do platformy Azure przez wartownika.

![Bezpieczeństwo centrów rozwiązania][16]

## Edytowanie konfiguracji zabezpieczeń dla zasad zabezpieczeń<a name="menu_securityconfigurations"></a>

Usługa Azure Security Center monitoruje konfiguracji zabezpieczeń, stosując zestaw [ponad 150 zalecanych reguł](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335). dotyczących wzmacniania ochrony systemu operacyjnego. Te reguły dotyczą zapory, inspekcje, zasady dotyczące haseł i inne. Jeśli okaże się, że maszyna ma konfigurację podatną na zagrożenia, usługa Security Center wygeneruje rekomendację dotyczącą zabezpieczeń. [Ekran konfiguracji zabezpieczeń edycji](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) pozwala klientom dostosowywać domyślna konfiguracja zabezpieczeń systemu operacyjnego w usłudze Security Center.

Firma Microsoft przechodzisz na emeryturę tę funkcję wersji zapoznawczej. Jeśli po dacie wycofania chcesz resetować swoje konfiguracje zabezpieczeń do wartości domyślnych, możesz to zrobić za pomocą programu Powershell i interfejsów API przy użyciu [postępując zgodnie z instrukcjami](https://aka.ms/ascresetsecurityconfigurations)

![Edytowanie konfiguracji zabezpieczeń][17]

### <a name="edit-security-configurations---the-new-experience"></a>Edytowanie konfiguracji zabezpieczeń — nowe środowisko

Firma Microsoft zamierzasz włączyć usługę Security Center w celu obsługi [agenta konfiguracji gościa](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Takiej aktualizacji pozwoli znacznie bogatszy zestaw funkcji, łącznie z obsługą systemów operacyjnych coraz integracji zasady gościa Azure w przypadku konfiguracji gościa. Po włączeniu tych zmian, także będziesz mieć możliwość kontrolowania konfiguracje na dużą skalę i automatycznie stosować je do nowych zasobów.

## Zabezpieczenia i inspekcja pulpit nawigacyjny dla obszarów roboczych usługi Log Analytics<a name="menu_securityomsdashboard"></a>

Pulpit nawigacyjny zabezpieczenia i inspekcja pierwotnie był używany w portalu pakietu OMS. W usłudze Log Analytics pulpit nawigacyjny zawiera omówienie-workspace zdarzeń związanych z zabezpieczeniami znaczące zagrożenia, mapa analizy zagrożeń i ocenę tożsamości i dostępu zdarzeń związanych z zabezpieczeniami, zapisane w obszarze roboczym. Firma Microsoft jest usunięcie pulpitu nawigacyjnego. Jak zaleca się już na pulpicie nawigacyjnym interfejsu użytkownika, które Radzimy umożliwia przejście do usługi Azure Security Center.

![Pulpit nawigacyjny usługi log Analytics security][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Pulpit nawigacyjny zabezpieczenia i inspekcja — nowe środowisko

Zaleca się, możesz przełączyć się do usługi Azure Security Center. Zapewnia Przegląd zabezpieczeń w tym samym w wielu subskrypcjach i obszary robocze są skojarzone z nimi, a także ustawić bardziej rozbudowane funkcji.

Można uzyskać, oryginalnym zapytań usługi Log Analytics, służące do wypełniania pulpit nawigacyjny zabezpieczenia i inspekcja w [repozytorium GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) usługi Security Center.

## <a name="next-steps"></a>Kolejne kroki

- Dowiedz się więcej o [usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Dowiedz się więcej o [Azure przez wartownika](https://docs.microsoft.com/azure/sentinel).

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
