---
title: Konfigurowanie oświadczenia roli wydanych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure AD | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować oświadczenie roli wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: CelesteDG
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/22/2019
ms.author: jeedes
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15165bce70a9bc2fbf3eb840ca8bce4fd5073280
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544637"
---
# <a name="how-to-configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications"></a>Instrukcje: Konfigurowanie oświadczenia roli wystawionych w tokenie SAML dla aplikacji dla przedsiębiorstw

Za pomocą usługi Azure Active Directory (Azure AD), można dostosować typ oświadczenia roli oświadczenia w tokenie odpowiedzi otrzymasz po autoryzujesz aplikację.

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcję usługi Azure AD za pomocą katalogu instalacji.
- Subskrypcja, która ma logowania jednokrotnego (SSO) włączone. Należy skonfigurować logowanie Jednokrotne za pomocą aplikacji.

## <a name="when-to-use-this-feature"></a>Kiedy należy używać tej funkcji

Jeśli aplikacja oczekuje ról niestandardowych, które zostaną przekazane do odpowiedzi SAML, musisz korzystać z tej funkcji. Można utworzyć dowolną liczbę ról potrzebnych do przekazania do swojej aplikacji, powrót po awarii z usługi Azure AD.

## <a name="create-roles-for-an-application"></a>Tworzenie ról aplikacji

