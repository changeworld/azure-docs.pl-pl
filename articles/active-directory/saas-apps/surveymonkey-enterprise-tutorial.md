---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Enterprise usługę SurveyMonkey na | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i usługę SurveyMonkey Enterprise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a2d9b57-27b5-4d9f-b52c-09b692872cf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fa05181d777d25af1f682e827a2c43a09da7302
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65158371"
---
# <a name="tutorial-azure-active-directory-integration-with-surveymonkey-enterprise"></a>Samouczek: Integracja usługi Azure Active Directory z usługą Enterprise usługę SurveyMonkey na

W tym samouczku dowiesz się, jak zintegrować usługę SurveyMonkey na przedsiębiorstwa za pomocą usługi Azure Active Directory (Azure AD).
Integracja przedsiębiorstwa usługę SurveyMonkey z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do organizacji usługę SurveyMonkey.
* Użytkownikom można automatycznie zalogowany do usługę SurveyMonkey Enterprise (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą Enterprise usługę SurveyMonkey na, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Enterprise usługę SurveyMonkey na logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje usługę SurveyMonkey Enterprise **dodatkiem SP oraz dostawców tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-surveymonkey-enterprise-from-the-gallery"></a>Dodawanie usługę SurveyMonkey przedsiębiorstwa z galerii

Aby skonfigurować integrację, usługę SurveyMonkey na przedsiębiorstwa w usłudze Azure AD, musisz dodać usługę SurveyMonkey przedsiębiorstwa z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać usługę SurveyMonkey przedsiębiorstwa z galerii, wykonaj następujące czynności:**

1. W witrynie **[Azure Portal](https://portal.azure.com)** w panelu nawigacyjnym po lewej stronie kliknij ikonę **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Enterprise usługę SurveyMonkey**, wybierz opcję **Enterprise usługę SurveyMonkey na** z panelu wyników, a następnie kliknij **Dodaj** przycisk, aby dodać aplikację.

    ![Enterprise usługę SurveyMonkey na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji skonfigurujesz i test usługi Azure AD logowanie jednokrotne z usługą Enterprise usługę SurveyMonkey w oparciu o nazwie użytkownika testowego **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w przedsiębiorstwie usługę SurveyMonkey na musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z usługą Enterprise usługę SurveyMonkey, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Skonfiguruj usługę SurveyMonkey logowania jednokrotnego dla firm](#configure-surveymonkey-enterprise-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkowników testowych w przedsiębiorstwie usługę SurveyMonkey](#create-surveymonkey-enterprise-test-user)**  — aby odpowiednikiem Britta Simon w przedsiębiorstwie usługę SurveyMonkey, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługi Azure AD logowanie jednokrotne, usługę SurveyMonkey Enterprise, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Enterprise usługę SurveyMonkey** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą języka SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja języka SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Na **podstawową konfigurację protokołu SAML** sekcji, aplikacja jest wstępnie skonfigurowana i wymaganych adresów URL już są wstępnie wypełniane przy użyciu platformy Azure. Użytkownik musi zapisać konfigurację, klikając **Zapisz** przycisku.

    ![Usługę SurveyMonkey Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/preintegrated.png)

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Usługę SurveyMonkey Enterprise domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/both-signonurl.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL: ` https://www.surveymonkey.com/user/sign-in/sso/?ut_source=megamenu`

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

7. Na **skonfigurować usługę SurveyMonkey Enterprise** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-surveymonkey-enterprise-single-sign-on"></a>Skonfiguruj usługę SurveyMonkey logowania jednokrotnego dla firm

Aby skonfigurować logowanie jednokrotne na **Enterprise usługę SurveyMonkey na** stronie, musisz wysłać pobrany **XML metadanych Federacji** i odpowiednie skopiowany adresy URL z portalu Azure, aby [usługę SurveyMonkey na Zespół pomocy technicznej Enterprise](mailto:support@selerix.com). Ustawią oni to ustawienie tak, aby połączenie logowania jednokrotnego SAML było ustawione właściwie po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do przedsiębiorstwa usługę SurveyMonkey.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz pozycję **Enterprise usługę SurveyMonkey na**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Enterprise usługę SurveyMonkey**.

    ![Link Enterprise usługę SurveyMonkey na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W **użytkowników i grup** okna dialogowego wybierz **Britta Simon** listy użytkowników, a następnie kliknij **wybierz** znajdujący się u dołu ekranu.

6. Jeśli dowolna wartość roli dla asercji SAML są oczekiwane w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-surveymonkey-enterprise-test-user"></a>Tworzenie użytkowników testowych w przedsiębiorstwie usługę SurveyMonkey

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w przedsiębiorstwie usługę SurveyMonkey. Praca z [zespołem pomocy technicznej Enterprise usługę SurveyMonkey](https://help.surveymonkey.com/) Aby dodać użytkowników na platformie usługę SurveyMonkey przedsiębiorstwa. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Enterprise usługę SurveyMonkey na panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Enterprise usługę SurveyMonkey, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [ Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
