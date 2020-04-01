---
title: 'Samouczek: Integracja usługi Azure Active Directory z TurboRater | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a turborater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088271"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Samouczek: Integracja usługi Azure Active Directory z turborater

W tym samouczku dowiesz się, jak zintegrować TurboRater z usługą Azure Active Directory (Azure AD).

Integracja TurboRater z usługą Azure AD zapewnia następujące korzyści:

* Można kontrolować w usłudze Azure AD, który ma dostęp do TurboRater.
* Możesz włączyć użytkowników, aby automatycznie zalogować się do TurboRater (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji z usługą Azure AD (SaaS) z aplikacją, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z TurboRater, potrzebujesz następujących elementów:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja TurboRater z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku skonfigurujesz i przetestujesz logowanie jednokrotne usługi Azure AD w środowisku testowym.

TurboRater obsługuje zainicjowane przez protokół IDP logowanie jednokrotne (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>Dodaj TurboRater z portalu Azure Marketplace

Aby skonfigurować integrację TurboRater z usługą Azure AD, musisz dodać TurboRater z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Opcja Aplikacji dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** w górnej części okienka.

    ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wpisz **TurboRater**. W wynikach wyszukiwania wybierz **TurboRater**, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![TurboRater na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z TurboRater na podstawie użytkownika testowego o nazwie **B Simon**. Aby logowanie jednokrotne działało, należy ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w TurboRater.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą TurboRater, musisz wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj logowanie jednokrotne TurboRater,](#configure-turborater-single-sign-on)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą B. Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby włączyć B. Simon do korzystania z usługi Azure AD logowania jednokrotnego.
1. **[Utwórz użytkownika testowego TurboRater,](#create-a-turborater-test-user)** aby istniał użytkownik o nazwie B. Simon w TurboRater, który jest połączony z użytkownikiem usługi Azure AD o nazwie B. Simon.
1. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednokrotne usługi Azure AD za pomocą turborater, należy wykonać następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **TurboRater** wybierz opcję **Logowanie jednokrotne.**

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. Na stronie **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML** wykonaj następujące czynności:

    ![Informacje o domenie TurboRater i adresach URL z logami jednokrotnymi](common/idp-intiated.png)

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL:

       `https://www.itcdataservices.com`

    1. W polu **Adres URL odpowiedzi (adres URL usługi konsumenta potwierdzenia)** wprowadź adres URL, używając następującego wzorca:

       | Środowisko | Adres URL |
       | ---------------| --------------- |
       | Testowanie  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Na żywo  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > To nie są rzeczywiste wartości. Należy je zastąpić rzeczywistymi wartościami identyfikatora i adresu URL odpowiedzi. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej TurboRater.](https://www.getitc.com/support) Można również odwołać się do wzorców wyświetlanych w podstawowym okienku **konfiguracji SAML** w witrynie Azure portal.

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz,** aby pobrać **kod XML metadanych federacji** z podanych opcji i zapisać go na komputerze.

    ![Opcja pobierania kodu XML metadanych federacji](common/metadataxml.png)

1. W sekcji **Konfigurowanie TurboRater** skopiuj adres URL lub adresy URL, których potrzebujesz:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Konfigurowanie logowania jednokrotnego TurboRater

Aby skonfigurować logowanie jednokrotne po stronie TurboRater, musisz wysłać pobrany kod XML metadanych federacji i odpowiednie skopiowane adresy URL z portalu Azure do [zespołu pomocy technicznej TurboRater.](https://www.getitc.com/support) Zespół TurboRater upewni się, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory**   > **Wszyscy użytkownicy**.

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

W tej sekcji można włączyć B. Simon do korzystania z logowania jednokrotnego platformy Azure, przyznając im dostęp do TurboRater.

1. W portalu Azure wybierz **aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje** > **TurboRater**.

    ![Okienko aplikacji dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz **TurboRater**.

    ![TurboRater na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **MANAGE**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **pozycję + Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku Dodawanie **przydziału.**

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **B. Simon** na liście **Użytkownicy,** a następnie wybierz pozycję **Wybierz** u dołu okienka.

1. Jeśli oczekujesz wartości roli w asercji SAML, a następnie w **okienku Wybierz rolę,** wybierz odpowiednią rolę dla użytkownika z listy. U dołu okienka wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-a-turborater-test-user"></a>Tworzenie użytkownika testowego TurboRater

W tej sekcji utworzysz użytkownika o nazwie B. Simon w TurboRater. Współpracuj z [zespołem wsparcia TurboRater,](https://www.getitc.com/support) aby dodać B. Simona jako użytkownika w TurboRater. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po **wybraniu TurboRater** w portalu Moje aplikacje, powinieneś być automatycznie zalogowany do subskrypcji TurboRater, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
