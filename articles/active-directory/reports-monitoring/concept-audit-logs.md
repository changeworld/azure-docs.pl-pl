---
title: Raporty dotyczące inspekcji w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących inspekcji w portalu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/17/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad399fc24b2cdfbdc51e7feccba2c05786216b19
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253236"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące inspekcji w portalu usługi Azure Active Directory 

Dzięki raportom usługi Azure Active Directory (Azure AD) można uzyskać informacje potrzebne do określenia działania środowiska.

Architektura raportowania składa się z następujących składników:

- **Działanie** 
    - **Logowania** — raport [logowania](concept-sign-ins.md) zawiera informacje na temat korzystania z zarządzanych aplikacji i działań logowania użytkownika.
    - **Dzienniki inspekcji** — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji obejmują zmiany wprowadzone do wszystkich zasobów w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- **Zabezpieczenia** 
    - **Ryzykowne logowania** — ryzykowne [logowanie](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania, która mogła zostać wykonana przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 
    - **Użytkownicy oflagowani ze względu na ryzyko** — ryzykowny [użytkownik](concept-user-at-risk.md) jest wskaźnikiem dla konta użytkownika, które mogło zostać naruszone.

Ten artykuł zawiera omówienie raportu z inspekcji.
 
## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?

* Użytkownicy w **rolach Administrator zabezpieczeń,** **Czytnik zabezpieczeń,** **Czytnik raportów,** **Czytnik globalny** lub **Administrator globalny**

## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji usługi Azure AD zawierają rekordy działań systemowych pod kątem zgodności. Aby uzyskać dostęp do raportu inspekcji, wybierz pozycję **Dzienniki inspekcji** w sekcji **Monitorowanie** **usługi Azure Active Directory**. Należy zauważyć, że dzienniki inspekcji może mieć opóźnienie do godziny, więc może upłynąć tak długo, aby dane działania inspekcji, aby wyświetlić się w portalu po wykonaniu zadania.



Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- datę i godzinę wystąpienia,
- usługa, która zarejestrowała wystąpienie
- kategorii i nazwy działania (*co*) 
- stan działania (sukces lub porażka)
- element docelowy.
- inicjatora/aktora (kto) działania,

![Dzienniki inspekcji](./media/concept-audit-logs/listview.png "Dzienniki inspekcji")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Dzienniki inspekcji](./media/concept-audit-logs/columns.png "Dzienniki inspekcji")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dzienniki inspekcji](./media/concept-audit-logs/columnselect.png "Dzienniki inspekcji")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Dzienniki inspekcji](./media/concept-audit-logs/details.png "Dzienniki inspekcji")


## <a name="filtering-audit-logs"></a>Dzienniki inspekcji filtrowania

Dane inspekcji można filtrować w następujących polach:

- Usługa
- Kategoria
- Działanie
- Stan
- Środowisko docelowe
- Zainicjowane przez (aktor)
- Zakres dat

![Dzienniki inspekcji](./media/concept-audit-logs/filter.png "Dzienniki inspekcji")

**Filtr Usługa** umożliwia wybranie z listy rozwijanej następujących usług:

- Wszystkie
- AAD Zarządzanie UX
- Przeglądy dostępu
- Aprowizacja kont
- Serwer proxy aplikacji
- Metody uwierzytelniania
- B2C
- Dostęp warunkowy
- Katalog podstawowy
- Zarządzanie uprawnieniami
- Uwierzytelnianie hybrydowe
- Identity Protection
- Zaproszeni użytkownicy
- Usługa MIM
- MyApps ( MyApps )
- PIM
- Samoobsługowe zarządzanie grupami
- Samoobsługowe zarządzanie hasłami
- Warunki użytkowania

Filtr **Kategoria** umożliwia wybranie jednego z następujących filtrów:

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
- Domena Protokołu Kerberos
- Zanieł po kluczach
- Label
- Inne
- Polityka na wysyłce
- Zasady
- Zagospodarowanie zasobami
- Zarządzanie rolą
- UserManagement (UserManagement)

Filtr **Działania** jest oparty na wybranym typie zasobów kategorii i działania. Możesz wybrać konkretne działanie, które chcesz zobaczyć, lub wybrać wszystkie działania. 

Listę wszystkich działań inspekcji można uzyskać za pomocą interfejsu API wykresu:`https://graph.windows.net/<tenantdomain>/activities/auditActivityTypesV2?api-version=beta`

**Filtr Stanu** umożliwia filtrowanie na podstawie stanu operacji inspekcji. Stan może być jedną z następujących czynności:

- Wszystkie
- Powodzenie
- Niepowodzenie

Filtr **docelowy** umożliwia wyszukiwanie określonego obiektu docelowego przez początek nazwy lub głównej nazwy użytkownika (UPN). W nazwie docelowej i nazwie UPN rozróżniana jest wielkość liter. 

