---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Syncplicity | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: c823b1b5d009d836ba3f134623a67cab0d38c180
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050293"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Samouczek: Integracja Syncplicity za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Syncplicity w usłudze Azure Active Directory (Azure AD). W ramach Syncplicity integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Syncplicity.
* Umożliwianie użytkownikom można automatycznie zalogowany do Syncplicity za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Syncplicity logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Syncplicity **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-syncplicity-from-the-gallery"></a>Dodawanie Syncplicity z galerii

Aby skonfigurować integrację Syncplicity w usłudze Azure AD, należy dodać Syncplicity z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Syncplicity** w polu wyszukiwania.
1. Wybierz **Syncplicity** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Syncplicity za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Syncplicity.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Syncplicity, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego Syncplicity](#configure-syncplicity-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Syncplicity](#create-syncplicity-test-user)**  — aby odpowiednikiem B.Simon w Syncplicity połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Syncplicity** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Syncplicity](https://www.syncplicity.com/contact-us) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie Syncplicity** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurowanie logowania jednokrotnego Syncplicity

1. Zaloguj się do Twojej **Syncplicity** dzierżawy.

1. W menu u góry kliknij **administratora**, wybierz opcję **ustawienia**, a następnie kliknij przycisk **niestandardowej domeny i logowanie jednokrotne**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na **pojedynczego logowania jednokrotnego (SSO)** okna dialogowego strony, wykonaj następujące czynności:

    ![Logowanie jednokrotne \(logowania jednokrotnego\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. W **domena niestandardowa** polu tekstowym wpisz nazwę domeny.
  
    b. Wybierz **włączone** jako **pojedynczego stanu logowania jednokrotnego**.

    c. W **identyfikator jednostki** pola tekstowego, Wklej **identyfikator jednostki** wartość, która ma być używane w **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

    d. W **adres URL logowania strony** pola tekstowego, Wklej **adres URL logowania** skopiowanej w witrynie Azure portal.

    e. W **adres URL strony wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej w witrynie Azure portal.

    f. W **certyfikatu dostawcy tożsamości**, kliknij przycisk **Choose file**, a następnie przekaż certyfikat, który został pobrany z witryny Azure portal.

    g. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Syncplicity.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Syncplicity**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-syncplicity-test-user"></a>Tworzenie użytkownika testowego Syncplicity

Dla użytkowników usługi AAD można było się zalogować musi być obsługiwana Syncplicity aplikacji. W tej sekcji opisano sposób tworzenia konta użytkownika usługi AAD w Syncplicity.

**Aby udostępnić konto Syncplicity, wykonaj następujące czynności:**

1. Zaloguj się do Twojej **Syncplicity** dzierżawy (na przykład: `https://company.Syncplicity.com`).

1. Kliknij przycisk **administratora** i wybierz **kont użytkowników** a następnie kliknij przycisk **Dodaj użytkownik**.

    ![Zarządzanie użytkownikami](./media/syncplicity-tutorial/ic769764.png "Zarządzanie użytkownikami")

1. Typ **adresy E-mail** chcesz aprowizować konta usługi Azure AD, wybierz **użytkownika** jako **roli**, a następnie kliknij przycisk **dalej**.

    ![Informacje o koncie](./media/syncplicity-tutorial/ic769765.png "informacje o koncie")

    > [!NOTE]
    > Właściciel konta usługi AAD otrzymuje wiadomość e-mail, w tym link do potwierdzenia i aktywować konto.

1. Wybierz grupę w Twojej firmie, która nowy użytkownik powinien zostać członkiem, a następnie kliknij **dalej**.

    ![Członkostwo w grupie](./media/syncplicity-tutorial/ic769772.png "członkostwo w grupie")

    > [!NOTE]
    > Jeśli nie ma żadnych grup, na liście, kliknij przycisk **dalej**.

1. Wybierz foldery, o których chcesz umieścić pod kontrolą firmy Syncplicity na komputerze użytkownika, a następnie kliknij przycisk **dalej**.

    ![Foldery Syncplicity](./media/syncplicity-tutorial/ic769773.png "Syncplicity folderów")

> [!NOTE]
> Można użyć jakichkolwiek innych Syncplicity użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Syncplicity do aprowizacji kont użytkowników usługi AAD.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Syncplicity w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Syncplicity, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)