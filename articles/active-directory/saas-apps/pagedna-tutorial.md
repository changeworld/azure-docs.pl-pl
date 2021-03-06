---
title: 'Samouczek: Integracja usługi Azure Active Directory z Usługą PageDNA | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68227478"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Samouczek: Integracja usługi Azure Active Directory z usługą PageDNA

W tym samouczku dowiesz się, jak zintegrować PageDNA z usługą Azure Active Directory (Azure AD).

Integracja PageDNA z usługą Azure AD zapewnia następujące korzyści:

* W usłudze Azure AD możesz kontrolować, kto ma dostęp do PageDNA.
* Można włączyć użytkowników, aby automatycznie zalogować się do PageDNA (logowanie jednokrotne) za pomocą swoich kont usługi Azure AD.
* Konta można zarządzać w jednej centralnej lokalizacji: w witrynie Azure portal.

Aby uzyskać szczegółowe informacje na temat integracji aplikacji z usługą Azure AD (SaaS) z aplikacją, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne za pomocą usługi Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z PageDNA, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji platformy Azure, [utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja PageDNA z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku można skonfigurować i przetestować azure ad logowania jednokrotnego w środowisku testowym i zintegrować PageDNA z usługą Azure AD.

PageDNA obsługuje następujące funkcje:

* Logowanie jednokrotne inicjowane przez sp.

* Just-in-time inicjowania obsługi administracyjnej użytkowników.

## <a name="add-pagedna-from-the-azure-marketplace"></a>Dodawanie PageDNA z portalu Azure Marketplace

Aby skonfigurować integrację PageDNA z usługą Azure AD, musisz dodać PageDNA z portalu Azure Marketplace do listy zarządzanych aplikacji SaaS:

1. Zaloguj się do [Portalu Azure](https://portal.azure.com?azure-portal=true).
1. W lewym okienku wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Przejdź do pozycji **Aplikacje dla przedsiębiorstw** i wybierz pozycję **Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać nową aplikację, wybierz **+ Nowa aplikacja** w górnej części okienka.

    ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wpisz **PageDNA**. W wynikach wyszukiwania wybierz pozycję **PageDNA**, a następnie wybierz pozycję **Dodaj,** aby dodać aplikację.

    ![PageDNA na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z PageDNA na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić łącze między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w PageDNA.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Usługi PageDNA, należy wykonać następujące bloki konstrukcyjne:

1. **[Skonfiguruj logowanie jednookrotne usługi Azure AD,](#configure-azure-ad-single-sign-on)** aby umożliwić użytkownikom korzystanie z tej funkcji.
1. **[Skonfiguruj logowanie jednookrotne PageDNA,](#configure-pagedna-single-sign-on)** aby skonfigurować ustawienia logowania jednokrotnego po stronie aplikacji.
1. **[Utwórz użytkownika testowego usługi Azure AD,](#create-an-azure-ad-test-user)** aby przetestować logowanie jednokrotne usługi Azure AD za pomocą Britta Simon.
1. **[Przypisz użytkownika testowego usługi Azure AD,](#assign-the-azure-ad-test-user)** aby umożliwić Britta Simon korzystanie z logowania jednokrotnego usługi Azure AD.
1. **[Utwórz użytkownika testowego PageDNA,](#create-a-pagedna-test-user)** aby w PageDNA był użytkownik o nazwie Britta Simon, który jest połączony z użytkownikiem usługi Azure AD o nazwie Britta Simon.
1. **[Przetestuj logowanie jednokrotne,](#test-single-sign-on)** aby sprawdzić, czy konfiguracja działa.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji włączysz logowanie jednokrotne usługi Azure AD w witrynie Azure Portal.

Aby skonfigurować logowanie jednookrotne usługi Azure AD za pomocą usługi PageDNA, należy wykonać następujące kroki:

1. W [witrynie Azure portal](https://portal.azure.com/)na stronie integracji aplikacji **PageDNA** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML** należy wykonać następujące czynności:

    ![Informacje o domenie PageDNA i adresach URL logowania jednokrotnego](common/sp-identifier.png)

    1. W polu **Podpisz adres URL** wprowadź adres URL, używając jednego z następujących wzorców:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. W polu **Identyfikator (identyfikator jednostki)** wprowadź adres URL, używając jednego z następujących wzorców:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > To nie są rzeczywiste wartości. Zaktualizuj te wartości, używając rzeczywistego identyfikatora i adresu URL logowania. Aby uzyskać te wartości, skontaktuj się z [zespołem pomocy technicznej PageDNA](mailto:success@pagedna.com). Można również odwołać się do wzorców wyświetlanych w podstawowym okienku **konfiguracji SAML** w witrynie Azure portal.

1. W okienku **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz,** aby pobrać **certyfikat (raw)** z podanych opcji i zapisać go na komputerze.

    ![Opcja pobierania certyfikatu (raw)](common/certificateraw.png)

1. W sekcji **Konfigurowanie PageDNA** skopiuj potrzebny adres URL lub adresy URL:

   * **Adres URL logowania**
   * **Identyfikator usługi Azure AD**
   * **Adres URL wylogowywania**

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>Konfigurowanie logowania jednokrotnego PageDNA

Aby skonfigurować logowanie jednokrotne po stronie PageDNA, wyślij pobrany certyfikat (raw) i odpowiednie skopiowane adresy URL z witryny Azure portal do [zespołu pomocy technicznej PageDNA](mailto:success@pagedna.com). Zespół PageDNA upewni się, że połączenie SSO SAML jest prawidłowo ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure portal w lewym okienku wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory**   > **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i "Wszyscy użytkownicy"](common/users.png)

1. U góry ekranu wybierz pozycję **+ Nowy użytkownik**.

    ![Nowa opcja użytkownika](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    ![Okienko Użytkownik](common/user-properties.png)

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **>\@\<BrittaSimon yourcompanydomain.\<>rozszerzenia **. Na **przykład,\@BrittaSimon contoso.com**.

    1. Zaznacz pole wyboru **Pokaż hasło** i zanotuj wartość wyświetlaną w polu **Hasło**.

    1. Wybierz **pozycję Utwórz**.

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji można włączyć użytkownika Britta Simon do korzystania z azure logowania jednokrotnego, przyznając użytkownikowi dostęp do PageDNA.

1. W portalu Azure wybierz **aplikacje** > Enterprise**Wszystkie aplikacje** > **PageDNA**.

    ![Okienko aplikacji dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **PageDNA**.

    ![PageDNA na liście aplikacji](common/all-applications.png)

1. W lewym okienku w obszarze **MANAGE**wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja "Użytkownicy i grupy"](common/users-groups-blade.png)

1. Wybierz **pozycję + Dodaj użytkownika**, a następnie wybierz pozycję **Użytkownicy i grupy** w okienku Dodawanie **przydziału.**

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście **Użytkownicy,** a następnie wybierz pozycję **Wybierz** u dołu okienka.

1. Jeśli oczekujesz wartości roli w asercji SAML, a następnie w **okienku Wybierz rolę,** wybierz odpowiednią rolę dla użytkownika z listy. U dołu okienka wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-a-pagedna-test-user"></a>Tworzenie użytkownika testowego PageDNA

Użytkownik o imieniu Britta Simon jest teraz tworzony w PageDNA. Nie musisz nic robić, aby utworzyć tego użytkownika. PageDNA obsługuje just-in-time inicjowania obsługi administracyjnej użytkownika, który jest domyślnie włączony. Jeśli użytkownik o imieniu Britta Simon jeszcze nie istnieje w PageDNA, nowy jest tworzony po uwierzytelnieniu.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po wybraniu **PageDNA** w portalu Moje aplikacje, powinieneś być automatycznie zalogowany do subskrypcji PageDNA, dla której skonfigurowano logowanie jednokrotne. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Zasoby dodatkowe

* [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

