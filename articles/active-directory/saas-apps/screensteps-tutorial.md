---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą ScreenSteps | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 570f789d0f399c5ffa7535101136ab65ba58ccd5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867733"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą ScreenSteps

W tym samouczku dowiesz się, jak zintegrować ScreenSteps w usłudze Azure Active Directory (Azure AD).
Integrowanie ScreenSteps z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do ScreenSteps.
* Aby umożliwić użytkownikom można automatycznie zalogowany do ScreenSteps (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą ScreenSteps, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* ScreenSteps pojedynczego logowania jednokrotnego włączonych subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje ScreenSteps **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-screensteps-from-the-gallery"></a>Dodawanie ScreenSteps z galerii

Aby skonfigurować integrację ScreenSteps w usłudze Azure AD, należy dodać ScreenSteps z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać ScreenSteps z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **ScreenSteps**, wybierz opcję **ScreenSteps** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![ScreenSteps na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą ScreenSteps w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w ScreenSteps musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą ScreenSteps, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie ScreenSteps logowania jednokrotnego](#configure-screensteps-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego ScreenSteps](#create-screensteps-test-user)**  — aby odpowiednikiem Britta Simon w ScreenSteps połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z ScreenSteps, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **ScreenSteps** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![ScreenSteps domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Ta wartość nie jest prawdziwa. Zaktualizuj tę wartość przy użyciu rzeczywistego logowania jednokrotnego adresu URL, który zostało wyjaśnione w dalszej części tego samouczka.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/certificatebase64.png)

6. Na **Konfigurowanie ScreenSteps** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-screensteps-single-sign-on"></a>Konfigurowanie ScreenSteps logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się do witryny firmy ScreenSteps, jako administrator.

1. Kliknij przycisk **ustawienia konta**.

    ![Zarządzanie kontami](./media/screensteps-tutorial/ic778523.png "Zarządzanie kontami")

1. Kliknij pozycję **Logowanie jednokrotne**.

    ![Zdalne uwierzytelnianie](./media/screensteps-tutorial/ic778524.png "uwierzytelniania zdalnego")

1. Kliknij przycisk **utworzyć pojedynczy punkt końcowy logowania**.

    ![Zdalne uwierzytelnianie](./media/screensteps-tutorial/ic778525.png "uwierzytelniania zdalnego")

1. W **utworzyć pojedynczy punkt końcowy logowania** sekcji, wykonaj następujące czynności:

    ![Tworzenie punktu końcowego uwierzytelniania](./media/screensteps-tutorial/ic778526.png "utworzenie punktu końcowego uwierzytelniania")

    a. W **tytuł** pole tekstowe, wpisz tytuł.

    b. Z **tryb** listy wybierz **SAML**.

    c. Kliknij pozycję **Utwórz**.

1. **Edytuj** nowy punkt końcowy.

    ![Edytuj punkt końcowy](./media/screensteps-tutorial/ic778528.png "Edytuj punkt końcowy")

1. W **edytować pojedynczy punkt końcowy logowania** sekcji, wykonaj następujące czynności:

    ![Punkt końcowy uwierzytelniania zdalnego](./media/screensteps-tutorial/ic778527.png "uwierzytelniania zdalnego punktu końcowego")

    a. Kliknij przycisk **Przekaż nowy plik certyfikatu SAML**, a następnie przekaż certyfikat, który został pobrany z witryny Azure portal.

    b. Wklej **adres URL logowania** wartości, które zostały skopiowane z witryny Azure portal do **zdalnego adresu URL logowania** pola tekstowego.

    c. Wklej **adres URL wylogowania** wartości, które zostały skopiowane z witryny Azure portal do **adres URL wylogowania** pola tekstowego.

    d. Wybierz **grupy** do przypisywania użytkowników do po ich udostępnieniu.

    e. Kliknij przycisk **Aktualizuj**.

    f. Kopiuj **adres URL klienta SAML** do Schowka i Wklej do **adres URL logowania** polu tekstowym w **podstawową konfigurację protokołu SAML** sekcji w witrynie Azure portal.

    g. Wróć do **edytować pojedynczy punkt końcowy logowania**.

    h. Kliknij przycisk **Ustaw jako domyślny dla konta** przycisk, aby użyć tego punktu końcowego dla wszystkich użytkowników logujących się na ScreenSteps. Alternatywnie możesz kliknąć pozycję **dodać do witryny** przycisk, aby użyć tego punktu końcowego dla określonych witryn internetowych w **ScreenSteps**.

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do ScreenSteps.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **ScreenSteps**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **ScreenSteps**.

    ![Link ScreenSteps na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-screensteps-test-user"></a>Tworzenie użytkownika testowego ScreenSteps

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w ScreenSteps. Praca z [zespołem pomocy technicznej klienta ScreenSteps](https://www.screensteps.com/contact) Aby dodać użytkowników na platformie ScreenSteps. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka ScreenSteps w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze ScreenSteps, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)