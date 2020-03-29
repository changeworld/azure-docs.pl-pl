---
title: Konfigurowanie oświadczenia o roli dla aplikacji usługi Azure AD w przedsiębiorstwie | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować oświadczenie o roli wystawione w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory
services: active-directory
author: jeevansd
manager: CelesteDG
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: jeedes
ms.openlocfilehash: a70abd1cddb866037926bbbc881682d50599366b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76699261"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Jak: Konfigurowanie oświadczenia roli wystawionego w tokenie SAML dla aplikacji korporacyjnych

Za pomocą usługi Azure Active Directory (Azure AD), można dostosować typ oświadczenia dla oświadczenia roli w tokenie odpowiedzi, który otrzymujesz po autoryzacji aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja usługi Azure AD z konfiguracją katalogu.
- Subskrypcja z włączoną funkcją logowania jednokrotnego.A subscription that has single sign-on (SSO) enabled. Należy skonfigurować sytą aplikację z aplikacją.

## <a name="when-to-use-this-feature"></a>Kiedy korzystać z tej funkcji

Jeśli aplikacja oczekuje, że role niestandardowe mają być przekazywane w odpowiedzi SAML, należy użyć tej funkcji. Można utworzyć dowolną liczbę ról, ile trzeba przekazać z usługi Azure AD do aplikacji.

## <a name="create-roles-for-an-application"></a>Tworzenie ról dla aplikacji

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz ikonę **usługi Azure Active Directory.**

    ![Ikona usługi Azure Active Directory][1]

2. Wybierz **aplikacje enterprise**. Następnie wybierz **opcję Wszystkie aplikacje**.

    ![Okienko aplikacji dla przedsiębiorstw][2]

3. Aby dodać nową aplikację, wybierz przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk "Nowa aplikacja"][3]

