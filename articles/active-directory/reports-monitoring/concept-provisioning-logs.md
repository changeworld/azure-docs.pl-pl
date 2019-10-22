---
title: Dzienniki aprowizacji w portalu Azure Active Directory (wersja zapoznawcza) | Microsoft Docs
description: Wprowadzenie do raportów dotyczących działań związanych z obsługą administracyjną w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 07/29/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d48aa3ead28ab0b0a22478a0c4183995483058a
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2019
ms.locfileid: "70983499"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Raporty dotyczące aprowizacji w portalu Azure Active Directory (wersja zapoznawcza)

Architektura raportowania w Azure Active Directory (Azure AD) składa się z następujących składników:

- **Działanie** 
    - **Logowania** — informacje na temat użycia zarządzanych aplikacji i działań związanych z logowaniem użytkowników.
    - **Dzienniki inspekcji**  - [dzienniki inspekcji](concept-audit-logs.md) zapewniają informacje o aktywności systemu dotyczące zarządzania użytkownikami i grupami, zarządzane aplikacje i działania w katalogu.
    - **Dzienniki aprowizacji** — zapewniają działania systemowe dotyczące użytkowników, grup i ról, które są obsługiwane przez usługę aprowizacji usługi Azure AD. 

- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — [ryzykowne logowanie](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania, które mogło zostać wykonane przez kogoś, kto nie jest uprawnionym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani do ryzyka** — [ryzykowny użytkownik](concept-user-at-risk.md) jest wskaźnikiem konta użytkownika, które mogło zostać naruszone.

Ten temat zawiera omówienie raportu aprowizacji.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w rolach administrator zabezpieczeń, czytelnik zabezpieczeń, czytelnik raportu, administrator aplikacji i administrator aplikacji w chmurze
* Administratorzy globalni


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do działań aprowizacji?

Dzierżawca musi mieć skojarzoną licencję Azure AD — wersja Premium, aby wyświetlić raport dotyczący całej aktywności aprowizacji. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). 

## <a name="provisioning-logs"></a>Dzienniki aprowizowania

Dzienniki aprowizacji zapewniają odpowiedzi na następujące pytania:

* Które grupy zostały pomyślnie utworzone w usługi ServiceNow?
* Jak role zostały zaimportowane z Amazon Web Services?
* Które użytkowników nie zostały pomyślnie utworzone w usłudze DropBox?

Dostęp do dzienników aprowizacji można uzyskać, wybierając pozycję **dzienniki aprowizacji** w sekcji **monitorowanie** w bloku **Azure Active Directory** w [Azure Portal](https://portal.azure.com). W przypadku niektórych rekordów aprowizacji w portalu może upłynąć do dwóch godzin.

![Dzienniki aprowizacji](./media/concept-provisioning-logs/access-provisioning-logs.png "Dzienniki aprowizowania")


Dziennik aprowizacji zawiera domyślny widok listy, który pokazuje:

- Tożsamość
- Akcja
- System źródłowy
- System docelowy
- Stan
- Data


![Kolumny domyślne](./media/concept-provisioning-logs/default-columns.png "Kolumny domyślne")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Wybór kolumn](./media/concept-provisioning-logs/column-chooser.png "Wybór kolumn")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dostępne kolumny](./media/concept-provisioning-logs/available-columns.png "Dostępne kolumny")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Szczegółowe informacje](./media/concept-provisioning-logs/steps.png "Filtr")


## <a name="filter-provisioning-activities"></a>Filtrowanie działań aprowizacji

Aby zawęzić zgłoszone dane do poziomu, który działa dla Ciebie, można odfiltrować dane aprowizacji przy użyciu następujących pól domyślnych. Należy zauważyć, że wartości w filtrach są dynamicznie wypełniane na podstawie dzierżawy. Jeśli na przykład nie masz żadnych zdarzeń tworzenia w dzierżawie, nie będzie dostępna opcja filtru dla operacji tworzenia.

- Tożsamość
- Działanie
- System źródłowy
- System docelowy
- Stan
- Data


![Filtru](./media/concept-provisioning-logs/filter.png "Filtr")

Filtr **tożsamości** umożliwia określenie nazwy lub tożsamości, o której Cię interesują. Ta tożsamość może być użytkownikiem, grupą, rolą lub innym obiektem. Można wyszukiwać według nazwy lub identyfikatora obiektu. Identyfikator różni się w zależności od scenariusza. Na przykład podczas aprowizacji obiektu z usługi Azure AD do usług SalesForce identyfikator źródłowy jest IDENTYFIKATORem obiektu użytkownika w usłudze Azure AD, a TargetID jest IDENTYFIKATORem użytkownika w usłudze Salesforce. Po zainicjowaniu obsługi administracyjnej od dnia roboczego do Active Directory identyfikator źródła to identyfikator pracownika procesu roboczego programu Workday. Należy zauważyć, że nazwa użytkownika może nie zawsze występować w kolumnie tożsamość. Zawsze będzie istnieć jeden identyfikator. 

