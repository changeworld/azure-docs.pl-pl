---
title: Raporty dotyczące inspekcji w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących inspekcji w portalu usługi Azure Active Directory
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: a1f93126-77d1-4345-ab7d-561066041161
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 883f521040c67cb8fe9578bc5c490bc3dfccba28
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2018
ms.locfileid: "51624662"
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

* Użytkownicy w **polu zabezpieczeń**, **Czytelnik zabezpieczeń** lub **administratora globalnego** ról
* Ponadto wszyscy użytkownicy (inni niż administratorzy) widoczne własnych działań inspekcji

## <a name="audit-logs"></a>Dzienniki inspekcji

Inspekcja usługi Azure AD dzienniki dostarczają informacji na temat aktywności systemu pod kątem zgodności. Aby uzyskać dostęp do raportu inspekcji, zaznacz **dzienniki inspekcji** w **działania** części **usługi Azure Active Directory**. 

![Dzienniki inspekcji](./media/concept-audit-logs/61.png "Dzienniki inspekcji")

Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- datę i godzinę wystąpienia,
- inicjatora/aktora (*kto*) działania, 
- działanie (*co*), 
- element docelowy.

![Dzienniki inspekcji](./media/concept-audit-logs/18.png "Dzienniki inspekcji")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Dzienniki inspekcji](./media/concept-audit-logs/19.png "Dzienniki inspekcji")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dzienniki inspekcji](./media/concept-audit-logs/21.png "Dzienniki inspekcji")

Wybierz element w widoku listy, aby uzyskać więcej szczegółowych informacji.

![Dzienniki inspekcji](./media/concept-audit-logs/22.png "Dzienniki inspekcji")


## <a name="filtering-audit-logs"></a>Dzienniki inspekcji filtrowania

Można filtrować dane inspekcji na następujące pola:

- Zakres dat
- Zainicjowane przez (aktor)
- Kategoria
- Typ zasobu działania
- Działanie

![Dzienniki inspekcji](./media/concept-audit-logs/23.png "Dzienniki inspekcji")

Filtr **Zakres dat** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 24 godziny
- Niestandardowy

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

**Inicjowane przez** Filtr pozwala na określenie nazwy aktora lub uniwersalnych głównej nazwy (UPN).

Filtr **Kategoria** umożliwia wybranie jednego z następujących filtrów:

- Wszyscy
- Kategoria podstawowa
- Katalog podstawowy
- Samoobsługowe zarządzanie hasłami
- Samoobsługowe zarządzanie grupami
- Aprowizacja kont — automatyczne przerzucanie haseł
- Zaproszeni użytkownicy
- Usługa MIM
- Identity Protection
- B2C

Filtr **Typ zasobu działania** umożliwia wybranie jednego z następujących filtrów:

- Wszyscy 
- Grupa
- Katalog
- Użytkownik
- Aplikacja
- Zasady
- Urządzenie
- Inne

Jeśli wybierzesz opcję **Grupa** w pozycji **Typ zasobu działania**, uzyskasz dostęp do dodatkowej kategorii filtru umożliwiającej podanie wartości **Źródło**:

- Azure AD
- O365


**Działania** filtru jest oparty na kategorii i działań wybór typów zasobów wprowadzeniu. Możesz wybrać konkretne działanie, które chcesz zobaczyć, lub wybrać wszystkie działania. 

Listę wszystkich działań związanych z inspekcją można uzyskać przy użyciu interfejsu API programu Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, gdzie $tenantdomain to nazwa Twojej domeny. Jest ona również przedstawiona w artykule [Zdarzenia raportów inspekcji](reference-audit-activities.md).

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

Jeśli chcesz przeglądać dane inspekcji dotyczące użytkowników i grup, możesz skorzystać z widoku filtrowanego znajdującego się w obszarze **Dzienniki inspekcji** w sekcji **Aktywność** na stronie **Użytkownicy i grupy**. Ten punkt wejścia ma wartość **Użytkownicy i grupy** wstępnie wybraną dla opcji **Typ zasobu działania**.

![Dzienniki inspekcji](./media/concept-audit-logs/93.png "Dzienniki inspekcji")

### <a name="enterprise-applications-audit-logs"></a>Dzienniki inspekcji aplikacji dla przedsiębiorstw

Za pomocą raportów inspekcji opartych na aplikacjach można uzyskać odpowiedzi na pytania, takie jak:

* Jakie aplikacje zostały dodane lub zaktualizowane?
* Jakie aplikacje zostały usunięte?
* Zmieniono nazwę główną usługi dla aplikacji?
* Czy nazwy aplikacji zostały zmienione?
* Kto udzielił zezwolenia dla aplikacji?

Jeśli chcesz przeglądać dane inspekcji dotyczące aplikacji, możesz znaleźć widoku filtrowanego znajdującego się w obszarze **dzienniki inspekcji** w **działania** części **aplikacje dla przedsiębiorstw** blok. Ten punkt wejścia ma **aplikacje dla przedsiębiorstw** instalowane jako **typ zasobu działania**.

![Dzienniki inspekcji](./media/concept-audit-logs/134.png "Dzienniki inspekcji")

Możesz filtrować ten widok w dół do **grup** lub **użytkowników**.

![Dzienniki inspekcji](./media/concept-audit-logs/25.png "Dzienniki inspekcji")


## <a name="next-steps"></a>Kolejne kroki

- [Informacje o działaniach inspekcji usługi Azure AD](reference-audit-activities.md)
- [Usługa Azure AD zgłasza odwołanie do przechowywania](reference-reports-data-retention.md)
- [Odwołanie opóźnienia dzienników w usłudze Azure AD](reference-reports-latencies.md)