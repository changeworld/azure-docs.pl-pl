---
title: 'Samouczek: Integracja usługi Azure Active Directory z ThousandEyes | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: b6dcc2e057ab0877646bb5ace962cfd00cfb8839
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39041810"
---
# <a name="tutorial-azure-active-directory-integration-with-thousandeyes"></a>Samouczek: Integracja usługi Azure Active Directory z ThousandEyes

W tym samouczku dowiesz się, jak zintegrować ThousandEyes w usłudze Azure Active Directory (Azure AD).

Integrowanie ThousandEyes z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do ThousandEyes
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do ThousandEyes (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ThousandEyes, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- ThousandEyes logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięcznej wersji próbnej tutaj: [oferta wersji próbnej](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie ThousandEyes z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-thousandeyes-from-the-gallery"></a>Dodawanie ThousandEyes z galerii
Aby skonfigurować integrację ThousandEyes w usłudze Azure AD, należy dodać ThousandEyes z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ThousandEyes z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Usługa Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]
    
3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

4. W polu wyszukiwania wpisz **ThousandEyes**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_search.png)

5. W panelu wyników wybierz **ThousandEyes**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/tutorial_thousandeyes_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą ThousandEyes w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w ThousandEyes do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ThousandEyes musi można ustanowić.

W ThousandEyes, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ThousandEyes, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego ThousandEyes](#creating-a-thousandeyes-test-user)**  — aby odpowiednikiem Britta Simon w ThousandEyes połączonego z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji ThousandEyes.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z ThousandEyes, wykonaj następujące czynności:**

1. W witrynie Azure portal na **ThousandEyes** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

2. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/tutorial_thousandeyes_samlbase.png)

3. Na **ThousandEyes domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/tutorial_thousandeyes_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL jako: `https://app.thousandeyes.com/login/sso`

4. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/tutorial_thousandeyes_certificate.png)

5. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/tutorial_general_400.png)

6. Na **konfiguracji ThousandEyes** , kliknij przycisk **skonfigurować ThousandEyes** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, identyfikator jednostki języka SAML i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/tutorial_thousandeyes_configure.png) 

7. W oknie przeglądarki internetowej innej, zaloguj się na swoje **ThousandEyes** witryny firmy jako administrator.

8. W menu u góry kliknij **ustawienia**.

    ![Ustawienia](./media/thousandeyes-tutorial/ic790066.png "ustawienia")

9. Kliknij przycisk **konta**

    ![Konto](./media/thousandeyes-tutorial/ic790067.png "konta")

10. Kliknij przycisk **zabezpieczeń i uwierzytelniania** kartę.

    ![Zabezpieczenia i uwierzytelnianie](./media/thousandeyes-tutorial/ic790068.png "zabezpieczeń i uwierzytelniania")

11. W **konfiguracji logowania jednokrotnego** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/ic790069.png "skonfigurować logowanie jednokrotne")

    a. Wybierz **włączyć rejestrację jednokrotną**.

    b. W **adres URL strony logowania** pola tekstowego, Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL**, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL strony wylogowania** pola tekstowego, Wklej **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    d. **Wystawca dostawcy tożsamości** pola tekstowego, Wklej **identyfikator jednostki SAML**, które zostały skopiowane z witryny Azure portal.

    e. W **certyfikatu weryfikacji**, kliknij przycisk **Choose file**, a następnie przekaż certyfikat został pobrany z witryny Azure portal.

    f. Kliknij pozycję **Zapisz**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_01.png) 

2. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.
    
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_02.png) 

3. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_03.png)

4. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/thousandeyes-tutorial/create_aaduser_04.png)

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij przycisk **Utwórz**.

### <a name="creating-a-thousandeyes-test-user"></a>Tworzenie użytkownika testowego ThousandEyes

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w ThousandEyes. ThousandEyes obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](thousandeyes-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do witryny firmy ThousandEyes jako administrator.

2. Kliknij przycisk **ustawienia**.

    ![Ustawienia](./media/thousandeyes-tutorial/IC790066.png "ustawienia")

3. Kliknij przycisk **konta**.

    ![Konto](./media/thousandeyes-tutorial/IC790067.png "konta")

4. Kliknij przycisk **konta & użytkowników** kartę.

    ![Konta & użytkowników](./media/thousandeyes-tutorial/IC790073.png "konta & użytkowników")

5. W **Dodawanie użytkowników i kont** sekcji, wykonaj następujące czynności:

    ![Dodaj konta użytkowników](./media/thousandeyes-tutorial/IC790074.png "dodać konta użytkowników")

    a. W **nazwa** polu tekstowym wpisz nazwę użytkownika, takie jak **Britta Simon**.

    b. W **E-mail** polu tekstowym wpisz adres e-mail użytkownika, takie jak **brittasimon@contoso.com**.

    b. Kliknij przycisk **dodać nowego użytkownika do konta**.

    > [!NOTE]
    > Właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail, w tym link do potwierdzenia i aktywować konto.

> [!NOTE]
> Można użyć jakichkolwiek innych ThousandEyes użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez ThousandEyes do świadczenia usługi Azure Active Directory kont użytkowników.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ThousandEyes.

![Przypisz użytkownika][200] 

**Aby przypisać Britta Simon ThousandEyes, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201] 

2. Na liście aplikacji wybierz **ThousandEyes**.

    ![Konfigurowanie logowania jednokrotnego](./media/thousandeyes-tutorial/tutorial_thousandeyes_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202] 

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

6. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

7. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.
    
### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ThousandEyes w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji ThousandEyes.

Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](thousandeyes-provisioning-tutorial.md)


<!--Image references-->

[1]: ./media/thousandeyes-tutorial/tutorial_general_01.png
[2]: ./media/thousandeyes-tutorial/tutorial_general_02.png
[3]: ./media/thousandeyes-tutorial/tutorial_general_03.png
[4]: ./media/thousandeyes-tutorial/tutorial_general_04.png

[100]: ./media/thousandeyes-tutorial/tutorial_general_100.png

[200]: ./media/thousandeyes-tutorial/tutorial_general_200.png
[201]: ./media/thousandeyes-tutorial/tutorial_general_201.png
[202]: ./media/thousandeyes-tutorial/tutorial_general_202.png
[203]: ./media/thousandeyes-tutorial/tutorial_general_203.png
