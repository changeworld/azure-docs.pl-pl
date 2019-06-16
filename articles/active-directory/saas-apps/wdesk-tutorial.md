---
title: 'Samouczek: Integracja usługi Azure Active Directory za pomocą Wdesk | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Wdesk.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 06900a91-a326-4663-8ba6-69ae741a536e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 0f614838ae44b5c4263bc9eac81e43fd13f87baa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "67087288"
---
# <a name="tutorial-azure-active-directory-integration-with-wdesk"></a>Samouczek: Integracja usługi Azure Active Directory za pomocą Wdesk

W tym samouczku dowiesz się, jak zintegrować Wdesk w usłudze Azure Active Directory (Azure AD).
Integrowanie Wdesk z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do Wdesk.
* Aby umożliwić użytkownikom można automatycznie zalogowany do Wdesk (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD za pomocą Wdesk, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie ma środowiska usługi Azure AD, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/)
* Wdesk logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Obsługuje Wdesk **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-wdesk-from-the-gallery"></a>Dodawanie Wdesk z galerii

Aby skonfigurować integrację Wdesk w usłudze Azure AD, należy dodać Wdesk z galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać Wdesk z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Wdesk**, wybierz opcję **Wdesk** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Wdesk na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji, konfigurowanie i testowanie usługi Azure AD logowanie jednokrotne za pomocą Wdesk w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanego użytkownika w Wdesk musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne za pomocą Wdesk, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie Wdesk logowania jednokrotnego](#configure-wdesk-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Wdesk](#create-wdesk-test-user)**  — aby odpowiednikiem Britta Simon w Wdesk połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z Wdesk, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Wdesk** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Wdesk domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/idp-intiated.png)

    a. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Wdesk domena i adresy URL pojedynczego logowania jednokrotnego informacji](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości można uzyskać od WDesk portalu, podczas konfigurowania logowania jednokrotnego.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

6. Na **Konfigurowanie Wdesk** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-wdesk-single-sign-on"></a>Konfigurowanie Wdesk logowanie jednokrotne

1. W oknie przeglądarki internetowej innej Zaloguj się do Wdesk jako Administrator zabezpieczeń.

2. W lewym dolnym rogu, kliknij przycisk **administratora** i wybierz polecenie **administrator konta**:
 
     ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. W Wdesk administratora, przejdź do **zabezpieczeń**, następnie **SAML** > **ustawienia języka SAML**:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

4. W obszarze **ustawienia ogólne**, sprawdź **Włącz SAML logowania jednokrotnego**:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. W obszarze **szczegóły dostawcy usługi**, wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Kopiuj **adres URL logowania** i wklej go w **adres Url logowania** pole tekstowe w witrynie Azure portal.
   
      b. Kopiuj **adres Url metadanych** i wklej go w **identyfikator** pole tekstowe w witrynie Azure portal.
       
      c. Kopiuj **adres url klienta** i wklej go w **adres Url odpowiedzi** pole tekstowe w witrynie Azure portal.
   
      d. Kliknij przycisk **Zapisz** w witrynie Azure portal, aby zapisać zmiany.      

6. Kliknij przycisk **Konfigurowanie ustawień protokołu IdP** otworzyć **edytowanie ustawień dostawców tożsamości** okna dialogowego. Kliknij przycisk **wybierz plik** zlokalizować **Metadata.xml** następnie przekaż plik został zapisany w witrynie Azure portal.
    
    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Kliknij przycisk **Zapisz zmiany**.

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

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

W tej sekcji możesz włączyć Britta Simon do używania usługi Azure logowanie jednokrotne za udzielanie dostępu do Wdesk.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Wdesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Wdesk**.

    ![Link Wdesk na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-wdesk-test-user"></a>Tworzenie użytkownika testowego Wdesk

Aby umożliwić użytkownikom usługi Azure AD, zaloguj się do Wdesk, musi być obsługiwana w Wdesk. W Wdesk Inicjowanie obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do Wdesk jako Administrator zabezpieczeń.

2. Przejdź do **administratora** > **konta administratora**.

     ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kliknij przycisk **członków** w obszarze **osób**.

4. Teraz klikaj polecenie **Dodawanie elementu członkowskiego** otworzyć **Dodawanie elementu członkowskiego** okno dialogowe. 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. W **użytkownika** tekstu wprowadź nazwę użytkownika, użytkownika, takich jak brittasimon@contoso.com i kliknij przycisk **Kontynuuj** przycisku.

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Wprowadź szczegółowe informacje, jak pokazano poniżej:
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. W **E-mail** tekstu wprowadź adres e-mail użytkownika, takich jak brittasimon@contoso.com.

    b. W polu tekstowym **First Name** (Imię) wprowadź imię użytkownika, na przykład **Britta**.

    c. W polu tekstowym **Last name** (Nazwisko) wprowadź nazwisko użytkownika, na przykład **Simon**.

7. Kliknij przycisk **zapisać element członkowski** przycisku.  

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka Wdesk w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Wdesk, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

