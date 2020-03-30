---
title: Planowanie raportów & monitorowania wdrożenia — usługa Azure AD
description: W tym artykule opisano sposób planowania i wykonywania implmentation raportowania i monitorowania.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: baselden
ms.reviewer: plenzke
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad84b8910e8d4f8af9845c33c22d128e317dedc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74232101"
---
# <a name="plan-an-azure-active-directory-reporting-and-monitoring-deployment"></a>Planowanie raportowania i monitorowania usługi Azure Active Directory

Rozwiązanie do raportowania i monitorowania usługi Azure Active Directory (Azure AD) zależy od wymagań prawnych, zabezpieczeń i operacyjnych oraz istniejącego środowiska i procesów. W tym artykule przedstawiono różne opcje projektowania i przewodnik do odpowiedniej strategii wdrażania.

### <a name="benefits-of-azure-ad-reporting-and-monitoring"></a>Korzyści z raportowania i monitorowania usługi Azure AD

Raportowanie usługi Azure AD zapewnia kompleksowy widok i dzienniki aktywności usługi Azure AD w twoim środowisku, w tym zdarzenia logowania, zdarzenia inspekcji i zmiany w katalogu.

Na podstawie udostępnionych danych można:

* określić sposób, w jaki używane są twoje aplikacje i usługi.

* potencjalnych zagrożeń wpływających na zdrowie środowiska.

* rozwiązywanie problemów uniemożliwiających użytkownikom wykonanie pracy.

* uzyskaj wgląd w dane, widząc zdarzenia inspekcji zmian w katalogu usługi Azure AD.

> [!IMPORTANT]
> Monitorowanie usługi Azure AD umożliwia kierowanie dzienników generowanych przez raportowanie usługi Azure AD do różnych systemów docelowych. Możesz następnie przechowywać je na potrzeby długoterminowego użytkowania lub zintegrować je z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM) innych firm, aby uzyskać wgląd w środowisko.

Za pomocą monitorowania usługi Azure AD można kierować dzienniki do:

* konto magazynu platformy Azure do celów archiwalnych.
* Dzienniki usługi Azure Monitor, wcześniej znany jako obszar roboczy usługi Azure Log Analytics, gdzie można analizować dane, tworzyć pulpity nawigacyjne i alerty o określonych zdarzeniach.
* centrum zdarzeń platformy Azure, w którym można zintegrować z istniejącymi narzędziami SIEM, takimi jak Splunk, Sumologic lub QRadar.

