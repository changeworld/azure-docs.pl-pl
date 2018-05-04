---
title: Raporty dotyczące logowań w portalu usługi Azure Active Directory | Microsoft Docs
description: Wprowadzenie do raportów dotyczących logowań w portalu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/23/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.openlocfilehash: cd057704197eb4bf66eff33d3c83a2ca9d5712d1
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Raporty dotyczące logowań w portalu Azure Active Directory

Dzięki raportom usługi Azure Active Directory (Azure AD) w witrynie [Azure Portal](https://portal.azure.com) możesz uzyskać wszystkie informacje, które pomogą ustalić, jak działa środowisko.

Architektura raportowania w usłudze Azure Active Directory obejmuje następujące składniki:

- **Działanie** 
    - **Działania związane z logowaniem** — informacje na temat użycia zarządzanych aplikacji i działania użytkownika związane z logowaniem
    - **Dzienniki inspekcji** — informacje o aktywności systemu obejmujące zarządzanie użytkownikami i grupami oraz zarządzane aplikacje i działania dotyczące katalogu.
- **Bezpieczeństwo** 
    - **Ryzykowne logowania** — ryzykowne logowanie jest wskaźnikiem próby logowania, które mogło zostać wykonane przez osobę, która nie jest prawowitym właścicielem konta użytkownika. Aby uzyskać więcej informacji, zobacz Ryzykowne logowania.
    - **Użytkownicy oflagowani w związku z ryzykiem** — ryzykowny użytkownik jest wskaźnikiem konta użytkownika, którego bezpieczeństwo mogło zostać naruszone. Aby uzyskać więcej informacji, zobacz Użytkownicy oflagowani w związku z ryzykiem.

Ten temat zawiera przegląd działań dotyczących logowania.

## <a name="pre-requisite"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w roli administratora zabezpieczeń lub czytelnika zabezpieczeń
* Administratorzy globalni
* Dowolny użytkownik (inny niż administrator) może uzyskać dostęp do danych na temat własnych logowań 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Jaka licencja usługi Azure AD jest wymagana w celu uzyskania dostępu do informacji dotyczących logowania?
* Dzierżawca musi mieć skojarzoną licencję usługi Azure AD w wersji Premium, aby wyświetlić pełny raport o wszystkich operacjach logowania


## <a name="signs-in-activities"></a>Działania związane z logowaniem

Dzięki informacjom zawartym w raporcie logowania użytkownika można uzyskać odpowiedzi na pytania, takie jak:

* Co to jest wzorzec logowania użytkownika?
* Ilu użytkowników zalogowało się w ciągu tygodnia?
* Jaki jest stan tych logowań?

Pierwszym punktem wejścia do wszystkich danych dotyczących logowania jest pozycja **Logowania** w sekcji Działania usługi **Azure Active**.


![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/61.png "Działania związane z logowaniem")


Dziennik inspekcji zawiera domyślny widok listy, który pokazuje:

- powiązanego użytkownika;
- aplikację, do której zalogował się użytkownik;
- stan logowania;
- czas logowania.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/41.png "Działania związane z logowaniem")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/19.png "Działania związane z logowaniem")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/42.png "Działania związane z logowaniem")

Klikając pozycję w widoku listy, możesz uzyskać wszystkie szczegóły na jej temat.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/43.png "Działania związane z logowaniem")


## <a name="filtering-sign-in-activities"></a>Filtrowanie działań związanych z logowaniem

Aby zawęzić zgłaszane dane do odpowiedniego poziomu, możesz odfiltrować dane logowania przy użyciu następujących pól:

- Przedział czasu
- Użytkownik
- Aplikacja
- Klient
- Stan logowania

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/44.png "Działania związane z logowaniem")


Filtr **Przedział czasu** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 24 godziny
- Niestandardowy

Po wybraniu niestandardowego przedziału czasu możesz skonfigurować godzinę rozpoczęcia i zakończenia.

Filtr **Użytkownik** umożliwia określenie nazwy lub głównej nazwy użytkownika (UPN, user principal name) dla żądanego użytkownika.

