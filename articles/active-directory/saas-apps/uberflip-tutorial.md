---
title: 'Samouczek: Integracja usługi Azure Active Directory z uberflip | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048472"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Samouczek: Integracja usługi Azure Active Directory z uberflipem

W tym samouczku dowiesz się, jak zintegrować Uberflip z usługą Azure Active Directory (Azure AD).

Integracja uberflip z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD można kontrolować, kto ma dostęp do uberflip.
* Możesz włączyć automatyczne logowanie użytkowników do uberflip (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji z usługą Azure AD (SaaS) z aplikacją, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z uberflip, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja Uberflip z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

Uberflip obsługuje następujące funkcje:

* Rejestracji jednokrotnej inicjowane przez sp iDP.
* Just-in-time inicjowania obsługi administracyjnej użytkowników.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Dodawanie uberflip z portalu Azure Marketplace

Aby skonfigurować integrację uberflip z usługą Azure AD, musisz dodać Uberflip z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

   ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

   ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** w górnej części okienka.

   ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wpisz **Uberflip**. W wynikach wyszukiwania wybierz **uberflip**, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

   ![Uberflip na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Uberflip na podstawie użytkownika testowego o nazwie **B Simon**. Aby logowanie jednokrotne działało, musisz ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w uberflip.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą uberflip, musisz wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj rejestrację jednokrotną uberflip,](#configure-uberflip-single-sign-on)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Utwórz użytkownika testowego Uberflip,](#create-an-uberflip-test-user)** aby w uberflipie był użytkownik o nazwie B. Simon, który jest połączony z użytkownikiem usługi Azure AD o nazwie B. Simon.
1. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą uberflip, należy wykonać następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **Uberflip** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

   ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML** wykonaj jedną z następujących czynności, w zależności od tego, który tryb jednośladu chcesz skonfigurować:

   * Aby skonfigurować aplikację w trybie SSO inicjowanym przez protokołu IDP, w polu **Adres URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** wprowadź adres URL przy użyciu następującego wzorca:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informacje o domenie Uberflip i adresach URL z logami jednokrotnymi](common/both-replyurl.png)

     > [!NOTE]
     > Ta wartość nie jest prawdziwa. Zaktualizuj tę wartość za pomocą rzeczywistego adresu URL odpowiedzi. Aby uzyskać rzeczywistą wartość, skontaktuj się z [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com). Można również odwołać się do wzorców wyświetlanych w podstawowym okienku **konfiguracji SAML** w witrynie Azure portal.

   * Aby skonfigurować aplikację w trybie logowania typu SP, wybierz pozycję **Ustaw dodatkowe adresy URL,** a w polu **Pozycję Adresu URL logowania** wprowadź ten adres URL:

     `https://app.uberflip.com/users/login`

     ![Informacje o domenie Uberflip i adresach URL z logami jednokrotnymi](common/both-signonurl.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz,** aby pobrać **kod XML metadanych federacji** z podanych opcji i zapisać go na komputerze.

   ![Opcja pobierania kodu XML metadanych federacji](common/metadataxml.png)

1. W okienku **Konfigurowanie uberflip** skopiuj potrzebny adres URL lub adresy URL:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

   ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Konfigurowanie rejestracji jednokrotnej uberflip

Aby skonfigurować logowanie jednokrotne po stronie Uberflip, musisz wysłać pobrany kod XML metadanych federacji i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej Uberflip](mailto:support@uberflip.com). Zespół Uberflip upewni się, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie B. Simon w witrynie Azure portal.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i "Wszyscy użytkownicy"](common/users.png)

1. U góry ekranu wybierz pozycję **+ Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    ![Okienko Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wprowadź **bsimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **BSimon\@\<yourcompanydomain\<>.>rozszerzenia **. Na przykład **BSimon\@contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć B. Simon do korzystania z usługi Azure logowania jednokrotnego, przyznając im dostęp do Uberflip.

1. W witrynie Azure portal wybierz pozycję **Aplikacje** > dla przedsiębiorstw**Wszystkie aplikacje** > **Uberflip**.

    ![Okienko aplikacji dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **uberflip**.

    ![Uberflip na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **MANAGE**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **pozycję + Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku Dodawanie **przydziału.**

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **B Simon** na liście **Użytkownicy,** a następnie wybierz pozycję **Wybierz** u dołu okienka.

1. Jeśli oczekujesz wartości roli w asercji SAML, a następnie w **okienku Wybierz rolę,** wybierz odpowiednią rolę dla użytkownika z listy. U dołu okienka wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-an-uberflip-test-user"></a>Tworzenie użytkownika testowego Uberflip

Użytkownik o imieniu B. Simon jest teraz tworzony w Uberflip. Nie musisz nic robić, aby utworzyć tego użytkownika. Uberflip obsługuje aprowizacji użytkowników just-in-time, która jest domyślnie włączona. Jeśli użytkownik o imieniu B. Simon jeszcze nie istnieje w Uberflip, nowy jest tworzony po uwierzytelnieniu.

> [!NOTE]
> Jeśli chcesz ręcznie utworzyć użytkownika, skontaktuj się z [zespołem pomocy technicznej Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po wybraniu **uberflip** w portalu Moje aplikacje powinieneś automatycznie zalogować się do subskrypcji Uberflip, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
