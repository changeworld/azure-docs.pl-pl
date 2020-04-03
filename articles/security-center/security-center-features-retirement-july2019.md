---
title: Funkcje Centrum Bezpieczeństwa (lipiec 2019) | Dokumenty firmy Microsoft
description: W tym artykule opisano funkcje w udziale zabezpieczeń Center, które zostały wycofane 31 lipca 2019 r.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 4a760fe1e5df7cf614a68f8fa8a05926326d3edf
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80583218"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Funkcje Centrum Bezpieczeństwa (lipiec 2019)

> [!NOTE]
> Ten dokument zawiera szczegółowe informacje o liście funkcji, które zostały wycofane z usługi Azure Security Center 31 lipca 2019 r.
>
>

W ciągu sześciu miesięcy poprzedzających lipiec 2019 r. wprowadzono kilka [ulepszeń](https://azure.microsoft.com/updates/?product=security-center) usługi Azure Security Center.
Dzięki tym ulepszonym funkcjom usunęliśmy niektóre zbędne funkcje i powiązane interfejsy API z usługi Security Center 31 lipca 2019 r.

Większość z tych wycofanych funkcji można zastąpić innymi funkcjami w usłudze Azure Security Center lub usłudze Azure Log Analytics. Inne funkcje można zaimplementować za pomocą [usługi Azure Sentinel (wersja zapoznawcza)](https://azure.microsoft.com/services/azure-sentinel/).

Wycofane funkcje Centrum zabezpieczeń obejmują:

- [Pulpit nawigacyjny zdarzeń](#menu_events)
- [Pozycja menu wyszukiwania](#menu_search)
- [Wyświetl klasyczne łącze identity & Access w u obiektu Identity and Access (wersja zapoznawcza)](#menu_classicidentity)
- [Przycisk mapa zdarzeń bezpieczeństwa na mapie alerty zabezpieczeń (wersja zapoznawcza)](#menu_securityeventsmap)
- [Niestandardowe reguły alertów (wersja zapoznawcza)](#menu_customalerts)
- [Przycisk Zbadaj w alertach zabezpieczeń ochrony przed zagrożeniami](#menu_investigate)
- [Podzbiór rozwiązań zabezpieczeń](#menu_solutions)
- [Edytowanie konfiguracji zabezpieczeń dla zasad zabezpieczeń](#menu_securityconfigurations)
- [Pulpit nawigacyjny zabezpieczeń i inspekcji (pierwotnie używany w portalu OMS) dla obszarów roboczych usługi Log Analytics](#menu_securityomsdashboard)

Ten artykuł zawiera szczegółowe informacje dotyczące każdej wycofanej funkcji i kroki, które można wykonać w celu zaimplementowania funkcji zastępczych.

## <a name="events-dashboard"></a>Pulpit nawigacyjny zdarzeń<a name="menu_events"></a>

Usługa Security Center używa agenta usługi Log Analytics do zbierania różnych konfiguracji i zdarzeń związanych z zabezpieczeniami z komputerów. Przechowuje te zdarzenia w obszarach roboczych. [Pulpit nawigacyjny zdarzeń](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) umożliwia wyświetlanie tych danych i daje punkt wejścia do usługi Log Analytics.

Wycofaliśmy pulpit nawigacyjny zdarzeń, który pojawił się po wybraniu obszaru roboczego:

![Pulpit nawigacyjny zdarzeń][2]

### <a name="events-dashboard---the-new-experience"></a>Pulpit nawigacyjny wydarzeń - nowe środowisko

Firma Microsoft zachęciła do korzystania z natywnych funkcji usługi Azure Log Analytics do wyświetlania znaczących zdarzeń w obszarach roboczych.

Jeśli utworzono niestandardowe wydarzenia godne uwagi w u centrum zabezpieczeń, będą one dostępne. W obszarze Analiza dzienników przejdź do **obszaru roboczego** > **Zapisane wyszukiwania**. Twoje dane nie zostaną utracone ani zmodyfikowane. Natywne znaczące zdarzenia są również dostępne na tym samym ekranie w usłudze Log Analytics.

![Zapisane wyszukiwania w obszarze roboczym][3]

## <a name="search-menu-entry"></a>Pozycja menu wyszukiwania<a name="menu_search"></a>

Usługa Azure Security Center obecnie używa wyszukiwania dzienników usługi Azure Monitor do pobierania i analizowania danych zabezpieczeń. Ten ekran służy jako okno do strony wyszukiwania usługi Log Analytics i umożliwia użytkownikom uruchamianie zapytań wyszukiwania w wybranym obszarze roboczym. Aby uzyskać więcej informacji, zobacz [Wyszukiwanie w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-search). Wycofaliśmy to okno wyszukiwania:

![Strona wyszukiwania][4]

### <a name="search-menu-entry---the-new-experience"></a>Szukaj pozycji menu - nowe doświadczenie

Zachęcamy do korzystania z natywnych funkcji usługi Azure Log Analytics do wykonywania zapytań wyszukiwania w obszarach roboczych. Przejdź do usługi Azure Log Analytics i wybierz pozycję **Dzienniki**.

![Strona dzienników usługi Log Analytics][5]

## <a name="classic-identity--access-preview"></a>Klasyczny dostęp do tożsamości & (wersja zapoznawcza)<a name="menu_classicidentity"></a>

Klasyczne środowisko dostępu do tożsamości & w usłudze Security Center pokazuje obecnie pulpit nawigacyjny informacji o tożsamości i dostępie w usłudze Log Analytics. Aby wyświetlić ten pulpit nawigacyjny:

1. Wybierz **pozycję Wyświetl klasyczny program Identity & Access**.

   ![Strona tożsamości][6]

1. Wyświetlanie **pulpitu nawigacyjnego programu Identity & Access**.

    ![Strona tożsamości — wybór obszaru roboczego][7]

1. Wybierz obszar roboczy, aby otworzyć pulpit nawigacyjny **identity & Access** w usłudze Log Analytics, aby wyświetlić informacje o tożsamości i dostępie do obszaru roboczego.

   ![Strona tożsamości — pulpit nawigacyjny][8]

Wycofaliśmy wszystkie trzy ekrany pokazane w poprzednich krokach. Twoje dane pozostają dostępne w rozwiązaniu zabezpieczeń usługi Log Analytics i nie zostały zmodyfikowane ani usunięte.

### <a name="classic-identity--access-preview---the-new-experience"></a>Classic Identity & Access (Wersja zapoznawcza) - nowe środowisko

Pulpit nawigacyjny usługi Log Analytics pokazuje szczegółowe informacje na temat jednego obszaru roboczego. Jednak natywne funkcje usługi Security Center zapewniają wgląd we wszystkie subskrypcje i wszystkie obszary robocze skojarzone z nimi. Możesz uzyskać dostęp do łatwego w użyciu widoku, który pozwala skupić się na tym, co ważne, z zaleceniami uszeregowymię według ich bezpiecznego wyniku.

Wszystkie funkcje pulpitu nawigacyjnego **dostępu do tożsamości &** w usłudze Log Analytics można uzyskać, wybierając opcję Dostęp do & tożsamości **(Wersja zapoznawcza)** w usłudze Security Center.

![Strona tożsamości - klasyczne doświadczenie emerytury][9]

## <a name="security-events-map"></a>Mapa zdarzeń bezpieczeństwa<a name="menu_securityeventsmap"></a>

Usługa Security Center udostępnia mapę alertów zabezpieczeń ułatwiające identyfikację zagrożeń bezpieczeństwa. Przycisk **Przejdź do mapy zdarzeń zabezpieczeń** na tej mapie otwiera pulpit nawigacyjny, który umożliwia wyświetlanie nieprzetworzonych zdarzeń zabezpieczeń w wybranym obszarze roboczym.

Usunęliśmy przycisk **Przejdź do mapy zdarzeń bezpieczeństwa** i pulpit nawigacyjny obszaru roboczego.

![Mapa alertów bezpieczeństwa - przycisk][10]

Po wybraniu przycisku **Przejdź do mapy zdarzeń bezpieczeństwa,** otworzył (teraz wycofany) pulpit nawigacyjny analizy zagrożeń.

![Pulpit nawigacyjny analizy zagrożeń][11]

Po wybraniu obszaru roboczego do wyświetlania jego pulpitu nawigacyjnego analizy zagrożeń, został otwarty (teraz wycofany) ekran mapy alertów zabezpieczeń (podgląd) w usłudze Log Analytics.

![Mapa alertów zabezpieczeń w usłudze Log Analytics][12]

Istniejące dane pozostają dostępne w rozwiązaniu zabezpieczeń usługi Log Analytics i nie zostały zmodyfikowane ani usunięte.

### <a name="security-events-map---the-new-experience"></a>Mapa wydarzeń bezpieczeństwa - nowe doświadczenie

Zachęcamy do korzystania z funkcji mapy alertów wbudowanej w mapę Security Center: **Security Alerts (Preview)**. Ta funkcja zapewnia zoptymalizowane środowisko i działa we wszystkich subskrypcjach i skojarzonych obszarach roboczych. Zapewnia widok wysokiego poziomu w całym środowisku i nie koncentruje się na jednym obszarze roboczym.

## <a name="custom-alert-rules-preview"></a>Niestandardowe reguły alertów (wersja zapoznawcza)<a name="menu_customalerts"></a>

Wycofaliśmy [niestandardowe alerty](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 czerwca 2019 r., ponieważ jego podstawowa infrastruktura została wycofana. Po dacie wycofania niestandardowe alerty zabezpieczeń nie są już generowane.
Zaleca się włączenie [usługi Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) i ponowne utworzenie tam alertów niestandardowych. Alternatywnie można utworzyć alerty za pomocą alertów dziennika usługi Azure Monitor.

Aby utworzyć niestandardowe alerty za pomocą usługi Azure Sentinel:

1. [Otwórz usługę Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) i wybierz obszar roboczy, w którym są przechowywane alerty niestandardowe
1. Wybierz **Analytics** z menu
1. Postępuj zgodnie z instrukcjami w poniższym [samouczku,](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) jak tworzyć niestandardowe alerty w usłudze Azure Sentinel

Jeśli nie jesteś zainteresowany przy użyciu usługi Azure Sentinel, możesz utworzyć alerty za pomocą alertów dziennika usługi Azure Monitor. Aby uzyskać instrukcje, zobacz [Tworzenie, wyświetlanie i zarządzanie alertami dziennika przy użyciu](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) alertów usługi Azure Monitor i [dziennik w usłudze Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

![Alerty niestandardowe][13]

Aby uzyskać więcej informacji na temat wycofywania alertów niestandardowych, zobacz [Niestandardowe reguły alertów w usłudze Azure Security Center (Wersja zapoznawcza)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Badanie alertów zabezpieczeń<a name="menu_investigate"></a>

Funkcja Dochodzenie w Centrum zabezpieczeń pomaga klasyfikować potencjalne zdarzenie zabezpieczeń. Funkcja pozwala zrozumieć zakres zdarzenia i wyśledzić jego główną przyczynę. Usunęliśmy tę funkcję z usługi Security Center, ponieważ została ona zastąpiona ulepszonym działaniem [usługi Azure Sentinel.](https://azure.microsoft.com/services/azure-sentinel/)

![Zdarzenie naruszenia zabezpieczeń][14]

Po wybraniu przycisku **Zbadaj** z ekranu **zdarzenia zabezpieczeń** otwórz pulpit nawigacyjny dochodzenia (podgląd) w usłudze Log Analytics. Wycofaliśmy pulpit nawigacyjny dochodzenia.

Istniejące dane pozostają dostępne w rozwiązaniu zabezpieczeń usługi Log Analytics i nie zostały zmodyfikowane ani usunięte.

![Pulpit nawigacyjny badania w usłudze Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Śledztwo - nowe doświadczenie

Zachęcamy do przejścia na [usługę Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) w celu uzyskania rozbudowanego środowiska dochodzeniowego. Usługa Azure Sentinel udostępnia zaawansowane narzędzia wyszukiwania i zapytań do wyszukiwania w środowiskach zagrożeń bezpieczeństwa w źródłach danych organizacji.

## <a name="subset-of-security-solutions"></a>Podzbiór rozwiązań zabezpieczających<a name="menu_solutions"></a>

Usługa Security Center może włączyć [zintegrowane rozwiązania zabezpieczeń na platformie Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Wycofaliśmy następujące rozwiązania partnerskie z Security Center. Te rozwiązania są włączone w [usłudze Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) wraz z szeregiem dodatkowych źródeł danych.

- [Rozwiązania zapory sieciowej nowej generacji i zapory aplikacji sieci Web](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Integracja rozwiązań zabezpieczających obsługujących wspólny format zdarzeń (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Azure AD Identity Protection](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

Po przejściu na emeryturę nie można dodać ani zmodyfikować żadnego z typów rozwiązań wymienionych na poprzedniej liście, z interfejsu użytkownika lub interfejsu API. Usługa Azure Security Center nie będzie już wykrywać żadnych nowych wystąpień tych rozwiązań partnerskich.

Jeśli masz istniejące połączone rozwiązania, zachęcamy do przejścia na usługę Azure Sentinel.

![Rozwiązania centrów bezpieczeństwa][16]

## <a name="edit-security-configurations-for-security-policies"></a>Edytowanie konfiguracji zabezpieczeń dla zasad zabezpieczeń<a name="menu_securityconfigurations"></a>

Usługa Azure Security Center monitoruje konfiguracje zabezpieczeń, stosując zestaw [ponad 150 zalecanych reguł](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) dotyczących wzmacniania ochrony systemu operacyjnego. Te reguły odnoszą się do zapór sieciowych, inspekcji, zasad haseł i innych. Jeśli okaże się, że maszyna ma konfigurację podatną na zagrożenia, usługa Security Center wygeneruje rekomendację dotyczącą zabezpieczeń. [Ekran konfiguracji zabezpieczeń Edycji](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) umożliwia klientom dostosowanie domyślnej konfiguracji zabezpieczeń systemu operacyjnego w umykarze zabezpieczeń.

Wycofaliśmy tę funkcję podglądu. Aby zresetować konfiguracje zabezpieczeń z powrotem do wartości domyślnych po dacie wycofania, należy to zrobić za pośrednictwem interfejsu API lub programu Powershell, korzystając z [poniższych instrukcji](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization).

![Edytowanie konfiguracji zabezpieczeń][17]

### <a name="edit-security-configurations---the-new-experience"></a>Edytowanie konfiguracji zabezpieczeń — nowe środowisko

Zamierzamy włączyć security center do obsługi [agenta konfiguracji gościa](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration). Taka aktualizacja pozwoli znacznie bogatszy zestaw funkcji, w tym obsługę więcej systemów operacyjnych i integracji zasad platformy Azure w gości dla konfiguracji gościa. Po włączeniu tych zmian, będziesz mieć również możliwość kontrolowania konfiguracji na dużą skalę i zastosować je do nowych zasobów automatycznie.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Pulpit nawigacyjny zabezpieczeń i inspekcji dla obszarów roboczych usługi Log Analytics<a name="menu_securityomsdashboard"></a>

Pulpit nawigacyjny zabezpieczeń i inspekcji był pierwotnie używany w portalu oms. W usłudze Log Analytics pulpit nawigacyjny zawiera omówienie obszarów roboczych istotnych zdarzeń i zagrożeń zabezpieczeń, mapę analizy zagrożeń oraz ocenę tożsamości i dostępu zdarzeń zabezpieczeń zapisanych w obszarze roboczym. Usunęliśmy pulpit nawigacyjny. Jak już zalecane w interfejsie użytkownika pulpitu nawigacyjnego, radzimy przejść do usługi Azure Security Center.

![Pulpit nawigacyjny zabezpieczeń usługi Log Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Pulpit nawigacyjny zabezpieczeń i inspekcji — nowe środowisko

Zalecamy przełączenie się do usługi Azure Security Center. Zapewnia ten sam przegląd zabezpieczeń w wielu subskrypcjach i obszarach roboczych skojarzonych z nimi oraz bogatszy zestaw funkcji.

Oryginalne zapytania usługi Log Analytics, które wypełniają pulpit nawigacyjny zabezpieczeń i inspekcji, można uzyskać w [repozytorium GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) dla usługi Security Center.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/).
- Dowiedz się więcej o [usłudze Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

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
