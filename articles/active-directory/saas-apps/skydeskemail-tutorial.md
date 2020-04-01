---
title: 'Samouczek: Integracja usługi Azure Active Directory z pocztą e-mail skydesk | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a pocztą e-mail skydesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a9d0bbcb-ddb5-473f-a4aa-028ae88ced1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: faaa5dcc435452d6ed9e0f2c5b481df1e352dfd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090440"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Samouczek: Integracja usługi Azure Active Directory z pocztą e-mail usługi SkyDesk

W tym samouczku dowiesz się, jak zintegrować skydesk poczty e-mail z usługi Azure Active Directory (Azure AD).
Integracja poczty SkyDesk z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do poczty skydesk.
* Można włączyć użytkownikom automatyczne logowanie się do skydesk e-mail (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z pocztą e-mail skydesk, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Subskrypcja z obsługą logowania jednokrotnego SkyDesk

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* SkyDesk Email obsługuje zainicjowane przez **usługę SP** wprowadzenie SSO

## <a name="adding-skydesk-email-from-the-gallery"></a>Dodawanie wiadomości e-mail skydesk z galerii

Aby skonfigurować integrację skydesk poczty e-mail do usługi Azure AD, należy dodać SkyDesk e-mail z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać skydesk e-mail z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SkyDesk Email**, wybierz **SkyDesk Email** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![SkyDesk E-mail na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą SkyDesk Email na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w skydesk e-mail.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi SkyDesk Email, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj logowanie jednokrotne skydesk e-mail](#configure-skydesk-email-single-sign-on)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz użytkownika testowego SkyDesk Email](#create-skydesk-email-test-user)** — aby mieć odpowiednik Britta Simon w skydesk e-mail, który jest połączony z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą usługi SkyDesk Email, wykonaj następujące czynności:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **SkyDesk Email** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![SkyDesk E-mail domeny i adresy URL pojedynczego logowania informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta poczty e-mail SkyDesk,](https://www.skydesk.jp/apps/support/) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie wiadomości e-mail SkyDesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurowanie logowania jednokrotnego poczty e-mail w umiań SkyDesk

1. W innej przeglądarce internetowej zaloguj się do swojego konta e-mail SkyDesk jako administrator.

1. W menu u góry kliknij pozycję **Ustawienia**i wybierz pozycję **Org**.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Kliknij **domeny** z lewego panelu.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Kliknij **pozycję Dodaj domenę**.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Wprowadź nazwę domeny, a następnie zweryfikuj domenę.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Kliknij na **UWIERZYTELNIANIE SAML** z lewego panelu.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na stronie okna dialogowego **Uwierzytelnianie SAML** wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Aby użyć uwierzytelniania opartego na SAML, należy **zweryfikować konfigurację adresu** URL domeny lub **portalu.** Możesz ustawić adres URL portalu z unikatową nazwą.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. W polu tekstowym **Adres URL logowania** wklej wartość adresu URL **logowania,** który został skopiowany z witryny Azure portal.

    b. W polu tekstowym adres URL **wylogowania** wklej wartość **adresu URL wylogowania,** który został skopiowany z witryny Azure Portal.

    d. **Zmień adres URL hasła** jest opcjonalny, więc pozostaw go pustym.

    d. Kliknij **przycisk Pobierz klucz z pliku,** aby wybrać pobrany certyfikat z witryny Azure Portal, a następnie kliknij przycisk **Otwórz,** aby przekazać certyfikat.

    e. Dla ustawienia **Algorithm** (Algorytm) wybierz pozycję **RSA**.

    f. Kliknij **przycisk Ok,** aby zapisać zmiany.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do SkyDesk e-mail.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **SkyDesk Email**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **SkyDesk Email**.

    ![Link SkyDesk Email na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-skydesk-email-test-user"></a>Tworzenie użytkownika testowego SkyDesk Email

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w SkyDesk Email.

Kliknij user **access** z lewego panelu w SkyDesk Email, a następnie wprowadź swoją nazwę użytkownika.

![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Jeśli chcesz utworzyć użytkowników masowych, skontaktuj się z [zespołem pomocy technicznej klienta poczty e-mail SkyDesk.](https://www.skydesk.jp/apps/support/)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka SkyDesk Email w Panelu dostępu należy automatycznie zalogować się do wiadomości e-mail SkyDesk, dla której skonfigurowano logującą się do rejestru jednośmiękowego. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

