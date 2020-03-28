---
title: 'Samouczek: Integracja usługi Azure Active Directory z syncplicity | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a syncplicity.
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
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233284"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Samouczek: Integrowanie synchronizacji z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować syncplicity z usługą Azure Active Directory (Azure AD). Po zintegrowaniu syncplicity z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Syncplicity.
* Włącz użytkownikom automatyczne logowanie do syncplicity za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z włączoną funkcją rejestracji jednokrotnej Syncplicity (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Syncplicity **obsługuje** sp zainicjowane SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Dodawanie syncplicity z galerii

Aby skonfigurować integrację syncplicity do usługi Azure AD, należy dodać Syncplicity z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Syncplicity** w polu wyszukiwania.
1. Wybierz **polecenie Syncplicity** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-sso"></a>Konfigurowanie i testowanie usługi Azure AD SSO

Konfigurowanie i testowanie usługi Azure AD SSO z synchronizacją przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Syncplicity.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD z pomocą syncplicity, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne syncplicity](#configure-syncplicity-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Syncplicity](#create-syncplicity-test-user)** — aby mieć odpowiednik B.Simon w Syncplicity, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Syncplicity** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Syncplicity,](https://www.syncplicity.com/contact-us) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie syncplicity** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Konfigurowanie usługi SYC syncplicity

1. Zaloguj się do **dzierżawy Syncplicity.**

1. W menu u góry kliknij pozycję **Administrator**, wybierz **ustawienia**, a następnie kliknij pozycję **Domena niestandardowa i logowanie jednokrotne**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Na stronie okna dialogowego **logowanie jednokrotne wykonaj** następujące czynności:

    ![Logowanie jednokrotne \(\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. W polach **tekstowych Domena niestandardowa** wpisz nazwę domeny.
  
    b. Wybierz **opcję Włączono** jako **stan logowania jednokrotnego**.

    d. W polu tekstowym **identyfikator jednostki** wklej wartość **identyfikatora (identyfikatora jednostki),** która została użyta w **podstawowej konfiguracji SAML** w witrynie Azure portal.

    d. W polu tekstowym **adresu URL strony logowania wklej** adres URL **logowania** skopiowany z witryny Azure Portal.

    e. W polu tekstowym **adresu URL strony wyloguj** **wklej adres URL wylogowania** skopiowany z witryny Azure Portal.

    f. W **polu Certyfikat dostawcy tożsamości**kliknij pozycję Wybierz **plik**, a następnie przekaż certyfikat pobrany z witryny Azure portal.

    g. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Syncplicity.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **syncplicity**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-syncplicity-test-user"></a>Utwórz użytkownika testowego Syncplicity

Aby użytkownicy usługi Azure AD mogli się zalogować, muszą być aprowizowany do aplikacji Syncplicity. W tej sekcji opisano sposób tworzenia kont użytkowników usługi Azure AD w syncplicity.

**Aby aprowizować konto użytkownika do Syncplicity, wykonaj następujące kroki:**

1. Zaloguj się do dzierżawy **Syncplicity** (na przykład: `https://company.Syncplicity.com`).

1. Kliknij **pozycję administrator** i wybierz pozycję Konta **użytkowników,** a następnie kliknij pozycję **DODAJ UŻYTKOWNIKA**.

    ![Zarządzanie użytkownikami](./media/syncplicity-tutorial/ic769764.png "Zarządzanie użytkownikami")

1. Wpisz **adresy e-mail** konta usługi Azure AD, które chcesz aprowizować, wybierz **pozycję Użytkownik** jako **rolę,** a następnie kliknij przycisk **DALEJ**.

    ![Informacje o koncie](./media/syncplicity-tutorial/ic769765.png "Informacje o koncie")

    > [!NOTE]
    > Posiadacz konta usługi Azure AD otrzymuje wiadomość e-mail z łączem potwierdzającym i aktywowanym kontem.

1. Wybierz grupę w firmie, do których powinien zostać nowy użytkownik, a następnie kliknij przycisk **DALEJ**.

    ![Członkostwo w grupie](./media/syncplicity-tutorial/ic769772.png "Członkostwo w grupie")

    > [!NOTE]
    > Jeśli na liście nie ma żadnych grup, kliknij przycisk **DALEJ**.

1. Wybierz foldery, które chcesz umieścić pod kontrolą Syncplicity na komputerze użytkownika, a następnie kliknij przycisk **DALEJ**.

    ![Foldery syncplicity](./media/syncplicity-tutorial/ic769773.png "Foldery syncplicity")

> [!NOTE]
> Do aprowizowania kont użytkowników usługi Azure AD można użyć innych narzędzi do tworzenia konta użytkownika Syncplicity lub interfejsów API dostarczonych przez syncplicity.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Syncplicity w Panelu dostępu należy automatycznie zalogować się do syncplicity, dla którego skonfigurowano logowanie logujące się. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)