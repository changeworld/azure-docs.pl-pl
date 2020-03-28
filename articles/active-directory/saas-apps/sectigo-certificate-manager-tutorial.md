---
title: 'Samouczek: Integracja usługi Azure Active Directory z Menedżerem certyfikatów Sectigo | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a Menedżerem certyfikatów Sectigo.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67588235"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Samouczek: Integracja usługi Azure Active Directory z Menedżerem certyfikatów Sectigo

W tym samouczku dowiesz się, jak zintegrować Sectigo Certificate Manager z usługą Azure Active Directory (Azure AD).

Integracja Programu Sectigo Certificate Manager z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do Menedżera certyfikatów Sectigo.
* Użytkownicy mogą być automatycznie zalogowane do Sectigo Certificate Manager za pomocą swoich kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji z usługą Azure AD na temat oprogramowania jako usługi, zobacz [Logowanie jednokrotne w aplikacjach usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z Menedżerem certyfikatów Sectigo, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja Sectigo Certificate Manager z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku konfigurujesz i testujesz logowanie jednokrotne usługi Azure AD w środowisku testowym i integrujesz Sectigo Certificate Manager z usługą Azure AD.

Sectigo Certificate Manager obsługuje następujące funkcje:

* **Logowanie jednokrotne inicjowane przez sp.**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Dodawanie Menedżera certyfikatów Sectigo w witrynie Azure portal

Aby zintegrować Sectigo Certificate Manager z usługą Azure AD, należy dodać Menedżera certyfikatów Sectigo do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz **aplikacje** > enterprise**Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wpisz **Sectigo Certificate Manager**. W wynikach wyszukiwania wybierz pozycję **Sectigo Certificate Manager**, a następnie select **Add**.

    ![Sectigo Certificate Manager na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z Sectigo Certificate Manager na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączoną relację między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w Sectigo Certificate Manager.

Aby skonfigurować i przetestować logowanie jednokrotne usługi Azure AD za pomocą Menedżera certyfikatów Sectigo, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego Menedżera certyfikatów Sectigo](#configure-sectigo-certificate-manager-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy logowania jednokrotnego usługi Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Firmie Britta Simon używanie logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego Menedżera certyfikatów Sectigo](#create-a-sectigo-certificate-manager-test-user)** | Tworzy odpowiednik Britta Simon w Sectigo Certificate Manager, który jest połączony z reprezentacji usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować azure ad logowania jednokrotnego za pomocą Sectigo Certificate Manager w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)w okienku integracji aplikacji **Programu Sectigo Certificate Manager** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML** lub **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML,** aby skonfigurować *tryb inicjowany przez IDP,* wykonaj następujące czynności:

    1. W polu **Identyfikator** wprowadź jeden z następujących adresów URL:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. W polu **Adres URL odpowiedzi** wprowadź jeden z następujących adresów URL:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Wybierz **pozycję Ustaw dodatkowe adresy URL**.

    1. W polu **Stan przekazywania** wprowadź jeden z następujących adresów URL:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Informacje o logach do logowania i adresów URL Sectigo Certificate Manager](common/idp-relay.png)

1.  Aby skonfigurować aplikację w *trybie inicjowanym przez dodatek SP,* wykonaj następujące czynności:

    * W polu **Zaloguj się na adres URL** wprowadź jeden z następujących adresów URL:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Informacje o logach do logowania i adresów URL Sectigo Certificate Manager](common/both-signonurl.png)

1. W okienku **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **Certyfikat (Base64).** Wybierz opcję pobierania na podstawie twoich wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu (Base64)](common/certificatebase64.png)

1. W sekcji **Konfigurowanie Menedżera certyfikatów Sectigo** skopiuj następujące adresy URL na podstawie wymagań:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurowanie logowania jednokrotnego Menedżera certyfikatów Sectigo

Aby skonfigurować logowanie jednokrotne po stronie Menedżera certyfikatów Sectigo, wyślij pobrany plik certyfikatu (Base64) i odpowiednie adresy URL skopiowane z portalu Azure do [zespołu pomocy technicznej Programu Sectigo Certificate Manager](https://sectigo.com/support). Zespół pomocy technicznej Menedżera certyfikatów Sectigo używa wysyłanych informacji, aby upewnić się, że połączenie jednokrotnego logowania SAML jest poprawnie ustawione po obu stronach.

### <a name="create-an-azure-ad-test-user"></a>Tworzenie użytkownika testowego usługi Azure AD 

W tej sekcji utworzysz użytkownika testowego o nazwie Britta Simon w witrynie Azure Portal.

1. W witrynie Azure portal wybierz pozycję**Użytkownicy** >  **usługi Azure Active Directory** > **Wszyscy użytkownicy**.

    ![Opcje Użytkownicy i Wszyscy użytkownicy](common/users.png)

1. Wybierz przycisk **Nowy użytkownik**.

    ![Opcja Nowy użytkownik](common/new-user.png)

1. W okienku **Użytkownik** wykonaj następujące czynności:

    1. W polu **Nazwa** wpisz **BrittaSimon**.
  
    1. W polu **Nazwa użytkownika** wprowadź **>\@\<brittasimon twoja firma-domena.\< rozszerzenie\>**. Na przykład **contoso.com brittasimon\@**.

    1. Zaznacz pole wyboru **Pokaż hasło.** Zapisz wartość wyświetlaną w polu **Hasło.**

    1. Wybierz **pozycję Utwórz**.

    ![Okienko Użytkownik](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Przypisywanie użytkownika testowego usługi Azure AD

W tej sekcji możesz udzielić Firmie Britta Simon dostępu do Menedżera certyfikatów Sectigo, aby mogła używać logowania jednokrotnego platformy Azure.

1. W portalu Azure wybierz **aplikacje** > enterprise**Wszystkie aplikacje** > **Sectigo Certificate Manager**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **Sectigo Certificate Manager**.

    ![Sectigo Certificate Manager na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz wartości roli w asercji SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Tworzenie użytkownika testowego Menedżera certyfikatów Sectigo

W tej sekcji utworzysz użytkownika o nazwie Britta Simon w Sectigo Certificate Manager. Współpracuj z [zespołem pomocy technicznej Menedżera certyfikatów Sectigo,](https://sectigo.com/support) aby dodać użytkownika na platformie Sectigo Certificate Manager. Użytkownicy muszą być utworzeni i aktywowani przed rozpoczęciem korzystania z logowania jednokrotnego.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu logowania jednokrotnego po **wybraniu Menedżera certyfikatów Sectigo** w portalu Moje aplikacje automatycznie zalogujesz się do Menedżera certyfikatów Sectigo. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z tymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


