---
title: 'Samouczek: Integracja usługi Azure Active Directory z BlueJeans | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i BlueJeans.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: jeedes
ms.openlocfilehash: 2ec94217a8df2efaa23eb3cc2c9d5a80e8037615
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39425991"
---
# <a name="tutorial-azure-active-directory-integration-with-bluejeans"></a>Samouczek: Integracja usługi Azure Active Directory z BlueJeans

W tym samouczku dowiesz się, jak zintegrować BlueJeans w usłudze Azure Active Directory (Azure AD).

Integrowanie BlueJeans z usługą Azure AD zapewnia następujące korzyści:

- Możesz kontrolować w usłudze Azure AD, kto ma dostęp do BlueJeans
- Umożliwia użytkownikom automatyczne pobieranie zalogowanych do BlueJeans (logowanie jednokrotne) przy użyciu konta usługi Azure AD
- Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą BlueJeans, potrzebne są następujące elementy:

- Subskrypcję usługi Azure AD
- BlueJeans logowania jednokrotnego włączonych subskrypcji

> [!NOTE]
> Aby przetestować kroki opisane w tym samouczku, zaleca się używania środowiska produkcyjnego.

Aby przetestować kroki opisane w tym samouczku, należy wykonać te zalecenia:

- Nie należy używać środowiska produkcyjnego, chyba że jest to konieczne.
- Jeśli nie masz środowisko usługi Azure AD w wersji próbnej, możesz pobrać miesięczna wersja próbna [tutaj](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Opis scenariusza
W ramach tego samouczka można przetestować usługę Azure AD rejestracji jednokrotnej w środowisku testowym. Scenariusz opisany w tym samouczku składa się z dwóch głównych bloków konstrukcyjnych:

1. Dodawanie BlueJeans z galerii
1. Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne

## <a name="adding-bluejeans-from-the-gallery"></a>Dodawanie BlueJeans z galerii
Aby skonfigurować integrację BlueJeans w usłudze Azure AD, należy dodać BlueJeans z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać BlueJeans z galerii, wykonaj następujące czynności:**

1. W  **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Usługa Active Directory][1]

1. Przejdź do **aplikacje dla przedsiębiorstw**. Następnie przejdź do **wszystkie aplikacje**.

    ![Aplikacje][2]

1. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Aplikacje][3]

1. W polu wyszukiwania wpisz **BlueJeans**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/tutorial_bluejeans_search.png)

1. W panelu wyników wybierz **BlueJeans**, a następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/tutorial_bluejeans_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurowanie i testowania usługi Azure AD logowanie jednokrotne
W tej sekcji Konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą BlueJeans w oparciu o użytkownika testu o nazwie "Britta Simon."

Dla logowania jednokrotnego do pracy usługi Azure AD musi znać użytkownika odpowiednika w BlueJeans do użytkownika w usłudze Azure AD. Innymi słowy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w BlueJeans musi można ustanowić.

W BlueJeans, należy przypisać wartość **nazwa_użytkownika** w usłudze Azure AD jako wartość **Username** do ustanawiania relacji łączy.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą BlueJeans, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configuring-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
1. **[Tworzenie użytkownika testowego usługi Azure AD](#creating-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
1. **[Tworzenie użytkownika testowego BlueJeans](#creating-a-bluejeans-test-user)**  — aby odpowiednikiem Britta Simon w BlueJeans jest połączony z usługi Azure AD reprezentacja użytkownika.
1. **[Przypisywanie użytkownika testowego usługi Azure AD](#assigning-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
1. **[Testowanie logowania jednokrotnego](#testing-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz włączyć usługi Azure AD logowania jednokrotnego w witrynie Azure portal i konfigurowanie logowania jednokrotnego w aplikacji BlueJeans.

**Aby skonfigurować usługę Azure AD logowanie jednokrotne z BlueJeans, wykonaj następujące czynności:**

1. W witrynie Azure portal na **BlueJeans** strona integracji aplikacji, kliknij przycisk **logowanie jednokrotne**.

    ![Konfigurowanie logowania jednokrotnego][4]

1. Na **logowanie jednokrotne** okno dialogowe, wybierz opcję **tryb** jako **opartej na SAML logowania jednokrotnego** włączyć logowanie jednokrotne.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_samlbase.png)

1. Na **BlueJeans domena i adresy URL** sekcji, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_url.png)

    a. W **adres URL logowania** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.BlueJeans.com`

    b. W **identyfikator** pole tekstowe, wpisz adres URL przy użyciu następującego wzorca: `https://<companyname>.BlueJeans.com`

    > [!NOTE]
    > Te wartości są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania jednokrotnego i identyfikator. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta BlueJeans](https://support.bluejeans.com/contact) do uzyskania tych wartości.

1. Na **certyfikat podpisywania SAML** kliknij **Certificate(Base64)** , a następnie zapisz plik certyfikatu na komputerze.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_certificate.png) 

1. Kliknij przycisk **Zapisz** przycisku.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_general_400.png)

1. Na **konfiguracji BlueJeans** , kliknij przycisk **skonfigurować BlueJeans** otworzyć **Konfigurowanie logowania jednokrotnego** okna. Kopiuj **adres URL wylogowania, Zmień hasło, adres URL i SAML pojedynczego logowania jednokrotnego usługi adresu URL** z **krótki przewodnik po sekcji.**

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_configure.png) 

1. W oknie przeglądarki internetowej innej, zaloguj się do Twojej **BlueJeans** witryny firmy jako administrator.

1. Przejdź do **administratora \> ustawienia grupy \> zabezpieczeń**.

   ![Administrator](./media/bluejeans-tutorial/IC785868.png "administratora")

