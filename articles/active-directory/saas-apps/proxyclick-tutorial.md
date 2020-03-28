---
title: 'Samouczek: Integracja usługi Azure Active Directory z proxyclick | Dokumenty firmy Microsoft'
description: W tym samouczku dowiesz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093494"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Samouczek: Integracja usługi Azure Active Directory z proxyclick

W tym samouczku dowiesz się, jak zintegrować proxyclick z usługi Azure Active Directory (Azure AD).
Integracja ta zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do proxyclick.
* Można włączyć użytkowników, aby automatycznie zalogować się do proxyclick (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z proxyclick, musisz mieć:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz zarejestrować się w celu uzyskania [miesięcznej wersji próbnej.](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja proxyclick, która ma włączone logowanie jednokrotne włączone.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Proxyclick obsługuje identyfikator sytowy inicjowany przez sp i idp.

## <a name="add-proxyclick-from-the-gallery"></a>Dodaj proxyclick z galerii

Aby skonfigurować integrację proxyclick do usługi Azure AD, należy dodać proxyclick z galerii do listy zarządzanych aplikacji SaaS.

1. W [witrynie Azure portal](https://portal.azure.com)w lewym okienku wybierz pozycję **Azure Active Directory:**

    ![Wybierz pozycję Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** > **Wszystkie aplikacje:**

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać aplikację, wybierz **pozycję Nowa aplikacja** w górnej części okna:

    ![Wybierz nową aplikację](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Proxyclick**. Wybierz **pozycję Proxyclick** w wynikach wyszukiwania, a następnie wybierz pozycję **Dodaj**.

     ![Wyniki wyszukiwania](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD za pomocą funkcji Proxyclick przy użyciu użytkownika testowego o nazwie Britta Simon.
Aby włączyć logowanie jednokrotne, należy ustanowić relację między użytkownikiem usługi Azure AD a odpowiednim użytkownikiem w aplikacji Proxyclick.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą narzędzia Proxyclick, należy wykonać następujące kroki:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby włączyć tę funkcję dla użytkowników.
2. **[Konfigurowanie rejestracji pojedynczej proxyclick](#configure-proxyclick-single-sign-on)** po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednookrotne usługi Azure AD.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć logowanie jednookrotne usługi Azure AD dla użytkownika.
5. **[Utwórz użytkownika testowego proxyclick,](#create-a-proxyclick-test-user)** który jest połączony z reprezentacją usługi Azure AD użytkownika.
6. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą narzędzia Proxyclick, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji proxyclick wybierz pozycję **Logowanie jednokrotne:**

    ![Wybieranie logowania jednokrotnego](common/select-sso.png)

2. W oknie **dialogowym Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne:

    ![Wybieranie metody logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edycja,** aby otworzyć okno dialogowe **Podstawowa konfiguracja SAML:**

    ![Ikona Edytuj](common/edit-urls.png)

4. W oknie dialogowym **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez IdP, wykonać następujące kroki.

    ![Podstawowe okno dialogowe Konfiguracja SAML](common/idp-intiated.png)

    1. W polu **Identyfikator** wprowadź adres URL w tym wzorcu:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. W polu **Odpowiedz na adres URL** wprowadź adres URL w tym wzorzec:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez sp, wybierz pozycję **Ustaw dodatkowe adresy URL**. W polu **Zaloguj się na adres URL** wprowadź adres URL w tym wzorzec:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Informacje o logowanie do domeny i adresów URL proxy](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Te wartości są symbolami zastępczymi. Musisz użyć rzeczywistego identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Kroki dotyczące uzyskiwania tych wartości są opisane w dalszej części tego samouczka.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Signing Certificate (Certyfikat podpisywania SAML)** wybierz łącze **Pobierz** obok **pozycji Certyfikat (Base64)**— zgodnie z wymaganiami i zapisz certyfikat na komputerze:

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

7. W sekcji **Konfigurowanie aplikacji Proxyclick** skopiuj odpowiednie adresy URL na podstawie wymagań:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    1. **Adres URL logowania**.

    1. **Identyfikator usługi Azure AD**.

    1. **Adres URL wylogowania**.

### <a name="configure-proxyclick-single-sign-on"></a>Konfigurowanie rejestracji pojedynczej typu proxyclick

1. W nowym oknie przeglądarki internetowej zaloguj się do witryny firmy Proxyclick jako administrator.

2. Wybierz **ustawienia & konta:**

    ![Wybieranie ustawień & konta](./media/proxyclick-tutorial/configure1.png)

3. Przewiń w dół do sekcji **Integracje** i wybierz **SAML**:

    ![Wybierz SAML](./media/proxyclick-tutorial/configure2.png)

4. W sekcji **SAML** należy wykonać następujące kroki.

    ![Sekcja SAML](./media/proxyclick-tutorial/configure3.png)

    1. Skopiuj wartość **adresu URL konsumenta SAML** i wklej ją do pola **Adres URL odpowiedzi** w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    1. Skopiuj wartość **adresu URL przekierowania logowania SSO SAML** i wklej ją do pól **Podpis na adresie URL** i **identyfikatorze** w oknie dialogowym **Podstawowa konfiguracja SAML** w witrynie Azure portal.

    1. Na liście **Metoda żądania SAML** wybierz pozycję **Przekierowanie HTTP**.

    1. W polu **Wystawić** wklej wartość **identyfikatora usługi Azure AD** skopiowaną z witryny Azure portal.

    1. W polu **ADRESU URL punktu końcowego SAML 2.0** wklej wartość **adresu URL logowania** skopiowaną z witryny Azure portal.

    1. W Notatniku otwórz plik certyfikatu pobrany z witryny Azure portal. Wklej zawartość tego pliku do pola **Certyfikat.**

    1. Wybierz pozycję **Zapisz zmiany**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure portal.

1. W witrynie Azure portal wybierz pozycję **Azure Active Directory** w lewym okienku, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy:**

    ![Wybierz pozycję Wszyscy użytkownicy](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu:

    ![Wybierz pozycję Nowy użytkownik](common/new-user.png)

3. W oknie dialogowym **Użytkownik** należy wykonać następujące czynności.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź BrittaSimon@ **\<> swojej>.\<>rozszerzenia **. (Na przykład BrittaSimon@contoso.com.)

    1. Wybierz **pozycję Pokaż hasło**, a następnie zapisz wartość w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz Britta Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając jej dostęp do proxyclick.

1. W portalu Azure wybierz **pozycję Aplikacje przedsiębiorstwa**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **Proxyclick**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję Proxyclick**.

    ![Lista wniosków](common/all-applications.png)

3. W lewym okienku wybierz **pozycję Użytkownicy i grupy:**

    ![Wybieranie pozycji Użytkownicy i grupy](common/users-groups-blade.png)

4. Wybierz pozycję **Dodaj użytkownika**, a następnie **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Wybieranie pozycji Dodaj użytkownika](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu okna.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Kliknij przycisk **Wybierz** u dołu okna.

7. W oknie dialogowym **Dodawanie przypisania** wybierz pozycję **Przypisz**.

### <a name="create-a-proxyclick-test-user"></a>Tworzenie użytkownika testowego proxyclick

Aby umożliwić użytkownikom usługi Azure AD zalogować się do proxyclick, należy dodać je do proxyclick. Należy dodać je ręcznie.

Aby utworzyć konto użytkownika, wykonaj następujące czynności:

1. Zaloguj się do witryny firmy Proxyclick jako administrator.

1. Wybierz **pozycję Współpracownicy** w górnej części okna:

    ![Wybierz współpracowników](./media/proxyclick-tutorial/user1.png)

1. Wybierz **pozycję Dodaj współpracownika**:

    ![Wybierz pozycję Dodaj współpracownika](./media/proxyclick-tutorial/user2.png)

1. W sekcji **Dodawanie współpracownika** należy wykonać następujące czynności.

    ![Dodawanie sekcji współpracownika](./media/proxyclick-tutorial/user3.png)

    1. W polu **Poczta e-mail** wprowadź adres e-mail użytkownika. W tym przypadku **contoso.com\@brittasimon**.

    1. W polu **Imię** wprowadź imię użytkownika. W tym przypadku **Britta**.

    1. W polu **Nazwisko** wprowadź nazwisko użytkownika. W tym **przypadku, Simon**.

    1. Wybierz **pozycję Dodaj użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

Teraz musisz przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po wybraniu kafelka Proxyclick w Panelu dostępu należy automatycznie zalogować się do wystąpienia proxyclick, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji o Panelu dostępu, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

