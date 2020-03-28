---
title: 'Samouczek: Integracja usługi Azure Active Directory z oceną | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Appraisd.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08453928ab000cf906c451fa6c1cd619a00ee4ca
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67561197"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Samouczek: Integrowanie oceny z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować ocenę z usługą Azure Active Directory (Azure AD). Po zintegrowaniu oceny z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do oceny.
* Włącz użytkownikom automatycznie zalogowany do oceny za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Oceniona subskrypcja z włączoną rejestracją jednokrotną (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Ocena obsługuje sp **i idp** zainicjowane SSO.

## <a name="adding-appraisd-from-the-gallery"></a>Dodawanie aplikacji Appraisd z galerii

Aby skonfigurować integrację aplikacji Appraisd z usługą Azure AD, należy z poziomu galerii dodać tę aplikację do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Oznacz w** polu wyszukiwania.
1. Wybierz **pozycję Ocena** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą oceny przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby użytkownik łączony sytuował działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w aplikacji Appraisd.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z oceną, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj ocenę,](#configure-appraisd)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego oceny,](#create-appraisd-test-user)** aby mieć odpowiednik B. Simon w ocenie, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie **Integracja** aplikacji oceny znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML** aplikacja jest wstępnie skonfigurowana, a niezbędne adresy URL są już wstępnie wypełnione platformą Azure. Użytkownik musi zapisać konfigurację, klikając przycisk Zapisz i wykonać następujące czynności:

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    b. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `<TENANTCODE>`

    d. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, w polu tekstowym **Adres URL logowania** wpisz adres URL przy użyciu następującego wzorca: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Uzyskasz rzeczywisty adres URL logowania i wartość stanu przekaźnika na stronie konfiguracji logowania jednokrotnego aplikacji Appraisd, co zostało wyjaśnione w dalszej części tego samouczka.

1. Aplikacja oceny oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodawania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja app oczekuje **nameidentifier** być mapowane z **user.mail**, więc trzeba edytować mapowanie atrybutów, klikając na **Edytuj** ikonę i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie oceny skopiuj** odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurowanie oceny

1. Aby zautomatyzować konfigurację w ramach oceny, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator Wycena** przekieruje Cię do aplikacji Oceny. W tym miejscu podaj poświadczenia administratora, aby zalogować się do oceny. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować ocenę ręcznie, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Appraisd jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij ikonę **Ustawienia,** a następnie przejdź do **pozycji Konfiguracja**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Po lewej stronie menu kliknij pozycję **SAML single sign-on** (Logowanie jednokrotne SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na stronie **SAML 2.0 Single Sign-On configuration** (Konfiguracja logowania jednokrotnego SAML 2.0) wykonaj następujące kroki:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Skopiuj domyślną wartość **stanu przekazywania** i wklej ją w polu **tekstowym stanu przekazywania** w **podstawowej konfiguracji SAML** w witrynie Azure portal.

    b. Skopiuj wartość **adresu URL logowania inicjowanego przez usługę** i wklej ją w polu **tekstowym Adresu URL logowania** w **podstawowej konfiguracji SAML** w witrynie Azure portal.

7. Przewiń tę samą stronę do pozycji **Identifying users** (Identyfikowanie użytkowników) i wykonaj następujące kroki:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. W polu tekstowym **Identity Provider Single Sign-On URL** (Adres URL logowania jednokrotnego dostawcy tożsamości) wklej wartość **Adres URL logowania** skopiowaną z witryny Azure Portal, a następnie kliknij przycisk **Save** (Zapisz).

    b. W polu tekstowym **adresu URL wystawcy dostawcy tożsamości** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z portalu Azure i kliknij przycisk **Zapisz**.

    d. W Notatniku otwórz zakodowany certyfikat base-64 pobrany z witryny Azure Portal, skopiuj jego zawartość, a następnie wklej go do pola **Certyfikat X.509** i kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B. Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do oceny.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Appraisd**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-appraisd-test-user"></a>Tworzenie użytkownika testowego aplikacji Appraisd

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji Appraisd, należy ich aprowizować w tej aplikacji. W aplikacji Appraisd aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do aplikacji Appraisd jako administrator zabezpieczeń.

2. W prawym górnym rogu strony kliknij ikonę **Ustawienia,** a następnie przejdź do **centrum administracyjnego**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na pasku narzędzi u góry strony kliknij pozycję **Kontakty**, a następnie przejdź do **pozycji Dodaj nowego użytkownika**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na stronie **Add a new user** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. W polu tekstowym **Imię** wprowadź imię użytkownika, takiego jak **Britta**.

    b. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **simon**.

    d. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `B. Simon@contoso.com`.

    d. Kliknij **pozycję Dodaj użytkownika**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Oceny w Panelu dostępu należy automatycznie zalogować się do oceny, dla której skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
