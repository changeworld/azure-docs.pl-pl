---
title: Zgłoś automatyczne Inicjowanie obsługi kont użytkowników w aplikacjach SaaS | Microsoft Docs
description: Dowiedz się, jak sprawdzić stan automatycznych zadań aprowizacji konta użytkownika i jak rozwiązywać problemy z obsługą poszczególnych użytkowników.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: fda7654ca2d825ae4112dd06021c7e83ed6867cd
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/22/2019
ms.locfileid: "68381244"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Samouczek: Raportowanie dotyczące automatycznego aprowizacji kont użytkowników

Usługa Azure Active Directory (Azure AD) obejmuje [usługę aprowizacji kont użytkowników](user-provisioning.md) , która pomaga zautomatyzować inicjowanie aprowizacji kont użytkowników w aplikacjach SaaS i innych systemach, na potrzeby kompleksowego zarządzania cyklem życia tożsamości. Usługa Azure AD obsługuje wstępnie zintegrowane łączniki aprowizacji użytkowników dla wszystkich aplikacji i systemów w sekcji "Polecane" w [galerii aplikacji usługi Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1&subcategories=featured).

W tym artykule opisano sposób sprawdzania stanu zadań aprowizacji po ich skonfigurowaniu oraz rozwiązywania problemów z inicjowaniem obsługi poszczególnych użytkowników i grup.

## <a name="overview"></a>Omówienie

