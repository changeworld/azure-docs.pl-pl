---
title: 'Samouczek: Integracja usługi Azure Active Directory z aplikacją Workday | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługi Azure Active Directory i Workday.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb9c8d1fb234efd5df297082cfc1001f28ca1656
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990374"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Samouczek: Integracja z produktu Workday za pomocą usługi Azure Active Directory

W tym samouczku dowiesz się, jak zintegrować pracy z usługą Azure Active Directory (Azure AD). Podczas pracy możesz zintegrować z usługą Azure AD, możesz wykonywać następujące czynności:

* Kontrolowanie w usłudze Azure AD, kto ma dostęp do produktu Workday.
* Umożliwianie użytkownikom można automatycznie zalogowany w produkcie Workday, za pomocą kont usługi Azure AD.
* Zarządzanie Twoimi kontami w jednej centralnej lokalizacji — witryny Azure portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć pracę, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz pobrać [bezpłatne konto](https://azure.microsoft.com/free/).
* WORKDAY logowania jednokrotnego (SSO) włączone subskrypcji.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku, skonfiguruj i przetestuj logowania jednokrotnego usługi Azure AD w środowisku testowym. Dzień roboczy obsługuje **SP** jednokrotne logowanie inicjowane przez.

## <a name="adding-workday-from-the-gallery"></a>Dodawanie produktu Workday za pomocą galerii

Aby skonfigurować integrację z produktu Workday do usługi Azure AD, należy dodać produktu Workday za pomocą galerii z listą zarządzanych aplikacji SaaS.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com) przy użyciu służbowego lub osobistego konta Microsoft.
1. W okienku nawigacji po lewej stronie wybierz **usługi Azure Active Directory** usługi.
1. Przejdź do **aplikacje dla przedsiębiorstw** , a następnie wybierz **wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz **nową aplikację**.
1. W **Dodaj z galerii** sekcji, wpisz **Workday** w polu wyszukiwania.
1. Wybierz **Workday** z wyników panelu, a następnie dodać aplikację. Odczekaj kilka sekund, podczas gdy aplikacja zostanie dodany do Twojej dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

Konfiguracja i testowanie logowania jednokrotnego usługi Azure AD z aplikacją Workday za pomocą użytkownika testu o nazwie **Britta Simon**. Logowanie Jednokrotne do pracy musisz ustanowić relację łącza między użytkownika usługi Azure AD i powiązanych użytkowników w produkcie Workday.

Aby skonfigurować i przetestować logowania jednokrotnego usługi Azure AD z aplikacją Workday, wykonaj poniższe bloki konstrukcyjne:

