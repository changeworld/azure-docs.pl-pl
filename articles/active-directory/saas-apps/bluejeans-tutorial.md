---
title: 'Samouczek: Integracja usługi Azure Active Directory z BlueJeans | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: jeedes
ms.openlocfilehash: cff1512c56dba9907adbf1bb4452f11d47d0787d
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/25/2018
ms.locfileid: "50095232"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Samouczek: Integracja usługi Azure Active Directory z BlueJeans

W tym samouczku dowiesz się, jak zintegrować BlueJeans w usłudze Azure Active Directory (Azure AD).

Integrowanie BlueJeans z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do BlueJeans.
- Aby umożliwić użytkownikom automatyczne pobieranie zalogowanych do BlueJeans (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą BlueJeans, potrzebne są następujące elementy:

- Subskrypcji usługi Azure AD
- BlueJeans logowanie jednokrotne włączone subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz to zrobić [miesięczna wersja próbna](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza

W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie BlueJeans z galerii
2. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie BlueJeans z galerii

Aby skonfigurować integrację BlueJeans w usłudze Azure AD, należy dodać BlueJeans z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać BlueJeans z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony. 

    ![Przycisk usługi Azure Active Directory][1]

2. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![W bloku aplikacji przedsiębiorstwa][2]

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji][3]

4. W polu wyszukiwania wpisz **BlueJeans**, wybierz opcję **BlueJeans** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![BlueJeans na liście wyników](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji służy do konfigurowania i testowanie usługi Azure AD logowanie jednokrotne za pomocą BlueJeans w oparciu o użytkownika testu o nazwie "Britta Simon".

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w BlueJeans do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w BlueJeans musi można ustanowić.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą BlueJeans, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
3. **[Tworzenie użytkownika testowego BlueJeans](#creating-a-bluejeans-test-user)**  — aby odpowiednikiem Britta Simon w BlueJeans jest połączony z usługi Azure AD reprezentacja użytkownika.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji BlueJeans.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z BlueJeans, wykonaj następujące czynności:**

1. W witrynie Azure portal na **BlueJeans** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej][4]

2. Na **wybierz jedną metodę logowania jednokrotnego** okno dialogowe, kliknij przycisk **wybierz** dla **SAML** trybu, aby włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_general_301.png)

4. Na **Ustaw się logowanie jednokrotne z SAML** kliknij **Edytuj** ikonę, aby otworzyć **podstawową konfigurację protokołu SAML** okna dialogowego.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_editurl.png)

