---
title: 'Samouczek: Integracji Azure Active Directory z usług sieci Web firmy Amazon (AWS) można połączyć wiele kont | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure AD i wiele kont z usług sieci Web firmy Amazon (AWS).
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
ms.date: 03/28/2018
ms.author: jeedes
ms.openlocfilehash: 2678cf043bb4b2569555309e873ae9ce0ab64eab
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/19/2018
ms.locfileid: "36217801"
---
# <a name="tutorial-azure-active-directory-integration-with-multiple-amazon-web-services-aws-accounts"></a>Samouczek: Integracji Azure Active Directory z wieloma kontami usługi Amazon Web Services (AWS)

Z tego samouczka dowiesz się Integrowanie usługi Azure Active Directory (Azure AD) z wieloma kontami z usług sieci Web firmy Amazon (AWS).

Integrowanie usługi Amazon Web Services (AWS) z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usług sieci Web firmy Amazon (AWS).
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Amazon Web Services (AWS) (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Amazon Web Services (AWS), potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD podstawowa lub premium
- Amazon Web Services (AWS) wielu logowanie jednokrotne włączone konta

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Amazon Web Services (AWS) z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-amazon-web-services-aws-from-the-gallery"></a>Dodawanie Amazon Web Services (AWS) z galerii
Aby skonfigurować integrację z usługami sieci Web Amazon (AWS) do usługi Azure AD, należy dodać Amazon Web Services (AWS) z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Amazon Web Services (AWS) z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Amazon Web Services (AWS)**, wybierz pozycję **Amazon Web Services (AWS)** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_addfromgallery.png)

5. Po dodaniu aplikacji, przejdź do **właściwości** strony i skopiuj **obiektu o identyfikatorze**.

    ![Amazon Web Services (AWS) na liście wyników](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_properties.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Amazon Web usługi (AWS) w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Amazon Web Services (AWS) jest dla użytkownika, w usłudze Azure AD. Innymi słowy musi można ustanowić łącze relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Amazon Web Services (AWS).

W Amazon Web Services (usług AWS), przypisz wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Amazon Web Services (AWS), należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji usługi sieci Web firmy Amazon (AWS).

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Amazon Web Services (AWS), wykonaj następujące czynności:**

1. W portalu Azure na **Amazon Web Services (AWS)** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_samlbase.png)

3. Na **Amazon Web Services (AWS) domeny i adres URL** sekcji, użytkownik nie trzeba wykonywać żadnych czynności, jak aplikacja już jest wstępna Integracja z usługą Azure.

    ![Domena Amazon Web Services (AWS) i adresy URL pojedynczy informacje logowania jednokrotnego](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_url.png)

4. Aplikacji Amazon Web Services (AWS) oczekuje potwierdzenia języka SAML w określonym formacie. Skonfiguruj następujące oświadczeń dla tej aplikacji. Możesz zarządzać wartości tych atrybutów z "**atrybuty użytkownika**" sekcji na stronie integracji aplikacji. Poniższy zrzut ekranu przedstawia przykład tego.

    ![Konfigurowanie rejestracji jednokrotnej atrybutu](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_attribute.png)    

5. W **atrybuty użytkownika** sekcji na **logowanie jednokrotne** okna dialogowego, skonfiguruj atrybut tokenu SAML, jak pokazano na ilustracji powyżej i wykonaj następujące czynności:
    
    | Nazwa atrybutu  | Wartość atrybutu | Przestrzeń nazw |
    | --------------- | --------------- | --------------- |
    | RoleSessionName | user.userprincipalname | https://aws.amazon.com/SAML/Attributes |
    | Rola            | User.assignedroles |  https://aws.amazon.com/SAML/Attributes |
    
    >[!TIP]
    >Należy skonfigurować Inicjowanie obsługi użytkowników w usłudze Azure AD można pobrać za pomocą konsoli usług AWS wszystkich ról. Zobacz poniższe kroki inicjowania obsługi administracyjnej.

    a. Kliknij przycisk **Dodaj atrybut** otworzyć **Dodawanie atrybutu** okna dialogowego.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/aws-multi-accounts-tutorial/tutorial_attribute_04.png)

    ![Konfigurowanie rejestracji jednokrotnej atrybutu](./media/aws-multi-accounts-tutorial/tutorial_attribute_05.png)

    b. W **nazwa** tekstowym, wpisz nazwę atrybut wyświetlany dla danego wiersza.

    c. Z **wartość** listy, wpisz wartość atrybutu wyświetlany dla danego wiersza.

    d. W **Namespace** tekstowym, wpisz wartość przestrzeni nazw wyświetlany dla danego wiersza.
    
    d. Kliknij przycisk **OK**.

6. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_certificate.png) 

7. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/aws-multi-accounts-tutorial/tutorial_general_400.png)

8. W oknie innej przeglądarki logowania do witryny firmy Amazon Web Services (AWS) jako Administrator.

9. Kliknij przycisk **głównej usług AWS**.
   
    ![Konfigurowanie rejestracji jednokrotnej głównej][11]

10. Kliknij przycisk **IAM** (Zarządzanie tożsamościami i dostępem). 
   
    ![Konfigurowanie tożsamości rejestracji jednokrotnej][12]

11. Kliknij przycisk **dostawców tożsamości**, a następnie kliknij przycisk **Tworzenie dostawcy**. 
   
    ![Konfigurowanie dostawcy rejestracji jednokrotnej][13]

12. Na **Konfigurowanie dostawcy** okna dialogowego strony, należy wykonać następujące czynności: 
   
    ![Konfigurowanie rejestracji jednokrotnej okna dialogowego][14]
 
    a. Jako **typ dostawcy**, wybierz pozycję **SAML**.

    b. W **Nazwa dostawcy** tekstowym, wpisz nazwę dostawcy (na przykład: *drewna*).

    c. Aby przekazać z pobranego **pliku metadanych** z portalu Azure, kliknij przycisk **wybierz plik**.

    d. Kliknij przycisk **następny krok**.

13. Na **Sprawdź informacje o dostawcy** strony okna dialogowego, kliknij przycisk **Utwórz**. 
    
    ![Konfigurowanie rejestracji jednokrotnej Sprawdź][15]

14. Kliknij przycisk **ról**, a następnie kliknij przycisk **Utwórz rolę**. 
    
    ![Konfigurowanie ról rejestracji jednokrotnej][16]

15. Na **Utwórz rolę** wykonaj następujące czynności:  
    
    ![Skonfigurować zaufanie rejestracji jednokrotnej][19] 

    a. Wybierz **federacyjnego SAML 2.0** w obszarze **wybierz typ zaufana jednostka**.

    b. W obszarze **Wybieranie sekcji SAML 2.0 dostawcy**, wybierz pozycję **SAML dostawcy** utworzonym wcześniej (na przykład: *drewna*)

    c. Wybierz **Zezwalaj programowych i dostępu do konsoli zarządzania usług AWS**.
  
    d. Kliknij przycisk **dalej: uprawnienia**.

16. Na **Dołącz zasady uprawnień** okna dialogowego, kliknij przycisk **dalej: Przejrzyj**.  
    
    ![Konfigurowanie zasad rejestracji jednokrotnej][33]

17. Na **przeglądu** okna dialogowego, wykonaj następujące czynności:   
    
    ![Skonfiguruj przeglądu rejestracji jednokrotnej][34] 

    a. W **nazwy roli** pole tekstowe, wprowadź nazwę roli.

    b. W **opis roli** pole tekstowe, wprowadź opis.

    a. Kliknij przycisk **utworzyć rolę**.

    b. Tworzenie ról tyle zgodnie z potrzebami i zamapowania ich na dostawcy tożsamości.

18. Wyloguj z bieżącego konta usług AWS i zaloguj się za pomocą innego konta, które chcesz skonfigurować funkcji logowania jednokrotnego w usłudze Azure AD.

19. Wykonaj krok-9, aby krok-17, aby utworzyć wiele ról, które mają być instalacji dla tego konta. Jeśli masz więcej niż dwóch kont, wykonaj te same kroki dla wszystkich kont utworzyć role dla nich.

20. Po utworzeniu wszystkich ról w ramach kont one widoczne w **ról** listy dla tych kont.

    ![Instalator ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_listofroles.png)

21. Musimy przechwytywania ARN roli i zaufane jednostki dla wszystkich ról, wszystkich kont, które należy ręcznie mapują z aplikacji usługi Azure AD. 

22. Kliknij na rolach, aby skopiować **ARN roli** i **zaufane jednostki** wartości. Te wartości są wymagane dla wszystkich ról, które należy utworzyć w usłudze Azure AD.

    ![Instalator ról](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_role_summary.png)
 
23. Wykonaj powyższy krok dla wszystkich ról w ramach kont i przechowywać wszystkie z nich w formacie **ARN roli, zaufane jednostki** w Notatniku. 

