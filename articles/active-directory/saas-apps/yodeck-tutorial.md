---
title: 'Samouczek: Integracja usługi Azure Active Directory z Yodeck | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Yodeck.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: b2c8dccb-eeb0-4f4d-a24d-8320631ce819
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: jeedes
ms.openlocfilehash: d461d106e7805f12327397a2572552fdd5606569
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39055711"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>Samouczek: Integracja usługi Azure Active Directory z Yodeck

W tym samouczku dowiesz się, jak zintegrować Yodeck w usłudze Azure Active Directory (Azure AD).

Integrowanie Yodeck z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Yodeck.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do Yodeck (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Yodeck, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- Yodeck logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie Yodeck z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-yodeck-from-the-gallery"></a>Dodawanie Yodeck z galerii
Aby skonfigurować integrację Yodeck w usłudze Azure AD, należy dodać Yodeck z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Yodeck z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **Yodeck**, wybierz opcję **Yodeck** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Yodeck na liście wyników](./media/yodeck-tutorial/tutorial_yodeck_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą Yodeck w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w Yodeck do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Yodeck musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Yodeck, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego Yodeck](#create-a-yodeck-test-user)**  — aby odpowiednikiem Britta Simon w Yodeck połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji Yodeck.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z Yodeck, wykonaj następujące czynności:**

1. W witrynie Azure portal na **Yodeck** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Okno dialogowe rejestracji jednokrotnej](./media/yodeck-tutorial/tutorial_yodeck_samlbase.png)

3. Na **Yodeck domena i adresy URL** sekcji, wykonaj następujące kroki, jeśli chcesz skonfigurować aplikację w **tożsamości** zainicjowano tryb:

    ![Yodeck domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/yodeck-tutorial/tutorial_yodeck_url.png)

    W **identyfikator jednostki** pole tekstowe, wpisz adres URL: `https://app.yodeck.com/api/v1/account/metadata/`

4. Sprawdź **Pokaż zaawansowane ustawienia adresu URL** i wykonać następujący krok, jeśli chcesz skonfigurować aplikację w **SP** zainicjowano tryb:

    ![Yodeck domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/yodeck-tutorial/tutorial_yodeck_url1.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL: `https://app.yodeck.com/login`

5. Na **certyfikat podpisywania SAML** sekcji, kliknij przycisk kopiowania, aby skopiować **adres Url metadanych Federacji aplikacji** i wklej go w Notatniku.

    ![Link pobierania certyfikatu](./media/yodeck-tutorial/tutorial_yodeck_certificate.png)

6. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie pojedynczego logowania jednokrotnego Zapisz przycisku](./media/yodeck-tutorial/tutorial_general_400.png)
    
7. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Yodeck.

8. Kliknij pozycję **ustawienia użytkownika** formie prawym górnym rogu strony i wybierając opcję **ustawienia konta**.

    ![Konfiguracja Yodeck](./media/yodeck-tutorial/configure1.png)

9. Wybierz **SAML** i wykonaj następujące czynności:

    ![Konfiguracja Yodeck](./media/yodeck-tutorial/configure2.png)

    a. Wybierz **Importuj z adresu URL**.

    b. W **adresu URL** pola tekstowego, Wklej **adres Url metadanych Federacji aplikacji** wartości, które zostały skopiowane z witryny Azure portal i kliknij przycisk **importu**.
    
    c. Po zaimportowaniu **adres Url metadanych Federacji aplikacji**, pozostałe pola wypełnienia automatycznie.

    d. Kliknij pozycję **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

   ![Tworzenie użytkownika testowego usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W witrynie Azure portal w okienku po lewej stronie kliknij pozycję **usługi Azure Active Directory** przycisku.

    ![Przycisk usługi Azure Active Directory](./media/yodeck-tutorial/create_aaduser_01.png)

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup**, a następnie kliknij przycisk **wszyscy użytkownicy**.

    !["Użytkownicy i grupy" i "All users" linki](./media/yodeck-tutorial/create_aaduser_02.png)

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** w górnej części **wszyscy użytkownicy** okno dialogowe.

    ![Przycisk Dodaj](./media/yodeck-tutorial/create_aaduser_03.png)

4. W **użytkownika** okna dialogowego pole, wykonaj następujące czynności:

    ![Okno dialogowe użytkownika](./media/yodeck-tutorial/create_aaduser_04.png)

    a. W **nazwa** wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** wpisz adres e-mail użytkownika Britta Simon.

    c. Wybierz **Pokaż hasło** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w **hasło** pole.

    d. Kliknij przycisk **Utwórz**.
 
### <a name="create-a-yodeck-test-user"></a>Tworzenie użytkownika testowego Yodeck

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Yodeck, musi być obsługiwana w Yodeck.
W przypadku Yodeck Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby udostępnić konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy Yodeck jako administrator.

2. Kliknij pozycję **ustawienia użytkownika** formie prawym górnym rogu strony i wybierając opcję **użytkowników**.

    ![Dodawanie pracownika](./media/yodeck-tutorial/user1.png)

3. Kliknij pozycję **+ użytkownik** otworzyć **szczegóły użytkownika** kartę.

    ![Dodawanie pracownika](./media/yodeck-tutorial/user2.png)

4. Na **szczegóły użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Dodawanie pracownika](./media/yodeck-tutorial/user3.png)

    a. W **imię** polu tekstowym wpisz imię użytkownika, takie jak **Britta**.

    b. W **nazwisko** polu tekstowym wpisz nazwisko użytkownika, takie jak **Simon**.

    c. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **brittasimon@contoso.com**.

    d. Wybierz odpowiedni **uprawnień konta** opcji zgodnie z wymaganiami organizacji.
    
    e. Kliknij pozycję **Zapisz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Yodeck.

![Przypisanie roli użytkownika][200]

**Aby przypisać Britta Simon Yodeck, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **Yodeck**.

    ![Link Yodeck na liście aplikacji](./media/yodeck-tutorial/tutorial_yodeck_app.png)  

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Link "Użytkownicy i grupy"][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Okienko Dodawanie przypisania][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Yodeck w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji Yodeck.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/yodeck-tutorial/tutorial_general_01.png
[2]: ./media/yodeck-tutorial/tutorial_general_02.png
[3]: ./media/yodeck-tutorial/tutorial_general_03.png
[4]: ./media/yodeck-tutorial/tutorial_general_04.png

[100]: ./media/yodeck-tutorial/tutorial_general_100.png

[200]: ./media/yodeck-tutorial/tutorial_general_200.png
[201]: ./media/yodeck-tutorial/tutorial_general_201.png
[202]: ./media/yodeck-tutorial/tutorial_general_202.png
[203]: ./media/yodeck-tutorial/tutorial_general_203.png

