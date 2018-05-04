---
title: Konfigurowanie oświadczeń roli wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skonfigurować oświadczeń roli wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory
services: active-directory
documentationcenter: ''
author: jeevansd
manager: mtillman
editor: ''
ms.assetid: eb2b3741-3cde-45c8-b639-a636f3df3b74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5520781c9f687e5e32ad1d8c38922a456bb5e3ce
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2018
---
# <a name="configuring-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Konfigurowanie roli oświadczeń wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory

Ta funkcja umożliwia dostosowywanie typu oświadczenia "role" oświadczenia w tokenie odpowiedzi otrzymanych od autoryzowanie aplikacji za pomocą usługi Azure AD.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcję usługi Azure AD z konfiguracją katalogu
- Logowanie jednokrotne włączone subskrypcji
- Należy skonfigurować logowanie Jednokrotne z aplikacją

## <a name="when-to-use-this-feature"></a>Kiedy należy używać tej funkcji

Jeśli aplikacja oczekuje role niestandardowe, należy przesłać odpowiedzi SAML, należy użyć tej funkcji. Ta funkcja służy do tworzenia ról dowolną liczbę potrzebnych do przekazania do aplikacji, z usługi Azure AD.

## <a name="steps-to-use-this-feature"></a>Kroki, aby użyć tej funkcji

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz nazwę aplikacji, wybierz aplikację z panelu wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja w listy wyników](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po dodaniu aplikacji, przejdź do **właściwości** strony i skopiuj **identyfikator obiektu:**

    ![Strony właściwości](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń globalnych Admin/współadministratora dla dzierżawy.

    b. Zmiana wersji na **beta** i pobrać listy nazwy główne usług z dzierżawy przy użyciu następujących zapytań:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Jeśli używasz wielu katalogów, następnie należy wykonać tego wzorca `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    c. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Wyszukaj **obiektu o identyfikatorze**, które zostały skopiowane ze strony właściwości i skorzystaj z następujących zapytania get do odpowiednich główną usługi.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    d. Wyodrębnij właściwości appRoles z obiekt główny usługi.

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    e. Należy wygenerować nowe role w aplikacji teraz. Można pobrać roli usługi Azure AD Generator z [tutaj](https://app.box.com/s/jw6m9p9ehmf4ut5jx8xhcw87cu09ml3y).

    f. Otwórz Generator programu Azure AD i wykonaj następujące kroki —

    ![Generator usługi Azure AD](./media/active-directory-enterprise-app-role-management/azure_ad_role_generator.png)
    
    Wprowadź **nazwy roli**, **opis roli**, i **wartość roli**. Kliknij przycisk **Dodaj** Dodaj rolę
    
    Po dodaniu wszystkich wymaganych ról, kliknij przycisk **Generowanie**
    
    Skopiuj zawartość, klikając **skopiować zawartość**

    > [!NOTE] 
    > Upewnij się, że masz **msiam_access** roli użytkownika i identyfikator jest zgodne z wygenerowanym roli.

    g. Powrót do Eksploratora programu Graph. Zmień metodę z **UZYSKAĆ** do **poprawka**. Poprawka obiekt główny usługi, aby mieć pożądane appRoles przez modyfikowanie właściwości appRoles przy użyciu wartości skopiowanych. Kliknij przycisk **kwerenda**.

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-patch.png)

    > [!NOTE]
    > Poniżej przedstawiono przykład appRoles obiektu. 
    > 
    >   ```
    > {
    > "appRoles": [
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "msiam_access",
    >   "displayName": "msiam_access",
    >   "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
    >   "isEnabled": true,
    >   "origin": "Application",
    >   "value": null
    > },
    > {
    >   "allowedMemberTypes": [
    >   "User"
    >   ],
    >   "description": "teacher",
    >   "displayName": "teacher",
    >   "id": "6478ffd2-5dbd-4584-b2ce-137390b09b60",
    >   "isEnabled": ,
    >   "origin": "ServicePrincipal",
    >   "value": "teacher"
    > }
    > ] 
    > }
    >
    >   ```

7. Po nazwy głównej usługi jest poprawiono więcej ról, firma Microsoft można przypisać użytkowników do odpowiednich ról. Można to zrobić, przechodząc do portalu i przechodząc do odpowiednich aplikacji. Następnie, klikając **użytkowników i grup** kartę w górnej części. Ten proces będzie zawierała listę wszystkich użytkowników lub grup.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-enterprise-app-role-management/userrole.png)

    a. Aby przypisać rolę do dowolnego użytkownika, po prostu wybierz określonego użytkownika lub grupę i kliknij na **przypisać** przycisk w dolnej części strony.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-enterprise-app-role-management/userandgroups.png)

    b. Kliknięcie, które powoduje wyświetlenie okienka wyskakującego, wybierz rolę w różnych ról, które są zdefiniowane dla odpowiedniej nazwy głównej usługi.

    c. Wybierz wymagane role i kliknij przycisk przesyłania.

8. Po przypisaniu ról dla użytkowników, należy zaktualizować **atrybuty** tabeli, aby określić niestandardowe mapowanie **roli** oświadczeń.

9. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|    
    | Nazwa roli      | user.assignedrole |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Konfigurowanie atrybutów rejestracji jednokrotnej](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

10. Aby przetestować aplikację w rozszerzeniu IDP zainicjować funkcji logowania jednokrotnego w dziennika do panelu dostępu (https://myapps.microsoft.com) i kliknięcie kafelka aplikacji. W tokenie SAML powinny być widoczne wszystkie przypisane role dla użytkownika o nazwie oświadczeń nadany.

## <a name="update-existing-role"></a>Aktualizuj istniejącą rolę

1. Aby uaktualnić istniejącą rolę, wykonaj następujące kroki —

    a. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    b. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń globalnych Admin/współadministratora dla dzierżawy.
    
    c. Zmiana wersji na **beta** i pobrać listy nazwy główne usług z dzierżawy przy użyciu następujących zapytań:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
        
    Jeśli używasz wielu katalogów, następnie należy wykonać tego wzorca `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Wyszukaj **obiektu o identyfikatorze**, które zostały skopiowane ze strony właściwości i skorzystaj z następujących zapytania get do odpowiednich główną usługi.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Wyodrębnij właściwości appRoles z obiekt główny usługi.
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)
    
    f. Aby uaktualnić istniejącą rolę, wykonaj następujące czynności:

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Zmień metodę z **UZYSKAĆ** do **poprawka**.

    * Skopiuj istniejących ról z aplikacji i wklej je do **treść żądania**.
    
    * Zaktualizuj wartość roli zastępując **opis roli**, **wartość roli**, i **displayname roli** zgodnie z wymaganiami organizacji.
    
    * Po zaktualizowaniu wszystkich wymaganych ról, kliknij przycisk **Uruchom kwerendę**.
        
