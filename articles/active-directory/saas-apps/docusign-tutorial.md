---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą DocuSign | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306298"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z usługą DocuSign

W tym samouczku dowiesz się, jak zintegrować usługę DocuSign z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi DocuSign z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do DocuSign.
* Zezwól użytkownikom na automatyczne logowanie się do usługi DocuSign przy użyciu kont w usłudze Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO) DocuSign.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* DocuSign obsługuje logowanie jednokrotne w usłudze **SP**

* DocuSign obsługuje Inicjowanie obsługi użytkowników **just in Time**

* DocuSign obsługuje [Automatyczne Inicjowanie obsługi użytkowników](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>Dodawanie DocuSign z galerii

Aby skonfigurować integrację programu DocuSign z usługą Azure AD, musisz dodać DocuSign z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Docusign** w polu wyszukiwania.
1. Wybierz pozycję **Docusign** from panel wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Skonfiguruj i przetestuj Logowanie jednokrotne w usłudze Azure AD dla DocuSign

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą DocuSign przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w DocuSign.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą DocuSign, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj Logowanie jednokrotne](#configure-docusign-sso)** w usłudze Docusign, aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego Docusign](#create-docusign-test-user)** , aby dysponować odpowiednikiem B. Simon w Docusign, która jest połączona z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj Logowanie jednokrotne](#test-sso)** — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja aplikacji **Docusign** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja języka SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego adresu URL logowania i identyfikatora, który został wyjaśniony w dalszej części sekcji **punkty końcowe protokołu SAML 2,0** w samouczku.

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Docusign** skopiuj odpowiednie adresy URL na podstawie wymagania.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do usługi DocuSign.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście Aplikacje wybierz pozycję **Docusign**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-docusign-sso"></a>Konfigurowanie logowania jednokrotnego DocuSign

1. Aby zautomatyzować konfigurację w programie DocuSign, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki kliknij pozycję **Setup Docusign** , aby skierować do aplikacji Docusign. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do usługi DocuSign. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-5.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować DocuSign, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy DocuSign jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij pozycję **logo** profilu, a następnie kliknij pozycję **Przejdź do administratora**.
  
    ![Konfigurowanie logowania jednokrotnego][51]

5. Na stronie rozwiązania domeny kliknij pozycję **domeny** .

    ![Konfigurowanie logowania jednokrotnego][50]

6. W sekcji **domeny** kliknij pozycję **Przejmij domenę**.

    ![Konfigurowanie logowania jednokrotnego][52]

7. W oknie dialogowym Zażądaj **domeny** w polu tekstowym **nazwa domeny** wpisz domenę firmy, a następnie kliknij pozycję **Przejmij**. Upewnij się, że zweryfikowano domenę i że stan jest aktywny.

    ![Konfigurowanie logowania jednokrotnego][53]

8. Na stronie rozwiązania domeny kliknij pozycję **dostawcy tożsamości**.
  
    ![Konfigurowanie logowania jednokrotnego][54]

9. W sekcji **dostawcy tożsamości** kliknij pozycję **Dodaj dostawcę tożsamości**. 

    ![Konfigurowanie logowania jednokrotnego][55]

10. Na stronie **Ustawienia dostawcy tożsamości** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][56]

    a. W polu tekstowym **Nazwa** wpisz unikatową nazwę konfiguracji. Nie używaj spacji.

    b. W polu **tekstowym wystawca dostawcy tożsamości**wklej wartość identyfikatora usługi **Azure AD**, która została skopiowana z Azure Portal.

    c. W polu tekstowym **adres URL logowania dostawcy tożsamości** wklej wartość **adresu URL logowania**, która została skopiowana z Azure Portal.

    d. W polu tekstowym **adres URL wylogowania dostawcy tożsamości** wklej wartość **adresu URL wylogowania**, która została skopiowana z Azure Portal.

    e. Wybierz pozycję **Podpisz żądanie AuthN**.

    f. Jako **Wyślij żądanie AuthN przez**, wybierz pozycję **post**.

    g. Jako **Wyślij żądanie wylogowania przez**, wybierz pozycję **Pobierz**.

    h. W sekcji **Mapowanie atrybutów niestandardowych** kliknij pozycję **Dodaj nowe mapowanie**.

    ![Konfigurowanie logowania jednokrotnego][62]

    i. Wybierz pole, które chcesz zmapować z zastrzeżeniem usługi Azure AD. W tym przykładzie **EmailAddress** jest mapowany na wartość **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Jest to domyślna nazwa dla usługi Azure AD na potrzeby żądania poczty e-mail, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego][57]

    > [!NOTE]
    > Użyj odpowiedniego **identyfikatora użytkownika** , aby zamapować użytkownika z usługi Azure AD do Docusign mapowania użytkownika. Wybierz odpowiednie pole i wprowadź odpowiednią wartość na podstawie ustawień organizacji.

    j. W sekcji **Certyfikaty dostawcy tożsamości** kliknij pozycję **Dodaj certyfikat**, a następnie Przekaż certyfikat pobrany z portalu usługi Azure AD, a następnie kliknij przycisk **Zapisz**.

    ![Konfigurowanie logowania jednokrotnego][58]

    k. W sekcji **dostawcy tożsamości** kliknij pozycję **Akcje**, a następnie kliknij pozycję **punkty końcowe**.

    ![Konfigurowanie logowania jednokrotnego][59]

    l. W sekcji **Zobacz punkty końcowe protokołu SAML 2,0** w **portalu administracyjnym Docusign**wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego][60]

    * Skopiuj **adres URL wystawcy dostawcy usługi**, a następnie wklej go do pola tekstowego **Identyfikator** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    * Skopiuj **adres URL logowania dostawcy usługi**, a następnie wklej go do pola tekstowego **Zaloguj się w adresie URL** w sekcji **Podstawowa konfiguracja SAML** w Azure Portal.

    * Kliknij przycisk **Zamknij** .

### <a name="create-docusign-test-user"></a>Utwórz użytkownika testowego DocuSign

W tej sekcji użytkownik o nazwie B. Simon został utworzony w DocuSign. DocuSign obsługuje Inicjowanie obsługi użytkowników just in Time, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik nie istnieje jeszcze w usłudze DocuSign, zostanie utworzony nowy po uwierzytelnieniu.

>[!Note]
>Jeśli musisz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Docusign](https://support.docusign.com/).

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka DocuSign w panelu dostępu należy automatycznie zalogować się do DocuSign, dla którego skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj DocuSign z usługą Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png