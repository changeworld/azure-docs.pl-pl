---
title: Znajdowanie raportów aktywności użytkowników w witrynie Azure Portal | Dokumenty firmy Microsoft
description: Dowiedz się, gdzie raporty aktywności użytkowników usługi Azure Active Directory znajdują się w witrynie Azure Portal.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d356f8c10ae3770d9f4ade100ab0496ee58d772f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008210"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Znajdowanie raportów dotyczących aktywności w witrynie Azure Portal

W tym artykule dowiesz się, jak znaleźć raporty aktywności użytkowników usługi Azure Active Directory (Azure AD) w witrynie Azure portal.

## <a name="audit-logs-report"></a>Raport dotyczący dzienników inspekcji

Raport dzienników inspekcji łączy kilka raportów dotyczących działań aplikacji w jednym widoku dla raportowania kontekstowego. Aby uzyskać dostęp do raportu dzienników inspekcji:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Wybierz katalog z prawego górnego rogu, a następnie wybierz blok **usługi Azure Active Directory** z lewego okienka nawigacji.
3. Wybierz **dzienniki inspekcji** z sekcji **Działania** bloku usługi Azure Active Directory. 

    ![Dzienniki inspekcji](./media/howto-find-activity-reports/482.png "Dzienniki inspekcji")

Raport dzienników inspekcji konsoliduje następujące raporty:

* Raport z inspekcji
* Działania związane z resetowaniem haseł
* Działanie rejestracji resetowania hasła
* Działanie grup samoobsługowych
* Zmiany nazwy grupy usługi Office365
* Działanie inicjowania obsługi administracyjnej konta
* Stan nałożenia hasła
* Błędy aprowizacji kont

### <a name="filtering-on-audit-logs"></a>Filtrowanie w dziennikach inspekcji

Zaawansowane filtrowanie można użyć w raporcie inspekcji, aby uzyskać dostęp do określonej kategorii danych inspekcji, określając je w filtrze **Kategoria.** Na przykład, aby wyświetlić wszystkie działania związane z użytkownikami, wybierz kategorię **UserManagement.** 

Kategorie obejmują:

- Wszystkie
- AdministrativeUnit
- Manakowanie aplikacji
- Uwierzytelnianie
- Autoryzacja
- Kontakt
- Urządzenie
- DeviceConfiguration
- KatalogManagement
- UprawnienieRządzanie
- Rządzanie grupami
- Inne
- Zasady
- Zagospodarowanie zasobami
- Zarządzanie rolą
- UserManagement (UserManagement)

Można również filtrować w określonej usłudze za pomocą filtru rozwijanego **Usługa.** Na przykład, aby uzyskać wszystkie zdarzenia inspekcji związane z samoobsługowym zarządzaniem hasłami, wybierz filtr **Samoobsługowe zarządzanie hasłami.**

Usługi obejmują:

- Wszystkie
- Przeglądy dostępu
- Aprowizacja kont 
- SSO aplikacji
- Metody uwierzytelniania
- B2C
- Dostęp warunkowy
- Katalog podstawowy
- Zarządzanie uprawnieniami
- Identity Protection
- Zaproszeni użytkownicy
- PIM
- Samoobsługowe zarządzanie grupami
- Samoobsługowe zarządzanie hasłami
- Warunki użytkowania

## <a name="sign-ins-report"></a>Raport dotyczący logowań 

Widok **logowania** zawiera wszystkie logowania użytkowników, a także raport **Użycie aplikacji.** Można również wyświetlić informacje o użytkowaniu aplikacji w sekcji **Zarządzanie** w przeglądzie **aplikacji przedsiębiorstwa.**

Aby uzyskać dostęp do raportu logowania:

