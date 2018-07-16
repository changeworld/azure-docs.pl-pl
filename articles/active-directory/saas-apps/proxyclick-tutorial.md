---
title: 'Samouczek: Integracja usługi Azure Active Directory z Proxyclick | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: jeedes
ms.openlocfilehash: 71b9b54e3b8eef1be9f6da7fa812bd8f9d246f47
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39051690"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Samouczek: Integracja usługi Azure Active Directory z Proxyclick

W tym samouczku dowiesz się, jak zintegrować Proxyclick w usłudze Azure Active Directory (Azure AD).

Integrowanie Proxyclick z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Proxyclick.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Proxyclick (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Proxyclick, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Proxyclick logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Proxyclick z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-proxyclick-from-the-gallery"></a>Dodawanie Proxyclick z galerii
Aby skonfigurować integrację Proxyclick w usłudze Azure AD, należy dodać Proxyclick z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Proxyclick z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Proxyclick**, wybierz opcję **Proxyclick** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Proxyclick na liście wyników](./media/proxyclick-tutorial/tutorial_proxyclick_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Proxyclick w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Proxyclick do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Proxyclick musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Proxyclick, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Proxyclick](#create-a-proxyclick-test-user)**  — aby odpowiednikiem Britta Simon w Proxyclick połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Proxyclick.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Proxyclick, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Proxyclick** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/proxyclick-tutorial/tutorial_proxyclick_samlbase.png)

3. Na **Proxyclick domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Proxyclick domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/proxyclick-tutorial/tutorial_proxyclick_url2.png)

    a. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://saml.proxyclick.com/init/<companyId>`

    b. W **adres URL odpowiedzi** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://saml.proxyclick.com/consume/<companyId>`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Proxyclick domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/proxyclick-tutorial/tutorial_proxyclick_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://saml.proxyclick.com/init/<companyId>`

    > [!NOTE]
    > Te wartości są prawdziwe. Te wartości zostaną zaktualizowane o rzeczywisty identyfikator, adres URL odpowiedzi i adres URL logowania, które zostało wyjaśnione w dalszej części tego samouczka.

5. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/proxyclick-tutorial/tutorial_proxyclick_certificate.png) 

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/proxyclick-tutorial/tutorial_general_400.png)

7. Na **konfiguracji Proxyclick** , kliknij przycisk **skonfigurować Proxyclick** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego adres URL usługi** z **krótki przewodnik po sekcji.**

    ![Konfiguracja Proxyclick](./media/proxyclick-tutorial/tutorial_proxyclick_configure.png)

8. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Proxyclick.

9. Wybierz **konta u & stawienia**.

    ![Konfiguracja Proxyclick](./media/proxyclick-tutorial/configure1.png)

10. Przewiń w dół do **INTEGRACJE** i wybierz **SAML**.

    ![Konfiguracja Proxyclick](./media/proxyclick-tutorial/configure2.png)

11. W **SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja Proxyclick](./media/proxyclick-tutorial/configure3.png)

    a. Kopiuj **adres URL klienta SAML** wartość i wklej go w **adres URL odpowiedzi** polu tekstowym w **Proxyclick domena i adresy URL** sekcji w witrynie Azure portal.

    b. Kopiuj **adresu URL przekierowania logowania jednokrotnego SAML** wartość i wklej go w **adres URL logowania** i **identyfikator** pola tekstowe w **Proxyclick domena i adresy URL** sekcji w witrynie Azure portal.

    c. Wybierz **metoda żądania języka SAML** jako **przekierowania HTTP**.

    d. W **wystawcy** pola tekstowego, Wklej wartość **identyfikator jednostki SAML** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **URL punktu końcowego usługi SAML 2.0** pola tekstowego, Wklej wartość **SAML pojedynczego logowania jednokrotnego usługi adresu URL** skopiowane z witryny Azure portal.

    f. Otwórz plik certyfikatu pobrany z witryny Azure portal w programie Notatnik i wklej go do **certyfikatu** pola tekstowego.

    g. Kliknij przycisk **Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/proxyclick-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/proxyclick-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/proxyclick-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/proxyclick-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.

### <a name="create-a-proxyclick-test-user"></a>Tworzenie użytkownika testowego Proxyclick

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Proxyclick, musi być obsługiwana w Proxyclick. W przypadku Proxyclick Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Proxyclick jako administrator.

2. Kliknij przycisk **współpracowników** z górnego paska nawigacyjnego.

    ![Dodawanie pracownika](./media/proxyclick-tutorial/user1.png)

3. Kliknij przycisk **dodawać współpracowników**

    ![Dodawanie pracownika](./media/proxyclick-tutorial/user2.png)

4. W **dodać współpracownika** sekcji, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/proxyclick-tutorial/user3.png)

    a. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **brittasimon@contoso.com**.

    b. W **imię** polu tekstowym Nazwa typu pierwszego użytkownika, takich jak Britta.

    c. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takich jak Simon.

    d. Kliknij przycisk **Dodaj użytkownika**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Proxyclick.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Proxyclick, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **Proxyclick**.

    ![Link Proxyclick na liście aplikacji](./media/proxyclick-tutorial/tutorial_proxyclick_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Proxyclick w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Proxyclick.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/proxyclick-tutorial/tutorial_general_01.png
[2]: ./media/proxyclick-tutorial/tutorial_general_02.png
[3]: ./media/proxyclick-tutorial/tutorial_general_03.png
[4]: ./media/proxyclick-tutorial/tutorial_general_04.png

[100]: ./media/proxyclick-tutorial/tutorial_general_100.png

[200]: ./media/proxyclick-tutorial/tutorial_general_200.png
[201]: ./media/proxyclick-tutorial/tutorial_general_201.png
[202]: ./media/proxyclick-tutorial/tutorial_general_202.png
[203]: ./media/proxyclick-tutorial/tutorial_general_203.png