Filtr **Aplikacja** umożliwia określenie nazwy żądanej aplikacji.

Filtr **Klient** umożliwia określenie informacji dotyczących żądanego urządzenia.

Filtr **Stan logowania** umożliwia wybranie jednego z następujących filtrów:

- Wszyscy
- Powodzenie
- Niepowodzenie


## <a name="sign-in-activities-shortcuts"></a>Skróty działań związanych z logowaniem

Poza usługą Azure Active Directory witryna Azure Portal zapewnia dwa dodatkowe punkty wejścia do danych dotyczących działań związanych z logowaniem:

- Użytkownicy i grupy
- Aplikacje dla przedsiębiorstw


### <a name="users-and-groups-sign-ins-activities"></a>Działania związane z logowaniem użytkowników i grup

Dzięki informacjom zawartym w raporcie logowania użytkownika można uzyskać odpowiedzi na pytania, takie jak:

- Co to jest wzorzec logowania użytkownika?
- Ilu użytkowników zalogowało się w ciągu tygodnia?
- Jaki jest stan tych logowań?



Punktem wyjścia do tych danych jest wykres logowania użytkownika znajdujący się w sekcji **Przegląd** w obszarze **Użytkownicy i grupy**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/45.png "Działania związane z logowaniem")

Wykres logowania użytkownika przedstawia tygodniowe agregacje logowań dla wszystkich użytkowników w danym okresie czasu. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/46.png "Działania związane z logowaniem")

Po kliknięciu dnia na wykresie logowania zostanie wyświetlona szczegółowa lista działań związanych z logowaniem dla tego dnia.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/41.png "Działania związane z logowaniem")

Każdy wiersz na liście działań związanych z logowaniem zapewnia szczegółowe informacje o wybranym logowaniu, takie jak:

* Kto się zalogował?
* Jaka była nazwa główna użytkownika?
* Do której aplikacji się logowano?
* Jaki jest adres IP komputera, z którego się logowano?
* Jaki był stan logowania?

Opcja **Logowania** umożliwia pełny przegląd logowań wszystkich użytkowników.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/51.png "Działania związane z logowaniem")



## <a name="usage-of-managed-applications"></a>Użycie zarządzanych aplikacji

Dzięki widokowi skoncentrowanemu na aplikacji w ramach danych logowania można uzyskać odpowiedzi na pytania, takie jak:

* Kto korzysta z aplikacji?
* Jakie są 3 najczęściej używane aplikacje w organizacji?
* Ostatnio została wdrożona aplikacja. W jaki sposób działa?

Punktem wyjścia do tych danych są 3 najczęściej używane aplikacje w organizacji uwzględnione w raporcie z ostatnich 30 dni znajdującym się w sekcji **Przegląd** w obszarze **Aplikacje dla przedsiębiorstw**.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/64.png "Działania związane z logowaniem")

Wykres użycia aplikacji przedstawia tygodniowe agregacje logowań 3 najczęściej używanych aplikacji w danym czasie. Domyślny okres to 30 dni.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/47.png "Działania związane z logowaniem")

Jeśli chcesz, możesz ustawić fokus na konkretnej aplikacji.


![Raportowanie](./media/active-directory-reporting-activity-sign-ins/single_spp_usage_graph.png "Raportowanie")

Po kliknięciu dnia na wykresie użycia aplikacji zostanie wyświetlona szczegółowa lista działań związanych z logowaniem.


![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/48.png "Działania związane z logowaniem")


Opcja **Logowania** umożliwia pełny przegląd zdarzeń logowania do aplikacji.

![Działania związane z logowaniem](./media/active-directory-reporting-activity-sign-ins/49.png "Działania związane z logowaniem")



## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się więcej na temat kodów błędów działań związanych z logowaniem, zobacz [Kody błędów w raportach działań związanych z logowaniem w portalu usługi Azure Active Directory](active-directory-reporting-activity-sign-ins-errors.md).
