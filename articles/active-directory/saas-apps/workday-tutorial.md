---
title: 'Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Workday | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a workday.
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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a2f39b6f58b250d68a3b2ce962f158c7df36d812
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77046597"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workday"></a>Samouczek: Integracja rejestracji jednokrotnej usługi Azure Active Directory z usługą Workday

W tym samouczku dowiesz się, jak zintegrować workday z usługą Azure Active Directory (Azure AD). Po zintegrowaniu Workday z usługą Azure AD można:

* Kontrola w usłudze Azure AD, który ma dostęp do Workday.
* Włącz użytkownikom automatyczne logowanie do workday za pomocą ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji — witrynie Azure Portal.

Aby dowiedzieć się więcej o integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto.](https://azure.microsoft.com/free/)
* Subskrypcja z włączoną funkcją logowania jednokrotnego w dniu roboczym.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować samouszeńców usługi Azure AD w środowisku testowym.

* Workday **obsługuje** sp zainicjowane SSO.

* Po skonfigurowaniu workday można wymusić kontrolę sesji, które chronią eksfiltrację i infiltrację poufnych danych organizacji w czasie rzeczywistym. Kontrola sesji rozciąga się od dostępu warunkowego. [Dowiedz się, jak wymusić kontrolę nad sesją za pomocą programu Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-workday-from-the-gallery"></a>Dodawanie dnia roboczego z galerii

Aby skonfigurować integrację programu Workday z usługą Azure AD, należy dodać workday z galerii do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta służbowego lub konta firmy Microsoft.
1. W lewym okienku nawigacji wybierz usługę **Azure Active Directory.**
1. Przejdź do **aplikacji korporacyjnych,** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Aby dodać nową aplikację, wybierz pozycję **Nowa aplikacja**.
1. W sekcji **Dodaj z galerii** wpisz **Dzień roboczy** w polu wyszukiwania.
1. Wybierz **pozycję Dzień roboczy** z panelu wyników, a następnie dodaj aplikację. Poczekaj kilka sekund, gdy aplikacja zostanie dodana do dzierżawy.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workday"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD dla dnia roboczego

Konfigurowanie i testowanie usługi Azure AD SSO przy użyciu usługi Workday przy użyciu użytkownika testowego o nazwie **B.Simon**. Aby użytkownik łączony sytuować działał, należy ustanowić relację łącza między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w workday.

Aby skonfigurować i przetestować usługę Azure AD SSO za pomocą usługi Workday, wykonaj następujące bloki konstrukcyjne:

1. **[Skonfiguruj sytuasz usługi Azure AD,](#configure-azure-ad-sso)** aby umożliwić użytkownikom korzystanie z tej funkcji.
    1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą usługi B.Simon.
    1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B.Simon do korzystania z usługi Azure AD logowania jednokrotnego.
2. **[Skonfiguruj dzień roboczy,](#configure-workday)** aby skonfigurować ustawienia logowania przyusznikowego po stronie aplikacji.
    1. **[Utwórz użytkownika testowego workday](#create-workday-test-user)** mieć odpowiednik B.Simon w Workday, który jest połączony z reprezentacji usługi Azure AD użytkownika.
3. **[Przetestuj sytą próbę sycącą,](#test-sso)** aby sprawdzić, czy konfiguracja działa.

## <a name="configure-azure-ad-sso"></a>Konfigurowanie rejestracji jednokrotnej w usłudze Azure AD

Wykonaj następujące kroki, aby włączyć usługę Azure AD SSO w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie Integracja aplikacji **Workday** znajdź sekcję **Zarządzaj** i wybierz pozycję **Logowanie jednokrotne**.
1. Na stronie **Wybierz metodę logowania jednokrotnego** wybierz pozycję **SAML**.
1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** kliknij ikonę edycji/pióra dla **podstawowej konfiguracji SAML,** aby edytować ustawienia.

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. Na stronie **Podstawowa konfiguracja SAML** wprowadź wartości dla następujących pól:

    a. W polu **tekstowym Podpisywania adresu URL** wpisz adres URL, używając następującego wzorca:`https://impl.workday.com/<tenant>/login-saml2.flex`

    b. W polu tekstowym **Identyfikator** wpisz adres URL przy użyciu następującego wzorca:`http://www.workday.com`

    d. W polu tekstowym **Adres URL odpowiedzi** wpisz adres URL, korzystając z następującego wzorca: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Te wartości nie są rzeczywiste. Zaktualizuj te wartości za pomocą rzeczywistego adresu URL logowania i adresu URL odpowiedzi. Adres URL odpowiedzi musi mieć na przykład poddomenę: www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Używanie czegoś `http://www.myworkday.com` `http://myworkday.com` takiego działa, ale nie. Skontaktuj się z [zespołem pomocy technicznej klienta workday,](https://www.workday.com/en-us/partners-services/services/support.html) aby uzyskać te wartości. Przydatne mogą się również okazać wzorce przedstawione w sekcji **Podstawowa konfiguracja protokołu SAML** w witrynie Azure Portal.

6. Aplikacja Workday oczekuje potwierdzeń SAML w określonym formacie, który wymaga dodania mapowań atrybutów niestandardowych do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Aplikacja Workday **oczekuje,** że identyfikator nazw zostanie zamapowany za pomocą **pliku user.mail,** **UPN**itp., więc musisz edytować mapowanie atrybutów, klikając ikonę **Edytuj** i zmienić mapowanie atrybutów.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > W tym miejscu zamapowaliśmy identyfikator nazwy z nazwą UPN (user.userprincipalname) jako domyślny. Musisz zamapować nazwę id z rzeczywistym identyfikatorem użytkownika na koncie Workday (adres e-mail, UPN itp.) w celu pomyślnej pracy logowania typu SSO.

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** znajdź **certyfikat (Base64)** i wybierz **pozycję Pobierz,** aby pobrać certyfikat i zapisać go na komputerze.

   ![Link do pobierania certyfikatu](common/certificatebase64.png)

1. Aby zmodyfikować opcje **podpisywania** zgodnie z wymaganiami, kliknij przycisk **Edytuj,** aby otworzyć okno dialogowe **Certyfikat podpisywania SAML.**

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Wybierz **pozycję Podpisz odpowiedź SAML i potwierdzenie** dla opcji **podpisywania**.

    b. Kliknij **przycisk Zapisz**

1. W sekcji **Konfigurowanie dnia roboczego** skopiuj odpowiednie adresy URL na podstawie wymagań.

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal o nazwie B.Simon.

1. Z lewego okienka w witrynie Azure portal wybierz pozycję **Azure Active Directory**, wybierz pozycję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.
1. Wybierz **pozycję Nowy użytkownik** u góry ekranu.
1. We właściwościach **Użytkownika** wykonaj następujące kroki:
   1. W polu **Nazwa** wprowadź wartość `B.Simon`.  
   1. W polu **Nazwa użytkownika** username@companydomain.extensionwprowadź pole . Na przykład `B.Simon@contoso.com`.
   1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.
   1. Kliknij przycisk **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji włączysz B.Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając dostęp do Workday.

1. W portalu Azure wybierz pozycję **Aplikacje dla przedsiębiorstw**, a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Na liście aplikacji wybierz pozycję **Dzień roboczy**.
1. Na stronie przegląd aplikacji znajdź sekcję **Zarządzaj** i wybierz pozycję **Użytkownicy i grupy**.

   ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

1. Wybierz **pozycję Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym Dodawanie **przydziału.**

    ![Łącze Dodaj użytkownika](common/add-assign-user.png)

1. W oknie dialogowym **Użytkownicy i grupy** wybierz pozycję **B.Simon** z listy Użytkownicy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, w oknie dialogowym **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy, a następnie kliknij przycisk **Wybierz** u dołu ekranu.
1. W oknie dialogowym **Dodawanie przydziału** kliknij przycisk **Przypisz.**

## <a name="configure-workday"></a>Konfigurowanie dnia roboczego

1. W innym oknie przeglądarki internetowej zaloguj się do witryny firmy Workday jako administrator.

2. W **polu Wyszukiwania** wyszukiwania z nazwą **Edytuj konfigurację dzierżawy — zabezpieczenia** w lewym górnym rogu strony głównej.

    ![Edytuj zabezpieczenia dzierżawy](./media/workday-tutorial/IC782925.png "Edytuj zabezpieczenia dzierżawy")

3. W sekcji **Adresy URL przekierowania** wykonaj następujące czynności:

    ![Adresy URL przekierowania](./media/workday-tutorial/IC7829581.png "Adresy URL przekierowania")

    a. Kliknij **pozycję Dodaj wiersz**.

    b. W **adresie URL przekierowania logowania**, **adres URL przekierowania limitu czasu** i adres URL **przekierowania mobilnego** wklej **adres URL logowania** skopiowany z sekcji **Konfigurowanie dnia roboczego** w witrynie Azure portal.

    d. W polu tekstowym **adresu URL przekierowania** wylogowania wklej **adres URL wylogowania** skopiowany z sekcji **Konfigurowanie dnia roboczego** w witrynie Azure portal.

    d. W obszarze Pola tekstowego **Używane dla środowisk** wybierz nazwę środowiska.  

   > [!NOTE]
   > Wartość atrybutu Środowisko jest powiązana z wartością adresu URL dzierżawy:  
   > -Jeśli nazwa domeny adresu URL dzierżawy workday zaczyna się od impl na przykład: * https://www.myworkday.com/"tenant"/login-saml2.htmld),* atrybut **Środowisko** musi być ustawiony na Implementacja.  
   > -Jeśli nazwa domeny zaczyna się od czegoś innego, należy skontaktować się z [zespołem pomocy technicznej klienta Workday,](https://www.workday.com/en-us/partners-services/services/support.html) aby uzyskać pasujące wartości **środowiska.**

4. W sekcji **Ustawienia SAML** wykonaj następujące czynności:

    ![Instalator SAML](./media/workday-tutorial/IC782926.png "Instalator SAML")

    a.  Wybierz **pozycję Włącz uwierzytelnianie SAML**.

    b.  Kliknij **pozycję Dodaj wiersz**.

5. W sekcji **Dostawcy tożsamości SAML** wykonaj następujące kroki:

    ![Dostawcy tożsamości języka SAML](./media/workday-tutorial/IC7829271.png "Dostawcy tożsamości języka SAML")

    a. W polach **tekstowych Nazwa dostawcy tożsamości** wpisz nazwę dostawcy (na przykład: *SPInitiatedSSO*).

    b. W witrynie Azure portal w sekcji **Konfigurowanie dnia roboczego** skopiuj wartość **identyfikatora usługi Azure AD,** a następnie wklej ją do pola tekstowego **wystawcy.**

    ![Dostawcy tożsamości języka SAML](./media/workday-tutorial/IC7829272.png "Dostawcy tożsamości języka SAML")

    d. W witrynie Azure portal w sekcji **Konfigurowanie dnia roboczego** skopiuj wartość **adresu URL wylogowania,** a następnie wklej ją do pola tekstowego **URL odpowiedzi wylogowania.**

    d. W witrynie Azure portal w sekcji **Konfigurowanie dnia roboczego** skopiuj wartość **adresu URL logowania,** a następnie wklej ją do pola tekstowego **adresu URL usługi logowania.**

    e. W obszarze Pola tekstowego **Używane dla środowisk** wybierz nazwę środowiska.

    f. Kliknij **pozycję Certyfikat klucza publicznego dostawcy tożsamości,** a następnie kliknij przycisk **Utwórz**.

    ![Utwórz](./media/workday-tutorial/IC782928.png "Przycisk Utwórz")

    g. Kliknij **pozycję Utwórz klucz publiczny x509**.

    ![Utwórz](./media/workday-tutorial/IC782929.png "Przycisk Utwórz")

6. W sekcji **Widok klucza publicznego x509** wykonaj następujące czynności:

    ![Wyświetl klucz publiczny x509](./media/workday-tutorial/IC782930.png "Wyświetl klucz publiczny x509")

    a. W polach tekstowych **Nazwa** wpisz nazwę certyfikatu (na przykład: *PPE\_SP*).

    b. W polu **tekstowym Ważne od** wpisz prawidłową wartość atrybutu certyfikatu.

    d.  W polu tekstowym **Ważne do** wpisz prawidłową wartość atrybutu certyfikatu.

    > [!NOTE]
    > Możesz uzyskać prawidłową datę od daty i prawidłową datę z pobranego certyfikatu, klikając go dwukrotnie.  Daty są wyświetlane na karcie **Szczegóły.**
    >
    >

    d.  Otwórz certyfikat zakodowany base-64 w notatniku, a następnie skopiuj jego zawartość.

    e.  W polach tekstowych **Certyfikat** wklej zawartość schowka.

    f.  Kliknij przycisk **OK**.

7. Wykonaj poniższe czynności:

    ![Konfiguracja SSO](./media/workday-tutorial/WorkdaySSOConfiguratio.png "Konfiguracja SSO")

    a.  W pole tekstowym **Identyfikator usługodawcy** wpisz . **http://www.workday.com**

    b. Wybierz **opcję Nie opróżniaj żądania uwierzytelniania inicjowanego przez sp.**

    d. Jako **metoda podpisu żądania uwierzytelniania**wybierz **SHA256**.

    ![Metoda podpisu żądania uwierzytelniania](./media/workday-tutorial/WorkdaySSOConfiguration.png "Metoda podpisu żądania uwierzytelniania")

    d. Kliknij przycisk **OK**.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Upewnij się, że logujesz się poprawnie. W przypadku włączenia logowania jednokrotnego z niepoprawną konfiguracją, może nie być w stanie wprowadzić aplikacji z poświadczeniami i zablokować. W tej sytuacji Workday udostępnia adres URL logowania do kopii zapasowej, w którym użytkownicy mogą logować się przy użyciu normalnej nazwy użytkownika i hasła w następującym formacie:[Your Workday URL]/login.flex?redirect=n

### <a name="create-workday-test-user"></a>Utwórz użytkownika testowego workday

W tej sekcji utworzysz użytkownika o nazwie B.Simon w Workday. Praca z [zespołem pomocy technicznej klienta workday,](https://www.workday.com/partners-services/services/support.html) aby dodać użytkowników na platformie Workday. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

## <a name="test-sso"></a>Test SSO

Po wybraniu kafelka Dzień roboczy w Panelu dostępu należy automatycznie zalogować się do dnia roboczego, dla którego skonfigurowano logującą się logującą logującą. Aby uzyskać więcej informacji na temat Panelu dostępu, zobacz [Wprowadzenie do Panelu dostępu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Wypróbuj dzień roboczy z usługą Azure AD](https://aad.portal.azure.com)

- [Co to jest kontrola sesji w usłudze Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Jak chronić Dzień Pracy dzięki zaawansowanej widoczności i kontrolom](https://docs.microsoft.com/cloud-app-security/protect-workday)