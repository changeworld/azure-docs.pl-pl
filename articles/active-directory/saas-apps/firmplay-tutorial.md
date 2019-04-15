---
title: 'Samouczek: Integracja usługi Azure Active Directory z FirmPlay - propagowanie pracowników dla Rekrutacja | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i FirmPlay - propagowanie pracowników dla Rekrutacja.
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
ms.openlocfilehash: dc1d7032f80babf8686397ae4dcf0043f456acbd
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565194"
---
# <a name="tutorial-azure-active-directory-integration-with-firmplay---employee-advocacy-for-recruiting"></a>Samouczek: Integracja usługi Azure Active Directory z FirmPlay - propagowanie pracowników dla Rekrutacja

W tym samouczku dowiesz się, jak zintegrować FirmPlay - propagowanie pracowników dla Rekrutacja w usłudze Azure Active Directory (Azure AD).
Integrowanie FirmPlay - propagowanie pracowników dla Rekrutacja z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do FirmPlay - propagowanie pracowników dla Rekrutacja.
* Aby umożliwić użytkownikom można automatycznie zalogowany do FirmPlay - propagowanie pracowników dla Rekrutacja (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z FirmPlay - propagowanie pracowników dla Rekrutacja, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* FirmPlay - propagowanie pracowników dla Rekrutacja logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje propagowanie pracowników dla Rekrutacja FirmPlay - **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-firmplay---employee-advocacy-for-recruiting-from-the-gallery"></a>Dodawanie FirmPlay - propagowanie pracowników dla Rekrutacja z galerii

Aby skonfigurować integrację FirmPlay - propagowanie pracowników dla Rekrutacja w usłudze Azure AD, należy dodać FirmPlay - propagowanie pracowników dla Rekrutacja z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać FirmPlay - propagowanie pracowników dla Rekrutacja z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **FirmPlay - propagowanie pracowników dla Rekrutacja**, wybierz opcję **FirmPlay - propagowanie pracowników dla Rekrutacja** z panelu wynik kliknięcie **Dodaj** przycisk, aby Dodaj aplikację.

     ![FirmPlay - propagowanie pracowników dla Rekrutacja na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą FirmPlay — propagowanie pracowników dla Rekrutacja w oparciu o nazwie użytkownika testowego **Britta Simon**.
Logowanie jednokrotne do pracy, relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w FirmPlay - propagowanie pracowników dla Rekrutacja musi do można ustanowić.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą FirmPlay - propagowanie pracowników dla Rekrutacja, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie FirmPlay - propagowanie pracowników dla rekrutacji logowania jednokrotnego](#configure-firmplay---employee-advocacy-for-recruiting-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Utwórz FirmPlay - propagowanie pracowników dla użytkownika testowego Rekrutacja](#create-firmplay---employee-advocacy-for-recruiting-test-user)**  — aby odpowiednikiem Britta Simon w FirmPlay - propagowanie pracowników dla rekrutacji, która jest połączona z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z FirmPlay - propagowanie pracowników dla Rekrutacja, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **FirmPlay - propagowanie pracowników dla Rekrutacja** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![FirmPlay - propagowanie pracowników rekrutacji domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<your-subdomain>.firmplay.com/`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [FirmPlay - propagowanie pracowników do zespołu pomocy technicznej klienta rekrutacji](mailto:engineering@firmplay.com) można uzyskać wartość. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie FirmPlay - propagowanie pracowników dla Rekrutacja** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-firmplay---employee-advocacy-for-recruiting-single-sign-on"></a>Konfigurowanie FirmPlay - propagowanie pracowników dla rekrutacji logowania jednokrotnego

Aby skonfigurować logowanie jednokrotne na **FirmPlay - propagowanie pracowników dla Rekrutacja** stronie, musisz wysłać pobrany **certyfikat (Base64)** i skopiować adresy URL z witryny Azure portal do [ FirmPlay - propagowanie pracowników do zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola brittasimon@yourcompanydomain.extension. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do FirmPlay - propagowanie pracowników dla Rekrutacja.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **FirmPlay - propagowanie pracowników dla Rekrutacja**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **FirmPlay - propagowanie pracowników dla Rekrutacja**.

    ![FirmPlay - propagowanie pracownika linku Rekrutacja na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-firmplay---employee-advocacy-for-recruiting-test-user"></a>Utwórz FirmPlay - propagowanie pracowników dla użytkownika testowego Rekrutacja

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w FirmPlay - propagowanie pracowników dla Rekrutacja. Praca z [FirmPlay - propagowanie pracowników do zespołu pomocy technicznej Rekrutacja](mailto:engineering@firmplay.com) Aby dodać użytkowników w FirmPlay - propagowanie pracowników Rekrutacja platformy. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu FirmPlay - propagowanie pracowników Rekrutacja kafelka w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze FirmPlay - propagowanie pracowników dla Rekrutacja dla którego możesz skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

