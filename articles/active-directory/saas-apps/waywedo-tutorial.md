---
title: 'Samouczek: Integracja usługi Azure Active Directory ze sposobem, w jaki robimy | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a sposobem, w jaki to robimy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67310405"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Samouczek: Integruj sposób, w jaki robimy z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować sposób, w jaki robimy z usługą Azure Active Directory (Azure AD). Po zintegrowaniu way we do z usługą Azure AD, można:

* Kontrola w usłudze Azure AD, który ma dostęp do way we do.
* Włącz użytkownikom automatyczne logowanie się do sposobu, w jaki robimy z ich kontami usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Way We Do subskrypcji logowania jednokrotnego (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Way We Do **obsługuje** sp zainicjowane SSO
* Way We Do obsługuje **just in time** użytkownika inicjowania obsługi administracyjnej

## <a name="adding-way-we-do-from-the-gallery"></a>Dodawanie sposobu, w jaki robimy z galerii

Aby skonfigurować integrację sposobu, w jaki robimy w usłudze Azure AD, należy dodać sposób, w jaki robimy z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **way We Do** w polu wyszukiwania.
1. Wybierz **sposób, w jaki robimy** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie samouszeńców usługi Azure AD za pomocą funkcji Way We Do przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w sposób, w jaki to robimy.

Aby skonfigurować i przetestować samouszeńców usługi Azure AD za pomocą way we do, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj sposób, w jaki robimy logowanie jednokrotne](#configure-way-we-do-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Way We Do](#create-way-we-do-test-user)** — aby mieć odpowiednik Britta Simon w sposób robimy, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Sposób, w jaki wykonujemy,** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej Way We Do Client,](mailto:support@waywedo.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (raw)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificateraw.png)

1. W sekcji **Konfigurowanie sposobu, w jaki robimy,** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Skonfiguruj sposób, w jaki robimy sytą dla pracowników sytego

1. Aby zautomatyzować konfigurację w ramach Way We Do, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

1. Po dodaniu rozszerzenia do przeglądarki, kliknij **Setup Way We Do** skieruje Cię do aplikacji Way We Do. Stamtąd podaj poświadczenia administratora, aby zalogować się do Way We Do. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

1. Jeśli chcesz ręcznie skonfigurować way we do, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Way We Do jako administrator i wykonaj następujące czynności:

1. Kliknij **ikonę osoby** w prawym górnym rogu dowolnej strony w polu Way We Do, a następnie kliknij **pozycję Konto** w menu rozwijanym.

    ![Konto Way We Do](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Kliknij **ikonę menu,** aby otworzyć menu nawigacji push, a następnie kliknij **pozycję Zaloguj się jednokrotnie**.

    ![Sposób, w jaki robimy singiel](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Na stronie **Ustawienia logowania jednokrotnego** wykonaj następujące czynności:

    ![Sposób, w jaki oszczędzamy](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kliknij przełącznik **Włącz logowanie jednokrotne na** **Tak,** aby włączyć logowanie jednokrotne.

    b. W polach **tekstowych Nazwa logowania jednokrotnego** wprowadź swoje imię i nazwisko.

    d. W polu tekstowym **identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **Adres URL logowania jednokrotnego protokołu SAML** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure Portal.

    e. Prześlij certyfikat, klikając przycisk **wybierz** obok **pozycji Certyfikat**.

    f. **Ustawienia opcjonalne** -
    
    * Włącz hasła — gdy ta opcja jest wyłączona, regularne funkcje hasła dla Way We Do, aby użytkownicy mogli używać tylko logowania jednokrotnego.

    * Włącz automatyczne inicjowanie obsługi administracyjnej — po włączeniu, adres e-mail używany do logowania zostanie automatycznie porównany z listą użytkowników w sposób robimy. Jeśli adres e-mail nie jest zgodny z aktywnym użytkownikiem w way we do, automatycznie dodaje nowe konto użytkownika dla osoby logującej się, żądając brakujących informacji.

      > [!NOTE]
      > Użytkownicy dodani za pośrednictwem logowania jednokrotnego są dodawane jako użytkownicy ogólne i nie są przypisane rolę w systemie. Administrator może wejść i zmodyfikować swoją rolę zabezpieczeń jako edytor lub administrator, a także przypisać jedną lub kilka ról schematu organizacyjnego.

    g. Kliknij **przycisk Zapisz,** aby utrwalić ustawienia.

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

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do way we do.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz opcję **Sposób, w jaki robimy**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-way-we-do-test-user"></a>Utwórz sposób, w jaki testujemy użytkownika

W tej sekcji użytkownik o nazwie Britta Simon jest tworzony w Sposób Robimy. Way We Do obsługuje just-in-time inicjowania obsługi administracyjnej użytkowników, który jest domyślnie włączony. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w sposób robimy, nowy jest tworzony po uwierzytelnieniu.

> [!Note]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Way We Do Client](mailto:support@waywedo.com).

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Way We Do w Panelu dostępu należy automatycznie zalogować się do sposobu, w jaki robimy, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)