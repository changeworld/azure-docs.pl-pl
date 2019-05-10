---
title: 'Samouczek: Integracja usługi Azure Active Directory transferu MOVEit — Integracja usługi Azure AD | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i MOVEit Transfer — Integracja usługi Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8ff7102d-be73-4888-ae81-d8e3d01dd534
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 2a491cd303cccd94cbb489654890d55c03912a63
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2019
ms.locfileid: "65408040"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Samouczek: Integracja usługi Azure Active Directory transferu MOVEit — Integracja usługi Azure AD

W tym samouczku dowiesz się, jak zintegrować MOVEit Transfer — Integracja usługi Azure AD z usługą Azure Active Directory (Azure AD).
Integrowanie MOVEit Transfer — Integracja usługi Azure AD z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do MOVEit Transfer — Integracja usługi Azure AD.
* Aby umożliwić użytkownikom można automatycznie zalogował się MOVEit Transfer — Integracja usługi Azure AD (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Do konfigurowania integracji z usługą Azure AD z MOVEit Transfer — Integracja usługi Azure AD, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* MOVEit Transfer — usługa Azure AD integracji logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* MOVEit Transfer — Integracja usługi Azure AD obsługuje **SP** jednokrotne logowanie inicjowane przez

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>Dodawanie MOVEit Transfer — Integracja usługi Azure AD za pomocą galerii

Aby skonfigurować integrację MOVEit Transfer — Integracja usługi Azure AD w usłudze Azure AD, należy dodać MOVEit Transfer — Integracja usługi Azure AD za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać MOVEit Transfer — Integracja usługi Azure AD z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **MOVEit Transfer — Integracja usługi Azure AD**, wybierz opcję **MOVEit Transfer — Integracja usługi Azure AD** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikacja.

     ![MOVEit Transfer — Integracja usługi Azure AD na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne za pomocą MOVEit Transfer — Integracja usługi Azure AD, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy, relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w MOVEit Transfer — integracji z usługą Azure AD musi zostać ustanowione.

Do konfigurowania i testowania usługi Azure AD logowanie jednokrotne za pomocą MOVEit Transfer — Integracja usługi Azure AD, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie MOVEit Transfer — Integracja usługi Azure AD, logowania jednokrotnego](#configure-moveit-transfer---azure-ad-integration-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Utwórz MOVEit Transfer — użytkownik test integracji usługi Azure AD](#create-moveit-transfer---azure-ad-integration-test-user)**  — aby odpowiednikiem Britta Simon w MOVEit Transfer — Integracja usługi Azure AD, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD w logowanie jednokrotne za pomocą MOVEit Transfer — Integracja usługi Azure AD, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **MOVEit Transfer — Integracja usługi Azure AD** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML**, jeśli masz **plik metadanych dostawcy usługi**, wykonaj następujące kroki:

    a. Kliknij pozycję **Przekaż plik metadanych**.

    ![Przekazywanie pliku metadanych](common/upload-metadata.png)

    b. Kliknij **logo folderu**, aby wybrać plik metadanych, a następnie kliknij pozycję **Przekaż**.

    ![wybierz plik metadanych](common/browse-upload-metadata.png)

    c. Po pomyślnym przekazaniu pliku metadanych **identyfikator** i **adres URL odpowiedzi** wartość pobiera automatycznie wypełnione w **podstawową konfigurację protokołu SAML** sekcji:

    ![MOVEit Transfer — Integracja usługi Azure AD, domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/sp-identifier-reply.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://contoso.com`

    > [!NOTE]
    > **Adres URL logowania** wartość nie jest prawdziwe. Zastąp tę wartość rzeczywistym adresem URL logowania. Skontaktuj się z pomocą [MOVEit Transfer — Integracja usługi Azure AD Obsługa klientów](https://community.ipswitch.com/s/support) zespołu, aby uzyskać wartość. Możesz pobrać **pliku metadanych dostawcy usługi** z **adres URL metadanych dostawcy usługi** co jest opisane w dalszej części w **skonfigurować MOVEit Transfer — Integracja usługi Azure AD pojedynczego Logowanie jednokrotne** części samouczka. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **skonfigurować MOVEit Transfer — Integracja usługi Azure AD** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Konfigurowanie MOVEit Transfer — Integracja usługi Azure AD, logowania jednokrotnego

1. Zaloguj się jako administrator dzierżawy MOVEit transferu.

2. W okienku nawigacji po lewej stronie kliknij pozycję **Settings** (Ustawienia).

    ![Po stronie sekcji w aplikacji ustawienia](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Kliknij przycisk **pojedynczego logować** łącza, która jest w trakcie **zasad zabezpieczeń -> Uwierzytelnianie użytkownika**.

    ![Po stronie aplikacji na zasady zabezpieczeń](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Kliknij link adres URL metadanych, aby pobrać dokumentu metadanych.

    ![Adres URL metadanych dostawcy usługi](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Sprawdź **entityID** odpowiada **identyfikator** w **podstawową konfigurację protokołu SAML** sekcji.
   * Sprawdź **AssertionConsumerService** pasuje do adresu URL lokalizacji **adres URL odpowiedzi** w **podstawową konfigurację protokołu SAML** sekcji.
    
     ![Konfigurowanie logowania jednokrotnego po stronie aplikacji](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Kliknij przycisk **Dodawanie dostawcy tożsamości** przycisk, aby dodać nowego dostawcę tożsamości federacyjnych.

    ![Dodawanie dostawcy tożsamości](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Kliknij przycisk **Przeglądaj...**  aby wybrać plik metadanych, który został pobrany z witryny Azure portal, a następnie przycisk **Dodawanie dostawcy tożsamości** można przekazać pobranego pliku.

    ![Dostawcy tożsamości SAML](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Wybierz pozycję "**tak**" jako **włączone** w **edytowanie federacyjnej ustawień dostawcy tożsamości...**  strony, a następnie kliknij przycisk **Zapisz**.

    ![Ustawienia dostawcy tożsamości federacyjnych](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. W **edytowanie ustawień tożsamości federacyjnych dostawca użytkownika** strony, wykonaj następujące czynności:
    
    ![Edytuj ustawienia dostawcy tożsamości federacyjnych](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Wybierz **identyfikatora SAML NameID** jako **nazwa logowania**.
    
    b. Wybierz **innych** jako **imię i nazwisko** i **nazwa atrybutu** textbox Umieść wartość: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Wybierz **innych** jako **wiadomości E-mail** i **nazwa atrybutu** textbox Umieść wartość: `https://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Wybierz **tak** jako **automatycznego tworzenia konta logowaniu**.
    
    e. Kliknij przycisk **Zapisz** przycisku.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W **nazwa_użytkownika** typ pola **brittasimon\@yourcompanydomain.extension**  
    Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do MOVEit Transfer — Integracja usługi Azure AD.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **MOVEit Transfer — Integracja usługi Azure AD**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **MOVEit Transfer — Integracja usługi Azure AD**.

    ![MOVEit Transfer — Integracja usługi Azure AD łącze na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Utwórz MOVEit Transfer — użytkownik test integracji usługi Azure AD

Celem tej sekcji jest utworzyć użytkownika o nazwie Britta Simon w MOVEit Transfer — Integracja usługi Azure AD. MOVEit Transfer — Integracja usługi Azure AD obsługę just-in-time, które mają włączone. W tej sekcji nie musisz niczego robić. Nowy użytkownik jest tworzony podczas próby dostępu MOVEit Transfer — Integracja usługi Azure AD, jeśli go jeszcze nie istnieje.

>[!NOTE]
>Jeśli potrzebujesz ręcznie utworzyć użytkownika, musisz skontaktować się z [MOVEit Transfer — zespół obsługi klienta integracji usługi Azure AD](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu MOVEit Transfer — Kafelek integracji usługi Azure AD w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze MOVEit Transfer — Integracja usługi Azure AD, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących integrowania aplikacji SaaS w usłudze Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