**Zainicjowany przez** filtr umożliwia zdefiniowanie, od czego zaczyna się nazwa aktora lub uniwersalna nazwa główna (UPN). W nazwie i nazwie UPN rozróżniana jest wielkość liter.

**Filtr Zakres dat** umożliwia zdefiniowanie przedziału czasowego dla zwróconych danych.  
Możliwe wartości:

- 7 dni
- 24 godziny
- Niestandardowy

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Możesz również pobrać filtrowane dane, maksymalnie 250 000 rekordów, wybierając przycisk **Pobierz.** Dzienniki można pobrać w formacie CSV lub JSON. Liczba rekordów, które można pobrać, jest ograniczona przez [zasady przechowywania raportów usługi Azure Active Directory](reference-reports-data-retention.md).

![Dzienniki inspekcji](./media/concept-audit-logs/download.png "Dzienniki inspekcji")

## <a name="audit-logs-shortcuts"></a>Skróty dzienników inspekcji

Poza usługą **Azure Active Directory** witryna Azure Portal zapewnia dwa dodatkowe punkty wejścia do danych inspekcji:

- Użytkownicy i grupy
- Aplikacje dla przedsiębiorstw

### <a name="users-and-groups-audit-logs"></a>Dzienniki inspekcji użytkowników i grup

Za pomocą raportów inspekcji opartych na użytkownikach i grupach można uzyskać odpowiedzi na pytania, takie jak:

- Jakie typy aktualizacji zostały zastosowane przez użytkowników?

- Ilu użytkowników zostało zmienionych?

- Ile haseł zostało zmienionych?

- Jakich zmian dokonał administrator w katalogu?

- Jakie grupy zostały dodane?

- Czy istnieją grupy, w których dokonano zmiany członkostwa?

- Czy właściciele grup zostali zmienieni?

- Jakie licencje zostały przypisane do grupy lub użytkownika?

Jeśli chcesz przejrzeć tylko dane inspekcji związane z użytkownikami, możesz znaleźć filtrowany widok w obszarze **Dzienniki inspekcji** w sekcji **Monitorowanie** na karcie **Użytkownicy.** Ten punkt wejścia ma **UserManagement** jako wstępnie wybranej kategorii.

![Dzienniki inspekcji](./media/concept-audit-logs/users.png "Dzienniki inspekcji")

Jeśli chcesz przejrzeć tylko dane inspekcji związane z grupami, możesz znaleźć filtrowany widok w obszarze **Dzienniki inspekcji** w sekcji **Monitorowanie** na karcie **Grupy.** Ten punkt wejścia ma **GroupManagement** jako wstępnie wybranej kategorii.

![Dzienniki inspekcji](./media/concept-audit-logs/groups.png "Dzienniki inspekcji")

### <a name="enterprise-applications-audit-logs"></a>Dzienniki inspekcji aplikacji dla przedsiębiorstw

Za pomocą raportów inspekcji opartych na aplikacjach można uzyskać odpowiedzi na pytania, takie jak:

* Jakie aplikacje zostały dodane lub zaktualizowane?
* Jakie aplikacje zostały usunięte?
* Czy podmiot usługi dla aplikacji został zmieniony?
* Czy nazwy aplikacji zostały zmienione?
* Kto udzielił zezwolenia dla aplikacji?

Jeśli chcesz przejrzeć dane inspekcji związane z aplikacjami, można znaleźć filtrowany widok w obszarze **Dzienniki inspekcji** w sekcji **Działania** w bloku **Aplikacje przedsiębiorstwa.** Ten punkt wejścia ma wstępnie wybrane **aplikacje enterprise** jako **typ aplikacji**.

![Dzienniki inspekcji](./media/concept-audit-logs/enterpriseapplications.png "Dzienniki inspekcji")

## <a name="office-365-activity-logs"></a>Dzienniki aktywności usługi Office 365

Dzienniki aktywności usługi Office 365 można wyświetlać w [centrum administracyjnym usługi Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Mimo że działania usługi Office 365 i dzienniki aktywności usługi Azure AD współużytkują wiele zasobów katalogu, tylko centrum administracyjne usługi Microsoft 365 zapewnia pełny widok dzienników aktywności usługi Office 365. 

Dzienniki aktywności usługi Office 365 można również uzyskać programowo, korzystając z [interfejsów API zarządzania usługą Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Następne kroki

- [Informacje o działaniach inspekcji usługi Azure AD](reference-audit-activities.md)
- [Odwołanie do przechowywania raportów usługi Azure AD](reference-reports-data-retention.md)
- [Odwołanie do opóźnień dzienników usługi Azure AD](reference-reports-latencies.md)
