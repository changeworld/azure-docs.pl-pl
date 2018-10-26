---
title: 'Samouczek: Integracja usługi Azure Active Directory z MyWorkDrive | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2018
ms.author: jeedes
ms.openlocfilehash: 7310d300c68399c31d9580f070602aa3adbc75e3
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50094060"
---
# <a name="tutorial-azure-active-directory-integration-with-myworkdrive"></a>Samouczek: Integracja usługi Azure Active Directory z MyWorkDrive

W tym samouczku dowiesz się, jak zintegrować MyWorkDrive w usłudze Azure Active Directory (Azure AD).

Integrowanie MyWorkDrive z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do MyWorkDrive.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do MyWorkDrive (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą MyWorkDrive, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- MyWorkDrive logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie MyWorkDrive z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-myworkdrive-from-the-gallery"></a>Dodawanie MyWorkDrive z galerii
Aby skonfigurować integrację MyWorkDrive do usługi Azure AD, należy dodać MyWorkDrive z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać MyWorkDrive z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![image](./media/myworkdrive-tutorial/selectazuread.png)

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![image](./media/myworkdrive-tutorial/a_select_app.png)
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![image](./media/myworkdrive-tutorial/a_new_app.png)

4. W polu wyszukiwania wpisz **MyWorkDrive**, wybierz opcję **MyWorkDrive** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą MyWorkDrive, w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w MyWorkDrive do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w MyWorkDrive musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą MyWorkDrive, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego MyWorkDrive](#create-a-myworkdrive-test-user)**  — aby odpowiednikiem Britta Simon w MyWorkDrive, połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji MyWorkDrive.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z MyWorkDrive, wykonaj następujące czynności:**

1. W [witryny Azure portal](https://portal.azure.com/)na **MyWorkDrive** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![image](./media/myworkdrive-tutorial/B1_B2_Select_SSO.png)

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, wybierz **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![image](./media/myworkdrive-tutorial/b1_b2_saml_sso.png)

3. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** przycisk, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![image](./media/myworkdrive-tutorial/b1-domains_and_urlsedit.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url.png)

    W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

5. Kliknij pozycję **Ustaw dodatkowe adresy URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<SERVER.DOMAIN.COM>/Account/Login-saml` 

    > [!NOTE]
    > Te wartości są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości.  Wprowadź name:e.g hosta serwera MyWorkDrive własnej firmy.
    > 
    > Adres URL odpowiedzi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Adres URL logowania:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Jeśli wiesz, jak skonfigurować własną nazwę hosta i certyfikat protokołu SSL dla tych wartości, skontaktuj się z zespołem pomocy technicznej MyWorkDrive klienta.

6. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, kliknij przycisk Kopiuj **ikonę** do skopiowania **adres Url metadanych Federacji aplikacji** i kliknij przycisk **Pobierz** można pobrać **certyfikat (Base64)** Zapisz go na komputerze.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_certficate.png) 

7. Na **Konfigurowanie MyWorkDrive** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    Należy zwrócić uwagę na to, czy adres URL może wskazywać następujące czynności:

    a. Adres URL logowania

    b. Identyfikator usługi Azure Ad

    c. Adres URL wylogowywania

    ![image](./media/myworkdrive-tutorial/d1_samlsonfigure.png) 

8. Aby skonfigurować logowanie jednokrotne na stronie MyWorkDrive, Pobierz **certyfikat (Base64), adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** ręcznie skonfigurować serwer MyWorkDrive lub skopiować i wkleić Azure  **Adres URL metadanych Federacji aplikacji** do Azure MyWorkDrive Server Admin panelu SAML ekranu konfiguracji usługi AD. Aby uzyskać dodatkowe informacje, skontaktuj się z pomocą [zespołem pomocy technicznej MyWorkDrive](mailto:support@myworkdrive.com).

    
### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![image](./media/myworkdrive-tutorial/d_users_and_groups.png)

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![image](./media/myworkdrive-tutorial/d_adduser.png)

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![image](./media/myworkdrive-tutorial/d_userproperties.png)

    a. W **nazwa** pola wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.
 
### <a name="create-a-myworkdrive-test-user"></a>Tworzenie użytkownika testowego MyWorkDrive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w MyWorkDrive. Praca z [zespołem pomocy technicznej MyWorkDrive](mailto:support@myworkdrive.com) Aby dodać użytkowników na platformie MyWorkDrive. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do MyWorkDrive.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![image](./media/myworkdrive-tutorial/d_all_applications.png)

2. Na liście aplikacji wybierz **MyWorkDrive**.

    ![image](./media/myworkdrive-tutorial/tutorial_myworkdrive_app.png)

3. W menu po lewej stronie wybierz **użytkowników i grup**.

    ![image](./media/myworkdrive-tutorial/d_leftpaneusers.png)

4. Wybierz **Dodaj** przycisk, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![image](./media/myworkdrive-tutorial/d_assign_user.png)

4. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

5. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka MyWorkDrive w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji MyWorkDrive.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
