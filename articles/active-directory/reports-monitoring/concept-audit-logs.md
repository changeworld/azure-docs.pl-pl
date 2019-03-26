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
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3da96c09026baff3965e0a90d1f461fd948a3a50
ms.sourcegitcommit: 70550d278cda4355adffe9c66d920919448b0c34
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58438399"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące inspekcji w portalu usługi Azure Active Directory 

W przypadku raportów z usługi Azure Active Directory (Azure AD) możesz uzyskać informacje, które trzeba ustalić środowiska.

Architektura raportowania składa się z następujących składników:

- **Działanie** 
    - **Operacje logowania** — [raporcie logowań](concept-sign-ins.md) zawiera informacje na temat użycia zarządzanych aplikacji i użytkownika działań logowania.
    - **Dzienniki inspekcji** — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Przykłady dzienników inspekcji zmiany wprowadzone do wszystkich zasobów w usłudze Azure AD, takie jak dodawanie lub usuwanie użytkowników, aplikacji, grup, ról i zasad.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — [ryzykowne logowania](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania, które mogły zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. 
    - **Użytkownicy oflagowani w związku z ryzykiem** — [ryzykowny użytkownik](concept-user-at-risk.md) jest wskaźnikiem konta użytkownika, który może być zabezpieczenia mogły zostać naruszone.

Ten artykuł zawiera omówienie raport z audytu.
 
## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?

* Użytkownicy w **polu zabezpieczeń**, **Czytelnik zabezpieczeń**, **czytelnika raportów** lub **administratora globalnego** ról
* Ponadto wszyscy użytkownicy (inni niż administratorzy) widoczne własnych działań inspekcji

## <a name="audit-logs"></a>Dzienniki inspekcji

Inspekcja usługi Azure AD dzienniki dostarczają informacji na temat aktywności systemu pod kątem zgodności. Aby uzyskać dostęp do raportu inspekcji, zaznacz **dzienniki inspekcji** w **działania** części **usługi Azure Active Directory**. Należy zauważyć, że dzienniki inspekcji mogą opóźnienie maksymalnie na godzinę, dzięki czemu może podjąć to długo, aby były wyświetlane w portalu po zakończeniu zadania, dane o aktywności inspekcji.

![Dzienniki inspekcji](./media/concept-audit-logs/61.png "Dzienniki inspekcji")

Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- datę i godzinę wystąpienia,
- Usługa, która rejestrowane wystąpienie
- Kategoria i nazwa działania (*co*) 
- Stan działania (powodzenie lub niepowodzenie)
- element docelowy.
- Inicjatora / aktora (kto) działania

![Dzienniki inspekcji](./media/concept-audit-logs/listview.png "Dzienniki inspekcji")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Dzienniki inspekcji](./media/concept-audit-logs/columns.png "Dzienniki inspekcji")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dzienniki inspekcji](./media/concept-audit-logs/columnselect.png "Dzienniki inspekcji")

Wybierz element w widoku listy, aby uzyskać więcej szczegółowych informacji.

![Dzienniki inspekcji](./media/concept-audit-logs/details.png "Dzienniki inspekcji")


## <a name="filtering-audit-logs"></a>Dzienniki inspekcji filtrowania

Można filtrować dane inspekcji na następujące pola:

- Usługa
- Kategoria
- Działanie
- Stan
- Środowisko docelowe
- Zainicjowane przez (aktor)
- Zakres dat

![Dzienniki inspekcji](./media/concept-audit-logs/filter.png "Dzienniki inspekcji")

**Usługi** filtr zezwala na wybranie z listy rozwijanej z następujących usług:

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
- Passord samoobsługowego zarządzania
- Warunki użytkowania

**Kategorii** filtr umożliwia wybranie jednej z następujących filtrów:

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

**Działania** filtru jest oparty na kategorii i działań wybór typów zasobów wprowadzeniu. Możesz wybrać konkretne działanie, które chcesz zobaczyć, lub wybrać wszystkie działania. 

