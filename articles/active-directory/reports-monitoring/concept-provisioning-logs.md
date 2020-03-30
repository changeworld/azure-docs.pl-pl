---
title: Udostępnianie obsługi dzienników w portalu usługi Azure Active Directory (wersja zapoznawcza) | Dokumenty firmy Microsoft
description: Wprowadzenie do raportów aktywności inicjowania obsługi administracyjnej w portalu usługi Azure Active Directory
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
ms.date: 11/04/2019
ms.author: markvi
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6e0c697f9ab9796feade9b4d5c2a64794f3980b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73612793"
---
# <a name="provisioning-reports-in-the-azure-active-directory-portal-preview"></a>Inicjowanie obsługi administracyjnej raportów w portalu usługi Azure Active Directory (wersja zapoznawcza)

Architektura raportowania w usłudze Azure Active Directory (Azure AD) składa się z następujących składników:

- **Działanie** 
    - **Logowania** — informacje o użyciu zarządzanych aplikacji i działań logowania użytkownika.
    - **Dzienniki inspekcji Dzienniki** - [inspekcji](concept-audit-logs.md) zawierają informacje o aktywności systemowej o użytkownikach i zarządzaniu grupami, zarządzanych aplikacjach i działaniach katalogowych.
    - **Dzienniki inicjowania obsługi administracyjnej** — podaj działanie systemowe dotyczące użytkowników, grup i ról, które są aprowidzone przez usługę inicjowania obsługi administracyjnej usługi Azure AD. 

- **Zabezpieczenia** 
    - **Ryzykowne logowania** — ryzykowne [logowanie](concept-risky-sign-ins.md) jest wskaźnikiem próby logowania, która mogła zostać wykonana przez osobę, która nie jest prawowitym właścicielem konta użytkownika.
    - **Użytkownicy oflagowani ze względu na ryzyko** — ryzykowny [użytkownik](concept-user-at-risk.md) jest wskaźnikiem dla konta użytkownika, które mogło zostać naruszone.

W tym temacie przedstawiono przegląd raportu inicjowania obsługi administracyjnej.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="who-can-access-the-data"></a>Kto ma dostęp do danych?
* Użytkownicy w rolach Administrator zabezpieczeń, Czytnik zabezpieczeń, Czytnik raportów, Administrator aplikacji i Administrator aplikacji w chmurze
* Administratorzy globalni


### <a name="what-azure-ad-license-do-you-need-to-access-provisioning-activities"></a>Jakiej licencji usługi Azure AD jest potrzebny do uzyskania dostępu do działań inicjowania obsługi administracyjnej?

Dzierżawa musi mieć skojarzoną z nią licencję usługi Azure AD Premium, aby wyświetlić cały raport aktywności inicjowania obsługi administracyjnej. Aby uaktualnić swoją wersję usługi Azure Active Directory, zobacz [Wprowadzenie do usługi Azure Active Directory w wersji Premium](../fundamentals/active-directory-get-started-premium.md). 

## <a name="provisioning-logs"></a>Dzienniki aprowizowania

Dzienniki inicjowania obsługi administracyjnej zawierają odpowiedzi na następujące pytania:

* Jakie grupy zostały pomyślnie utworzone w ServiceNow?
* Jak role zostały zaimportowane z Amazon Web Services?
* Co użytkownicy zostali bezskutecznie utworzone w DropBox?

