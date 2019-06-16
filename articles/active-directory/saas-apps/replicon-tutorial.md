---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Replicon | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67092765"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Samouczek: Integracja Replicon za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Replicon w usłudze Azure Active Directory (Azure AD). W ramach Replicon integracji z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do Replicon.
* Umożliwianie użytkownikom można automatycznie zalogowany do Replicon za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać miesięcznej bezpłatnej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Replicon logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Obsługuje replicon **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-replicon-from-the-gallery"></a>Dodawanie Replicon z galerii

Aby skonfigurować integrację Replicon w usłudze Azure AD, należy dodać Replicon z galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Replicon** w polu wyszukiwania.
1. Wybierz **Replicon** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD za pomocą Replicon za pomocą użytkownika testu o nazwie **B.Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w Replicon.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD za pomocą Replicon, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie logowania jednokrotnego Replicon](#configure-replicon-sso)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą B.Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć B.Simon do użycia usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Replicon](#create-replicon-test-user)**  — aby odpowiednikiem B.Simon w Replicon połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Replicon** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://global.replicon.com/!/saml2/<client name>`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Replicon](https://www.replicon.com/customerzone/contact-support) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Kliknij ikonę edycji/pióra **certyfikat podpisywania SAML** edytować ustawienia.

    ![Algorytm podpisywania](common/signing-algorithm.png)

    1. Wybierz **potwierdzenie SAML logowania** jako **opcja podpisywania**.

    1. Wybierz **algorytmu SHA-256** jako **algorytm podpisywania**.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **XML metadanych Federacji** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Konfigurowanie Replicon logowania jednokrotnego

1. W oknie przeglądarki innej witryny sieci web należy zalogować się do witryny firmy Replicon jako administrator.

2. Aby skonfigurować SAML 2.0, wykonaj następujące czynności:

    ![Włącz uwierzytelnianie SAML](./media/replicon-tutorial/ic777805.png "uwierzytelnianie Włącz SAML")

    a. Aby wyświetlić **EnableSAML Authentication2** okno dialogowe, Dołącz następujący ciąg do adresu URL, po klucz firmy: `/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Na poniższym obrazie przedstawiono schemat pełny adres URL: `https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Kliknij przycisk **+** rozszerzania **v20Configuration** sekcji.

   c. Kliknij przycisk **+** rozszerzania **metaDataConfiguration** sekcji.

   d. Wybierz **SHA256** dla xmlSignatureAlgorithm

   e. Kliknij przycisk **wybierz plik**, aby wybrać plik XML metadanych dostawcy tożsamości, a następnie kliknij przycisk **przesyłania**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie B.Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić B.Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Replicon.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Replicon**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **B.Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-replicon-test-user"></a>Tworzenie użytkownika testowego Replicon

Celem tej sekcji jest, aby utworzyć użytkownika o nazwie B.Simon w Replicon.

**Jeśli potrzebujesz utworzyć użytkownika ręcznie, wykonaj następujące czynności:**

1. W oknie przeglądarki sieci web należy zalogować się do witryny firmy Replicon jako administrator.

2. Przejdź do **administracji \> użytkowników**.

    ![Użytkownicy](./media/replicon-tutorial/ic777806.png "Użytkownicy")

3. Kliknij przycisk **+ Dodaj użytkownika**.

    ![Dodawanie użytkownika](./media/replicon-tutorial/ic777807.png "Dodawanie użytkownika")

4. W **profilu użytkownika** sekcji, wykonaj następujące czynności:

    ![Profil użytkownika](./media/replicon-tutorial/ic777808.png "profilu użytkownika")

    a. W **nazwa logowania** polu tekstowym wpisz usługi Azure AD adres e-mail użytkownika usługi Azure AD, w którym chcesz udostępnić, takich jak `B.Simon@contoso.com`.

    > [!NOTE]
    > Nazwa logowania musi być zgodna adres e-mail użytkownika w usłudze Azure AD

    b. Jako **typ uwierzytelniania**, wybierz opcję **logowania jednokrotnego**.

    c. Ustaw identyfikator uwierzytelniania taką samą wartość jak nazwa logowania (Azure AD adres e-mail użytkownika)

    d. W **działu** polu tekstowym wpisz dział użytkownika.

    e. Jako **typu pracownika**, wybierz opcję **administratora**.

    f. Kliknij przycisk **Zapisz profil użytkownika**.

> [!NOTE]
> Można użyć jakichkolwiek innych Replicon użytkownika konta tworzenie narzędzi lub interfejsów API dostarczonych przez Replicon można uaktywniać ich konta usługi Azure AD.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Replicon w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Replicon, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)