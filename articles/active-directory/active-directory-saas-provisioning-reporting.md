---
title: Raporty dotyczące inicjowania obsługi konta użytkowników usługi Azure Active Directory do aplikacji SaaS | Dokumentacja firmy Microsoft
description: Dowiedz się, jak sprawdzić stan inicjowania obsługi zadania konta użytkowników i jak rozwiązywać problemy z obsługi poszczególnych użytkowników.
services: active-directory
documentationcenter: ''
author: asmalser-msft
writer: asmalser-msft
manager: mtillman
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/12/2017
ms.author: asmalser-msft
ms.openlocfilehash: 1527e8fe28d395a77d7b3c1535e40fe59df1baba
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Samouczek: Raportowania na inicjowanie obsługi konta użytkowników


Usługa Azure Active Directory obejmuje [konta użytkownika usługi inicjowania obsługi administracyjnej](active-directory-saas-app-provisioning.md) który ułatwia automatyzację inicjowania obsługi administracyjnej anulowanie obsługi kont użytkowników w aplikacji SaaS i innymi systemami na potrzeby zarządzania cyklem życia tożsamości na trasie. Usługi Azure AD obsługuje wstępnie zintegrowanych użytkownika inicjowania obsługi administracyjnej łączniki dla wszystkich aplikacji i systemów w sekcji "Proponowanym" [galerii aplikacji Azure AD](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

W tym artykule opisano sposób sprawdzania stanu zadania obsługi, gdy zostały skonfigurowane i jak rozwiązywać problemy z inicjowania obsługi poszczególnych użytkowników i grup.

## <a name="overview"></a>Przegląd

Łączniki inicjowania obsługi administracyjnej są głównie Konfigurowanie i skonfigurować za pomocą [portalu zarządzania Azure](https://portal.azure.com), wykonując [podane dokumentacji](active-directory-saas-tutorial-list.md) dla aplikacji, których inicjowanie obsługi konta użytkownika jest potrzebne. Gdy skonfigurowana i uruchomiona, inicjowania obsługi administracyjnej zadania dla aplikacji mogą być zgłaszane przy użyciu jednej z dwóch metod:

* **Portal zarządzania Azure** — w tym artykule opisano głównie podczas pobierania informacji w raporcie z [portalu zarządzania Azure](https://portal.azure.com), zapewniające zarówno inicjowania obsługi administracyjnej raport z podsumowaniem jak i szczegółowe inspekcji inicjowania obsługi administracyjnej Dzienniki dla danej aplikacji.

* **Interfejs API inspekcji** — Azure Active Directory oferuje również inspekcji interfejsu API, który umożliwia pobieranie programowe szczegółowe dzienniki inspekcji inicjowania obsługi administracyjnej. Zobacz [inspekcji usługi Azure Active Directory dokumentacja interfejsu API](active-directory-reporting-api-audit-reference.md) dokumentacji dotyczące korzystania z tego interfejsu API. Gdy w tym artykule nie opisano w szczególności sposób użycia interfejsu API, jego szczegóły typy obsługi zdarzeń, które są rejestrowane w dzienniku inspekcji.

### <a name="definitions"></a>Definicje

W tym artykule używane następujące pojęcia określonych poniżej:

* **Źródłowy System** -użytkowników, które synchronizuje usługi Azure AD usługi inicjowania obsługi administracyjnej z repozytorium. Azure Active Directory jest w systemie źródłowym dla większości wstępnie zintegrowanych łączników udostępniania, jednak istnieje kilka wyjątków (przykład: produktu Workday synchronizacji ruchu przychodzącego).

* **Docelowy System** -użytkowników, które synchronizuje usługi Azure AD, świadczenie usługi do repozytorium. Jest to zazwyczaj aplikacji SaaS (przykłady: Salesforce, usługi ServiceNow, Google Apps, Dropbox dla firm), jednak w niektórych przypadkach może być systemu lokalnego, takie jak Active Directory (przykład: synchronizacji ruchu przychodzącego dla produktu Workday do usługi Active Directory).


## <a name="getting-provisioning-reports-from-the-azure-management-portal"></a>Pobieranie inicjowania obsługi administracyjnej raporty z portalu zarządzania platformy Azure

Aby pobrać udostępniania informacji w raporcie dla danej aplikacji, najpierw uruchamianie [portalu zarządzania Azure](https://portal.azure.com) i przechodząc do aplikacji przedsiębiorstwa, dla której skonfigurowano usługę inicjowania obsługi administracyjnej. Na przykład w przypadku udostępniania użytkownikom LinkedIn podniesienia uprawnień, ścieżka nawigacji do szczegółów aplikacji jest:

**Usługa Azure Active Directory > aplikacje dla przedsiębiorstw > wszystkie aplikacje > LinkedIn podniesienia uprawnień**

W tym miejscu można uzyskać dostępu do raportu podsumowania inicjowania obsługi administracyjnej i inicjowania obsługi administracyjnej dzienniki inspekcji, oba opisane poniżej.


### <a name="provisioning-summary-report"></a>Raport z podsumowaniem inicjowania obsługi administracyjnej

Raport podsumowania inicjowania obsługi administracyjnej jest widoczny w **inicjowania obsługi administracyjnej** karcie daną aplikację. Znajduje się w sekcji szczegółów synchronizacji poniżej **ustawienia**i zawiera następujące informacje:

* Całkowita liczba użytkowników i / grupy które zostały zsynchronizowane i są obecnie w zakresie obsługi między systemu źródłowego i docelowego systemu.

* Czas ostatniej synchronizacji zostało uruchomione. Synchronizacja odbyła zazwyczaj co 20 40 minut, po zakończeniu pełnej synchronizacji.

* Określa, czy początkowa Pełna synchronizacja została ukończona.

* Określa, czy Proces inicjowania obsługi administracyjnej zostały umieszczone w kwarantannie i Przyczyna stanu kwarantanny to np. (błąd komunikacji z systemu docelowego z powodu poświadczeń administratora nieprawidłowe)

Podsumowanie inicjowania obsługi administracyjnej powinien być pierwszym wygląd Administratorzy miejsca aby sprawdzić kondycję operacyjną zadanie inicjowania obsługi administracyjnej.

 ![Raport z podsumowaniem](./media/active-directory-saas-provisioning-reporting/summary_report.PNG)

### <a name="provisioning-audit-logs"></a>Dzienniki inspekcji inicjowania obsługi administracyjnej
Wszystkie działania wykonywane przez usługę inicjowania obsługi administracyjnej są rejestrowane w dziennikach inspekcji usługi Azure AD, które można wyświetlić w **dzienniki inspekcji** w obszarze **Inicjowanie obsługi konta** kategorii. Typy zdarzeń rejestrowane działania:

* **Importowanie zdarzeń** — "import" zdarzenie jest rejestrowane zawsze usługi Azure AD usługi inicjowania obsługi administracyjnej pobiera informacje o poszczególnych użytkowników i grup z systemu źródłowego lub systemu docelowego. Podczas synchronizacji użytkownicy są pobierane z systemu źródłowego najpierw z wynikami rejestrowane jako "przycisk Importuj, zdarzenia. Zgodnymi identyfikatorami pobrane użytkowników są następnie zapytanie względem systemu docelowego, aby sprawdzić, czy istnieją one z wynikami, w zarejestrowany jako "przycisk Importuj, zdarzenia. Te zdarzenia rejestrować wszystkie atrybuty zamapowane użytkowników i ich wartości, które zostały odebrane przez usługę Azure AD w czasie zdarzenia inicjowania obsługi usługi. 

* **Zdarzenia reguły synchronizacji** — te zdarzenia raportu dotyczącego wyników reguły mapowania atrybutu i dowolne skonfigurowane filtry zakresu po dane użytkownika zostały zaimportowane i obliczone z systemów źródłowych i docelowych. Na przykład jeśli użytkownik w systemie źródłowym jest uważany w zakresie obsługi i uznane za nie istnieje w systemie docelowym, a następnie rejestruje tego zdarzenia, który użytkownik zostanie zainicjowana w systemie docelowym. 

* **Eksportowanie zdarzeń** — "export" zdarzenie jest rejestrowane zawsze usługi Azure AD, świadczenie usługi zapisuje obiekt konta lub grupy użytkowników do systemu docelowego. Te zdarzenia rejestrować wszystkie atrybuty użytkowników i ich wartości, które zostały napisane przez usługę Azure AD w czasie zdarzenia inicjowania obsługi usługi. Jeśli wystąpił błąd podczas zapisywania obiektu konta lub grupy użytkowników do systemu docelowego, będzie wyświetlana w tym miejscu.

* **Przetwarzanie zdarzeń depozytu** -Escrow — proces wystąpić podczas inicjowania obsługi administracyjnej usługi napotka błąd podczas próby wykonania operacji i rozpocznie się ponowić próbę wykonania operacji na wycofania interwał czasu. Zdarzenie "depozytu" rejestrowany jest zawsze, gdy operacji inicjowania obsługi administracyjnej została wycofana.

Podczas przeglądania inicjowania obsługi zdarzeń dla poszczególnych użytkowników, zdarzenia normalnie występuje w następującej kolejności:

1. Importowanie zdarzeń: użytkownika są pobierane z systemu źródłowego.

2. Importowanie zdarzeń: system docelowy jest wysyłane do sprawdzania istnienia pobrane użytkownika.

3. Zdarzenia reguły synchronizacji: dane użytkownika z systemów źródłowych i docelowych są obliczane względem skonfigurowany atrybut mapowania reguł i określania zakresu filtrów w celu określenia, jakie działania, należy wykonać.

4. Eksportowanie zdarzeń: Jeśli zdarzenia reguły synchronizacji definiowane, że działanie powinno być wykonywane (np. Add, Update, Delete), a następnie wyniki akcji są rejestrowane w przypadku eksportowania.

![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-provisioning-reporting/audit_logs.PNG)


### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Wyszukiwanie inicjowania obsługi zdarzeń dla określonego użytkownika

Najbardziej typowe przypadek użycia dla inicjowania obsługi administracyjnej dzienników inspekcji jest sprawdzać stan inicjowania obsługi administracyjnej indywidualne konto użytkownika. Aby sprawdzić ostatniego zdarzenia inicjowania obsługi administracyjnej dla określonego użytkownika:

1. Przejdź do **dzienniki inspekcji** sekcji.

2. Z **kategorii** menu, wybierz opcję **Inicjowanie obsługi konta**.

3. W **zakres dat** menu, wybierz zakres dat, który chcesz przeszukać,

4. W **wyszukiwania** pasek, wprowadź identyfikator użytkownika, który chcesz wyszukać użytkownika. Format wartości ID powinna odpowiadać niezależnie od wybranego jako podstawowy identyfikator dopasowania w konfiguracji mapowania atrybutu (np. userPrincipalName lub pracowników numer identyfikacyjny). Wartość Identyfikatora wymagane będzie widoczny w kolumnie elementów docelowych.

5. Naciśnij klawisz Enter, aby wyszukać. Najpierw zostanie zwrócony najnowszych zdarzeń, inicjowania obsługi administracyjnej.

6. Zdarzenia są zwracane, należy pamiętać typów działań i czy powodzeniem lub niepowodzeniem. Jeśli żadne wyniki nie są zwracane, następnie oznacza użytkownika nie istnieje albo nie jeszcze został wykryty przez proces inicjowania obsługi administracyjnej Jeśli pełnej synchronizacji nie zostało jeszcze zakończone.

7. Polecenie pojedynczych zdarzeń, aby wyświetlić szczegóły rozszerzonej, w tym wszystkie właściwości użytkownika, które zostały pobrane, obliczenia lub zapisywane jako część zdarzenia.


### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Porady dotyczące wyświetlania inicjowania obsługi administracyjnej dziennika inspekcji

Najlepsze czytelności w portalu Azure, wybierz **kolumn** przycisk i wybierz następujące kolumny:

* **Data** — umożliwia wyświetlenie daty wystąpiło zdarzenie.
* **Docelowych** — zawiera nazwę i użytkownika identyfikator aplikacji są tematy zdarzenia.
* **Działanie** — typ działania, jak opisano wcześniej.
* **Stan** — czy zdarzenie zakończyło się powodzeniem, czy nie.
* **Przyczyna stanu** — podsumowanie co się stało, w przypadku inicjowania obsługi administracyjnej.


## <a name="troubleshooting"></a>Rozwiązywanie problemów

Inicjowania obsługi administracyjnej podsumowania dzienniki raportu i inspekcji odgrywają kluczową rolę ułatwienia Administratorzy Rozwiązywanie problemów z różnych inicjowania obsługi problemów z konta użytkownika.

Oparta na scenariuszu wskazówki na temat rozwiązywania problemów inicjowania obsługi użytkowników, zobacz [problemów, konfigurowanie i Inicjowanie obsługi użytkowników aplikacji](active-directory-application-provisioning-content-map.md).


## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie aprowizacja konta użytkowników dla aplikacji przedsiębiorstwa](active-directory-enterprise-apps-manage-provisioning.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)
