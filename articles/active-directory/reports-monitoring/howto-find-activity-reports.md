---
title: Znajdowanie raportów aktywności użytkownika usługi Azure Active Directory w witrynie Azure portal | Dokumentacja firmy Microsoft
description: Dowiedz się, gdzie raporty aktywności użytkownika w usłudze Azure Active Directory znajdują się w witrynie Azure portal.
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
ms.openlocfilehash: d47072713c57576abe780134792c3a5cbc27127c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60438330"
---
# <a name="find-activity-reports-in-the-azure-portal"></a>Znajdowanie raportów aktywności w witrynie Azure portal

W tym artykule dowiesz się, jak znajdowanie raportów aktywności użytkownika usługi Azure Active Directory (Azure AD) w witrynie Azure portal.

## <a name="audit-logs-report"></a>Raport dotyczący dzienników inspekcji

Raport dotyczący dzienników inspekcji łączy kilka raportów w całym działania aplikacji w jednym widoku na podstawie kontekstu raportowania. Dostęp do raportu dzienników inspekcji:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz swój katalog w prawym górnym rogu, a następnie wybierz **usługi Azure Active Directory** bloku, w okienku nawigacji po lewej stronie.
3. Wybierz **dzienniki inspekcji** z **działania** części bloku Azure Active Directory. 

    ![Dzienniki inspekcji](./media/howto-find-activity-reports/482.png "Dzienniki inspekcji")

Raport dotyczący dzienników inspekcji konsoliduje następujące raporty:

* Raport z inspekcji
* Działania związane z resetowaniem haseł
* Aktywność rejestracji resetowania haseł
* Aktywności grup samoobsługi
* Office365 Group Name Changes
* Działanie z aprowizacją kont
* Stan przerzucania hasła
* Błędy aprowizacji kont

### <a name="filtering-on-audit-logs"></a>Filtrowanie według dzienników inspekcji

Umożliwia filtrowanie Zaawansowane w raporcie inspekcji dostępu do określonej kategorii danych inspekcji, określając ją w **kategorii** filtru. Na przykład, aby wyświetlić wszystkie działania związane z użytkownikami, wybierz pozycję **UserManagement** kategorii. 

Kategorie:

- Wszyscy
- AdministrativeUnit
- ApplicationManagement
- Authentication
- Autoryzacja
- Kontakt
- Urządzenie
- Konfiguracja urządzenia
- DirectoryManagement
- EntitlementManagement
- GroupManagement
- Inne
- Zasady
- ResourceManagement
- RoleManagement
- UserManagement

Można również filtrować w określonej usługi za pomocą **usługi** filtru listy rozwijanej. Na przykład, aby uzyskać wszystkie zdarzenia inspekcji dotyczące Samoobsługowe zarządzanie hasłami, wybierz **samoobsługowego zarządzania hasłami** filtru.

Usługi to:

- Wszyscy
- Przeglądy dostępu
- Aprowizacja kont 
- Usługa rejestracji Jednokrotnej w aplikacji
- Metody uwierzytelniania
- B2C
- Dostęp warunkowy
- Katalog podstawowy
- Zarządzanie upoważnieniami
- Identity Protection
- Zaproszeni użytkownicy
- PIM
- Samoobsługowe zarządzanie grupami
- Samoobsługowe zarządzanie hasłami
- Warunki użytkowania

## <a name="sign-ins-report"></a>Raport dotyczący logowań 

**Logowania** widok zawiera wszystkie logowania użytkownika, jak również **użycia aplikacji** raportu. Również wyświetlane informacje o użyciu aplikacji w **Zarządzaj** części **aplikacje dla przedsiębiorstw** Przegląd.

Dostęp do raportów logowania:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wybierz swój katalog w prawym górnym rogu, a następnie wybierz **usługi Azure Active Directory** bloku, w okienku nawigacji po lewej stronie.
3. Wybierz **plik logowań do** z **działania** części bloku Azure Active Directory. 

    ![Widok logowania](./media/howto-find-activity-reports/483.png "wyświetlić logowania")


### <a name="filtering-on-application-name"></a>Filtrowanie według nazwy aplikacji

W raporcie logowań można użyć, aby wyświetlić szczegóły dotyczące użycia aplikacji przez filtrowanie według nazwy użytkownika lub aplikacji.