> [!NOTE]
Niedawno uruchomiliśmy termin Dzienniki usługi Azure Monitor zamiast usługi Log Analytics. Dane dziennika są nadal przechowywane w obszarze roboczym usługi Log Analytics i są nadal zbierane i analizowane przez tę samą usługę analizy dzienników. Aktualizujemy terminologię, aby lepiej odzwierciedlać rolę [dzienników w usłudze Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/data-collection) Zobacz [zmiany terminologii usługi Azure Monitor,](https://docs.microsoft.com/azure/azure-monitor/azure-monitor-rebrand) aby uzyskać szczegółowe informacje.

[Dowiedz się więcej o zasadach przechowywania raportów](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention).

### <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencjonowanie i wymagania wstępne dotyczące raportowania i monitorowania usługi Azure AD

Aby uzyskać dostęp do dzienników logowania usługi Azure AD, musisz mieć licencję premium usługi Azure AD.

Szczegółowe informacje dotyczące funkcji i licencjonowania znajdują się w [przewodniku po cenach usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

Aby wdrożyć monitorowanie i raportowanie usługi Azure AD, potrzebujesz użytkownika, który jest administratorem globalnym lub administratorem zabezpieczeń dla dzierżawy usługi Azure AD.

W zależności od miejsca docelowego danych dziennika, będziesz potrzebować jednej z następujących czynności:

* Konto usługi Azure Storage, do którego masz uprawnienia ListKeys. Zalecamy używanie konta magazynu ogólnego, a nie konta magazynu obiektów blob. Aby uzyskać informacje o cenach magazynu, zobacz [kalkulator cen usługi Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Obszar nazw usługi Azure Event Hubs do integracji z rozwiązaniami SIEM innych firm.

* Obszar roboczy usługi Azure Log Analytics do wysyłania dzienników do dzienników usługi Azure Monitor.

## <a name="plan-an-azure-reporting-and-monitoring-deployment-project"></a>Planowanie projektu raportowania i monitorowania platformy Azure

W tym projekcie zdefiniujesz odbiorców, którzy będą wykorzystywać i monitorować raporty, oraz zdefiniujesz architekturę monitorowania usługi Azure AD.

### <a name="engage-the-right-stakeholders"></a>Zaangażuj odpowiednich interesariuszy

Gdy projekty technologiczne nie powiodą się, zazwyczaj robią to z powodu niedopasowanych oczekiwań dotyczących wpływu, wyników i odpowiedzialności. Aby uniknąć tych pułapek, [upewnij się, że angażujesz odpowiednie zainteresowane strony.](https://aka.ms/deploymentplans) Należy również zapewnić, aby role zainteresowanych stron w projekcie były dobrze zrozumiałe poprzez dokumentowanie zainteresowanych stron oraz ich wkładu i odpowiedzialności w ramach projektu.

### <a name="plan-communications"></a>Planowanie komunikacji

Komunikacja ma kluczowe znaczenie dla powodzenia każdej nowej usługi. Proaktywnie komunikuj się z użytkownikami, jak zmieni się ich środowisko, kiedy się zmieni i jak uzyskać wsparcie, jeśli wystąpią problemy.

### <a name="document-your-current-infrastructure-and-policies"></a>Dokumentowanie bieżącej infrastruktury i zasad

Bieżąca infrastruktura i zasady będą napędzać projekt raportowania i monitorowania. Upewnij się, że wiesz,

* Co, jeśli w ogóle, narzędzia SIEM używasz.

* Infrastruktury platformy Azure, w tym istniejących kont magazynu i monitorowania używane.

* Zasady przechowywania w organizacji dla dzienników, w tym wszelkie odpowiednie wymagane struktury zgodności. 

## <a name="plan-an-azure-ad-reporting-and-monitoring-deployment"></a>Planowanie raportowania i monitorowania usługi Azure AD

Raportowanie i monitorowanie są używane do spełniania wymagań biznesowych, uzyskiwania wglądu w wzorce użycia i zwiększania postawy zabezpieczeń organizacji.

### <a name="business-use-cases"></a>Przypadki użycia biznesowego

* Wymagane rozwiązanie spełniające potrzeby biznesowe
* Miło, że trzeba zaspokoić potrzeby biznesowe
* Nie dotyczy

|Obszar |Opis |
|-|-|
|Przechowywanie| **Przechowywanie dziennika przez ponad 30 dni**. Ze względu na wymagania prawne lub biznesowe jest wymagane do przechowywania dzienników inspekcji i logowania dzienników usługi Azure AD dłużej niż 30 dni. |
|Analiza| **Dzienniki muszą być przeszukiwalne**. Przechowywane dzienniki muszą być przeszukiwane za pomocą narzędzi analitycznych. |
| Operational Insights| **Spostrzeżenia dla różnych zespołów**. Konieczność zapewnienia dostępu dla różnych użytkowników, aby uzyskać informacje operacyjne, takie jak użycie aplikacji, błędy logowania, samoobsługowe użycie, trendy itp. |
| Szczegółowe informacje o zabezpieczeniach| **Spostrzeżenia dla różnych zespołów**. Konieczność zapewnienia dostępu dla różnych użytkowników, aby uzyskać informacje operacyjne, takie jak użycie aplikacji, błędy logowania, samoobsługowe użycie, trendy itp. |
| Integracja w systemach SIEM      | **Integracja SIEM**. Konieczność integracji i przesyłania strumieniowego dzienników logowania usługi Azure AD i dzienników inspekcji do istniejących systemów SIEM. |

### <a name="choose-a-monitoring-solution-architecture"></a>Wybierz architekturę rozwiązania do monitorowania

Dzięki monitorowaniu usługi Azure AD można kierować dzienniki aktywności usługi Azure AD do systemu, który najlepiej odpowiada Twoim potrzebom biznesowym. Następnie można zachować je do długoterminowego raportowania i analizy, aby uzyskać wgląd w środowisko i zintegrować je z narzędziami SIEM.

#### <a name="decision-flow-chartan-image-showing-what-is-described-in-subsequent-sections"></a>Schemat blokowy decyzji![Obraz przedstawiający to, co jest opisane w kolejnych sekcjach](media/reporting-deployment-plan/deploy-reporting-flow-diagram.png)

#### <a name="archive-logs-in-a-storage-account"></a>Archiwizowanie dzienników na koncie magazynu

Routingu dzienników do konta magazynu platformy Azure, można zachować je dłużej niż domyślny okres przechowywania opisane w naszych [zasadach przechowywania.](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-reports-data-retention) Tej metody należy użyć, jeśli chcesz zarchiwizować dzienniki, ale nie trzeba integrować ich z systemem SIEM i nie potrzebujesz bieżących zapytań i analiz. Nadal możesz wyszukiwać na żądanie.

Dowiedz się, jak [kierować dane do konta magazynu](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account).

#### <a name="send-logs-to-azure-monitor-logs"></a>Wysyłanie dzienników do dzienników usługi Azure Monitor

[Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) konsolidują dane monitorowania z różnych źródeł. Zapewnia również język zapytań i aparat analizy, który zapewnia wgląd w działanie aplikacji i korzystanie z zasobów. Wysyłając dzienniki aktywności usługi Azure AD do dzienników usługi Azure Monitor, można szybko pobierać, monitorować i alertować o zebranych danych. Tej metody należy użyć, gdy nie masz istniejącego rozwiązania SIEM, które chcesz wysłać dane bezpośrednio, ale chcesz zapytań i analizy. Gdy dane są w dziennikach usługi Azure Monitor, można następnie wysłać go do Centrum zdarzeń, a stamtąd do SIEM, jeśli chcesz.

Dowiedz się, jak [wysyłać dane do dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Można również zainstalować wstępnie utworzone widoki dzienników aktywności usługi Azure AD, aby monitorować typowe scenariusze obejmujące zdarzenia logowania i inspekcji.

Dowiedz się, jak [zainstalować widoki analizy dzienników dla dzienników aktywności usługi Azure AD i używać ich](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views).

#### <a name="stream-logs-to-your-azure-event-hub"></a>Przesyłanie strumieniowe dzienników do centrum zdarzeń platformy Azure

Dzienniki routingu do centrum zdarzeń platformy Azure umożliwia integrację z narzędziami SIEM innych firm. Ta integracja pozwala połączyć dane dzienników aktywności usługi Azure AD z innymi danymi zarządzanymi przez rozwiązania SIEM, co zapewnia lepszy wgląd w środowisko. 

Dowiedz się, jak [wysyłać strumieniowo dzienniki do centrum zdarzeń](https://docs.microsoft.com//azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub).

## <a name="plan-operations-and-security-for-azure-ad-reporting-and-monitoring"></a>Planowanie operacji i zabezpieczeń dla raportowania i monitorowania usługi Azure AD

Interesariusze muszą uzyskać dostęp do dzienników usługi Azure AD, aby uzyskać wgląd w dane operacyjne. Prawdopodobnie użytkownicy obejmują członków zespołu zabezpieczeń, wewnętrznych lub zewnętrznych audytorów oraz zespół operacji zarządzania tożsamościami i dostępem.

Role usługi Azure AD umożliwiają delegowanie możliwości konfigurowania i wyświetlania raportów usługi Azure AD na podstawie twojej roli. Określ, kto w organizacji potrzebuje uprawnień do odczytywania raportów usługi Azure AD i jaka rola byłaby dla nich odpowiednia. 

Następujące role mogą odczytywać raporty usługi Azure AD:

* Administrator globalny

* Administrator zabezpieczeń

* Czytelnik zabezpieczeń

* Czytnik raportów

Dowiedz się więcej o [rolach administracyjnych usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

*Zawsze stosuj pojęcie najmniejszych uprawnień, aby zmniejszyć ryzyko naruszenia zabezpieczeń konta*. Rozważ wdrożenie [zarządzania tożsamościami uprzywilejowanymi](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) w celu dalszego zabezpieczenia organizacji.

##  

## <a name="deploy-azure-ad-reporting-and-monitoring"></a>Wdrażanie raportowania i monitorowania usługi Azure AD

W zależności od decyzji podjętych wcześniej przy użyciu wskazówek dotyczących projektu powyżej, w tej sekcji zostanie przewodnik do dokumentacji dotyczącej różnych opcji wdrażania.

### <a name="consume-and-archive-azure-ad-logs"></a>Korzystanie z dzienników usługi Azure AD i archiwizowanie ich

[Znajdowanie raportów dotyczących aktywności w witrynie Azure Portal](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-find-activity-reports)

[Archiwizuj dzienniki usługi Azure AD na koncie usługi Azure Storage](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account)

### <a name="implement-monitoring-and-analytics"></a>Wdrażanie monitoringu i analizy

[Wysyłanie dzienników do usługi Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)

[Instalowanie widoków analizy dzienników dla usługi Azure Active Directory i używanie ich](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-install-use-log-analytics-views)

[Analizowanie dzienników aktywności usługi Azure AD za pomocą dzienników usługi Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)

* [Interpretowanie schematu dzienników inspekcji w usłudze Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

* [Interpretowanie schematu dzienników logowania w usłudze Azure Monitor](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-sign-ins-log-schema)

 * [Strumieniowanie dzienników usługi Azure AD do centrum zdarzeń platformy Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub)

* [Integrate Azure AD logs with Splunk by using Azure Monitor (Integrowanie dzienników usługi Azure AD z narzędziem Splunk przy użyciu usługi Azure Monitor)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/tutorial-integrate-activity-logs-with-splunk)

* [Integrate Azure AD logs with SumoLogic by using Azure Monitor (Integrowanie dzienników usługi Azure AD z narzędziem SumoLogic przy użyciu usługi Azure Monitor)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)

 

 

## <a name="next-steps"></a>Następne kroki

Rozważ wdrożenie [zarządzania tożsamościami uprzywilejowanymi](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) 

Rozważ wdrożenie [kontroli dostępu opartej na rolach (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview)

 
