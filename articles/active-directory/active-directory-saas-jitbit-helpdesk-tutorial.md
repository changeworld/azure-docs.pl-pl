---
title: 'Samouczek: Integracji Azure Active Directory z pomocą techniczną Jitbit | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Jitbit pracy działu pomocy technicznej.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 15ce27d4-0621-4103-8a34-e72c98d72ec3
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2017
ms.author: jeedes
ms.openlocfilehash: c8e387bb98ad2e23c667ba058ff8ab5dbfedffbf
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34343933"
---
# <a name="tutorial-azure-active-directory-integration-with-jitbit-helpdesk"></a>Samouczek: Integracji Azure Active Directory z pomocą techniczną Jitbit

Z tego samouczka dowiesz integrowanie pomocy technicznej Jitbit z usługi Azure Active Directory (Azure AD).

Integrowanie pomocy technicznej Jitbit z usługą Azure AD zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do działu pomocy technicznej Jitbit
- Umożliwia użytkownikom automatycznie pobrać zalogowane do działu pomocy technicznej Jitbit (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pomocą techniczną Jitbit, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pomoc techniczna Jitbit logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie pomocy technicznej Jitbit z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-jitbit-helpdesk-from-the-gallery"></a>Dodawanie pomocy technicznej Jitbit z galerii
Aby skonfigurować integrację usługi Azure AD Jitbit pracy działu pomocy technicznej, należy dodać techniczną Jitbit z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać techniczną Jitbit z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **techniczną Jitbit**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_search.png)

5. W panelu wyników wybierz **techniczną Jitbit**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z pomocą techniczną Jitbit w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Jitbit pomoc techniczna jest dla użytkownika, w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i danemu użytkownikowi w Jitbit pomoc techniczna musi określone.

Pomoc techniczna Jitbit, przypisywanie wartości **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z pomocą techniczną Jitbit, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego techniczną Jitbit](#creating-a-jitbit-helpdesk-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Jitbit pomocy, w którym jest połączony z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Jitbit pracy działu pomocy technicznej.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z pomocą techniczną Jitbit, wykonaj następujące czynności:**

1. W portalu Azure na **techniczną Jitbit** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_samlbase.png)

3. Na **Jitbit pomoc techniczna domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: 
    | |     
    | ----------------------------------------|
    | `https://<hostname>/helpdesk/User/Login`|
    | `https://<tenant-name>.Jitbit.com`|
    | |
    
    > [!NOTE] 
    > Ta wartość nie jest prawdziwe. Zaktualizuj tę wartość przy rzeczywisty adres URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta techniczną Jitbit](https://www.jitbit.com/support/) aby zyskać tę wartość. 
    
    b.  W **identyfikator** tekstowym, wpisz adres URL jako poniżej: `https://www.jitbit.com/web-helpdesk/`

    
 


4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_400.png)

6. Na **konfiguracji pomoc techniczna Jitbit** , kliknij przycisk **skonfigurować Pomoc techniczna Jitbit** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_configure.png) 

7. W oknie przeglądarki innej witryny sieci web Zaloguj się do witryny Jitbit pracy działu pomocy technicznej firmy jako administrator.

8. Na pasku narzędzi u góry kliknij **administracji**.
   
    ![Administracja](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "administracji")

9. Kliknij przycisk **ustawienia ogólne**.
   
    ![Użytkownicy, firm i uprawnienia](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777680.png "użytkowników, firmach i uprawnień")

10. W **ustawienia uwierzytelniania** konfiguracji sekcji, wykonaj następujące czynności:
   
    ![Ustawienia uwierzytelniania](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777683.png "ustawienia uwierzytelniania")
    
    a. Wybierz **Włącz SAML 2.0 pojedynczego logowania**, aby zalogować się przy użyciu pojedynczego logowania jednokrotnego (SSO), z **OneLogin**.

    b. W **adres URL punktu końcowego** pole tekstowe, Wklej wartość **SAML pojedynczy znak na adres URL usługi** którego została skopiowana z portalu Azure.

    c. Otwórz z **base-64** zakodowane certyfikatów w programie Notatnik, skopiuj zawartość go do Schowka, a następnie wklej go do **certyfikatu X.509** pole tekstowe

    d. Kliknij przycisk **zapisać zmiany**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-jitbit-helpdesk-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, nazwa typu jako **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-jitbit-helpdesk-test-user"></a>Tworzenie użytkownika testowego Jitbit pomoc techniczna

Aby włączyć użytkowników usługi Azure AD zalogować się do pomocy technicznej Jitbit, muszą mieć przydzielone do Jitbit pracy działu pomocy technicznej.  W przypadku Jitbit pomocy technicznej Inicjowanie obsługi to zadanie ręczne.

**Aby udostępnić konta użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Twojego **techniczną Jitbit** dzierżawy.

2. W menu u góry kliknij **administracji**.
   
    ![Administracja](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777681.png "administracji")

3. Kliknij przycisk **użytkowników, firmach i uprawnienia**.
   
    ![Użytkownicy, firm i uprawnienia](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777682.png "użytkowników, firmach i uprawnień")

4. Kliknij przycisk **Dodaj użytkownika**.
   
    ![Dodaj użytkownika](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777685.png "Dodaj użytkownika")
   
5. W sekcji Tworzenie wpisz dane konto usługi Azure AD, którą chcesz udostępnić w następujący sposób:

    ![Utwórz](./media/active-directory-saas-jitbit-helpdesk-tutorial/ic777686.png "tworzenie")
   
   a. W **Username** pole tekstowe, typ **BrittaSimon**, nazwę użytkownika w portalu Azure.

   b. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak **BrittaSimon@contoso.com**.

   c. W **imię** tekstowym, wpisz imię użytkownika, takich jak **Britta**.

   d. W **nazwisko** tekstowym, wpisz nazwisko użytkownika, takich jak **Simona**.
   
   e. Kliknij przycisk **Utwórz**.

>[!NOTE]
>Inne narzędzia do tworzenia konta użytkownika Jitbit pracy działu pomocy technicznej lub interfejsów API dostarczonych przez Pomoc techniczna Jitbit służy do obsługi administracyjnej kont użytkowników usługi Azure AD.
> 
        

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji musisz włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu do działu pomocy technicznej Jitbit.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta do działu pomocy technicznej Jitbit, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **techniczną Jitbit**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_jitbit-helpdesk_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Jitbit pomoc techniczna w panelu dostępu, należy pobrać strony logowania aplikacji Jitbit pracy działu pomocy technicznej.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jitbit-helpdesk-tutorial/tutorial_general_203.png