Dzienniki inicjowania obsługi administracyjnej można uzyskać, wybierając **dzienniki inicjowania obsługi administracyjnej** w sekcji **Monitorowanie** bloku **usługi Azure Active Directory** w [witrynie Azure portal](https://portal.azure.com). Może upłynąć do dwóch godzin dla niektórych rekordów inicjowania obsługi administracyjnej, aby wyświetlić w portalu.

![Dzienniki aprowizowania](./media/concept-provisioning-logs/access-provisioning-logs.png "Dzienniki aprowizowania")


Dziennik inicjowania obsługi administracyjnej ma domyślny widok listy, który pokazuje:

- Tożsamość
- Akcja
- System źródłowy
- System docelowy
- Status
- Data


![Kolumny domyślne](./media/concept-provisioning-logs/default-columns.png "Kolumny domyślne")

Możesz dostosować widok listy, klikając pozycję **Kolumny** na pasku narzędzi.

![Wybór kolumny](./media/concept-provisioning-logs/column-chooser.png "Wybór kolumny")

Dzięki temu możesz wyświetlić dodatkowe pola lub usunąć pola, które są już wyświetlane.

![Dostępne kolumny](./media/concept-provisioning-logs/available-columns.png "Dostępne kolumny")

Wybierz element w widoku listy, aby uzyskać bardziej szczegółowe informacje.

![Szczegółowe informacje](./media/concept-provisioning-logs/steps.png "Filtr")


## <a name="filter-provisioning-activities"></a>Filtrowanie działań inicjowania obsługi administracyjnej

Aby zawęzić zgłoszone dane do poziomu, który działa dla Ciebie, można filtrować dane inicjowania obsługi administracyjnej przy użyciu następujących pól domyślnych. Należy zauważyć, że wartości w filtrach są dynamicznie wypełniane na podstawie dzierżawy. Jeśli na przykład nie masz żadnych zdarzeń tworzenia w dzierżawie, nie będzie opcji filtrowania do tworzenia.

- Tożsamość
- Akcja
- System źródłowy
- System docelowy
- Stan
- Data


![Filtr](./media/concept-provisioning-logs/filter.png "Filtr")

**Filtr Tożsamości** umożliwia określenie nazwy lub tożsamości, na której Ci zależy. Ta tożsamość może być użytkownik, grupa, rola lub inny obiekt. Można wyszukiwać według nazwy lub identyfikatora obiektu. Identyfikator różni się w zależności od scenariusza. Na przykład podczas inicjowania obsługi administracyjnej obiektu z usługi Azure AD do SalesForce identyfikator źródła jest identyfikatorem obiektu użytkownika w usłudze Azure AD, podczas gdy identyfikator docelowy jest identyfikatorem użytkownika w salesforce. Podczas inicjowania obsługi administracyjnej z workday do usługi Active Directory identyfikator źródłowy jest identyfikatorem pracownika pracownika workday. Należy zauważyć, że nazwa użytkownika nie zawsze może być obecny w identity kolumny. Zawsze będzie jeden identyfikator. 

**Filtr System źródłowy** umożliwia określenie, gdzie tożsamość jest coraz aprowizowana z. Na przykład podczas inicjowania obsługi administracyjnej obiektu z usługi Azure AD do ServiceNow, system źródłowy jest Azure AD. 

Filtr **systemu docelowego** umożliwia określenie, gdzie tożsamość jest coraz aprowizowana do. Na przykład podczas inicjowania obsługi administracyjnej obiektu z usługi Azure AD do ServiceNow, system docelowy jest ServiceNow. 

Filtr **Stan** umożliwia wybranie:

- Wszystkie
- Powodzenie
- Niepowodzenie
- Pominięto

Filtr **Akcja** umożliwia filtrowanie:

- Utwórz 
- Aktualizacja
- Usuń
- Wyłączanie
- Inne

Filtr **Data** umożliwia zdefiniowanie przedziału czasu dla zwracanych danych.  
Możliwe wartości:

- 1 miesiąc
- 7 dni
- 30 dni
- 24 godziny
- Niestandardowy zakres czasu

Po wybraniu niestandardowego przedziału czasu można skonfigurować datę rozpoczęcia i datę zakończenia.


Oprócz pól domyślnych, gdy jest zaznaczona, w filtrze można również uwzględnić następujące pola:

- **Identyfikator zadania** — unikatowy identyfikator zadania jest skojarzony z każdą aplikacją, dla której włączono inicjowanie obsługi administracyjnej.   

- **Identyfikator cyklu** — jednoznacznie identyfikuje cykl inicjowania obsługi administracyjnej. Można udostępnić ten identyfikator do obsługi, aby wyszukać cykl, w którym wystąpiło to zdarzenie.

- **Zmień identyfikator** — unikatowy identyfikator zdarzenia inicjowania obsługi administracyjnej. Można udostępnić ten identyfikator do obsługi, aby wyszukać zdarzenie inicjowania obsługi administracyjnej.   



  

## <a name="provisioning-details"></a>Szczegóły inicjowania obsługi administracyjnej 

Po wybraniu elementu w widoku listy inicjowania obsługi administracyjnej, można uzyskać więcej szczegółów na temat tego elementu.
Szczegóły są pogrupowane na podstawie następujących kategorii:

- Kroki

- Rozwiązywanie problemów i zalecenia

- Zmodyfikowane właściwości

- Podsumowanie


![Filtr](./media/concept-provisioning-logs/provisioning-tabs.png "Karty")



### <a name="steps"></a>Kroki

Na karcie **Kroki** przedstawiono kroki podjęte w celu aprowizowania obiektu. Inicjowanie obsługi administracyjnej obiektu może składać się z czterech kroków: 

- Importowanie obiektu
- Określanie, czy obiekt znajduje się w zakresie
- Dopasowywalaj obiekt między źródłem a obiektem docelowym
- Obiekt aprowizowania (podejmij działanie — może to być tworzenie, aktualizowanie, usuwanie lub wyłączanie)



![Filtr](./media/concept-provisioning-logs/steps.png "Filtr")


### <a name="troubleshoot-and-recommendations"></a>Rozwiązywanie problemów i zalecenia


Karta **Rozwiązywanie problemów i zalecenia** zawiera kod błędu i przyczynę. Informacje o błędzie są dostępne tylko w przypadku awarii. 


### <a name="modified-properties"></a>Zmodyfikowane właściwości

**Zmodyfikowane właściwości** pokazuje starą wartość i nową wartość. W przypadkach, gdy nie ma starej wartości, stara kolumna wartości jest pusta. 


### <a name="summary"></a>Podsumowanie

Karta **podsumowanie** zawiera omówienie tego, co się stało i identyfikatory obiektu w systemie źródłowym i docelowym. 

## <a name="what-you-should-know"></a>Co należy wiedzieć

- Portal Azure przechowuje zgłaszane dane inicjowania obsługi administracyjnej przez 30 dni, jeśli masz wersji premium i 7 dni, jeśli masz bezpłatną wersję..

- Atrybut Zmień identyfikator można użyć jako unikatowego identyfikatora. Jest to, na przykład, przydatne podczas interakcji z pomocą techniczną produktu.

- Obecnie nie ma opcji pobierania danych aprowizujących.

- Obecnie nie ma obsługi analizy dzienników.

- Po dodaniu dostępu do dzienników inicjowania obsługi administracyjnej z kontekstu aplikacji, nie automatycznie filtrować zdarzenia do określonej aplikacji sposób dzienniki inspekcji zrobić.

## <a name="error-codes"></a>Kody błędów

Skorzystaj z poniższej tabeli, aby lepiej zrozumieć, jak rozwiązywać błędy, które można znaleźć w dziennikach inicjowania obsługi administracyjnej. W przypadku brakujących kodów błędów prześlij opinię za pomocą łącza u dołu tej strony. 

|Kod błędu|Opis|
|---|---|
|Konflikt, EntryConflict|Popraw wartości atrybutów powodujących konflikt w usłudze Azure AD lub aplikacji lub przejrzyj dopasowaną konfigurację atrybutu, jeśli konto użytkownika powodujące konflikt miało zostać dopasowane i przejęte. Zapoznaj się z [poniższą dokumentacją,](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) aby uzyskać więcej informacji na temat konfigurowania pasujących atrybutów.|
|TooManyRequests (Prośby o nie)|Aplikacja docelowa odrzuciła tę próbę aktualizacji użytkownika, ponieważ jest przeciążony i odbiera zbyt wiele żądań. Nie ma nic do zrobienia. Ta próba zostanie automatycznie wycofana. Firma Microsoft została również powiadomiona o tym problemie.|
|InternalServerError |Aplikacja docelowa zwróciła nieoczekiwany błąd. Może występować problem z usługą aplikacji docelowej, który uniemożliwia działanie tego problemu. Ta próba zostanie automatycznie wycofana w ciągu 40 minut.|
|InsufficientRights, MethodNotAllowed, NotPermitted, Unauthorized| Usługa Azure AD mogła uwierzytelnić się za pomocą aplikacji docelowej, ale nie została autoryzowana do wykonania aktualizacji. Proszę przejrzeć wszelkie instrukcje dostarczone przez aplikację docelową, jak również odpowiedniego [samouczka](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)aplikacji .|
|Nieprzetworzalnaentność|Aplikacja docelowa zwróciła nieoczekiwaną odpowiedź. Konfiguracja aplikacji docelowej może nie być poprawna lub może występować problem z usługą aplikacji docelowej, który uniemożliwia działanie tego problemu.|
|WebExceptionProtocolError |Wystąpił błąd protokołu HTTP podczas łączenia się z aplikacją docelową. Nie ma nic do zrobienia. Ta próba zostanie automatycznie wycofana w ciągu 40 minut.|
|InvalidAnchor (Inwalida, że)|Użytkownik, który został wcześniej utworzony lub dopasowany przez usługę inicjowania obsługi administracyjnej już nie istnieje. Sprawdź, czy użytkownik istnieje. Aby wymusić ponowne dopasowanie wszystkich użytkowników, użyj interfejsu API programu MS Graph, aby [ponownie uruchomić zadanie](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-restart?view=graph-rest-beta&tabs=http). Należy zauważyć, że ponowne uruchomienie inicjowania obsługi administracyjnej spowoduje początkowy cykl, który może zająć trochę czasu. Usuwa również pamięć podręczną, której usługa inicjowania obsługi administracyjnej używa do działania, co oznacza, że wszyscy użytkownicy i grupy w dzierżawie będą musiały zostać ponownie ocenione i niektóre zdarzenia inicjowania obsługi administracyjnej mogą zostać usunięte.|
|Nienałożony | Aplikacja docelowa zwróciła nieoczekiwaną odpowiedź. Konfiguracja aplikacji może być nieprawidłowa lub może występować problem z usługą aplikacji docelowej, który uniemożliwia działanie tej aplikacji. Proszę przejrzeć wszelkie instrukcje dostarczone przez aplikację docelową, jak również odpowiedniego [samouczka](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)aplikacji . |
|MandatoryFieldsMissing, Brakwartości |Nie można utworzyć użytkownika, ponieważ brakuje wymaganych wartości. Popraw brakujące wartości atrybutów w rekordzie źródłowym lub przejrzyj dopasowaną konfigurację atrybutu, aby upewnić się, że wymagane pola nie zostaną pominięte. [Dowiedz się więcej](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) o konfigurowaniu pasujących atrybutów.|
|SchemaAttributeNotFound |Nie można wykonać operacji, ponieważ określono atrybut, który nie istnieje w aplikacji docelowej. Zapoznaj się z [dokumentacją](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) dostosowywania atrybutów i upewnij się, że konfiguracja jest poprawna.|
|InternalError |Wystąpił błąd usługi wewnętrznej w usłudze inicjowania obsługi administracyjnej usługi Azure AD. Nie ma nic do zrobienia. Ta próba zostanie automatycznie ponowiona w ciągu 40 minut.|
|Domena nieprawidłowa |Nie można wykonać operacji z powodu wartości atrybutu zawierającej nieprawidłową nazwę domeny. Zaktualizuj nazwę domeny użytkownika lub dodaj ją do listy dozwolonych w aplikacji docelowej. |
|Limit czasu |Nie można ukończyć operacji, ponieważ aplikacja docelowa trwała zbyt długo, aby odpowiedzieć. Nie ma nic do zrobienia. Ta próba zostanie automatycznie ponowiona w ciągu 40 minut.|
|LicenseLimitExceeded|Nie można utworzyć użytkownika w aplikacji docelowej, ponieważ nie ma dostępnych licencji dla tego użytkownika. Zaopatrzyć się w dodatkowe licencje dla aplikacji docelowej lub przejrzeć przypisania użytkowników i konfiguracji mapowania atrybutów, aby upewnić się, że poprawni użytkownicy są przypisane z poprawnymi atrybutami.|
|DuplicateTargetEntries (Zduplikowane celeety)  |Nie można ukończyć operacji, ponieważ znaleziono więcej niż jednego użytkownika w aplikacji docelowej ze skonfigurowanymi pasującymi atrybutami. Usuń zduplikowanego użytkownika z aplikacji docelowej lub ponownie skonfiguruj mapowania atrybutów zgodnie z opisem [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|
|DuplicateSourceEntries (Zduplikowane źródłowych) | Nie można ukończyć operacji, ponieważ znaleziono więcej niż jednego użytkownika ze skonfigurowanymi pasującymi atrybutami. Usuń zduplikowanego użytkownika lub skonfiguruj ponownie mapowania atrybutów zgodnie z opisem [w tym miejscu](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes).|

## <a name="next-steps"></a>Następne kroki

* [Sprawdzanie stanu inicjowania obsługi administracyjnej przez użytkowników](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)
* [Problem z konfigurowaniem inicjowania obsługi administracyjnej przez użytkownika aplikacji usługi Azure AD Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem)


