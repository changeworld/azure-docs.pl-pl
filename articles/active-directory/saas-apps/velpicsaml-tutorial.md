---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z velpic SAML | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a velpic SAML.
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
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72241563"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (SSO) z velpic SAML

W tym samouczku dowiesz się, jak zintegrować velpic SAML z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Velpic SAML z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Velpic SAML.
* Włącz użytkownikom automatyczne logowanie do velpic SAML za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Velpic SAML jednokrotnego logowania (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Velpic SAML **obsługuje** sp zainicjowane SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Dodawanie Velpic SAML z galerii

Aby skonfigurować integrację Velpic SAML z usługą Azure AD, należy dodać Velpic SAML z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Velpic SAML** w polu wyszukiwania.
1. Wybierz **Velpic SAML** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla velpic SAML

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą velpic SAML przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w velpic SAML.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z velpic SAML, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj velpic SAML SSO](#configure-velpic-saml-sso)** - aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Velpic SAML](#create-velpic-saml-test-user)** — aby mieć odpowiednik B.Simon w Velpic SAML, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **VELPIC SAML** znajdź sekcję **Zarządzaj** i wybierz **opcję logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<sub-domain>.velpicsaml.net`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Należy pamiętać, że adres URL logowania zostanie podany przez zespół Velpic SAML, a wartość identyfikatora będzie dostępna po skonfigurowaniu wtyczki SSO po stronie Velpic SAML. Musisz skopiować tę wartość ze strony aplikacji Velpic SAML i wkleić ją tutaj.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** znajdź kod **XML metadanych federacji** i wybierz pozycję **Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

1. W sekcji **Konfigurowanie velpic SAML** skopiuj odpowiednie adresy URL na podstawie wymagań.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Velpic SAML.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz **velpic SAML**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-velpic-saml-sso"></a>Konfigurowanie velpic SAML SSO

1. Aby zautomatyzować konfigurację w velpic SAML, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij Przycisk **Ustaw Velpic SAML** przekieruje Cię do aplikacji Velpic SAML. Stamtąd podaj poświadczenia administratora, aby zalogować się do Velpic SAML. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-8.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować velpic SAML, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Velpic SAML jako administrator i wykonaj następujące czynności:

4. Kliknij na **zakasanie,** przejdź do sekcji **Integracja,** w której musisz kliknąć przycisk **Wtyczki,** aby utworzyć nową wtyczkę do logowania.

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_1.png)

5. Kliknij przycisk **"Dodaj wtyczkę".**
    
    ![Wtyczka](./media/velpicsaml-tutorial/velpic_2.png)

6. Kliknij kafelek **SAML** na stronie Dodaj wtyczkę.
    
    ![Wtyczka](./media/velpicsaml-tutorial/velpic_3.png)

7. Wpisz nazwę nowej wtyczki SAML i kliknij przycisk **"Dodaj".**

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_4.png)

8. Wprowadź szczegóły w następujący sposób:

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_5.png)

    a. W polach tekstowych **Nazwa** wpisz nazwę wtyczki SAML.

    b. W pola tekstowym **adresu URL wystawcy** wklej **identyfikator usługi Azure AD** skopiowany z okna **konfigurowania logowania w witrynie** Azure portal.

    d. W **konfiguracji metadanych dostawcy** przekaż plik XML metadanych, który został pobrany z witryny Azure portal.

    d. Możesz również włączyć SAML tylko w czasie inicjowania obsługi administracyjnej, włączając pole wyboru **"Automatyczne tworzenie nowych użytkowników".** Jeśli użytkownik nie istnieje w Velpic i ta flaga nie jest włączona, logowania z platformy Azure zakończy się niepowodzeniem. Jeśli flaga jest włączona, użytkownik zostanie automatycznie aprowizowana do Velpic w momencie logowania. 

    e. Skopiuj **pojedynczy znak na adresie URL** z pola tekstowego i wklej go w witrynie Azure portal.
    
    f. Kliknij przycisk **Zapisz**.

### <a name="create-velpic-saml-test-user"></a>Utwórz użytkownika testowego Velpic SAML

Ten krok zwykle nie jest wymagane, ponieważ aplikacja obsługuje tylko w czasie inicjowania obsługi administracyjnej użytkownika. Jeśli automatyczne inicjowanie obsługi administracyjnej użytkownika nie jest włączone, można wykonać ręczne tworzenie przez użytkownika w sposób opisany poniżej.

Zaloguj się do witryny firmy Velpic SAML jako administrator i wykonaj następujące czynności:
    
1. Kliknij kartę Zarządzaj i przejdź do sekcji Użytkownicy, a następnie kliknij przycisk Nowy, aby dodać użytkowników.

    ![dodawanie użytkownika](./media/velpicsaml-tutorial/velpic_7.png)

2. Na stronie okna dialogowego **"Utwórz nowego użytkownika"** wykonaj następujące czynności.

    ![użytkownik](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. W polach tekstowych **Imię** wpisz imię B.

    b. W polach tekstowych **Nazwisko** wpisz nazwisko Szymona.

    d. W polach tekstowych **Nazwa użytkownika** wpisz nazwę użytkownika B.Simon.

    d. W **polach tekstowych Poczta e-mail** wpisz adres e-mail B.Simon@contoso.com konta.

    e. Reszta informacji jest opcjonalna, w razie potrzeby można je wypełnić.
    
    f. Kliknij **przycisk ZAPISZ**.

## <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

1. Po kliknięciu kafelka Velpic SAML w panelu dostępu należy uzyskać stronę logowania aplikacji Velpic SAML. Powinien zostać wyświetlony przycisk **"Zaloguj się za pomocą usługi Azure AD"** na stronie logowania.

    ![Wtyczka](./media/velpicsaml-tutorial/velpic_6.png)

1. Kliknij przycisk **"Zaloguj się za pomocą usługi Azure AD",** aby zalogować się do Velpic przy użyciu konta usługi Azure AD.

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj velpic SAML z usługą Azure AD](https://aad.portal.azure.com/)

