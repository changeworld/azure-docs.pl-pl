---
title: 'Samouczek: Integracja usługi Azure Active Directory z sytuałem jednokrotnym usługi Soloinsight-CloudGate | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Soloinsight-CloudGate SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b88822f164e0fe8cdf55eddfa981644f725e01f3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73159934"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Samouczek: Integracja funkcji SSO Soloinsight-CloudGate z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować sso Soloinsight-CloudGate z usługą Azure Active Directory (Azure AD). Po zintegrowaniu solinsight-CloudGate SSO z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do solinsight-CloudGate SSO.
* Włącz użytkownikom automatyczne logowanie do solinsight-CloudGate SSO za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać miesięczny bezpłatny okres próbny [tutaj](https://azure.microsoft.com/pricing/free-trial/).
* Subskrypcja jednokrotna logowania jednokrotnego (SSO) firmy Soloinsight-CloudGate.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Soloinsight-CloudGate SSO **obsługuje** sp inicjowane SSO.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Dodawanie aplikacji Soloinsight-CloudGate SSO z galerii

Aby skonfigurować integrację aplikacji Soloinsight-CloudGate SSO z usługą Azure AD, należy dodać aplikację Soloinsight-CloudGate SSO z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **solinsight-CloudGate SSO** w polu wyszukiwania.
1. Wybierz **soloinsight-CloudGate SSO** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą funkcji SSO Soloinsight-CloudGate przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik jednokrotny działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Solinsight-CloudGate SSO.

Aby skonfigurować i przetestować samouszeńców usługi Azure AD za pomocą usługi Soloinsight-CloudGate SSO, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj sytuacy Soloinsight-CloudGate,](#configure-soloinsight-cloudgate-sso)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz soloinsight-CloudGate SSO użytkownika testowego,](#create-soloinsight-cloudgate-sso-test-user)** aby mieć odpowiednik Britta Simon w Soloinsight-CloudGate SSO, który jest połączony z reprezentacji azure ad użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **SSO Soloinsight-CloudGate** znajdź sekcję **Zarządzaj** i wybierz opcję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    1. W polu tekstowym **Adres URL logowania** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/login`

    1. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania, co zostało wyjaśnione poniżej w sekcji **Konfigurowanie logowania jednokrotnego w aplikacji Soloinsight-CloudGate SSO** samouczka.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie sytuowania sytua bezsiłanego Soloinsight-CloudGate** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Konfigurowanie sytuowania SSO Soloinsight-CloudGate

1. Aby zautomatyzować konfigurację w ramach pakietu SSO Soloinsight-CloudGate, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Setup Soloinsight-CloudGate SSO** skieruje Cię do aplikacji SSO Soloinsight-CloudGate. Stamtąd podaj poświadczenia administratora, aby zalogować się do solinsight-CloudGate SSO. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-8.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować sso Soloinsight-CloudGate, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Soloinsight-CloudGate jako administrator i wykonaj następujące czynności:

4. Aby uzyskać wartości, które mają być wklejone w witrynie Azure portal podczas konfigurowania podstawowego SAML, zaloguj się do portalu CloudGate Web Przy użyciu poświadczeń, a następnie uzyskać dostęp do ustawień logowania jednośliowego, które można znaleźć w następującej ścieżce **>Ustawienia>>systemowe>ogólne**.

    ![Ustawienia aplikacji CloudGate SSO](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **Adres URL użytkownika języka SAML**

    * Skopiuj łącza dostępne względem **adresu URL konsumenta saml** i **przekierowania pól URL** i wklej je odpowiednio w sekcji **Podstawowa konfiguracja SAML** usługi Azure portal dla pól **Identyfikator (identyfikator jednostki)** i **Adres URL odpowiedzi.**

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **Certyfikat podpisywania SAML**

    * Przejdź do źródła pliku certyfikatu (Base64), który został pobrany z listy certyfikatów podpisywania SAML usługi Azure portal SAML i kliknij go prawym przyciskiem myszy. Wybierz opcję **Edit with Notepad++** (Edytuj za pomocą Notatnika++) z listy. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Skopiuj zawartość pliku Notatnika++ certyfikatu (Base64).

        ![Kopiowanie certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Wklej zawartość w obszarze ustawień logowania jednokrotnego portalu internetowego CloudGate w polu **Certificate** (Certyfikat), a następnie kliknij przycisk zapisywania.

        ![Portal certyfikatu](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Grupa domyślna**

    * Wybierz pozycję **Business Admin** (Administrator firmy) z listy rozwijanej **Default Group** (Grupa domyślna) w portalu internetowym CloudGate

        ![Grupa domyślna](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **Adres URL identyfikatora usługi AD i adres URL logowania**

    * Skopiowany **adres URL logowania** z witryny Azure portal Konfigurowanie konfiguracji **logowania jednokrotnego Soloinsight-CloudGate** należy wprowadzić w sekcji ustawienia logowania jednokrotnego portalu CloudGate Web Portal.

    * Wklej łącze **adresu URL logowania** z witryny Azure portal w polu Adres URL logowania usługi **AD** CloudGate w portalu sieci Web.

    * Wklej łącze **identyfikatora usługi Azure AD** z witryny Azure portal w polu **Identyfikator usługi AD** portalu cloudgate w portalu sieci Web

        ![Logowanie w usłudze AD](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do solinsight-CloudGate SSO.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Soloinsight-CloudGate SSO**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Tworzenie użytkownika testowego aplikacji Soloinsight-CloudGate SSO

Aby utworzyć użytkownika testowego, wybierz pozycję **Employees** z menu głównego w internetowym portalu aplikacji CloudGate i wypełnij formularz Add new employee (Dodawanie nowego pracownika). Poziom urzędu do przypisania do użytkownika testowego to **Business Admin** (Administrator firmy). Kliknij raz pozycję **Create** (Utwórz) po wypełnieniu wszystkich wymaganych pól.

![Testowanie pracownika](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka SSO Soloinsight-CloudGate w panelu dostępu należy automatycznie zalogować się do sytuowania logowania jednokrotnego Soloinsight-CloudGate, dla którego skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)