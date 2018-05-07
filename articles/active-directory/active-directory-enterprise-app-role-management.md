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
ms.openlocfilehash: 94b451f66d286426f6dd2cc556e8c6785c3f743e
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
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

5. Po dodaniu aplikacji, przejdź do **właściwości** strony i skopiuj **obiektu o identyfikatorze**.

    ![Strony właściwości](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.PNG)

6. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń globalnych Admin/współadministratora dla dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Polecenie **uprawnień do modyfikowania** można uzyskać wymaganych uprawnień. 

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (Jeśli te nie mają już) i kliknij polecenie "Modyfikuj uprawnienia" 

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Poprosi zalogować się ponownie i zaakceptować zgody. Po zaakceptowaniu zgody, użytkownik jest zalogowany do systemu ponownie.

    e. Zmiana wersji na **beta** i pobrać listy nazwy główne usług z dzierżawy przy użyciu następujących zapytań:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Jeśli używasz wielu katalogów, następnie należy wykonać tego wzorca `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Wyszukaj **obiektu o identyfikatorze**, które zostały skopiowane ze strony właściwości i skorzystaj z następujących zapytania get do odpowiednich główną usługi.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Wyodrębnij właściwości appRoles z obiekt główny usługi. 

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

    > [!Note]
    > Jeśli używasz niestandardowej aplikacji (z systemem innym niż gallery), zobaczysz dwa domyślne role - msiam_access i użytkownika. W przypadku aplikacji galerii msiam_access jest jedyną rolą domyślne. Nie trzeba wprowadzać żadnych zmian w domyślnych ról.

    h. Teraz należy wygenerować nowe role w aplikacji. 

    i. Poniżej JSON jest przykładem appRoles obiektu. Utwórz obiekt podobny do dodawania ról, do których chcesz dla aplikacji. 

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
    > Można dodawać tylko nowe role po **msiam_access** dla operacji poprawki. Ponadto można dodać dowolną liczbę ról można dowolnie na potrzeby organizacji. Usługi Azure AD będzie wysyłać **wartość** tych ról jako wartość oświadczenia w odpowiedzi SAML.
    
    j. Przejdź wstecz do Eksploratora z wykresu i zmień metodę z **UZYSKAĆ** do **poprawka**. Poprawka obiekt główny usługi do ma pożądane ról przez modyfikowanie właściwości appRoles podobny do przedstawionego powyżej w przykładzie. Kliknij przycisk **Uruchom kwerendę** wykonanie operacji poprawki. Komunikat z potwierdzeniem potwierdza tworzenia roli.

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po nazwy głównej usługi jest poprawiono więcej ról, można przypisać użytkowników do odpowiednich ról. Można to zrobić, przechodząc do portalu i przechodząc do odpowiednich aplikacji. Polecenie **użytkowników i grup** kartę w górnej części. Ta lista zawiera wszystkich użytkowników i grup, które są już przypisane do aplikacji. Można dodawać nowych użytkowników na nową rolę i można również wybrać istniejącego użytkownika i kliknij przycisk **Edytuj** zmiana roli.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

     Aby przypisać rolę do dowolnego użytkownika, wybierz nową rolę, a następnie kliknij przycisk na **przypisać** przycisk w dolnej części strony.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Należy pamiętać, trzeba odświeżyć sesję w portalu Azure, aby zobaczyć nowe role.

8. Po przypisaniu ról dla użytkowników, należy zaktualizować **atrybuty** tabeli, aby określić niestandardowe mapowanie **roli** oświadczeń.

9. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące czynności:
    
    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|    
    | Nazwa roli      | user.assignedrole |

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

    ![Konfigurowanie atrybutów rejestracji jednokrotnej](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybutu, zgodnie z potrzebami. W tym przykładzie użyliśmy **nazwy roli** jako nazwy oświadczenia.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** puste.
    
    e. Kliknij przycisk **OK**.

10. Aby przetestować aplikację w rozszerzeniu IDP zainicjować funkcji logowania jednokrotnego w dziennika do panelu dostępu (https://myapps.microsoft.com) i kliknięcie kafelka aplikacji. W tokenie SAML powinny być widoczne wszystkie przypisane role dla użytkownika o nazwie oświadczeń nadany.

## <a name="update-existing-role"></a>Aktualizuj istniejącą rolę

Aby uaktualnić istniejącą rolę, wykonaj następujące kroki —

1. Otwórz [Eksploratora usługi Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń globalnych Admin/współadministratora dla dzierżawy.
    
3. Zmiana wersji na **beta** i pobrać listy nazwy główne usług z dzierżawy przy użyciu następujących zapytań:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Jeśli używasz wielu katalogów, następnie należy wykonać tego wzorca `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Wyszukaj **obiektu o identyfikatorze**, które zostały skopiowane ze strony właściwości i skorzystaj z następujących zapytania get do odpowiednich główną usługi.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Wyodrębnij właściwości appRoles z obiekt główny usługi.
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Aby uaktualnić istniejącą rolę, wykonaj następujące czynności:

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    * Zmień metodę z **UZYSKAĆ** do **poprawka**.

    * Istniejące role skopiuj i wklej je do **treść żądania**.

    * Zaktualizuj wartość roli, aktualizując **opis roli**, **wartość roli** lub **displayname roli** zgodnie z potrzebami.

    * Po zaktualizowaniu wszystkich wymaganych ról, kliknij przycisk **Uruchom kwerendę**.
        
## <a name="delete-existing-role"></a>Usuń istniejącą rolę

Aby usunąć istniejącą rolę, wykonaj następujące kroki:

1. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

2. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń globalnych Admin/współadministratora dla dzierżawy.

3. Zmiana wersji na **beta** i pobrać listy nazwy główne usług z dzierżawy przy użyciu następujących zapytań:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Jeśli używasz wielu katalogów, następnie należy wykonać tego wzorca `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Wyszukaj **obiektu o identyfikatorze**, które zostały skopiowane ze strony właściwości i skorzystaj z następujących zapytania get do odpowiednich główną usługi.
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Wyodrębnij właściwości appRoles z obiekt główny usługi.
    
    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Aby usunąć istniejącą rolę, wykonaj następujące czynności:

    ![Okno dialogowe Eksplorator wykresu](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    * Zmień metodę z **UZYSKAĆ** do **poprawka**.

    * Skopiuj istniejących ról z aplikacji i wklej je w **treść żądania**.
        
    * Ustaw **IsEnabled** do wartości **false** dla roli, które chcesz usunąć

    * Kliknij przycisk **kwerenda**.
    
    > [!NOTE] 
    > Upewnij się, że masz **msiam_access** roli użytkownika i identyfikator jest zgodne z wygenerowanym roli.
    
7. Po wyłączeniu roli Usuń ten blok roli z sekcji appRoles, należy metodę jako **poprawka** i kliknij przycisk **Uruchom kwerendę**.
    
8. Po uruchomieniu kwerendy roli zostaną usunięte.
    
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