4. W polu wyszukiwania wpisz nazwę aplikacji, a następnie wybierz aplikację z panelu wyników. Wybierz przycisk **Dodaj,** aby dodać aplikację.

    ![Zastosowanie na liście wyników](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po dodaniu aplikacji przejdź do strony **Właściwości** i skopiuj identyfikator obiektu.

    ![Strona Właściwości](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otwórz [Eksploratora wykresu firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer) w innym oknie i podejmij następujące kroki:

    a. Zaloguj się do witryny Eksploratora wykresu przy użyciu poświadczeń administratora globalnego lub współprzewodnicze dla dzierżawy.

    b. Potrzebne są wystarczające uprawnienia do tworzenia ról. Wybierz **uprawnienia do modyfikowania,** aby uzyskać uprawnienia.

      ![Przycisk "Modyfikować uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    d. Wybierz następujące uprawnienia z listy (jeśli jeszcze ich nie masz) i wybierz pozycję **Modyfikuj uprawnienia**.

      ![Lista uprawnień i przycisk "Modyfikuj uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Administrator aplikacji w chmurze i administrator aplikacji nie będzie działać w tym scenariuszu, ponieważ potrzebujemy uprawnień administratora globalnego do odczytu i zapisu katalogu.

    d. Zaakceptuj zgodę. Ponownie zalogujesz się do systemu.

    e. Zmień wersję na **wersję beta**i pobierz listę podmiotów usługi z dzierżawy przy użyciu następującej kwerendy:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Jeśli używasz wielu katalogów, postępuj zgodnie z tym wzorcem:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Okno dialogowe Eksplorator wykresów z kwerendą do pobierania podmiotów świadczących usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Jesteśmy już w trakcie uaktualniania interfejsów API, aby klienci mogli zobaczyć pewne zakłócenia w usłudze.

    f. Z listy pobranych podmiotów usługi, pobierz ten, który należy zmodyfikować. Można również użyć Ctrl +F do wyszukiwania aplikacji ze wszystkich wymienionych podmiotów usługi. Wyszukaj identyfikator obiektu skopiowany ze strony Właściwości i użyj następującej **kwerendy,** aby uzyskać pomoc podmiotu usługi:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Zapytanie o uzyskanie jednostki usługi, którą należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Wyodrębnij **właściwość appRoles** z obiektu jednostki usługi.

      ![Szczegóły właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Jeśli używasz aplikacji niestandardowej (nie aplikacji portalu Azure Marketplace), zobaczysz dwie domyślne role: użytkownik i msiam_access. W przypadku aplikacji Marketplace msiam_access jest jedyną domyślną rolą. Nie trzeba wprowadzać żadnych zmian w rolach domyślnych.

    h. Generowanie nowych ról dla aplikacji.

      Poniższy JSON jest przykładem **appRoles** obiektu. Utwórz podobny obiekt, aby dodać role, które chcesz dla aplikacji.

      ```
      {
         "appRoles": [
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "msiam_access",
              "displayName": "msiam_access",
              "id": "b9632174-c057-4f7e-951b-be3adc52bfe6",
              "isEnabled": true,
              "origin": "Application",
              "value": null
          },
          {
              "allowedMemberTypes": [
                  "User"
              ],
              "description": "Administrators Only",
              "displayName": "Admin",
              "id": "4f8f8640-f081-492d-97a0-caf24e9bc134",
              "isEnabled": true,
              "origin": "ServicePrincipal",
              "value": "Administrator"
          }
      ]
      }
      ```

      > [!Note]
      > Nowe role można dodawać tylko po msiam_access dla operacji poprawki. Ponadto można dodać dowolną liczbę ról, ile potrzebuje organizacja. Usługa Azure AD wyśle wartość tych ról jako wartość oświadczenia w odpowiedzi SAML. Aby wygenerować wartości identyfikatora GUID dla identyfikatora nowych ról, użyj takich narzędzi internetowych [this](https://www.guidgenerator.com/)

    i. Wróć do Graph Explorer i zmień metodę z **GET** na **PATCH**. Popraw obiekt jednostki usługi, aby mieć żądane role, aktualizując **właściwość appRoles,** taką jak pokazano w poprzednim przykładzie. Wybierz **opcję Uruchom kwerendę,** aby wykonać operację poprawki. Komunikat o sukcesie potwierdza utworzenie roli.

      ![Operacja aktualizacji z komunikatem o sukcesie](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po załataniu jednostki usługi większą liczką więcej ról, można przypisać użytkowników do odpowiednich ról. Użytkownicy mogą przypisać, przechodząc do portalu i przeglądając aplikację. Wybierz kartę **Użytkownicy i grupy.** Ta karta zawiera listę wszystkich użytkowników i grup, które są już przypisane do aplikacji. Do nowych ról można dodawać nowych użytkowników. Można również wybrać istniejącego użytkownika i wybrać **edytuj,** aby zmienić rolę.

    ![Karta "Użytkownicy i grupy"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Aby przypisać rolę do dowolnego użytkownika, wybierz nową rolę i wybierz przycisk **Przypisz** u dołu strony.

    ![Okienko "Edytuj przydział" i okienko "Wybierz rolę"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Musisz odświeżyć sesję w witrynie Azure portal, aby wyświetlić nowe role.

8. Zaktualizuj tabelę **Atrybuty,** aby zdefiniować niestandardowe mapowanie oświadczenia roli.

9. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|
    | Nazwa roli  | user.assignedroles |

    >[!NOTE]
    >Jeśli wartość oświadczenia roli jest null, a następnie usługi Azure AD nie wyśle tę wartość w tokenie i jest to domyślnie zgodnie z projektem.

    a. Kliknij ikonę **Edytuj,** aby otworzyć okno dialogowe **Atrybuty & Oświadczenia użytkownika.**

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. W oknie dialogowym **Zarządzanie roszczeniami użytkowników** dodaj atrybut tokenu SAML, klikając pozycję **Dodaj nowe oświadczenie**.

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Okienko "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    d. W polu **Nazwa** wpisz nazwę atrybutu w razie potrzeby. W tym przykładzie użyto **nazwy roli** jako nazwy oświadczenia.

    d. Pozostaw pole **Obszaru nazw** puste.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Wybierz **pozycję Zapisz**.

10. Aby przetestować aplikację w logowaniu jednokrotnym zainicjowanym przez dostawcę tożsamości, zaloguj się do [panelu dostępu](https://myapps.microsoft.com) i wybierz kafelek aplikacji. W tokenie SAML powinny być widoczne wszystkie przypisane role dla użytkownika o podanej nazwie oświadczenia.

## <a name="update-an-existing-role"></a>Aktualizowanie istniejącej roli

Aby zaktualizować istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [Eksploratora wykresów firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer).

2. Zaloguj się do witryny Eksploratora wykresu przy użyciu poświadczeń administratora globalnego lub współprzewodnicze dla dzierżawy.

3. Zmień wersję na **wersję beta**i pobierz listę podmiotów usługi z dzierżawy przy użyciu następującej kwerendy:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, postępuj zgodnie z tym wzorcem:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksplorator wykresów z kwerendą do pobierania podmiotów świadczących usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Z listy pobranych podmiotów usługi, pobierz ten, który należy zmodyfikować. Można również użyć Ctrl +F do wyszukiwania aplikacji ze wszystkich wymienionych podmiotów usługi. Wyszukaj identyfikator obiektu skopiowany ze strony Właściwości i użyj następującej **kwerendy,** aby uzyskać pomoc podmiotu usługi:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie o uzyskanie jednostki usługi, którą należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Wyodrębnij **właściwość appRoles** z obiektu jednostki usługi.

    ![Szczegóły właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Aby zaktualizować istniejącą rolę, należy wykonać następujące kroki.

    ![Treść żądania dla "PATCH", z wyróżnioną "opis" i "nazwa wyświetlana"](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Zmień metodę z **GET** na **PATCH**.

    b. Skopiuj istniejące role i wklej je w obszarze **Treść żądania**.

    d. Zaktualizuj wartość roli, aktualizując w razie potrzeby opis roli, wartość roli lub nazwę wyświetlaną roli.

    d. Po zaktualizowaniu wszystkich wymaganych ról wybierz pozycję **Uruchom kwerendę**.

## <a name="delete-an-existing-role"></a>Usuwanie istniejącej roli

Aby usunąć istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [Eksploratora wykresów firmy Microsoft](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

2. Zaloguj się do witryny Eksploratora wykresu przy użyciu poświadczeń administratora globalnego lub współprzewodnicze dla dzierżawy.

3. Zmień wersję na **wersję beta**i pobierz listę podmiotów usługi z dzierżawy przy użyciu następującej kwerendy:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, postępuj zgodnie z tym wzorcem:`https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksplorator wykresów z kwerendą zawierającą kwerendę zawierającą listę podmiotów usług](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Z listy pobranych podmiotów usługi, pobierz ten, który należy zmodyfikować. Można również użyć Ctrl +F do wyszukiwania aplikacji ze wszystkich wymienionych podmiotów usługi. Wyszukaj identyfikator obiektu skopiowany ze strony Właściwości i użyj następującej **kwerendy,** aby uzyskać pomoc podmiotu usługi:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie o uzyskanie jednostki usługi, którą należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Wyodrębnij **właściwość appRoles** z obiektu jednostki usługi.

    ![Szczegóły appRoles właściwości z jednostki usługi obiektu](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Aby usunąć istniejącą rolę, należy wykonać następujące kroki.

    ![Treść żądania dla "PATCH", z IsEnabled ustawiona na false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Zmień metodę z **GET** na **PATCH**.

    b. Skopiuj istniejące role z aplikacji i wklej je w obszarze **Treść żądania**.

    d. Ustaw wartość **IsEnabled** na **false** dla roli, którą chcesz usunąć.

    d. Wybierz pozycję **Uruchom zapytanie**.

    > [!NOTE]
    > Upewnij się, że masz rolę msiam_access, a identyfikator jest pasując w wygenerowanej roli.

7. Po wyłączenie roli usuń ten blok roli z sekcji **AppRoles.** Zachowaj metodę jako **PATCH**i wybierz pozycję **Uruchom kwerendę**.

8. Po uruchomieniu kwerendy rola jest usuwana.

    > [!NOTE]
    > Rola musi zostać wyłączona, zanim będzie można ją usunąć.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe kroki, zobacz [dokumentację aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
