---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą MyWorkDrive | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą MyWorkDrive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 4d049778-3c7b-46c0-92a4-f2633a32334b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: jeedes
ms.openlocfilehash: 60fdd9b0a8fb272da885df97e39804a98e48de67
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478867"
---
# <a name="tutorial-integrate-myworkdrive-with-azure-active-directory"></a>Samouczek: Integracja usługi MyWorkDrive z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę MyWorkDrive z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi MyWorkDrive z usługą Azure AD można:

* Kontrola w usłudze Azure AD, która ma dostęp do usługi MyWorkDrive.
* Włącz użytkownikom automatyczne logowanie się do usługi MyWorkDrive za pomocą kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja z włączoną funkcją logowania jednokrotnego usługi MyWorkDrive.MyWorkDrive single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. MyWorkDrive obsługuje **jednostkę** SSO inicjowane przez **sp** i idp

## <a name="adding-myworkdrive-from-the-gallery"></a>Dodawanie usługi MyWorkDrive z galerii

Aby skonfigurować integrację usługi MyWorkDrive z usługą Azure AD, należy dodać usługę MyWorkDrive z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **myWorkDrive** w polu wyszukiwania.
1. Wybierz **myworkdrive** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą usługi MyWorkDrive przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze MyWorkDrive.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi MyWorkDrive, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne usługi MyWorkDrive](#configure-myworkdrive-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego MyWorkDrive](#create-myworkdrive-test-user)** — aby mieć odpowiednik Britta Simon w MyWorkDrive, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [portalu Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **MyWorkDrive** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML,** Jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wprowadź wartości dla następującego pola:

    W polu tekstowym **Odpowiedz na adres URL** wpisz adres URL, używając następującego wzorca:`https://<SERVER.DOMAIN.COM>/SAML/AssertionConsumerService.aspx`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SERVER.DOMAIN.COM>/Account/Login-saml`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj je, używając faktycznego adresu URL odpowiedzi i adresu URL logowania. Wprowadź nazwę hosta serwera MyWorkDrive Server własnej firmy: np.
    > 
    > Adres URL odpowiedzi: `https://yourserver.yourdomain.com/SAML/AssertionConsumerService.aspx`
    > 
    > Adres URL logowania:`https://yourserver.yourdomain.com/Account/Login-saml`
    > 
    > Jeśli nie masz pewności, jak skonfigurować własną nazwę hosta i certyfikat TLS/SSL dla tych wartości, skontaktuj się z zespołem pomocy technicznej usługi [MyWorkDrive.](mailto:support@myworkdrive.com)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** kliknij przycisk kopiuj, aby skopiować **adres URL metadanych federacji aplikacji** do schowka.

    ![Link do pobierania certyfikatu](common/copy-metadataurl.png)

### <a name="configure-myworkdrive-sso"></a>Konfigurowanie usługi MyWorkDrive — sycowe

1. Aby zautomatyzować konfigurację w usłudze MyWorkDrive, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki kliknij **przycisk Ustawienia MyWorkDrive** spowoduje skierowanie użytkownika do aplikacji MyWorkDrive. W tym miejscu podaj poświadczenia administratora, aby zalogować się do usługi MyWorkDrive. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-4.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz skonfigurować usługę MyWorkDrive ręcznie, w innym oknie przeglądarki sieci Web zaloguj się do usługi MyWorkDrive jako administrator zabezpieczeń.

1. Na serwerze MyWorkDrive w panelu administracyjnym kliknij enterprise **i** wykonaj następujące czynności:

    ![Administrator](./media/myworkdrive-tutorial/tutorial_myworkdrive_admin.png)

    a. Włącz **szosę SSO SAML/ADFS**.

    b. Wybierz **SAML — usługa Azure AD**

    d. W polu tekstowym **Url metadanych metadanych aplikacji platformy Azure** wklej wartość adresu URL **metadanych federacji aplikacji** skopiowanego z witryny Azure portal.

    d. Kliknij **przycisk Zapisz**

    > [!NOTE]
    > Aby uzyskać dodatkowe informacje, zapoznaj się z [artykułem pomocy technicznej usługi MyWorkDrive Azure AD](https://www.myworkdrive.com/support/saml-single-sign-on-azure-ad/).

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

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Usługi MyWorkDrive.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **MyWorkDrive**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-myworkdrive-test-user"></a>Tworzenie użytkownika testowego Usługi MyWorkDrive

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w MyWorkDrive. Współpracuj z [zespołem pomocy technicznej MyWorkDrive,](mailto:support@myworkdrive.com) aby dodawać użytkowników na platformie MyWorkDrive. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka MyWorkDrive w Panelu dostępu należy automatycznie zalogować się do usługi MyWorkDrive, dla której skonfigurowano logowanie logowane. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)