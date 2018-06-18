---
title: Skonfiguruj oświadczenia roli wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory | Dokumentacja firmy Microsoft
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
ms.date: 05/30/2018
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 5aa716f91a3155e81ef8dc7c436b4a9a5811238b
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2018
ms.locfileid: "34723256"
---
# <a name="configure-the-role-claim-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Skonfiguruj oświadczenia roli wydanych w tokenie SAML dla aplikacji przedsiębiorstwa w usłudze Azure Active Directory

Za pomocą usługi Azure Active Directory (Azure AD), można dostosować typ oświadczenia roli oświadczenia w tokenie odpowiedzi czy po błędzie Autoryzuj aplikację.

## <a name="prerequisites"></a>Wymagania wstępne
- Subskrypcja usługi Azure AD z katalogu instalacji.
- Subskrypcję, która ma rejestracji jednokrotnej (SSO) włączone. Należy skonfigurować logowanie Jednokrotne z aplikacją.

## <a name="when-to-use-this-feature"></a>Kiedy należy używać tej funkcji

Jeśli aplikacja oczekuje role niestandardowe, które mają być przekazane do odpowiedzi SAML, należy użyć tej funkcji. Można utworzyć dowolną liczbę ról jak muszą być przekazywane z usługi Azure AD do aplikacji.

## <a name="create-roles-for-an-application"></a>Tworzenie ról dla aplikacji