Listę wszystkich działań związanych z inspekcją można uzyskać przy użyciu interfejsu API programu Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, gdzie $tenantdomain to nazwa Twojej domeny. Jest ona również przedstawiona w artykule [Zdarzenia raportów inspekcji](reference-audit-activities.md).

**Stan** filtr umożliwia filtrowanie na podstawie stanu operacji inspekcji. Stan może być jednym z następujących czynności:

- Wszyscy
- Powodzenie
- Niepowodzenie

**Docelowej** filtr umożliwia wyszukiwanie według nazwy lub głównej nazwy użytkownika (UPN) do określonego celu. Nazwa obiektu docelowego i nazwy UPN jest rozróżniana wielkość liter. 

**Inicjowane przez** Filtr pozwala na określenie nazwy aktora lub uniwersalnych głównej nazwy (UPN). Nazwy i nazwy UPN jest rozróżniana wielkość liter.

**Zakres dat** filtr umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 24 godziny
- Niestandardowy

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Możesz również pobrać odfiltrowane dane, maksymalnie 250 000 rekordów, wybierając **Pobierz** przycisku. Użytkownik może pobrać dzienniki w formacie CSV lub JSON. Liczba rekordów, które można pobrać jest ograniczony przez [zasady przechowywania raportów usługi Azure Active Directory](reference-reports-data-retention.md).

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

Jeśli chcesz przeglądać dane inspekcji dotyczące użytkowników, możesz znaleźć widoku filtrowanego znajdującego się w obszarze **dzienniki inspekcji** w **działania** części **użytkowników** kartę. Ten punkt wejścia ma **UserManagement** jako kategoria instalowane.

![Dzienniki inspekcji](./media/concept-audit-logs/users.png "Dzienniki inspekcji")

Jeśli chcesz przeglądać dane inspekcji, który jest powiązany z grup, możesz znaleźć widoku filtrowanego znajdującego się w obszarze **dzienniki inspekcji** w **działania** części **grup** kartę. Ten punkt wejścia ma **GroupManagement** jako kategoria instalowane.

![Dzienniki inspekcji](./media/concept-audit-logs/groups.png "Dzienniki inspekcji")

### <a name="enterprise-applications-audit-logs"></a>Dzienniki inspekcji aplikacji dla przedsiębiorstw

Za pomocą raportów inspekcji opartych na aplikacjach można uzyskać odpowiedzi na pytania, takie jak:

* Jakie aplikacje zostały dodane lub zaktualizowane?
* Jakie aplikacje zostały usunięte?
* Zmieniono nazwę główną usługi dla aplikacji?
* Czy nazwy aplikacji zostały zmienione?
* Kto udzielił zezwolenia dla aplikacji?

Jeśli chcesz przeglądać dane inspekcji dotyczące aplikacji, możesz znaleźć widoku filtrowanego znajdującego się w obszarze **dzienniki inspekcji** w **działania** części **aplikacje dla przedsiębiorstw** blok. Ten punkt wejścia ma **aplikacje dla przedsiębiorstw** instalowane jako **typ aplikacji**.

![Dzienniki inspekcji](./media/concept-audit-logs/enterpriseapplications.png "Dzienniki inspekcji")

## <a name="office-365-activity-logs"></a>Dzienniki aktywności usługi Office 365

Można wyświetlić dzienniki aktywności usługi Office 365 z [Centrum administracyjnego usługi Microsoft 365](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Mimo że aktywności usługi Office 365 i usługi Azure AD działania dzienniki współdzielą wiele zasobów katalogu, Centrum administracyjnego usługi Microsoft 365 zapewnia pełnego widoku dzienników aktywności usługi Office 365. 

Można także przejść do dzienników aktywności usługi Office 365, programowo przy użyciu [interfejsów API zarządzania usługi Office 365](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Kolejne kroki

- [Informacje o działaniach inspekcji usługi Azure AD](reference-audit-activities.md)
- [Usługa Azure AD zgłasza odwołanie do przechowywania](reference-reports-data-retention.md)
- [Odwołanie opóźnienia dzienników w usłudze Azure AD](reference-reports-latencies.md)