1. Przejdź do [witryny Azure portal](https://portal.azure.com).
2. Wybierz katalog z prawego górnego rogu, a następnie wybierz blok **usługi Azure Active Directory** z lewego okienka nawigacji.
3. Wybierz **signins** z sekcji **Działania** bloku usługi Azure Active Directory. 

    ![Widok logowania](./media/howto-find-activity-reports/483.png "Widok logowania")


### <a name="filtering-on-application-name"></a>Filtrowanie według nazwy aplikacji

Raport logowania służy do wyświetlania szczegółów dotyczących użycia aplikacji, filtrując nazwę użytkownika lub nazwę aplikacji.

![Strona Filtrowanie zdarzeń logowania](./media/howto-find-activity-reports/07.png "Strona Filtrowanie zdarzeń logowania")

## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

### <a name="anomalous-activity-reports"></a>Nietypowe raporty aktywności

Raporty nietypowych działań zawierają informacje na temat wykrywania ryzyka związanego z zabezpieczeniami, które usługa Azure AD może wykrywać i raportować.

W poniższej tabeli wymieniono raporty zabezpieczeń działań nietypowych usługi Azure AD i odpowiadające im typy wykrywania ryzyka w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Wykrywanie ryzyka usługi Azure Active Directory](concept-risk-events.md).  


| Raport aktywności nietypowej usługi Azure AD |  Typ wykrywania ryzyka ochrony tożsamości|
| :--- | :--- |
| Użytkownicy z ujawnionymi poświadczeniami | Wyciekające poświadczenia |
| Nieregularne działania związane z logowaniem | Niemożliwa podróż do nietypowych lokalizacji |
| Logowania z urządzeń, które mogą być zainfekowane | Logowania z zainfekowanych urządzeń|
| Logowania z nieznanych źródeł | Logowania z anonimowych adresów IP |
| Logowania z adresów IP związanych z podejrzanymi działaniami | Logowania z adresów IP związanych z podejrzanymi działaniami |
| - | Logowania z nieznanych lokalizacji |

Następujące raporty zabezpieczeń nieprawidłowych działań usługi Azure AD nie są uwzględniane jako wykrywanie ryzyka w witrynie Azure portal:

* Logowania po wielokrotnych niepowodzeniach
* Logowania z wielu lokalizacji geograficznych


### <a name="detected-risk-detections"></a>Wykryte wykrywanie ryzyka

