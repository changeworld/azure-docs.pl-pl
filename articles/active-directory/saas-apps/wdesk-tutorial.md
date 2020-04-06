---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą Wdesk | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a usługą Wdesk.
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
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: 349a9920adc0d9353ee8e8e821f54bc2ea2f2616
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-wdesk"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory (Logowanie jednokrotne) z usługą Wdesk

W tym samouczku dowiesz się, jak zintegrować program Wdesk z usługą Azure Active Directory (Azure AD). Po zintegrowaniu programu Wdesk z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do usługi Wdesk.
* Włącz użytkownikom automatyczne logowanie do usługi Wdesk za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego (SSO) firmy Wdesk.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Wdesk obsługuje **sp** i **idp** zainicjowane SSO
* Po skonfigurowaniu programu Wdesk można wymusić kontrolę sesji, która chroni eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-wdesk-from-the-gallery"></a>Dodawanie Wdesk z galerii

Aby skonfigurować integrację usługi Wdesk z usługą Azure AD, należy dodać usługę Wdesk z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **polecenie Wdesk** w polu wyszukiwania.
1. Wybierz **pozycję Wdesk** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą Wdesk na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w programie Wdesk.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Wdesk, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj samouszeńcę usługi Azure AD](#configure-azure-ad-sso)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić B.Simon używać logowania jednokrotnego usługi Azure AD.
1. **[Skonfiguruj logowanie jednokrotne Wdesk](#configure-wdesk-sso)** — aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego Wdesk](#create-wdesk-test-user)** — aby mieć odpowiednik B.Simon w wdesk, który jest połączony z reprezentacją użytkownika usługi Azure AD.
1. **[Test SSO](#test-sso)** - aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą usługi Wdesk, wykonaj następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Wdesk** wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja SAML,** jeśli chcesz skonfigurować aplikację w trybie inicjowanym **przez IDP,** wykonaj następujące czynności:

    ![Informacje o logach w domenie Wdesk i adresach URL](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`https://<subdomain>.wdesk.com/auth/saml/sp/metadata/<instancename>`

    b. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/saml/sp/consumer/<instancename>`

5. Kliknij **pozycję Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowanym w sp: **SP**

    ![Informacje o logach w domenie Wdesk i adresach URL](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://<subdomain>.wdesk.com/auth/login/saml/<instancename>`

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp je rzeczywistymi wartościami identyfikatora, adresu URL odpowiedzi i adresu URL logowania. Te wartości są utrzymykiwki z portalu WDesk podczas konfigurowania samego i sycącego.

4. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/metadataxml.png)

6. W sekcji **Konfigurowanie programu Wdesk** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    d. Adres URL wylogowywania

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

W tej sekcji można włączyć Britta Simon do korzystania z usługi Azure logowania jednokrotnego, udzielając dostępu do Wdesk.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, wybierz **pozycję Wszystkie aplikacje,** a następnie wybierz pozycję **Wdesk**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz pozycję **Wdesk**.

    ![Łącze Wdesk na liście Aplikacje](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, a następnie w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

## <a name="configure-wdesk-sso"></a>Konfigurowanie usługi SSO programu Wdesk

1. W innym oknie przeglądarki internetowej zaloguj się do Wdesk jako administrator zabezpieczeń.

2. W lewym dolnym rogu kliknij pozycję **Administrator** i wybierz pozycję **Administrator konta**:
 
     ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. W aplikacji Wdesk Admin przejdź do pozycji **Zabezpieczenia**, a następnie **ustawienia SAML** > **SAML**:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig2.png)

1. W obszarze **Ustawienia identyfikatora użytkownika SAML**sprawdź, czy **identyfikator użytkownika SAML to Nazwa użytkownika firmy Wdesk**.

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/wdesk-username.png)

4. W obszarze **Ustawienia ogólne**zaznacz opcję **Włącz logowanie jednokrotne SAML:**

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig3.png)

5. W obszarze **Szczegóły usługodawcy**wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig4.png)

      a. Skopiuj **adres URL logowania** i wklej go w pola tekstowym adresu URL logowania w **witrynie** Azure portal.
   
      b. Skopiuj **adres URL metadanych** i wklej go w pola tekstowym **identyfikatora** w witrynie Azure portal.
       
      d. Skopiuj **adres URL konsumenta** i wklej go w pola tekstowym **Odpowiedz adres URL** w witrynie Azure portal.
   
      d. Kliknij **pozycję Zapisz** w witrynie Azure portal, aby zapisać zmiany.      

6. Kliknij **pozycję Konfiguruj ustawienia IdP,** aby otworzyć okno dialogowe **Edytuj ustawienia IdP.** Kliknij **pozycję Wybierz plik,** aby zlokalizować plik **Metadata.xml** zapisany w witrynie Azure portal, a następnie przekaż go.
    
    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig5.png)
  
7. Kliknij **pozycję Zapisz zmiany**.

    ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfigsavebutton.png)

### <a name="create-wdesk-test-user"></a>Tworzenie użytkownika testowego Wdesk

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do usługi Wdesk, muszą one być aprowidywne w witrynie Wdesk. W Wdesk inicjowania obsługi administracyjnej jest zadanie ręczne.

**Aby aprowizować konto użytkownika, wykonaj następujące czynności:**

1. Zaloguj się do Wdesk jako administrator zabezpieczeń.

2. Przejdź do strony Administrator konta **administratora** > **Account Admin**.

     ![Konfigurowanie logowania jednokrotnego](./media/wdesk-tutorial/tutorial_wdesk_ssoconfig1.png)

3. Kliknij **pozycję Członkowie** w obszarze **Kontakty**.

4. Teraz kliknij pozycję **Dodaj członka,** aby otworzyć okno dialogowe **Dodawanie członka.** 
   
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser1.png)  

5. W polu tekstowym **Użytkownik** wprowadź b.simon@contoso.com nazwę użytkownika polub i kliknij przycisk **Kontynuuj.**

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser3.png)

6.  Wprowadź szczegóły, jak pokazano poniżej:
  
    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser4.png)
 
    a. W polu **tekstowym Wiadomości e-mail** b.simon@contoso.comwprowadź adres e-mail użytkownika w stylu .

    b. W polu **tekstowym Imię** wprowadź imię użytkownika, takiego jak **B**.

    d. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, takiego jak **Simon**.

7. Kliknij przycisk **Zapisz członka.**  

    ![Tworzenie użytkownika testowego usługi Azure AD](./media/wdesk-tutorial/createuser5.png)

### <a name="test-sso"></a>Test SSO 

W tej sekcji przetestujesz konfigurację logowania jednokrotnego usługi Azure AD przy użyciu panelu dostępu.

Po kliknięciu kafelka Wdesk w Panelu dostępu należy automatycznie zalogować się do wydzielić w programie Wdesk, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)