1. W [portalu Azure](https://portal.azure.com), w okienku po lewej stronie wybierz **usługi Azure Active Directory** ikony. 

    ![Ikona usługi Azure Active Directory][1]

2. Wybierz **aplikacje dla przedsiębiorstw**. Następnie wybierz **wszystkie aplikacje**.

    ![Okienko aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, zaznacz **nowej aplikacji** przycisk w górnej części okna dialogowego.

    !["Nowej aplikacji" przycisku][3]

4. W polu wyszukiwania wpisz nazwę aplikacji, a następnie wybierz aplikację z poziomu panelu wyników. Wybierz **Dodaj** przycisk, aby dodać aplikację.

    ![Aplikacja w listy wyników](./media/active-directory-enterprise-app-role-management/tutorial_app_addfromgallery.png)

5. Po dodaniu aplikacji, przejdź do **właściwości** zaznacz i skopiuj identyfikatora obiektu.

    ![Strony właściwości](./media/active-directory-enterprise-app-role-management/tutorial_app_properties.png)

6. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie i wykonaj następujące czynności:

    a. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń administratora lub coadmin globalnych dla dzierżawy.

    b. Należy wystarczające uprawnienia do tworzenia ról. Wybierz **uprawnień do modyfikowania** do uzyskania uprawnień. 

      ![Przycisk "Modyfikuj uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (Jeśli te nie mają już) i wybierz **zmodyfikować uprawnienia**. 

      ![Lista uprawnień i przycisk "Zmodyfikować uprawnienia"](./media/active-directory-enterprise-app-role-management/graph-explorer-new10.png)

    d. Zaakceptuj zgody. Zalogowano do systemu ponownie.

    e. Zmiana wersji na **beta**i pobrać listy nazwy główne usług z dzierżawy za pomocą następującej kwerendy:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
      Jeśli używasz wielu katalogów, wykonaj tego wzorca: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
      ![Okno dialogowe Eksplorator wykresu, z zapytaniem pobierania nazwy główne usług](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
    f. Z listy nazwy główne usług pobranych Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich podmiotów zabezpieczeń wymienionych usług. Wyszukiwania dla obiektu o identyfikatorze skopiowany z **właściwości** strony i użyj następującego zapytania, aby uzyskać dostęp do nazwy głównej usługi:
    
      `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

      ![Zapytanie o wprowadzenie nazwy głównej usługi, które należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)

    g. Wyodrębnij **appRoles** właściwości z obiektu główną usługi. 

      ![Szczegółowe informacje o właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)

      > [!Note]
      > Jeśli używasz niestandardowej aplikacji (a nie aplikacji portalu Azure Marketplace), zobacz dwa domyślne role: msiam_access i użytkownika. Aplikacja portalu Marketplace msiam_access jest jedyną rolą domyślne. Nie trzeba wprowadzać zmian w domyślnych ról.

    h. Generuj nowe role w aplikacji. 

      Następujący kod JSON jest przykładem **appRoles** obiektu. Utwórz obiekt podobne do dodawania ról, które mają dla aplikacji. 

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
      > Można dodawać tylko nowe role po msiam_access dla operacji poprawki. Ponadto można dodać dowolną liczbę ról jako potrzeb organizacji. Usługi Azure AD będzie wysyłać wartość tych ról jako wartość oświadczenia w odpowiedzi SAML. Do generowania identyfikatora GUID wartości identyfikatora nowych ról, użyj narzędzia sieci web, takie jak [to](https://www.guidgenerator.com/)
    
    i. Przejdź wstecz do Eksploratora wykres i zmień metodę z **UZYSKAĆ** do **poprawka**. Poprawka obiekt główny usługi ma odpowiednie role, aktualizując **appRoles** właściwości tak jak pokazano w poprzednim przykładzie. Wybierz **Uruchom kwerendę** wykonanie operacji poprawki. Komunikat z potwierdzeniem potwierdza tworzenia roli.

      ![Operacja poprawki z komunikat z potwierdzeniem](./media/active-directory-enterprise-app-role-management/graph-explorer-new11.png)

7. Po nazwy głównej usługi jest poprawiono więcej ról, można przypisać użytkowników do odpowiednich ról. Można przypisać użytkownikom, przechodząc do portalu i przeglądanie do aplikacji. Wybierz **użytkowników i grup** kartę. Ta karta zawiera listę wszystkich użytkowników i grup, które są już przypisane do aplikacji. Można dodawać nowych użytkowników na nowych ról. Możesz również wybrać istniejącego użytkownika i wybierz **Edytuj** zmiana roli.

    ![Karta "Użytkownicy i grupy"](./media/active-directory-enterprise-app-role-management/graph-explorer-new5.png)

    Aby przypisać rolę do dowolnego użytkownika, wybierz nową rolę, a następnie wybierz **przypisać** przycisk w dolnej części strony.

    ![Okienka "Edytuj przydziału" i "Wybierz rolę"](./media/active-directory-enterprise-app-role-management/graph-explorer-new6.png)

    > [!Note]
    > Należy odświeżyć sesję w portalu Azure, aby zobaczyć nowe role.

8. Aktualizacja **atrybuty** tabeli, aby zdefiniować niestandardowe mapowanie oświadczenia roli.

9. W **atrybuty użytkownika** sekcji **logowanie jednokrotne** okna dialogowego Skonfiguruj atrybut tokenu SAML, jak pokazano w obrazie i wykonaj następujące kroki.
    
    | Nazwa atrybutu | Wartość atrybutu |
    | -------------- | ----------------|    
    | Nazwa roli      | user.assignedrole |

    a. Wybierz **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okienka.

      ![Przycisk "Dodaj atrybut"](./media/active-directory-enterprise-app-role-management/tutorial_attribute_04.png)

      !["Dodaj atrybut" okienko](./media/active-directory-enterprise-app-role-management/tutorial_attribute_05.png)

    b. W **nazwa** wpisz nazwę atrybutu, zgodnie z potrzebami. W tym przykładzie użyto **nazwy roli** jako nazwa oświadczenia.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. Pozostaw **Namespace** pole puste.
    
    e. Wybierz **Ok**.

10. Aby przetestować aplikację w rejestracji jednokrotnej jest inicjowane przez dostawcę tożsamości, zaloguj się do [panelu dostępu](https://myapps.microsoft.com) i wybrać kafelka aplikacji. Powinny być widoczne wszystkie przypisane role dla użytkownika z nadana nazwa oświadczenia w tokenie SAML.

## <a name="update-an-existing-role"></a>Aktualizuj istniejącą rolę

Aby uaktualnić istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [Eksploratora usługi Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer).

2. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń administratora lub coadmin globalnych dla dzierżawy.
    
3. Zmiana wersji na **beta**i pobrać listy nazwy główne usług z dzierżawy za pomocą następującej kwerendy:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Jeśli używasz wielu katalogów, wykonaj tego wzorca: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksplorator wykresu, z zapytaniem pobierania nazwy główne usług](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Z listy nazwy główne usług pobranych Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich podmiotów zabezpieczeń wymienionych usług. Wyszukiwania dla obiektu o identyfikatorze skopiowany z **właściwości** strony i użyj następującego zapytania, aby uzyskać dostęp do nazwy głównej usługi:
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie o wprowadzenie nazwy głównej usługi, które należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Wyodrębnij **appRoles** właściwości z obiektu główną usługi.
    
    ![Szczegółowe informacje o właściwości appRoles](./media/active-directory-enterprise-app-role-management/graph-explorer-new3.png)
    
6. Aby uaktualnić istniejącą rolę, wykonaj następujące kroki.

    ![Treść żądania "Poprawki", "opis" i "displayname" wyróżnione](./media/active-directory-enterprise-app-role-management/graph-explorer-patchupdate.png)
    
    a. Zmień metodę z **UZYSKAĆ** do **poprawka**.

    b. Istniejące role skopiuj i wklej je w obszarze **treść żądania**.

    c. Zaktualizuj wartość roli, aktualizując opis roli, wartość rolę lub nazwę wyświetlaną roli, zgodnie z potrzebami.

    d. Po zaktualizowaniu wszystkich wymaganych ról, wybierz **Uruchom kwerendę**.
        
## <a name="delete-an-existing-role"></a>Usuń istniejącą rolę

Aby usunąć istniejącą rolę, wykonaj następujące czynności:

1. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

2. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń administratora lub coadmin globalnych dla dzierżawy.

3. Zmiana wersji na **beta**i pobrać listy nazwy główne usług z dzierżawy za pomocą następującej kwerendy:
    
    `https://graph.microsoft.com/beta/servicePrincipals`
    
    Jeśli używasz wielu katalogów, wykonaj tego wzorca: `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu, zapytania do pobierania listy nazwy główne usług](./media/active-directory-enterprise-app-role-management/graph-explorer-new1.png)
    
4. Z listy nazwy główne usług pobranych Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich podmiotów zabezpieczeń wymienionych usług. Wyszukiwania dla obiektu o identyfikatorze skopiowany z **właściwości** strony i użyj następującego zapytania, aby uzyskać dostęp do nazwy głównej usługi:
     
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`

    ![Zapytanie o wprowadzenie nazwy głównej usługi, które należy zmodyfikować](./media/active-directory-enterprise-app-role-management/graph-explorer-new2.png)
    
5. Wyodrębnij **appRoles** właściwości z obiektu główną usługi.
    
    ![Szczegółowe informacje o właściwości appRoles od obiektu głównej usługi](./media/active-directory-enterprise-app-role-management/graph-explorer-new7.png)

6. Aby usunąć istniejącą rolę, wykonaj następujące kroki.

    ![Treść żądania "Poprawki," z IsEnabled ustawiony na wartość false](./media/active-directory-enterprise-app-role-management/graph-explorer-new8.png)

    a. Zmień metodę z **UZYSKAĆ** do **poprawka**.

    b. Skopiuj istniejących ról z aplikacji i wklej je w obszarze **treść żądania**.
        
    c. Ustaw **IsEnabled** do wartości **false** dla roli, który chcesz usunąć.

    d. Wybierz **kwerenda**.
    
    > [!NOTE] 
    > Upewnij się, że rola msiam_access i jest zgodne z Identyfikatorem w wygenerowanym roli.
    
7. Po wyłączeniu roli Usuń ten blok roli z **appRoles** sekcji. Zachowaj metodę jako **poprawka**i wybierz **Uruchom kwerendę**.
    
8. Po uruchomieniu kwerendy roli jest usuwane.
    
    > [!NOTE]
    > Rolę musi być wyłączona, można było usunąć. 

## <a name="next-steps"></a>Kolejne kroki

Dodatkowe kroki opisane w artykule [dokumentacji aplikacji](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

<!--Image references-->
<!--Image references-->

[1]: ./media/active-directory-enterprise-app-role-management/tutorial_general_01.png
[2]: ./media/active-directory-enterprise-app-role-management/tutorial_general_02.png
[3]: ./media/active-directory-enterprise-app-role-management/tutorial_general_03.png
[4]: ./media/active-directory-enterprise-app-role-management/tutorial_general_04.png