Filtr **systemu źródłowego** umożliwia określenie lokalizacji, z której jest inicjowana tożsamość. Na przykład podczas aprowizacji obiektu z usługi Azure AD do usługi ServiceNow, system źródłowy to Azure AD. 

**Docelowy filtr systemu** umożliwia określenie lokalizacji, do której jest obsługiwana tożsamość. Na przykład podczas aprowizacji obiektu z usługi Azure AD do usługi ServiceNow system docelowy to usługi ServiceNow. 

Filtr **stanu** umożliwia wybranie:

- Wszystko
- Powodzenie
- Niepowodzenie
- Pominięto

Filtr **akcji** umożliwia filtrowanie:

- Create 
- Aktualizacja
- Usuń
- Wyłączenie
- Inne

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 30 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu można skonfigurować datę początkową i datę końcową.


Oprócz pól domyślnych, po wybraniu można także uwzględnić następujące pola w filtrze:

- **Identyfikator zadania** — unikatowy identyfikator zadania jest skojarzony z każdą aplikacją, dla której włączono obsługę administracyjną.   

- **Identyfikator cyklu** — jednoznacznie identyfikuje cykl aprowizacji. Ten identyfikator można udostępnić do obsługi, aby wyszukać cykl, w którym wystąpiło zdarzenie.

- **Zmień identyfikator** — unikatowy identyfikator dla zdarzenia aprowizacji. Ten identyfikator można udostępnić do obsługi wyszukania zdarzenia aprowizacji.   



  

## <a name="provisioning-details"></a>Szczegóły aprowizacji 

Po wybraniu elementu w widoku listy aprowizacji uzyskasz więcej szczegółowych informacji na temat tego elementu.
Szczegóły są pogrupowane w oparciu o następujące kategorie:

- Kroki

- Rozwiązywanie problemów i zalecenia

- Zmodyfikowane właściwości

- Podsumowanie


![Filtru](./media/concept-provisioning-logs/provisioning-tabs.png "Przedstawia")



### <a name="steps"></a>Kroki

Na karcie **kroki** przedstawiono kroki, które należy wykonać w celu aprowizacji obiektu. Inicjowanie obsługi obiektu może składać się z czterech kroków: 

- Importuj obiekt
- Określanie, czy obiekt jest w zakresie
- Dopasuj obiekt między źródłem i elementem docelowym
- Inicjowanie obiektu (podejmowanie akcji — może to być tworzenie, aktualizowanie, usuwanie lub wyłączanie)



![Filtru](./media/concept-provisioning-logs/steps.png "Filtr")


### <a name="troubleshoot-and-recommendations"></a>Rozwiązywanie problemów i zalecenia


Karta **Rozwiązywanie problemów i zalecenia** zawiera kod błędu i przyczynę. Informacje o błędzie są dostępne tylko w przypadku awarii. 


### <a name="modified-properties"></a>Zmodyfikowane właściwości

**Zmodyfikowane właściwości** pokazuje starą wartość i nową wartość. W przypadku braku starej wartości kolumna stara wartość jest pusta. 


### <a name="summary"></a>Podsumowanie

Karta **Podsumowanie** zawiera przegląd informacji o tym, co się stało i identyfikatory dla obiektu w systemie źródłowym i docelowym. 

## <a name="what-you-should-know"></a>Co należy wiedzieć

- W Azure Portal są przechowywane zgłoszone dane aprowizacji przez 30 dni, jeśli masz wersję Premium i 7 dni, jeśli masz bezpłatną wersję.

- Można użyć atrybutu identyfikatora zmiany jako unikatowego identyfikatora. Jest to przydatne na przykład podczas współdziałania z pomocą techniczną produktu.

- Obecnie nie ma możliwości pobrania danych aprowizacji.

- Obecnie nie ma obsługi usługi log Analytics.

- W przypadku uzyskiwania dostępu do dzienników aprowizacji z kontekstu aplikacji nie są automatycznie filtrowane zdarzenia do konkretnej aplikacji w sposób, w jaki dzienniki inspekcji.

## <a name="next-steps"></a>Następne kroki

* [Sprawdź stan aprowizacji użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Wystąpił problem podczas konfigurowania aprowizacji użytkowników w aplikacji z galerii usługi Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


