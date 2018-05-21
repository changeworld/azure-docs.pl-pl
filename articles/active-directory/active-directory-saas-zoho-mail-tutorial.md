---
title: 'Samouczek: Integracji Azure Active Directory z Zoho | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9874e1f3-ade5-42e7-a700-e08b3731236a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: d332dd65d660106a9f296ea57f89379f07ced027
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
---
# <a name="tutorial-azure-active-directory-integration-with-zoho"></a>Samouczek: Integracji Azure Active Directory z Zoho

Z tego samouczka dowiesz się integrowanie Zoho z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Zoho zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Zoho.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Zoho (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Zoho, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Zoho logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Zoho z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-zoho-from-the-gallery"></a>Dodawanie Zoho z galerii
Aby skonfigurować integrację usługi Azure AD Zoho, należy dodać Zoho z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Zoho z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Zoho**, wybierz pozycję **Zoho** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Zoho na liście wyników](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Zoho w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Zoho jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Zoho musi się.

W Zoho, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Zoho, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Zoho](#create-a-zoho-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Zoho połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Zoho.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Zoho, wykonaj następujące czynności:**

1. W portalu Azure na **Zoho** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_samlbase.png)

3. Na **Zoho domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny Zoho pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.zohomail.com`

    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Zoho](https://www.zoho.com/mail/contact.html) aby zyskać tę wartość. 
 
4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Zoho** , kliknij przycisk **skonfigurować Zoho** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, Zmień adres URL hasła i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja Zoho](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny firmy poczty Zoho jako administrator.

8. Przejdź do **Panelu sterowania**.
   
    ![Panel sterowania](./media/active-directory-saas-zoho-mail-tutorial/ic789607.png "Panel sterowania")

9. Kliknij przycisk **uwierzytelnianie SAML** kartę.
   
    ![Uwierzytelnianie SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789608.png "uwierzytelnianie SAML")

10. W **Szczegóły okna uwierzytelnianie SAML** sekcji, wykonaj następujące czynności:
   
    ![Szczegóły okna uwierzytelnianie SAML](./media/active-directory-saas-zoho-mail-tutorial/ic789609.png "Szczegóły okna uwierzytelnianie SAML")
   
    a. W **adres URL logowania** pole tekstowe, Wklej **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.
   
    b. W **adresu URL wylogowania** pole tekstowe, Wklej **Sign-Out URL** którego została skopiowana z portalu Azure.
   
    c. W **Zmień adres URL hasła** pole tekstowe, Wklej **Zmień adres URL hasła** którego została skopiowana z portalu Azure.
       
    d. Otwórz base-64 zakodowany certyfikat pobrany z portalu Azure w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **PublicKey** pola tekstowego.
   
    e. Jako **algorytm**, wybierz pozycję **RSA**.
   
    f. Kliknij przycisk **OK**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-zoho-mail-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-zoho-test-user"></a>Tworzenie użytkownika testowego Zoho

Aby włączyć użytkowników usługi Azure AD zalogować się do Zoho poczty, muszą mieć przydzielone do Zoho poczty. W przypadku poczty Zoho Inicjowanie obsługi to zadanie ręczne.

> [!NOTE]
> Możesz użyć innych Zoho poczty użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez Zoho poczty do kont użytkowników usługi AAD.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konta użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Twojego **poczty Zoho** witryny firmy jako administrator.

2. Przejdź do **Panel sterowania \> poczty & Docs**.

3. Przejdź do **szczegóły użytkownika \> dodać użytkownika**.
   
    ![Dodaj użytkownika](./media/active-directory-saas-zoho-mail-tutorial/ic789611.png "Dodaj użytkownika")

4. Na **dodawania użytkowników** okna dialogowego, wykonaj następujące czynności:
   
    ![Dodaj użytkownika](./media/active-directory-saas-zoho-mail-tutorial/ic789612.png "Dodaj użytkownika")
   
    a. W **imię** tekstowym, wpisz imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak **Simona**.

    c. W **identyfikator wiadomości E-mail** pole tekstowe, typ identyfikatora e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    d. W **hasło** pole tekstowe, wprowadź hasło użytkownika.
   
    e. Kliknij przycisk **OK**.  
      
    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory zostanie wysłana wiadomość e-mail z łączem do potwierdzenia konta, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Zoho.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta Zoho, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Zoho**.

    ![Łącze Zoho na liście aplikacji](./media/active-directory-saas-zoho-mail-tutorial/tutorial_zoho_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Zoho w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Zoho.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-zoho-mail-tutorial/tutorial_general_203.png

