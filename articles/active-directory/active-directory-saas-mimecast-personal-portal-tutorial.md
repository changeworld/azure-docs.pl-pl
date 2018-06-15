---
title: 'Samouczek: Azure Active Directory integracji z portalu osobiste Mimecast | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między Mimecast osobistych portalu i usługi Azure Active Directory.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 345b22be-d87e-45a4-b4c0-70a67eaf9bfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2018
ms.author: jeedes
ms.openlocfilehash: 4b976481e60926e6ab95a679e6dcc8aa0818b48b
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346884"
---
# <a name="tutorial-azure-active-directory-integration-with-mimecast-personal-portal"></a>Samouczek: Integracji Azure Active Directory z Mimecast osobistych portalu

Z tego samouczka dowiesz integrowanie Mimecast Portal osobiste z usługą Azure Active Directory (Azure AD).

Integrowanie portalu osobiste Mimecast z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do portalu osobiste Mimecast.
- Umożliwia użytkownikom automatycznie pobrać zalogowane Mimecast portalu osobiste (logowanie jednokrotne) z konta usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z portalem osobiste Mimecast, potrzebne są następujące zasoby:

- Subskrypcję usługi Azure AD
- Mimecast osobistych portalu rejestracji jednokrotnej włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie portalu osobiste Mimecast z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-mimecast-personal-portal-from-the-gallery"></a>Dodawanie portalu osobiste Mimecast z galerii
Aby skonfigurować integrację usługi Azure AD Mimecast osobistych portalu, należy dodać Portal osobiste Mimecast z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Mimecast osobistych Portal z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Mimecast osobistych portalu**, wybierz pozycję **Mimecast osobistych portalu** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Portal osobiste Mimecast na liście wyników](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z portalem osobiste Mimecast w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w portalu osobiste Mimecast jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązane użytkownika w portalu osobiste Mimecast musi określone.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z portalem osobiste Mimecast, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego portalu osobiste Mimecast](#create-a-mimecast-personal-portal-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Mimecast Portal osobistych, który jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Portal osobiste Mimecast.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Mimecast osobistych portalu, wykonaj następujące czynności:**

1. W portalu Azure na **Portal osobiste Mimecast** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_samlbase.png)

3. Na **Mimecast osobistych portalu domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Mimecast osobistych portalu domeny i adres URL z jednym informacje logowania jednokrotnego](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL: 

    | Region  |  Wartość | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Platformy        | `https://jer-api.mimecast.com/login/saml`|

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:

    | Region  |  Wartość | 
    | --------------- | --------------- |
    | Europa          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/sso/<accountcode>`|    
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | Australia       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | Platformy        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL: 

    | Region  |  Wartość | 
    | --------------- | --------------- | 
    | Europa          | `https://eu-api.mimecast.com/login/saml`|
    | Stany Zjednoczone   | `https://us-api.mimecast.com/login/saml`|
    | Republika Południowej Afryki    | `https://za-api.mimecast.com/login/saml`|
    | Australia       | `https://au-api.mimecast.com/login/saml`|
    | Platformy        | `https://jer-api.mimecast.com/login/saml`|
    
    > [!NOTE] 
    > Wartość identyfikatora nie jest prawdziwe. Zaktualizuj tę wartość z rzeczywistego identyfikatora. Skontaktuj się z [zespołem pomocy technicznej osobiste klienta portalu Mimecast](http://www.mimecast.com/customer-success/technical-support/) można uzyskać wartość. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_400.png)

6. Na **Mimecast osobistych konfiguracji portalu** , kliknij przycisk **skonfigurować Portal osobiste Mimecast** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja portalu Mimecast osobiste](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do portalu osobiste Mimecast jako administrator.

8. Przejdź do **usług \> aplikacji**.
   
    ![Aplikacje](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794998.png "aplikacji")

9. Kliknij przycisk **profile uwierzytelniania**.
   
    ![Profile uwierzytelniania](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic794999.png "profile uwierzytelniania")

10. Kliknij przycisk **nowy profil uwierzytelniania**.
   
    ![Nowy profil uwierzytelniania](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795000.png "nowy profil uwierzytelniania")

11. W **profilu uwierzytelniania** sekcji, wykonaj następujące czynności:
   
    ![Profil uwierzytelniania](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795001.png "profilu uwierzytelniania")
   
    a. W **opis** tekstowym, wpisz nazwę dla danej konfiguracji.
   
    b. Wybierz **wymusić uwierzytelnianie SAML portalu osobiste Mimecast**.
   
    c. Jako **dostawcy**, wybierz pozycję **usługi Azure Active Directory**.
   
    d. W **adres URL wystawcy** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.
   
    e. W **adres URL logowania** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.
   
    f. W **adresu URL wylogowania** pole tekstowe, Wklej wartość **Sign-Out URL**, które zostały skopiowane z portalu Azure.

    g. Otwórz z **base-64** zakodowanego certyfikatu w programie Notatnik pobrany z portalu Azure, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikat dostawcy tożsamości (metadanymi)** pola tekstowego.

    h. Wybierz **Zezwalaj funkcji logowania jednokrotnego w**.
   
    i. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-mimecast-personal-portal-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-mimecast-personal-portal-test-user"></a>Tworzenie użytkownika testowego Mimecast osobistych portalu

Aby umożliwić użytkownikom zalogować się do portalu osobiste Mimecast usługi Azure AD, muszą mieć przydzielone do Mimecast osobistych portalu. W przypadku osobistych portalu Mimecast Inicjowanie obsługi to zadanie ręczne.

Należy zarejestrować domeny, przed przystąpieniem do tworzenia użytkowników.

**Aby skonfigurować, inicjowanie obsługi użytkowników, wykonaj następujące czynności:**

1. Zaloguj się na Twojej **Portal osobiste Mimecast** jako administrator.

2. Przejdź do **katalogów \> wewnętrzny**.
   
    ![Katalogi](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795003.png "katalogów")

3. Kliknij przycisk **zarejestrować nową domenę**.
   
    ![Zarejestrować nową domenę](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795004.png "zarejestrować nową domenę")

4. Po utworzeniu nowej domeny, kliknij przycisk **nowy adres**.
   
    ![Nowy adres](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795005.png "nowy adres")

5. W oknie dialogowym Nowy adres, wykonaj następujące kroki prawidłowy Azure AD konta chcesz udostępnić:
   
    ![Zapisz](./media/active-directory-saas-mimecast-personal-portal-tutorial/ic795006.png "Zapisz")
   
    a. W **adres E-mail** pole tekstowe, typ **adres E-mail** użytkownika jako **BrittaSimon@contoso.com**.
    
    b. W **globalną nazwę** pole tekstowe, typ **username** jako **BrittaSimon**.

    c. W **hasło**, i **Potwierdź hasło** pól tekstowych, typ **hasło** użytkownika.
   
    b. Kliknij pozycję **Zapisz**.

>[!NOTE]
>Inne narzędzia do tworzenia konta użytkownika portalu osobiste Mimecast lub interfejsów API dostarczonych przez Portal osobiste Mimecast służy do obsługi administracyjnej kont użytkowników usługi Azure AD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do portalu osobiste Mimecast.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Mimecast osobistych portalu, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Portal osobiste Mimecast**.

    ![Łącze Mimecast Portal osobistych na liście aplikacji](./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_mimecastpersonalportal_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Mimecast Portal osobistych w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Portal osobiste Mimecast.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-mimecast-personal-portal-tutorial/tutorial_general_203.png

