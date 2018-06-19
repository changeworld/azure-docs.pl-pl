---
title: 'Samouczek: Integracji Azure Active Directory z Pingboard | Dokumentacja firmy Microsoft'
description: Informacje o sposobie konfigurowania rejestracji jednokrotnej między usługą Azure Active Directory i Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: b465816821a491aa14df4e7224c23d805d4afc68
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2018
ms.locfileid: "35943436"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Samouczek: Integracji Azure Active Directory z Pingboard

Z tego samouczka dowiesz się integrowanie Pingboard z usługi Azure Active Directory (Azure AD).

Integracja z usługą Azure AD Pingboard zapewnia następujące korzyści:

- Można kontrolować w usłudze Azure AD, który ma dostęp do Pingboard
- Umożliwia użytkownikom automatycznie pobrać zalogowane do Pingboard (logowanie jednokrotne) przy użyciu ich kont usługi Azure AD
- Możesz zarządzać kont w jednej centralnej lokalizacji - portalu Azure

Jeśli chcesz dowiedzieć się więcej informacji o integracji aplikacji SaaS w usłudze Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Pingboard, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Pingboard logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, jeśli jest to konieczne.
- Jeśli nie masz środowisko wersji próbnej usługi Azure AD, możesz [uzyskać miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W tym samouczku można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych elementów:

1. Dodawanie Pingboard z galerii
2. Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne

## <a name="adding-pingboard-from-the-gallery"></a>Dodawanie Pingboard z galerii
Aby skonfigurować integrację usługi Azure AD Pingboard, należy dodać Pingboard z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać Pingboard z galerii, wykonaj następujące czynności:**

1. W  **[portalu Azure](https://portal.azure.com)**, na panelu nawigacyjnym po lewej stronie kliknij **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje dla przedsiębiorstw][2]

3. Kliknij przycisk **Dodaj** przycisk w górnej części okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Pingboard**, wybierz pozycję **Pingboard** z panelu wyników, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Pingboard na liście wyników](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Pingboard w oparciu o nazwie "Britta Simona" użytkownika testowego.

Dla rejestracji jednokrotnej do pracy usługi Azure AD musi wiedzieć, użytkownik odpowiednika w Pingboard jest dla użytkownika, w usłudze Azure AD. Innymi słowy link relację między użytkownikiem usługi Azure AD i danemu użytkownikowi w Pingboard musi się.

Ta relacja łącza zostanie nawiązane, przypisując wartość **nazwy użytkownika** w usłudze Azure AD jako wartość **Username** w Pingboard.

Aby skonfigurować i przetestować usługi Azure AD rejestracji jednokrotnej z Pingboard, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD rejestracji jednokrotnej](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD rejestracji jednokrotnej z Simona Britta.
3. **[Tworzenie użytkownika testowego Pingboard](#create-a-pingboard-test-user)**  — w celu zapewnienia odpowiednikiem Simona Britta Pingboard połączonego z usługi Azure AD reprezentację użytkownika.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — aby umożliwić Simona Britta do użycia usługi Azure AD rejestracji jednokrotnej.
5. **[Test rejestracji jednokrotnej](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD rejestracji jednokrotnej

W tej sekcji można włączyć usługi Azure AD rejestracji jednokrotnej w portalu Azure i skonfigurować logowanie jednokrotne w aplikacji Pingboard.

**Aby skonfigurować usługi Azure AD rejestracji jednokrotnej z Pingboard, wykonaj następujące czynności:**

1. W portalu Azure na **Pingboard** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2.  Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **na języku SAML logowania jednokrotnego** Aby włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

3. Na **Pingboard domeny i adres URL** sekcji, wykonaj następujące kroki, aby skonfigurować aplikację w **IDP** inicjowane tryb:

    ![Adresy URL i domeny Pingboard pojedynczy informacje logowania jednokrotnego IDP](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. W **identyfikator** tekstowym, wpisz wartość, jak: `http://app.pingboard.com/sp`

    b. W **adres URL odpowiedzi** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<entity-id>.pingboard.com/auth/saml/consume`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** inicjowane tryb:

    ![Adresy URL i domeny Pingboard pojedynczy informacje logowania jednokrotnego SP](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     W **adres URL logowania** tekstowym, wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Należy pamiętać, że te wartości nie są prawdziwe. Rzeczywisty adres URL odpowiedzi i adres URL logowania, należy zaktualizować te wartości. Skontaktuj się z [zespołem pomocy technicznej klienta Pingboard](https://support.pingboard.com/) uzyskać te wartości.

5. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Pingboard xml metadanych](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

6. Kliknij przycisk **zapisać** przycisku.

    ![Skonfiguruj przycisk pojedynczego logowania jednokrotnego Zapisz](./media/pingboard-tutorial/tutorial_general_400.png)

7. Aby skonfigurować logowanie Jednokrotne po stronie Pingboard, Otwórz nowe okno przeglądarki i zaloguj się do swojego konta Pingboard. Musisz być administratorem Pingboard, aby skonfigurować funkcji logowania jednokrotnego.

8. Wybierz z górnego menu,, **aplikacji > integracji**

    ![Konfigurowanie rejestracji jednokrotnej](./media/pingboard-tutorial/Pingboard_integration.png)

9. Na **integracji** strony, Znajdź **"Azure Active Directory"** Kafelek, a następnie kliknij go.

    ![Jednym Pingboard logowania jednokrotnego integracji](./media/pingboard-tutorial/Pingboard_aad.png)

10. W modalne, kliknij poniżej **"Konfiguruj"**

    ![Przycisk Konfiguracja Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

11. Na następnej stronie można zauważyć "Integracja z usługą Azure logowania jednokrotnego włączenie". Otwórz pobrany plik XML metadanych w programie Notatnik i Wklej zawartość **metadanych IDP**.

    ![Ekranie konfiguracji rejestracji Jednokrotnej Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

12. Plik jest zweryfikowany, a jeśli wszystko jest poprawny, logowania jednokrotnego zostanie ono włączone.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w portalu Azure o nazwie Simona Britta.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **portalu Azure**, w lewym okienku nawigacji, kliknij polecenie **usługi Azure Active Directory** ikony.

    ![Przycisk usługi Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "Wszyscy użytkownicy" łącza](./media/pingboard-tutorial/create_aaduser_02.png)

3. W górnej części okna dialogowego, kliknij przycisk **Dodaj** otworzyć **użytkownika** okna dialogowego.

    ![Przycisk dodawania](./media/pingboard-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, należy wykonać następujące czynności:

    ![Okno dialogowe użytkownika](./media/pingboard-tutorial/create_aaduser_04.png)

    a. W **nazwa** pole tekstowe, typ **BrittaSimon**.

    b. W **nazwy użytkownika** pole tekstowe, typ **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-pingboard-test-user"></a>Tworzenie użytkownika testowego Pingboard

Celem tej sekcji jest utworzenie użytkownika o nazwie Simona Britta w Pingboard. Pingboard obsługę automatycznego użytkownika, który jest domyślnie włączone. Więcej informacji można znaleźć [tutaj](pingboard-provisioning-tutorial.md) na temat konfigurowania użytkowników automatycznego inicjowania obsługi administracyjnej.

**Jeśli trzeba ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do witryny firmy Pingboard jako administrator.

2. Kliknij przycisk **"Dodaj pracownika"** znajdującego się na **katalogu** strony.

    ![Dodawanie pracownika](./media/pingboard-tutorial/create_testuser_add.png)

3. Na **"Dodaj pracownika"** okna dialogowego strony, należy wykonać następujące czynności:

    ![Zaproś inne osoby](./media/pingboard-tutorial/create_testuser_name.png)

    a. W **imię i nazwisko** tekstowym, wpisz pełną nazwę użytkownika, takich jak **Simona Britta**.

    b. W **E-mail** tekstowym, wpisz adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    c. W **stanowisko** tekstowym, wpisz nazwę zadania Simona Britta.

    d. W **lokalizacji** listy rozwijanej wybierz lokalizację Simona Britta.

    e. Kliknij pozycję **Add** (Dodaj).

4. Ekran potwierdzenia pojawia się, aby potwierdzić dodanie użytkownika.

    ![Upewnij się](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzymuje wiadomość e-mail i następuje łącze, aby potwierdzić swoje konto, zanim staje się aktywny.

### <a name="assign-the-azure-ad-test-user"></a>Przypisz użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Simona Britta do używania Azure logowania jednokrotnego za udzielanie dostępu Pingboard.

![Przypisz użytkownika][200] 

**Aby przypisać Simona Britta Pingboard, wykonaj następujące czynności:**

1. W portalu Azure Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji zaznacz **Pingboard**.

    ![Łącze Pingboard na liście aplikacji](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Łącze "Użytkownicy i grupy"][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![W okienku Dodaj przydziału][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Simona Britta** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdującego się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdującego się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfiguracji usługi Azure AD pojedynczego logowania za pomocą panelu dostępu.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../active-directory-saas-access-panel-introduction.md).

Po kliknięciu kafelka Pingboard w panelu dostępu użytkownik powinien pobrać automatycznie zalogowane do aplikacji Pingboard.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobów integracji aplikacji SaaS przy użyciu usługi Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Skonfiguruj Inicjowanie obsługi użytkowników](pingboard-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/pingboard-tutorial/tutorial_general_01.png
[2]: ./media/pingboard-tutorial/tutorial_general_02.png
[3]: ./media/pingboard-tutorial/tutorial_general_03.png
[4]: ./media/pingboard-tutorial/tutorial_general_04.png

[100]: ./media/pingboard-tutorial/tutorial_general_100.png

[200]: ./media/pingboard-tutorial/tutorial_general_200.png
[201]: ./media/pingboard-tutorial/tutorial_general_201.png
[202]: ./media/pingboard-tutorial/tutorial_general_202.png
[203]: ./media/pingboard-tutorial/tutorial_general_203.png