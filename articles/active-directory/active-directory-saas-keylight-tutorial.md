---
title: 'Samouczek: Integracji Azure Active Directory z LockPath Keylight | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i LockPath Keylight.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 5eb3aa008ccfa5711d8282f6d98df44239d236a4
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345915"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Samouczek: Integracji Azure Active Directory z LockPath Keylight

Z tego samouczka dowiesz się integrowanie LockPath Keylight z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD LockPath Keylight zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do LockPath Keylight
- Umożliwia użytkownikom automatycznie pobrać zalogowane do LockPath Keylight (logowanie jednokrotne) z konta usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z LockPath Keylight, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- LockPath Keylight jednokrotnego włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie LockPath Keylight z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-lockpath-keylight-from-the-gallery"></a>Dodawanie LockPath Keylight z galerii
Aby skonfigurować integrację usługi Azure AD LockPath Keylight, należy dodać LockPath Keylight z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać LockPath Keylight z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **LockPath Keylight**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_search.png)

5. W panelu wyników wybierz **LockPath Keylight**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i test usługi Azure AD rejestracji jednokrotnej z LockPath Keylight oparte na użytkownika testowego o nazwie "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w LockPath Keylight jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w LockPath Keylight musi się.

W LockPath Keylight, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z LockPath Keylight, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego LockPath Keylight](#creating-a-lockpath-keylight-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta LockPath Keylight, połączonej z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji LockPath Keylight.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z LockPath Keylight, wykonaj następujące czynności:**

1. W portalu Azure na **LockPath Keylight** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_samlbase.png)

3. Na **LockPath Keylight domeny i adres URL** sekcji, wykonaj następujące kroki::

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.keylightgrc.com/`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.keylightgrc.com`

    c. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<company name>.keylightgrc.com/Login.aspx`
    
    > [!NOTE] 
    > Wartości te nie są prawdziwe. Rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Keylight LockPath](https://www.lockpath.com/contact/) uzyskać te wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Raw)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/tutorial_general_400.png)
    
6. Na **LockPath Keylight konfiguracji** kliknij **skonfigurować LockPath Keylight** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adresu URL i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_configure.png) 

7. Aby włączyć logowanie Jednokrotne w LockPath Keylight, wykonaj następujące czynności:
   
    a. Logowanie do konta LockPath Keylight jako administrator.
    
    b. W menu u góry kliknij **osoby**i wybierz **Keylight Instalatora**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/401.png) 

    c. W widoku drzewa po lewej stronie, kliknij przycisk **SAML**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/402.png) 

    d. Na **ustawienia SAML** okna dialogowego, kliknij przycisk **Edytuj**.
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/404.png) 

8. Na **edytowanie ustawień SAML** okna dialogowego strony, należy wykonać następujące czynności:
   
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/405.png) 
   
    a. Ustaw **uwierzytelnianie SAML** do **Active**.

    b. Wklej **SAML pojedynczy znak na adres URL usługi** wartość, która została skopiowana z portalu Azure do **adresu URL logowania do dostawcy tożsamości** pola tekstowego.

    c. Wklej **pojedynczy adres URL usługi Sign-Out** wartość, która została skopiowana z portalu Azure do **adres URL wylogowania dostawcy tożsamości** pola tekstowego.

    d. Kliknij przycisk **wybierz plik** wybierz pobranego certyfikatu LockPath Keylight, a następnie kliknij przycisk **Otwórz** można przekazać certyfikatu.

    e. Ustaw **lokalizacji identyfikator użytkownika SAML** do **elementu NameIdentifier instrukcji podmiotu**.
    
    f. Podaj **dostawcy usług Keylight** przy użyciu następującego wzorca: **https://&lt;NazwaFirmy&gt;. keylightgrc.com**.
    
    g. Ustaw **automatycznego udostępniania użytkownikom** do **Active**.

    h. Ustaw **typ konta Auto-provision** do **pełnej**.

    i. Ustaw **roli zabezpieczeń Auto-provision**, wybierz pozycję **użytkowników standardowych z SAML**.
    
    j. Ustaw **konfiguracji zabezpieczeń Auto-provision**, wybierz pozycję **standardowej konfiguracji użytkownika**.
     
    k. W **atrybut poczty E-mail** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    l. W **atrybutu imię** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.
    
    m. W **ostatniego atrybutu name** pole tekstowe, typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.
    
    n. Kliknij pozycję **Zapisz**.

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-keylight-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-a-lockpath-keylight-test-user"></a>Tworzenie użytkownika testowego LockPath Keylight

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w LockPath Keylight. LockPath Keylight obsługę w czasie, który jest domyślnie włączona.

Nie ma elementu akcji można w tej sekcji. Nowy użytkownik jest tworzony podczas uzyskiwania dostępu do LockPath Keylight, jeśli użytkownik nie istnieje. 

>[!NOTE]
>Jeśli trzeba ręcznie utworzyć użytkownika, należy skontaktować się [zespołem pomocy technicznej klienta Keylight LockPath](https://www.lockpath.com/contact/). 

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu LockPath Keylight.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta LockPath Keylight, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **LockPath Keylight**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-keylight-tutorial/tutorial_keylight_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka LockPath Keylight w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji LockPath Keylight. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-keylight-tutorial/tutorial_general_203.png

