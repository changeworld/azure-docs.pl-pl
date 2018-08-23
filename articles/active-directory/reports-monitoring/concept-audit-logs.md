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
ms.date: 04/19/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: b6fa26cb7947658af77496831d7239b4331aa1f2
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055089"
---
# <a name="audit-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące inspekcji w portalu usługi Azure Active Directory 

Dzięki raportom w usłudze Azure Active Directory (Azure AD) możesz uzyskać wszystkie informacje, które pomogą ustalić działanie środowiska.

Architektura raportowania w usłudze Azure AD obejmuje następujące składniki:

- **Działanie** 
    - **Działania związane z logowaniem** — informacje na temat użycia zarządzanych aplikacji i działania użytkownika związane z logowaniem
    - **Dzienniki inspekcji** — udostępnia możliwość śledzenia wszystkich zmian wprowadzanych przez różne funkcje usługi Azure AD za pomocą dzienników. Dzienniki inspekcji zawierają na przykład zmiany wprowadzone dla wszelkich zasobów w usłudze Azure AD, takich jak użytkownicy, aplikacje, grupy, role, zasady, uwierzytelnienia itp.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. Aby uzyskać więcej informacji, zobacz Ryzykowne logowania.
    - **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. Aby uzyskać więcej informacji, zobacz Użytkownicy oflagowani w związku z ryzykiem.

Ten temat zawiera przegląd działań dotyczących inspekcji.
 
## <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w roli administratora zabezpieczeń lub czytelnika zabezpieczeń
* Administratorzy globalni
* Poszczególni użytkownicy (inni niż administratorzy) mogą wyświetlać dane na temat własnych działań


## <a name="audit-logs"></a>Dzienniki inspekcji

Dzienniki inspekcji w usłudze Azure Active Directory dostarczają informacji na temat aktywności systemu pod kątem zgodności.  
Pierwszym punktem wejścia do wszystkich danych inspekcji jest pozycja **Dzienniki inspekcji** znajdująca się w sekcji **Aktywność** usługi **Azure Active Directory**.

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


Klikając pozycję w widoku listy, możesz uzyskać wszystkie szczegóły na jej temat.

![Dzienniki inspekcji](./media/concept-audit-logs/22.png "Dzienniki inspekcji")


## <a name="filtering-audit-logs"></a>Dzienniki inspekcji filtrowania

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz odfiltrować dane inspekcji przy użyciu następujących pól:

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

Filtr **Zainicjowane przez** umożliwia określenie nazwy aktora lub jego głównej nazwy użytkownika (UPN, user principal name).

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


Filtr **Działanie** jest oparty na wybranej kategorii i typie zasobu działania. Możesz wybrać konkretne działanie, które chcesz zobaczyć, lub wybrać wszystkie działania. 

Listę wszystkich działań związanych z inspekcją można uzyskać przy użyciu interfejsu API programu Graph https://graph.windows.net/$tenantdomain/activities/auditActivityTypes?api-version=beta, gdzie $tenantdomain to nazwa Twojej domeny. Jest ona również przedstawiona w artykule [Zdarzenia raportów inspekcji](concept-audit-logs.md).


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
* Czy usługa w ramach aplikacji została zmieniona?
* Czy nazwy aplikacji zostały zmienione?
* Kto udzielił zezwolenia dla aplikacji?

Jeśli chcesz przeglądać dane inspekcji dotyczące aplikacji, możesz skorzystać z widoku filtrowanego znajdującego się w obszarze **Dzienniki inspekcji** w sekcji **Aktywność** bloku **Aplikacje dla przedsiębiorstw**. Ten punkt wejścia ma wartość **Aplikacje dla przedsiębiorstw** wstępnie wybraną dla opcji **Typ zasobu działania**.

![Dzienniki inspekcji](./media/concept-audit-logs/134.png "Dzienniki inspekcji")

Możesz odfiltrować ten widok, aby wyświetlić tylko **grupy** lub **użytkowników**.

![Dzienniki inspekcji](./media/concept-audit-logs/25.png "Dzienniki inspekcji")



## <a name="next-steps"></a>Kolejne kroki

- Omówienie funkcji raportowania można znaleźć w temacie [Raporty w usłudze Azure Active Directory](overview-reports.md).

- Aby uzyskać pełną listę wszystkich działań inspekcji, zobacz artykuł[Azure AD audit activity reference (Informacje o działaniach inspekcji usługi Azure AD)](reference-audit-activities.md)