Łączniki aprowizacji są konfigurowane i konfigurowane przy użyciu [Azure Portal](https://portal.azure.com), zgodnie z [podaną dokumentacją](../saas-apps/tutorial-list.md) dla obsługiwanej aplikacji. Po skonfigurowaniu i uruchomieniu zadań aprowizacji można zgłaszać przy użyciu jednej z dwóch metod:

* **Azure Portal** — w tym artykule opisano pobieranie informacji o raportach z [Azure Portal](https://portal.azure.com), które zawierają Raport z podsumowaniem udostępniania oraz szczegółowe dzienniki inspekcji dla danej aplikacji.
* **Interfejs API inspekcji** — Azure Active Directory również udostępnia interfejs API inspekcji, który umożliwia programistyczne pobieranie szczegółowych dzienników inspekcji aprowizacji. Informacje dotyczące korzystania z tego interfejsu API można znaleźć w dokumentacji dotyczącej [interfejsu API inspekcji Azure Active Directory](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) . Chociaż w tym artykule nie opisano sposobu korzystania z interfejsu API, szczegółowe informacje o typach zdarzeń aprowizacji, które są rejestrowane w dzienniku inspekcji.

### <a name="definitions"></a>Definicje

W tym artykule opisano następujące warunki:

* **System źródłowy** — repozytorium użytkowników, z których usługa Azure AD Provisioning synchronizuje. Azure Active Directory jest systemem źródłowym większości wstępnie zintegrowanych łączników aprowizacji, jednak istnieją pewne wyjątki (przykład: Synchronizacja ruchu przychodzącego z produktu Workday).
* **System docelowy** — repozytorium użytkowników, do których jest synchronizowana usługa Azure AD Provisioning. Jest to zazwyczaj aplikacja SaaS (przykłady: Salesforce, usługi ServiceNow, G Suite, Dropbox dla firm), ale w niektórych przypadkach może to być system lokalny, taki jak Active Directory (przykład: Synchronizacja ruchu przychodzącego z programu Workday do Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Uzyskiwanie raportów aprowizacji z Azure Portal

Aby uzyskać informacje o raportowaniu dla danej aplikacji, Zacznij od uruchomienia [Azure Portal](https://portal.azure.com) i przechodzenie do aplikacji przedsiębiorstwa, dla której skonfigurowano obsługę administracyjną. Na przykład w przypadku aprowizacji użytkowników do podniesienia uprawnień do serwisu LinkedIn ścieżka nawigacji do szczegółów aplikacji jest:

**Azure Active Directory > aplikacje dla przedsiębiorstw > Wszystkie aplikacje > podnoszenie uprawnień do serwisu LinkedIn**

W tym miejscu możesz uzyskać dostęp do raportu podsumowania aprowizacji i dzienników inspekcji aprowizacji, które opisano poniżej.

## <a name="provisioning-summary-report"></a>Raport z podsumowaniem aprowizacji

Raport podsumowujący aprowizacji jest widoczny na karcie **aprowizacji** dla danej aplikacji. Znajduje się w sekcji **szczegóły synchronizacji** poniżej **ustawień**i zawiera następujące informacje:

* Łączna liczba użytkowników i/grup, które zostały zsynchronizowane i są obecnie w zakresie aprowizacji między systemem źródłowym a systemem docelowym.
* Ostatnia synchronizacja została uruchomiona. Synchronizacje zwykle odbywają się co 20-40 minut po zakończeniu [początkowej synchronizacji](user-provisioning.md#what-happens-during-provisioning) .
* Czy [początkowa synchronizacja](user-provisioning.md#what-happens-during-provisioning) została ukończona.
* Czy proces aprowizacji został umieszczony w kwarantannie, a powód stanu kwarantanny to (na przykład niepowodzenie komunikacji z systemem docelowym z powodu nieprawidłowych poświadczeń administratora).

Raport z podsumowaniem udostępniania powinien być pierwszym miejscem, w którym administratorzy muszą sprawdzić kondycję operacyjną zadania aprowizacji.

 ![Raport z podsumowaniem](./media/check-status-user-account-provisioning/summary_report.PNG)

## <a name="provisioning-audit-logs"></a>Dzienniki inspekcji aprowizacji

Wszystkie działania wykonywane przez usługę aprowizacji są rejestrowane w dziennikach inspekcji usługi Azure AD, które można wyświetlić na karcie **dzienniki inspekcji** w kategorii **Inicjowanie obsługi konta** . Zarejestrowane typy zdarzeń działania obejmują:

* **Importuj zdarzenia** — zdarzenie "Import" jest rejestrowane za każdym razem, gdy usługa Azure AD Provisioning pobiera informacje o pojedynczym użytkowniku lub grupie z systemu źródłowego lub docelowego. Podczas synchronizacji użytkownicy są najpierw pobierani z systemu źródłowego, z wynikami zarejestrowanymi jako zdarzenia "Import". Identyfikatory pasujących pobranych użytkowników są następnie wysyłane do systemu docelowego w celu sprawdzenia, czy istnieją, a wyniki są również rejestrowane jako zdarzenia "Import". Te zdarzenia rejestrują wszystkie zamapowane atrybuty użytkownika i ich wartości, które były widoczne w usłudze Azure AD Provisioning w momencie zdarzenia.
* **Zdarzenia reguły synchronizacji** — te zdarzenia są raportowane względem wyników reguł mapowania atrybutów i wszelkich skonfigurowanych filtrów zakresu, po zaimportowaniu danych użytkownika i ich ocenie z systemów źródłowych i docelowych. Na przykład, jeśli użytkownik w systemie źródłowym jest uznawany za w zakresie aprowizacji i uznawany za nieistniejący w systemie docelowym, to zdarzenie to rejestruje, że użytkownik zostanie zainicjowany w systemie docelowym.
* **Eksportuj zdarzenia** — zdarzenie "Export" jest rejestrowane za każdym razem, gdy usługa Azure AD Provisioning zapisuje obiekt konta użytkownika lub grupy w systemie docelowym. Te zdarzenia rejestrują wszystkie atrybuty użytkownika i ich wartości, które zostały wprowadzone w usłudze Azure AD Provisioning w momencie zdarzenia. Jeśli wystąpił błąd podczas zapisywania obiektu konta użytkownika lub grupy w systemie docelowym, zostanie on wyświetlony w tym miejscu.
* **Przetwarzaj zdarzenia Escrow** — proces obsługi Escrow występuje, gdy usługa aprowizacji napotka błąd podczas próby wykonania operacji, i rozpocznie ponawianie próby wykonania operacji w przedziale czasu. Zdarzenie "Escrow" jest rejestrowane za każdym razem, gdy podjęto próbę wykonania operacji aprowizacji.

Podczas przeglądania zdarzeń aprowizacji dla pojedynczego użytkownika zdarzenia zwykle występują w następującej kolejności:

1. Importuj zdarzenie: Użytkownik jest pobierany z systemu źródłowego.
1. Importuj zdarzenie: System docelowy jest proszony o sprawdzenie obecności pobranego użytkownika.
1. Zdarzenie reguły synchronizacji: Dane użytkownika z systemów źródłowych i docelowych są oceniane względem skonfigurowanych reguł mapowania atrybutów i filtrów określania zakresu, aby określić, jaka akcja ma być wykonana.
1. Eksportuj zdarzenie: Jeśli zdarzenie reguły synchronizacji wykryło, że akcja powinna zostać wykonana (Dodawanie, aktualizowanie, usuwanie), wyniki akcji są rejestrowane w zdarzeniu eksportu.

   ![Przykład: Strona dziennika inspekcji przedstawiająca działania i stan](./media/check-status-user-account-provisioning/audit_logs.PNG)

### <a name="looking-up-provisioning-events-for-a-specific-user"></a>Wyszukiwanie zdarzeń aprowizacji dla określonego użytkownika

Najczęstszym przypadkiem użycia w dziennikach inspekcji aprowizacji jest sprawdzenie stanu aprowizacji poszczególnych kont użytkowników. Aby wyszukać ostatnie zdarzenia aprowizacji dla określonego użytkownika:

1. Przejdź do sekcji **dzienniki inspekcji** .
1. Z menu **Kategoria** wybierz opcję **Inicjowanie obsługi konta**.
1. W menu **zakres dat** wybierz zakres dat, który chcesz wyszukać.
1. Na pasku **wyszukiwania** wprowadź identyfikator użytkownika, który chcesz wyszukać. Format wartości identyfikatora powinien być taki sam, jak wybrany jako główny identyfikator dopasowania w konfiguracji mapowania atrybutów (na przykład userPrincipalName lub numer IDENTYFIKACYJNy pracownika). Wymagana wartość identyfikatora będzie widoczna w kolumnie targets (s).
1. Naciśnij klawisz ENTER, aby wyszukać. Najnowsze zdarzenia aprowizacji zostaną zwrócone jako pierwsze.
1. Jeśli są zwracane zdarzenia, należy zwrócić uwagę na typy działań oraz to, czy zakończyły się powodzeniem, czy niepowodzeniem. Jeśli żadne wyniki nie zostaną zwrócone, oznacza to, że użytkownik nie istnieje lub nie został jeszcze wykryty przez proces aprowizacji, jeśli pełna synchronizacja nie została jeszcze ukończona.
1. Kliknij poszczególne zdarzenia, aby wyświetlić rozszerzone szczegóły, w tym wszystkie właściwości użytkowników, które zostały pobrane, ocenione lub zapisywana jako część zdarzenia.

Aby zapoznać się z prezentacją dotyczącą korzystania z dzienników inspekcji, zobacz film wideo poniżej. Dzienniki inspekcji są prezentowane wokół 5:30 znacznika:

> [!VIDEO https://www.youtube.com/embed/pKzyts6kfrw]

### <a name="tips-for-viewing-the-provisioning-audit-logs"></a>Porady dotyczące wyświetlania dzienników inspekcji aprowizacji

Aby zapewnić najlepszą czytelność w Azure Portal, wybierz przycisk **kolumny** , a następnie wybierz następujące kolumny:

* **Date** — pokazuje datę wystąpienia zdarzenia.
* **Elementy docelowe** — pokazuje nazwę aplikacji i identyfikator użytkownika, które są tematami zdarzenia.
* **Działanie** — typ działania opisany wcześniej.
* **Stan** — czy zdarzenie zakończyło się pomyślnie.
* **Przyczyna stanu** — podsumowanie informacji o tym, co się stało w zdarzeniu aprowizacji.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Raport z podsumowaniem udostępniania i dzienniki inspekcji odgrywają kluczową rolę pomagającą administratorom rozwiązywać różne problemy z inicjowaniem obsługi konta użytkownika.

Aby zapoznać się ze wskazówkami dotyczącymi sposobu rozwiązywania problemów dotyczących automatycznego aprowizacji użytkowników, zobacz [problemy dotyczące konfigurowania i aprowizacji użytkowników w aplikacji](application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Zarządzanie obsługą kont użytkowników w aplikacjach dla przedsiębiorstw](configure-automatic-user-provisioning-portal.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](what-is-single-sign-on.md)
