---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Tableau Online | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Tableau Online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 1d4b1149-ba3b-4f4e-8bce-9791316b730d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2017
ms.author: jeedes
ms.openlocfilehash: 9e7de7946769eb2aeeac1c8b21448efa142728bb
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-online"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Tableau Online

Z tego samouczka dowiesz się integrowanie Tableau Online z usługą Azure Active Directory (Azure AD).

Integrowanie Tableau Online z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Tableau Online
- Umożliwia użytkownikom automatycznie pobrać zalogowane Tableau online (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Tableau Online, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Tableau Online logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Tableau Online z poziomu galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-tableau-online-from-the-gallery"></a>Dodawanie Tableau Online z poziomu galerii
Aby skonfigurować integrację Tableau online z usługą Azure AD, należy dodać do listy zarządzanych aplikacji SaaS Tableau Online z poziomu galerii.

**Aby dodać Tableau Online z poziomu galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Tableau Online**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_search.png)

5. W panelu wyników wybierz **Tableau Online**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji można skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Tableau Online na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Tableau w trybie Online jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w trybie Online Tableau musi określone.

W Tableau w trybie Online, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Tableau Online, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Tableau Online](#creating-a-tableau-online-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Tableau Online, które jest połączone z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w trybie Online Tableau aplikacji.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Tableau Online, wykonaj następujące czynności:**

1. W portalu Azure na **Tableau Online** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_samlbase.png)

3. Na **Tableau Online domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_url.png)
    
    a. W **adres URL logowania** tekstowym, wpisz adres URL: `https://sso.online.tableau.com`

    b. W **identyfikator** tekstowym, wpisz adres URL: `https://sso.online.tableau.com/public/sp/metadata?alias=<entityid> `

4. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik metadanych na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_general_400.png)

6. W oknie innej przeglądarki logowanie do aplikacji Tableau w trybie Online. Przejdź do **ustawienia** , a następnie **uwierzytelniania**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_09.png)
    
7. Aby włączyć SAML, w obszarze **typy uwierzytelniania** sekcji. Sprawdź **rejestracji jednokrotnej z SAML** wyboru.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_12.png)

8. Przewiń w dół do **Importowanie pliku metadanych do Tableau Online** sekcji.  Kliknij przycisk Przeglądaj i zaimportować plik metadanych, które zostały pobrane z usługi Azure AD. Następnie kliknij przycisk **Zastosuj**.
   
   ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_13.png)

9. W **odpowiada potwierdzenia** sekcję należy wstawić z odpowiednią nazwą potwierdzenia dostawcy tożsamości dla **adres e-mail**, **imię**, i **nazwisko**. Aby wyświetlić te informacje z usługi Azure AD: 
  
    a. W portalu Azure, przejdź **Tableau Online** strony integracja aplikacji.
    
    b. W obszarze atrybuty wybierz **"wyświetlić i edytować wszystkie atrybuty użytkowników"** pola wyboru. 
    
   ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/attributesection.png)
      
    c. Skopiuj wartość przestrzeni nazw dla tych atrybutów: givenname, poczty e-mail i nazwisko, korzystając z następujących czynności:

   ![Azure AD rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_10.png)
    
    d. Kliknij przycisk **user.givenname** wartości 
    
    e. Skopiuj wartości z **przestrzeni nazw** pola tekstowego.

   ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/attributesection2.png)

    f. Aby skopiować namesapce wartości poczty e-mail i nazwisko wykonaj te czynności.

    g. Przełącz się do aplikacji Tableau Online, a następnie ustaw **Tableau Online atrybuty** sekcji w następujący sposób:
     * Adres e-mail: **poczty** lub **userprincipalname**
     * Imię: **givenname**
     * Nazwisko: **nazwisko**
   
   ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_14.png)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-tableau-online-test-user"></a>Tworzenie użytkownika testowego Tableau Online

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta Tableau online.

1. Na **Tableau Online**, kliknij przycisk **ustawienia** , a następnie **uwierzytelniania** sekcji. Przewiń w dół do **Wybieranie: Użytkownicy** sekcji. Kliknij przycisk **dodawania użytkowników** , a następnie **wprowadź adresy E-mail**.
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_15.png)
2. Wybierz **Dodawanie użytkowników do uwierzytelniania rejestracji jednokrotnej (SSO)**. W **wprowadź adresy E-mail** Dodaj pole tekstowe britta.simon@contoso.com
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_11.png)
3. Kliknij przycisk **Utwórz**.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do Tableau Online.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Tableau Online, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Tableau Online**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-tableauonline-tutorial/tutorial_tableauonline_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

Celem tej sekcji służy do testowania konfigurację usługi Azure AD z logowania jednokrotnego za pomocą panelu dostępu.

Po kliknięciu kafelka Tableau Online w panelu dostępu użytkownik powinien uzyskać automatycznie zalogowane do aplikacji Tableau Online.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-tableauonline-tutorial/tutorial_general_203.png
