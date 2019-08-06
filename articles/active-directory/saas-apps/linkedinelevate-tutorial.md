---
title: 'Samouczek: Azure Active Directory integrację z usługą LinkedIn podniesienia uprawnień | Microsoft Docs'
description: Dowiedz się, jak skonfigurować Logowanie jednokrotne między Azure Active Directory i podniesienia uprawnień do serwisu LinkedIn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b7cb8d6ab34a632e36ea2fd1c87005a038bc523
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/06/2019
ms.locfileid: "68823718"
---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-elevate"></a>Samouczek: Azure Active Directory integrację z usługą LinkedIn

W tym samouczku dowiesz się, jak zintegrować usługę LinkedIn z usługą Azure Active Directory (Azure AD).
Integracja serwisu LinkedIn z usługą Azure AD zapewnia następujące korzyści:

* Możesz kontrolować usługę Azure AD, która ma dostęp do podniesienia uprawnień do serwisu LinkedIn.
* Możesz umożliwić użytkownikom automatyczne logowanie do usługi LinkedIn podwyższanie poziomu (Logowanie jednokrotne) przy użyciu swoich kont w usłudze Azure AD.
* Możesz zarządzać konta w jednej centralnej lokalizacji — witryny Azure portal.

Jeśli chcesz dowiedzieć się więcej na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne z usługą Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację z usługą Azure AD przy użyciu podniesienia uprawnień serwisu LinkedIn, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/)
* Podnieś poziom subskrypcji włączone Logowanie jednokrotne w serwisie LinkedIn

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

* Podniesienie poziomu serwisu LinkedIn obsługuje usługę **SP i dostawcy tożsamości** zainicjowane przez usługę SSO

* Podnoszenie uprawnień serwisu LinkedIn obsługuje funkcję aprowizacji użytkowników **just in Time**