Dostęp do raportów dotyczących wykrytych wykrywania zagrożeń można uzyskać w sekcji **Zabezpieczenia** bloku **Usługi Azure Active Directory** w [witrynie Azure portal](https://portal.azure.com). Wykryte wykrywanie ryzyka są śledzone w następujących raportach:   

- [Narażeni użytkownicy](concept-user-at-risk.md)
- [Ryzykowne logowania](concept-risky-sign-ins.md)

    ![Raporty dotyczące zabezpieczeń](./media/howto-find-activity-reports/04.png "Raporty dotyczące zabezpieczeń")

## <a name="troubleshoot-issues-with-activity-reports"></a>Rozwiązywanie problemów z raportami aktywności

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Brakujące dane w pobranych dziennikach aktywności

#### <a name="symptoms"></a>Objawy 

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Przyczyna

Podczas pobierania dzienników aktywności w witrynie Azure portal, możemy ograniczyć skalę do 250000 rekordów, posortowane według najnowszych najpierw. 

#### <a name="resolution"></a>Rozwiązanie

Można wykorzystać [interfejsy API raportowania usługi Azure AD](concept-reporting-api.md), aby pobrać do miliona rekordów z dowolnego okresu.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Brak danych inspekcji dla ostatnich akcji w witrynie Azure portal

#### <a name="symptoms"></a>Objawy

W witrynie Azure Portal wykonano pewne akcje, które powinny zostać odzwierciedlone w dziennikach inspekcji w bloku `Activity logs > Audit Logs`, ale nie można ich znaleźć.

 ![Raportowanie](./media/troubleshoot-missing-audit-data/01.png)
 
#### <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dziennikach aktywności. W poniższej tabeli wyliczono wartości opóźnień dla dzienników aktywności. 

| Raport | &nbsp; | Opóźnienie (P95) | Opóźnienie (P99) |
|--------|--------|---------------|---------------|
| Inspekcja katalogu | &nbsp; | 2 min | 5 min |
| Aktywność związana z logowaniem | &nbsp; | 2 min | 5 min | 

#### <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do dwóch godzin, a następnie sprawdź, czy akcje pojawią się w dzienniku. Jeśli dzienniki nie są widoczne nawet po dwóch godzinach, [utwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), abyśmy mogli przeanalizować tę sytuację.

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Brak dzienników ostatnich logowań użytkowników w dzienniku aktywności logowania usługi Azure AD

#### <a name="symptoms"></a>Objawy

Po ostatnim zalogowaniu się do witryny Azure Portal oczekiwano wyświetlenia dzienników logowania dla akcji w bloku `Activity logs > Sign-ins`, ale nie można ich znaleźć.

 ![Raportowanie](./media/troubleshoot-missing-audit-data/02.png)
 
#### <a name="cause"></a>Przyczyna

Akcje nie pojawiają się natychmiast w dziennikach aktywności. W poniższej tabeli wyliczono wartości opóźnień dla dzienników aktywności. 

| Raport | &nbsp; | Opóźnienie (P95) | Opóźnienie (P99) |
|--------|--------|---------------|---------------|
| Inspekcja katalogu | &nbsp; | 2 min | 5 min |
| Aktywność związana z logowaniem | &nbsp; | 2 min | 5 min | 

#### <a name="resolution"></a>Rozwiązanie

Poczekaj od 15 minut do dwóch godzin, a następnie sprawdź, czy akcje pojawią się w dzienniku. Jeśli dzienniki nie są widoczne nawet po dwóch godzinach, [utwórz bilet pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), abyśmy mogli przeanalizować tę sytuację.

### <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Nie mogę wyświetlić danych raportu obejmujących więcej niż 30 dni w witrynie Azure Portal

#### <a name="symptoms"></a>Objawy

Nie mogę wyświetlić danych logowania i inspekcji obejmujących więcej niż 30 dni w witrynie Azure Portal. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-audit-data/03.png)

#### <a name="cause"></a>Przyczyna

W zależności od licencji w obszarze akcji usługi Azure Active Directory raporty aktywności są przechowywane przez następujący okres:

| Raport           | &nbsp; |  Usługa Azure AD — warstwa Bezpłatna | Usługa Azure AD — warstwa Premium P1 | Usługa Azure AD — warstwa Premium P2 |
| ---              | ----   |  ---           | ---                 | ---                 |
| Inspekcja katalogu  | &nbsp; |   7 dni     | 30 dni             | 30 dni             |
| Aktywność związana z logowaniem | &nbsp; | Niedostępne. Możesz uzyskać dostęp do własnych logowań przez 7 dni z poziomu bloku profilu użytkownika | 30 dni | 30 dni             |

Aby uzyskać więcej informacji, zobacz [Azure Active Directory report retention policies (Zasady przechowywania raportów w usłudze Azure Active Directory)](reference-reports-data-retention.md).  

#### <a name="resolution"></a>Rozwiązanie

Dostępne są dwie opcje przechowywania danych przez czas dłuższy niż 30 dni. Możesz użyć [interfejsów API raportowania usługi Azure AD](concept-reporting-api.md) do programowego pobierania danych i przechowywania ich w bazie danych. Alternatywnie możesz zintegrować dzienniki inspekcji w systemie SIEM innej firmy, takim jak Splunk lub SumoLogic.

## <a name="next-steps"></a>Następne kroki

* [Omówienie dzienników inspekcji](concept-audit-logs.md)
* [Omówienie logowania](concept-sign-ins.md)
* [Przegląd zdarzeń ryzykownych](concept-risk-events.md)
