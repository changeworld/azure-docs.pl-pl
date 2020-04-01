---
title: 'Samouczek: Integracja usługi Azure Active Directory z Freedcamp | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Freedcamp.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67101911"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>Samouczek: Integracja Freedcamp z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować Freedcamp z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Freedcamp z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Freedcamp.
* Włącz użytkownikom automatyczne logowanie do Freedcamp za pomocą swoich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Freedcamp rejestracji jednokrotnej (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Freedcamp obsługuje **sp i IDP** zainicjowane SSO.

## <a name="adding-freedcamp-from-the-gallery"></a>Dodawanie Freedcamp z galerii

Aby skonfigurować integrację Freedcamp z usługą Azure AD, należy dodać Freedcamp z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Freedcamp** w polu wyszukiwania.
1. Wybierz **pozycję Freedcamp** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO z Freedcamp przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Freedcamp.

Aby skonfigurować i przetestować przychylić się do usługi Azure AD z freedcamp, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj Freedcamp,](#configure-freedcamp)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Freedcamp,](#create-freedcamp-test-user)** aby mieć odpowiednik Britta Simon w Freedcamp, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Freedcamp** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>`

    2. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>`

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<SUBDOMAIN>.freedcamp.com/login`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Należy je zastąpić rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Użytkownicy mogą również wprowadzić wartości adresów URL w odniesieniu do własnej `freedcamp.com`domeny klienta i mogą nie być koniecznie wzorca , mogą wprowadzić dowolną wartość specyficzne dla domeny klienta, specyficzne dla ich wystąpienia aplikacji. Możesz również skontaktować się z [zespołem pomocy technicznej Freedcamp Client,](mailto:devops@freedcamp.com) aby uzyskać więcej informacji na temat wzorców adresów URL.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Freedcamp** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-freedcamp"></a>Konfigurowanie freedcamp

1. Aby zautomatyzować konfigurację w Freedcamp, musisz zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Instalator Freedcamp** skieruje Cię do aplikacji Freedcamp. Stamtąd podaj poświadczenia administratora, aby zalogować się do Freedcamp. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-5.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować Freedcamp, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Freedcamp jako administrator i wykonaj następujące czynności:

4. W prawym górnym rogu strony kliknij **profil,** a następnie przejdź do **pozycji Moje konto**.

    ![Konfiguracja freedcamp](./media/freedcamp-tutorial/config01.png)

5. Po lewej stronie paska menu kliknij jedno i jedno **i** kolejne godziny na stronie **Połączenia jednosojowe** wykonaj następujące czynności:

    ![Konfiguracja freedcamp](./media/freedcamp-tutorial/config02.png)

    a. W polu tekstowym **Tytuł** wpisz tytuł.

    b. W polu **tekstowym identyfikator jednostki** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. W polu tekstowym **Adres URL logowania** wklej wartość adresu URL **logowania,** która została skopiowana z witryny Azure portal.

    d. Otwórz zakodowany certyfikat Base64 w notatniku, skopiuj jego zawartość i wklej go do pola tekstowego **Certyfikat.**

    e. Kliknij **przycisk Prześlij**.

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

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Freedcamp.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Freedcamp**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-freedcamp-test-user"></a>Utwórz użytkownika testowego Freedcamp

Aby włączyć użytkowników usługi Azure AD, zaloguj się do Freedcamp, muszą one być aprowizować w Freedcamp. W Freedcamp inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. W innym oknie przeglądarki internetowej zaloguj się do Freedcamp jako administrator zabezpieczeń.

2. W górnym rogu strony kliknij **profil,** a następnie przejdź do **pozycji Zarządzaj systemem**.

    ![Konfiguracja freedcamp](./media/freedcamp-tutorial/config03.png)

3. Po prawej stronie strony Zarządzanie systemem wykonaj następujące czynności:

    ![Konfiguracja freedcamp](./media/freedcamp-tutorial/config04.png)

    a. Kliknij **dodaj lub zaproś użytkowników**.

    b. W polu tekstowym **Poczta e-mail** wprowadź adres e-mail użytkownika w stylu `Brittasimon@contoso.com`.

    d. Kliknij **pozycję Dodaj użytkownika**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Freedcamp w Panelu dostępu należy automatycznie zalogować się do Freedcamp, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)