24. Otwórz [Explorer Azure AD Graph](https://developer.microsoft.com/graph/graph-explorer) w innym oknie.

    a. Zaloguj się do witryny Explorer wykresu przy użyciu poświadczeń administratora/ko Administrator globalny dla dzierżawy.

    b. Musisz mieć wystarczające uprawnienia do tworzenia ról. Polecenie **uprawnień do modyfikowania** można uzyskać wymaganych uprawnień. 

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new9.png)

    c. Wybierz następujące uprawnienia z listy (Jeśli te nie mają już) i kliknij polecenie "Modyfikuj uprawnienia" 

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new10.png)

    d. Poprosi zalogować się ponownie i zaakceptować zgody. Po zaakceptowaniu zgody, użytkownik jest zalogowany w Eksploratorze wykres ponownie.

    e. Wersja listy rozwijanej, aby zmienić **beta**. Można pobrać wszystkich podmiotów zabezpieczeń usługi z dzierżawy, użyj następującego zapytania:
    
     `https://graph.microsoft.com/beta/servicePrincipals`
        
    Jeśli używasz wielu katalogów, możesz użyć następujących wzorcu ma w nim domeny głównej `https://graph.microsoft.com/beta/contoso.com/servicePrincipals`
    
    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new1.png)
    
    f. Z listy pobranych nazwy główne usług Pobierz ten, który należy zmodyfikować. Ctrl + F umożliwia również wyszukiwanie aplikacji z wszystkich wymienionych ServicePrincipals. Można użyć następujących zapytań, za pomocą **obiektu o identyfikatorze** którego została skopiowana ze strony właściwości usługi Azure AD można pobrać do odpowiednich główną usługi.
    
    `https://graph.microsoft.com/beta/servicePrincipals/<objectID>`.

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new2.png)

    g. Wyodrębnij właściwości appRoles z obiekt główny usługi. 

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new3.png)

    h. Teraz należy wygenerować nowe role w aplikacji. 

    i. Poniżej JSON jest przykładem appRoles obiektu. Utwórz obiekt podobne do dodawania ról, do których chcesz dla aplikacji. 

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
    > Można dodawać tylko nowe role po **msiam_access** dla operacji poprawki. Ponadto można dodać dowolną liczbę ról można dowolnie na potrzeby organizacji. Usługi Azure AD będzie wysyłać **wartość** tych ról jako wartość oświadczenia w odpowiedzi SAML.
    
    j. Przejdź wstecz do Eksploratora z wykresu i zmień metodę z **UZYSKAĆ** do **poprawka**. Poprawka obiektu nazwy głównej usługi ma pożądane ról przez modyfikowanie właściwości appRoles podobny do przedstawionego powyżej w przykładzie. Kliknij przycisk **Uruchom kwerendę** wykonanie operacji poprawki. Komunikat z potwierdzeniem potwierdza utworzenie roli aplikacji usług Amazon Web Services.

    ![Okno dialogowe Eksplorator wykresu](./media/aws-multi-accounts-tutorial/graph-explorer-new11.png)

25. Po nazwy głównej usługi jest poprawiono więcej ról, można przypisać użytkowników i grupy do odpowiednich ról. Można to zrobić, przechodząc do portalu i do aplikacji usług Amazon Web Services. Polecenie **użytkowników i grup** na górnym pasku. 

26. Zalecamy tworzenie nowych grup dla każdej roli usług AWS, dzięki czemu można przypisać tej konkretnej roli w tej grupie. Należy pamiętać, że to mapowanie jeden-do-jednego dla jednej grupy do jednej roli. Następnie można dodać członków, którzy należą do tej grupy.

27. Po utworzeniu grupy, wybierz grupę i przypisać do aplikacji. 

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/aws-multi-accounts-tutorial/graph-explorer-new5.png)

28. Aby przypisać rolę do grupy, wybierz rolę, a następnie kliknij pozycję **przypisać** przycisk w dolnej części strony.

    ![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/aws-multi-accounts-tutorial/graph-explorer-new6.png)

> [!Note]
> Należy pamiętać, trzeba odświeżyć sesję w portalu Azure, aby zobaczyć nowe role.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Amazon Web Services (AWS) w panelu dostępu, należy pobrać strony aplikacji Amazon Web Services (AWS) z opcji, aby wybrać rolę.

![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_screen.png)

Można również sprawdzić odpowiedzi SAML, aby wyświetlić role były przekazywane jako oświadczenia.

![Konfigurowanie rejestracji jednokrotnej Dodaj](./media/aws-multi-accounts-tutorial/tutorial_amazonwebservices(aws)_test_saml.png)

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



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

