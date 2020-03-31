---
title: 'Samouczek: Konfigurowanie salesforce do automatycznego inicjowania obsługi administracyjnej za pomocą usługi Azure Active Directory| Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i usługą Salesforce.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 49384b8b-3836-4eb1-b438-1c46bb9baf6f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b8038896a11b65e835ce71f5fc34e85723cc91a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77060525"
---
# <a name="tutorial-configure-salesforce-for-automatic-user-provisioning"></a>Samouczek: Konfigurowanie salesforce do automatycznego inicjowania obsługi administracyjnej użytkowników

Celem tego samouczka jest pokazanie kroków wymaganych do wykonania w salesforce i usługi Azure AD, aby automatycznie aprowizować i usuwać z nich konta użytkowników z usługi Azure AD do salesforce.

## <a name="prerequisites"></a>Wymagania wstępne

Scenariusz opisany w tym samouczku zakłada, że masz już następujące elementy:

* Dzierżawa usługi Azure Active
* Najemca Salesforce.com

> [!IMPORTANT]
> Jeśli używasz konta Salesforce.com wersji próbnej, nie będzie można skonfigurować automatycznego inicjowania obsługi administracyjnej użytkowników. Konta próbne nie mają włączonego dostępu do interfejsu API, dopóki nie zostaną zakupione. Możesz obejść to ograniczenie, korzystając z bezpłatnego [konta dewelopera,](https://developer.salesforce.com/signup) aby ukończyć ten samouczek.

Jeśli używasz środowiska izolowatego Salesforce, zapoznaj się z [samouczkiem integracji z piaskownicą Salesforce](https://go.microsoft.com/fwLink/?LinkID=521879).

## <a name="assigning-users-to-salesforce"></a>Przypisywanie użytkowników do salesforce

Usługa Azure Active Directory używa pojęcia o nazwie "przydziały", aby określić, którzy użytkownicy powinni otrzymać dostęp do wybranych aplikacji. W kontekście automatycznego inicjowania obsługi administracyjnej konta użytkownika tylko użytkownicy i grupy, które zostały "przypisane" do aplikacji w usłudze Azure AD jest synchronizowana.

Przed skonfigurowaniem i włączeniem usługi inicjowania obsługi administracyjnej należy zdecydować, którzy użytkownicy lub grupy w usłudze Azure AD potrzebują dostępu do aplikacji Salesforce. Po wykonaniu tej decyzji możesz przypisać tych użytkowników do aplikacji Salesforce, postępując zgodnie z instrukcjami w [aplikacji Przypisywanie użytkownika lub grupy do aplikacji przedsiębiorstwa](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-salesforce"></a>Ważne wskazówki dotyczące przypisywania użytkowników do Salesforce

* Zaleca się, że jeden użytkownik usługi Azure AD jest przypisany do Salesforce do testowania konfiguracji inicjowania obsługi administracyjnej. Dodatkowi użytkownicy i/lub grupy mogą być przypisane później.

* Podczas przypisywania użytkownika do Salesforce należy wybrać prawidłową rolę użytkownika. Rola "Dostęp domyślny" nie działa w przypadku inicjowania obsługi administracyjnej

    > [!NOTE]
    > Ta aplikacja importuje profile z salesforce w ramach procesu inicjowania obsługi administracyjnej, który klient może chcieć wybrać podczas przypisywania użytkowników w usłudze Azure AD. Należy pamiętać, że profile, które są importowane z Salesforce są wyświetlane jako role w usłudze Azure AD.

## <a name="enable-automated-user-provisioning"></a>Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników

W tej sekcji przewodnik po połączeniu usługi Azure AD z [interfejsem API inicjowania obsługi administracyjnej konta użytkownika salesforce — w wersji 40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api.meta/api/implementation_considerations.htm)i konfigurowaniu usługi inicjowania obsługi administracyjnej w celu tworzenia, aktualizowania i wyłączania przypisanych kont użytkowników w salesforce na podstawie przypisania użytkownika i grupy w usłudze Azure AD.

> [!Tip]
> Można również włączyć logowanie jednokrotne oparte na saml dla salesforce, postępując zgodnie z instrukcjami podanymi w [witrynie Azure portal.](https://portal.azure.com) Logowanie jednokrotne można skonfigurować niezależnie od automatycznego inicjowania obsługi administracyjnej, chociaż te dwie funkcje wzajemnie się uzupełniają.

### <a name="configure-automatic-user-account-provisioning"></a>Konfigurowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika

Celem tej sekcji jest określenie sposobu włączania obsługi administracyjnej kont użytkowników usługi Active Directory do salesforce.

1. W [witrynie Azure portal](https://portal.azure.com)przejdź do sekcji **Azure Active Directory > Enterprise Apps > wszystkie aplikacje.**

2. Jeśli salesforce został już skonfigurowany do logowania jednokrotnego, wyszukaj wystąpienie Salesforce za pomocą pola wyszukiwania. W przeciwnym razie wybierz pozycję **Dodaj** i wyszukaj **salesforce** w galerii aplikacji. Wybierz Salesforce z wyników wyszukiwania i dodaj ją do listy aplikacji.

3. Wybierz wystąpienie Salesforce, a następnie wybierz kartę **Inicjowanie obsługi administracyjnej.**

4. Ustaw **tryb inicjowania obsługi administracyjnej** na **Automatyczny**.

    ![Inicjowania obsługi](./media/salesforce-provisioning-tutorial/provisioning.png)

5. W sekcji **Poświadczenia administratora** podaj następujące ustawienia konfiguracji:

    a. W polach tekstowych **Nazwa administratora** wpisz nazwę konta Salesforce, do których przypisano profil **Administratora systemu** w Salesforce.com.

    b. W polach tekstowych **Hasło administratora** wpisz hasło dla tego konta.

6. Aby uzyskać token zabezpieczający Salesforce, otwórz nową kartę i zaloguj się na to samo konto administratora Salesforce. W prawym górnym rogu strony kliknij swoje imię i nazwisko, a następnie kliknij pozycję **Ustawienia**.

    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/salesforce-provisioning-tutorial/sf-my-settings.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

7. W lewym okienku nawigacji kliknij pozycję **Moje dane osobowe,** aby rozwinąć powiązaną sekcję, a następnie kliknij pozycję **Resetuj mój token zabezpieczający**.
  
    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/salesforce-provisioning-tutorial/sf-personal-reset.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

8. Na stronie **Reset token zabezpieczający** kliknij przycisk **Resetuj token zabezpieczający.**

    ![Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników](./media/salesforce-provisioning-tutorial/sf-reset-token.png "Włączanie automatycznego inicjowania obsługi administracyjnej przez użytkowników")

9. Sprawdź skrzynkę odbiorczą poczty e-mail skojarzoną z tym kontem administratora. Poszukaj wiadomości e-mail z Salesforce.com zawierającej nowy token zabezpieczający.

10. Skopiuj token, przejdź do okna usługi Azure AD i wklej go do pola **Tajny token.**

11. Adres **URL dzierżawy** należy wprowadzić, jeśli wystąpienie Salesforce znajduje się w chmurze dla instytucji rządowych Salesforce. W przeciwnym razie jest opcjonalny. Wprowadź adres URL dzierżawy w formacie\<"https://\>instancji .my.salesforce.com", \<zastępując wystąpienie\> nazwą instancji Salesforce.

12. W witrynie Azure portal kliknij pozycję **Testuj połączenie,** aby upewnić się, że usługa Azure AD może łączyć się z aplikacją Salesforce.

13. W polu **Wiadomość e-mail z powiadomieniem** wprowadź adres e-mail osoby lub grupy, która powinna otrzymywać powiadomienia o błędach inicjowania obsługi administracyjnej, i zaznacz pole wyboru poniżej.

14. Kliknij **pozycję Zapisz.**  

15. W sekcji Mapowania wybierz pozycję **Synchronizuj użytkowników usługi Azure Active Directory z salesforce.**

16. W sekcji **Mapowania atrybutów** przejrzyj atrybuty użytkownika, które są synchronizowane z usługi Azure AD do Salesforce. Należy zauważyć, że atrybuty wybrane jako **pasujące** właściwości są używane do dopasowania kont użytkowników w Salesforce dla operacji aktualizacji. Wybierz przycisk Zapisz, aby zatwierdzić wszelkie zmiany.

17. Aby włączyć usługę inicjowania obsługi administracyjnej usługi Azure AD dla salesforce, zmień **stan inicjowania obsługi administracyjnej** **na Włączone** w sekcji Ustawienia

18. Kliknij **pozycję Zapisz.**

> [!NOTE]
> Gdy użytkownicy są aprowizacji w aplikacji Salesforce, administrator musi skonfigurować ustawienia specyficzne dla języka dla nich. Zobacz [ten](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) artykuł, aby uzyskać więcej informacji na temat konfiguracji języka.

Spowoduje to rozpoczęcie początkowej synchronizacji wszystkich użytkowników i/lub grup przypisanych do Salesforce w sekcji Użytkownicy i grupy. Należy zauważyć, że synchronizacja początkowa trwa dłużej niż kolejne synchronizacje, które występują co około 40 minut, o ile usługa jest uruchomiona. Za pomocą sekcji **Szczegóły synchronizacji** można monitorować postęp i obserwować łącza do dzienników działań inicjowania obsługi administracyjnej, które opisują wszystkie akcje wykonywane przez usługę inicjowania obsługi administracyjnej w aplikacji Salesforce.

Aby uzyskać więcej informacji na temat sposobu zapoznania się z dziennikami inicjowania obsługi administracyjnej usługi Azure AD, zobacz [Raportowanie automatycznego inicjowania obsługi administracyjnej konta użytkownika.](../app-provisioning/check-status-user-account-provisioning.md)

## <a name="common-issues"></a>Typowe problemy
* W przypadku problemów z autoryzowaniem dostępu do Salesforce upewnij się, że:
    * Używane poświadczenia mają dostęp administratora do Salesforce.
    * Wersja używanej usługi Salesforce obsługuje program Web Access (np.
    * Dostęp do interfejsu API sieci Web jest włączony dla użytkownika.
* Usługa inicjowania obsługi administracyjnej usługi Azure AD obsługuje język inicjowania obsługi administracyjnej, ustawienia regionalne i timeZone dla użytkownika. Te atrybuty znajdują się w domyślnych mapowaniach atrybutów, ale nie mają domyślnego atrybutu źródłowego. Upewnij się, że wybierzesz domyślny atrybut źródłowy i że atrybut źródłowy jest w formacie oczekiwanym przez SalesForce. Na przykład localeSidKey for english(Stany Zjednoczone) jest en_US. Przejrzyj wskazówki podane [w tym miejscu,](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5) aby określić odpowiedni format localeSidKey. Formaty languageLocaleKey można znaleźć [tutaj](https://help.salesforce.com/articleView?id=faq_getstart_what_languages_does.htm&type=5). Oprócz zapewnienia, że format jest poprawny, może być konieczne upewnienie się, że język jest włączony dla użytkowników, jak opisano [tutaj](https://help.salesforce.com/articleView?id=setting_your_language.htm&type=5). 
* **SalesforceLicenseLimitExceeded:** Nie można utworzyć użytkownika w aplikacji docelowej, ponieważ nie ma dostępnych licencji dla tego użytkownika. Zaopatrzyć się w dodatkowe licencje dla aplikacji docelowej lub przejrzeć przypisania użytkowników i konfiguracji mapowania atrybutów, aby upewnić się, że poprawni użytkownicy są przypisane z poprawnymi atrybutami.
* **SalesforceDuplicateUserName:** Nie można aprowizować użytkownika, ponieważ ma Salesforce.com "Nazwa użytkownika", który jest duplikowany w innej dzierżawie Salesforce.com.W Salesforce.com wartości dla atrybutu "Nazwa użytkownika" muszą być unikatowe dla wszystkich dzierżaw Salesforce.com.Domyślnie userPrincipalName użytkownika w usłudze Azure Active Directory staje się ich "Nazwa użytkownika" w Salesforce.com.Dostępne są dwie opcje.Jedną z opcji jest znalezienie i zmiana nazwy użytkownika z duplikatem "Nazwa użytkownika" w drugiej Salesforce.com dzierżawy, jeśli administrujesz również tą inną dzierżawą.Inną opcją jest usunięcie dostępu z użytkownika usługi Azure Active Directory do dzierżawy Salesforce.com, z którą katalog jest zintegrowany. Ponowimy próbę wykonania tej operacji przy następnej próbie synchronizacji. 
* **SalesforceRequiredFieldMissing:** Salesforce wymaga, aby niektóre atrybuty były obecne u użytkownika, aby pomyślnie utworzyć lub zaktualizować użytkownika. Ten użytkownik brakuje jednego z wymaganych atrybutów. Upewnij się, że atrybuty, takie jak poczta e-mail i alias, są wypełniane przez wszystkich użytkowników, których chcesz aprowizować w Salesforce. Można wywężona z zakresu użytkowników, którzy nie mają tych atrybutów przy użyciu [filtrów zakresu opartych na atrybutach.](https://docs.microsoft.com/azure/active-directory/manage-apps/define-conditional-rules-for-provisioning-user-accounts) 
* Domyślne mapowanie atrybutów do inicjowania obsługi administracyjnej salesforce zawiera wyrażenie SingleAppRoleAssignments do mapowania appRoleAssignments w usłudze Azure AD do ProfileName w Salesforce. Upewnij się, że użytkownicy nie mają wiele przypisań ról aplikacji w usłudze Azure AD, ponieważ mapowanie atrybutów obsługuje tylko inicjowanie obsługi administracyjnej jednej roli. 
* Salesforce wymaga, aby aktualizacje poczty e-mail były zatwierdzane ręcznie przed zmianą. W rezultacie może zostać wyświetlonych wiele wpisów w dziennikach inicjowania obsługi administracyjnej, aby zaktualizować pocztę e-mail użytkownika (dopóki zmiana wiadomości e-mail nie zostanie zatwierdzona).


## <a name="additional-resources"></a>Zasoby dodatkowe

* [Zarządzanie inicjowanie obsługi administracyjnej kont użytkowników dla aplikacji dla przedsiębiorstw](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie logowania jednokrotnego](https://docs.microsoft.com/azure/active-directory/active-directory-saas-salesforce-tutorial)
