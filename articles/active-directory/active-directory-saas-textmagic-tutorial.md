---
title: 'Samouczek: Integracji Azure Active Directory z TextMagic | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i TextMagic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3e5b49d2-7096-46bc-a9ce-90e09177ba28
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: jeedes
ms.openlocfilehash: 61a3d4f99f2b86c157d166b57275500b86be6e17
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351906"
---
# <a name="tutorial-azure-active-directory-integration-with-textmagic"></a>Samouczek: Integracji Azure Active Directory z TextMagic

Z tego samouczka dowiesz się integrowanie TextMagic z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD TextMagic zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do TextMagic.
- Umożliwia użytkownikom automatycznie pobrać zalogowane do TextMagic (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD.
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure.

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z TextMagic, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- TextMagic logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie TextMagic z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-textmagic-from-the-gallery"></a>Dodawanie TextMagic z galerii
Aby skonfigurować integrację usługi Azure AD TextMagic, należy dodać TextMagic z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać TextMagic z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Blok aplikacje przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **TextMagic**, wybierz pozycję **TextMagic** z panelu wyników kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![TextMagic na liście wyników](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TextMagic na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w TextMagic jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w TextMagic musi się.

W TextMagic, należy przypisać wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łącza.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z TextMagic, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego TextMagic](#create-a-textmagic-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta TextMagic połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji TextMagic.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z TextMagic, wykonaj następujące czynności:**

1. W portalu Azure na **TextMagic** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Okno dialogowe rejestracji jednokrotnej](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_samlbase.png)

3. Na **TextMagic domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny TextMagic pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_url.png)

    W **identyfikator** tekstowym, wpisz adres URL: `https://my.textmagic.com/saml/metadata`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny TextMagic pojedynczy informacje logowania jednokrotnego](./media/active-directory-saas-textmagic-tutorial/url1.png)

    W **adres URL logowania** tekstowym, wpisz adres URL: `https://my.textmagic.com/login/sso`


5. Na **certyfikat podpisywania SAML** kliknij **certyfikatu (Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Łącze pobierania certyfikatu](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_certificate.png) 

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/active-directory-saas-textmagic-tutorial/tutorial_general_400.png)
    
7. Na **konfiguracji TextMagic** , kliknij przycisk **skonfigurować TextMagic** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **Sign-Out adres URL, identyfikator jednostki SAML i SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfiguracja TextMagic](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_configure.png) 

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy TextMagic.

9. Wybierz **ustawienia konta** w obszarze nazwy użytkownika.

    ![Konfiguracja TextMagic](./media/active-directory-saas-textmagic-tutorial/config1.png) 
10. Kliknij kartę **"pojedynczego logowania jednokrotnego (SSO)"** i wypełnij następujące pola:  
    
    ![Konfiguracja TextMagic](./media/active-directory-saas-textmagic-tutorial/config2.png)

    a. W **dostawcy tożsamości identyfikator jednostki:** pole tekstowe, Wklej wartość **identyfikator jednostki SAML**, które zostały skopiowane z portalu Azure.

    b. W **dostawcy tożsamości adresu URL logowania jednokrotnego:** pole tekstowe, Wklej wartość **pojedynczy znak na adres URL usługi**, które zostały skopiowane z portalu Azure.

    c. W **dostawcy tożsamości adres URL usługi:** pole tekstowe, Wklej wartość **Sign-Out adres URL**, które zostały skopiowane z portalu Azure.

    d. Otwórz z **certyfikatu algorytmem base-64** w Notatniku pobrany z portalu Azure, należy skopiować zawartość go do Schowka, a następnie wklej go do **certyfikatu publicznego x509:** pola tekstowego.

    e. Kliknij pozycję **Zapisz**.


> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W portalu Azure, w okienku po lewej stronie kliknij **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/active-directory-saas-textmagic-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/active-directory-saas-textmagic-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/active-directory-saas-textmagic-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/active-directory-saas-textmagic-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwy użytkownika** wpisz adres e-mail użytkownika Simona Britta.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zanotuj wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-textmagic-test-user"></a>Tworzenie użytkownika testowego TextMagic

Aplikacja obsługuje tylko w czasie Inicjowanie obsługi użytkowników i uwierzytelnianie użytkowników zostaną utworzone w aplikacji automatycznie. Musisz podać informacje raz na pierwsze logowanie do aktywowania konta podrzędnego w systemie.
Nie ma elementu akcji można w tej sekcji.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu TextMagic.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Simona Britta TextMagic, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **TextMagic**.

    ![Łącze TextMagic na liście aplikacji](./media/active-directory-saas-textmagic-tutorial/tutorial_textmagic_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka TextMagic w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji TextMagic.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-textmagic-tutorial/tutorial_general_203.png