1. **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-sso)**  aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie produktu Workday](#configure-workday)**  do konfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  do testowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  umożliwiające Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie użytkownika testowego Workday](#create-workday-test-user)**  mieć odpowiednikiem Britta Simon w produkcie Workday, połączonego z usługi Azure AD reprezentacja użytkownika.
6. **[Testowanie logowania jednokrotnego](#test-sso)**  Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-sso"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

Wykonaj następujące kroki, aby włączyć logowania jednokrotnego usługi Azure AD w witrynie Azure portal.

1. W [witryny Azure portal](https://portal.azure.com/)na **Workday** strona integracji aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **logowanie jednokrotne**.
1. Na **wybierz jedną metodę logowania jednokrotnego** wybierz **SAML**.
1. Na **Ustaw się logowanie jednokrotne z SAML** kliknij ikonę edycji/pióra **podstawową konfigurację protokołu SAML** edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na **podstawową konfigurację protokołu SAML** strony, wprowadź wartości dla następujących pól:

    a. W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. W polu **Identyfikator** wpisz adres URL, korzystając z następującego wzorca: `https://www.workday.com`

    c. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Rzeczywisty adres URL logowania i adres URL odpowiedzi, należy zaktualizować te wartości. Adres URL odpowiedzi muszą mieć poddomeny na przykład: www, wd2, wd3, wd3 impl, wd5, wd5 impl).
    > Przy użyciu polecenia podobnego `http://www.myworkday.com` działa, ale `http://myworkday.com` nie. Skontaktuj się z pomocą [zespołem pomocy technicznej klienta Workday](https://www.workday.com/partners-services/services/support.html) do uzyskania tych wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja produktu Workday oczekuje twierdzenia SAML w określonym formacie, który wymaga dodania mapowania atrybutów niestandardowych konfiguracji atrybuty tokenu języka SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. WORKDAY aplikacja oczekuje **nameidentifier** mają być mapowane z **user.mail**, **UPN**itd., więc trzeba edytować mapowanie atrybutów, klikając  **Edytuj** ikonę i zmień mapowanie atrybutu.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > W tym miejscu możemy zmapowane identyfikator nazwy przy użyciu nazwy UPN (user.userprincipalname) jako domyślny. Należy zamapować identyfikator nazwy przy użyciu rzeczywisty identyfikator użytkownika na koncie Workday (wiadomości e-mail, nazwy UPN, itp.) dla pomyślnego działania logowania jednokrotnego.

1. Na **Ustaw się logowanie jednokrotne z SAML** strony w **certyfikat podpisywania SAML** sekcji, Znajdź **certyfikat (Base64)** i wybierz **Pobierz** do pobrania certyfikatu i zapisz go na komputerze.

   ![Link pobierania certyfikatu](common/certificatebase64.png)

1. Aby zmodyfikować **podpisywanie** kliknij opcje zgodnie z wymaganiami **Edytuj** przycisk, aby otworzyć **certyfikat podpisywania SAML** okna dialogowego.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Wybierz **odpowiedź i potwierdzenie SAML logowania** dla **opcja podpisywania**.

    b. Kliknij polecenie **Zapisz**.

1. Na **Konfigurowanie produktu Workday** sekcji, skopiuj odpowiednie adresy URL, zgodnie z wymaganiami.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Konfigurowanie produktu Workday

1. W oknie przeglądarki internetowej innej Zaloguj się w witrynie firmy Workday jako administrator.

2. W **pole wyszukiwania** wyszukiwania o nazwie **Edytuj Konfiguracja dzierżawy — zabezpieczenia** u góry po lewej stronie strony głównej.

    ![Edytuj zabezpieczenia dzierżawy](./media/workday-tutorial/IC782925.png "Edytuj zabezpieczenia dzierżawy")

3. W **adresy URL przekierowania** sekcji, wykonaj następujące czynności:

    ![Adresy URL przekierowania](./media/workday-tutorial/IC7829581.png "adresy URL przekierowania")

    a. Kliknij przycisk **Dodaj wiersz**.

    b. W **Przekierowywanie adresu URL logowania do**, **adresu URL przekierowania wygaśnięcia** i **adresu URL przekierowania Mobile** pola tekstowego, Wklej **adres URL logowania** który skopiowano z **Konfigurowanie produktu Workday** części witryny Azure portal.

    c. W **adres URL przekierowania wylogowania** pola tekstowego, Wklej **adres URL wylogowania** skopiowanej z **Konfigurowanie produktu Workday** części witryny Azure portal.

    d. W **używane w środowiskach** pola tekstowego, wybierz nazwę środowiska.  

   > [!NOTE]
   > Wartość atrybutu środowisko jest powiązana wartość adres URL dzierżawy:  
   > -Czy nazwa domeny adres URL dzierżawy produktu Workday rozpoczyna się od impl na przykład: *https:\//impl.workday.com/\<dzierżawy\>/login-saml2.flex*), **środowiska**atrybut należy skonfigurować do implementacji.  
   > — Jeśli nazwa domeny rozpoczyna się czymś innym, musisz skontaktować się z [zespołem pomocy technicznej klienta Workday](https://www.workday.com/partners-services/services/support.html) można pobrać dopasowywania **środowiska** wartość.

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

W tej sekcji utworzymy użytkownika testowego w witrynie Azure portal, o nazwie Britta Simon.

1. W okienku po lewej stronie w witrynie Azure portal wybierz **usługi Azure Active Directory**, wybierz opcję **użytkowników**, a następnie wybierz pozycję **wszyscy użytkownicy**.
1. Wybierz **nowego użytkownika** w górnej części ekranu.
1. W **użytkownika** właściwości, wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `Britta Simon`.  
   1. W **nazwa_użytkownika** wprowadź username@companydomain.extension. Na przykład `BrittaSimon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można udostępnić Britta Simon do używania platformy Azure logowanie jednokrotne za udzielanie dostępu do produktu Workday.

1. W witrynie Azure portal wybierz **aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Na liście aplikacji wybierz **Workday**.
1. Na stronie Przegląd usługi aplikacji, Znajdź **Zarządzaj** i wybierz pozycję **użytkowników i grup**.

   ![Link "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **Dodaj użytkownika**, a następnie wybierz **użytkowników i grup** w **Dodaj przydziału** okna dialogowego.

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W **użytkowników i grup** okno dialogowe, wybierz opcję **Britta Simon** z listy użytkowników, następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. Jeśli oczekujesz wszelkie wartości roli dla asercji SAML w **wybierz rolę** okno dialogowe, wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **wybierz** znajdujący się u dołu ekranu.
1. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-workday-test-user"></a>Tworzenie użytkownika testowego Workday

W tej sekcji utworzysz użytkownika w usłudze WORKDAY aplikacja o nazwie Britta Simon. Praca z [zespołem pomocy technicznej klienta Workday](https://www.workday.com/en-us/partners-services/services/support.html) Aby dodać użytkowników na platformie produktu Workday. Użytkownicy muszą być tworzone i aktywowana, aby używać logowania jednokrotnego.

### <a name="test-sso"></a>Test SSO

Po wybraniu kafelka produktu Workday w panelu dostępu, powinny być automatycznie zarejestrowaniu w usłudze Workday, dla którego skonfigurować logowanie Jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
