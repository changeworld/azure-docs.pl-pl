---
title: 'Samouczek: Integracja usługi Azure Active Directory z firmplay - Rzecznictwo pracowników w zakresie rekrutacji | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a firmplay — rzecznictwo pracowników w zakresie rekrutacji.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a6799629-7546-43f8-a966-956db32864b1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 2bdc3a13582f079cc9325ef1c6949c3ae10138eb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102526"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Samouczek: Integracja usługi Azure Active Directory z programem FirmPlay — rzecznictwo pracowników w zakresie rekrutacji

W tym samouczku dowiesz się, jak zintegrować firmplay — rzecznictwo pracowników dla rekrutacji z usługą Azure Active Directory (Azure AD).
Integracja firmPlay — rzecznictwo pracowników w zakresie rekrutacji z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do firmplay — rzecznictwo pracowników dla rekrutacji.
* Można włączyć użytkowników do automatycznego logowania się do FirmPlay — rzecznictwo pracowników dla rekrutacji (logowanie jednokrotne) z ich kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą FirmPlay — rzecznictwo pracowników w zakresie rekrutacji, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* FirmPlay — rzecznictwo pracowników w zakresie rekrutacji subskrypcji z obsługą logowania jednokrotnego

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* FirmPlay - Rzecznictwo pracowników dla rekrutacji obsługuje **SP** zainicjowane SSO

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Dodawanie FirmPlay - Rzecznictwo pracowników dla rekrutacji z galerii

Aby skonfigurować integrację firmPlay — rzecznictwo pracowników dla rekrutacji do usługi Azure AD, należy dodać FirmPlay — rzecznictwo pracowników dla rekrutacji z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać FirmPlay - Rzecznictwo pracowników dla rekrutacji z galerii, wykonaj następujące kroki:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę usługi **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **FirmPlay - Employee Advocacy for Recruiting**, wybierz **firmPlay - Rzecznictwo pracowników dla rekrutacji** z panelu wyników, a następnie kliknij przycisk **Dodaj,** aby dodać aplikację.

     ![FirmPlay - Rzecznictwo pracowników rekrutacji na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z FirmPlay — Rzecznictwo pracowników dla rekrutacji na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze FirmPlay — rzecznictwo pracowników dotyczące rekrutacji.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi FirmPlay — rzecznictwo pracowników dotyczące rekrutacji, należy wykonać następujące elementy konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj firmPlay — rzecznictwo pracowników do rekrutacji logowania jednokrotnego](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)** — w celu skonfigurowania ustawień logowania jednokrotnego po stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie FirmPlay — rzecznictwo pracowników dla użytkownika testowego rekrutacji](#create-firmplay---employee-advocacy-for-recruiting-test-user)** — aby mieć odpowiednik Britta Simon w FirmPlay — rzecznictwo pracowników dla rekrutacji, która jest połączona z reprezentacją użytkownika usługi Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą funkcji FirmPlay — rzecznictwo pracowników dotyczące rekrutacji, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie **FirmPlay — Rzecznictwo pracowników w zakresie** rekrutacji aplikacji wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![FirmPlay - Rzecznictwo pracowników w zakresie rekrutacji domen i adresów URL z informacjami o logowaniu jednokrotnym](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z [FirmPlay - Rzecznictwo pracowników dla zespołu pomocy technicznej klienta rekrutacji,](mailto:engineering@firmplay.com) aby uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. W sekcji **Konfigurowanie firmPlay — rzecznictwo pracowników w zakresie rekrutacji** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Konfigurowanie firmPlay - Rzecznictwo pracowników do rekrutacji logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne w **witrynie FirmPlay — rzecznictwo pracowników po** stronie rekrutacji, należy wysłać pobrany **certyfikat (Base64)** i odpowiednie skopiowane adresy URL z witryny Azure portal do [firmplay — Obsługa pracowników dla zespołu pomocy technicznej rekrutacji](mailto:engineering@firmplay.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** u góry ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa** brittasimon@yourcompanydomain.extensionużytkownika wpisz . Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając dostęp do FirmPlay — rzecznictwo pracowników dla rekrutacji.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje**, a następnie wybierz pozycję **FirmPlay — Rzecznictwo pracowników w zakresie rekrutacji**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **pozycję FirmPlay - Employee Advocacy for Recruiting**.

    ![Link FirmPlay - Rzecznictwo pracowników w rekrutacji na liście Aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Tworzenie firmPlay - Rzecznictwo pracowników dla rekrutacji użytkownika testowego

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w FirmPlay - Rzecznictwo pracowników dla rekrutacji. Praca z [FirmPlay - Rzecznictwo pracowników dla zespołu pomocy technicznej rekrutacji,](mailto:engineering@firmplay.com) aby dodać użytkowników w FirmPlay - Rzecznictwo pracowników dla rekrutacji platformy. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka FirmPlay — Rzecznictwo pracowników dla rekrutacji w panelu dostępu należy automatycznie zalogować się do witryny FirmPlay — Rzecznictwo pracowników w zakresie rekrutacji, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

