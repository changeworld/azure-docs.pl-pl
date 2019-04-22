---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą adresu E-mail SkyDesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i SkyDesk wiadomości E-mail.
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
ms.openlocfilehash: e0c2dc6c370e697f896e24e7d56c6eb8900601a9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59271054"
---
# <a name="tutorial-azure-active-directory-integration-with-skydesk-email"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą adresu E-mail SkyDesk

W tym samouczku dowiesz się, jak zintegrować SkyDesk wiadomości E-mail z usługi Azure Active Directory (Azure AD).
Integrowanie SkyDesk wiadomości E-mail z usługi Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do poczty E-mail SkyDesk.
* Użytkownikom można automatycznie zalogowany do E-mail SkyDesk (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu adresu E-mail SkyDesk, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Adres E-mail SkyDesk logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje E-mail SkyDesk **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-skydesk-email-from-the-gallery"></a>Dodawanie funkcji E-mail SkyDesk z galerii

Aby skonfigurować integrację SkyDesk wiadomości e-mail w usłudze Azure AD, należy dodać SkyDesk wiadomości E-mail z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SkyDesk wiadomości E-mail z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SkyDesk wiadomości E-mail**, wybierz opcję **E-mail SkyDesk** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

     ![SkyDesk poczty E-mail, na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i test usługi Azure AD logowania jednokrotnego przy użyciu adresu E-mail SkyDesk w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w wiadomości E-mail SkyDesk musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu adresu E-mail SkyDesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie SkyDesk adres E-mail logowania jednokrotnego](#configure-skydesk-email-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego E-mail SkyDesk](#create-skydesk-email-test-user)**  — aby odpowiednikiem Britta Simon w wiadomości E-mail SkyDesk, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu adresu E-mail SkyDesk, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **E-mail SkyDesk** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![SkyDesk domeny poczty E-mail i adresów URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://mail.skydesk.jp/portal/<companyname>`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta poczty E-mail SkyDesk](https://www.skydesk.jp/apps/support/) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **skonfigurować konta E-mail SkyDesk** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-skydesk-email-single-sign-on"></a>Konfigurowanie wiadomości E-mail SkyDesk logowania jednokrotnego

1. W przeglądarce internetowej innej Zaloguj się do swojego konta E-mail SkyDesk jako administrator.

1. W menu u góry kliknij **instalacji**i wybierz **organizacji**.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_51.png)
  
1. Kliknij pozycję **domen** z lewego panelu.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_53.png)

1. Kliknij pozycję **Dodawanie domeny**.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_54.png)

1. Wprowadź nazwę domeny, a następnie zweryfikować domenę.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_55.png)

1. Kliknij pozycję **uwierzytelnianie SAML** z lewego panelu.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_52.png)

1. Na **uwierzytelnianie SAML** okna dialogowego strony, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_56.png)

    > [!NOTE]
    > Aby używać uwierzytelniania SAML na podstawie, użytkownik powinien mieć **zweryfikowaną domenę** lub **portal adresu URL** Instalatora. Możesz ustawić portalu adres URL o unikatowej nazwie.

    ![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_57.png)

    a. W **adres URL logowania** pola tekstowego, Wklej wartość **adres URL logowania**, który skopiowano z witryny Azure portal.

    b. W **wylogowania** polu tekstowym adresu URL, Wklej wartość **adres URL wylogowania**, które zostały skopiowane z witryny Azure portal.

    c. **Zmień adres URL hasła** jest opcjonalny więc pozostaw to pole puste.

    d. Kliknij pozycję **uzyskać klucz z pliku** wybierz certyfikat pobrany z witryny Azure portal, a następnie kliknij przycisk **Otwórz** można przekazać certyfikatu.

    e. Dla ustawienia **Algorithm** (Algorytm) wybierz pozycję **RSA**.

    f. Kliknij przycisk **Ok** Aby zapisać zmiany.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz **brittasimon@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do udzielania dostępu do poczty E-mail SkyDesk za pomocą platformy Azure logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **E-mail SkyDesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **E-mail SkyDesk**.

    ![Link E-mail SkyDesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-skydesk-email-test-user"></a>Tworzenie użytkownika testowego SkyDesk wiadomości E-mail

W tej sekcji utworzysz użytkownika o nazwie Britta Simon SkyDesk wiadomości e-mail.

Kliknij pozycję **dostęp użytkownika** z lewej strony panelu SkyDesk wiadomości e-mail, a następnie wprowadź swoją nazwę użytkownika.

![Konfigurowanie logowania jednokrotnego](./media/skydeskemail-tutorial/tutorial_skydeskemail_58.png)

> [!NOTE]
> Jeśli potrzebujesz do tworzenia użytkowników zbiorczo, musisz skontaktować się z [zespołem pomocy technicznej klienta poczty E-mail SkyDesk](https://www.skydesk.jp/apps/support/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SkyDesk poczty E-mail w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze SkyDesk poczty E-mail, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