![Filtr zdarzeń logowania strony](./media/howto-find-activity-reports/07.png "strony filtr zdarzeń logowania")

## <a name="security-reports"></a>Raporty dotyczące zabezpieczeń

### <a name="anomalous-activity-reports"></a>Raporty nietypowych działań

Nietypowe działanie raporty zawierają informacje dotyczące zdarzeń ryzyka związanych z zabezpieczeniami usługi Azure AD można wykrywać i raportować.

Poniższej tabeli list usługi Azure AD nietypowych działań raporty dotyczące zabezpieczeń i odpowiednie typy zdarzeń o podwyższonym ryzyku w witrynie Azure portal. Aby uzyskać więcej informacji, zobacz [Zdarzenia o podwyższonym ryzyku w usłudze Azure Active Directory](concept-risk-events.md).  


| Raport nietypowych działań w usłudze Azure AD |  Typ zdarzenia o podwyższonym ryzyku ochrony tożsamości|
| :--- | :--- |
| Użytkownicy z ujawnionymi poświadczeniami | Ujawnione poświadczenia |
| Nieregularne działania związane z logowaniem | Niemożliwa podróż do nietypowych lokalizacji |
| Logowania z urządzeń, które mogą być zainfekowane | Logowania z zainfekowanych urządzeń|
| Logowania z nieznanych źródeł | Logowania z anonimowych adresów IP |
| Logowania z adresów IP związanych z podejrzanymi działaniami | Logowania z adresów IP związanych z podejrzanymi działaniami |
| - | Logowania z nieznanych lokalizacji |

Następujące zabezpieczeń usługi Azure AD w nietypowych działań raporty nie są uwzględniane jako zdarzeń o podwyższonym ryzyku w witrynie Azure portal:

* Logowania po wielokrotnych niepowodzeniach
* Logowania z wielu lokalizacji geograficznych


### <a name="detected-risk-events"></a>Wykrytych ryzykownych zdarzeń

Raporty o zdarzeniach zagrożenia wykryte w **zabezpieczeń** części **usługi Azure Active Directory** bloku [witryny Azure portal](https://portal.azure.com). Zdarzenia o podwyższonym ryzyku w wykryte są śledzone w następujące raporty:   

- [Narażeni użytkownicy](concept-user-at-risk.md)
- [Ryzykowne logowania](concept-risky-sign-ins.md)

    ![Raporty dotyczące zabezpieczeń](./media/howto-find-activity-reports/04.png "raporty dotyczące zabezpieczeń")

## <a name="troubleshoot-issues-with-activity-reports"></a>Rozwiązywanie problemów z raportów aktywności

### <a name="missing-data-in-the-downloaded-activity-logs"></a>Brak danych w dziennikach aktywności pobrany

#### <a name="symptoms"></a>Objawy 

Pobrano dzienniki aktywności (inspekcji lub logowania), ale nie widać wszystkich rekordów dla wybranego czasu. Dlaczego? 

 ![Raportowanie](./media/troubleshoot-missing-data-download/01.png)
 
#### <a name="cause"></a>Przyczyna

Podczas pobierania dzienników aktywności w witrynie Azure portal jest ograniczona na skalę, która rozmiar 250 000 rekordów posortowanych według najnowsze na początku. 

#### <a name="resolution"></a>Rozwiązanie

Można wykorzystać [interfejsy API raportowania usługi Azure AD](concept-reporting-api.md), aby pobrać do miliona rekordów z dowolnego okresu.

### <a name="missing-audit-data-for-recent-actions-in-the-azure-portal"></a>Brak danych inspekcji dla ostatnich działań w witrynie Azure portal

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

### <a name="missing-logs-for-recent-user-sign-ins-in-the-azure-ad-sign-ins-activity-log"></a>Zaloguj się Brak dzienników dla ostatnich operacji logowania użytkownika w ramach działania logowania usługi Azure AD

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

## <a name="next-steps"></a>Kolejne kroki

* [Przegląd dzienników inspekcji](concept-audit-logs.md)
* [Omówienie logowania](concept-sign-ins.md)
* [Przegląd ryzykownych zdarzeń](concept-risk-events.md)
