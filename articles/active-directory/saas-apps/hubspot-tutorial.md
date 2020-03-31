---
title: 'Samouczek: Integracja usługi Azure Active Directory z usługą HubSpot | Dokumenty firmy Microsoft'
description: Dowiedz się, jak skonfigurować logowanie jednokrotne między usługą Azure Active Directory a hubspot.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 57343ccd-53ea-4e62-9e54-dee2a9562ed5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c4b235426a7029abb9bb79ba56e582cccc3b14a6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944447"
---
# <a name="tutorial-azure-active-directory-integration-with-hubspot"></a>Samouczek: Integracja usługi Azure Active Directory z usługą HubSpot

W tym samouczku dowiesz się, jak zintegrować hubspot z usługą Azure Active Directory (Azure AD).

Integracja usługi HubSpot z usługą Azure AD zapewnia następujące korzyści:

* Za pomocą usługi Azure AD można kontrolować, kto ma dostęp do usługi HubSpot.
* Użytkownicy mogą być automatycznie zalogowany do HubSpot za pomocą swoich kont usługi Azure AD (logowanie jednokrotne).
* Możesz zarządzać kontami z jednej centralnej lokalizacji — witryny Azure Portal.

Aby uzyskać więcej informacji na temat integracji aplikacji z usługą Azure AD na temat oprogramowania jako usługi, zobacz [Logowanie jednokrotne w aplikacjach usługi Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Wymagania wstępne

Aby skonfigurować integrację usługi Azure AD z usługą HubSpot, potrzebne są następujące elementy:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji usługi Azure AD, utwórz [bezpłatne konto](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Subskrypcja HubSpot z włączoną rejestracją jednokrotną.

## <a name="scenario-description"></a>Opis scenariusza

W tym samouczku konfigurujesz i testujesz logowanie jednookrotne usługi Azure AD w środowisku testowym i integrujesz platformę HubSpot z usługą Azure AD.

HubSpot obsługuje następujące funkcje:

* **Logowanie jednokrotne inicjowane przez sp.**
* **Logowanie jednokrotne inicjowane przez dostawcę tożsamości**

## <a name="add-hubspot-in-the-azure-portal"></a>Dodawanie hubspot w witrynie Azure portal

Aby zintegrować platformę HubSpot z usługą Azure AD, należy dodać hubspot do listy zarządzanych aplikacji SaaS.

1. Zaloguj się do [Portalu Azure](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Azure Active Directory**.

    ![Opcja usługi Azure Active Directory](common/select-azuread.png)

1. Wybierz **aplikacje** > enterprise**Wszystkie aplikacje**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Aby dodać aplikację, wybierz pozycję **Nowa aplikacja**.

    ![Opcja Nowa aplikacja](common/add-new-app.png)

1. W polu wyszukiwania wprowadź **hubspot**. W wynikach wyszukiwania wybierz pozycję **HubSpot**, a następnie wybierz pozycję **Dodaj**.

    ![HubSpot na liście wyników](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurowanie i testowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować i przetestować azure ad logowania jednokrotnego z HubSpot na podstawie użytkownika testowego o nazwie **Britta Simon**. Aby logowanie jednokrotne działało, należy ustanowić połączony związek między użytkownikiem usługi Azure AD a powiązanym użytkownikiem w witrynie HubSpot.

Aby skonfigurować i przetestować logowanie jednookrotne usługi Azure AD za pomocą usługi HubSpot, należy wykonać następujące bloki konstrukcyjne:

| Zadanie | Opis |
| --- | --- |
| **[Konfigurowanie logowania jednokrotnego usługi Azure AD](#configure-azure-ad-single-sign-on)** | Umożliwia użytkownikom korzystanie z tej funkcji. |
| **[Konfigurowanie logowania jednokrotnego w ujmowanie hubspot](#configure-hubspot-single-sign-on)** | Konfiguruje ustawienia logowania jednokrotnego w aplikacji. |
| **[Tworzenie użytkownika testowego usługi Azure AD](#create-an-azure-ad-test-user)** | Testy logowania jednokrotnego usługi Azure AD dla użytkownika o nazwie Britta Simon. |
| **[Przypisywanie użytkownika testowego usługi Azure AD](#assign-the-azure-ad-test-user)** | Umożliwia Firmie Britta Simon używanie logowania jednokrotnego usługi Azure AD. |
| **[Tworzenie użytkownika testowego HubSpot](#create-a-hubspot-test-user)** | Tworzy odpowiednik Britta Simon w HubSpot, który jest połączony z reprezentacji usługi Azure AD użytkownika. |
| **[Testowanie logowania jednokrotnego](#test-single-sign-on)** | Sprawdza, czy konfiguracja działa. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurowanie logowania jednokrotnego usługi Azure AD

W tej sekcji można skonfigurować azure ad logowania jednokrotnego z HubSpot w witrynie Azure portal.

1. W [witrynie Azure portal](https://portal.azure.com/)w okienku integracji aplikacji **HubSpot** wybierz pozycję **Logowanie jednokrotne**.

    ![Konfigurowanie opcji logowania jednokrotnego](common/select-sso.png)

1. W okienku **Wybierz metodę logowania jednokrotnego** wybierz tryb **SAML** lub **SAML/WS-Fed,** aby włączyć logowanie jednokrotne.

    ![Wybieranie trybu logowania jednokrotnego](common/select-saml-option.png)

1. W okienku **Konfigurowanie logowania jednokrotnego za pomocą saml** wybierz pozycję **Edytuj** (ikona ołówka), aby otworzyć podstawowe okienko **konfiguracji SAML.**

    ![Edycja podstawowej konfiguracji protokołu SAML](common/edit-urls.png)

1. W podstawowym okienku **konfiguracji SAML,** aby skonfigurować *tryb inicjowany przez IDP,* wykonaj następujące czynności:

    1. W polu **Identyfikator** wprowadź adres URL zawierający następujący wzorzec: https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<IDENTYFIKATOR\>KLIENTA .

    1. W polu **Adres URL odpowiedzi** wprowadź adres URL zawierający\/następujący wzorzec: https: /api.hubspot.com/login-api/v1/saml/acs?portalId=\<Identyfikator\>klienta .

    ![Informacje o domenie i adresach URL HubSpot](common/idp-intiated.png)

    > [!NOTE]
    > Aby sformatować adresy URL, można również odwołać się do wzorców wyświetlanych w podstawowym okienku **konfiguracji SAML** w witrynie Azure portal.

1. Aby skonfigurować aplikację w trybie *inicjowanym przez dodatek SP:*

    1. Wybierz **pozycję Ustaw dodatkowe adresy URL**.

    1. W polu **Zaloguj się na adres URL** wprowadź **\/https: /app.hubspot.com/login**.

    ![Opcja Ustaw dodatkowe adresy URL](common/metadata-upload-additional-signon.png)

1. W okienku **Konfigurowanie logowania jednokrotnego z saml** w sekcji **Certyfikat podpisywania SAML** wybierz pozycję **Pobierz** obok pozycji **Certyfikat (Base64).** Wybierz opcję pobierania na podstawie twoich wymagań. Zapisz certyfikat na komputerze.

    ![Opcja pobierania certyfikatu (Base64)](common/certificatebase64.png)

1. W sekcji **Konfigurowanie hubspot** skopiuj następujące adresy URL na podstawie wymagań:

    * Adres URL logowania
    * Identyfikator usługi Azure AD
    * Adres URL wylogowywania

    ![Kopiowanie adresów URL konfiguracji](common/copy-configuration-urls.png)

### <a name="configure-hubspot-single-sign-on"></a>Konfigurowanie logowania jednokrotnego w ujmowanie hubspot

1. Otwórz nową kartę w przeglądarce i zaloguj się do konta administratora HubSpot.

1. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w aplikacji HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz **pozycję Domyślne konta**.

    ![Opcja Domyślne konto w hubspot](./media/hubspot-tutorial/config2.png)

1. Przewiń w dół do sekcji **Zabezpieczenia,** a następnie wybierz pozycję **Skonfiguruj**.

    ![Opcja Konfigurowanie w hubspot](./media/hubspot-tutorial/config3.png)

1. W sekcji **Konfigurowanie logowania jednokrotnego** wykonaj następujące czynności:

    1. W polu **Audience URl (Identyfikator jednostki dostawcy usług)** wybierz pozycję **Kopiuj,** aby skopiować wartość. W witrynie Azure portal w podstawowym okienku **konfiguracji SAML** wklej wartość w polu **Identyfikator.**

    1. W polu **Zaloguj się na URl, ACS, Odbiorca lub Przekierowanie** wybierz pozycję **Kopiuj,** aby skopiować wartość. W witrynie Azure portal w podstawowym okienku **konfiguracji SAML** wklej wartość w polu **Adres URL odpowiedzi.**

    1. W witrynie HubSpot w polu **Identyfikator dostawcy tożsamości lub adres URL wystawcy** wklej wartość **identyfikatora usługi Azure AD** skopiowanej w witrynie Azure portal.

    1. W witrynie HubSpot w polu **Adres URL logowania jednokrotnego dostawcy tożsamości** wklej wartość adresu URL **logowania** skopiowanego w witrynie Azure portal.

    1. W Notatniku systemu Windows otwórz pobrany plik Certyfikat (Base64). Zaznacz i skopiuj zawartość pliku. Następnie w polu HubSpot wklej go w polu **Certyfikat X.509.**

    1. Wybierz pozycję **Verify** (Weryfikuj).

        ![Sekcja Konfigurowanie logowania jednokrotnego w hubspot](./media/hubspot-tutorial/config4.png)

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

W tej sekcji udzielisz firmie Britta Simon dostępu do usługi HubSpot, aby mogła korzystać z logowania jednokrotnego platformy Azure.

1. W portalu Azure wybierz pozycję **Aplikacje** > dla**przedsiębiorstw Wszystkie aplikacje** > **HubSpot**.

    ![Okienko Aplikacje dla przedsiębiorstw](common/enterprise-applications.png)

1. Na liście aplikacji wybierz pozycję **HubSpot**.

    ![HubSpot na liście aplikacji](common/all-applications.png)

1. W menu wybierz pozycję **Użytkownicy i grupy**.

    ![Opcja Użytkownicy i grupy](common/users-groups-blade.png)

1. Wybierz przycisk **Dodaj użytkownika**. Następnie w okienku **Dodawanie przypisania** wybierz pozycję **Użytkownicy i grupy**.

    ![Okienko Dodawanie przypisania](common/add-assign-user.png)

1. W okienku **Użytkownicy i grupy** wybierz pozycję **Britta Simon** na liście użytkowników. Wybierz pozycję **Wybierz**.

1. Jeśli oczekujesz wartości roli w asercji SAML, w okienku **Wybierz rolę** wybierz odpowiednią rolę dla użytkownika z listy. Wybierz pozycję **Wybierz**.

1. W okienku **Dodawanie przydziału** wybierz pozycję **Przypisz**.

### <a name="create-a-hubspot-test-user"></a>Tworzenie użytkownika testowego HubSpot

Aby umożliwić usługi Azure AD użytkownika, aby zalogować się do HubSpot, użytkownik musi być aprowizowana w HubSpot. W HubSpot inicjowania obsługi administracyjnej jest zadanie ręczne.

Aby aprowizować konto użytkownika w centrum usługi HubSpot:

1. Zaloguj się do witryny firmy HubSpot jako administrator.

1. Wybierz ikonę **Ustawienia** w prawym górnym rogu strony.

    ![Ikona Ustawienia w aplikacji HubSpot](./media/hubspot-tutorial/config1.png)

1. Wybierz **użytkownicy & zespoły**.

    ![Opcja Użytkownicy & zespoły w HubSpot](./media/hubspot-tutorial/user1.png)

1. Wybierz **pozycję Utwórz użytkownika**.

    ![Opcja Utwórz użytkownika w hubspot](./media/hubspot-tutorial/user2.png)

1. W polu **Dodaj dodatek e-mail** wprowadź adres e-mail użytkownika w formacie contoso.com,\@a następnie wybierz pozycję **Dalej**.

    ![Pole Dodaj adresy e-mail w sekcji Tworzenie użytkowników w hubspot](./media/hubspot-tutorial/user3.png)

1. W sekcji **Tworzenie użytkowników** wybierz każdą kartę. Na każdej karcie ustaw odpowiednie opcje i uprawnienia dla użytkownika. Następnie wybierz przycisk **Dalej**.

    ![Karty w sekcji Tworzenie użytkowników w hubspot](./media/hubspot-tutorial/user4.png)

1. Aby wysłać zaproszenie do użytkownika, wybierz pozycję **Wyślij**.

    ![Opcja Wyślij w hubspot](./media/hubspot-tutorial/user5.png)

    > [!NOTE]
    > Użytkownik jest aktywowany po zaakceptowaniu zaproszenia przez użytkownika.

### <a name="test-single-sign-on"></a>Testowanie logowania jednokrotnego

W tej sekcji można przetestować konfigurację logowania jednokrotnego usługi Azure AD przy użyciu portalu Moje aplikacje.

Po skonfigurowaniu logowania jednokrotnego po **wybraniu hubspot** w portalu Moje aplikacje, użytkownik jest automatycznie zalogowany do hubspot. Aby uzyskać więcej informacji o portalu Moje aplikacje, zobacz [Uzyskiwanie dostępu do aplikacji i korzystanie z nich w portalu Moje aplikacje](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zapoznaj się z tymi artykułami:

- [Lista samouczków dotyczących integrowania aplikacji SaaS z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Single sign-on to applications in Azure Active Directory (Logowanie jednokrotne do aplikacji w usłudze Azure Active Directory)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Co to jest dostęp warunkowy w usłudze Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