1. W **zabezpieczeń** sekcji, wykonaj następujące czynności:

   ![SAML logowania jednokrotnego](./media/bluejeans-tutorial/IC785869.png "SAML logowania jednokrotnego")

   a. Wybierz **SAML logowania jednokrotnego**.

   b. Wybierz **włączyć automatyczną aprowizację**.

1. Przenieś następujące czynności:

    ![Ścieżka certyfikatu](./media/bluejeans-tutorial/IC785870.png "certyfikatu ścieżki")

    a. Kliknij przycisk **wybierz plik**, a następnie przekaż pobranego certyfikatu.

    b. Wklej **SAML pojedynczego logowania jednokrotnego usługi adresu URL** do **adres URL logowania** pola tekstowego.

    c. Wklej **Zmień hasło, adres URL** do **hasła, Zmień adres URL** pola tekstowego.

    d. Wklej **adres URL wylogowania** do **adres URL wylogowania** pola tekstowego.

1. Przenieś następujące czynności:

    ![Zapisz zmiany](./media/bluejeans-tutorial/IC785874.png "Zapisz zmiany")

    a. W **identyfikator użytkownika** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    b. W **E-mail** polu tekstowym wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    c. Kliknij przycisk **Zapisz zmiany**.

### <a name="creating-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD
Celem tej sekcji jest tworzenie użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

![Utwórz użytkownika usługi Azure AD][100]

**Aby utworzyć użytkownika testowego w usłudze Azure AD, wykonaj następujące czynności:**

1. W **witryny Azure portal**, w okienku nawigacji po lewej stronie kliknij **usługi Azure Active Directory** ikony.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/create_aaduser_01.png)

1. Aby wyświetlić listę użytkowników, przejdź do **użytkowników i grup** i kliknij przycisk **wszyscy użytkownicy**.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/create_aaduser_02.png)

1. Aby otworzyć **użytkownika** okno dialogowe, kliknij przycisk **Dodaj** u góry okna dialogowego.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/create_aaduser_03.png)

1. Na **użytkownika** okna dialogowego strony, wykonaj następujące czynności:

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/bluejeans-tutorial/create_aaduser_04.png) 

    a. W **nazwa** polu tekstowym wpisz **BrittaSimon**.

    b. W **nazwa_użytkownika** polu tekstowym wpisz **adres e-mail** z BrittaSimon.

    c. Wybierz **Pokaż hasło** i zanotuj wartość **hasło**.

    d. Kliknij pozycję **Utwórz**.

### <a name="creating-a-bluejeans-test-user"></a>Tworzenie użytkownika testowego BlueJeans

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie Britta Simon w BlueJeans. BlueJeans obsługuje automatyczna aprowizacja użytkowników, która jest domyślnie włączona. Więcej szczegółów można znaleźć [tutaj](bluejeans-provisioning-tutorial.md) dotyczące sposobu konfigurowania automatycznej aprowizacji użytkowników.

**Jeśli potrzebujesz ręcznie utworzyć użytkownika, należy wykonać następujące kroki:**

1. Zaloguj się do Twojej **BlueJeans** witryny firmy jako administrator.

1. Przejdź do **administratora \> Zarządzanie użytkownikami \> Dodaj użytkownika**.

   ![Administrator](./media/bluejeans-tutorial/IC785877.png "administratora")

   >[!IMPORTANT]
   >**Dodaj użytkownika** karta jest dostępna tylko jeśli w **kartę Zabezpieczenia**, **włączyć automatyczną aprowizację** nie jest zaznaczone. 

1. W **Dodaj użytkownika** sekcji, wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/bluejeans-tutorial/IC785886.png "Dodaj użytkownika")

    a. Wpisz **BlueJeans Username**, **adres E-mail**, **BlueJeans spotkania identyfikator**, **kodu dostępu Moderator**, **imię i nazwisko** , **firmy** poprawnego konta usługi AAD do aprowizowania w powiązanych pól tekstowych.

    b. Kliknij przycisk **Dodaj użytkownika**.

>[!NOTE]
>Można użyć jakichkolwiek innych BlueJeans użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez BlueJeans do aprowizacji kont użytkowników usługi AAD.

### <a name="assigning-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do BlueJeans.

![Przypisz użytkownika][200]

**Aby przypisać Britta Simon BlueJeans, wykonaj następujące czynności:**

1. W witrynie Azure portal Otwórz widok aplikacji, a następnie przejdź do widoku katalogu i przejdź do **aplikacje dla przedsiębiorstw** kliknięcie **wszystkie aplikacje**.

    ![Przypisz użytkownika][201]

1. Na liście aplikacji wybierz **BlueJeans**.

    ![Konfigurowanie logowania jednokrotnego](./media/bluejeans-tutorial/tutorial_bluejeans_app.png)

1. W menu po lewej stronie kliknij **użytkowników i grup**.

    ![Przypisz użytkownika][202]

1. Kliknij przycisk **Dodaj** przycisku. Następnie wybierz pozycję **użytkowników i grup** na **Dodaj przydziału** okna dialogowego.

    ![Przypisz użytkownika][203]

1. Na **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** na liście Użytkownicy.

1. Kliknij przycisk **wybierz** znajdujący się na **użytkowników i grup** okna dialogowego.

1. Kliknij przycisk **przypisać** znajdujący się na **Dodaj przydziału** okna dialogowego.

### <a name="testing-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka BlueJeans w panelu dostępu, należy uzyskać strony logowania BlueJeans aplikacji.
Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [wprowadzenie do panelu dostępu](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](tutorial-list.md)
* [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurowanie Aprowizowania użytkowników](bluejeans-provisioning-tutorial.md)

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
