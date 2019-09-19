---
title: 'Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z programem Evernote | Microsoft Docs'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Evernote.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55216e9eb4bd5cda2635564480751459f05e8309
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/19/2019
ms.locfileid: "71121624"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-evernote"></a>Samouczek: Azure Active Directory integrację logowania jednokrotnego (SSO) z programem Evernote

W tym samouczku dowiesz się, jak zintegrować program Evernote z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Evernote z usługą Azure AD można:

* Kontrolka w usłudze Azure AD, która ma dostęp do programu Evernote.
* Zezwól użytkownikom na automatyczne logowanie do programu Evernote przy użyciu kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* Subskrypcja z włączonym logowaniem jednokrotnym (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Program Evernote obsługuje usługę **SP i dostawcy tożsamości** zainicjowane Logowanie jednokrotne

> [!NOTE]
> Identyfikator tej aplikacji to stała wartość ciągu, dlatego można skonfigurować tylko jedno wystąpienie w jednej dzierżawie.

## <a name="adding-evernote-from-the-gallery"></a>Dodawanie aplikacji Evernote z galerii

Aby skonfigurować integrację aplikacji Evernote z usługą Azure AD, należy dodać aplikację Evernote z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz usługę **Azure Active Directory** .
1. Przejdź do **aplikacji przedsiębiorstwa** , a następnie wybierz pozycję **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Evernote** w polu wyszukiwania.
1. Wybierz opcję **Evernote** z panelu wyników, a następnie Dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-evernote"></a>Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD dla programu Evernote

Skonfiguruj i przetestuj Logowanie jednokrotne usługi Azure AD za pomocą programu Evernote przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD i powiązanym użytkownikiem w programie Evernote.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą programu Evernote, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj Logowanie jednokrotne usługi Azure AD](#configure-azure-ad-sso)** , aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować Logowanie jednokrotne w usłudze Azure AD za pomocą usługi B. Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić usłudze B. Simon korzystanie z logowania jednokrotnego w usłudze Azure AD.
1. **[Skonfiguruj rejestrację jednokrotną programu Evernote](#configure-evernote-sso)** , aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
    1. **[Utwórz użytkownika testowego programu Evernote](#create-evernote-test-user)** , aby dysponować odpowiednikiem B. Simon w programie Evernote, który jest połączony z reprezentacją użytkownika w usłudze Azure AD.
1. **[Przetestuj logowanie](#test-sso)** jednokrotne — aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć logowanie jednokrotne usługi Azure AD w Azure Portal.

1. W [Azure Portal](https://portal.azure.com/)na stronie integracja z aplikacją **Evernote** Znajdź sekcję **Zarządzanie** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** kliknij ikonę Edytuj/pióro, aby określić **podstawową konfigurację języka SAML** , aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Jeśli chcesz skonfigurować aplikację w trybie inicjalizacji **dostawcy tożsamości** , w sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    W polu tekstowym **Identyfikator** wpisz adres URL: `https://www.evernote.com/saml2`

1. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    W polu tekstowym **Adres URL logowania** wpisz adres URL: `https://www.evernote.com/Login.action`

1. Na stronie **Konfigurowanie logowania jednokrotnego przy użyciu języka SAML** w sekcji **certyfikat podpisywania SAML** Znajdź **certyfikat (base64)** i wybierz pozycję **Pobierz** , aby pobrać certyfikat i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

7. Aby zmodyfikować opcje **podpisywania** , kliknij przycisk **Edytuj** , aby otworzyć okno dialogowe **certyfikat podpisywania SAML** .

    ![image](common/edit-certificate.png) 

    ![image](./media/evernote-tutorial/samlassertion.png)

    a. Wybierz opcję **podpisanie odpowiedzi SAML i potwierdzenie** dla **opcji podpisywania**.

    b. Kliknij polecenie **Zapisz**.

1. W sekcji **Konfigurowanie programu Evernote** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w Azure Portal o nazwie B. Simon.

1. W lewym okienku w Azure Portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. We właściwościach **użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** wprowadź wartość username@companydomain.extension. Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz usługę B. Simon, aby korzystać z logowania jednokrotnego na platformie Azure przez przyznanie dostępu do programu Evernote.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Evernote**.
1. Na stronie Przegląd aplikacji Znajdź sekcję **Zarządzanie** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz pozycję **Dodaj użytkownika**, a następnie w oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy** .

    ![Link Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w potwierdzeniu SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** w dolnej części ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-evernote-sso"></a>Konfigurowanie rejestracji jednokrotnej w programie Evernote

1. Aby zautomatyzować konfigurację w programie Evernote, należy zainstalować **Moje aplikacje bezpieczne logowanie do przeglądarki** , klikając pozycję **Zainstaluj rozszerzenie**.

    ![Rozszerzenie moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij pozycję **Konfiguracja programu Evernote** przekieruje Cię do aplikacji Evernote. Z tego miejsca podaj poświadczenia administratora, aby zalogować się do programu Evernote. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i automatyzuje kroki 3-6.

    ![Konfiguracja konfiguracji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Evernote, Otwórz nowe okno przeglądarki sieci Web i zaloguj się w witrynie firmy Evernote jako administrator i wykonaj następujące czynności:

4. Przejdź do pozycji **Admin Console** (Konsola administracyjna)

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

5. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Security** (Zabezpieczenia) i wybierz pozycję **Single Sign-On** (Logowanie jednokrotne)

    ![Ustawienia logowania jednokrotnego](./media/evernote-tutorial/tutorial_evernote_sso.png)

6. Skonfiguruj następujące wartości:

    ![Ustawienia certyfikatu](./media/evernote-tutorial/tutorial_evernote_certx.png)
    
    a.  **Włącz logowanie jednokrotne:** Logowanie jednokrotne jest domyślnie włączone (kliknij pozycję **Disable Single Sign-on** (Wyłącz logowanie jednokrotne), aby usunąć wymaganie logowania jednokrotnego)

    b. W polu tekstowym **SAML HTTP Request URL** (Adres URL żądań HTTP SAML) wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    c. Otwórz w Notatniku certyfikat pobrany z usługi Azure AD, skopiuj zawartość włącznie z pozycjami „BEGIN CERTIFICATE” i „END CERTIFICATE”, a następnie wklej ją w polu tekstowym **X.509 Certificate** (Certyfikat X.509). 

    d. Kliknij pozycję **Save Changes** (Zapisz zmiany)

### <a name="create-evernote-test-user"></a>Tworzenie użytkownika testowego aplikacji Evernote

Aby umożliwić użytkownikom usługi Azure AD logowanie się do programu Evernote, muszą one być obsługiwane w programie Evernote.  
W aplikacji Evernote aprowizowanie jest zadaniem ręcznym.

**Aby aprowizować konta użytkowników, wykonaj następujące kroki:**

1. Zaloguj się do witryny firmy Evernote jako administrator.

2. Kliknij pozycję **Admin Console** (Konsola administracyjna).

    ![Konsola administracyjna](./media/evernote-tutorial/tutorial_evernote_adminconsole.png)

3. Z obszaru **Admin Console** (Konsola administracyjna) przejdź do pozycji **Add users** (Dodaj użytkowników).

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0001.png)

4. W obszarze **Add team members** (Dodaj członków zespołu) w polu tekstowym **Email** (Adres e-mail) wpisz adres e-mail konta użytkownika i kliknij pozycję **Invite** (Zaproś).

    ![Add-testUser](./media/evernote-tutorial/create_aaduser_0002.png)
    
5. Po wysłaniu zaproszenia właściciel konta usługi Azure Active Directory otrzyma wiadomość e-mail z prośbą o zaakceptowanie zaproszenia.

## <a name="test-sso"></a>Testuj Logowanie jednokrotne 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Evernote na panelu dostępu powinno nastąpić automatyczne zalogowanie do aplikacji Evernote, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj program Evernote z usługą Azure AD](https://aad.portal.azure.com/)

