---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Pingboard | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Pingboard.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a26418a5159781eab96cf7730d669ccb65742d86
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/18/2019
ms.locfileid: "57861489"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Pingboard

W tym samouczku dowiesz się, jak zintegrować Pingboard w usłudze Azure Active Directory (Azure AD).

Integrowanie Pingboard z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do Pingboard
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do Pingboard (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Pingboard, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Pingboard logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Pingboard z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-pingboard-from-the-gallery"></a>Dodawanie Pingboard z galerii
Aby skonfigurować integrację Pingboard w usłudze Azure AD, należy dodać Pingboard z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Pingboard z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje dla przedsiębiorstw][2]

1. Kliknij przycisk **Dodaj** przycisk u góry okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

1. W polu wyszukiwania wpisz **Pingboard**, wybierz opcję **Pingboard** panel wyników i kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Pingboard na liście wyników](./media/pingboard-tutorial/tutorial_pingboard_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Pingboard w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Pingboard do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Pingboard musi można ustanowić.

Ustanowieniu tej relacji łączy, przypisując wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** w Pingboard.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Pingboard, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
1. **[Tworzenie użytkownika testowego Pingboard](#create-a-pingboard-test-user)**  — aby odpowiednikiem Britta Simon w Pingboard połączonego z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Pingboard.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Pingboard, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Pingboard** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

1.  Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/pingboard-tutorial/tutorial_pingboard_samlbase.png)

1. Na **Pingboard domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Pingboard domena i adresy URL pojedynczy informacje logowania dostawcy tożsamości](./media/pingboard-tutorial/tutorial_pingboard_url.png)

    a. W **identyfikator** polu tekstowym wpisz wartość jako: `http://app.pingboard.com/sp`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<entity-id>.pingboard.com/auth/saml/consume`

1. Sprawdź **Pokaż zaawansowane ustawienia adresu URL**, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Pingboard domena i adresy URL pojedynczego logowania jednokrotnego informacji SP](./media/pingboard-tutorial/tutorial_pingboard_sp_initiated01.png)

     W **adres URL logowania** pole tekstowe, wpisz adres URL, za pomocą następującego wzorca: `https://<sub-domain>.pingboard.com/sign_in`

    > [!NOTE]
    > Należy pamiętać, że te wartości są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Pingboard](https://support.pingboard.com/) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **XML metadanych** , a następnie zapisz plik XML na tym komputerze.

    ![Kod xml metadanych Pingboard](./media/pingboard-tutorial/tutorial_pingboard_certificate.png)

1. Kliknij przycisk **Save** (Zapisz).

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/pingboard-tutorial/tutorial_general_400.png)

1. Aby skonfigurować logowanie Jednokrotne stronie Pingboard, Otwórz nowe okno przeglądarki i zaloguj się do swojego konta Pingboard. Musi być kontem administratora Pingboard, aby skonfigurować logowanie jednokrotne.

1. Wybierz z górnego menu,, **aplikacji > integracje**

    ![Konfigurowanie logowania jednokrotnego](./media/pingboard-tutorial/Pingboard_integration.png)

1. Na **integracje** strony, Znajdź **"Azure Active Directory"** kafelka, a następnie kliknij go.

    ![Pingboard pojedynczego logowania jednokrotnego integracji](./media/pingboard-tutorial/Pingboard_aad.png)

1. W modalne, kliknij przycisk poniżej **"Konfiguruj"**

    ![Przycisk Konfiguracja Pingboard](./media/pingboard-tutorial/Pingboard_configure.png)

1. Na następnej stronie można zauważyć, że "Integracja logowania jednokrotnego usługi Azure jest włączone". Otwórz pobrany plik XML metadanych w programie Notatnik i Wklej zawartość **metadanych tożsamości**.

    ![Ekran konfiguracji logowania jednokrotnego Pingboard](./media/pingboard-tutorial/Pingboard_sso_configure.png)

1. Plik jest weryfikowana, a jeśli wszystko jest poprawna, logowania jednokrotnego zostanie ono włączone.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](./media/pingboard-tutorial/create_aaduser_01.png) 

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](./media/pingboard-tutorial/create_aaduser_02.png)

1. W górnej części okna dialogowego kliknij **Dodaj** otworzyć **użytkownika** okna dialogowego.

    ![Przycisk dodawania](./media/pingboard-tutorial/create_aaduser_03.png) 

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Okno dialogowe Użytkownik](./media/pingboard-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="create-a-pingboard-test-user"></a>Tworzenie użytkownika testowego Pingboard

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w Pingboard. Pingboard obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznej aprowizacji użytkowników można znaleźć [tutaj](pingboard-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Pingboard jako administrator.

1. Kliknij przycisk **"Dodaj Employee"** znajdujący się na **katalogu** strony.

    ![Dodawanie pracownika](./media/pingboard-tutorial/create_testuser_add.png)

1. Na **"Dodaj Employee"** okna dialogowego strony, wykonaj następujące czynności:

    ![Zaproś inne osoby](./media/pingboard-tutorial/create_testuser_name.png)

    a. W **imię i nazwisko** polu tekstowym wpisz pełną nazwę użytkownika, takie jak **Britta Simon**.

    b. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **brittasimon\@contoso.com**.

    c. W **stanowisko** polu tekstowym wpisz stanowisko Britta Simon.

    d. W **lokalizacji** listę rozwijaną, wybierz lokalizację, w pozycji Britta simon.

    e. Kliknij pozycję **Add** (Dodaj).

1. Ekran potwierdzenia pojawi się, aby upewnić się, Dodawanie użytkownika.

    ![Upewnij się](./media/pingboard-tutorial/create_testuser_confirm.png)

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Pingboard.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon Pingboard, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

1. Na liście aplikacji wybierz **Pingboard**.

    ![Link Pingboard na liście aplikacji](./media/pingboard-tutorial/tutorial_pingboard_app.png) 

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202] 

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](../user-help/active-directory-saas-access-panel-introduction.md) (Wprowadzenie do panelu dostępu).

Po kliknięciu kafelka Pingboard w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Pingboard.

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie aprowizacji użytkowników](pingboard-provisioning-tutorial.md)

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