## <a name="delete-existing-role"></a>Usuń istniejącą rolę

1. Aby usunąć istniejącą rolę, wykonaj następujące kroki —

    a. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    b. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń globalnych Admin/współadministratora dla dzierżawy.

    c. Zmiana wersji na **beta** i pobrać listy nazwy główne usług z dzierżawy przy użyciu następujących zapytań:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Jeśli używasz wielu katalogów, następnie należy wykonać tego wzorca `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer1-updated.png)
    
    d. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Wyszukaj **obiektu o identyfikatorze**, które zostały skopiowane ze strony właściwości i skorzystaj z następujących zapytania get do odpowiednich główną usługi.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.
    
    e. Wyodrębnij właściwości appRoles z obiekt główny usługi.
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-approles.png)

    f. Aby usunąć istniejącą rolę, wykonaj następujące czynności:

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchdelete.png)

    Zmień metodę z **UZYSKAĆ** do **poprawka**.

    Skopiuj istniejących ról z aplikacji i wklej je w **treść żądania**.
    
    Ustaw **IsEnabled** do wartości **false** dla roli, które chcesz usunąć

    Kliknij przycisk **kwerenda**.
    
    > [!NOTE] 
    > Upewnij się, że masz **msiam_access** roli użytkownika i identyfikator jest zgodne z wygenerowanym roli.
    
    g. Po wykonaniu powyższych procesu, Zachowaj metodę jako **poprawka** i Wklej zawartość w roli remianing **treść żądania** i kliknij przycisk **Uruchom kwerendę**.
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchfinal.png)

    h. Po uruchomieniu kwerendy roli zostaną usunięte.
    
    > [!NOTE]
    > Rola musi być wyłączona najpierw można było usunąć. 

## <a name="next-steps"></a>Następne kroki

Zobacz [dokumentacji aplikacji ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) dla dodatkowych kroków.

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png
