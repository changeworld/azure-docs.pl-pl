---
title: 'Samouczek: Azure integracji usługi Active Directory za pomocą Dyrektor wydatki SciQuest | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i dyrektora wydatków SciQuest.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9fab641b-292e-4bef-91d1-8ccc4f3a0c1f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 371ea6609181b84ff8554b9e2f1a5047d2d48c48
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65867776"
---
# <a name="tutorial-azure-active-directory-integration-with-sciquest-spend-director"></a>Samouczek: Azure integracji usługi Active Directory za pomocą SciQuest wydatków Dyrektor

W tym samouczku dowiesz się, jak zintegrować SciQuest Dyrektor wydatków za pomocą usługi Azure Active Directory (Azure AD).
Integrowanie SciQuest Dyrektor wydatków z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do SciQuest Dyrektor wydatki.
* Użytkownikom można automatycznie zalogowany dyrektorowi SciQuest wydatki (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z SciQuest wydatków Dyrektor, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* Dyrektor wydatki SciQuest logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Dyrektor wydatki SciQuest **SP** jednokrotne logowanie inicjowane przez
* Obsługuje Dyrektor wydatki SciQuest **Just In Time** aprowizacji użytkowników

## <a name="adding-sciquest-spend-director-from-the-gallery"></a>Dodawanie SciQuest wydatków dyrektor z galerii

Aby skonfigurować integrację dyrektora SciQuest wydatków w usłudze Azure AD, należy dodać SciQuest Dyrektor wydatków z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać SciQuest Dyrektor wydatków z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **SciQuest wydatków Dyrektor**, wybierz opcję **Dyrektor wydatki SciQuest** z panelu wynik następnie kliknij przycisk **Dodaj** przycisk, aby dodać aplikację.

     ![Dyrektor SciQuest poświęcić na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą SciQuest Dyrektor wydatki w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkownika w SciQuest wydatków dyrektor musi zostać nawiązane.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą SciQuest wydatków Dyrektor, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie SciQuest wydatków Dyrektor ds. logowanie jednokrotne](#configure-sciquest-spend-director-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Dyrektor wydatki SciQuest](#create-sciquest-spend-director-test-user)**  — aby odpowiednikiem Britta Simon w wydatki Dyrektor SciQuest, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne z SciQuest Dyrektor wydatków, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Dyrektor wydatki SciQuest** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![SciQuest wydatki Dyrektor ds. domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.sciquest.com/apps/Router/SAMLAuth/<instancename>`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.sciquest.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<companyname>.sciquest.com/apps/Router/ExternalAuth/Login/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami adresu URL logowania, identyfikatora i adresu URL odpowiedzi. Skontaktuj się z pomocą [zespołem pomocy technicznej SciQuest wydatków Dyrektor ds. klienta](https://www.jaggaer.com/contact-us/) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie Dyrektor wydatki SciQuest** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-sciquest-spend-director-single-sign-on"></a>Konfigurowanie SciQuest wydatki, Dyrektor ds. logowanie jednokrotne

Do konfigurowania logowania jednokrotnego na **SciQuest wydatków Dyrektor** stronie, musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowany adresy URL z portalu Azure w celu [SciQuest wydatków Dyrektor ds. pomocy technicznej zespół](https://www.jaggaer.com/contact-us/). Ustawiają to ustawienie, aby były prawidłowo po obu stronach połączenia logowania jednokrotnego SAML.

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

W tej sekcji możesz włączyć Britta Simon do używania Azure logowanie jednokrotne za udzielanie dostępu do SciQuest Dyrektor wydania.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Dyrektor wydatki SciQuest**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Dyrektor wydatki SciQuest**.

    ![Link SciQuest Dyrektor poświęcić na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-sciquest-spend-director-test-user"></a>Tworzenie użytkownika testowego SciQuest wydatków Dyrektor

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w SciQuest Dyrektor wydania.

Musisz skontaktować się z Twojego [zespołem pomocy technicznej SciQuest wydatków Dyrektor](https://www.jaggaer.com/contact-us/) i udostępniać szczegółowe informacje o Twoim koncie test, aby został utworzony.

Alternatywnie, można również korzystać w czasie inicjowania obsługi pojedynczego logowania jednokrotnego funkcja, która jest obsługiwana przez dyrektora wydatków SciQuest.  
Jeśli Inicjowanie obsługi administracyjnej just in time jest włączone, użytkownicy są tworzone automatycznie przez dyrektora wydatki SciQuest podczas jednego próba logowania jednokrotnego Jeśli istnieją. Ta funkcja eliminuje potrzebę ręcznego tworzenia użytkowników odpowiednika rejestracji jednokrotnej.

Można pobrać, just-in-time aprowizacji, włączone, musisz skontaktować się z Twojego [zespołem pomocy technicznej SciQuest wydatków Dyrektor](https://www.jaggaer.com/contact-us/).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka SciQuest Dyrektor wydatki w panelu dostępu, użytkownik powinien automatyczne logowanie dyrektorowi SciQuest wydania dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)