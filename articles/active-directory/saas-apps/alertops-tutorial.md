---
title: 'Samouczek: Integracja usługi Azure Active Directory z alertops | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107055"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Samouczek: Integrowanie alertów z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować AlertOps z usługą Azure Active Directory (Azure AD). Podczas integrowania AlertOps z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do AlertOps.
* Włącz użytkownikom automatyczne logowanie do alertów za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego alertops.AlertOps single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. AlertOps obsługuje **sp i idp** zainicjowane SSO.

## <a name="adding-alertops-from-the-gallery"></a>Dodawanie aplikacji AlertOps z galerii

Aby skonfigurować integrację aplikacji AlertOps z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **AlertOps** w polu wyszukiwania.
1. Wybierz **alertops** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą alertops przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik sytuować uszy działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w alertops.

Aby skonfigurować i przetestować sytua usługi Azure AD z oś alertów, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj AlertOps,](#configure-alertops)** aby skonfigurować ustawienia logowania przy tym po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz alertops użytkownika testowego,](#create-alertops-test-user)** aby mieć odpowiednik Britta Simon w AlertOps, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **AlertOps** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<SUBDOMAIN>.alertops.com`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji AlertOps](mailto:support@alertops.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie alertów** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurowanie alertops

1. Aby zautomatyzować konfigurację w ramach AlertOps, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator AlertOps** przekieruje Cię do aplikacji AlertOps. W tym miejscu podaj poświadczenia administratora, aby zalogować się do AlertOps. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować alerty, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy AlertOps jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Account settings** (Ustawienia konta) w panelu nawigacji po lewej stronie.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/configure1.png)

5. Na stronie **Subscription Settings** (Ustawienia subskrypcji) wybierz pozycję **SSO** (Logowanie jednokrotne), a następnie wykonaj następujące kroki:

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/configure2.png)

    a. Zaznacz pole wyboru **Użyj logowania jednokrotnego Sign-On (SSO)**.

    b. Wybierz **usługę Azure Active Directory** jako **dostawcę usługi SSO** z listy rozwijanej.

    d. W polu tekstowym **adres URL wystawcy** użyj wartości identyfikatora, która została użyta w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    d. W polu tekstowym **adresu URL punktu końcowego SAML** wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal.

    e. W polu **tekstowym adresu URL punktu końcowego SLO** wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal.

    f. Z listy rozwijanej **SAML Signature Algorithm** (Algorytm podpisu SAML) wybierz pozycję **SHA256**.

    g. Otwórz pobrany plik Certificate(Base64) w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej go w polu tekstowym X.509 Certificate (Certyfikat X.509).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie Britta Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do AlertOps.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **AlertOps**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-alertops-test-user"></a>Tworzenie użytkownika testowego aplikacji AlertOps

1. W innym oknie przeglądarki zaloguj się do witryny firmy AlertOps jako administrator.

2. Kliknij pozycję **Users** (Użytkownicy) na panelu nawigacyjnym po lewej stronie.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user1.png)

3. Wybierz **pozycję Dodaj użytkownika**.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user2.png)

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user3.png)

    a. W polu tekstowym **Login User Name** (Nazwa użytkownika logowania) wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    b. W oficjalnym polu **tekstowym e-mail** wprowadź adres e-mail użytkownika, takiego jak **\@Brittasimon contoso.com**.

    d. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, na przykład **Britta**.

    d. W polu tekstowym **Last Name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    e. Wybierz wartość z listy rozwijanej **Type** (Typ) zgodnie z wymaganiami w Twojej organizacji.

    f. Wybierz wartość z listy rozwijanej **Role** (Rola) zgodnie z wymaganiami w Twojej organizacji.

    g. Wybierz pozycję **Dodaj**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka AlertOps w Panelu dostępu, należy automatycznie zalogować się do AlertOps dla których skonfigurowano logującą się logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)