---
title: 'Samouczek: Integracji Azure Active Directory z UserVoice | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i usługi UserVoice.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 684a405b-8932-46f6-b43a-4d97a42b6b87
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/21/2017
ms.author: jeedes
ms.openlocfilehash: 41466c73dbd8dec88a1e1e06a907d9686c49efbe
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351226"
---
# <a name="tutorial-azure-active-directory-integration-with-uservoice"></a>Samouczek: Integracji Azure Active Directory z UserVoice

Z tego samouczka dowiesz się Integrowanie usługi UserVoice w usłudze Azure Active Directory (Azure AD).

Integrowanie UserVoice w usłudze Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do usługi UserVoice.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do środowiska UserVoice (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z UserVoice, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- UserVoice logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie UserVoice z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-uservoice-from-the-gallery"></a>Dodawanie UserVoice z galerii
Aby skonfigurować integrację usługi UserVoice w usłudze Azure Active Directory, należy dodać UserVoice z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać UserVoice z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **UserVoice**, wybierz pozycję **UserVoice** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![UserVoice na liście wyników](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z UserVoice w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w UserVoice jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w UserVoice musi się.

W UserVoice, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z UserVoice, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego UserVoice](#create-a-uservoice-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta UserVoice połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji UserVoice.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z UserVoice, wykonaj następujące czynności:**

1. W portalu Azure na **UserVoice** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_samlbase.png)

3. Na **UserVoice domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Adresy URL i domeny UserVoice pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<tenantname>.UserVoice.com`

    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty adres URL logowania i identyfikator, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta UserVoice](https://www.uservoice.com/) uzyskać te wartości.

4. Na **certyfikat podpisywania SAML** sekcji, skopiuj **odcisk PALCA** wartości certyfikatu.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-uservoice-tutorial/tutorial_general_400.png)

6. Na **konfiguracji UserVoice** , kliknij przycisk **skonfigurować UserVoice** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja usługi UserVoice](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_configure.png) 

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy UserVoice.

8. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie wybierz **portalu sieci Web** z menu.
   
    ![W sekcji Ustawienia na stronie aplikacji](./media/active-directory-saas-uservoice-tutorial/ic777519.png "ustawienia")

9. Na **portalu sieci Web** karcie **uwierzytelnianie użytkownika** , kliknij przycisk **Edytuj** otworzyć **Edytuj uwierzytelnianie użytkownika** strony okna dialogowego.
   
    ![Portal sieci Web kartę](./media/active-directory-saas-uservoice-tutorial/ic777520.png "portalu sieci Web")

10. Na **Edytuj uwierzytelnianie użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
   
    ![Edytuj uwierzytelnianie użytkownika](./media/active-directory-saas-uservoice-tutorial/ic777521.png "Edytuj uwierzytelnianie użytkownika")
   
    a. Kliknij przycisk **logowanie jednokrotne (SSO)**.
 
    b. Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure do **logowania jednokrotnego zdalnego logowania** pola tekstowego.

    c. Wklej **Sign-Out URL** wartość, która została skopiowana z portalu Azure do **Sign-Out zdalnego logowania jednokrotnego textbox**.
 
    d. Wklej **odcisk palca** wartość, która została skopiowana z portalu Azure do **bieżącego odcisk palca certyfikatu SHA1** pola tekstowego.
    
    e. Kliknij przycisk **Zapisz ustawienia uwierzytelniania**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-uservoice-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-uservoice-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-uservoice-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-uservoice-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-uservoice-test-user"></a>Tworzenie użytkownika testowego UserVoice

Aby umożliwić użytkownikom usługi Azure AD do logowania się w usłudze UserVoice, musi być przygotowana do UserVoice. W przypadku UserVoice Inicjowanie obsługi to zadanie ręczne.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Aby udostępnić konta użytkownika, wykonaj następujące czynności:
1. Zaloguj się do Twojego **UserVoice** dzierżawy.

2. Przejdź do **ustawienia**.
   
    ![Ustawienia](./media/active-directory-saas-uservoice-tutorial/ic777811.png "ustawienia")

3. Kliknij przycisk **ogólne**.

4. Kliknij przycisk **agentów i uprawnienia**.
   
    ![Agenci i uprawnienia](./media/active-directory-saas-uservoice-tutorial/ic777812.png "agentów i uprawnień")

5. Kliknij przycisk **dodać Administratorzy**.
   
    ![Dodaj administratorów](./media/active-directory-saas-uservoice-tutorial/ic777813.png "dodać administratorów")

6. Na **zaprosić Administratorzy** okna dialogowego, wykonaj następujące czynności:
   
    ![Zaproś Administratorzy](./media/active-directory-saas-uservoice-tutorial/ic777814.png "zaprosić Administratorzy")
   
    a. W polu tekstowym wiadomości E-mail, wpisz adres e-mail konta, aby udostępnić, a następnie kliknij przycisk **Dodaj**.
   
    b. Kliknij przycisk **zaprosić**.

> [!NOTE]
> Możesz użyć innych UserVoice użytkownika konta tworzenia narzędzi lub interfejsów API dostarczonych przez UserVoice do kont użytkowników usługi AAD.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do środowiska UserVoice.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta UserVoice, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **UserVoice**.

    ![Łącze UserVoice na liście aplikacji](./media/active-directory-saas-uservoice-tutorial/tutorial_uservoice_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka UserVoice w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji usługi UserVoice.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-uservoice-tutorial/tutorial_general_203.png