5. Na **podstawową konfigurację protokołu SAML** sekcji, wykonaj następujące czynności:

    ![BlueJeans domena i adresy URL pojedynczego logowania jednokrotnego informacji](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Wartość logowania nie jest prawdziwe. Zaktualizuj wartość za pomocą adresu URL logowania rzeczywistych. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta BlueJeans](https://support.bluejeans.com/contact) można uzyskać wartość.

6. Na **certyfikat podpisywania SAML** strony w **certyfikat podpisywania SAML** kliknij **Pobierz** można pobrać **certyfikat (Base64)**, a następnie zapisz plik certyfikatu na komputerze.

    ![Link pobierania certyfikatu](./media/bluejeans-tutorial/tutorial_bluejeans_certficate.png) 

7. Na **Konfigurowanie BlueJeans** sekcji, skopiuj odpowiedni adres URL, zgodnie z wymaganiami.

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

    ![Konfiguracja blueJeans](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

8. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **BlueJeans** witryny firmy jako administrator.

9. Przejdź do **administratora \> ustawienia grupy \> zabezpieczeń**.

    ![Administrator](./media/bluejeans-tutorial/IC785868.png "administratora")

10. W **zabezpieczeń** sekcji, wykonaj następujące czynności:

    ![SAML logowania jednokrotnego](./media/bluejeans-tutorial/IC785869.png "SAML logowania jednokrotnego")

    a. Wybierz **SAML logowania jednokrotnego**.

    b. Wybierz **włączyć automatyczną aprowizację**.

11. Przenieś następujące czynności:

    ![Ścieżka certyfikatu](./media/bluejeans-tutorial/IC785870.png "certyfikatu ścieżki")

    a. Kliknij przycisk **wybierz plik**, aby przekazać certyfikat zakodowany base-64, pobrany z witryny Azure portal.

    b. W **adres URL logowania** pola tekstowego, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. W **hasła, Zmień adres URL** pola tekstowego, Wklej wartość **Zmień hasło, adres URL** skopiowanej w witrynie Azure portal.

    d. W **adres URL wylogowania** pola tekstowego, Wklej wartość **adres URL wylogowania** skopiowanej w witrynie Azure portal.

12. Przenieś następujące czynności:

    ![Zapisz zmiany](./media/bluejeans-tutorial/IC785874.png "Zapisz zmiany")

    a. W **identyfikator użytkownika** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. W **E-mail** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kliknij przycisk **ZAPISAĆ zmiany w**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W witrynie Azure portal w okienku po lewej stronie wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.

    ![Utwórz użytkownika usługi Azure AD][100]

2. Wybierz **nowego użytkownika** w górnej części ekranu.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png) 

3. We właściwościach użytkownika wykonaj następujące czynności.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

    a. W **nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    c. Wybierz **właściwości**, wybierz opcję **hasło Show** pole wyboru, a następnie zapisz wartość, która jest wyświetlana w polu hasło.

    d. Wybierz pozycję **Utwórz**.

### <a name="creating-a-bluejeans-test-user"></a>Tworzenie użytkownika testowego BlueJeans

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w BlueJeans. BlueJeans obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](bluejeans-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do Twojej **BlueJeans** witryny firmy jako administrator.

2. Przejdź do **administratora \> MANAGE USERS \> Dodaj użytkownika**.

    ![Administrator](./media/bluejeans-tutorial/IC785877.png "administratora")

    >[!IMPORTANT]
    >**Dodaj użytkownika** karta jest dostępna tylko jeśli w **kartę WIELOSKŁADNIKOWYM**, **włączyć automatyczną aprowizację** nie jest zaznaczone. 

3. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/bluejeans-tutorial/IC785886.png "Dodaj użytkownika")

    a. W **imię** tekstu Wprowadź imię użytkownika, takich jak **Britta**.

    b. W **nazwisko** tekstu wprowadź nazwisko użytkownika, takich jak **simon**.

    c. W **wybierz nazwę użytkownika BlueJeans** tekstu wprowadź nazwę użytkownika, użytkownika, takich jak **Brittasimon**

    d. W **Utwórz hasło** tekstu wprowadź hasło.

    e. W **firmy** tekstu wprowadź Twojej firmy.

    f. W **adres E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak **brittasimon@contoso.com**.

    g. W **tworzenie spełniony spotkania BlueJeans** tekstu wprowadź swój identyfikator spotkania.

    h. W **pobranie kodu dostępu Moderator** tekstu wprowadź kod dostępu.

    i. Kliknij przycisk **Kontynuuj**.

    ![Użytkownik Addd](./media/bluejeans-tutorial/IC785887.png "Addd użytkownika")

    J. Kliknij przycisk **Dodaj użytkownika**.

>[!NOTE]
>Można użyć jakichkolwiek innych BlueJeans użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez BlueJeans można uaktywniać ich konta usługi Azure AD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do BlueJeans.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

2. Na liście aplikacji wybierz **BlueJeans**.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_app.png) 

3. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

4. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** na liście użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

6. W **Dodaj przydziału** okna dialogowego wybierz **przypisać** przycisku.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka BlueJeans w panelu dostępu, użytkownik powinien uzyskać automatycznie zalogowanych do aplikacji BlueJeans.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/bluejeans-tutorial/tutorial_general_01.png
[2]: ./media/bluejeans-tutorial/tutorial_general_02.png
[3]: ./media/bluejeans-tutorial/tutorial_general_03.png
[4]: ./media/bluejeans-tutorial/tutorial_general_04.png

[100]: ./media/bluejeans-tutorial/tutorial_general_100.png

[200]: ./media/bluejeans-tutorial/tutorial_general_200.png
[201]: ./media/bluejeans-tutorial/tutorial_general_201.png
[202]: ./media/bluejeans-tutorial/tutorial_general_202.png
[203]: ./media/bluejeans-tutorial/tutorial_general_203.png
