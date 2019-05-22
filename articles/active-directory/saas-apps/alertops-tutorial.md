---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją AlertOps | Microsoft Docs'
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
ms.openlocfilehash: 124f69b3fd6944d0a6e3814f1bbfa5594bcd95cb
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65988405"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Samouczek: Integracja AlertOps za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować AlertOps w usłudze Azure Active Directory (Azure AD). W ramach AlertOps integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do AlertOps.
* Umożliwianie użytkownikom można automatycznie zalogowany do AlertOps za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* AlertOps logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje AlertOps **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-alertops-from-the-gallery"></a>Dodawanie aplikacji AlertOps z galerii

Aby skonfigurować integrację aplikacji AlertOps z usługą Azure AD, należy z poziomu galerii dodać to rozwiązanie do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **AlertOps** w polu wyszukiwania.
1. Wybierz **AlertOps** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą AlertOps za pomocą użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w AlertOps.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą AlertOps, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie AlertOps](#configure-alertops)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego AlertOps](#create-alertops-test-user)**  mieć odpowiednikiem Britta Simon AlertOps połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **AlertOps** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    1. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Skontaktuj się z [zespołem obsługi klienta aplikacji AlertOps](mailto:support@alertops.com) w celu uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie AlertOps** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>Konfigurowanie AlertOps

1. Aby zautomatyzować konfigurację w ramach AlertOps, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **AlertOps Instalatora** nastąpi bezpośrednie przekierowanie do aplikacji AlertOps. W tym miejscu należy podać poświadczenia administratora do logowania się do AlertOps. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki 3 – 5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować AlertOps, Otwórz nowe okno przeglądarki sieci web i zaloguj się do witryny firmy AlertOps jako administrator i wykonaj następujące czynności:

4. Kliknij pozycję **Account settings** (Ustawienia konta) w panelu nawigacji po lewej stronie.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/configure1.png)

5. Na stronie **Subscription Settings** (Ustawienia subskrypcji) wybierz pozycję **SSO** (Logowanie jednokrotne), a następnie wykonaj następujące kroki:

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/configure2.png)

    a. Zaznacz pole wyboru **Użyj logowania jednokrotnego Sign-On (SSO)**.

    b. Wybierz **usługi Azure Active Directory** jako **dostawcy logowania jednokrotnego** z listy rozwijanej.

    c. W **adres URL wystawcy** pola tekstowego, użyj wartości identyfikatora, który jest używany, **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    d. W **adresu URL punktu końcowego protokołu SAML** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    e. W **adresu URL punktu końcowego SLO** pola tekstowego, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    f. Z listy rozwijanej **SAML Signature Algorithm** (Algorytm podpisu SAML) wybierz pozycję **SHA256**.

    g. Otwórz pobrany plik Certificate(Base64) w Notatniku. Skopiuj jego zawartość do schowka, a następnie wklej go w polu tekstowym X.509 Certificate (Certyfikat X.509).

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do AlertOps.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **AlertOps**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-alertops-test-user"></a>Tworzenie użytkownika testowego aplikacji AlertOps

1. W oknie innej przeglądarki Zaloguj się w lokacji firmy AlertOps jako administrator.

2. Kliknij pozycję **Users** (Użytkownicy) na panelu nawigacyjnym po lewej stronie.

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user1.png)

3. Wybierz pozycję **Add User** (Dodaj użytkownika).

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user2.png)

4. W oknie dialogowym **Add User** (Dodawanie użytkownika) wykonaj następujące kroki:

    ![Konfigurowanie aplikacji AlertOps](./media/alertops-tutorial/user3.png)

    a. W polu tekstowym **Login User Name** (Nazwa użytkownika logowania) wprowadź nazwę użytkownika, na przykład **Brittasimon**.

    b. W **oficjalne wiadomości E-mail** polu tekstowym wprowadź adres e-mail użytkownika, takich jak **Brittasimon\@contoso.com**.

    c. W polu tekstowym **First Name** (Imię) wpisz imię użytkownika, na przykład **Britta**.

    d. W polu tekstowym **Last Name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

    e. Wybierz wartość z listy rozwijanej **Type** (Typ) zgodnie z wymaganiami w Twojej organizacji.

    f. Wybierz wartość z listy rozwijanej **Role** (Rola) zgodnie z wymaganiami w Twojej organizacji.

    g. Wybierz pozycję **Dodaj**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka AlertOps w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze AlertOps, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)