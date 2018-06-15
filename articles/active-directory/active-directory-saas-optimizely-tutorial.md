---
title: 'Samouczek: Integracji Azure Active Directory z Optimizely | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Optimizely.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 28ef03e1-9aad-4301-af97-d94e853edc74
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 995b440cbf9dc93ec01fe0b86c69ea09cd4714ec
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/20/2018
ms.locfileid: "34346047"
---
# <a name="tutorial-azure-active-directory-integration-with-optimizely"></a>Samouczek: Integracji Azure Active Directory z Optimizely

Z tego samouczka dowiesz się integrowanie Optimizely z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Optimizely zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Optimizely
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Optimizely (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Optimizely, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Optimizely logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Optimizely z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-optimizely-from-the-gallery"></a>Dodawanie Optimizely z galerii
Aby skonfigurować integrację usługi Azure AD Optimizely, należy dodać Optimizely z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Optimizely z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **nowej aplikacji** przycisk w górnej części okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **Optimizely**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_search.png)

5. W panelu wyników wybierz **Optimizely**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Optimizely na podstawie użytkownika testowego, nazywany "Britta Simona".

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Optimizely jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Optimizely musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Optimizely.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Optimizely, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Optimizely](#creating-an-optimizely-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Optimizely połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Testowanie rejestracji jednokrotnej](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Optimizely.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Optimizely, wykonaj następujące czynności:**

1. W portalu Azure na **Optimizely** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.
 
    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_samlbase.png)

3. Na **Optimizely domeny i adres URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_url.png)

    a. W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://app.optimizely.net/<instance name>`

    b. W **identyfikator** tekstowym, wpisz adres URL, używając następującego wzorca:  `urn:auth0:optimizely:contoso`

    > [!NOTE] 
    > Wartości te nie są rzeczywistych. Rzeczywisty adres URL logowania i identyfikator, który znajduje się w dalszej części samouczka spowoduje zaktualizowanie wartości. 

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na tym komputerze.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_certificate.png) 

5. Kliknij przycisk **zapisać** przycisku.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Optimizely** , kliknij przycisk **skonfigurować Optimizely** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **SAML pojedynczy znak na adres URL usługi** z **sekcji krótkimi opisami.**

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_configure.png) 

7. Aby skonfigurować logowanie jednokrotne w **Optimizely** po stronie, skontaktuj się z menedżerem ds. klientów Optimizely i zapewnić pobrany **certyfikatu (Base64)**, i **SAML pojedynczy znak na adres URL usługi**. 

8. W odpowiedzi na adres e-mail Optimizely pozwala na adres URL logowania (SSO zainicjował SP) i wartości Identyfikator usługi dostawcy jednostki.

    a. Kopia **inicjowane SP adres URL logowania jednokrotnego** o Optimizely i Wklej do **na adres URL logowania** textbox w **Optimizely domeny i adres URL** sekcji z portalu Azure 

    b. Kopiuj **identyfikator jednostki dostawcy usługi** o Optimizely i Wklej do **identyfikator** textbox w **Optimizely domeny i adres URL** sekcji z portalu Azure 

9. W oknie innej przeglądarki logowanie do aplikacji Optimizely.

10. Kliknij możesz nazwa konta w górnym prawym narożniku, a następnie **ustawienia konta**.
   
    ![Azure AD rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_09.png)

11. Zaznacz pole wyboru na karcie konto **włączenia logowania jednokrotnego** w obszarze rejestracji jednokrotnej w **omówienie** sekcji.
   
    ![Azure AD rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_10.png)
    
12. Kliknij pozycję **Zapisz**

> [!TIP]
> Teraz możesz przeczytać zwięzły wersji tych instrukcji wewnątrz [portalu Azure](https://portal.azure.com), podczas konfigurowania aplikacji!  Po dodaniu tej aplikacji z **usługi Active Directory > aplikacje dla przedsiębiorstw** po prostu kliknij **rejestracji jednokrotnej** karcie i dostęp do dokumentacji osadzonych za pomocą **konfiguracji** sekcji u dołu. Więcej o funkcji dokumentacji osadzonego w tym miejscu: [dokumentacji osadzonych usługi Azure AD]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkowników usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okna dialogowego, kliknij przycisk **Dodaj** górnej części okna dialogowego.
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:
 
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_04.png) 

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z Simona Britta.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="creating-an-optimizely-test-user"></a>Tworzenie użytkownika testowego Optimizely

W tej sekcji należy utworzyć użytkownika o nazwie Simona Britta w Optimizely.

1. Na stronie głównej wybierz **współpracownicy** kartę.

2. Aby dodać nowy współpracownika do projektu, kliknij przycisk **nowego współpracownika**.
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_10.png)

3. Wprowadź adres e-mail i przydzielić mu rolę. Kliknij przycisk **zaprosić**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/active-directory-saas-optimizely-tutorial/create_aaduser_11.png)

4. Te otrzymają zaproszenie poczty e-mail. Przy użyciu adresu e-mail, muszą oni zalogować się do Optimizely.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Optimizely.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Optimizely, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Optimizely**.

    ![Konfigurowanie rejestracji jednokrotnej](./media/active-directory-saas-optimizely-tutorial/tutorial_optimizely_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie rejestracji jednokrotnej

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.

Po kliknięciu kafelka Optimizely w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Optimizely. 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](active-directory-saas-tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-optimizely-tutorial/tutorial_general_203.png

