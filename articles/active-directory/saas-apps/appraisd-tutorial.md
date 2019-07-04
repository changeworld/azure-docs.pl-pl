---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Appraisd | Microsoft Docs'
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
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561197"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Samouczek: Integracja Appraisd za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Appraisd w usłudze Azure Active Directory (Azure AD). W ramach Appraisd integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Appraisd.
* Umożliwianie użytkownikom można automatycznie zalogowany do Appraisd za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Appraisd logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Appraisd **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-appraisd-from-the-gallery"></a>Dodawanie aplikacji Appraisd z galerii

Aby skonfigurować integrację aplikacji Appraisd z usługą Azure AD, należy z poziomu galerii dodać tę aplikację do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Appraisd** w polu wyszukiwania.
1. Wybierz **Appraisd** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Appraisd za pomocą użytkownika testu o nazwie **B. Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Appraisd.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Appraisd, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Appraisd](#configure-appraisd)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą B. Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Simon B. korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Appraisd](#create-appraisd-test-user)**  mieć odpowiednikiem B. Simon Appraisd połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Appraisd** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając przycisk Zapisz i wykonaj następujące czynności:

    a. Kliknij pozycję **Ustaw dodatkowe adresy URL**.

    b. W polu tekstowym **Stan przekaźnika** wpisz adres URL: `<TENANTCODE>`

    c. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę usług**, w polu tekstowym **Adres URL logowania** wpisz adres URL przy użyciu następującego wzorca: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > Uzyskasz rzeczywisty adres URL logowania i wartość stanu przekaźnika na stronie konfiguracji logowania jednokrotnego aplikacji Appraisd, co zostało wyjaśnione w dalszej części tego samouczka.

1. Aplikacja Appraisd oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Appraisd oczekuje **nameidentifier** mają być mapowane z **user.mail**, więc należy edytować mapowanie atrybutów, klikając **Edytuj** ikonę i zmiany Mapowanie atrybutów.

    ![image](common/edit-attribute.png)

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie Appraisd** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Konfigurowanie Appraisd

1. Aby zautomatyzować konfigurację w ramach Appraisd, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Appraisd Instalatora** nastąpi bezpośrednie przekierowanie do aplikacji Appraisd. W tym miejscu należy podać poświadczenia administratora do logowania się do Appraisd. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Appraisd, Otwórz nowe okno przeglądarki sieci web i logowania do witryny firmy Appraisd z uprawnieniami administratora i wykonaj następujące czynności:

4. U góry po prawej stronie kliknij pozycję na **ustawienia** ikonę, a następnie przejdź do **konfiguracji**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. Po lewej stronie menu kliknij pozycję **SAML single sign-on** (Logowanie jednokrotne SAML).

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Na stronie **SAML 2.0 Single Sign-On configuration** (Konfiguracja logowania jednokrotnego SAML 2.0) wykonaj następujące kroki:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Kopiuj **domyślny stan przekazywania** wartości, a następnie wklej je **tan przekaźnika** polu tekstowym w **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

    b. Kopiuj **adres URL logowania zainicjowanych przez usługę** wartości, a następnie wklej je **adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** w witrynie Azure portal.

7. Przewiń tę samą stronę do pozycji **Identifying users** (Identyfikowanie użytkowników) i wykonaj następujące kroki:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. W polu tekstowym **Identity Provider Single Sign-On URL** (Adres URL logowania jednokrotnego dostawcy tożsamości) wklej wartość **Adres URL logowania** skopiowaną z witryny Azure Portal, a następnie kliknij przycisk **Save** (Zapisz).

    b. W **adres URL wystawcy dostawcy tożsamości** pola tekstowego, Wklej wartość **usługi Azure AD identyfikator**, skopiowanej w witrynie Azure portal, a następnie kliknij **Zapisz**.

    c. W programie Notatnik, otwórz certyfikat zakodowany base-64, pobrany z witryny Azure portal, skopiuj jego zawartość i następnie wklej go do **certyfikat X.509** pole, a następnie kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B. Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `B. Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Simon B. do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Appraisd.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Appraisd**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B. Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-appraisd-test-user"></a>Tworzenie użytkownika testowego aplikacji Appraisd

Aby umożliwić użytkownikom usługi Azure AD logowanie się w aplikacji Appraisd, należy ich aprowizować w tej aplikacji. W aplikacji Appraisd aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do aplikacji Appraisd jako administrator zabezpieczeń.

2. U góry po prawej stronie kliknij pozycję na **ustawienia** ikonę, a następnie przejdź do **Centrum administracyjne**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Na pasku narzędzi w górnej części strony kliknij **osób**, a następnie przejdź do **dodać nowego użytkownika**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Na stronie **Add a new user** (Dodawanie nowego użytkownika) wykonaj następujące kroki:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. W polu tekstowym **First name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    b. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    c. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak `B. Simon@contoso.com`.

    d. Kliknij pozycję **Dodaj użytkownika**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Appraisd w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Appraisd, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
