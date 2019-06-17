---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Freedcamp | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Freedcamp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: bfc73563-017d-458f-b634-162f93e03b74
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4de1ae135df4cd070fe9c6ee322e304caa1e17c9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67101911"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Samouczek: Integracja Freedcamp za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Freedcamp w usłudze Azure Active Directory (Azure AD). W ramach Freedcamp integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Freedcamp.
* Umożliwianie użytkownikom można automatycznie zalogowany do Freedcamp za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* Freedcamp logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje Freedcamp **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez.

## <a name="adding-freedcamp-from-the-gallery"></a>Dodawanie Freedcamp z galerii

Aby skonfigurować integrację Freedcamp w usłudze Azure AD, należy dodać Freedcamp z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Freedcamp** w polu wyszukiwania.
1. Wybierz **Freedcamp** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Freedcamp za pomocą użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Freedcamp.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Freedcamp, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Freedcamp](#configure-freedcamp)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Freedcamp](#create-freedcamp-test-user)**  mieć odpowiednikiem Britta Simon Freedcamp połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Freedcamp** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    1. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Użytkownicy mogą również wprowadzać wartości adresu url w odniesieniu do swojej własnej domenie klienta i nie mogą być koniecznie wzorca `freedcamp.com`, mogą wprowadzać dowolne klienta domeny określonej wartości, specyficzne dla ich wystąpienia aplikacji. Ponadto możesz skontaktować się ze [zespołem pomocy technicznej klienta Freedcamp](mailto:devops@freedcamp.com) więcej informacji na temat wzorców adresów url.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Na **Konfigurowanie Freedcamp** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurowanie Freedcamp

1. Aby zautomatyzować konfigurację w ramach Freedcamp, musisz zainstalować **rozszerzenia przeglądarki do bezpiecznego Moje aplikacje logowania** , klikając **zainstalować rozszerzenie**.

    ![Moje rozszerzenie aplikacji](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Freedcamp Instalatora** nastąpi bezpośrednie przekierowanie do aplikacji Freedcamp. W tym miejscu należy podać poświadczenia administratora do logowania się do Freedcamp. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzować kroki 3 – 5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Freedcamp, Otwórz nowe okno przeglądarki sieci web i logowania do witryny firmy Freedcamp z uprawnieniami administratora i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij **profilu** , a następnie przejdź do **Moje konto**.

    ![Konfiguracja Freedcamp](./media/freedcamp-tutorial/config01.png)

5. Z lewej strony paska menu, kliknij pozycję **logowania jednokrotnego** i **połączenia usługi logowania jednokrotnego** strony należy wykonać następujące czynności:

    ![Konfiguracja Freedcamp](./media/freedcamp-tutorial/config02.png)

    a. W **tytuł** tekstu wpisz tytuł.

    b. W **identyfikator jednostki** pole tekstowe, Wklej **usługi Azure AD identyfikator** wartości, które zostały skopiowane z witryny Azure portal.

    c. W **adres URL logowania** pole tekstowe, Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal.

    d. Otwórz certyfikat zakodowany w formacie Base64 w programie Notatnik, skopiuj jego zawartość i wklej go do **certyfikatu** pola tekstowego.

    e. Kliknij przycisk **Submit** (Prześlij).

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

W tej sekcji można udostępnić Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Freedcamp.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Freedcamp**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-freedcamp-test-user"></a>Tworzenie użytkownika testowego Freedcamp

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się w celu Freedcamp, ich musi być obsługiwana w Freedcamp. W Freedcamp Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. W oknie przeglądarki internetowej innej Zaloguj się do Freedcamp jako Administrator zabezpieczeń.

2. Na toright górnym rogu strony kliknij **profilu** , a następnie przejdź do **System zarządzania**.

    ![Konfiguracja Freedcamp](./media/freedcamp-tutorial/config03.png)

3. Po prawej stronie na stronie System zarządzania wykonaj następujące czynności:

    ![Konfiguracja Freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Kliknij pozycję **apletu Dodaj lub zaproszenia użytkowników**.

    b. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak `Brittasimon@contoso.com`.

    c. Kliknij przycisk **Dodaj użytkownika**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Freedcamp w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Freedcamp, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)