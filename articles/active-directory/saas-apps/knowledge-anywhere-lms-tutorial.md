---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Knowledge Anywhere LMS | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory i aplikacją Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44324bbdd5af6675dfb4f5664cbbde2627edfec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098560"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Samouczek: Integrowanie usługi Knowledge Anywhere z usługą Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować usługę Knowledge Anywhere LMS z usługą Azure Active Directory (Azure AD). Po zintegrowaniu usługi Knowledge Anywhere LMS z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi Knowledge Anywhere LMS.
* Włącz użytkownikom automatyczne logowanie do usługi Knowledge Anywhere LMS za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego usługi Knowledge Anywhere LMS (SSO).

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym. Knowledge Anywhere LMS obsługuje zainicjowane przez **usługę SP** wprowadzenie identyfikatora SSO i obsługuje inicjowanie obsługi administracyjnej użytkowników **just in time.**

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Dodawanie aplikacji Knowledge Anywhere LMS z galerii

Aby skonfigurować integrację aplikacji Knowledge Anywhere LMS z usługą Azure AD, należy dodać aplikację Knowledge Anywhere LMS z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **knowledge anywhere LMS** w polu wyszukiwania.
1. Wybierz **pozycję Knowledge Anywhere LMS** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

Konfigurowanie i testowanie sytuaszowego usługi Azure AD za pomocą usługi Knowledge Anywhere LMS przy użyciu użytkownika testowego o nazwie **B. Simon**. Aby funkcja SSO działała, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Knowledge Anywhere LMS.

Aby skonfigurować i przetestować sytuasz usługi Azure AD z usługą Knowledge Anywhere LMS, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj program Knowledge Anywhere LMS,](#configure-knowledge-anywhere-lms)** aby skonfigurować ustawienia logowania sytego po stronie aplikacji.
3. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
4. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz użytkownika testowego LMS knowledge anywhere,](#create-knowledge-anywhere-lms-test-user)** aby mieć odpowiednik B. Simon w usłudze Knowledge Anywhere LMS, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)— na stronie Integracja aplikacji **Knowledge Anywhere LMS** znajdź sekcję **Zarządzaj** i wybierz opcję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    1. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL odpowiedzi, co zostało opisane w dalszej części tego samouczka.

1. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > Wartość adresu URL logowania nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide), aby uzyskać tę wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. W sekcji **Konfigurowanie usługi Knowledge Anywhere LMS** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Konfigurowanie usługi Knowledge Anywhere W.

1. Aby zautomatyzować konfigurację w usłudze Knowledge Anywhere LMS, należy zainstalować **rozszerzenie przeglądarki My Apps Secure Sign-in,** klikając pozycję Zainstaluj **rozszerzenie**.

    ![Rozszerzenie Moje aplikacje](common/install-myappssecure-extension.png)

2. Po dodaniu rozszerzenia do przeglądarki, kliknij **na Setup Knowledge Anywhere LMS** skieruje Cię do aplikacji Knowledge Anywhere LMS. W tym miejscu podaj poświadczenia administratora, aby zalogować się do usługi Knowledge Anywhere LMS. Rozszerzenie przeglądarki automatycznie skonfiguruje aplikację i zautomatyzuje kroki 3–7.

    ![Konfiguracja instalacji](common/setup-sso.png)

3. Jeśli chcesz ręcznie skonfigurować program Knowledge Anywhere LMS, otwórz nowe okno przeglądarki sieci Web i zaloguj się do witryny firmy Knowledge Anywhere LMS jako administrator i wykonaj następujące czynności:

4. Wybierz kartę **Site** (Witryna).

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Wybierz kartę **SAML Settings** (Ustawienia języka SAML).

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Kliknij pozycję **Add New** (Dodaj nowe).

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Na stronie **Add/Update SAML Settings** (Dodawanie/aktualizowanie ustawień języka SAML), wykonaj następujące czynności:

    ![Konfiguracja aplikacji Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Wprowadź nazwę dostawcy tożsamości zgodnie z wymaganiami organizacji. Na przykład:- `Azure`.

    b. W polu tekstowym **identyfikator jednostki IDP** wklej wartość **identyfikatora usługi Azure AD,** która została skopiowana z witryny Azure portal.

    d. W polu tekstowym **adresu URL usługi IDP** wklej wartość **adresu URL logowania,** która została skopiowana z witryny Azure portal.

    d. Otwórz plik certyfikatu pobrany z witryny Azure Portal do Notatnika, skopiuj zawartość certyfikatu i wklej go w polu tekstowym **Certificate** (Certyfikat).

    e. W polu tekstowym **adresu URL wylogowania** wklej wartość **adresu URL wylogowania,** która została skopiowana z witryny Azure Portal.

    f. Wybierz pozycję **Main Site** (Witryna główna) z listy rozwijanej w polu **Domain** (Domena).

    g. Skopiuj wartość **SP Entity ID** (Identyfikator jednostki dostawcy usługi) i wklej ją w polu tekstowym **Identifier** (Identyfikator) w sekcji **Podstawowa konfiguracja protokołu SAML** witryny Azure Portal.

    h. Skopiuj wartość **SP Response(ACS) URL** (Adres URL odpowiedzi dostawcy usługi (ACS)) i wklej go w polu tekstowym **Adres URL odpowiedzi** sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

    i. Kliknij przycisk **Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B. Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B. Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do usługi Knowledge Anywhere LMS.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Knowledge Anywhere LMS**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz **pozycję B. Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tworzenie użytkownika testowego aplikacji Knowledge Anywhere LMS

W tej sekcji użytkownik o nazwie B. Simon jest tworzony w Knowledge Anywhere LMS. Aplikacja Knowledge Anywhere LMS obsługuje aprowizację użytkowników typu „just in time”, która jest domyślnie włączona. W tej sekcji nie musisz niczego robić. Jeśli użytkownik jeszcze nie istnieje w aplikacji Knowledge Anywhere LMS, zostanie utworzony po uwierzytelnieniu.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Knowledge Anywhere LMS w Panelu dostępu należy automatycznie zalogować się do usługi Knowledge Anywhere LMS, dla której skonfigurowano logującą się logującą logującą log. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)