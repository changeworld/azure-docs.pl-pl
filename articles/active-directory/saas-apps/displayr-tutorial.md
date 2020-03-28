---
title: 'Samouczek: Integracja usługi Azure Active Directory z displayerem | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a displayrem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: b739b4e3-1a37-4e3c-be89-c3945487f4c1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3cd1785595cf2f6b2401837780106f52fdc97e36
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103984"
---
# <a name="tutorial-integrate-displayr-with-azure-active-directory"></a>Samouczek: Integracja displayr z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować displayr z usługą Azure Active Directory (Azure AD). Po zintegrowaniu displayr z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do displayr.
* Włącz użytkownikom automatyczne logowanie do displayra za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego displayera.Displayr single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Displayr **obsługuje** sp inicjowane SSO.

## <a name="adding-displayr-from-the-gallery"></a>Dodawanie wyświetlacza z galerii

Aby skonfigurować integrację displayr z usługą Azure AD, należy dodać Displayr z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Displayr** w polu wyszukiwania.
1. Wybierz **pozycję Displayr** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie usługi Azure AD SSO za pomocą displayra przy użyciu użytkownika testowego o nazwie **Britta Simon**. Aby użytkownik syt/r działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w witrynie Displayr.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą displayra, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj displayr,](#configure-displayr)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego Displayr](#create-displayr-test-user)** mieć odpowiednik Britta Simon w displayr, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Displayr** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujący krok:

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://<YOURDOMAIN>.displayr.com`

    b. W polu tekstowym **Identyfikator (identyfikator jednostki)** wpisz adres URL, używając następującego wzorca:`<YOURDOMAIN>.displayr.com`

    >[!NOTE]
    >Te wartości nie są prawdziwe. Zaktualizuj te wartości przy użyciu rzeczywistego identyfikatora i adresu URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Displayr,](mailto:support@displayr.com) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji Podstawowa konfiguracja protokołu SAML w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aplikacja Displayr oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Poniższy zrzut ekranu przedstawia listę atrybutów domyślnych. Kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe Atrybuty użytkownika.

    ![image](common/edit-attribute.png)

1. Oprócz powyższej aplikacji Displayr oczekuje kilka więcej atrybutów, które mają być przekazywane z powrotem w odpowiedzi SAML. W sekcji **Atrybuty & oświadczenia użytkownika** w oknie dialogowym **Oświadczenia grupy (wersja zapoznawcza)** wykonaj następujące czynności:

    a. Kliknij **pióro** obok pozycji **Grupy zwrócone w zgłęceniu**.

    ![image](./media/displayr-tutorial/config04.png)

    ![image](./media/displayr-tutorial/config05.png)

    b. Wybierz **pozycję Wszystkie grupy** z listy radiowej.

    d. Wybierz **atrybut źródłowy** identyfikatora **grupy**.

    d. Zaznacz **pozycję Dostosuj nazwę oświadczenia grupy**.

    e. Sprawdź **grupy Emitowania jako oświadczenia roli**.

    f. Kliknij przycisk **Zapisz**.

1. W sekcji **Set-up Displayr** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-displayr"></a>Konfigurowanie displayer

1. Aby zautomatyzować konfigurację w displayr, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **Setup Displayr** przekieruje Cię do aplikacji Displayr. W tym miejscu podaj poświadczenia administratora, aby zalogować się do displayra. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację dla Ciebie i zautomatyzuje kroki 3-6.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz skonfigurować Displayr ręcznie, otwórz nowe okno przeglądarki internetowej i zaloguj się do witryny firmy Displayr jako administrator i wykonaj następujące czynności:

4. Kliknij **ustawienia,** a następnie przejdź do **pozycji Konto**.

    ![Konfigurowanie](./media/displayr-tutorial/config01.png)

5. Przełącz się do **ustawień** z górnego menu i przewiń stronę w dół, aby kliknąć **pozycję Konfiguruj logowanie jednokrotne (SAML)**.

    ![Konfigurowanie](./media/displayr-tutorial/config02.png)

6. Na stronie **Jednokrotne logowanie (SAML)** wykonaj następujące czynności:

    ![Konfigurowanie](./media/displayr-tutorial/config03.png)

    a. Zaznacz pole **Włącz logowanie jednokrotne (SAML).**

    b. Skopiuj rzeczywistą wartość **identyfikatora** z sekcji **Podstawowa konfiguracja SAML** usługi Azure AD i wklej ją do pola tekstowego **wystawcy.**

    d. W polu tekstowym **Adres URL logowania** wklej wartość adresu URL **logowania,** który został skopiowany z witryny Azure portal.

    d. W polu tekstowym **Adres URL wylogowania** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure Portal.

    e. Otwórz certyfikat (Base64) w Notatniku, skopiuj jego zawartość i wklej ją do pola tekstowego **Certyfikat.**

    f. **Mapowania grup** są opcjonalne.

    g. Kliknij przycisk **Zapisz**.  

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

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do displayr.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Displayr**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-displayr-test-user"></a>Utwórz użytkownika testowego Displayr

Aby włączyć użytkowników usługi Azure AD, zaloguj się do displayr, muszą one być aprowizowana do displayr. W displayr inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do displayer jako administrator.

2. Kliknij **ustawienia,** a następnie przejdź do **pozycji Konto**.

    ![Konfiguracja displayeru](./media/displayr-tutorial/config01.png)

3. Przełącz się do **ustawień** z górnego menu i przewiń stronę w dół, aż **sekcja Użytkownicy** następnie kliknij **przycisk Nowy użytkownik**.

    ![Konfiguracja displayeru](./media/displayr-tutorial/config07.png)

4. Na stronie **Nowy użytkownik** wykonaj następujące czynności:

    ![Konfiguracja displayeru](./media/displayr-tutorial/config06.png)

    a. W polu tekstowym **Nazwa** wprowadź nazwę użytkownika, takiego jak **Brittasimon**.

    b. W polu tekstowym **Email** (Adres e-mail) wprowadź adres e-mail użytkownika, na przykład `Brittasimon@contoso.com`.

    d. Wybierz odpowiednie **członkostwo w grupie**.

    d. Kliknij przycisk **Zapisz**.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Displayr w Panelu dostępu należy automatycznie zalogować się do displayera, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
