---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Workday | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: jeedes
ms.openlocfilehash: 8451fd692409933803f5f8023f1e1161c3a97daf
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59278535"
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>Samouczek: Integracja usługi Azure Active Directory z aplikacją Workday

W tym samouczku dowiesz się, jak zintegrować pracy z usługą Azure Active Directory (Azure AD).
Integrowanie pracy z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować, czy w usłudze Azure AD, kto ma dostęp do produktu Workday.
* Aby umożliwić użytkownikom można automatycznie zalogowany w produkcie Workday (logowanie jednokrotne) przy użyciu konta usługi Azure AD.
* Możesz zarządzać swoimi kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z aplikacją Workday, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz skorzystać z miesięcznej wersji próbnej [tutaj](https://azure.microsoft.com/pricing/free-trial/)
* WORKDAY logowanie jednokrotne włączone subskrypcji

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Dzień roboczy obsługuje **SP** i **tożsamości** jednokrotne logowanie inicjowane przez

## <a name="adding-workday-from-the-gallery"></a>Dodawanie produktu Workday za pomocą galerii

Aby skonfigurować integrację z produktu Workday do usługi Azure AD, należy dodać produktu Workday za pomocą galerii z listą zarządzanych aplikacji SaaS.

**Aby dodać produktu Workday w galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)**, w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij przycisk **Nowa aplikacja** w górnej części okna dialogowego.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wpisz **Workday**, wybierz opcję **Workday** z panelu wynik kliknięcie **Dodaj** przycisk, aby dodać aplikację.

     ![Dzień roboczy na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji możesz skonfigurować i przetestować usługi Azure AD logowanie jednokrotne z aplikacją Workday, w oparciu o użytkownika testu o nazwie **Britta Simon**.
Dla logowania jednokrotnego do pracy relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w usłudze WORKDAY aplikacja musi zostać ustanowione.

Aby skonfigurować i testowanie usługi Azure AD logowanie jednokrotne z aplikacją Workday, należy wykonać poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** — aby umożliwić użytkownikom korzystanie z tej funkcji.
2. **[Konfigurowanie produktu Workday logowania jednokrotnego](#configure-workday-single-sign-on)**  — Aby skonfigurować ustawienia logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** — aby przetestować logowanie jednokrotne usługi Azure AD z użytkownikiem Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. **[Tworzenie użytkownika testowego Workday](#create-workday-test-user)**  — aby odpowiednikiem Britta Simon w produkcie Workday, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)** — aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować usługę Azure AD logowanie jednokrotne z aplikacją Workday, wykonaj następujące czynności:

1. W [witryny Azure portal](https://portal.azure.com/)na **Workday** strona integracji aplikacji, wybierz opcję **logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. W sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące czynności:

    ![WORKDAY domena i adresy URL pojedynczy informacje logowania jednokrotnego](common/sp-identifier.png)

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https:\//impl.workday.com/<tenant>/login-saml2.flex`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.workday.com`

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujące kroki:

    ![WORKDAY domena i adresy URL pojedynczy informacje logowania jednokrotnego](./media/workday-tutorial/reply.png)

    W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https:\//impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Rzeczywisty adres URL logowania i adres URL odpowiedzi, należy zaktualizować te wartości. Adres URL odpowiedzi muszą mieć poddomeny na przykład: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Przy użyciu polecenia podobnego `http://www.myworkday.com` działa, ale `http://myworkday.com` nie. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja produktu Workday oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. WORKDAY aplikacja oczekuje **nameidentifier** mają być mapowane z **user.mail**, **UPN** itd, więc trzeba edytować mapowanie atrybutów, klikając **edycji**  ikonę i zmień mapowanie atrybutu.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > W tym miejscu możemy zmapowane identyfikator nazwy przy użyciu nazwy UPN (user.userprincipalname) jako domyślny. Należy zamapować identyfikator nazwy przy użyciu rzeczywisty identyfikator użytkownika na koncie Workday (wiadomości e-mail, nazwy UPN itp.) dla pomyślnego działania logowania jednokrotnego.

7. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **certyfikat (Base64)** z podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link do pobierania certyfikatu](common/certificatebase64.png)

8. Na **Konfigurowanie produktu Workday** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-workday-single-sign-on"></a>Konfigurowanie Workday logowania jednokrotnego

1. W oknie przeglądarki innej witryny sieci web należy zalogować się jako administrator do witryny firmy Workday.

2. W **pole wyszukiwania** wyszukiwania o nazwie **Edytuj Konfiguracja dzierżawy — zabezpieczenia** u góry po lewej stronie strony głównej.

    ![Edytuj zabezpieczenia dzierżawy](./media/workday-tutorial/IC782925.png "Edytuj zabezpieczenia dzierżawy")

3. W **adresy URL przekierowania** sekcji, wykonaj następujące czynności:

    ![Adresy URL przekierowania](./media/workday-tutorial/IC7829581.png "adresy URL przekierowania")

    a. Kliknij przycisk **Dodaj wiersz**.

    b. W **Przekierowywanie adresu URL logowania do** pole tekstowe i **adresu URL przekierowania Mobile** polu tekstowym wpisz **adres URL logowania** zostały wprowadzone na **podstawową konfigurację protokołu SAML**  części witryny Azure portal.

    c. W witrynie Azure portal na **Konfigurowanie produktu Workday** sekcji, skopiuj **adres URL wylogowania**, a następnie wklej go do **adres URL przekierowania wylogowania** pola tekstowego.

    d. W **używane w środowiskach** pola tekstowego, wybierz nazwę środowiska.  

   > [!NOTE]
   > Wartość atrybutu środowisko jest powiązana wartość adres URL dzierżawy:  
   > -Czy nazwa domeny adres URL dzierżawy produktu Workday rozpoczyna się od impl na przykład: *https:\//impl.workday.com/\<dzierżawy\>/login-saml2.flex*), **środowiska**atrybut należy skonfigurować do implementacji.  
   > — Jeśli nazwa domeny rozpoczyna się czymś innym, musisz skontaktować się z [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) można pobrać dopasowywania **środowiska** wartość.

4. W **konfiguracji SAML** sekcji, wykonaj następujące czynności:

    ![Konfiguracja protokołu SAML](./media/workday-tutorial/IC782926.png "Konfiguracja protokołu SAML")

    a.  Wybierz **włączyć uwierzytelnianie SAML**.

    b.  Kliknij przycisk **Dodaj wiersz**.

5. W **dostawcy tożsamości SAML** sekcji, wykonaj następujące czynności:

    ![Dostawcy tożsamości SAML](./media/workday-tutorial/IC7829271.png "SAML dostawcy tożsamości")

    a. W **nazwę dostawcy tożsamości** polu tekstowym wpisz nazwę dostawcy (na przykład: *SPInitiatedSSO*).

    b. W witrynie Azure portal na **Konfigurowanie produktu Workday** sekcji, skopiuj **usługi Azure AD identyfikator** wartość, a następnie wklej go do **wystawcy** pola tekstowego.

    ![Dostawcy tożsamości SAML](./media/workday-tutorial/IC7829272.png "SAML dostawcy tożsamości")

    c. W witrynie Azure portal na **Konfigurowanie produktu Workday** sekcji, skopiuj **adres URL wylogowania** wartość, a następnie wklej go do **adres URL wylogowania odpowiedzi** pola tekstowego.

    d. W witrynie Azure portal na **Konfigurowanie produktu Workday** sekcji, skopiuj **adres URL logowania** wartość, a następnie wklej go do **adres URL usługi logowania jednokrotnego dostawcy tożsamości** pola tekstowego.

    e. W **używane w środowiskach** pola tekstowego, wybierz nazwę środowiska.

    f. Kliknij przycisk **certyfikatu klucza publicznego dostawcy tożsamości**, a następnie kliknij przycisk **Utwórz**.

    ![Tworzenie](./media/workday-tutorial/IC782928.png "tworzenie")

    g. Kliknij przycisk **tworzenie x509 klucza publicznego**.

    ![Tworzenie](./media/workday-tutorial/IC782929.png "tworzenie")

6. W **klucza publicznego widoku x509** sekcji, wykonaj następujące czynności:

    ![Klucz publiczny widoku x509](./media/workday-tutorial/IC782930.png "widoku x509 klucza publicznego")

    a. W **nazwa** polu tekstowym wpisz nazwę certyfikatu (na przykład: *PPE\_SP*).

    b. W **ważny od** polu tekstowym wpisz prawidłowe wartości atrybutu certyfikatu.

    c.  W **ważny do** polu tekstowym wpisz prawidłową wartość atrybutu certyfikatu.

    > [!NOTE]
    > Możesz uzyskać prawidłowe daty i prawidłowe, do tej pory z pobranego certyfikatu, klikając go dwukrotnie.  Daty są wyświetlane w obszarze **szczegóły** kartę.
    >
    >

    d.  Otwórz swój certyfikat zakodowany base-64 w Notatniku, a następnie skopiuj zawartość go.

    e.  W **certyfikatu** pola tekstowego, Wklej zawartość Schowka.

    f.  Kliknij przycisk **OK**.

7. Wykonaj poniższe czynności:

    ![Konfiguracja logowania jednokrotnego](./media/workday-tutorial/WorkdaySSOConfiguratio.png "konfiguracji logowania jednokrotnego")

    a.  W **identyfikator dostawcy usługi** polu tekstowym wpisz **https://www.workday.com**.

    b. Wybierz **nie Deflate żądania zainicjowanego przez dostawcę usług uwierzytelniania**.

    c. Jako **podpisu żądania uwierzytelnienia**, wybierz opcję **SHA256**.

    ![Metoda podpisu żądania uwierzytelniania](./media/workday-tutorial/WorkdaySSOConfiguration.png "metoda podpisu żądania uwierzytelniania") 

    d. Kliknij przycisk **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Upewnij się, że możesz poprawnie skonfigurowane logowanie jednokrotne. W przypadku, gdy zostanie włączone logowanie jednokrotne za pomocą niewłaściwe ustawienia nie można wprowadzić aplikacji przy użyciu poświadczeń i Uzyskaj zablokowane. W takiej sytuacji Workday zawiera adres url kopii zapasowych dziennika w przypadku gdy użytkownicy mogą Zaloguj się przy użyciu swojej zwykłej nazwy użytkownika i hasła w następującym formacie: [Your Workday URL]/login.flex?redirect=n

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

W tej sekcji możesz włączyć Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do produktu Workday.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, wybierz opcję **wszystkie aplikacje**, a następnie wybierz **Workday**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście aplikacji wybierz **Workday**.

    ![Link Workday, na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-workday-test-user"></a>Tworzenie użytkownika testowego Workday

W tej sekcji utworzysz użytkownika w usłudze WORKDAY aplikacja o nazwie Britta Simon. Praca z [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) Aby dodać użytkowników na platformie produktu Workday. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego 

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka produktu Workday w panelu dostępu, powinien zostać automatycznie zarejestrowaniu w usłudze Workday, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

