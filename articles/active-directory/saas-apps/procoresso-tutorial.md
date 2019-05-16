---
title: 'Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Procore | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Procore logowania jednokrotnego.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4be92cae0042da7341b716a6c3c497b6248eed6a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "65744997"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Samouczek: Integracja usługi Azure Active Directory przy użyciu logowania jednokrotnego Procore

W tym samouczku dowiesz się, jak zintegrować Procore logowanie Jednokrotne z usługą Azure Active Directory (Azure AD).
Integrowanie Procore logowanie Jednokrotne z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Procore logowania jednokrotnego.
* Użytkownikom można automatycznie zalogowany do Procore SSO (logowanie jednokrotne) można włączyć za pomocą kont usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD przy użyciu logowania jednokrotnego Procore, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Procore logowanie Jednokrotne logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje logowanie Jednokrotne procore **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-procore-sso-from-the-gallery"></a>Dodawanie Procore logowania jednokrotnego za pomocą galerii

Aby skonfigurować integrację Procore logowania jednokrotnego w usłudze Azure AD, należy dodać Procore logowania jednokrotnego z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Procore logowania jednokrotnego z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Procore logowania jednokrotnego**, wybierz opcję **Procore logowania jednokrotnego** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

    ![Procore logowania jednokrotnego na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowania i testowania usługi Azure AD logowania jednokrotnego przy użyciu Procore logowania jednokrotnego w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Procore logowania jednokrotnego musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Procore, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Procore logowania jednokrotnego logowania jednokrotnego](#configure-procore-sso-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Procore logowania jednokrotnego](#create-procore-sso-test-user)**  — aby odpowiednikiem Britta Simon w Procore logowanie Jednokrotne połączonej z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowania jednokrotnego przy użyciu logowania jednokrotnego Procore, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Procore logowania jednokrotnego** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** użytkownik nie musi wykonywać żadnych kroków, ponieważ aplikacja jest już wstępnie zintegrowana z platformą Azure.

    ![Procore domena logowania jednokrotnego i adresy URL pojedynczy informacje logowania jednokrotnego](common/preintegrated.png)

5. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **skonfigurować logowanie Jednokrotne Procore** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-procore-sso-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Procore logowania jednokrotnego

1. Aby skonfigurować logowanie jednokrotne na **Procore logowania jednokrotnego** stronie, zaloguj się do witryny firmy procore jako administrator.

2. Z listy przybornika w dół, kliknij pozycję **administratora** o otwarcie strony ustawień logowania jednokrotnego.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_tool_admin.png)

3. Wklej wartość w polach, zgodnie z opisem poniżej-

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. W **pojedynczy znak na adres URL wystawcy** tekstu pole, Wklej wartość **usługi Azure AD identyfikator** skopiowanej w witrynie Azure portal.

    b. W **SAML logowania na docelowy adres URL** pole, Wklej wartość **adres URL logowania** skopiowanej w witrynie Azure portal.

    c. Teraz Otwórz **XML metadanych Federacji** pobrane powyżej w witrynie Azure portal i skopiować certyfikat w tagu o nazwie **X509Certificate**. Wklej skopiowany wartość do **certyfikatu logowania jednokrotnego x509** pole.

4. Kliknij przycisk **Zapisz zmiany**.

5. Po tych ustawień musi wysyłać **nazwy domeny** (np. **contoso.com**) za pomocą którego logujesz się do Procore do [zespołem pomocy technicznej Procore](https://support.procore.com/) i będą oni mogli Aktywuj federacyjnego logowania jednokrotnego dla tej domeny.

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do Procore logowania jednokrotnego.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Procore logowania jednokrotnego**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Procore logowania jednokrotnego**.

    ![Link Procore logowania jednokrotnego na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-procore-sso-test-user"></a>Tworzenie użytkownika testowego Procore logowania jednokrotnego

Wykonaj poniższe kroki, aby utworzyć użytkownika testowego Procore stronie Procore logowania jednokrotnego.

1. Zaloguj się do witryny firmy procore jako administrator.    

2. Z listy przybornika w dół, kliknij pozycję **katalogu** o otwarcie strony katalogu firmy.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Kliknij pozycję **dodać osobę** opcję, aby otworzyć formularz, a następnie wprowadź wykonywać następujące opcje —

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_add.png)

    a. W **imię** pola tekstowego, imię typ użytkownika, takich jak **Britta**.

    b. W **nazwisko** pola tekstowego, nazwisko typ użytkownika, takich jak **Simon**.

    c. W **adres E-mail** , adres e-mail użytkownika typu pole tekstowe, takich jak BrittaSimon@contoso.com.

    d. Wybierz **szablon uprawnień** jako **Zastosuj szablon uprawnień później**.

    e. Kliknij pozycję **Utwórz**.

4. Sprawdź i aktualizowanie szczegółów dla nowo dodanego kontaktu.

    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_check.png)

5. Kliknij pozycję **Zapisz i Wyślij zaproszenie** (Jeśli wymagane jest za pośrednictwem wiadomości e-mail) lub **Zapisz** (zapisane bezpośrednio), aby ukończyć rejestrację użytkownika.
    
    ![Konfigurowanie logowania jednokrotnego](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Procore logowania jednokrotnego w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Procore logowania jednokrotnego, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