* Podnoszenie uprawnień serwisu LinkedIn obsługuje [ **Automatyczne** Inicjowanie obsługi użytkowników](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>Dodawanie podniesienia uprawnień do serwisu LinkedIn z galerii

Aby skonfigurować integrację serwisu LinkedIn z usługą Azure AD, należy dodać podnoszenie uprawnień serwisu LinkedIn z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać podnoszenie uprawnień do serwisu LinkedIn z galerii, wykonaj następujące czynności:**

1. W **[witryny Azure portal](https://portal.azure.com)** , w panelu nawigacyjnym po lewej stronie kliknij pozycję **usługi Azure Active Directory** ikony.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do grupy **Aplikacje dla przedsiębiorstw** i wybierz opcję **Wszystkie aplikacje**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Aby dodać nową aplikację, kliknij **nową aplikację** przycisk u góry okna dialogowego.

    ![Nowy przycisk aplikacji](common/add-new-app.png)

4. W polu wyszukiwania wpisz podnoszenie **uprawnień serwisu LinkedIn**, wybierz pozycję **LinkedIn Podnieś poziom** z panelu wyników, a następnie kliknij przycisk **Dodaj** , aby dodać aplikację.

    ![Podnoszenie uprawnień do serwisu LinkedIn na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfiguracja i testowanie usługi Azure AD logowania jednokrotnego

W tej sekcji skonfigurujesz i testujesz Logowanie jednokrotne usługi Azure AD za pomocą podniesienia uprawnień w serwisie LinkedIn na podstawie użytkownika testowego o nazwie **Britta Simon**.
Aby logowanie jednokrotne działało, należy ustanowić relację linku między użytkownikiem usługi Azure AD a pokrewnym użytkownikiem w serwisie LinkedIn.

Aby skonfigurować i przetestować Logowanie jednokrotne usługi Azure AD za pomocą podniesienia uprawnień serwisu LinkedIn, należy wykonać następujące bloki konstrukcyjne:

1. **[Konfigurowanie usługi Azure AD logowania jednokrotnego](#configure-azure-ad-single-sign-on)**  — aby umożliwić użytkownikom korzystać z tej funkcji.
2. **[Konfigurowanie](#configure-linkedin-elevate-single-sign-on)** ustawienia Logowanie jednokrotne w serwisie LinkedIn — w celu skonfigurowania ustawień logowania jednokrotnego na stronie aplikacji.
3. **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)**  — do przetestowania usługi Azure AD logowanie jednokrotne za pomocą Britta Simon.
4. **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)**  — Aby włączyć Britta Simon korzystać z usługi Azure AD logowania jednokrotnego.
5. **[Tworzenie podniesienia uprawnień użytkownika testowego w serwisie LinkedIn](#create-linkedin-elevate-test-user)** , aby uzyskać odpowiednik usługi Britta Simon w usłudze LinkedIn, która jest powiązana z reprezentacją użytkownika w usłudze Azure AD.
6. **[Testowanie logowania jednokrotnego](#test-single-sign-on)**  — Aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować funkcję rejestracji jednokrotnej usługi Azure AD przy użyciu podniesienia uprawnień serwisu LinkedIn, wykonaj następujące czynności:

1. W [Azure Portal](https://portal.azure.com/)na stronie **serwis LinkedIn podnoszenie poziomu** integracji aplikacji wybierz pozycję **Logowanie**jednokrotne.

    ![Skonfigurować łącze rejestracji jednokrotnej](common/select-sso.png)

2. W oknie dialogowym **Wybieranie metody logowania jednokrotnego** wybierz tryb **SAML/WS-Fed**, aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** kliknij ikonę **Edytuj**, aby otworzyć okno dialogowe **Podstawowa konfiguracja protokołu SAML**.

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

4. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez **dostawcę tożsamości**, w sekcji **Podstawowa konfiguracja protokołu SAML** wykonaj następujące kroki:

    ![Serwis LinkedIn Podnieś poziom rejestracji jednokrotnej domeny i adresów URL](common/idp-intiated.png)

    a. W polu tekstowym **Identyfikator** wprowadź wartość **identyfikatora jednostki** . wartość identyfikatora jednostki zostanie skopiowana z portalu LinkedIn w dalszej części tego samouczka.

    b. W polu tekstowym **adres URL odpowiedzi** wprowadź wartość **adresu URL dostępu konsumenta potwierdzenia (ACS** ), a w dalszej części tego samouczka skopiuj wartość adresu URL dostępu do odbiorcy usługi ACS z portalu LinkedIn.

5. Kliknij przycisk **Ustaw dodatkowe adresy URL** i wykonaj następujący krok, jeśli chcesz skonfigurować aplikację w trybie inicjowania przez **dostawcę usług**:

    ![Serwis LinkedIn Podnieś poziom rejestracji jednokrotnej domeny i adresów URL](common/metadata-upload-additional-signon.png)

    W polu tekstowym **Adres URL logowania** wpisz adres URL, korzystając z następującego wzorca: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

6. Podnoszenie poziomu aplikacji w serwisie LinkedIn oczekuje potwierdzeń SAML w określonym formacie, co wymaga dodania niestandardowych mapowań atrybutów do konfiguracji atrybutów tokenu SAML. Na poniższym zrzucie ekranu przedstawiono listę atrybutów domyślnych, gdzie atrybut **nameidentifier** jest mapowany na atrybut **user.userprincipalname**. Podnoszenie poziomu aplikacji na serwis LinkedIn oczekuje na zamapowanie NameIdentifier z **użytkownikiem. mail**, dlatego należy edytować Mapowanie atrybutów, klikając ikonę Edytuj i zmieniając mapowanie atrybutu.

    ![image](common/edit-attribute.png)

7. Oprócz powyższych, usługa LinkedIn Podnieś poziom aplikacji oczekuje kilku atrybutów do przekazania z powrotem do odpowiedzi SAML. W sekcji oświadczenia użytkownika w oknie dialogowym **atrybuty użytkownika** wykonaj następujące kroki, aby dodać ATRYBUT tokenu SAML, jak pokazano w poniższej tabeli:

    | Name (Nazwa) | Atrybut źródłowy|
    | -------| -------------|
    | Dział | user.department |

    a. Kliknij przycisk **Dodaj nowe oświadczenie**, aby otworzyć okno dialogowe **Zarządzanie oświadczeniami użytkownika**.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. W polu tekstowym **Nazwa** wpisz nazwę atrybutu pokazaną dla tego wiersza.

    d. Pozostaw pole **Przestrzeń nazw** puste.

    d. Dla opcji Źródło wybierz wartość **Atrybut**.

    e. Na liście **Atrybut źródłowy** wpisz wartość atrybutu pokazaną dla tego wiersza.

    f. Kliknij przycisk **OK**.

    g. Kliknij polecenie **Zapisz**.

8. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą protokołu SAML** w sekcji **Certyfikat podpisywania SAML** kliknij link **Pobierz**, aby pobrać **kod XML metadanych federacji** na podstawie podanych opcji zgodnie z wymaganiami i zapisać go na komputerze.

    ![Link pobierania certyfikatu](common/metadataxml.png)

9. W sekcji **Konfigurowanie podniesienia uprawnień serwisu LinkedIn** skopiuj odpowiednie adresy URL zgodnie z wymaganiami.

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    a. Adres URL logowania

    b. Identyfikator usługi Azure AD

    c. Adres URL wylogowywania

### <a name="configure-linkedin-elevate-single-sign-on"></a>Konfiguruj logowanie jednokrotne w serwisie LinkedIn

1. W innym oknie przeglądarki sieci Web logowanie do konta w serwisie LinkedIn Podnieś poziom dzierżawy jako administrator.

1. W **Centrum kont** kliknij przycisk **Ustawienia globalne** w obszarze **Ustawienia**. Ponadto wybierz z listy rozwijanej pozycję Podnieś poziom na podniesienie poziomu usługi **AAD** .

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Kliknij **lub kliknij tutaj, aby załadować i skopiować pojedyncze pola z formularza** , i wykonaj następujące czynności:

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Skopiuj **Identyfikator jednostki** i wklej go do pola tekstowego **Identyfikator** w **podstawowej konfiguracji SAML** w Azure Portal.

    b. Skopiuj **adres URL dostępu konsumenta (ACS) potwierdzenia** i wklej go w polu tekstowym **adres URL odpowiedzi** w **podstawowej konfiguracji SAML** w Azure Portal.

1. Przejdź do sekcji **LinkedIn Admin Settings** (Ustawienia administratora LinkedIn). Przekaż pobrany plik XML z Azure Portal, klikając opcję Przekaż plik XML.

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Kliknij przycisk **On** (Włącz), aby włączyć funkcję logowania jednokrotnego. Stan rejestracji jednokrotnej zmieni się z niepołączone na **połączone**

    ![Konfigurowanie logowania jednokrotnego](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji w witrynie Azure Portal utworzysz użytkownika testowego o nazwie Britta Simon.

1. W witrynie Azure Portal w okienku po lewej stronie wybierz pozycję **Azure Active Directory**, wybierz opcję **Użytkownicy**, a następnie wybierz pozycję **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz przycisk **Nowy użytkownik** w górnej części ekranu.

    ![Przycisk Nowy użytkownik](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki.

    ![Okno dialogowe Użytkownik](common/user-properties.png)

    a. W polu **Nazwa** wprowadź **BrittaSimon**.
  
    b. W polu **Nazwa użytkownika** wpisz `brittasimon@yourcompanydomain.extension`. Na przykład: BrittaSimon@contoso.com

    d. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu Hasło.

    d. Kliknij pozycję **Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji Britta Simon do korzystania z logowania jednokrotnego na platformie Azure, przyznając dostęp do podniesienia uprawnień do serwisu LinkedIn.

1. W Azure Portal wybierz pozycję **aplikacje dla przedsiębiorstw**, wybierz pozycję **wszystkie aplikacje**, a następnie wybierz pozycję Podnieś poziom do **serwisu LinkedIn**.

    ![Blok Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Na liście Aplikacje wybierz pozycję **Podnieś poziom serwisu LinkedIn**.

    ![Link podniesienia uprawnień do serwisu LinkedIn na liście aplikacji](common/all-applications.png)

3. W menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Kliknij przycisk **Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w oknie dialogowym **Dodawanie przypisania**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

5. W oknie dialogowym **Użytkownicy i grupy** wybierz użytkownika **Britta Simon** na liście użytkowników, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

6. Jeśli oczekujesz wartości roli w asercji SAML, w oknie dialogowym **Wybieranie roli** wybierz z listy odpowiednią rolę dla użytkownika, a następnie kliknij przycisk **Wybierz** u dołu ekranu.

7. W oknie dialogowym **Dodawanie przypisania** kliknij przycisk **Przypisz**.

### <a name="create-linkedin-elevate-test-user"></a>Utwórz użytkownika testowego podniesienia uprawnień w serwisie LinkedIn

Podwyższanie poziomu aplikacji przez serwis LinkedIn obsługuje funkcję aprowizacji użytkowników just in Time, a po automatycznym utworzeniu użytkowników uwierzytelniania w aplikacji. Na stronie Ustawienia administratora w portalu usługi LinkedIn Podnieś poziom uprawnień **automatycznie Przypisz licencje** do aktywnej obsługi przed chwilą i spowoduje to również przypisanie licencji do użytkownika. Podnoszenie uprawnień serwisu LinkedIn obsługuje również automatyczne Inicjowanie obsługi użytkowników. więcej informacji można znaleźć w [tym miejscu](linkedinelevate-provisioning-tutorial.md) w artykule Jak skonfigurować automatyczne Inicjowanie obsługi użytkowników.

   ![Tworzenie użytkownika testowego usługi Azure AD](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji służy do testowania konfiguracji usługi Azure AD pojedynczego logowania jednokrotnego przy użyciu panelu dostępu.

Po kliknięciu kafelka podnoszenie uprawnień w serwisie LinkedIn w panelu dostępu należy automatycznie zalogować się do usługi LinkedIn, dla której skonfigurowano Logowanie jednokrotne. Aby uzyskać więcej informacji na temat panelu dostępu, zobacz [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Wprowadzenie do panelu dostępu).

## <a name="additional-resources"></a>Dodatkowe zasoby

- [Lista samouczków dotyczących sposobu integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Czym jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Co to jest dostęp warunkowy w Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)