---
title: 'Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem AppDynamics | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i AppDynamics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 25fd1df0-411c-4f55-8be3-4273b543100f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/09/2018
ms.author: jeedes
ms.openlocfilehash: 3600e83d18f8cabd03c46af2ef47445c588cbdb5
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2018
ms.locfileid: "38548286"
---
# <a name="tutorial-azure-active-directory-integration-with-appdynamics"></a>Samouczek: Integracja usługi Azure Active Directory z oprogramowaniem AppDynamics

W tym samouczku dowiesz się, jak zintegrować AppDynamics w usłudze Azure Active Directory (Azure AD).

Integracja z oprogramowaniem AppDynamics z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do AppDynamics
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do AppDynamics (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z oprogramowaniem AppDynamics, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- AppDynamics logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym.
Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie AppDynamics z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-appdynamics-from-the-gallery"></a>Dodawanie AppDynamics z galerii
Aby skonfigurować integrację z oprogramowaniem AppDynamics w usłudze Azure AD, należy dodać AppDynamics z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać AppDynamics z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **AppDynamics**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/appdynamics-tutorial/tutorial_appdynamics_search.png)

5. W panelu wyników wybierz **AppDynamics**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/appdynamics-tutorial/tutorial_appdynamics_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z oprogramowaniem AppDynamics w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w AppDynamics dla użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w AppDynamics musi można ustanowić.

W AppDynamics, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z oprogramowaniem AppDynamics, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego AppDynamics](#creating-an-appdynamics-test-user)**  — aby odpowiednikiem Britta Simon w AppDynamics połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji AppDynamics.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z oprogramowaniem AppDynamics, wykonaj następujące czynności:**

1. W witrynie Azure portal na **AppDynamics** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/appdynamics-tutorial/tutorial_appdynamics_samlbase.png)

3. Na **AppDynamics domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/appdynamics-tutorial/tutorial_appdynamics_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.saas.appdynamics.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.saas.appdynamics.com/controller`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta AppDynamics](https://www.appdynamics.com/support/) do uzyskania tych wartości.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/appdynamics-tutorial/tutorial_appdynamics_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/appdynamics-tutorial/tutorial_general_400.png)

6. Na **konfiguracji AppDynamics** , kliknij przycisk **skonfigurować AppDynamics** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/appdynamics-tutorial/tutorial_appdynamics_configure.png)

7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy AppDynamics.

8. Na pasku narzędzi u góry kliknij **ustawienia**, a następnie kliknij przycisk **administracji**.

    ![Administracja](./media/appdynamics-tutorial/ic790216.png "administracji")

9. Kliknij przycisk **dostawcy uwierzytelniania** kartę.

    ![Dostawca uwierzytelniania](./media/appdynamics-tutorial/ic790224.png "dostawcy uwierzytelniania")

10. W **dostawcy uwierzytelniania** sekcji, wykonaj następujące czynności:

    ![Plik konfiguracji SAML](./media/appdynamics-tutorial/ic790225.png "plik konfiguracji SAML")

    a. Jako **dostawcy uwierzytelniania**, wybierz opcję **SAML**.

    b. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowanej w witrynie Azure portal.

    c. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    d. Otwórz swój certyfikat zakodowany base-64 w programie Notatnik, skopiuj jego zawartość do Schowka, a następnie wklej go do **certyfikatu** textbox

    e. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/appdynamics-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/appdynamics-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/appdynamics-tutorial/create_aaduser_03.png) 

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/appdynamics-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-an-appdynamics-test-user"></a>Tworzenie użytkownika testowego AppDynamics

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w AppDynamics. AppDynamics obsługę just-in-time, który jest domyślnie włączona. Brak elementu akcji dla Ciebie w tej sekcji. Nowy użytkownik jest tworzony podczas próby dostępu AppDynamics, jeśli go jeszcze nie istnieje.
>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej klienta AppDynamics](https://www.appdynamics.com/support/).

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do AppDynamics.

![Przypisz użytkownika][200]

**Aby przypisać Britta Simon AppDynamics, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **AppDynamics**.

    ![Konfigurowanie logowania jednokrotnego](./media/appdynamics-tutorial/tutorial_appdynamics_app.png)

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

Celem tej sekcji jest do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka AppDynamics w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji AppDynamics.

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/appdynamics-tutorial/tutorial_general_01.png
[2]: ./media/appdynamics-tutorial/tutorial_general_02.png
[3]: ./media/appdynamics-tutorial/tutorial_general_03.png
[4]: ./media/appdynamics-tutorial/tutorial_general_04.png

[100]: ./media/appdynamics-tutorial/tutorial_general_100.png

[200]: ./media/appdynamics-tutorial/tutorial_general_200.png
[201]: ./media/appdynamics-tutorial/tutorial_general_201.png
[202]: ./media/appdynamics-tutorial/tutorial_general_202.png
[203]: ./media/appdynamics-tutorial/tutorial_general_203.png
