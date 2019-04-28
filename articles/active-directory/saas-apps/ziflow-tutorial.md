---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Ziflow | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 175e678365016bafd3d18f590a5434c32ac9fadd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60798217"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Ziflow

W tym samouczku dowiesz się, jak zintegrować Ziflow w usłudze Azure Active Directory (Azure AD).

Integrowanie Ziflow z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Ziflow.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Ziflow (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Ziflow, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- Ziflow logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować czynności opisane w tym samouczku, należy postępować zgodnie z następującymi zaleceniami:

- Nie używaj środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Ziflow z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-ziflow-from-the-gallery"></a>Dodawanie Ziflow z galerii
Aby skonfigurować integrację Ziflow w usłudze Azure AD, należy dodać Ziflow z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Ziflow z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja][3]

4. W polu wyszukiwania wpisz **Ziflow**, wybierz opcję **Ziflow** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Ziflow na liście wyników](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Ziflow w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Ziflow do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Ziflow musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Ziflow, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
3. **[Tworzenie użytkownika testowego Ziflow](#create-a-ziflow-test-user)**  — aby odpowiednikiem Britta Simon w Ziflow połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Ziflow.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Ziflow, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Ziflow** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Na **Ziflow domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Ziflow domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. W polu tekstowym **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > Podane wyżej wartości nie są rzeczywiste. Wartość unikatowego Identyfikatora identyfikator i adres URL logowania zostaną zaktualizowane o wartości rzeczywiste, co zostało wyjaśnione w dalszej części tego samouczka.

4. Na **certyfikat podpisywania SAML** kliknij **certyfikat (Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link do pobierania certyfikatu](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie przycisku Zapisz logowania jednokrotnego](./media/ziflow-tutorial/tutorial_general_400.png)

6. Na **konfiguracji Ziflow** , kliknij przycisk **skonfigurować Ziflow** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Ziflow](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. W oknie przeglądarki internetowej innej, zaloguj się do Ziflow jako Administrator zabezpieczeń.

8. W prawym górnym rogu kliknij awatara, a następnie kliknij przycisk **Zarządzanie kontem**.

    ![Zarządzanie Ziflow konfiguracji](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. W lewym górnym rogu, kliknij przycisk **logowania jednokrotnego**.

    ![Ziflow konfiguracji logowania](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Na stronie **Single Sign-On**(Logowanie jednokrotne) wykonaj następujące czynności:

    ![Jednym konfiguracji Ziflow](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Wybierz **typu** jako **SAML2.0**.

    b. W **adres URL logowania** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, który skopiowano z witryny Azure portal.

    c. Przekaż certyfikat zakodowany base-64, pobrany z witryny Azure portal do **X509 certyfikatu podpisywania**.

    d. W **adresu URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    e. Z **ustawienia konfiguracji dla identyfikatora dostawcy** sekcji, skopiuj wartość unikatowego Identyfikatora wyróżnione i dołącz go z identyfikatorem i logowanie jednokrotne adresu URL w **sekcji Ziflow domena i adresy URL** na Witryna Azure portal.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/ziflow-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/ziflow-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/ziflow-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/ziflow-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij pozycję **Utwórz**.
  
### <a name="create-a-ziflow-test-user"></a>Tworzenie użytkownika testowego Ziflow

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Ziflow, musi być obsługiwana w Ziflow. W Ziflow Inicjowanie obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do Ziflow jako Administrator zabezpieczeń.

2. Przejdź do **osób** u góry.

    ![Konfiguracja Ziflow osoby](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kliknij przycisk **Dodaj** a następnie kliknij przycisk **Dodaj użytkownika**.

    ![Konfiguracja Ziflow dodawania użytkownika](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Na **Dodawanie użytkownika** okna podręcznego, wykonaj następujące czynności:

    ![Konfiguracja Ziflow dodawania użytkownika](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak brittasimon@contoso.com.

    b. W **imię** tekstu Wprowadź imię użytkownika, takich jak Britta.

    c. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak Simon.

    d. Wybierz rolę Ziflow.

    e. Kliknij przycisk **Dodaj 1 użytkownika**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z linkiem umożliwiającym potwierdzenie konta, zanim stanie się ono aktywne.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Ziflow.

![Przypisanie roli użytkownika][200] 

**Aby przypisać Britta Simon Ziflow, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Ziflow**.

    ![Link Ziflow na liście aplikacji](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Ziflow w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Ziflow.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