1. W [witryny Azure portal](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony.

    ![Ikona usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**. Następnie wybierz pozycję **wszystkie aplikacje**.

    ![Okienko aplikacje przedsiębiorstwa][2]

3. Aby dodać nową aplikację, wybierz **nową aplikację** przycisk u góry okna dialogowego.

    !["Nowa aplikacja" przycisk][3]

4. W polu wyszukiwania wpisz nazwę aplikacji, a następnie wybierz swoją aplikację z poziomu panelu wyników. Wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja na liście wyników](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po dodaniu aplikacji przejdź do **właściwości** strony, a następnie skopiować identyfikator obiektu.

    ![Strona właściwości](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otwórz [programu Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) w innym oknie i wykonaj następujące czynności:

    a. Zaloguj się do witryny Graph Explorer przy użyciu poświadczeń administratora lub coadmin globalnych dla Twojej dzierżawy.

    b. Należy wystarczających uprawnień do utworzenia tych ról. Wybierz **modyfikowania uprawnień** uzyskanie uprawnień.

      ![Przycisk "Modyfikuj uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (jeśli jeszcze nie masz tych) i wybierz pozycję **zmodyfikować uprawnienia**.

      ![Lista uprawnień i przycisk "Zmodyfikuj uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    > [!Note]
    > Rola Administrator aplikacji w chmurze i Administrator aplikacji nie będzie działać w tym scenariuszu, ponieważ firma Microsoft musi mieć uprawnienia administratora globalnego do katalogu odczytu i zapisu.

    d. Zaakceptuj zgody. Zalogowano Cię do systemu ponownie.

    e. Zmień wersję do **beta**i pobrać listę jednostek usługi w dzierżawie za pomocą następującej kwerendy:

     `https://graph.microsoft.com/beta/servicePrincipals`

      Jeśli używasz wielu katalogów, postępuj zgodnie z tego wzorca: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

      ![Okno dialogowe Eksploratora programu Graph, z zapytania do pobierania nazwy główne usług](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

      > [!Note]
      > Już jesteśmy w trakcie uaktualniania interfejsów API, dzięki czemu klienci mogą widzieć niektóre przerwy w działaniu usługi.

    f. Z listy jednostek usługi pobrano Pobierz jeden, który chcesz zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji ze wszystkich wymienionych usług podmiotów zabezpieczeń. Wyszukaj identyfikator obiektu, który został skopiowany z **właściwości** strony i użyj następującego zapytania, aby przejść do nazwy głównej usługi:

      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Zapytanie w celu uzyskania nazwy głównej usługi, który chcesz zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Wyodrębnij **appRoles** właściwości z obiektu jednostki usługi.

      ![Szczegółowe informacje o właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Jeśli używasz niestandardowej aplikacji (a nie aplikacji portalu Azure Marketplace), zobaczysz dwie domyślne role: użytkowników i msiam_access. W przypadku aplikacja portalu Marketplace msiam_access jest jedyną rolą domyślną. Nie trzeba wprowadzać zmian w domyślne role.

    h. Generuj nowe role w aplikacji.

      Następujący kod JSON jest przykładem **appRoles** obiektu. Utwórz obiekt podobne, aby dodać role, które mają dla swojej aplikacji.

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
      > Nowe role można dodać tylko po msiam_access na potrzeby operacji patch. Ponadto można dodać jako wiele ról jako potrzeb organizacji. Usługa Azure AD będzie wysyłać wartości z tych ról jako wartość oświadczenia w odpowiedzi SAML. Aby wygenerować identyfikator GUID wartości dla Identyfikatora nowych ról należy użyć narzędzi sieci web, takich jak [to](https://www.guidgenerator.com/)

    i. Wróć do Eksploratora programu Graph i zmienić metodę z **UZYSKAĆ** do **PATCH**. Stosowanie poprawek do obiektu jednostki usługi do ma odpowiednią ról, aktualizując **appRoles** właściwości, tak jak pokazano w powyższym przykładzie. Wybierz **Uruchom kwerendę** do wykonywania operacji patch. Komunikat o powodzeniu potwierdza tworzenia roli.

      ![Operacja poprawki z komunikatem o powodzeniu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po jednostki usługi są zainstalowane odpowiednie poprawki z większej liczby ról, można przypisać użytkowników do odpowiednich ról. Można przypisać użytkownikom, przechodząc do portalu i przechodzenie do aplikacji. Wybierz **użytkowników i grup** kartę. Ta karta zawiera listę wszystkich użytkowników i grup, którzy zostali przypisani do aplikacji. W przypadku nowych ról, można dodać nowych użytkowników. Można również wybrać istniejącego użytkownika i wybierz **Edytuj** zmiana roli.

    ![Karty "Użytkownicy i grupy"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Aby przypisać rolę dla każdego użytkownika, wybierz nową rolę, a następnie wybierz pozycję **przypisać** przycisk w dolnej części strony.

    ![Okienka "Edytować przypisania" i "Wybierz rolę"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Konieczne jest odświeżenie sesji w witrynie Azure portal, aby zobaczyć nowe role.

8. Aktualizacja **atrybuty** tabeli, aby zdefiniować niestandardowe Mapowanie oświadczeń roli.

9. W sekcji **Oświadczenia użytkownika** w oknie dialogowym **Atrybuty użytkownika** wykonaj następujące czynności, aby dodać atrybut tokenu SAML, jak pokazano w poniższej tabeli:

    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|
    | Nazwa roli  | user.assignedroles |

    >[!NOTE]
    >Jeśli wartość oświadczenia rola ma wartość null, następnie usługi Azure AD nie będzie wysyłać tę wartość w tokenie, a następnie jest ustawienie domyślne dla każdego projektu.

    a. Kliknij przycisk **Edytuj** ikonę, aby otworzyć **atrybutów użytkowników i oświadczeń** okna dialogowego.

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/editattribute.png)

    b. W **Zarządzanie oświadczenia użytkownika** okno dialogowe, Dodaj atrybut tokenu SAML, klikając **Dodaj nowe oświadczenie**.

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      ![Okienko "Dodawanie atrybutu"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    c. W **nazwa** wpisz nazwę atrybutu, zgodnie z potrzebami. W tym przykładzie użyto **nazwy roli** jako nazwa oświadczenia.

    d. Pozostaw **Namespace** puste pole.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Wybierz pozycję **Zapisz**.

10. Aby przetestować aplikację w jednokrotne logowanie inicjowane przez dostawcę tożsamości, zaloguj się do [panelu dostępu](https://myapps.microsoft.com) i wybrać kafelka aplikacji. W tokenie SAML powinny być widoczne wszystkie przypisane role dla użytkownika o nazwie oświadczenia, które wyrażono.

## <a name="update-an-existing-role"></a>Aktualizuj istniejącą rolę

Aby zaktualizować istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [funkcji Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer).

2. Zaloguj się do witryny Graph Explorer przy użyciu poświadczeń administratora lub coadmin globalnych dla Twojej dzierżawy.

3. Zmień wersję do **beta**i pobrać listę jednostek usługi w dzierżawie za pomocą następującej kwerendy:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, postępuj zgodnie z tego wzorca: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksploratora programu Graph, z zapytania do pobierania nazwy główne usług](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Z listy jednostek usługi pobrano Pobierz jeden, który chcesz zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji ze wszystkich wymienionych usług podmiotów zabezpieczeń. Wyszukaj identyfikator obiektu, który został skopiowany z **właściwości** strony i użyj następującego zapytania, aby przejść do nazwy głównej usługi:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie w celu uzyskania nazwy głównej usługi, który chcesz zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Wyodrębnij **appRoles** właściwości z obiektu jednostki usługi.

    ![Szczegółowe informacje o właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

6. Aby zaktualizować istniejącą rolę, wykonaj następujące kroki.

    ![Treść żądania dla "Poprawka", "description" i "displayname" wyróżniony](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)

    a. Zmień metodę z **UZYSKAĆ** do **PATCH**.

    b. Istniejące role skopiuj i wklej je w obszarze **treść żądania**.

    c. Zaktualizuj wartość roli, aktualizując opis roli, wartości rolę lub nazwę wyświetlaną roli, zgodnie z potrzebami.

    d. Po zaktualizowaniu wymaganych ról wybierz **Uruchom kwerendę**.

## <a name="delete-an-existing-role"></a>Usuń istniejącą rolę

Aby usunąć istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [programu Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

2. Zaloguj się do witryny Graph Explorer przy użyciu poświadczeń administratora lub coadmin globalnych dla Twojej dzierżawy.

3. Zmień wersję do **beta**i pobrać listę jednostek usługi w dzierżawie za pomocą następującej kwerendy:

    `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, postępuj zgodnie z tego wzorca: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksploratora programu Graph, z zapytania do pobierania listy jednostek usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)

4. Z listy jednostek usługi pobrano Pobierz jeden, który chcesz zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji ze wszystkich wymienionych usług podmiotów zabezpieczeń. Wyszukaj identyfikator obiektu, który został skopiowany z **właściwości** strony i użyj następującego zapytania, aby przejść do nazwy głównej usługi:

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie w celu uzyskania nazwy głównej usługi, który chcesz zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

5. Wyodrębnij **appRoles** właściwości z obiektu jednostki usługi.

    ![Szczegółowe informacje o właściwości appRoles z obiektu jednostki usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Aby usunąć istniejącą rolę, wykonaj następujące kroki.

    ![Treść żądania dla "Poprawka", przy użyciu IsEnabled ustawiony na wartość false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Zmień metodę z **UZYSKAĆ** do **PATCH**.

    b. Kopiowanie istniejących ról z aplikacji, a następnie wklej je w obszarze **treść żądania**.

    c. Ustaw **IsEnabled** wartość **false** dla roli, która ma zostać usunięty.

    d. Wybierz **uruchom zapytanie**.

    > [!NOTE]
    > Upewnij się, że rola msiam_access i identyfikator jest zgodny w wygenerowanym roli.

7. Po wyłączeniu roli należy usunąć tego bloku roli z **appRoles** sekcji. Zachowaj metodę jako **PATCH**i wybierz **Uruchom kwerendę**.

8. Po uruchomieniu zapytania roli jest usuwany.

    > [!NOTE]
    > Rolę musi być wyłączona, można było usunąć.

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać dodatkowe czynności, zobacz [dokumentacji aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
