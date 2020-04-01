---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą BlueJeans dla usługi Azure AD | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a bluejeans dla usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595044"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą BlueJeans dla usługi Azure AD

W tym samouczku dowiesz się, jak zintegrować BlueJeans for Azure AD z usługą Azure Active Directory (Azure AD). Po zintegrowaniu BlueJeans for Azure AD z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do BlueJeans dla usługi Azure AD.
* Włącz użytkownikom automatyczne logowanie się do usługi BlueJeans dla usługi Azure AD za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego usługi BlueJeans for Azure AD.BlueJeans for Azure AD single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* BlueJeans for Azure AD obsługuje sygosk SSO inicjowane przez usługę **SP**

* BlueJeans for Azure AD obsługuje [ **automatyczne** inicjowanie obsługi administracyjnej użytkowników](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Identyfikator tej aplikacji jest stałą wartością ciągu, więc tylko jedno wystąpienie można skonfigurować w jednej dzierżawie.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>Dodawanie bluejeans dla usługi Azure AD z galerii

Aby skonfigurować integrację BlueJeans for Azure AD z usługą Azure AD, należy dodać BlueJeans for Azure AD z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **BlueJeans for Azure AD** w polu wyszukiwania.
1. Wybierz **bluejeans dla usługi Azure AD** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla usługi BlueJeans dla usługi Azure AD

Konfigurowanie i testowanie samoustawu SSO usługi Azure AD za pomocą usługi BlueJeans for Azure AD przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik samouszeńców działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w bluejeans dla usługi Azure AD.

Aby skonfigurować i przetestować samoustawę samoustawniczą usługi Azure AD przy za pomocą usługi BlueJeans for Azure AD, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj bluejeans dla usługi Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz BlueJeans dla użytkownika testowego usługi Azure AD](#create-bluejeans-for-azure-ad-test-user)** — aby mieć odpowiednik B.Simon w BlueJeans dla usługi Azure AD, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Usługi BlueJeans for Azure AD** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej BlueJeans dla klienta usługi Azure AD,](https://support.bluejeans.com/contact) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie bluejeans dla usługi Azure AD** skopiuj odpowiednie adresy URL na podstawie wymagań.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do BlueJeans dla usługi Azure AD.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **bluejeans dla usługi Azure AD**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-bluejeans-for-azure-ad-sso"></a>Konfigurowanie usługi BlueJeans dla usługi Azure AD SSO

1. W innym oknie przeglądarki sieci Web zaloguj się do witryny firmy **BlueJeans for Azure AD** jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> GROUP SETTINGS (USTAWIENIA GRUPY) \> SECURITY (ZABEZPIECZENIA)**.

    ![Administracja](./media/bluejeans-tutorial/ic785868.png "Administrator")

3. W sekcji **SECURITY** (ZABEZPIECZENIA) wykonaj następujące czynności:

    ![Saml Jednokrotne logowanie na](./media/bluejeans-tutorial/ic785869.png "Saml Jednokrotne logowanie na")

    a. Wybierz pozycję **SAML Single Sign On** (Logowanie jednokrotne SAML).

    b. Wybierz pozycję **Enable automatic provisioning** (Włącz automatyczną aprowizację).

4. Przejdź dalej, wykonując następujące czynności:

    ![Ścieżka certyfikatu](./media/bluejeans-tutorial/ic785870.png "Ścieżka certyfikatu")

    a. Kliknij pozycję **Choose File** (Wybierz plik), aby przekazać certyfikat zakodowany w formacie base-64, pobrany wcześniej z witryny Azure Portal.

    b. W polu tekstowym **Adres URL logowania** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Password Change URL** (Adres URL zmiany hasła) wklej wartość **adresu URL zmiany hasła** skopiowaną z witryny Azure Portal.

    d. W polu tekstowym **Logout URL** (Adres URL wylogowywania) wklej wartość **adresu URL wylogowywania** skopiowaną z witryny Azure Portal.

5. Przejdź dalej, wykonując następujące czynności:

    ![Zapisz zmiany](./media/bluejeans-tutorial/ic785874.png "Zapisz zmiany")

    a. W polach tekstowych Identyfikator `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` **użytkownika** wpisz polecenie .

    b. W polu tekstowym **Email** (Adres e-mail) wpisz `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`.

    d. Kliknij przycisk **SAVE CHANGES** (ZAPISZ ZMIANY).

### <a name="create-bluejeans-for-azure-ad-test-user"></a>Tworzenie bluejeans dla użytkownika testowego usługi Azure AD

Celem tej sekcji jest utworzenie użytkownika o nazwie B.Simon w BlueJeans dla usługi Azure AD. BlueJeans for Azure AD obsługuje automatyczne inicjowanie obsługi administracyjnej użytkowników, która jest domyślnie włączona. Więcej szczegółów dotyczących konfigurowania automatycznego inicjowania obsługi użytkowników można znaleźć [tutaj](bluejeans-provisioning-tutorial.md).

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. Zaloguj się do witryny firmy **BlueJeans for Azure AD** jako administrator.

2. Wybierz pozycję **ADMIN (ADMINISTRATOR) \> MANAGE USERS (ZARZĄDZAJ UŻYTKOWNIKAMI) \> ADD USER (DODAJ UŻYTKOWNIKA)**.

    ![Administracja](./media/bluejeans-tutorial/ic785877.png "Administrator")

    > [!IMPORTANT]
    > Karta **ADD USER** (DODAJ UŻYTKOWNIKA) jest dostępna tylko wtedy, gdy na **karcie SECUTIRY** (ZABEZPIECZENIA) nie zaznaczono pola wyboru **Enable automatic provisioning** (Włącz automatyczną aprowizację).

3. W sekcji **DODAJ UŻYTKOWNIKA** wykonaj następujące czynności:

    ![Dodaj użytkownika](./media/bluejeans-tutorial/ic785886.png "Dodaj użytkownika")

    a. W polu **tekstowym Imię** wprowadź imię użytkownika, takiego jak **B**.

    b. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

    d. W polu **tekstowym Wybierz bluejeans dla usługi Azure AD Nazwa użytkownika** wprowadź nazwę użytkownika, takiego jak **Brittasimon**

    d. W polu tekstowym **Create a Password** (Utwórz hasło) wprowadź hasło.

    e. W polu tekstowym **Company** (Firma) wprowadź nazwę Twojej firmy.

    f. W polu tekstowym **Email Address** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `b.simon\@contoso.com`.

    g. W polu **tekstowym Utwórz bluejeans dla usługi Azure AD Meeting ID** wprowadź swój identyfikator spotkania.

    h. W polu tekstowym **Pick a Moderator Passcode** (Wybierz kod dostępu moderatora) wprowadź kod dostępu.

    i. Kliknij pozycję **CONTINUE** (KONTYNUUJ).

    ![Dodaj użytkownika](./media/bluejeans-tutorial/ic785887.png "Dodaj użytkownika")

    J. Kliknij przycisk **ADD USER** (DODAJ UŻYTKOWNIKA).

> [!NOTE]
> Można użyć innych narzędzi do tworzenia konta użytkownika usługi Azure AD lub interfejsów API dostarczonych przez BlueJeans for Azure AD do aprowizowania kont użytkowników usługi Azure AD.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu bluejeans dla usługi Azure AD kafelka w panelu dostępu, powinny być automatycznie zalogowane do BlueJeans dla usługi Azure AD, dla którego skonfigurowano logującą się. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj bluejeans dla usługi Azure AD z usługą Azure AD](https://aad.portal.azure.com/)

