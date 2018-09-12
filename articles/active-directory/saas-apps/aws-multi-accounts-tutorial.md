---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Amazon Web Services (AWS) do łączenia z wieloma kontami | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure AD i wiele kont Amazon Web Services (AWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 7561c20b-2325-4d97-887f-693aa383c7be
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2018
ms.author: jeedes
ms.openlocfilehash: a7d77df4d6be1572d2076684cfa4702cb32b5ed6
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391916"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Samouczek: Integracja usługi Azure Active Directory z wieloma kontami usług Amazon Web Services (AWS)

W tym samouczku dowiesz się, jak zintegrować usługi Azure Active Directory (Azure AD) z wieloma kontami Amazon Web Services (AWS).

Integrowanie usług Amazon Web Services (AWS) z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do usługi Amazon Web Services (AWS).
- Użytkowników, aby automatycznie uzyskać zalogowanych do Amazon Web Services (AWS) (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

![Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/amazonwebservice.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą usługi Amazon Web Services (AWS), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD podstawowa lub premium
- Amazon Web Services (AWS) wielu logowanie jednokrotne włączone konta

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie usług Amazon Web Services (AWS) z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie usług Amazon Web Services (AWS) z galerii
Aby skonfigurować integrację z usługi Amazon Web Services (AWS) do usługi Azure AD, należy dodać Amazon Web Services (AWS) z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Amazon Web Services (AWS) z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz opcję **Amazon Web Services (AWS)** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Po dodaniu aplikacji przejdź do obszaru **właściwości** strony i skopiuj **obiektu o identyfikatorze**.

    ![Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowania, w usłudze Azure AD logowanie jednokrotne, za pomocą Amazon Web Services (AWS) na podstawie użytkownika testowego, o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Amazon Web Services (AWS) do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Amazon Web Services (AWS) musi zostać ustanowione.

W Amazon Web Services (AWS), przypisz wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji usług Amazon Web Services (AWS).

**Aby skonfigurować usługi Azure AD logowanie jednokrotne za pomocą usługi Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W witrynie Azure portal na **Amazon Web Services (AWS)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na **Amazon Web Services (AWS) domena i adresy URL** sekcji, użytkownik nie ma do wykonywania żadnych czynności, jak aplikacja już jest wstępnie zintegrowana z platformą Azure.

    ![Domena usługi Amazon Web Services (AWS) i adresy URL pojedynczego logowania jednokrotnego informacji](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Aplikacja oprogramowania usług Amazon Web Services (AWS) oczekuje twierdzenia SAML w określonym formacie. Skonfiguruj następujące oświadczenia dla tej aplikacji. Możesz zarządzać wartości te atrybuty z "**atrybutów użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie logowania jednokrotnego, atrybut](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)    

5. W **atrybutów użytkownika** sekcji na **logowanie jednokrotne** okno dialogowe, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:

    | Nazwa atrybutu  | Wartość atrybutu | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    | SessionDuration             | "Podaj wartość czas trwania sesji na potrzeby" |  https://aws.amazon.com/SAML/Attributes |

    >[!TIP]
    >Należy skonfigurować aprowizację użytkowników w usłudze Azure AD, aby pobrać wszystkie role z konsoli usług AWS. Zobacz poniższe kroki inicjowania obsługi administracyjnej.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie logowania jednokrotnego, atrybut](./media/aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** polu tekstowym wpisz nazwę atrybutu, wyświetlanego dla tego wiersza.

    c. Z **wartość** wpisz wartość atrybutu wyświetlanego dla tego wiersza.

    d. W **Namespace** polu tekstowym wpisz wartość przestrzeni nazw wyświetlanego dla tego wiersza.

    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Link pobierania certyfikatu](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/aws-multi-accounts-tutorial/tutorial_general_400.png)

8. W oknie innej przeglądarki Zaloguj się do witryny firmy Amazon Web Services (AWS) jako Administrator.

9. Kliknij przycisk **strona główna usług AWS**.

    ![Konfigurowanie logowania jednokrotnego głównej][11]

10. Kliknij przycisk **IAM** (Zarządzanie tożsamościami i dostępem).

    ![Konfigurowanie tożsamości rejestracji jednokrotnej][12]

11. Kliknij przycisk **dostawców tożsamości**, a następnie kliknij przycisk **Tworzenie dostawcy**.

    ![Konfigurowanie dostawcy rejestracji jednokrotnej][13]

12. Na **Konfigurowanie dostawcy** okna dialogowego strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego okna dialogowego][14]

    a. Jako **typ dostawcy**, wybierz opcję **SAML**.

    b. W **Nazwa dostawcy** polu tekstowym wpisz nazwę dostawcy (na przykład: *WAAD*).

    c. Można przekazać swoje pobrany **plik metadanych** z witryny Azure portal, kliknij przycisk **wybierz plik**.

    d. Kliknij przycisk **następny krok**.

13. Na **Sprawdź informacje o dostawcy** strony okna dialogowego kliknij **Utwórz**.

    ![Konfigurowanie logowania jednokrotnego Sprawdź][15]

14. Kliknij przycisk **role**, a następnie kliknij przycisk **tworzenia ról**.

    ![Konfigurowanie ról rejestracji jednokrotnej][16]

15. Na **tworzenia ról** strony, wykonaj następujące czynności:  

    ![Skonfigurować zaufanie rejestracji jednokrotnej][19]

    a. Wybierz **federation SAML 2.0** w obszarze **wybierz typ obiektu zaufanego**.

    b. W obszarze **wybierz sekcję SAML 2.0 Provider**, wybierz opcję **SAML dostawcy** utworzonego wcześniej (na przykład: *WAAD*)

    c. Wybierz **dozwolonych programowy i dostęp do konsoli zarządzania usług AWS**.
  
    d. Kliknij przycisk **dalej: uprawnienia**.

16. Na **Dołącz zasady uprawnień** okno dialogowe, kliknij przycisk **dalej: Przejrzyj**.  

    ![Konfigurowanie zasad rejestracji jednokrotnej][33]

17. Na **przeglądu** okno dialogowe, należy wykonać następujące czynności:

    ![Konfigurowanie przeglądu rejestracji jednokrotnej][34]

    a. W **nazwy roli** polu tekstowym wprowadź nazwę roli.

    b. W **opis roli** polu tekstowym wprowadź opis.

    a. Kliknij przycisk **utworzyć rolę**.

    b. Utwórz tyle ról stosownie do potrzeb i mapować je do dostawcy tożsamości.

18. Wyloguj z bieżącego konta usługi AWS i zaloguj się przy użyciu innego konta, które chcesz skonfigurować funkcję logowania jednokrotnego na usłudze Azure AD.

19. Wykonaj krok – 9, aby krok-17, aby utworzyć wiele ról, które chcesz instalacji dla tego konta. Jeśli masz więcej niż dwóch kont, wykonaj te same czynności dla wszystkich kont do tworzenia ról dla nich.

20. Gdy wszystkie role są tworzone w ramach kont, są one wyświetlane w **role** listy dla tych kont.

    ![Ustawienia ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Musimy przechwytywania ARN roli i zaufane jednostki dla wszystkich ról dla wszystkich kont, które musimy mapowanie ręczne za pomocą aplikacji usługi Azure AD. 

22. Kliknij na rolach, aby skopiować **ARN roli** i **zaufane jednostki** wartości. Te wartości będą potrzebne dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Ustawienia ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)

23. Wykonaj powyższy krok dla wszystkich ról we wszystkich kont i zapisz je wszystkie w formacie **ARN roli, zaufane jednostki** w Notatniku.

24. Otwórz [programu Azure AD Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Graph Explorer przy użyciu poświadczeń globalny administrator/współadministrator dla Twojej dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Kliknij pozycję **modyfikowania uprawnień** do uzyskania wymaganych uprawnień.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (jeśli jeszcze nie masz tych) i kliknij pozycję "Modyfikuj uprawnienia" 

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Zapyta zalogować się ponownie i zaakceptować zgody. Po zaakceptowaniu zezwolenia, użytkownik jest zalogowany w Eksploratorze programu Graph ponownie.

    e. Zmień listę rozwijaną wersji, aby **beta**. Do pobrania wszystkich jednostek usługi w dzierżawie, użyj następującego zapytania:

     `https://graph.microsoft.com/beta/servicePrincipals`

    Jeśli używasz wielu katalogów, wówczas można użyć następujących wzorzec ma w nim domeny głównej `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)

    f. Z listy pobrania nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji ze wszystkich wymienionych ServicePrincipals. Można użyć następujących zapytań, za pomocą **obiektu o identyfikatorze** skopiowanej ze strony właściwości usługi AD platformy Azure w celu uzyskania do odpowiedniej nazwy głównej usługi.

    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Wyodrębnij właściwości appRoles z obiektu jednostki usługi.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teraz trzeba generować nowe role w aplikacji. 

    i. Poniżej JSON jest przykładem obiektu appRoles. Utworzyć podobne obiektu można dodać role, które mają dla swojej aplikacji.

    ```
    {
    "appRoles": [
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "msiam_access",
            "displayName": "msiam_access",
            "id": "7dfd756e-8c27-4472-b2b7-38c17fc5de5e",
            "isEnabled": true,
            "origin": "Application",
            "value": null
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Admin,WAAD",
            "displayName": "Admin,WAAD",
            "id": "4aacf5a4-f38b-4861-b909-bae023e88dde",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Admin,arn:aws:iam::12345:saml-provider/WAAD"
        },
        {
            "allowedMemberTypes": [
                "User"
            ],
            "description": "Auditors,WAAD",
            "displayName": "Auditors,WAAD",
            "id": "bcad6926-67ec-445a-80f8-578032504c09",
            "isEnabled": true,
            "origin": "ServicePrincipal",
            "value": "arn:aws:iam::12345:role/Auditors,arn:aws:iam::12345:saml-provider/WAAD"
        }    ]
    }
    ```

    > [!Note]
    > Możesz tylko dodawać nowe role po **msiam_access** potrzeby operacji patch. Ponadto można dodać dowolną liczbę ról dowolną na potrzeby Twojej organizacji. Usługa Azure AD będzie wysyłać **wartość** tych ról jako wartość oświadczenia w odpowiedzi SAML.

    j. Wróć do usługi Graph Explorer i zmień metodę z **UZYSKAĆ** do **PATCH**. Stosowanie poprawek do obiektu jednostki usługi mają żądanego ról, aktualizując właściwości appRoles podobny do przedstawionego powyżej w przykładzie. Kliknij przycisk **Uruchom kwerendę** do wykonywania operacji patch. Komunikat o powodzeniu potwierdza tworzenia ról dla aplikacji usług Amazon Web Services.

    ![Okno dialogowe Eksploratora programu Graph](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Po jednostki usługi są zainstalowane odpowiednie poprawki z większej liczby ról, można przypisać użytkowników i grup do odpowiednich ról. Można to zrobić, przechodząc do witryny portal i przejdź do aplikacji usług Amazon Web Services. Kliknij pozycję **użytkowników i grup** kartę w górnej części. 

26. Firma Microsoft zaleca tworzenie nowych grup dla każdej roli usług AWS, dzięki czemu można przypisać tego określoną rolę w tej grupie. Należy zauważyć, że to mapowanie jeden-do-jednego dla jednej grupy do jednej roli. Następnie można dodać członków, którzy należą do tej grupy.

27. Po utworzeniu grupy wybierz grupę i przypisz do aplikacji.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

> [!Note]
> Grupy zagnieżdżone nie są obsługiwane podczas przypisywania grup.

28. Aby przypisać rolę do grupy, wybierz rolę, a następnie kliknij pozycję **przypisać** przycisk w dolnej części strony.

    ![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Należy pamiętać, trzeba odświeżyć sesję w witrynie Azure portal, aby zobaczyć nowe role.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka usługi Amazon Web Services (AWS), w panelu dostępu, powinna pojawić się strona aplikacji usług Amazon Web Services (AWS) przy użyciu opcji, aby wybrać rolę.

![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Można również sprawdzić odpowiedzi SAML, aby zobaczyć ról przekazywany jako oświadczenia.

![Konfigurowanie logowania jednokrotnego Dodaj](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/aws-multi-accounts-tutorial/tutorial_general_01.png
[2]: ./media/aws-multi-accounts-tutorial/tutorial_general_02.png
[3]: ./media/aws-multi-accounts-tutorial/tutorial_general_03.png
[4]: ./media/aws-multi-accounts-tutorial/tutorial_general_04.png

[100]: ./media/aws-multi-accounts-tutorial/tutorial_general_100.png

[200]: ./media/aws-multi-accounts-tutorial/tutorial_general_200.png
[201]: ./media/aws-multi-accounts-tutorial/tutorial_general_201.png
[202]: ./media/aws-multi-accounts-tutorial/tutorial_general_202.png
[203]: ./media/aws-multi-accounts-tutorial/tutorial_general_203.png
[11]: ./media/aws-multi-accounts-tutorial/ic795031.png
[12]: ./media/aws-multi-accounts-tutorial/ic795032.png
[13]: ./media/aws-multi-accounts-tutorial/ic795033.png
[14]: ./media/aws-multi-accounts-tutorial/ic795034.png
[15]: ./media/aws-multi-accounts-tutorial/ic795035.png
[16]: ./media/aws-multi-accounts-tutorial/ic795022.png
[17]: ./media/aws-multi-accounts-tutorial/ic795023.png
[18]: ./media/aws-multi-accounts-tutorial/ic795024.png
[19]: ./media/aws-multi-accounts-tutorial/ic795025.png
[32]: ./media/aws-multi-accounts-tutorial/ic7950251.png
[33]: ./media/aws-multi-accounts-tutorial/ic7950252.png
[35]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning.png
[34]: ./media/aws-multi-accounts-tutorial/ic7950253.png
[36]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials.png
[37]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_securitycredentials_continue.png
[38]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_createnewaccesskey.png
[39]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_automatic.png
[40]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_testconnection.png
[41]: ./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices_provisioning_on.png

