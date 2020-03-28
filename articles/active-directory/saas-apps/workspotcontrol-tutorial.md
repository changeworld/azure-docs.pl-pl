---
title: 'Samouczek: Integracja usługi Azure Active Directory z formantem workspot | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne dla usługi Azure Active Directory i kontroli w witrynie Workspot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67086680"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Samouczek: Integracja usługi Azure Active Directory z formantem workspot

W tym samouczku dowiesz się, jak zintegrować formant workspot z usługą Azure Active Directory (Azure AD). Po zintegrowaniu formantu workspot z usługą Azure AD można:

* Użyj usługi Azure AD, aby kontrolować, kto ma dostęp do kontroli workspot.
* Umożliwia użytkownikom automatyczne logowanie się do usługi Workspot Control (logowanie jednokrotne [Logowanie jednokrotne]) przy użyciu ich kont usługi Azure AD.
* Zarządzaj kontami w jednej centralnej lokalizacji: w witrynie Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji SaaS z usługą Azure AD, zobacz [Logowanie jednokrotne do aplikacji w usłudze Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z formantem workspot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz środowiska usługi Azure AD, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

* Subskrypcja z obsługą logowania jednokrotnego w ucho.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

> [!Note]
> Workspot Control obsługuje jednostkę SSO inicjującą protokół SP i protokół IDP.


## <a name="adding-workspot-control-from-the-gallery"></a>Dodawanie formantu workspot z galerii

Aby skonfigurować integrację formantu programu Workspot z usługą Azure AD, należy dodać formant programu Workspot z galerii do listy zarządzanych aplikacji SaaS.

**Aby dodać formant workspot z galerii, wykonaj następujące kroki:**

1. W lewym okienku [witryny Azure portal](https://portal.azure.com)wybierz pozycję **Azure Active Directory**.

    ![Przycisk Azure Active Directory](common/select-azuread.png)

2. Przejdź do **aplikacji dla przedsiębiorstw** i wybierz pozycję Wszystkie **aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

3. Wybierz **pozycję Nowa aplikacja** w górnej części okna.

    ![Przycisk Nowa aplikacja](common/add-new-app.png)

4. W polu wyszukiwania wprowadź pozycję **Formant miejsca pracy**, wybierz **pozycję Formant punktu robót** budowlanych z panelu wyników, a następnie wybierz pozycję **Dodaj**.

     ![Okno "Dodaj z galerii"](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego za pomocą kontroli Workspot dla użytkownika testowego, Britta Simon.
Aby logowanie jednokrotne działało, należy ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w usłudze Workspot Control.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą formantu workspot, należy wykonać następujące zadania:

1. [Skonfiguruj logowanie jednokrotne w usłudze Azure AD](#configure-azure-ad-single-sign-on), aby umożliwić użytkownikom korzystanie z tej funkcji.
2. [Skonfiguruj logowanie jednokrotne w uchodze w](#configure-workspot-control-single-sign-on) uchodze ii w celu skonfigurowania ustawień logowania jednokrotnego po stronie aplikacji.
3. [Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user) aby przetestować logowanie jednokrotne usługi Azure AD dla Britta Simon.
4. [Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user) — aby umożliwić użytkownikowi Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
5. [Utwórz użytkownika testowego workspot kontroli,](#create-a-workspot-control-test-user) aby ustanowić odpowiednik Britta Simon w Workspot Control, który jest połączony z reprezentacji usługi Azure AD użytkownika.
6. [Przetestuj logowanie jednokrotne](#test-single-sign-on), aby sprawdzić działanie konfiguracji.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą formantu workspot, wykonaj następujące kroki:

1. Na stronie integracji aplikacji **kontroli witryny w** witrynie [Azure portal](https://portal.azure.com/)wybierz pozycję **Logowanie jednokrotne**.

    ![Link do konfigurowania logowania jednokrotnego](common/select-sso.png)

2. W oknie **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML,** aby włączyć logowanie jednokrotne.

    ![Wybieranie okna metody wyboru pojedynczego logowania](common/select-saml-option.png)

3. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz ikonę **Edytuj** (ołówek), aby uzyskać dostęp do **podstawowej konfiguracji SAML**.

    ![Ikona edycji wyróżniona w "Podstawowej konfiguracji SAML"](common/edit-urls.png)

4. Jeśli aplikacja ma zostać skonfigurowana w trybie inicjowanym przez IDP, w sekcji **Podstawowa konfiguracja SAML,** wykonaj następujące czynności:

    ![Informacje o domenie i adresach URL kontroli w programie Workspot](common/idp-intiated.png)

    1. W polu tekstowym **identyfikatora** wprowadź adres URL w następującym wzorzec:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/metadata***

    1. W polu tekstowym **adresu URL odpowiedzi** wprowadź adres URL w następującym wzorze:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/saml/assertion***

5. Jeśli chcesz skonfigurować aplikację w trybie inicjowanym przez sp, wybierz pozycję **Ustaw dodatkowe adresy URL**.

    ![Informacje o domenie i adresach URL kontroli w programie Workspot](common/metadata-upload-additional-signon.png)

    W polu **tekstowym Zaloguj adres URL** wprowadź adres URL w następującym wzorze:<br/>
    ***https://<<i> </i>INSTANCENAME>-saml.workspot.com/***

    > [!NOTE]
    > Te wartości nie są prawdziwe. Zastąp te wartości rzeczywistym identyfikatorem, adresem URL odpowiedzi i adresem URL logowania. Skontaktuj się z [zespołem pomocy technicznej klienta kontroli workspot,](mailto:support@workspot.com) aby uzyskać te wartości. Lub można również odwołać się do wzorców w sekcji **Podstawowa konfiguracja SAML** w witrynie Azure portal.

6. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Saml Podpisywanie certyfikatów** wybierz pozycję **Pobierz,** aby pobrać **certyfikat (Base64)** z dostępnych opcji zgodnie z wymaganiami. Zapisz go na komputerze.

    ![Link do pobrania certyfikatu (Base64)](common/certificatebase64.png)

7. W sekcji **Konfigurowanie formantu punktu programu Workspot** skopiuj odpowiednie adresy URL zgodnie z wymaganiami:

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

    - **Adres URL logowania**

    - **Identyfikator usługi Azure AD**

    - **Adres URL wylogowywania**

### <a name="configure-workspot-control-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w ucho.

1. W innym oknie przeglądarki internetowej zaloguj się do usługi Workspot Control jako administrator zabezpieczeń.

2. Na pasku narzędzi u góry strony wybierz pozycję **Ustawienia,** a następnie **pozycję SAML**.

    ![Opcje konfiguracji](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. W oknie **Konfiguracja języka znaczników oświadczeń zabezpieczeń** wykonaj następujące kroki:
 
    ![Okno Konfiguracji języka znaczników asercji zabezpieczeń](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. W polu **Identyfikator jednostki** wklej **identyfikator reklamy platformy Azure** skopiowany z witryny Azure portal.

    1. W polu **Adres URL usługi Signon** wklej **adres URL logowania** skopiowany z witryny Azure portal.

    1. W polu **Adres URL usługi wylogowania** wklej **adres URL wylogowania** skopiowany z witryny Azure portal.

    1. Wybierz **opcję Aktualizuj plik,** aby przekazać do certyfikatu X.509 certyfikat zakodowany base-64, który został pobrany z witryny Azure portal.

    1. Wybierz **pozycję Zapisz**.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego w witrynie Azure portal.

1. W lewym okienku witryny Azure Portal wybierz pozycję **Azure Active Directory**, **Users**, a następnie **Wszyscy użytkownicy**.

    ![Linki „Użytkownicy i grupy” i „Wszyscy użytkownicy”](common/users.png)

2. Wybierz **pozycję Nowy użytkownik** w górnej części okna.

    ![Przycisk "Nowy użytkownik"](common/new-user.png)

3. We właściwościach użytkownika wykonaj następujące kroki:

    ![Okno Właściwości użytkownika](common/user-properties.png)

    1. W polu **Nazwa** wprowadź pole **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wpisz **brittasimon@* swojąfirmę.extension***. Na przykład wprowadź ** BrittaSimon@contoso.<i> </i> com**.

    1. Zaznacz pole wyboru **Pokaż hasło.** Następnie zapisz wartość wyświetlaną w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji udzielisz Firmie Britta Simon dostępu do formantu workspot, aby umożliwić jej korzystanie z logowania jednokrotnego platformy Azure.

1. W portalu Azure wybierz pozycję **Aplikacje przedsiębiorstwa**, **Wszystkie aplikacje**, a następnie **formant workspot**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

2. Z listy aplikacji wybierz pozycję **Sterowanie w miejscu robót budowlanych**.

    ![Łącze Formantu punktu workspot na liście Aplikacje](common/all-applications.png)

3. Z menu po lewej stronie wybierz pozycję **Użytkownicy i grupy**.

    ![Link „Użytkownicy i grupy”](common/users-groups-blade.png)

4. Wybierz przycisk **Dodaj użytkownika.** Następnie wybierz **pozycję Użytkownicy i grupy** w oknie Dodawanie **przydziału.**

    ![Okno "Dodaj przydział"](common/add-assign-user.png)

5. W oknie **Użytkownicy i grupy** wybierz **pozycję Britta Simon** z listy **Użytkownicy.** Następnie kliknij pozycję **Wybierz**.

6. Jeśli oczekujesz dowolnej wartości roli w asercji SAML, wybierz odpowiednią rolę dla użytkownika z listy w oknie **Wybierz rolę.** Następnie kliknij przycisk **Wybierz** u dołu.

7. W oknie **Dodaj przypisanie** wybierz pozycję **Przypisz**.

### <a name="create-a-workspot-control-test-user"></a>Tworzenie użytkownika testowego workspot control

Aby umożliwić użytkownikom usługi Azure AD zalogowanie się do kontroli workspot, muszą one być aprowizowane do Workspot Control. Inicjowanie obsługi administracyjnej jest zadaniem ręcznym.

**Aby aprowizować konto użytkownika, wykonaj następujące kroki:**

1. Zaloguj się do formantu workspot jako administrator zabezpieczeń.

2. Na pasku narzędzi u góry strony wybierz pozycję **Użytkownicy,** a następnie **pozycję Dodaj użytkownika**.

    ![Opcje "Użytkownicy"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. W oknie **Dodaj nowego użytkownika** wykonaj następujące czynności:

    ![Okno "Dodaj nowego użytkownika"](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. W polu **Imię** wprowadź imię użytkownika, na przykład **Britta**.

    1. W polu **tekstowym Nazwisko** wprowadź nazwisko użytkownika, na przykład **Simon**.

    1. W polu **Poczta e-mail** wprowadź adres e-mail użytkownika, na przykład ** Brittasimon@contoso<i> </i> . com**.

    1. Wybierz odpowiednią rolę użytkownika z listy rozwijanej **Rola.**

    1. Wybierz odpowiednią grupę użytkowników z listy rozwijanej **Grupa.**

    1. Wybierz **pozycję Dodaj użytkownika**.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji testujemy naszą konfigurację logowania jednokrotnego usługi Azure AD za pośrednictwem *Panelu dostępu.*

Po kliknięciu kafelka **Formantu miejsca pracy** w Panelu dostępu należy automatycznie zalogować się do formantu Workspot, dla którego skonfigurowano logowanie jednośmiękowe. Aby uzyskać więcej informacji, zobacz [Wprowadzenie do panelu dostępu](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access).

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Samouczki dotyczące integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
